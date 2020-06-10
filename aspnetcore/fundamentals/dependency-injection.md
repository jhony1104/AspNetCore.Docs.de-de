---
<span data-ttu-id="707a0-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-102">'Blazor'</span></span>
- <span data-ttu-id="707a0-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-103">'Identity'</span></span>
- <span data-ttu-id="707a0-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-105">'Razor'</span></span>
- <span data-ttu-id="707a0-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-106">'SignalR' uid:</span></span> 

---
# <a name="dependency-injection-in-aspnet-core"></a><span data-ttu-id="707a0-107">Dependency Injection in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="707a0-107">Dependency injection in ASP.NET Core</span></span>

<span data-ttu-id="707a0-108">Von [Steve Smith](https://ardalis.com/) und [Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="707a0-108">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://scottaddie.com)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="707a0-109">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-109">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="707a0-110">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="707a0-110">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="707a0-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="707a0-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="707a0-112">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="707a0-112">Overview of dependency injection</span></span>

<span data-ttu-id="707a0-113">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="707a0-113">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="707a0-114">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="707a0-114">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="707a0-115">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="707a0-115">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="707a0-116">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="707a0-116">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="707a0-117">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="707a0-117">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="707a0-118">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-118">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="707a0-119">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-119">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="707a0-120">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-120">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="707a0-121">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="707a0-121">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="707a0-122">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="707a0-122">This implementation is difficult to unit test.</span></span> <span data-ttu-id="707a0-123">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="707a0-123">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="707a0-124">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="707a0-124">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="707a0-125">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="707a0-125">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="707a0-126">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="707a0-126">Registration of the dependency in a service container.</span></span> <span data-ttu-id="707a0-127">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="707a0-127">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="707a0-128">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-128">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="707a0-129">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-129">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="707a0-130">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-130">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="707a0-131">In der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="707a0-131">In the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="707a0-132">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="707a0-132">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="707a0-133">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="707a0-133">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="707a0-134">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="707a0-134">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="707a0-135">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="707a0-135">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="707a0-136">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="707a0-136">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="707a0-137">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="707a0-137">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="707a0-138">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-138">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="707a0-139">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-139">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="707a0-140">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-140">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="707a0-141">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="707a0-141">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="707a0-142">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-142">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="707a0-143">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="707a0-143">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="707a0-144">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="707a0-144">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="707a0-145">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="707a0-145">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="707a0-146">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="707a0-146">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="707a0-147">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="707a0-147">We recommended that apps follow this convention.</span></span> <span data-ttu-id="707a0-148">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="707a0-148">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="707a0-149">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-149">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="707a0-150">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="707a0-150">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="707a0-151">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="707a0-151">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="707a0-152">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="707a0-152">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="707a0-153">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="707a0-153">Services injected into Startup</span></span>

<span data-ttu-id="707a0-154">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-154">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="707a0-155">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-155">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="707a0-156">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="707a0-156">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="707a0-157">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="707a0-157">Framework-provided services</span></span>

<span data-ttu-id="707a0-158">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="707a0-158">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="707a0-159">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="707a0-159">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="707a0-160">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-160">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="707a0-161">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="707a0-161">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="707a0-162">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="707a0-162">Service Type</span></span> | <span data-ttu-id="707a0-163">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="707a0-163">Lifetime</span></span> |
| ---
<span data-ttu-id="707a0-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-164">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-165">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-165">'Blazor'</span></span>
- <span data-ttu-id="707a0-166">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-166">'Identity'</span></span>
- <span data-ttu-id="707a0-167">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-167">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-168">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-168">'Razor'</span></span>
- <span data-ttu-id="707a0-169">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-169">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-170">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-171">'Blazor'</span></span>
- <span data-ttu-id="707a0-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-172">'Identity'</span></span>
- <span data-ttu-id="707a0-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-174">'Razor'</span></span>
- <span data-ttu-id="707a0-175">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-176">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-177">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-177">'Blazor'</span></span>
- <span data-ttu-id="707a0-178">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-178">'Identity'</span></span>
- <span data-ttu-id="707a0-179">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-179">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-180">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-180">'Razor'</span></span>
- <span data-ttu-id="707a0-181">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-181">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-182">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-183">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-183">'Blazor'</span></span>
- <span data-ttu-id="707a0-184">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-184">'Identity'</span></span>
- <span data-ttu-id="707a0-185">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-185">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-186">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-186">'Razor'</span></span>
- <span data-ttu-id="707a0-187">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-187">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-188">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-189">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-189">'Blazor'</span></span>
- <span data-ttu-id="707a0-190">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-190">'Identity'</span></span>
- <span data-ttu-id="707a0-191">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-191">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-192">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-192">'Razor'</span></span>
- <span data-ttu-id="707a0-193">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-193">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-194">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-195">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-195">'Blazor'</span></span>
- <span data-ttu-id="707a0-196">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-196">'Identity'</span></span>
- <span data-ttu-id="707a0-197">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-197">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-198">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-198">'Razor'</span></span>
- <span data-ttu-id="707a0-199">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-199">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Flüchtig | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Flüchtig | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Flüchtig | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Flüchtig | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="707a0-200">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | `IHostApplicationLifetime` | Singleton | | `IWebHostEnvironment` | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="707a0-201">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="707a0-201">Register additional services with extension methods</span></span>

<span data-ttu-id="707a0-202">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="707a0-202">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="707a0-203">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-203">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="707a0-204">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="707a0-204">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="707a0-205">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="707a0-205">Service lifetimes</span></span>

<span data-ttu-id="707a0-206">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="707a0-206">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="707a0-207">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-207">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="707a0-208">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="707a0-208">Transient</span></span>

<span data-ttu-id="707a0-209">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-209">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="707a0-210">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="707a0-210">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="707a0-211">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="707a0-211">Scoped</span></span>

<span data-ttu-id="707a0-212">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-212">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="707a0-213">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="707a0-213">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="707a0-214">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="707a0-214">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="707a0-215">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="707a0-215">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="707a0-216">Singleton</span><span class="sxs-lookup"><span data-stu-id="707a0-216">Singleton</span></span>

<span data-ttu-id="707a0-217">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="707a0-217">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="707a0-218">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="707a0-218">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="707a0-219">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="707a0-219">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="707a0-220">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="707a0-220">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="707a0-221">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="707a0-221">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="707a0-222">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="707a0-222">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="707a0-223">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="707a0-223">Service registration methods</span></span>

<span data-ttu-id="707a0-224">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="707a0-224">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="707a0-225">Methode</span><span class="sxs-lookup"><span data-stu-id="707a0-225">Method</span></span> | <span data-ttu-id="707a0-226">Automatische</span><span class="sxs-lookup"><span data-stu-id="707a0-226">Automatic</span></span><br><span data-ttu-id="707a0-227">Objekt</span><span class="sxs-lookup"><span data-stu-id="707a0-227">object</span></span><br><span data-ttu-id="707a0-228">bereinigung</span><span class="sxs-lookup"><span data-stu-id="707a0-228">disposal</span></span> | <span data-ttu-id="707a0-229">Mehrere</span><span class="sxs-lookup"><span data-stu-id="707a0-229">Multiple</span></span><br><span data-ttu-id="707a0-230">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="707a0-230">implementations</span></span> | <span data-ttu-id="707a0-231">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="707a0-231">Pass args</span></span> |
| ---
<span data-ttu-id="707a0-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-232">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-233">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-233">'Blazor'</span></span>
- <span data-ttu-id="707a0-234">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-234">'Identity'</span></span>
- <span data-ttu-id="707a0-235">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-235">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-236">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-236">'Razor'</span></span>
- <span data-ttu-id="707a0-237">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-237">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-238">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-239">'Blazor'</span></span>
- <span data-ttu-id="707a0-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-240">'Identity'</span></span>
- <span data-ttu-id="707a0-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-242">'Razor'</span></span>
- <span data-ttu-id="707a0-243">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-245">'Blazor'</span></span>
- <span data-ttu-id="707a0-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-246">'Identity'</span></span>
- <span data-ttu-id="707a0-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-248">'Razor'</span></span>
- <span data-ttu-id="707a0-249">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-251">'Blazor'</span></span>
- <span data-ttu-id="707a0-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-252">'Identity'</span></span>
- <span data-ttu-id="707a0-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-254">'Razor'</span></span>
- <span data-ttu-id="707a0-255">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-257">'Blazor'</span></span>
- <span data-ttu-id="707a0-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-258">'Identity'</span></span>
- <span data-ttu-id="707a0-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-260">'Razor'</span></span>
- <span data-ttu-id="707a0-261">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-263">'Blazor'</span></span>
- <span data-ttu-id="707a0-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-264">'Identity'</span></span>
- <span data-ttu-id="707a0-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-266">'Razor'</span></span>
- <span data-ttu-id="707a0-267">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-269">'Blazor'</span></span>
- <span data-ttu-id="707a0-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-270">'Identity'</span></span>
- <span data-ttu-id="707a0-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-272">'Razor'</span></span>
- <span data-ttu-id="707a0-273">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-275">'Blazor'</span></span>
- <span data-ttu-id="707a0-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-276">'Identity'</span></span>
- <span data-ttu-id="707a0-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-278">'Razor'</span></span>
- <span data-ttu-id="707a0-279">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-281">'Blazor'</span></span>
- <span data-ttu-id="707a0-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-282">'Identity'</span></span>
- <span data-ttu-id="707a0-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-284">'Razor'</span></span>
- <span data-ttu-id="707a0-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-287">'Blazor'</span></span>
- <span data-ttu-id="707a0-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-288">'Identity'</span></span>
- <span data-ttu-id="707a0-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-290">'Razor'</span></span>
- <span data-ttu-id="707a0-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-293">'Blazor'</span></span>
- <span data-ttu-id="707a0-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-294">'Identity'</span></span>
- <span data-ttu-id="707a0-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-296">'Razor'</span></span>
- <span data-ttu-id="707a0-297">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-299">'Blazor'</span></span>
- <span data-ttu-id="707a0-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-300">'Identity'</span></span>
- <span data-ttu-id="707a0-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-302">'Razor'</span></span>
- <span data-ttu-id="707a0-303">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-305">'Blazor'</span></span>
- <span data-ttu-id="707a0-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-306">'Identity'</span></span>
- <span data-ttu-id="707a0-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-308">'Razor'</span></span>
- <span data-ttu-id="707a0-309">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-309">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-310">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-311">'Blazor'</span></span>
- <span data-ttu-id="707a0-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-312">'Identity'</span></span>
- <span data-ttu-id="707a0-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-314">'Razor'</span></span>
- <span data-ttu-id="707a0-315">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-317">'Blazor'</span></span>
- <span data-ttu-id="707a0-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-318">'Identity'</span></span>
- <span data-ttu-id="707a0-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-320">'Razor'</span></span>
- <span data-ttu-id="707a0-321">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-323">'Blazor'</span></span>
- <span data-ttu-id="707a0-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-324">'Identity'</span></span>
- <span data-ttu-id="707a0-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-326">'Razor'</span></span>
- <span data-ttu-id="707a0-327">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-327">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-328">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-329">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-329">'Blazor'</span></span>
- <span data-ttu-id="707a0-330">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-330">'Identity'</span></span>
- <span data-ttu-id="707a0-331">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-331">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-332">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-332">'Razor'</span></span>
- <span data-ttu-id="707a0-333">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-333">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-334">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-335">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-335">'Blazor'</span></span>
- <span data-ttu-id="707a0-336">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-336">'Identity'</span></span>
- <span data-ttu-id="707a0-337">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-337">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-338">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-338">'Razor'</span></span>
- <span data-ttu-id="707a0-339">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-339">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-340">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-341">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-341">'Blazor'</span></span>
- <span data-ttu-id="707a0-342">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-342">'Identity'</span></span>
- <span data-ttu-id="707a0-343">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-343">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-344">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-344">'Razor'</span></span>
- <span data-ttu-id="707a0-345">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-345">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-346">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-347">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-347">'Blazor'</span></span>
- <span data-ttu-id="707a0-348">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-348">'Identity'</span></span>
- <span data-ttu-id="707a0-349">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-349">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-350">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-350">'Razor'</span></span>
- <span data-ttu-id="707a0-351">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-351">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-353">'Blazor'</span></span>
- <span data-ttu-id="707a0-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-354">'Identity'</span></span>
- <span data-ttu-id="707a0-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-356">'Razor'</span></span>
- <span data-ttu-id="707a0-357">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-357">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-358">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-359">'Blazor'</span></span>
- <span data-ttu-id="707a0-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-360">'Identity'</span></span>
- <span data-ttu-id="707a0-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-362">'Razor'</span></span>
- <span data-ttu-id="707a0-363">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-365">'Blazor'</span></span>
- <span data-ttu-id="707a0-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-366">'Identity'</span></span>
- <span data-ttu-id="707a0-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-368">'Razor'</span></span>
- <span data-ttu-id="707a0-369">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-369">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-370">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-371">'Blazor'</span></span>
- <span data-ttu-id="707a0-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-372">'Identity'</span></span>
- <span data-ttu-id="707a0-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-374">'Razor'</span></span>
- <span data-ttu-id="707a0-375">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-375">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="707a0-376">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="707a0-377">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="707a0-377">Example:</span></span><br><span data-ttu-id="707a0-378">`services.AddSingleton<IMyDep, MyDep>();` | Ja | Ja | Nein | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="707a0-378">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="707a0-379">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-379">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="707a0-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ja | Ja | Ja | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="707a0-380">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="707a0-381">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="707a0-381">Example:</span></span><br><span data-ttu-id="707a0-382">`services.AddSingleton<MyDep>();` | Ja | Nein | Nein | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="707a0-382">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="707a0-383">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-383">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="707a0-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Nein | Ja | Ja | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="707a0-384">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="707a0-385">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-385">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="707a0-386">`services.AddSingleton(new MyDep("A string!"));` | Nein | Nein | Ja |</span><span class="sxs-lookup"><span data-stu-id="707a0-386">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="707a0-387">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="707a0-387">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="707a0-388">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="707a0-388">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="707a0-389">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="707a0-389">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="707a0-390">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="707a0-390">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="707a0-391">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="707a0-391">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="707a0-392">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="707a0-392">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="707a0-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="707a0-393">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="707a0-394">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="707a0-394">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="707a0-395">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="707a0-395">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="707a0-396">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-396">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="707a0-397">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="707a0-397">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="707a0-398">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="707a0-398">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="707a0-399">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="707a0-399">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="707a0-400">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="707a0-400">Constructor injection behavior</span></span>

<span data-ttu-id="707a0-401">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-401">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="707a0-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="707a0-402"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="707a0-403">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="707a0-403">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="707a0-404">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="707a0-404">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="707a0-405">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="707a0-405">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="707a0-406">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="707a0-406">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="707a0-407">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="707a0-407">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="707a0-408">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="707a0-408">Entity Framework contexts</span></span>

<span data-ttu-id="707a0-409">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-409">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="707a0-410">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-410">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="707a0-411">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="707a0-411">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="707a0-412">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="707a0-412">Lifetime and registration options</span></span>

<span data-ttu-id="707a0-413">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="707a0-413">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="707a0-414">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="707a0-414">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="707a0-415">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="707a0-415">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="707a0-416">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="707a0-416">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="707a0-417">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="707a0-417">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="707a0-418">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="707a0-418">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="707a0-419">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="707a0-419">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="707a0-420">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="707a0-420">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="707a0-421">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="707a0-421">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="707a0-422">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="707a0-422">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="707a0-423">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="707a0-423">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="707a0-424">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="707a0-424">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="707a0-425">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="707a0-425">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="707a0-426">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="707a0-426">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="707a0-427">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="707a0-427">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="707a0-428">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="707a0-428">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="707a0-429">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="707a0-429">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="707a0-430">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="707a0-430">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/3.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="707a0-431">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="707a0-431">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="707a0-432">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="707a0-432">**First request:**</span></span>

<span data-ttu-id="707a0-433">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-433">Controller operations:</span></span>

<span data-ttu-id="707a0-434">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="707a0-434">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="707a0-435">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="707a0-435">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="707a0-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-436">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-437">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-437">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-438">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-438">`OperationService` operations:</span></span>

<span data-ttu-id="707a0-439">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="707a0-439">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="707a0-440">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="707a0-440">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="707a0-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-441">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-442">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-442">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-443">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="707a0-443">**Second request:**</span></span>

<span data-ttu-id="707a0-444">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-444">Controller operations:</span></span>

<span data-ttu-id="707a0-445">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="707a0-445">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="707a0-446">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="707a0-446">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="707a0-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-447">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-448">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-448">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-449">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-449">`OperationService` operations:</span></span>

<span data-ttu-id="707a0-450">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="707a0-450">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="707a0-451">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="707a0-451">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="707a0-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-452">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-453">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-453">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-454">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="707a0-454">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="707a0-455">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="707a0-455">*Transient* objects are always different.</span></span> <span data-ttu-id="707a0-456">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="707a0-456">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="707a0-457">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-457">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="707a0-458">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="707a0-458">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="707a0-459">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="707a0-459">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="707a0-460">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="707a0-460">Call services from main</span></span>

<span data-ttu-id="707a0-461">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="707a0-461">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="707a0-462">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="707a0-462">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="707a0-463">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="707a0-463">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="707a0-464">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="707a0-464">Scope validation</span></span>

<span data-ttu-id="707a0-465">Wenn die App in der Entwicklungsumgebung ausgeführt wird und [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) aufruft, um den Host zu erstellen, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="707a0-465">When the app is running in the Development environment and calls [CreateDefaultBuilder](xref:fundamentals/host/generic-host#default-builder-settings) to build the host, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="707a0-466">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="707a0-466">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="707a0-467">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="707a0-467">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="707a0-468">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-468">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="707a0-469">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-469">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="707a0-470">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="707a0-470">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="707a0-471">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-471">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="707a0-472">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-472">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="707a0-473">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="707a0-473">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="707a0-474">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="707a0-474">Request Services</span></span>

<span data-ttu-id="707a0-475">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="707a0-475">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="707a0-476">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-476">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="707a0-477">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="707a0-477">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="707a0-478">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="707a0-478">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="707a0-479">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="707a0-479">Instead, request the types that classes require via class constructors and allow the framework to inject the dependencies.</span></span> <span data-ttu-id="707a0-480">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="707a0-480">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="707a0-481">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="707a0-481">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="707a0-482">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="707a0-482">Design services for dependency injection</span></span>

<span data-ttu-id="707a0-483">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="707a0-483">Best practices are to:</span></span>

* <span data-ttu-id="707a0-484">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="707a0-484">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="707a0-485">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="707a0-485">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="707a0-486">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="707a0-486">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="707a0-487">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="707a0-487">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="707a0-488">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="707a0-488">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="707a0-489">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="707a0-489">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="707a0-490">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="707a0-490">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="707a0-491">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="707a0-491">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="707a0-492">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="707a0-492">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="707a0-493">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="707a0-493">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="707a0-494">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="707a0-494">Disposal of services</span></span>

<span data-ttu-id="707a0-495">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="707a0-495">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="707a0-496">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="707a0-496">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="707a0-497">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="707a0-497">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="707a0-498">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="707a0-498">In the following example:</span></span>

* <span data-ttu-id="707a0-499">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-499">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="707a0-500">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="707a0-500">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="707a0-501">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="707a0-501">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="707a0-502">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-502">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

<span data-ttu-id="707a0-503">Eine Erläuterung zu Möglichkeiten zum Verwerfen von Diensten finden Sie unter [Vier Möglichkeiten, IDisposables in ASP.NET Core zu verwerfen](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span><span class="sxs-lookup"><span data-stu-id="707a0-503">For a discussion of service disposal options, see [Four ways to dispose IDisposables in ASP.NET Core](https://andrewlock.net/four-ways-to-dispose-idisposables-in-asp-net-core/).</span></span>

## <a name="default-service-container-replacement"></a><span data-ttu-id="707a0-504">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="707a0-504">Default service container replacement</span></span>

<span data-ttu-id="707a0-505">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="707a0-505">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="707a0-506">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-506">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="707a0-507">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="707a0-507">Property injection</span></span>
* <span data-ttu-id="707a0-508">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="707a0-508">Injection based on name</span></span>
* <span data-ttu-id="707a0-509">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="707a0-509">Child containers</span></span>
* <span data-ttu-id="707a0-510">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="707a0-510">Custom lifetime management</span></span>
* <span data-ttu-id="707a0-511">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="707a0-511">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="707a0-512">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="707a0-512">Convention-based registration</span></span>

<span data-ttu-id="707a0-513">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-513">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="707a0-514">Autofac</span><span class="sxs-lookup"><span data-stu-id="707a0-514">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="707a0-515">DryIoc</span><span class="sxs-lookup"><span data-stu-id="707a0-515">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="707a0-516">Grace</span><span class="sxs-lookup"><span data-stu-id="707a0-516">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="707a0-517">LightInject</span><span class="sxs-lookup"><span data-stu-id="707a0-517">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="707a0-518">Lamar</span><span class="sxs-lookup"><span data-stu-id="707a0-518">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="707a0-519">Stashbox</span><span class="sxs-lookup"><span data-stu-id="707a0-519">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="707a0-520">Unity</span><span class="sxs-lookup"><span data-stu-id="707a0-520">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="707a0-521">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="707a0-521">Thread safety</span></span>

<span data-ttu-id="707a0-522">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="707a0-522">Create thread-safe singleton services.</span></span> <span data-ttu-id="707a0-523">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="707a0-523">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="707a0-524">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="707a0-524">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="707a0-525">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="707a0-525">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="707a0-526">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="707a0-526">Recommendations</span></span>

* <span data-ttu-id="707a0-527">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="707a0-527">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="707a0-528">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="707a0-528">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="707a0-529">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="707a0-529">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="707a0-530">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-530">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="707a0-531">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-531">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="707a0-532">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="707a0-532">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="707a0-533">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-533">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="707a0-534">Vermeiden Sie den statischen Zugriff auf Dienste (z. B. statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle).</span><span class="sxs-lookup"><span data-stu-id="707a0-534">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="707a0-535">Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*.</span><span class="sxs-lookup"><span data-stu-id="707a0-535">Avoid using the *service locator pattern*.</span></span> <span data-ttu-id="707a0-536">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:</span><span class="sxs-lookup"><span data-stu-id="707a0-536">For example, don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

  <span data-ttu-id="707a0-537">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="707a0-537">**Incorrect:**</span></span>

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

  <span data-ttu-id="707a0-538">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="707a0-538">**Correct**:</span></span>

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

* <span data-ttu-id="707a0-539">Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst.</span><span class="sxs-lookup"><span data-stu-id="707a0-539">Another service locator variation to avoid is injecting a factory that resolves dependencies at runtime.</span></span> <span data-ttu-id="707a0-540">Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).</span><span class="sxs-lookup"><span data-stu-id="707a0-540">Both of these practices mix [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

* <span data-ttu-id="707a0-541">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="707a0-541">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="707a0-542">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="707a0-542">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="707a0-543">Es gibt nur wenige Ausnahmen &mdash; die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="707a0-543">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="707a0-544">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="707a0-544">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="707a0-545">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="707a0-545">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="recommended-patterns-for-multi-tenancy-in-di"></a><span data-ttu-id="707a0-546">Empfohlene Muster für Mehrinstanzenfähigkeit in der Dependency Injection (DI)</span><span class="sxs-lookup"><span data-stu-id="707a0-546">Recommended patterns for multi-tenancy in DI</span></span>

<span data-ttu-id="707a0-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) bietet die Mehrinstanzenfähigkeit.</span><span class="sxs-lookup"><span data-stu-id="707a0-547">[Orchard Core](https://github.com/OrchardCMS/OrchardCore) provides multi-tenancy.</span></span> <span data-ttu-id="707a0-548">Weitere Informationen finden Sie in der [Orchard Core-Dokumentation](https://docs.orchardcore.net/en/dev/).</span><span class="sxs-lookup"><span data-stu-id="707a0-548">For more information, see the [Orchard Core Documentation](https://docs.orchardcore.net/en/dev/).</span></span>

<span data-ttu-id="707a0-549">Beispiele und Vorgehensweisen zum Erstellen modularer Apps und mehrinstanzenfähigen Apps nur mit dem Orchard Core-Framework aber ohne CMS-spezifische Features finden Sie in den Beispiel-Apps unter https://github.com/OrchardCMS/OrchardCore.Samples.</span><span class="sxs-lookup"><span data-stu-id="707a0-549">See the samples apps at https://github.com/OrchardCMS/OrchardCore.Samples for examples of how to build modular and multi-tenant apps using just Orchard Core Framework without any of the CMS specific features.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="707a0-550">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="707a0-550">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="707a0-551">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="707a0-551">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="707a0-552">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="707a0-552">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="707a0-553">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="707a0-553">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="707a0-554">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="707a0-554">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="707a0-555">ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-555">ASP.NET Core supports the dependency injection (DI) software design pattern, which is a technique for achieving [Inversion of Control (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) between classes and their dependencies.</span></span>

<span data-ttu-id="707a0-556">Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="707a0-556">For more information specific to dependency injection within MVC controllers, see <xref:mvc/controllers/dependency-injection>.</span></span>

<span data-ttu-id="707a0-557">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="707a0-557">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="overview-of-dependency-injection"></a><span data-ttu-id="707a0-558">Übersicht über Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="707a0-558">Overview of dependency injection</span></span>

<span data-ttu-id="707a0-559">Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt.</span><span class="sxs-lookup"><span data-stu-id="707a0-559">A *dependency* is any object that another object requires.</span></span> <span data-ttu-id="707a0-560">Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:</span><span class="sxs-lookup"><span data-stu-id="707a0-560">Examine the following `MyDependency` class with a `WriteMessage` method that other classes in an app depend upon:</span></span>

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

<span data-ttu-id="707a0-561">Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen.</span><span class="sxs-lookup"><span data-stu-id="707a0-561">An instance of the `MyDependency` class can be created to make the `WriteMessage` method available to a class.</span></span> <span data-ttu-id="707a0-562">Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:</span><span class="sxs-lookup"><span data-stu-id="707a0-562">The `MyDependency` class is a dependency of the `IndexModel` class:</span></span>

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

<span data-ttu-id="707a0-563">Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab.</span><span class="sxs-lookup"><span data-stu-id="707a0-563">The class creates and directly depends on the `MyDependency` instance.</span></span> <span data-ttu-id="707a0-564">Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-564">Code dependencies (such as the previous example) are problematic and should be avoided for the following reasons:</span></span>

* <span data-ttu-id="707a0-565">Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-565">To replace `MyDependency` with a different implementation, the class must be modified.</span></span>
* <span data-ttu-id="707a0-566">Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-566">If `MyDependency` has dependencies, they must be configured by the class.</span></span> <span data-ttu-id="707a0-567">In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.</span><span class="sxs-lookup"><span data-stu-id="707a0-567">In a large project with multiple classes depending on `MyDependency`, the configuration code becomes scattered across the app.</span></span>
* <span data-ttu-id="707a0-568">Diese Implementierung ist nicht für Komponententests geeignet.</span><span class="sxs-lookup"><span data-stu-id="707a0-568">This implementation is difficult to unit test.</span></span> <span data-ttu-id="707a0-569">Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.</span><span class="sxs-lookup"><span data-stu-id="707a0-569">The app should use a mock or stub `MyDependency` class, which isn't possible with this approach.</span></span>

<span data-ttu-id="707a0-570">Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:</span><span class="sxs-lookup"><span data-stu-id="707a0-570">Dependency injection addresses these problems through:</span></span>

* <span data-ttu-id="707a0-571">Die Verwendung einer Schnittstelle oder Basisklasse zur Abstraktion der Abhängigkeitsimplementierung.</span><span class="sxs-lookup"><span data-stu-id="707a0-571">The use of an interface or base class to abstract the dependency implementation.</span></span>
* <span data-ttu-id="707a0-572">Registrierung der Abhängigkeit in einem Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="707a0-572">Registration of the dependency in a service container.</span></span> <span data-ttu-id="707a0-573">ASP.NET Core stellt einen integrierten Dienstcontainer (<xref:System.IServiceProvider>) bereit.</span><span class="sxs-lookup"><span data-stu-id="707a0-573">ASP.NET Core provides a built-in service container, <xref:System.IServiceProvider>.</span></span> <span data-ttu-id="707a0-574">Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-574">Services are registered in the app's `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="707a0-575">Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-575">*Injection* of the service into the constructor of the class where it's used.</span></span> <span data-ttu-id="707a0-576">Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-576">The framework takes on the responsibility of creating an instance of the dependency and disposing of it when it's no longer needed.</span></span>

<span data-ttu-id="707a0-577">In der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:</span><span class="sxs-lookup"><span data-stu-id="707a0-577">In the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples), the `IMyDependency` interface defines a method that the service provides to the app:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

<span data-ttu-id="707a0-578">Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:</span><span class="sxs-lookup"><span data-stu-id="707a0-578">This interface is implemented by a concrete type, `MyDependency`:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

<span data-ttu-id="707a0-579">`MyDependency` fordert einen <xref:Microsoft.Extensions.Logging.ILogger`1> beim zugehörigen Konstruktor an.</span><span class="sxs-lookup"><span data-stu-id="707a0-579">`MyDependency` requests an <xref:Microsoft.Extensions.Logging.ILogger`1> in its constructor.</span></span> <span data-ttu-id="707a0-580">Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet.</span><span class="sxs-lookup"><span data-stu-id="707a0-580">It's not unusual to use dependency injection in a chained fashion.</span></span> <span data-ttu-id="707a0-581">Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an.</span><span class="sxs-lookup"><span data-stu-id="707a0-581">Each requested dependency in turn requests its own dependencies.</span></span> <span data-ttu-id="707a0-582">Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück.</span><span class="sxs-lookup"><span data-stu-id="707a0-582">The container resolves the dependencies in the graph and returns the fully resolved service.</span></span> <span data-ttu-id="707a0-583">Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="707a0-583">The collective set of dependencies that must be resolved is typically referred to as a *dependency tree*, *dependency graph*, or *object graph*.</span></span>

<span data-ttu-id="707a0-584">`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-584">`IMyDependency` and `ILogger<TCategoryName>` must be registered in the service container.</span></span> <span data-ttu-id="707a0-585">`IMyDependency` ist in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-585">`IMyDependency` is registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="707a0-586">`ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen [von einem Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-586">`ILogger<TCategoryName>` is registered by the logging abstractions infrastructure, so it's a [framework-provided service](#framework-provided-services) registered by default by the framework.</span></span>

<span data-ttu-id="707a0-587">Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:</span><span class="sxs-lookup"><span data-stu-id="707a0-587">The container resolves `ILogger<TCategoryName>` by taking advantage of [(generic) open types](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types), eliminating the need to register every [(generic) constructed type](/dotnet/csharp/language-reference/language-specification/types#constructed-types):</span></span>

```csharp
services.AddSingleton(typeof(ILogger<>), typeof(Logger<>));
```

<span data-ttu-id="707a0-588">In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-588">In the sample app, the `IMyDependency` service is registered with the concrete type `MyDependency`.</span></span> <span data-ttu-id="707a0-589">Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein.</span><span class="sxs-lookup"><span data-stu-id="707a0-589">The registration scopes the service lifetime to the lifetime of a single request.</span></span> <span data-ttu-id="707a0-590">Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.</span><span class="sxs-lookup"><span data-stu-id="707a0-590">[Service lifetimes](#service-lifetimes) are described later in this topic.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> <span data-ttu-id="707a0-591">Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise).</span><span class="sxs-lookup"><span data-stu-id="707a0-591">Each `services.Add{SERVICE_NAME}` extension method adds (and potentially configures) services.</span></span> <span data-ttu-id="707a0-592">So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu.</span><span class="sxs-lookup"><span data-stu-id="707a0-592">For example, `services.AddMvc()` adds the services Razor Pages and MVC require.</span></span> <span data-ttu-id="707a0-593">Es wird empfohlen, dass Apps dieser Konvention folgen.</span><span class="sxs-lookup"><span data-stu-id="707a0-593">We recommended that apps follow this convention.</span></span> <span data-ttu-id="707a0-594">Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.</span><span class="sxs-lookup"><span data-stu-id="707a0-594">Place extension methods in the [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection) namespace to encapsulate groups of service registrations.</span></span>

<span data-ttu-id="707a0-595">Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-595">If the service's constructor requires a [built in type](/dotnet/csharp/language-reference/keywords/built-in-types-table), such as a `string`, the type can be injected by using [configuration](xref:fundamentals/configuration/index) or the [options pattern](xref:fundamentals/configuration/options):</span></span>

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

<span data-ttu-id="707a0-596">Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="707a0-596">An instance of the service is requested via the constructor of a class where the service is used and assigned to a private field.</span></span> <span data-ttu-id="707a0-597">Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="707a0-597">The field is used to access the service as necessary throughout the class.</span></span>

<span data-ttu-id="707a0-598">In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="707a0-598">In the sample app, the `IMyDependency` instance is requested and used to call the service's `WriteMessage` method:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="services-injected-into-startup"></a><span data-ttu-id="707a0-599">In den Start eingefügte Dienste</span><span class="sxs-lookup"><span data-stu-id="707a0-599">Services injected into Startup</span></span>

<span data-ttu-id="707a0-600">Bei Verwendung des generischen Hosts (<xref:Microsoft.Extensions.Hosting.IHostBuilder>) können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-600">Only the following service types can be injected into the `Startup` constructor when using the Generic Host (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* `IWebHostEnvironment`
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

<span data-ttu-id="707a0-601">Dienste können in `Startup.Configure` eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-601">Services can be injected into `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IOptions<MyOptions> options)
{
    ...
}
```

<span data-ttu-id="707a0-602">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="707a0-602">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="framework-provided-services"></a><span data-ttu-id="707a0-603">Von Frameworks bereitgestellte Dienste</span><span class="sxs-lookup"><span data-stu-id="707a0-603">Framework-provided services</span></span>

<span data-ttu-id="707a0-604">Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies umfasst auch Plattformfeatures wie Entity Framework Core und ASP.NET Core MVC.</span><span class="sxs-lookup"><span data-stu-id="707a0-604">The `Startup.ConfigureServices` method is responsible for defining the services that the app uses, including platform features, such as Entity Framework Core and ASP.NET Core MVC.</span></span> <span data-ttu-id="707a0-605">Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` die vom Framework definierten Dienste, abhängig davon, wie der [Host konfiguriert](xref:fundamentals/index#host) wurde.</span><span class="sxs-lookup"><span data-stu-id="707a0-605">Initially, the `IServiceCollection` provided to `ConfigureServices` has services defined by the framework depending on [how the host was configured](xref:fundamentals/index#host).</span></span> <span data-ttu-id="707a0-606">Es ist nicht ungewöhnlich, dass das Framework Hunderte von Diensten für eine auf einer ASP.NET Core-Vorlage basierende App registriert.</span><span class="sxs-lookup"><span data-stu-id="707a0-606">It's not uncommon for an app based on an ASP.NET Core template to have hundreds of services registered by the framework.</span></span> <span data-ttu-id="707a0-607">In der folgenden Tabelle finden Sie eine kleine Auswahl der Dienste, die vom Framework registriert werden können.</span><span class="sxs-lookup"><span data-stu-id="707a0-607">A small sample of framework-registered services is listed in the following table.</span></span>

| <span data-ttu-id="707a0-608">Diensttyp</span><span class="sxs-lookup"><span data-stu-id="707a0-608">Service Type</span></span> | <span data-ttu-id="707a0-609">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="707a0-609">Lifetime</span></span> |
| ---
<span data-ttu-id="707a0-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-610">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-611">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-611">'Blazor'</span></span>
- <span data-ttu-id="707a0-612">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-612">'Identity'</span></span>
- <span data-ttu-id="707a0-613">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-613">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-614">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-614">'Razor'</span></span>
- <span data-ttu-id="707a0-615">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-615">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-616">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-617">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-617">'Blazor'</span></span>
- <span data-ttu-id="707a0-618">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-618">'Identity'</span></span>
- <span data-ttu-id="707a0-619">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-619">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-620">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-620">'Razor'</span></span>
- <span data-ttu-id="707a0-621">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-621">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-622">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-623">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-623">'Blazor'</span></span>
- <span data-ttu-id="707a0-624">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-624">'Identity'</span></span>
- <span data-ttu-id="707a0-625">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-625">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-626">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-626">'Razor'</span></span>
- <span data-ttu-id="707a0-627">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-627">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-628">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-629">'Blazor'</span></span>
- <span data-ttu-id="707a0-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-630">'Identity'</span></span>
- <span data-ttu-id="707a0-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-632">'Razor'</span></span>
- <span data-ttu-id="707a0-633">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-633">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-634">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-635">'Blazor'</span></span>
- <span data-ttu-id="707a0-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-636">'Identity'</span></span>
- <span data-ttu-id="707a0-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-638">'Razor'</span></span>
- <span data-ttu-id="707a0-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-639">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-640">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-641">'Blazor'</span></span>
- <span data-ttu-id="707a0-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-642">'Identity'</span></span>
- <span data-ttu-id="707a0-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-644">'Razor'</span></span>
- <span data-ttu-id="707a0-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-645">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Flüchtig | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Flüchtig | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Flüchtig | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Flüchtig | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span><span class="sxs-lookup"><span data-stu-id="707a0-646">---- | | <xref:Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartup?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Hosting.IStartupFilter?displayProperty=fullName> | Transient | | <xref:Microsoft.AspNetCore.Hosting.Server.IServer?displayProperty=fullName> | Singleton | | <xref:Microsoft.AspNetCore.Http.IHttpContextFactory?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Logging.ILogger`1?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Logging.ILoggerFactory?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider?displayProperty=fullName> | Singleton | | <xref:Microsoft.Extensions.Options.IConfigureOptions`1?displayProperty=fullName> | Transient | | <xref:Microsoft.Extensions.Options.IOptions`1?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticSource?displayProperty=fullName> | Singleton | | <xref:System.Diagnostics.DiagnosticListener?displayProperty=fullName> | Singleton |</span></span>

## <a name="register-additional-services-with-extension-methods"></a><span data-ttu-id="707a0-647">Registrieren weiterer Dienste mit Erweiterungsmethoden</span><span class="sxs-lookup"><span data-stu-id="707a0-647">Register additional services with extension methods</span></span>

<span data-ttu-id="707a0-648">Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="707a0-648">When a service collection extension method is available to register a service (and its dependent services, if required), the convention is to use a single `Add{SERVICE_NAME}` extension method to register all of the services required by that service.</span></span> <span data-ttu-id="707a0-649">Der folgende Code ist ein Beispiel dafür, wie mit den Erweiterungsmethoden [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) und <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*> zusätzliche Dienste zum Container hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-649">The following code is an example of how to add additional services to the container using the extension methods [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) and <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentityCore*>:</span></span>

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

<span data-ttu-id="707a0-650">Weitere Informationen finden Sie in der <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection>-Klasse in der API-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="707a0-650">For more information, see the <xref:Microsoft.Extensions.DependencyInjection.ServiceCollection> class in the API documentation.</span></span>

## <a name="service-lifetimes"></a><span data-ttu-id="707a0-651">Dienstlebensdauer</span><span class="sxs-lookup"><span data-stu-id="707a0-651">Service lifetimes</span></span>

<span data-ttu-id="707a0-652">Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus.</span><span class="sxs-lookup"><span data-stu-id="707a0-652">Choose an appropriate lifetime for each registered service.</span></span> <span data-ttu-id="707a0-653">ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-653">ASP.NET Core services can be configured with the following lifetimes:</span></span>

### <a name="transient"></a><span data-ttu-id="707a0-654">Transient (vorübergehend)</span><span class="sxs-lookup"><span data-stu-id="707a0-654">Transient</span></span>

<span data-ttu-id="707a0-655">Kurzlebige Dienste (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-655">Transient lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddTransient*>) are created each time they're requested from the service container.</span></span> <span data-ttu-id="707a0-656">Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.</span><span class="sxs-lookup"><span data-stu-id="707a0-656">This lifetime works best for lightweight, stateless services.</span></span>

### <a name="scoped"></a><span data-ttu-id="707a0-657">Bereichsbezogen</span><span class="sxs-lookup"><span data-stu-id="707a0-657">Scoped</span></span>

<span data-ttu-id="707a0-658">Dienste mit bereichsbezogener Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) werden einmal pro Clientanforderung (-verbindung) erstellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-658">Scoped lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddScoped*>) are created once per client request (connection).</span></span>

> [!WARNING]
> <span data-ttu-id="707a0-659">Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen.</span><span class="sxs-lookup"><span data-stu-id="707a0-659">When using a scoped service in a middleware, inject the service into the `Invoke` or `InvokeAsync` method.</span></span> <span data-ttu-id="707a0-660">Fügen Sie ihn nicht über [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt.</span><span class="sxs-lookup"><span data-stu-id="707a0-660">Don't inject via [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) because it forces the service to behave like a singleton.</span></span> <span data-ttu-id="707a0-661">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span><span class="sxs-lookup"><span data-stu-id="707a0-661">For more information, see <xref:fundamentals/middleware/write#per-request-middleware-dependencies>.</span></span>

### <a name="singleton"></a><span data-ttu-id="707a0-662">Singleton</span><span class="sxs-lookup"><span data-stu-id="707a0-662">Singleton</span></span>

<span data-ttu-id="707a0-663">Dienste mit Singleton-Lebensdauer (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) werden bei der ersten Anforderung erstellt (bzw. wenn `Startup.ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird).</span><span class="sxs-lookup"><span data-stu-id="707a0-663">Singleton lifetime services (<xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>) are created the first time they're requested (or when `Startup.ConfigureServices` is run and an instance is specified with the service registration).</span></span> <span data-ttu-id="707a0-664">Jeder nachfolgenden Anforderung verwendet die gleiche Instanz.</span><span class="sxs-lookup"><span data-stu-id="707a0-664">Every subsequent request uses the same instance.</span></span> <span data-ttu-id="707a0-665">Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen.</span><span class="sxs-lookup"><span data-stu-id="707a0-665">If the app requires singleton behavior, allowing the service container to manage the service's lifetime is recommended.</span></span> <span data-ttu-id="707a0-666">Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="707a0-666">Don't implement the singleton design pattern and provide user code to manage the object's lifetime in the class.</span></span>

> [!WARNING]
> <span data-ttu-id="707a0-667">Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="707a0-667">It's dangerous to resolve a scoped service from a singleton.</span></span> <span data-ttu-id="707a0-668">Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.</span><span class="sxs-lookup"><span data-stu-id="707a0-668">It may cause the service to have incorrect state when processing subsequent requests.</span></span>

## <a name="service-registration-methods"></a><span data-ttu-id="707a0-669">Dienstregistrierungsmethoden</span><span class="sxs-lookup"><span data-stu-id="707a0-669">Service registration methods</span></span>

<span data-ttu-id="707a0-670">Methoden zur Erweiterung der Dienstregistrierung bieten Überladungen, die in bestimmten Szenarios hilfreich sind.</span><span class="sxs-lookup"><span data-stu-id="707a0-670">Service registration extension methods offer overloads that are useful in specific scenarios.</span></span>

| <span data-ttu-id="707a0-671">Methode</span><span class="sxs-lookup"><span data-stu-id="707a0-671">Method</span></span> | <span data-ttu-id="707a0-672">Automatische</span><span class="sxs-lookup"><span data-stu-id="707a0-672">Automatic</span></span><br><span data-ttu-id="707a0-673">Objekt</span><span class="sxs-lookup"><span data-stu-id="707a0-673">object</span></span><br><span data-ttu-id="707a0-674">bereinigung</span><span class="sxs-lookup"><span data-stu-id="707a0-674">disposal</span></span> | <span data-ttu-id="707a0-675">Mehrere</span><span class="sxs-lookup"><span data-stu-id="707a0-675">Multiple</span></span><br><span data-ttu-id="707a0-676">Implementierungen</span><span class="sxs-lookup"><span data-stu-id="707a0-676">implementations</span></span> | <span data-ttu-id="707a0-677">Argumentübergabe</span><span class="sxs-lookup"><span data-stu-id="707a0-677">Pass args</span></span> |
| ---
<span data-ttu-id="707a0-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-678">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-679">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-679">'Blazor'</span></span>
- <span data-ttu-id="707a0-680">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-680">'Identity'</span></span>
- <span data-ttu-id="707a0-681">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-681">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-682">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-682">'Razor'</span></span>
- <span data-ttu-id="707a0-683">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-683">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-684">--- | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-685">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-685">'Blazor'</span></span>
- <span data-ttu-id="707a0-686">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-686">'Identity'</span></span>
- <span data-ttu-id="707a0-687">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-687">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-688">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-688">'Razor'</span></span>
- <span data-ttu-id="707a0-689">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-689">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-690">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-691">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-691">'Blazor'</span></span>
- <span data-ttu-id="707a0-692">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-692">'Identity'</span></span>
- <span data-ttu-id="707a0-693">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-693">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-694">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-694">'Razor'</span></span>
- <span data-ttu-id="707a0-695">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-695">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-696">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-697">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-697">'Blazor'</span></span>
- <span data-ttu-id="707a0-698">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-698">'Identity'</span></span>
- <span data-ttu-id="707a0-699">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-699">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-700">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-700">'Razor'</span></span>
- <span data-ttu-id="707a0-701">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-701">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-702">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-703">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-703">'Blazor'</span></span>
- <span data-ttu-id="707a0-704">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-704">'Identity'</span></span>
- <span data-ttu-id="707a0-705">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-705">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-706">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-706">'Razor'</span></span>
- <span data-ttu-id="707a0-707">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-707">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-708">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-709">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-709">'Blazor'</span></span>
- <span data-ttu-id="707a0-710">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-710">'Identity'</span></span>
- <span data-ttu-id="707a0-711">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-711">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-712">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-712">'Razor'</span></span>
- <span data-ttu-id="707a0-713">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-713">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-714">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-715">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-715">'Blazor'</span></span>
- <span data-ttu-id="707a0-716">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-716">'Identity'</span></span>
- <span data-ttu-id="707a0-717">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-717">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-718">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-718">'Razor'</span></span>
- <span data-ttu-id="707a0-719">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-719">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-720">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-721">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-721">'Blazor'</span></span>
- <span data-ttu-id="707a0-722">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-722">'Identity'</span></span>
- <span data-ttu-id="707a0-723">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-723">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-724">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-724">'Razor'</span></span>
- <span data-ttu-id="707a0-725">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-725">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-726">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-727">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-727">'Blazor'</span></span>
- <span data-ttu-id="707a0-728">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-728">'Identity'</span></span>
- <span data-ttu-id="707a0-729">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-729">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-730">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-730">'Razor'</span></span>
- <span data-ttu-id="707a0-731">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-731">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-732">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-733">'Blazor'</span></span>
- <span data-ttu-id="707a0-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-734">'Identity'</span></span>
- <span data-ttu-id="707a0-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-736">'Razor'</span></span>
- <span data-ttu-id="707a0-737">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-737">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-739">'Blazor'</span></span>
- <span data-ttu-id="707a0-740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-740">'Identity'</span></span>
- <span data-ttu-id="707a0-741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-741">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-742">'Razor'</span></span>
- <span data-ttu-id="707a0-743">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-745">'Blazor'</span></span>
- <span data-ttu-id="707a0-746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-746">'Identity'</span></span>
- <span data-ttu-id="707a0-747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-747">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-748">'Razor'</span></span>
- <span data-ttu-id="707a0-749">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-751">'Blazor'</span></span>
- <span data-ttu-id="707a0-752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-752">'Identity'</span></span>
- <span data-ttu-id="707a0-753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-753">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-754">'Razor'</span></span>
- <span data-ttu-id="707a0-755">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-755">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-756">---------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-757">'Blazor'</span></span>
- <span data-ttu-id="707a0-758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-758">'Identity'</span></span>
- <span data-ttu-id="707a0-759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-759">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-760">'Razor'</span></span>
- <span data-ttu-id="707a0-761">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-763">'Blazor'</span></span>
- <span data-ttu-id="707a0-764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-764">'Identity'</span></span>
- <span data-ttu-id="707a0-765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-765">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-766">'Razor'</span></span>
- <span data-ttu-id="707a0-767">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-769">'Blazor'</span></span>
- <span data-ttu-id="707a0-770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-770">'Identity'</span></span>
- <span data-ttu-id="707a0-771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-771">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-772">'Razor'</span></span>
- <span data-ttu-id="707a0-773">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-775">'Blazor'</span></span>
- <span data-ttu-id="707a0-776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-776">'Identity'</span></span>
- <span data-ttu-id="707a0-777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-777">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-778">'Razor'</span></span>
- <span data-ttu-id="707a0-779">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-781">'Blazor'</span></span>
- <span data-ttu-id="707a0-782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-782">'Identity'</span></span>
- <span data-ttu-id="707a0-783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-783">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-784">'Razor'</span></span>
- <span data-ttu-id="707a0-785">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-787">'Blazor'</span></span>
- <span data-ttu-id="707a0-788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-788">'Identity'</span></span>
- <span data-ttu-id="707a0-789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-789">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-790">'Razor'</span></span>
- <span data-ttu-id="707a0-791">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-793">'Blazor'</span></span>
- <span data-ttu-id="707a0-794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-794">'Identity'</span></span>
- <span data-ttu-id="707a0-795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-795">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-796">'Razor'</span></span>
- <span data-ttu-id="707a0-797">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-799">'Blazor'</span></span>
- <span data-ttu-id="707a0-800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-800">'Identity'</span></span>
- <span data-ttu-id="707a0-801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-801">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-802">'Razor'</span></span>
- <span data-ttu-id="707a0-803">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-805">'Blazor'</span></span>
- <span data-ttu-id="707a0-806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-806">'Identity'</span></span>
- <span data-ttu-id="707a0-807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-807">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-808">'Razor'</span></span>
- <span data-ttu-id="707a0-809">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="707a0-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-811">'Blazor'</span></span>
- <span data-ttu-id="707a0-812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-812">'Identity'</span></span>
- <span data-ttu-id="707a0-813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-813">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-814">'Razor'</span></span>
- <span data-ttu-id="707a0-815">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-815">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="707a0-816">-------------: | :--- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="707a0-817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="707a0-817">'Blazor'</span></span>
- <span data-ttu-id="707a0-818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="707a0-818">'Identity'</span></span>
- <span data-ttu-id="707a0-819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="707a0-819">'Let's Encrypt'</span></span>
- <span data-ttu-id="707a0-820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="707a0-820">'Razor'</span></span>
- <span data-ttu-id="707a0-821">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="707a0-821">'SignalR' uid:</span></span> 

<span data-ttu-id="707a0-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="707a0-822">----: | | `Add{LIFETIME}<{SERVICE}, {IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="707a0-823">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="707a0-823">Example:</span></span><br><span data-ttu-id="707a0-824">`services.AddSingleton<IMyDep, MyDep>();` | Ja | Ja | Nein | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="707a0-824">`services.AddSingleton<IMyDep, MyDep>();` | Yes | Yes | No | | `Add{LIFETIME}<{SERVICE}>(sp => new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="707a0-825">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-825">Examples:</span></span><br>`services.AddSingleton<IMyDep>(sp => new MyDep());`<br><span data-ttu-id="707a0-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Ja | Ja | Ja | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span><span class="sxs-lookup"><span data-stu-id="707a0-826">`services.AddSingleton<IMyDep>(sp => new MyDep("A string!"));` | Yes | Yes | Yes | | `Add{LIFETIME}<{IMPLEMENTATION}>()`</span></span><br><span data-ttu-id="707a0-827">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="707a0-827">Example:</span></span><br><span data-ttu-id="707a0-828">`services.AddSingleton<MyDep>();` | Ja | Nein | Nein | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="707a0-828">`services.AddSingleton<MyDep>();` | Yes | No | No | | `AddSingleton<{SERVICE}>(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="707a0-829">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-829">Examples:</span></span><br>`services.AddSingleton<IMyDep>(new MyDep());`<br><span data-ttu-id="707a0-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | Nein | Ja | Ja | | `AddSingleton(new {IMPLEMENTATION})`</span><span class="sxs-lookup"><span data-stu-id="707a0-830">`services.AddSingleton<IMyDep>(new MyDep("A string!"));` | No | Yes | Yes | | `AddSingleton(new {IMPLEMENTATION})`</span></span><br><span data-ttu-id="707a0-831">Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-831">Examples:</span></span><br>`services.AddSingleton(new MyDep());`<br><span data-ttu-id="707a0-832">`services.AddSingleton(new MyDep("A string!"));` | Nein | Nein | Ja |</span><span class="sxs-lookup"><span data-stu-id="707a0-832">`services.AddSingleton(new MyDep("A string!"));` | No | No | Yes |</span></span>

<span data-ttu-id="707a0-833">Weitere Informationen zum Löschen von Typen finden Sie im Abschnitt [Löschen von Diensten](#disposal-of-services).</span><span class="sxs-lookup"><span data-stu-id="707a0-833">For more information on type disposal, see the [Disposal of services](#disposal-of-services) section.</span></span> <span data-ttu-id="707a0-834">Ein häufiges Szenario für mehrere Implementierungen ist [Typen zu Testzwecken simulieren](xref:test/integration-tests#inject-mock-services).</span><span class="sxs-lookup"><span data-stu-id="707a0-834">A common scenario for multiple implementations is [mocking types for testing](xref:test/integration-tests#inject-mock-services).</span></span>

<span data-ttu-id="707a0-835">`TryAdd{LIFETIME}`-Methoden registrieren den Dienst nur, wenn noch keine Implementierung registriert ist.</span><span class="sxs-lookup"><span data-stu-id="707a0-835">`TryAdd{LIFETIME}` methods only register the service if there isn't already an implementation registered.</span></span>

<span data-ttu-id="707a0-836">Im folgenden Beispiel registriert die erste Zeile `MyDependency` für `IMyDependency`.</span><span class="sxs-lookup"><span data-stu-id="707a0-836">In the following example, the first line registers `MyDependency` for `IMyDependency`.</span></span> <span data-ttu-id="707a0-837">Die zweite Zeile hat keine Auswirkungen, da es für `IMyDependency` bereits eine registrierte Implementierung gibt:</span><span class="sxs-lookup"><span data-stu-id="707a0-837">The second line has no effect because `IMyDependency` already has a registered implementation:</span></span>

```csharp
services.AddSingleton<IMyDependency, MyDependency>();
// The following line has no effect:
services.TryAddSingleton<IMyDependency, DifferentDependency>();
```

<span data-ttu-id="707a0-838">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="707a0-838">For more information, see:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAdd*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddTransient*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddScoped*>
* <xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddSingleton*>

<span data-ttu-id="707a0-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*)-Methoden registrieren den Dienst nur, wenn es nicht bereits eine Implementierung *des gleichen Typs* gibt.</span><span class="sxs-lookup"><span data-stu-id="707a0-839">[TryAddEnumerable(ServiceDescriptor)](xref:Microsoft.Extensions.DependencyInjection.Extensions.ServiceCollectionDescriptorExtensions.TryAddEnumerable*) methods only register the service if there isn't already an implementation *of the same type*.</span></span> <span data-ttu-id="707a0-840">Mehrere Dienste werden über `IEnumerable<{SERVICE}>` aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="707a0-840">Multiple services are resolved via `IEnumerable<{SERVICE}>`.</span></span> <span data-ttu-id="707a0-841">Beim Registrieren von Diensten möchte der Entwickler nur eine Instanz hinzufügen, wenn nicht bereits eine Instanz vom gleichen Typ hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="707a0-841">When registering services, the developer only wants to add an instance if one of the same type hasn't already been added.</span></span> <span data-ttu-id="707a0-842">Diese Methode wird in der Regel von Bibliotheksautoren verwendet, um zu vermeiden, dass zwei Kopien einer Instanz im Container registriert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-842">Generally, this method is used by library authors to avoid registering two copies of an instance in the container.</span></span>

<span data-ttu-id="707a0-843">Im folgenden Beispiel registriert die erste Zeile `MyDep` für `IMyDep1`.</span><span class="sxs-lookup"><span data-stu-id="707a0-843">In the following example, the first line registers `MyDep` for `IMyDep1`.</span></span> <span data-ttu-id="707a0-844">Die zweite Zeile registriert `MyDep` für `IMyDep2`.</span><span class="sxs-lookup"><span data-stu-id="707a0-844">The second line registers `MyDep` for `IMyDep2`.</span></span> <span data-ttu-id="707a0-845">Die dritte Zeile hat keine Auswirkungen, da es für `IMyDep1` bereits eine registrierte Implementierung von `MyDep` gibt:</span><span class="sxs-lookup"><span data-stu-id="707a0-845">The third line has no effect because `IMyDep1` already has a registered implementation of `MyDep`:</span></span>

```csharp
public interface IMyDep1 {}
public interface IMyDep2 {}

public class MyDep : IMyDep1, IMyDep2 {}

services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep2, MyDep>());
// Two registrations of MyDep for IMyDep1 is avoided by the following line:
services.TryAddEnumerable(ServiceDescriptor.Singleton<IMyDep1, MyDep>());
```

### <a name="constructor-injection-behavior"></a><span data-ttu-id="707a0-846">Verhalten von Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="707a0-846">Constructor injection behavior</span></span>

<span data-ttu-id="707a0-847">Dienste können durch zwei Mechanismen aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-847">Services can be resolved by two mechanisms:</span></span>

* <xref:System.IServiceProvider>
* <span data-ttu-id="707a0-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Lässt die Erstellung von Objekten ohne Dienstregistrierung im Dependency-Injection-Container zu</span><span class="sxs-lookup"><span data-stu-id="707a0-848"><xref:Microsoft.Extensions.DependencyInjection.ActivatorUtilities>: Permits object creation without service registration in the dependency injection container.</span></span> <span data-ttu-id="707a0-849">`ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.</span><span class="sxs-lookup"><span data-stu-id="707a0-849">`ActivatorUtilities` is used with user-facing abstractions, such as Tag Helpers, MVC controllers, and model binders.</span></span>

<span data-ttu-id="707a0-850">Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.</span><span class="sxs-lookup"><span data-stu-id="707a0-850">Constructors can accept arguments that aren't provided by dependency injection, but the arguments must assign default values.</span></span>

<span data-ttu-id="707a0-851">Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) einen *öffentlichen* Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="707a0-851">When services are resolved by `IServiceProvider` or `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires a *public* constructor.</span></span>

<span data-ttu-id="707a0-852">Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection), dass nur ein anwendbarer Konstruktor vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="707a0-852">When services are resolved by `ActivatorUtilities`, [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) requires that only one applicable constructor exists.</span></span> <span data-ttu-id="707a0-853">Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="707a0-853">Constructor overloads are supported, but only one overload can exist whose arguments can all be fulfilled by dependency injection.</span></span>

## <a name="entity-framework-contexts"></a><span data-ttu-id="707a0-854">Entity Framework-Kontexte</span><span class="sxs-lookup"><span data-stu-id="707a0-854">Entity Framework contexts</span></span>

<span data-ttu-id="707a0-855">Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-855">Entity Framework contexts are usually added to the service container using the [scoped lifetime](#service-lifetimes) because web app database operations are normally scoped to the client request.</span></span> <span data-ttu-id="707a0-856">Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)-Überladung angegeben wird, wenn der Datenbankkontext registriert wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-856">The default lifetime is scoped if a lifetime isn't specified by an [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext) overload when registering the database context.</span></span> <span data-ttu-id="707a0-857">Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.</span><span class="sxs-lookup"><span data-stu-id="707a0-857">Services of a given lifetime shouldn't use a database context with a shorter lifetime than the service.</span></span>

## <a name="lifetime-and-registration-options"></a><span data-ttu-id="707a0-858">Lebensdauer und Registrierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="707a0-858">Lifetime and registration options</span></span>

<span data-ttu-id="707a0-859">Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen.</span><span class="sxs-lookup"><span data-stu-id="707a0-859">To demonstrate the difference between the lifetime and registration options, consider the following interfaces that represent tasks as an operation with a unique identifier, `OperationId`.</span></span> <span data-ttu-id="707a0-860">Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="707a0-860">Depending on how the lifetime of an operations service is configured for the following interfaces, the container provides either the same or a different instance of the service when requested by a class:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

<span data-ttu-id="707a0-861">Die Schnittstellen sind in die Klasse `Operation` implementiert.</span><span class="sxs-lookup"><span data-stu-id="707a0-861">The interfaces are implemented in the `Operation` class.</span></span> <span data-ttu-id="707a0-862">Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:</span><span class="sxs-lookup"><span data-stu-id="707a0-862">The `Operation` constructor generates a GUID if one isn't supplied:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

<span data-ttu-id="707a0-863">`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab.</span><span class="sxs-lookup"><span data-stu-id="707a0-863">An `OperationService` is registered that depends on each of the other `Operation` types.</span></span> <span data-ttu-id="707a0-864">Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="707a0-864">When `OperationService` is requested via dependency injection, it receives either a new instance of each service or an existing instance based on the lifetime of the dependent service.</span></span>

* <span data-ttu-id="707a0-865">Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`.</span><span class="sxs-lookup"><span data-stu-id="707a0-865">When transient services are created when requested from the container, the `OperationId` of the `IOperationTransient` service is different than the `OperationId` of the `OperationService`.</span></span> <span data-ttu-id="707a0-866">`OperationService` erhält eine neue Instanz der Klasse `IOperationTransient`.</span><span class="sxs-lookup"><span data-stu-id="707a0-866">`OperationService` receives a new instance of the `IOperationTransient` class.</span></span> <span data-ttu-id="707a0-867">Der `OperationId`-Wert der neuen Instanz ist anders.</span><span class="sxs-lookup"><span data-stu-id="707a0-867">The new instance yields a different `OperationId`.</span></span>
* <span data-ttu-id="707a0-868">Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung.</span><span class="sxs-lookup"><span data-stu-id="707a0-868">When scoped services are created per client request, the `OperationId` of the `IOperationScoped` service is the same as that of `OperationService` within a client request.</span></span> <span data-ttu-id="707a0-869">Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.</span><span class="sxs-lookup"><span data-stu-id="707a0-869">Across client requests, both services share a different `OperationId` value.</span></span>
* <span data-ttu-id="707a0-870">Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.</span><span class="sxs-lookup"><span data-stu-id="707a0-870">When singleton and singleton-instance services are created once and used across all client requests and all services, the `OperationId` is constant across all service requests.</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

<span data-ttu-id="707a0-871">In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="707a0-871">In `Startup.ConfigureServices`, each type is added to the container according to its named lifetime:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

<span data-ttu-id="707a0-872">Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`.</span><span class="sxs-lookup"><span data-stu-id="707a0-872">The `IOperationSingletonInstance` service is using a specific instance with a known ID of `Guid.Empty`.</span></span> <span data-ttu-id="707a0-873">Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).</span><span class="sxs-lookup"><span data-stu-id="707a0-873">It's clear when this type is in use (its GUID is all zeroes).</span></span>

<span data-ttu-id="707a0-874">Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an.</span><span class="sxs-lookup"><span data-stu-id="707a0-874">The sample app demonstrates object lifetimes within and between individual requests.</span></span> <span data-ttu-id="707a0-875">Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an.</span><span class="sxs-lookup"><span data-stu-id="707a0-875">The sample app's `IndexModel` requests each kind of `IOperation` type and the `OperationService`.</span></span> <span data-ttu-id="707a0-876">Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:</span><span class="sxs-lookup"><span data-stu-id="707a0-876">The page then displays all of the page model class's and service's `OperationId` values through property assignments:</span></span>

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

<span data-ttu-id="707a0-877">Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:</span><span class="sxs-lookup"><span data-stu-id="707a0-877">Two following output shows the results of two requests:</span></span>

<span data-ttu-id="707a0-878">**Erste Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="707a0-878">**First request:**</span></span>

<span data-ttu-id="707a0-879">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-879">Controller operations:</span></span>

<span data-ttu-id="707a0-880">Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518</span><span class="sxs-lookup"><span data-stu-id="707a0-880">Transient: d233e165-f417-469b-a866-1cf1935d2518</span></span>  
<span data-ttu-id="707a0-881">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="707a0-881">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="707a0-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-882">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-883">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-883">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-884">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-884">`OperationService` operations:</span></span>

<span data-ttu-id="707a0-885">Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span><span class="sxs-lookup"><span data-stu-id="707a0-885">Transient: c6b049eb-1318-4e31-90f1-eb2dd849ff64</span></span>  
<span data-ttu-id="707a0-886">Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span><span class="sxs-lookup"><span data-stu-id="707a0-886">Scoped: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19</span></span>  
<span data-ttu-id="707a0-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-887">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-888">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-888">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-889">**Zweite Anforderung:**</span><span class="sxs-lookup"><span data-stu-id="707a0-889">**Second request:**</span></span>

<span data-ttu-id="707a0-890">Controllervorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-890">Controller operations:</span></span>

<span data-ttu-id="707a0-891">Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0</span><span class="sxs-lookup"><span data-stu-id="707a0-891">Transient: b63bd538-0a37-4ff1-90ba-081c5138dda0</span></span>  
<span data-ttu-id="707a0-892">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="707a0-892">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="707a0-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-893">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-894">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-894">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-895">`OperationService`-Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="707a0-895">`OperationService` operations:</span></span>

<span data-ttu-id="707a0-896">Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span><span class="sxs-lookup"><span data-stu-id="707a0-896">Transient: c4cbacb8-36a2-436d-81c8-8c1b78808aaf</span></span>  
<span data-ttu-id="707a0-897">Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4</span><span class="sxs-lookup"><span data-stu-id="707a0-897">Scoped: 31e820c5-4834-4d22-83fc-a60118acb9f4</span></span>  
<span data-ttu-id="707a0-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span><span class="sxs-lookup"><span data-stu-id="707a0-898">Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9</span></span>  
<span data-ttu-id="707a0-899">Instanz: 00000000-0000-0000-0000-000000000000</span><span class="sxs-lookup"><span data-stu-id="707a0-899">Instance: 00000000-0000-0000-0000-000000000000</span></span>

<span data-ttu-id="707a0-900">Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:</span><span class="sxs-lookup"><span data-stu-id="707a0-900">Observe which of the `OperationId` values vary within a request and between requests:</span></span>

* <span data-ttu-id="707a0-901">Objekte vom Typ *Vorübergehend* sind immer unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="707a0-901">*Transient* objects are always different.</span></span> <span data-ttu-id="707a0-902">Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="707a0-902">The transient `OperationId` value for both the first and second client requests are different for both `OperationService` operations and across client requests.</span></span> <span data-ttu-id="707a0-903">Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-903">A new instance is provided to each service request and client request.</span></span>
* <span data-ttu-id="707a0-904">*Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.</span><span class="sxs-lookup"><span data-stu-id="707a0-904">*Scoped* objects are the same within a client request but different across client requests.</span></span>
* <span data-ttu-id="707a0-905">Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `Startup.ConfigureServices` bereitgestellt wird).</span><span class="sxs-lookup"><span data-stu-id="707a0-905">*Singleton* objects are the same for every object and every request regardless of whether an `Operation` instance is provided in `Startup.ConfigureServices`.</span></span>

## <a name="call-services-from-main"></a><span data-ttu-id="707a0-906">Abrufen von Diensten aus „Main“</span><span class="sxs-lookup"><span data-stu-id="707a0-906">Call services from main</span></span>

<span data-ttu-id="707a0-907">Erstellen Sie <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> mit [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="707a0-907">Create an <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> with [IServiceScopeFactory.CreateScope](xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory.CreateScope*) to resolve a scoped service within the app's scope.</span></span> <span data-ttu-id="707a0-908">Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen.</span><span class="sxs-lookup"><span data-stu-id="707a0-908">This approach is useful to access a scoped service at startup to run initialization tasks.</span></span> <span data-ttu-id="707a0-909">Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:</span><span class="sxs-lookup"><span data-stu-id="707a0-909">The following example shows how to obtain a context for the `MyScopedService` in `Program.Main`:</span></span>

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

## <a name="scope-validation"></a><span data-ttu-id="707a0-910">Bereichsvalidierung</span><span class="sxs-lookup"><span data-stu-id="707a0-910">Scope validation</span></span>

<span data-ttu-id="707a0-911">Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:</span><span class="sxs-lookup"><span data-stu-id="707a0-911">When the app is running in the Development environment, the default service provider performs checks to verify that:</span></span>

* <span data-ttu-id="707a0-912">Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden</span><span class="sxs-lookup"><span data-stu-id="707a0-912">Scoped services aren't directly or indirectly resolved from the root service provider.</span></span>
* <span data-ttu-id="707a0-913">Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden</span><span class="sxs-lookup"><span data-stu-id="707a0-913">Scoped services aren't directly or indirectly injected into singletons.</span></span>

<span data-ttu-id="707a0-914">Der Stammdienstanbieter wird erstellt, wenn <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-914">The root service provider is created when <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionContainerBuilderExtensions.BuildServiceProvider*> is called.</span></span> <span data-ttu-id="707a0-915">Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-915">The root service provider's lifetime corresponds to the app/server's lifetime when the provider starts with the app and is disposed when the app shuts down.</span></span>

<span data-ttu-id="707a0-916">Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat.</span><span class="sxs-lookup"><span data-stu-id="707a0-916">Scoped services are disposed by the container that created them.</span></span> <span data-ttu-id="707a0-917">Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-917">If a scoped service is created in the root container, the service's lifetime is effectively promoted to singleton because it's only disposed by the root container when app/server is shut down.</span></span> <span data-ttu-id="707a0-918">Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-918">Validating service scopes catches these situations when `BuildServiceProvider` is called.</span></span>

<span data-ttu-id="707a0-919">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.</span><span class="sxs-lookup"><span data-stu-id="707a0-919">For more information, see <xref:fundamentals/host/web-host#scope-validation>.</span></span>

## <a name="request-services"></a><span data-ttu-id="707a0-920">Anfordern von Diensten</span><span class="sxs-lookup"><span data-stu-id="707a0-920">Request Services</span></span>

<span data-ttu-id="707a0-921">Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)-Sammlung verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="707a0-921">The services available within an ASP.NET Core request from `HttpContext` are exposed through the [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) collection.</span></span>

<span data-ttu-id="707a0-922">Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-922">Request Services represent the services configured and requested as part of the app.</span></span> <span data-ttu-id="707a0-923">Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).</span><span class="sxs-lookup"><span data-stu-id="707a0-923">When the objects specify dependencies, these are satisfied by the types found in `RequestServices`, not `ApplicationServices`.</span></span>

<span data-ttu-id="707a0-924">Generell sollte die App diese Eigenschaften nicht direkt verwenden.</span><span class="sxs-lookup"><span data-stu-id="707a0-924">Generally, the app shouldn't use these properties directly.</span></span> <span data-ttu-id="707a0-925">Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen.</span><span class="sxs-lookup"><span data-stu-id="707a0-925">Instead, request the types that classes require via class constructors and allow the framework inject the dependencies.</span></span> <span data-ttu-id="707a0-926">So erhalten Sie Klassen, die einfacher getestet werden können.</span><span class="sxs-lookup"><span data-stu-id="707a0-926">This yields classes that are easier to test.</span></span>

> [!NOTE]
> <span data-ttu-id="707a0-927">Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.</span><span class="sxs-lookup"><span data-stu-id="707a0-927">Prefer requesting dependencies as constructor parameters to accessing the `RequestServices` collection.</span></span>

## <a name="design-services-for-dependency-injection"></a><span data-ttu-id="707a0-928">Entwerfen von Diensten für die Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="707a0-928">Design services for dependency injection</span></span>

<span data-ttu-id="707a0-929">Best Practices:</span><span class="sxs-lookup"><span data-stu-id="707a0-929">Best practices are to:</span></span>

* <span data-ttu-id="707a0-930">Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="707a0-930">Design services to use dependency injection to obtain their dependencies.</span></span>
* <span data-ttu-id="707a0-931">Vermeiden Sie zustandsbehaftete statische Klassen und Member.</span><span class="sxs-lookup"><span data-stu-id="707a0-931">Avoid stateful, static classes and members.</span></span> <span data-ttu-id="707a0-932">Entwerfen Sie Apps so, dass stattdessen Singletondienste verwendet werden. Diese vermeiden das Entstehen eines globalen Zustands.</span><span class="sxs-lookup"><span data-stu-id="707a0-932">Design apps to use singleton services instead, which avoid creating global state.</span></span>
* <span data-ttu-id="707a0-933">Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten.</span><span class="sxs-lookup"><span data-stu-id="707a0-933">Avoid direct instantiation of dependent classes within services.</span></span> <span data-ttu-id="707a0-934">Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.</span><span class="sxs-lookup"><span data-stu-id="707a0-934">Direct instantiation couples the code to a particular implementation.</span></span>
* <span data-ttu-id="707a0-935">Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.</span><span class="sxs-lookup"><span data-stu-id="707a0-935">Make app classes small, well-factored, and easily tested.</span></span>

<span data-ttu-id="707a0-936">Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt.</span><span class="sxs-lookup"><span data-stu-id="707a0-936">If a class seems to have too many injected dependencies, this is generally a sign that the class has too many responsibilities and is violating the [Single Responsibility Principle (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility).</span></span> <span data-ttu-id="707a0-937">Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben.</span><span class="sxs-lookup"><span data-stu-id="707a0-937">Attempt to refactor the class by moving some of its responsibilities into a new class.</span></span> <span data-ttu-id="707a0-938">Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt.</span><span class="sxs-lookup"><span data-stu-id="707a0-938">Keep in mind that Razor Pages page model classes and MVC controller classes should focus on UI concerns.</span></span> <span data-ttu-id="707a0-939">Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).</span><span class="sxs-lookup"><span data-stu-id="707a0-939">Business rules and data access implementation details should be kept in classes appropriate to these [separate concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span>

### <a name="disposal-of-services"></a><span data-ttu-id="707a0-940">Löschen von Diensten</span><span class="sxs-lookup"><span data-stu-id="707a0-940">Disposal of services</span></span>

<span data-ttu-id="707a0-941">Der Container ruft <xref:System.IDisposable.Dispose*> für die erstellten <xref:System.IDisposable>-Typen auf.</span><span class="sxs-lookup"><span data-stu-id="707a0-941">The container calls <xref:System.IDisposable.Dispose*> for the <xref:System.IDisposable> types it creates.</span></span> <span data-ttu-id="707a0-942">Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.</span><span class="sxs-lookup"><span data-stu-id="707a0-942">If an instance is added to the container by user code, it isn't disposed automatically.</span></span>

<span data-ttu-id="707a0-943">Im folgenden Beispiel werden die Dienste vom Dienstcontainer erstellt und automatisch verworfen:</span><span class="sxs-lookup"><span data-stu-id="707a0-943">In the following example, the services are created by the service container and disposed automatically:</span></span>

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

<span data-ttu-id="707a0-944">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="707a0-944">In the following example:</span></span>

* <span data-ttu-id="707a0-945">werden die Dienstinstanzen nicht vom Dienstcontainer erstellt.</span><span class="sxs-lookup"><span data-stu-id="707a0-945">The service instances aren't created by the service container.</span></span>
* <span data-ttu-id="707a0-946">kennt das Framework die geplante Lebensdauer der Dienste nicht.</span><span class="sxs-lookup"><span data-stu-id="707a0-946">The intended service lifetimes aren't known by the framework.</span></span>
* <span data-ttu-id="707a0-947">verwirft das Framework die Dienste nicht automatisch.</span><span class="sxs-lookup"><span data-stu-id="707a0-947">The framework doesn't dispose of the services automatically.</span></span>
* <span data-ttu-id="707a0-948">werden Dienste, die nicht explizit im Entwicklercode verworfen werden, weiter ausgeführt, bis die App beendet wird.</span><span class="sxs-lookup"><span data-stu-id="707a0-948">If the services aren't explicitly disposed in developer code, they persist until the app shuts down.</span></span>

```csharp
public class Service1 : IDisposable {}
public class Service2 : IDisposable {}

public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<Service1>(new Service1());
    services.AddSingleton(new Service2());
}
```

## <a name="default-service-container-replacement"></a><span data-ttu-id="707a0-949">Ersetzen von Standarddienstcontainern</span><span class="sxs-lookup"><span data-stu-id="707a0-949">Default service container replacement</span></span>

<span data-ttu-id="707a0-950">Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="707a0-950">The built-in service container is designed to serve the needs of the framework and most consumer apps.</span></span> <span data-ttu-id="707a0-951">Die Verwendung des integrierten Containers wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das von diesem nicht unterstützt wird. Einige Beispiele:</span><span class="sxs-lookup"><span data-stu-id="707a0-951">We recommend using the built-in container unless you need a specific feature that the built-in container doesn't support, such as:</span></span>

* <span data-ttu-id="707a0-952">Eigenschaftsinjektion</span><span class="sxs-lookup"><span data-stu-id="707a0-952">Property injection</span></span>
* <span data-ttu-id="707a0-953">Auf Namen basierende Injektion</span><span class="sxs-lookup"><span data-stu-id="707a0-953">Injection based on name</span></span>
* <span data-ttu-id="707a0-954">Untergeordnete Container</span><span class="sxs-lookup"><span data-stu-id="707a0-954">Child containers</span></span>
* <span data-ttu-id="707a0-955">Benutzerdefinierte Verwaltung der Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="707a0-955">Custom lifetime management</span></span>
* <span data-ttu-id="707a0-956">`Func<T>`-Unterstützung für die verzögerte Initialisierung</span><span class="sxs-lookup"><span data-stu-id="707a0-956">`Func<T>` support for lazy initialization</span></span>
* <span data-ttu-id="707a0-957">Konventionsbasierte Registrierung</span><span class="sxs-lookup"><span data-stu-id="707a0-957">Convention-based registration</span></span>

<span data-ttu-id="707a0-958">Die folgenden Container von Drittanbietern können mit ASP.NET Core-Apps verwendet werden:</span><span class="sxs-lookup"><span data-stu-id="707a0-958">The following 3rd party containers can be used with ASP.NET Core apps:</span></span>

* [<span data-ttu-id="707a0-959">Autofac</span><span class="sxs-lookup"><span data-stu-id="707a0-959">Autofac</span></span>](https://autofac.readthedocs.io/en/latest/integration/aspnetcore.html)
* [<span data-ttu-id="707a0-960">DryIoc</span><span class="sxs-lookup"><span data-stu-id="707a0-960">DryIoc</span></span>](https://www.nuget.org/packages/DryIoc.Microsoft.DependencyInjection)
* [<span data-ttu-id="707a0-961">Grace</span><span class="sxs-lookup"><span data-stu-id="707a0-961">Grace</span></span>](https://www.nuget.org/packages/Grace.DependencyInjection.Extensions)
* [<span data-ttu-id="707a0-962">LightInject</span><span class="sxs-lookup"><span data-stu-id="707a0-962">LightInject</span></span>](https://github.com/seesharper/LightInject.Microsoft.DependencyInjection)
* [<span data-ttu-id="707a0-963">Lamar</span><span class="sxs-lookup"><span data-stu-id="707a0-963">Lamar</span></span>](https://jasperfx.github.io/lamar/)
* [<span data-ttu-id="707a0-964">Stashbox</span><span class="sxs-lookup"><span data-stu-id="707a0-964">Stashbox</span></span>](https://github.com/z4kn4fein/stashbox-extensions-dependencyinjection)
* [<span data-ttu-id="707a0-965">Unity</span><span class="sxs-lookup"><span data-stu-id="707a0-965">Unity</span></span>](https://www.nuget.org/packages/Unity.Microsoft.DependencyInjection)

### <a name="thread-safety"></a><span data-ttu-id="707a0-966">Threadsicherheit</span><span class="sxs-lookup"><span data-stu-id="707a0-966">Thread safety</span></span>

<span data-ttu-id="707a0-967">Erstellen Sie threadsichere Singleton-Dienste.</span><span class="sxs-lookup"><span data-stu-id="707a0-967">Create thread-safe singleton services.</span></span> <span data-ttu-id="707a0-968">Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="707a0-968">If a singleton service has a dependency on a transient service, the transient service may also require thread safety depending how it's used by the singleton.</span></span>

<span data-ttu-id="707a0-969">Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), muss nicht threadsicher sein.</span><span class="sxs-lookup"><span data-stu-id="707a0-969">The factory method of single service, such as the second argument to [AddSingleton\<TService>(IServiceCollection, Func\<IServiceProvider,TService>)](xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*), doesn't need to be thread-safe.</span></span> <span data-ttu-id="707a0-970">Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.</span><span class="sxs-lookup"><span data-stu-id="707a0-970">Like a type (`static`) constructor, it's guaranteed to be called once by a single thread.</span></span>

## <a name="recommendations"></a><span data-ttu-id="707a0-971">Empfehlungen</span><span class="sxs-lookup"><span data-stu-id="707a0-971">Recommendations</span></span>

* <span data-ttu-id="707a0-972">`async/await`- und `Task`-basierte Dienstauflösung wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="707a0-972">`async/await` and `Task` based service resolution is not supported.</span></span> <span data-ttu-id="707a0-973">C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="707a0-973">C# does not support asynchronous constructors; therefore, the recommended pattern is to use asynchronous methods after synchronously resolving the service.</span></span>

* <span data-ttu-id="707a0-974">Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer.</span><span class="sxs-lookup"><span data-stu-id="707a0-974">Avoid storing data and configuration directly in the service container.</span></span> <span data-ttu-id="707a0-975">Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-975">For example, a user's shopping cart shouldn't typically be added to the service container.</span></span> <span data-ttu-id="707a0-976">Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-976">Configuration should use the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="707a0-977">Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="707a0-977">Similarly, avoid "data holder" objects that only exist to allow access to some other object.</span></span> <span data-ttu-id="707a0-978">Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="707a0-978">It's better to request the actual item via DI.</span></span>

* <span data-ttu-id="707a0-979">Vermeiden Sie den statischen Zugriff auf Dienste (z. B. statische Eingabe von [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) zur Verwendung an anderer Stelle).</span><span class="sxs-lookup"><span data-stu-id="707a0-979">Avoid static access to services (for example, statically-typing [IApplicationBuilder.ApplicationServices](xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices) for use elsewhere).</span></span>

* <span data-ttu-id="707a0-980">Vermeiden Sie die Verwendung des *Dienstlocatormusters*, da dieses [Steuerungsumkehrungsstrategien](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) vermischt.</span><span class="sxs-lookup"><span data-stu-id="707a0-980">Avoid using the *service locator pattern*, which mixes [Inversion of Control](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) strategies.</span></span>

  * <span data-ttu-id="707a0-981">Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Abhängigkeitsinjektion verwenden können:</span><span class="sxs-lookup"><span data-stu-id="707a0-981">Don't invoke <xref:System.IServiceProvider.GetService*> to obtain a service instance when you can use DI instead:</span></span>

    <span data-ttu-id="707a0-982">**Falsch:**</span><span class="sxs-lookup"><span data-stu-id="707a0-982">**Incorrect:**</span></span>

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

    <span data-ttu-id="707a0-983">**Richtig**:</span><span class="sxs-lookup"><span data-stu-id="707a0-983">**Correct**:</span></span>

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

  * <span data-ttu-id="707a0-984">Vermeiden Sie die Injektion von Factorys, die Abhängigkeiten zur Laufzeit mit <xref:System.IServiceProvider.GetService*> auflösen.</span><span class="sxs-lookup"><span data-stu-id="707a0-984">Avoid injecting a factory that resolves dependencies at runtime using <xref:System.IServiceProvider.GetService*>.</span></span>

* <span data-ttu-id="707a0-985">Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span><span class="sxs-lookup"><span data-stu-id="707a0-985">Avoid static access to `HttpContext` (for example, [IHttpContextAccessor.HttpContext](xref:Microsoft.AspNetCore.Http.IHttpContextAccessor.HttpContext)).</span></span>

<span data-ttu-id="707a0-986">Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss.</span><span class="sxs-lookup"><span data-stu-id="707a0-986">Like all sets of recommendations, you may encounter situations where ignoring a recommendation is required.</span></span> <span data-ttu-id="707a0-987">Es gibt nur wenige Ausnahmen &mdash; die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.</span><span class="sxs-lookup"><span data-stu-id="707a0-987">Exceptions are rare&mdash;mostly special cases within the framework itself.</span></span>

<span data-ttu-id="707a0-988">Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar.</span><span class="sxs-lookup"><span data-stu-id="707a0-988">DI is an *alternative* to static/global object access patterns.</span></span> <span data-ttu-id="707a0-989">Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.</span><span class="sxs-lookup"><span data-stu-id="707a0-989">You may not be able to realize the benefits of DI if you mix it with static object access.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="707a0-990">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="707a0-990">Additional resources</span></span>

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:blazor/dependency-injection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [<span data-ttu-id="707a0-991">Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)</span><span class="sxs-lookup"><span data-stu-id="707a0-991">Writing Clean Code in ASP.NET Core with Dependency Injection (MSDN)</span></span>](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [<span data-ttu-id="707a0-992">Prinzip der expliziten Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="707a0-992">Explicit Dependencies Principle</span></span>](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* <span data-ttu-id="707a0-993">[Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler)](https://www.martinfowler.com/articles/injection.html) (in englischer Sprache)</span><span class="sxs-lookup"><span data-stu-id="707a0-993">[Inversion of Control Containers and the Dependency Injection Pattern (Martin Fowler)](https://www.martinfowler.com/articles/injection.html)</span></span>
* [<span data-ttu-id="707a0-994">Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI</span><span class="sxs-lookup"><span data-stu-id="707a0-994">How to register a service with multiple interfaces in ASP.NET Core DI</span></span>](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)

::: moniker-end
