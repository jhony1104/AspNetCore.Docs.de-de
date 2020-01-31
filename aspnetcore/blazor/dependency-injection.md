---
title: ASP.net Core Blazor Abhängigkeitsinjektion
author: guardrex
description: Erfahren Sie, wie Blazor-Apps Dienste in-Komponenten einfügen können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2020
no-loc:
- Blazor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: fa6762522c831c7fbe2742dbfe4e25a377988e1e
ms.sourcegitcommit: fe41cff0b99f3920b727286944e5b652ca301640
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76869563"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="7ec96-103">ASP.net Core blazor-Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="7ec96-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="7ec96-104">Von [Rainer stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="7ec96-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="7ec96-105">Blazor unterstützt die [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="7ec96-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7ec96-106">Apps können integrierte Dienste verwenden, indem Sie Sie in Komponenten einfügen.</span><span class="sxs-lookup"><span data-stu-id="7ec96-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="7ec96-107">Apps können auch benutzerdefinierte Dienste definieren und registrieren und in der gesamten App über di verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="7ec96-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="7ec96-108">DI ist eine Technik für den Zugriff auf Dienste, die an einem zentralen Ort konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="7ec96-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="7ec96-109">Dies kann bei blazor-Apps für folgende Aktionen nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="7ec96-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="7ec96-110">Freigeben einer einzelnen Instanz einer Dienstklasse für viele Komponenten, die als *Singleton* -Dienst bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="7ec96-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="7ec96-111">Entkoppeln von Komponenten aus konkreten Dienst Klassen mithilfe von Verweis Abstraktionen.</span><span class="sxs-lookup"><span data-stu-id="7ec96-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="7ec96-112">Nehmen Sie beispielsweise eine Schnittstelle `IDataAccess` für den Zugriff auf Daten in der app.</span><span class="sxs-lookup"><span data-stu-id="7ec96-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="7ec96-113">Die Schnittstelle wird von einer konkreten `DataAccess` Klasse implementiert und als Dienst im Dienst Container der APP registriert.</span><span class="sxs-lookup"><span data-stu-id="7ec96-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="7ec96-114">Wenn eine Komponente di zum Empfangen einer `IDataAccess`-Implementierung verwendet, ist die Komponente nicht mit dem konkreten Typ gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="7ec96-115">Die-Implementierung kann ausgetauscht werden, vielleicht für eine Pseudo Implementierung in Komponententests.</span><span class="sxs-lookup"><span data-stu-id="7ec96-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="7ec96-116">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="7ec96-116">Default services</span></span>

<span data-ttu-id="7ec96-117">Standarddienste werden automatisch der Dienst Sammlung der app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="7ec96-118">Dienst</span><span class="sxs-lookup"><span data-stu-id="7ec96-118">Service</span></span> | <span data-ttu-id="7ec96-119">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="7ec96-119">Lifetime</span></span> | <span data-ttu-id="7ec96-120">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7ec96-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="7ec96-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="7ec96-121">Singleton</span></span> | <span data-ttu-id="7ec96-122">Stellt Methoden zum Senden von HTTP-Anforderungen und empfangen von HTTP-Antworten aus einer Ressource bereit, die durch einen URI identifiziert wird</span><span class="sxs-lookup"><span data-stu-id="7ec96-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span><br><br><span data-ttu-id="7ec96-123">Die Instanz von `HttpClient` in einer blazor Webassembly-App verwendet den Browser, um den HTTP-Datenverkehr im Hintergrund zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="7ec96-123">The instance of `HttpClient` in a Blazor WebAssembly app uses the browser for handling the HTTP traffic in the background.</span></span><br><br><span data-ttu-id="7ec96-124">Blazor-Server-Apps enthalten keine `HttpClient`, die standardmäßig als Dienst konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="7ec96-124">Blazor Server apps don't include an `HttpClient` configured as a service by default.</span></span> <span data-ttu-id="7ec96-125">Stellen Sie eine `HttpClient` für eine blazor-Server-App bereit.</span><span class="sxs-lookup"><span data-stu-id="7ec96-125">Provide an `HttpClient` to a Blazor Server app.</span></span><br><br><span data-ttu-id="7ec96-126">Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="7ec96-126">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="7ec96-127">Singleton (blazor-Webassembly)</span><span class="sxs-lookup"><span data-stu-id="7ec96-127">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="7ec96-128">Bereich (blazor-Server)</span><span class="sxs-lookup"><span data-stu-id="7ec96-128">Scoped (Blazor Server)</span></span> | <span data-ttu-id="7ec96-129">Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="7ec96-129">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="7ec96-130">Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="7ec96-130">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="7ec96-131">Singleton (blazor-Webassembly)</span><span class="sxs-lookup"><span data-stu-id="7ec96-131">Singleton (Blazor WebAssembly)</span></span><br><span data-ttu-id="7ec96-132">Bereich (blazor-Server)</span><span class="sxs-lookup"><span data-stu-id="7ec96-132">Scoped (Blazor Server)</span></span> | <span data-ttu-id="7ec96-133">Enthält Hilfsprogramme zum Arbeiten mit URIs und dem Navigations Zustand.</span><span class="sxs-lookup"><span data-stu-id="7ec96-133">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="7ec96-134">Weitere Informationen finden Sie unter [URI-und Navigations Zustands Hilfen](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="7ec96-134">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="7ec96-135">Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit.</span><span class="sxs-lookup"><span data-stu-id="7ec96-135">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="7ec96-136">Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="7ec96-136">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="7ec96-137">Hinzufügen von Diensten zu einer APP</span><span class="sxs-lookup"><span data-stu-id="7ec96-137">Add services to an app</span></span>

<span data-ttu-id="7ec96-138">Nachdem Sie eine neue App erstellt haben, überprüfen Sie die `Startup.ConfigureServices`-Methode:</span><span class="sxs-lookup"><span data-stu-id="7ec96-138">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="7ec96-139">Der `ConfigureServices`-Methode wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>übermittelt, das eine Liste von Dienst Deskriptor-Objekten ist (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="7ec96-139">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="7ec96-140">Dienste werden hinzugefügt, indem Dienst Deskriptoren für die Dienst Sammlung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="7ec96-140">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="7ec96-141">Im folgenden Beispiel wird das Konzept mit der `IDataAccess`-Schnittstelle und deren konkreten Implementierungs `DataAccess`veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="7ec96-141">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="7ec96-142">Dienste können mit der in der folgenden Tabelle angezeigten Lebensdauer konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="7ec96-142">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="7ec96-143">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="7ec96-143">Lifetime</span></span> | <span data-ttu-id="7ec96-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="7ec96-144">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor<span data-ttu-id="7ec96-145"> Webassembly-apps verfügen derzeit nicht über ein Konzept von di-Bereichen.</span><span class="sxs-lookup"><span data-stu-id="7ec96-145"> WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="7ec96-146">`Scoped`registrierte Dienste Verhalten sich wie `Singleton` Dienste.</span><span class="sxs-lookup"><span data-stu-id="7ec96-146">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="7ec96-147">Das Blazor Server-Hostingmodell unterstützt jedoch die `Scoped` Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="7ec96-147">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="7ec96-148">In Blazor Server-Apps ist eine Bereichs bezogene Dienst Registrierung auf die *Verbindung*beschränkt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-148">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="7ec96-149">Aus diesem Grund wird die Verwendung von Bereichs bezogenen Diensten für Dienste bevorzugt, die auf den aktuellen Benutzer beschränkt werden sollten, auch wenn die aktuelle Absicht ist, die Client seitige Ausführung im Browser auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7ec96-149">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="7ec96-150">DI erstellt eine *einzelne Instanz* des Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="7ec96-150">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="7ec96-151">Alle Komponenten, die einen `Singleton` Dienst erfordern, erhalten eine Instanz desselben diensdienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="7ec96-151">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="7ec96-152">Wenn eine Komponente eine Instanz eines `Transient` Dienstanbieter aus dem Dienst Container erhält, empfängt Sie eine *neue Instanz* des Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="7ec96-152">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="7ec96-153">Das di-System basiert auf dem System System in ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="7ec96-153">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="7ec96-154">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7ec96-154">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="7ec96-155">Anfordern eines Dienstanbieter in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="7ec96-155">Request a service in a component</span></span>

<span data-ttu-id="7ec96-156">Nachdem die Dienste der Dienst Auflistung hinzugefügt wurden, fügen Sie die Dienste mithilfe des [\@](xref:mvc/views/razor#inject) Razor-Direktive in die Komponenten ein.</span><span class="sxs-lookup"><span data-stu-id="7ec96-156">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="7ec96-157">`@inject` verfügt über zwei Parameter:</span><span class="sxs-lookup"><span data-stu-id="7ec96-157">`@inject` has two parameters:</span></span>

* <span data-ttu-id="7ec96-158">Geben Sie &ndash; den Typ des einzuschlettenden Dienstanbieter ein.</span><span class="sxs-lookup"><span data-stu-id="7ec96-158">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="7ec96-159">Eigenschaft &ndash; der Name der Eigenschaft, die den injizierten App-Dienst empfängt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-159">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="7ec96-160">Die-Eigenschaft erfordert keine manuelle Erstellung.</span><span class="sxs-lookup"><span data-stu-id="7ec96-160">The property doesn't require manual creation.</span></span> <span data-ttu-id="7ec96-161">Der Compiler erstellt die-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="7ec96-161">The compiler creates the property.</span></span>

<span data-ttu-id="7ec96-162">Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="7ec96-162">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="7ec96-163">Verwenden Sie mehrere `@inject`-Anweisungen, um unterschiedliche Dienste einzufügen.</span><span class="sxs-lookup"><span data-stu-id="7ec96-163">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="7ec96-164">Das folgende Beispiel veranschaulicht die Verwendung von `@inject`.</span><span class="sxs-lookup"><span data-stu-id="7ec96-164">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="7ec96-165">Der Dienst, der `Services.IDataAccess` implementiert, wird in die-Eigenschaften `DataRepository`der Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-165">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="7ec96-166">Beachten Sie, dass der Code nur die `IDataAccess` Abstraktion verwendet:</span><span class="sxs-lookup"><span data-stu-id="7ec96-166">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="7ec96-167">Intern verwendet die generierte Eigenschaft (`DataRepository`) das `InjectAttribute`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="7ec96-167">Internally, the generated property (`DataRepository`) uses the `InjectAttribute` attribute.</span></span> <span data-ttu-id="7ec96-168">In der Regel wird dieses Attribut nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="7ec96-168">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="7ec96-169">Wenn eine Basisklasse für Komponenten erforderlich ist und eingefügte Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie die `InjectAttribute`manuell hinzu:</span><span class="sxs-lookup"><span data-stu-id="7ec96-169">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="7ec96-170">In Komponenten, die von der-Basisklasse abgeleitet sind, ist die `@inject`-Anweisung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7ec96-170">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="7ec96-171">Die `InjectAttribute` der Basisklasse ist ausreichend:</span><span class="sxs-lookup"><span data-stu-id="7ec96-171">The `InjectAttribute` of the base class is sufficient:</span></span>

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="7ec96-172">Verwenden von di in Diensten</span><span class="sxs-lookup"><span data-stu-id="7ec96-172">Use DI in services</span></span>

<span data-ttu-id="7ec96-173">Für komplexe Dienste sind möglicherweise zusätzliche Dienste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7ec96-173">Complex services might require additional services.</span></span> <span data-ttu-id="7ec96-174">Im vorherigen Beispiel ist für `DataAccess` möglicherweise der `HttpClient` Standard Dienst erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7ec96-174">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="7ec96-175">`@inject` (oder das `InjectAttribute`) steht nicht für die Verwendung in-Diensten zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7ec96-175">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="7ec96-176">Stattdessen muss die *Konstruktorinjektion* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="7ec96-176">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="7ec96-177">Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-177">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="7ec96-178">Wenn di den Dienst erstellt, werden die erforderlichen Dienste im Konstruktor erkannt und entsprechend bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-178">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="7ec96-179">Voraussetzungen für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="7ec96-179">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="7ec96-180">Ein Konstruktor muss vorhanden sein, dessen Argumente von di erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="7ec96-180">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="7ec96-181">Zusätzliche Parameter, die nicht von di abgedeckt werden, sind zulässig, wenn Sie Standardwerte angeben.</span><span class="sxs-lookup"><span data-stu-id="7ec96-181">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="7ec96-182">Der anwendbare Konstruktor muss *öffentlich*sein.</span><span class="sxs-lookup"><span data-stu-id="7ec96-182">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="7ec96-183">Ein anwendbarer Konstruktor muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="7ec96-183">One applicable constructor must exist.</span></span> <span data-ttu-id="7ec96-184">Bei einer Mehrdeutigkeit löst di eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="7ec96-184">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="7ec96-185">Basiskomponenten Klassen des-Hilfsprogramms zum Verwalten eines di-Bereichs</span><span class="sxs-lookup"><span data-stu-id="7ec96-185">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="7ec96-186">In ASP.net Core-apps werden Bereichs bezogene Dienste in der Regel auf die aktuelle Anforderung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-186">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="7ec96-187">Nachdem die Anforderung abgeschlossen ist, werden alle Bereichs bezogenen oder vorübergehenden Dienste vom System System entfernt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-187">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="7ec96-188">In Blazor Server-apps dauert der Anforderungs Bereich für die Dauer der Client Verbindung, was dazu führen kann, dass vorübergehende und Bereichs bezogene Dienste viel länger als erwartet Leben.</span><span class="sxs-lookup"><span data-stu-id="7ec96-188">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="7ec96-189">Um Dienste auf die Lebensdauer einer Komponente zu beschränken, können Sie die `OwningComponentBase`-und `OwningComponentBase<TService>` Basisklassen verwenden.</span><span class="sxs-lookup"><span data-stu-id="7ec96-189">To scope services to the lifetime of a component, you can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="7ec96-190">Diese Basisklassen machen eine `ScopedServices`-Eigenschaft des Typs `IServiceProvider` verfügbar, mit der Dienste aufgelöst werden, die auf die Lebensdauer der Komponente beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="7ec96-190">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="7ec96-191">Verwenden Sie die `@inherits`-Direktive, um eine Komponente zu erstellen, die von einer Basisklasse in Razor erbt.</span><span class="sxs-lookup"><span data-stu-id="7ec96-191">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```razor
@page "/users"
@attribute [Authorize]
@inherits OwningComponentBase<Data.ApplicationDbContext>

<h1>Users (@Service.Users.Count())</h1>
<ul>
    @foreach (var user in Service.Users)
    {
        <li>@user.UserName</li>
    }
</ul>
```

> [!NOTE]
> <span data-ttu-id="7ec96-192">Dienste, die mithilfe von `@inject` oder der `InjectAttribute` in die Komponente eingefügt werden, werden nicht im Bereich der Komponente erstellt und sind an den Anforderungs Bereich gebunden.</span><span class="sxs-lookup"><span data-stu-id="7ec96-192">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7ec96-193">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="7ec96-193">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
