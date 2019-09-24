---
title: ASP.net Core blazor-Abhängigkeitsinjektion
author: guardrex
description: Erfahren Sie, wie blazor-Apps Dienste in Komponenten einfügen können.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/dependency-injection
ms.openlocfilehash: 00c874e43496eaad8841db91843f41a299813aec
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207166"
---
# <a name="aspnet-core-blazor-dependency-injection"></a><span data-ttu-id="cbd0f-103">ASP.net Core blazor-Abhängigkeitsinjektion</span><span class="sxs-lookup"><span data-stu-id="cbd0f-103">ASP.NET Core Blazor dependency injection</span></span>

<span data-ttu-id="cbd0f-104">Von [Rainer stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="cbd0f-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="cbd0f-105">Blazor unterstützt die [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="cbd0f-105">Blazor supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cbd0f-106">Apps können integrierte Dienste verwenden, indem Sie Sie in Komponenten einfügen.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="cbd0f-107">Apps können auch benutzerdefinierte Dienste definieren und registrieren und in der gesamten App über di verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

<span data-ttu-id="cbd0f-108">DI ist eine Technik für den Zugriff auf Dienste, die an einem zentralen Ort konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-108">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="cbd0f-109">Dies kann bei blazor-Apps für folgende Aktionen nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-109">This can be useful in Blazor apps to:</span></span>

* <span data-ttu-id="cbd0f-110">Freigeben einer einzelnen Instanz einer Dienstklasse für viele Komponenten, die als *Singleton* -Dienst bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-110">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="cbd0f-111">Entkoppeln von Komponenten aus konkreten Dienst Klassen mithilfe von Verweis Abstraktionen.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-111">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="cbd0f-112">Stellen Sie sich beispielsweise eine `IDataAccess` Schnittstelle für den Zugriff auf Daten in der APP vor.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-112">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="cbd0f-113">Die Schnittstelle wird von einer konkreten `DataAccess` Klasse implementiert und als Dienst im Dienst Container der APP registriert.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-113">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="cbd0f-114">Wenn eine Komponente di zum Empfangen einer `IDataAccess` Implementierung verwendet, ist die Komponente nicht mit dem konkreten Typ gekoppelt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-114">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="cbd0f-115">Die-Implementierung kann ausgetauscht werden, vielleicht für eine Pseudo Implementierung in Komponententests.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-115">The implementation can be swapped, perhaps for a mock implementation in unit tests.</span></span>

## <a name="default-services"></a><span data-ttu-id="cbd0f-116">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="cbd0f-116">Default services</span></span>

<span data-ttu-id="cbd0f-117">Standarddienste werden automatisch der Dienst Sammlung der app hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-117">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="cbd0f-118">Dienst</span><span class="sxs-lookup"><span data-stu-id="cbd0f-118">Service</span></span> | <span data-ttu-id="cbd0f-119">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="cbd0f-119">Lifetime</span></span> | <span data-ttu-id="cbd0f-120">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="cbd0f-120">Description</span></span> |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="cbd0f-121">Singleton</span><span class="sxs-lookup"><span data-stu-id="cbd0f-121">Singleton</span></span> | <span data-ttu-id="cbd0f-122">Stellt Methoden zum Senden von HTTP-Anforderungen und empfangen von HTTP-Antworten aus einer Ressource bereit, die durch einen URI identifiziert wird</span><span class="sxs-lookup"><span data-stu-id="cbd0f-122">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI.</span></span> <span data-ttu-id="cbd0f-123">Beachten Sie, dass diese `HttpClient` Instanz von den Browser zum Verarbeiten des HTTP-Datenverkehrs im Hintergrund verwendet.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-123">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="cbd0f-124">[HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) wird automatisch auf das Basis-URI-Präfix der APP festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-124">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="cbd0f-125">Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-125">For more information, see <xref:blazor/call-web-api>.</span></span> |
| `IJSRuntime` | <span data-ttu-id="cbd0f-126">Singleton</span><span class="sxs-lookup"><span data-stu-id="cbd0f-126">Singleton</span></span> | <span data-ttu-id="cbd0f-127">Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-127">Represents an instance of a JavaScript runtime where JavaScript calls are dispatched.</span></span> <span data-ttu-id="cbd0f-128">Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-128">For more information, see <xref:blazor/javascript-interop>.</span></span> |
| `NavigationManager` | <span data-ttu-id="cbd0f-129">Singleton</span><span class="sxs-lookup"><span data-stu-id="cbd0f-129">Singleton</span></span> | <span data-ttu-id="cbd0f-130">Enthält Hilfsprogramme zum Arbeiten mit URIs und dem Navigations Zustand.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-130">Contains helpers for working with URIs and navigation state.</span></span> <span data-ttu-id="cbd0f-131">Weitere Informationen finden Sie unter [URI-und Navigations Zustands Hilfen](xref:blazor/routing#uri-and-navigation-state-helpers).</span><span class="sxs-lookup"><span data-stu-id="cbd0f-131">For more information, see [URI and navigation state helpers](xref:blazor/routing#uri-and-navigation-state-helpers).</span></span> |

<span data-ttu-id="cbd0f-132">Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-132">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="cbd0f-133">Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-133">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="add-services-to-an-app"></a><span data-ttu-id="cbd0f-134">Hinzufügen von Diensten zu einer APP</span><span class="sxs-lookup"><span data-stu-id="cbd0f-134">Add services to an app</span></span>

<span data-ttu-id="cbd0f-135">Überprüfen Sie nach dem Erstellen einer neuen `Startup.ConfigureServices` APP die-Methode:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-135">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="cbd0f-136">An `ConfigureServices` die-Methode wird <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>ein-Objekt übermittelt, das eine Liste von Dienst<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>Deskriptoren-Objekten () ist.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-136">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="cbd0f-137">Dienste werden hinzugefügt, indem Dienst Deskriptoren für die Dienst Sammlung bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-137">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="cbd0f-138">Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess` -Schnittstelle und ihrer `DataAccess`konkreten Implementierung:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-138">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="cbd0f-139">Dienste können mit der in der folgenden Tabelle angezeigten Lebensdauer konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-139">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="cbd0f-140">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="cbd0f-140">Lifetime</span></span> | <span data-ttu-id="cbd0f-141">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="cbd0f-141">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="cbd0f-142">Blazor Webassembly-apps haben zurzeit kein Konzept von di-Bereichen.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-142">Blazor WebAssembly apps don't currently have a concept of DI scopes.</span></span> <span data-ttu-id="cbd0f-143">`Scoped`-registrierte Dienste Verhalten sich `Singleton` wie Dienste.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-143">`Scoped`-registered services behave like `Singleton` services.</span></span> <span data-ttu-id="cbd0f-144">Das serverhostingmodell des blazor- `Scoped` Servers unterstützt jedoch die Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-144">However, the Blazor Server hosting model supports the `Scoped` lifetime.</span></span> <span data-ttu-id="cbd0f-145">In blazor-Server-apps wird eine Bereichs bezogene Dienst Registrierung auf die *Verbindung*beschränkt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-145">In Blazor Server apps, a scoped service registration is scoped to the *connection*.</span></span> <span data-ttu-id="cbd0f-146">Aus diesem Grund wird die Verwendung von Bereichs bezogenen Diensten für Dienste bevorzugt, die auf den aktuellen Benutzer beschränkt werden sollten, auch wenn die aktuelle Absicht ist, die Client seitige Ausführung im Browser auszuführen.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-146">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="cbd0f-147">DI erstellt eine *einzelne Instanz* des Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-147">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="cbd0f-148">Alle Komponenten, die `Singleton` einen Dienst erfordern, erhalten eine Instanz desselben diensdienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-148">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="cbd0f-149">Wenn eine Komponente eine Instanz eines `Transient` Dienstanbieter aus dem Dienst Container abruft, empfängt Sie eine *neue Instanz* des Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-149">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |

<span data-ttu-id="cbd0f-150">Das di-System basiert auf dem System System in ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-150">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="cbd0f-151">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-151">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="cbd0f-152">Anfordern eines Dienstanbieter in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="cbd0f-152">Request a service in a component</span></span>

<span data-ttu-id="cbd0f-153">Nachdem die Dienste der Dienst Auflistung hinzugefügt wurden, fügen Sie die Dienste mithilfe der [ \@](xref:mvc/views/razor#inject) Razor-Direktive einfügen in die Komponenten ein.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-153">After services are added to the service collection, inject the services into the components using the [\@inject](xref:mvc/views/razor#inject) Razor directive.</span></span> <span data-ttu-id="cbd0f-154">`@inject`verfügt über zwei Parameter:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-154">`@inject` has two parameters:</span></span>

* <span data-ttu-id="cbd0f-155">Geben &ndash; Sie den Typ des einzuschleusenden Dienstanbieter ein.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-155">Type &ndash; The type of the service to inject.</span></span>
* <span data-ttu-id="cbd0f-156">Eigenschaft &ndash; der Name der Eigenschaft, die den injizierten App-Dienst empfängt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-156">Property &ndash; The name of the property receiving the injected app service.</span></span> <span data-ttu-id="cbd0f-157">Die-Eigenschaft erfordert keine manuelle Erstellung.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-157">The property doesn't require manual creation.</span></span> <span data-ttu-id="cbd0f-158">Der Compiler erstellt die-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-158">The compiler creates the property.</span></span>

<span data-ttu-id="cbd0f-159">Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-159">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="cbd0f-160">Verwenden Sie `@inject` mehrere-Anweisungen, um unterschiedliche Dienste einzufügen.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-160">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="cbd0f-161">Das folgende Beispiel veranschaulicht die Verwendung von `@inject`.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-161">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="cbd0f-162">Der Dienst, `Services.IDataAccess` der implementiert, wird in die- `DataRepository`Eigenschaft der Komponente eingefügt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-162">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="cbd0f-163">Beachten Sie, dass der Code nur die `IDataAccess` Abstraktion verwendet:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-163">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

<span data-ttu-id="cbd0f-164">Intern wird die generierte Eigenschaft (`DataRepository`) mit dem `InjectAttribute` -Attribut versehen.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-164">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="cbd0f-165">In der Regel wird dieses Attribut nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-165">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="cbd0f-166">Wenn eine Basisklasse für Komponenten erforderlich ist und eingefügte Eigenschaften auch für die Basisklasse erforderlich sind, fügen `InjectAttribute`Sie manuell Folgendes hinzu:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-166">If a base class is required for components and injected properties are also required for the base class, manually add the `InjectAttribute`:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="cbd0f-167">In Komponenten, die von der-Basisklasse `@inject` abgeleitet sind, ist die-Anweisung nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-167">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="cbd0f-168">Der `InjectAttribute` der Basisklasse ist ausreichend:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-168">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a><span data-ttu-id="cbd0f-169">Verwenden von di in Diensten</span><span class="sxs-lookup"><span data-stu-id="cbd0f-169">Use DI in services</span></span>

<span data-ttu-id="cbd0f-170">Für komplexe Dienste sind möglicherweise zusätzliche Dienste erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-170">Complex services might require additional services.</span></span> <span data-ttu-id="cbd0f-171">Im vorherigen Beispiel ist `DataAccess` möglicherweise der `HttpClient` Standard Dienst erforderlich.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-171">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="cbd0f-172">`@inject`(oder) `InjectAttribute`ist für die Verwendung in-Diensten nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-172">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="cbd0f-173">Stattdessen muss die *Konstruktorinjektion* verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-173">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="cbd0f-174">Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-174">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="cbd0f-175">Wenn di den Dienst erstellt, werden die erforderlichen Dienste im Konstruktor erkannt und entsprechend bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-175">When DI creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="cbd0f-176">Voraussetzungen für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="cbd0f-176">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="cbd0f-177">Ein Konstruktor muss vorhanden sein, dessen Argumente von di erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-177">One constructor must exist whose arguments can all be fulfilled by DI.</span></span> <span data-ttu-id="cbd0f-178">Zusätzliche Parameter, die nicht von di abgedeckt werden, sind zulässig, wenn Sie Standardwerte angeben.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-178">Additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="cbd0f-179">Der anwendbare Konstruktor muss *öffentlich*sein.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-179">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="cbd0f-180">Ein anwendbarer Konstruktor muss vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-180">One applicable constructor must exist.</span></span> <span data-ttu-id="cbd0f-181">Bei einer Mehrdeutigkeit löst di eine Ausnahme aus.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-181">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a><span data-ttu-id="cbd0f-182">Basiskomponenten Klassen des-Hilfsprogramms zum Verwalten eines di-Bereichs</span><span class="sxs-lookup"><span data-stu-id="cbd0f-182">Utility base component classes to manage a DI scope</span></span>

<span data-ttu-id="cbd0f-183">In ASP.net Core-apps werden Bereichs bezogene Dienste in der Regel auf die aktuelle Anforderung festgelegt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-183">In ASP.NET Core apps, scoped services are typically scoped to the current request.</span></span> <span data-ttu-id="cbd0f-184">Nachdem die Anforderung abgeschlossen ist, werden alle Bereichs bezogenen oder vorübergehenden Dienste vom System System entfernt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-184">After the request completes, any scoped or transient services are disposed by the DI system.</span></span> <span data-ttu-id="cbd0f-185">In blazor-Server-apps dauert der Anforderungs Bereich für die Dauer der Client Verbindung, was dazu führen kann, dass vorübergehende und Bereichs bezogene Dienste viel länger als erwartet Leben.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-185">In Blazor Server apps, the request scope lasts for the duration of the client connection, which can result in transient and scoped services living much longer than expected.</span></span>

<span data-ttu-id="cbd0f-186">Um Dienste auf die Lebensdauer einer Komponente zu beschränken, kann die `OwningComponentBase` - `OwningComponentBase<TService>` Klasse und die-Basisklasse verwenden.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-186">To scope services to the lifetime of a component, can use the `OwningComponentBase` and `OwningComponentBase<TService>` base classes.</span></span> <span data-ttu-id="cbd0f-187">Diese Basisklassen machen eine `ScopedServices` Eigenschaft vom Typ `IServiceProvider` verfügbar, die Dienste auflöst, die auf die Lebensdauer der Komponente beschränkt sind.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-187">These base classes expose a `ScopedServices` property of type `IServiceProvider` that resolve services that are scoped to the lifetime of the component.</span></span> <span data-ttu-id="cbd0f-188">Verwenden Sie die `@inherits` -Direktive, um eine Komponente zu erstellen, die von einer Basisklasse in Razor erbt.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-188">To author a component that inherits from a base class in Razor, use the `@inherits` directive.</span></span>

```cshtml
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
> <span data-ttu-id="cbd0f-189">Dienste, die mithilfe `@inject` von `InjectAttribute` oder in die Komponente eingefügt werden, werden nicht im Bereich der Komponente erstellt und sind an den Anforderungs Bereich gebunden.</span><span class="sxs-lookup"><span data-stu-id="cbd0f-189">Services injected into the component using `@inject` or the `InjectAttribute` aren't created in the component's scope and are tied to the request scope.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cbd0f-190">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="cbd0f-190">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
