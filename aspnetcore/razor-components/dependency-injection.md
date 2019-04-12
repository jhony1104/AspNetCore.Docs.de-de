---
title: Abhängigkeitsinjektion für Razor-Komponenten
author: guardrex
description: Sehen Sie, wie Blazor und Razor-Komponenten apps Dienste verwenden können, indem sie in Komponenten einfügen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: razor-components/dependency-injection
ms.openlocfilehash: 40aec2e3a5032039c7d921f67d7d333b03c07fb1
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2019
ms.locfileid: "59515489"
---
# <a name="razor-components-dependency-injection"></a><span data-ttu-id="9cced-103">Abhängigkeitsinjektion für Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="9cced-103">Razor Components dependency injection</span></span>

<span data-ttu-id="9cced-104">Durch [Rainer Stropek](https://www.timecockpit.com)</span><span class="sxs-lookup"><span data-stu-id="9cced-104">By [Rainer Stropek](https://www.timecockpit.com)</span></span>

<span data-ttu-id="9cced-105">Razor-Komponenten unterstützt [Abhängigkeitsinjektion (Dependency Injection)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="9cced-105">Razor Components supports [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="9cced-106">Apps können integrierte Dienste verwenden, indem sie in Komponenten einfügen.</span><span class="sxs-lookup"><span data-stu-id="9cced-106">Apps can use built-in services by injecting them into components.</span></span> <span data-ttu-id="9cced-107">Apps können auch definieren und Registrieren von benutzerdefinierten Diensten und in der gesamten app über Dependency Injection zur Verfügung stellen.</span><span class="sxs-lookup"><span data-stu-id="9cced-107">Apps can also define and register custom services and make them available throughout the app via DI.</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="9cced-108">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="9cced-108">Dependency injection</span></span>

<span data-ttu-id="9cced-109">DI ist eine Technik für den Zugriff auf Dienste an einem zentralen Ort konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="9cced-109">DI is a technique for accessing services configured in a central location.</span></span> <span data-ttu-id="9cced-110">Dies kann in Razor-Komponenten apps nützlich sein:</span><span class="sxs-lookup"><span data-stu-id="9cced-110">This can be useful in Razor Components apps to:</span></span>

* <span data-ttu-id="9cced-111">Teilen Sie eine einzelne Instanz einer Dienstklasse in vielen Komponenten, bekannt als eine *Singleton* Service.</span><span class="sxs-lookup"><span data-stu-id="9cced-111">Share a single instance of a service class across many components, known as a *singleton* service.</span></span>
* <span data-ttu-id="9cced-112">Entkoppeln von konkreten Dienstklassen mit Verweis Abstraktionen.</span><span class="sxs-lookup"><span data-stu-id="9cced-112">Decouple components from concrete service classes by using reference abstractions.</span></span> <span data-ttu-id="9cced-113">Betrachten Sie beispielsweise eine Schnittstelle `IDataAccess` für den Zugriff auf Daten in der app.</span><span class="sxs-lookup"><span data-stu-id="9cced-113">For example, consider an interface `IDataAccess` for accessing data in the app.</span></span> <span data-ttu-id="9cced-114">Die Schnittstelle wird implementiert, indem Sie eine konkrete `DataAccess` Klasse und als Dienst im Dienstcontainer der app registriert ist.</span><span class="sxs-lookup"><span data-stu-id="9cced-114">The interface is implemented by a concrete `DataAccess` class and registered as a service in the app's service container.</span></span> <span data-ttu-id="9cced-115">Wenn eine Komponente DI verwendet, zum Empfangen einer `IDataAccess` Implementierung, die Komponente ist nicht mit den konkreten Typ verknüpft.</span><span class="sxs-lookup"><span data-stu-id="9cced-115">When a component uses DI to receive an `IDataAccess` implementation, the component isn't coupled to the concrete type.</span></span> <span data-ttu-id="9cced-116">Die Implementierung kann z. B. um eine pseudoimplementierung in Komponententests ausgetauscht werden.</span><span class="sxs-lookup"><span data-stu-id="9cced-116">The implementation can be swapped, perhaps to a mock implementation in unit tests.</span></span>

<span data-ttu-id="9cced-117">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9cced-117">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="add-services-to-di"></a><span data-ttu-id="9cced-118">Hinzufügen von Diensten zu Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="9cced-118">Add services to DI</span></span>

<span data-ttu-id="9cced-119">Überprüfen Sie nach dem Erstellen einer neuen app, die `Startup.ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="9cced-119">After creating a new app, examine the `Startup.ConfigureServices` method:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

<span data-ttu-id="9cced-120">Die `ConfigureServices` -Methode übergeben eine <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, eine Liste der Dienstobjekte-Deskriptor (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span><span class="sxs-lookup"><span data-stu-id="9cced-120">The `ConfigureServices` method is passed an <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, which is a list of service descriptor objects (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>).</span></span> <span data-ttu-id="9cced-121">Dienste werden hinzugefügt, durch die Bereitstellung von Dienst-Deskriptoren, um die Sammlung von Diensten.</span><span class="sxs-lookup"><span data-stu-id="9cced-121">Services are added by providing service descriptors to the service collection.</span></span> <span data-ttu-id="9cced-122">Das folgende Beispiel zeigt das Konzept, mit der `IDataAccess` -Schnittstelle und die konkrete Implementierung `DataAccess`:</span><span class="sxs-lookup"><span data-stu-id="9cced-122">The following example demonstrates the concept with the `IDataAccess` interface and its concrete implementation `DataAccess`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

<span data-ttu-id="9cced-123">Dienste können mit der Lebensdauer, die in der folgenden Tabelle gezeigt konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="9cced-123">Services can be configured with the lifetimes shown in the following table.</span></span>

| <span data-ttu-id="9cced-124">Lebensdauer</span><span class="sxs-lookup"><span data-stu-id="9cced-124">Lifetime</span></span> | <span data-ttu-id="9cced-125">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9cced-125">Description</span></span> |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | <span data-ttu-id="9cced-126">DI erstellt eine *Einzelinstanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="9cced-126">DI creates a *single instance* of the service.</span></span> <span data-ttu-id="9cced-127">Alle Komponenten, erfordern eine `Singleton` Service erhalten Sie eine Instanz des gleichen Diensts.</span><span class="sxs-lookup"><span data-stu-id="9cced-127">All components requiring a `Singleton` service receive an instance of the same service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | <span data-ttu-id="9cced-128">Eine Komponente erhält jedes Mal, wenn eine Instanz von einer `Transient` Dienst aus dem Dienstcontainer, empfängt er eine *neue Instanz* des Diensts.</span><span class="sxs-lookup"><span data-stu-id="9cced-128">Whenever a component obtains an instance of a `Transient` service from the service container, it receives a *new instance* of the service.</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | <span data-ttu-id="9cced-129">Die clientseitige Blazor keine derzeit das Konzept der Dependency Injection-Bereiche.</span><span class="sxs-lookup"><span data-stu-id="9cced-129">Client-side Blazor doesn't currently have the concept of DI scopes.</span></span> <span data-ttu-id="9cced-130">`Scoped` verhält sich wie `Singleton`.</span><span class="sxs-lookup"><span data-stu-id="9cced-130">`Scoped` behaves like `Singleton`.</span></span> <span data-ttu-id="9cced-131">ASP.NET Core-Razor-Komponenten unterstützen jedoch die `Scoped` Lebensdauer.</span><span class="sxs-lookup"><span data-stu-id="9cced-131">However, ASP.NET Core Razor Components support the `Scoped` lifetime.</span></span> <span data-ttu-id="9cced-132">In einer Razor-Komponente ist eine Bereichsbezogene dienstregistrierung für die Verbindung beschränkt.</span><span class="sxs-lookup"><span data-stu-id="9cced-132">In a Razor Component, a scoped service registration is scoped to the connection.</span></span> <span data-ttu-id="9cced-133">Aus diesem Grund verwenden Bereichsbezogene Dienste wird bevorzugt für Dienste, die den aktuellen Benutzer zugeordnet werden soll, auch wenn die aktuelle Absicht ist, führen Sie die clientseitige im Browser.</span><span class="sxs-lookup"><span data-stu-id="9cced-133">For this reason, using scoped services is preferred for services that should be scoped to the current user, even if the current intent is to run client-side in the browser.</span></span> |

<span data-ttu-id="9cced-134">Das DI-System basiert auf der in ASP.NET Core DI-System.</span><span class="sxs-lookup"><span data-stu-id="9cced-134">The DI system is based on the DI system in ASP.NET Core.</span></span> <span data-ttu-id="9cced-135">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9cced-135">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="default-services"></a><span data-ttu-id="9cced-136">Standarddienste</span><span class="sxs-lookup"><span data-stu-id="9cced-136">Default services</span></span>

<span data-ttu-id="9cced-137">Standarddienste werden automatisch auf der app Service-Auflistung hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="9cced-137">Default services are automatically added to the app's service collection.</span></span>

| <span data-ttu-id="9cced-138">Dienst</span><span class="sxs-lookup"><span data-stu-id="9cced-138">Service</span></span> | <span data-ttu-id="9cced-139">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="9cced-139">Description</span></span> |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | <span data-ttu-id="9cced-140">Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource identifiziert, die durch einen URI (Singleton).</span><span class="sxs-lookup"><span data-stu-id="9cced-140">Provides methods for sending HTTP requests and receiving HTTP responses from a resource identified by a URI (singleton).</span></span> <span data-ttu-id="9cced-141">Beachten Sie, dass diese Instanz von `HttpClient` verwendet der Browser für die Verarbeitung des HTTP-Datenverkehrs im Hintergrund.</span><span class="sxs-lookup"><span data-stu-id="9cced-141">Note that this instance of `HttpClient` uses the browser for handling the HTTP traffic in the background.</span></span> <span data-ttu-id="9cced-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) wird automatisch auf der Basis-URI-Präfix der app festgelegt.</span><span class="sxs-lookup"><span data-stu-id="9cced-142">[HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) is automatically set to the base URI prefix of the app.</span></span> <span data-ttu-id="9cced-143">`HttpClient` wird nur für die clientseitige Blazor apps bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="9cced-143">`HttpClient` is only provided to client-side Blazor apps.</span></span> |
| `IJSRuntime` | <span data-ttu-id="9cced-144">Stellt eine Instanz einer JavaScript-Laufzeit, die an den Aufrufe verteilt werden können.</span><span class="sxs-lookup"><span data-stu-id="9cced-144">Represents an instance of a JavaScript runtime to which calls may be dispatched.</span></span> <span data-ttu-id="9cced-145">Weitere Informationen finden Sie unter <xref:razor-components/javascript-interop>.</span><span class="sxs-lookup"><span data-stu-id="9cced-145">For more information, see <xref:razor-components/javascript-interop>.</span></span> |
| `IUriHelper` | <span data-ttu-id="9cced-146">Enthält Hilfsmethoden für die Arbeit mit URIs und Navigation-Zustand (Singleton).</span><span class="sxs-lookup"><span data-stu-id="9cced-146">Contains helpers for working with URIs and navigation state (singleton).</span></span> <span data-ttu-id="9cced-147">`IUriHelper` sowohl Blazor und Razor-Komponenten-apps bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="9cced-147">`IUriHelper` is provided to both Blazor and Razor Components apps.</span></span> |

<span data-ttu-id="9cced-148">Es ist möglich, einen benutzerdefinierten Anbieter anstelle des Standard-Service-Anbieters hinzugefügt, indem die Standardvorlage zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="9cced-148">It's possible to use a custom service provider instead of the default service provider added by the default template.</span></span> <span data-ttu-id="9cced-149">Ein benutzerdefinierter Dienst-Anbieter bereit nicht automatisch die Standarddienste, die in der Tabelle aufgeführten.</span><span class="sxs-lookup"><span data-stu-id="9cced-149">A custom service provider doesn't automatically provide the default services listed in the table.</span></span> <span data-ttu-id="9cced-150">Wenn Sie benutzerdefinierte Dienstanbieter verwenden und in der Tabelle aufgeführten Dienste erforderlich, fügen Sie die erforderlichen Dienste hinzu, an den neuen Dienstanbieter.</span><span class="sxs-lookup"><span data-stu-id="9cced-150">If you use a custom service provider and require any of the services shown in the table, add the required services to the new service provider.</span></span>

## <a name="request-a-service-in-a-component"></a><span data-ttu-id="9cced-151">Anfordern eines Diensts in einer Komponente</span><span class="sxs-lookup"><span data-stu-id="9cced-151">Request a service in a component</span></span>

<span data-ttu-id="9cced-152">Nach Diensten der Dienstsammlung hinzugefügt werden, fügen Sie die Dienste in der Serverkomponenten Razor-Vorlagen mithilfe der [ @inject ](xref:mvc/views/razor#section-4) Razor-Anweisung.</span><span class="sxs-lookup"><span data-stu-id="9cced-152">After services are added to the service collection, inject the services into the components' Razor templates using the [@inject](xref:mvc/views/razor#section-4) Razor directive.</span></span> <span data-ttu-id="9cced-153">`@inject` hat zwei Parameter:</span><span class="sxs-lookup"><span data-stu-id="9cced-153">`@inject` has two parameters:</span></span>

* <span data-ttu-id="9cced-154">Typname: Der Typ des Diensts eingefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="9cced-154">Type name: The type of the service to inject.</span></span>
* <span data-ttu-id="9cced-155">Name der Eigenschaft: Der Name der Eigenschaft, die den eingefügten app-Dienst empfangen werden soll.</span><span class="sxs-lookup"><span data-stu-id="9cced-155">Property name: The name of the property receiving the injected app service.</span></span> <span data-ttu-id="9cced-156">Beachten Sie, dass die Eigenschaft keine manuelle Erstellung erfordert.</span><span class="sxs-lookup"><span data-stu-id="9cced-156">Note that the property doesn't require manual creation.</span></span> <span data-ttu-id="9cced-157">Der Compiler erstellt die-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="9cced-157">The compiler creates the property.</span></span>

<span data-ttu-id="9cced-158">Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="9cced-158">For more information, see <xref:mvc/views/dependency-injection>.</span></span>

<span data-ttu-id="9cced-159">Verwenden Sie mehrere `@inject` Anweisungen zum Einfügen von verschiedenen Diensten.</span><span class="sxs-lookup"><span data-stu-id="9cced-159">Use multiple `@inject` statements to inject different services.</span></span>

<span data-ttu-id="9cced-160">Das folgende Beispiel veranschaulicht die Verwendung von `@inject`.</span><span class="sxs-lookup"><span data-stu-id="9cced-160">The following example shows how to use `@inject`.</span></span> <span data-ttu-id="9cced-161">Der Dienst implementiert `Services.IDataAccess` wird eingefügt, in der Eigenschaft der Komponente `DataRepository`.</span><span class="sxs-lookup"><span data-stu-id="9cced-161">The service implementing `Services.IDataAccess` is injected into the component's property `DataRepository`.</span></span> <span data-ttu-id="9cced-162">Beachten Sie, wie der Code nur nutzt die `IDataAccess` Abstraktion:</span><span class="sxs-lookup"><span data-stu-id="9cced-162">Note how the code is only using the `IDataAccess` abstraction:</span></span>

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

<span data-ttu-id="9cced-163">Intern wird die generierte Eigenschaft (`DataRepository`) versehen ist, mit der `InjectAttribute` Attribut.</span><span class="sxs-lookup"><span data-stu-id="9cced-163">Internally, the generated property (`DataRepository`) is decorated with the `InjectAttribute` attribute.</span></span> <span data-ttu-id="9cced-164">In der Regel wird dieses Attribut nicht direkt verwendet.</span><span class="sxs-lookup"><span data-stu-id="9cced-164">Typically, this attribute isn't used directly.</span></span> <span data-ttu-id="9cced-165">Wenn eine Basisklasse für Komponenten erforderlich ist, und eingefügte Eigenschaften auch erforderlich, für die Basisklasse sind, `InjectAttribute` manuell hinzugefügt werden können:</span><span class="sxs-lookup"><span data-stu-id="9cced-165">If a base class is required for components and injected properties are also required for the base class, `InjectAttribute` can be manually added:</span></span>

```csharp
public class ComponentBase : IComponent
{
    // Dependency injection works even if using the
    // InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

<span data-ttu-id="9cced-166">In Komponenten, die von der Basisklasse abgeleitete der `@inject` Richtlinie ist nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="9cced-166">In components derived from the base class, the `@inject` directive isn't required.</span></span> <span data-ttu-id="9cced-167">Die `InjectAttribute` der Basisklasse ist ausreichend:</span><span class="sxs-lookup"><span data-stu-id="9cced-167">The `InjectAttribute` of the base class is sufficient:</span></span>

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a><span data-ttu-id="9cced-168">Abhängigkeitsinjektion in Diensten</span><span class="sxs-lookup"><span data-stu-id="9cced-168">Dependency injection in services</span></span>

<span data-ttu-id="9cced-169">Komplexen Dienste müssen möglicherweise zusätzliche Dienste.</span><span class="sxs-lookup"><span data-stu-id="9cced-169">Complex services might require additional services.</span></span> <span data-ttu-id="9cced-170">Im vorherigen Beispiel `DataAccess` möglicherweise die `HttpClient` Standarddienst.</span><span class="sxs-lookup"><span data-stu-id="9cced-170">In the prior example, `DataAccess` might require the `HttpClient` default service.</span></span> <span data-ttu-id="9cced-171">`@inject` (oder die `InjectAttribute`) ist nicht verfügbar für die Verwendung in Diensten.</span><span class="sxs-lookup"><span data-stu-id="9cced-171">`@inject` (or the `InjectAttribute`) isn't available for use in services.</span></span> <span data-ttu-id="9cced-172">*Konstruktorinjektion* muss stattdessen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="9cced-172">*Constructor injection* must be used instead.</span></span> <span data-ttu-id="9cced-173">Die erforderlichen Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="9cced-173">Required services are added by adding parameters to the service's constructor.</span></span> <span data-ttu-id="9cced-174">Abhängigkeitsinjektion auf den Dienst erstellt, erkennt es die Dienste, die im Konstruktor erfordert und stellt diese entsprechend an.</span><span class="sxs-lookup"><span data-stu-id="9cced-174">When dependency injection creates the service, it recognizes the services it requires in the constructor and provides them accordingly.</span></span>

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

<span data-ttu-id="9cced-175">Voraussetzungen für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="9cced-175">Prerequisites for constructor injection:</span></span>

* <span data-ttu-id="9cced-176">Es muss einen Konstruktor, deren Argumente alle durch Dependency Injection erfüllt werden können.</span><span class="sxs-lookup"><span data-stu-id="9cced-176">There must be one constructor whose arguments can all be fulfilled by dependency injection.</span></span> <span data-ttu-id="9cced-177">Beachten Sie, dass zusätzliche Parameter, die nicht durch Dependency Injection abgedeckt zulässig sind, wenn sie Standardwerte angeben.</span><span class="sxs-lookup"><span data-stu-id="9cced-177">Note that additional parameters not covered by DI are allowed if they specify default values.</span></span>
* <span data-ttu-id="9cced-178">Anwendbarer Konstruktor muss *öffentliche*.</span><span class="sxs-lookup"><span data-stu-id="9cced-178">The applicable constructor must be *public*.</span></span>
* <span data-ttu-id="9cced-179">Es muss nur ein anwendbarer Konstruktor vorhanden sein.</span><span class="sxs-lookup"><span data-stu-id="9cced-179">There must only be one applicable constructor.</span></span> <span data-ttu-id="9cced-180">Im Falle einer Mehrdeutigkeit werden von DI eine Ausnahme ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="9cced-180">In case of an ambiguity, DI throws an exception.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9cced-181">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="9cced-181">Additional resources</span></span>

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
