---
title: Dependency Injection in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie ASP.NET Core Dependency Injection implementiert und wie Sie dieses Muster verwenden können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: fundamentals/dependency-injection
ms.openlocfilehash: da6ddf1f0efd164a58f017ff55ce216bbefa7cc6
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068322"
---
# <a name="dependency-injection-in-aspnet-core"></a>Dependency Injection in ASP.NET Core

Von [Steve Smith](https://ardalis.com/), [Scott Addie](https://scottaddie.com), und [Luke Latham](https://github.com/guardrex)

ASP.NET Core unterstützt das Softwareentwurfsmuster Abhängigkeitsinjektion. Damit kann eine [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) (Inversion of Control, IoC) zwischen Klassen und ihren Abhängigkeiten erreicht werden.

Weitere Informationen zur Abhängigkeitsinjektion innerhalb von MVC-Controllern finden Sie unter <xref:mvc/controllers/dependency-injection>.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="overview-of-dependency-injection"></a>Übersicht über Abhängigkeitsinjektion

Eine *Abhängigkeit* ist ein beliebiges Objekt, das ein anderes Objekt benötigt. Überprüfen Sie die folgende `MyDependency`-Klasse mit einer `WriteMessage`-Methode, von der andere Klassen in einer App abhängen:

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

Eine Instanz der Klasse `MyDependency` kann erstellt werden, um die `WriteMessage`-Methode einer Klasse zur Verfügung zu stellen. Die Klasse `MyDependency` ist eine Abhängigkeit der Klasse `IndexModel`:

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

Die Klasse erstellt die Instanz `MyDependency` und hängt direkt von dieser ab. Codeabhängigkeiten (wie im vorherigen Beispiel) sind problematisch und sollten aus folgenden Gründen vermieden werden:

* Um `MyDependency` durch eine andere Implementierung zu ersetzen, muss die Klasse geändert werden.
* Wenn `MyDependency` über Abhängigkeiten verfügt, müssen diese von der Klasse konfiguriert werden. In einem großen Projekt mit mehreren Klassen, die von `MyDependency` abhängig sind, wird der Konfigurationscode über die App verteilt.
* Diese Implementierung ist nicht für Komponententests geeignet. Die App sollte eine `MyDependency`-Modell- oder Stubklasse verwenden, was mit diesem Ansatz nicht möglich ist.

Die Abhängigkeitsinjektion löst dieses Problem mithilfe der folgenden Schritte:

* Die Verwendung einer Schnittstelle zur Abstraktion der Abhängigkeitsimplementierung.
* Registrierung der Abhängigkeit in einem Dienstcontainer. ASP.NET Core stellt einen integrierten Dienstcontainer ([IServiceProvider](/dotnet/api/system.iserviceprovider)) bereit. Die Dienste werden in der App-Methode `Startup.ConfigureServices` registriert.
* Die *Injektion* des Diensts in den Konstruktor der Klasse, wo er verwendet wird. Das Framework erstellt eine Instanz der Abhängigkeit und entfernt diese, wenn sie nicht mehr benötigt wird.

In der [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/dependency-injection/samples) definiert die `IMyDependency`-Schnittstelle eine Methode, die der Dienst für die App bereitstellt:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IMyDependency.cs?name=snippet1)]

Diese Schnittstelle wird durch einen konkreten Typ (`MyDependency`) implementiert:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/MyDependency.cs?name=snippet1)]

`MyDependency` fordert [ILogger&lt;TCategoryName&gt;](/dotnet/api/microsoft.extensions.logging.ilogger-1) im Konstruktor an. Die Abhängigkeitsinjektion wird häufig als Verkettung verwendet. Jede angeforderte Abhängigkeit fordert wiederum ihre eigenen Abhängigkeiten an. Der Container löst die Abhängigkeiten im Diagramm auf und gibt den vollständig aufgelösten Dienst zurück. Die gesammelten aufzulösenden Abhängigkeiten werden als *Abhängigkeitsstruktur*, *Abhängigkeitsdiagramm* oder *Objektdiagramm* bezeichnet.

`IMyDependency` und `ILogger<TCategoryName>` müssen im Dienstcontainer registriert werden. `IMyDependency` ist in `Startup.ConfigureServices` registriert. `ILogger<TCategoryName>` wird von der Protokollierungsabstraktionsinfrastruktur registriert. Es handelt sich also um einen von einem [Framework bereitgestellten Dienst](#framework-provided-services), der standardmäßig vom Framework registriert wird.

Der Container löst `ILogger<TCategoryName>` unter Verwendung der [(generischen) offenen Typen](/dotnet/csharp/language-reference/language-specification/types#open-and-closed-types) auf, wodurch die Notwendigkeit entfällt, jeden [(generischen) konstruierten Typ](/dotnet/csharp/language-reference/language-specification/types#constructed-types) zu registrieren:

```csharp
services.AddSingleton(typeof(ILogger<T>), typeof(Logger<T>));
```

In der Beispiel-App ist der Dienst `IMyDependency` mit dem konkreten Typ `MyDependency` registriert. Die Registrierung schränkt die Lebensdauer des Diensts auf die Lebensdauer einer einzelnen Anforderung ein. Auf die [Dienstlebensdauer](#service-lifetimes) wird später in diesem Artikel eingegangen.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=5)]

> [!NOTE]
> Jede Erweiterungsmethode vom Typ `services.Add{SERVICE_NAME}` fügt Dienste hinzu (und konfiguriert diese möglicherweise). So fügt beispielsweise `services.AddMvc()` die erforderlichen Dienste Razor Pages und MVC hinzu. Es wird empfohlen, dass Apps dieser Konvention folgen. Platzieren Sie Erweiterungsmethoden im Namespace [Microsoft.Extensions.DependencyInjection](/dotnet/api/microsoft.extensions.dependencyinjection), um Gruppen von Dienstregistrierungen zu kapseln.

Wenn für den Dienstkonstruktor ein [integrierter Typ](/dotnet/csharp/language-reference/keywords/built-in-types-table) erforderlich ist, z. B. `string`, kann dieser Typ über [Konfiguration](xref:fundamentals/configuration/index) oder das [Optionsmuster](xref:fundamentals/configuration/options) eingefügt werden:

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

Eine Instanz des Diensts wird über den Konstruktor einer Klasse angefordert, in der der Dienst verwendet wird, und einem privaten Feld zugewiesen. Das Feld wird verwendet, um auf den Dienst bei Bedarf über die gesamte Klasse zuzugreifen.

In der Beispiel-App wird die Instanz `IMyDependency` angefordert, und mit ihr wird die App-Methode `WriteMessage` aufgerufen:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3,6,13,29-30)]

## <a name="framework-provided-services"></a>Von Frameworks bereitgestellte Dienste

Die Methode `Startup.ConfigureServices` definiert die von der App verwendeten Dienste. Dies beinhaltet Plattformfunktionen wie Entity Framework Core und ASP.NET Core MVC. Zunächst enthält die in `ConfigureServices` bereitgestellte `IServiceCollection` folgende definierten Dienste (abhängig von der [Vorgehensweise bei der Konfiguration des Hosts](xref:fundamentals/index#host)):

| Diensttyp | Lebensdauer |
| ------------ | -------- |
| [Microsoft.AspNetCore.Hosting.Builder.IApplicationBuilderFactory](/dotnet/api/microsoft.aspnetcore.hosting.builder.iapplicationbuilderfactory) | Transient (vorübergehend) |
| [Microsoft.AspNetCore.Hosting.IApplicationLifetime](/dotnet/api/microsoft.aspnetcore.hosting.iapplicationlifetime) | Singleton |
| [Microsoft.AspNetCore.Hosting.IHostingEnvironment](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartup](/dotnet/api/microsoft.aspnetcore.hosting.istartup) | Singleton |
| [Microsoft.AspNetCore.Hosting.IStartupFilter](/dotnet/api/microsoft.aspnetcore.hosting.istartupfilter) | Transient (vorübergehend) |
| [Microsoft.AspNetCore.Hosting.Server.IServer](/dotnet/api/microsoft.aspnetcore.hosting.server.iserver) | Singleton |
| [Microsoft.AspNetCore.Http.IHttpContextFactory](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextfactory) | Transient (vorübergehend) |
| [Microsoft.Extensions.Logging.ILogger&lt;T&gt;](/dotnet/api/microsoft.extensions.logging.ilogger) | Singleton |
| [Microsoft.Extensions.Logging.ILoggerFactory](/dotnet/api/microsoft.extensions.logging.iloggerfactory) | Singleton |
| [Microsoft.Extensions.ObjectPool.ObjectPoolProvider](/dotnet/api/microsoft.extensions.objectpool.objectpoolprovider) | Singleton |
| [Microsoft.Extensions.Options.IConfigureOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.iconfigureoptions-1) | Transient (vorübergehend) |
| [Microsoft.Extensions.Options.IOptions&lt;T&gt;](/dotnet/api/microsoft.extensions.options.ioptions-1) | Singleton |
| [System.Diagnostics.DiagnosticSource](/dotnet/core/api/system.diagnostics.diagnosticsource) | Singleton |
| [System.Diagnostics.DiagnosticListener](/dotnet/core/api/system.diagnostics.diagnosticlistener) | Singleton |

Wenn eine Dienstsammlungs-Erweiterungsmethode verfügbar ist, um einen Dienst (und ggf. seine abhängigen Dienste) zu registrieren, ist die Konvention, eine einzige `Add{SERVICE_NAME}`-Erweiterungsmethode zu verwenden, um alle von diesem Dienst benötigten Dienste zu registrieren. Der folgende Code ist ein Beispiel für das Hinzufügen zusätzlicher Dienste zum Container mit den Erweiterungsmethoden [AddDbContext](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext), [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity) und [AddMvc](/dotnet/api/microsoft.extensions.dependencyinjection.mvcservicecollectionextensions.addmvc):

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

Weitere Informationen finden Sie im Artikel [ServiceCollection-Klasse](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollection) in der API-Dokumentation.

## <a name="service-lifetimes"></a>Dienstlebensdauer

Wählen Sie eine geeignete Lebensdauer für jeden registrierten Dienst aus. ASP.NET Core-Dienste können mit folgender Lebensdauer konfiguriert werden:

**Transient (vorübergehend)**

Kurzlebige Dienste werden bei jeder Anforderung aus dem Dienstcontainer neu erstellt. Diese Lebensdauer ist am besten für einfache, zustandslose Dienste geeignet.

**Bereichsbezogen**

Dienste mit bereichsbezogener Lebensdauer werden einmal pro Clientanforderung (-verbindung) erstellt.

> [!WARNING]
> Wenn Sie einen bereichsbezogenen Dienst in einer Middleware verwenden, müssen Sie den Dienst in die `Invoke`- oder `InvokeAsync`-Methode einfügen. Fügen Sie ihn nicht über Constructor Injection ein, da hierdurch der Dienst ein Verhalten wie ein Singleton zeigt. Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.

**Singleton**

Dienste mit einer Singletonlebensdauer werden bei der ersten Anforderung erstellt (bzw. wenn `ConfigureServices` ausgeführt wird, und eine Instanz bei der Dienstregistrierung angegeben wird). Jeder nachfolgenden Anforderung verwendet die gleiche Instanz. Wenn die App ein Verhalten vom Typ „Singleton“ erfordert, wird empfohlen, den Dienstcontainer die Dienstlebensdauer verwalten zu lassen. Implementieren Sie nicht das Designmuster „Singleton“ und stellen Sie Benutzercode bereit, um die Objektlebensdauer in der Klasse zu verwalten.

> [!WARNING]
> Es ist riskant, einen bereichsbezogenen Dienst über ein Singleton aufzulösen. Möglicherweise weist der Dienst bei der Verarbeitung nachfolgender Anforderungen einen falschen Status auf.

### <a name="constructor-injection-behavior"></a>Verhalten von Constructor Injection

Dienste können durch zwei Mechanismen aufgelöst werden:

* `IServiceProvider`
* [ActivatorUtilities](/dotnet/api/microsoft.extensions.dependencyinjection.activatorutilities) &ndash; lässt die Erstellung von Objekten ohne Dienstregistrierung im Abhängigkeitsinjektionscontainer zu. `ActivatorUtilities` wird mit Abstraktionen für Benutzer verwendet. Dazu zählen Taghilfsprogramme, MVC-Controller und Modellbindungen.

Konstruktoren können Argumente akzeptieren, die nicht durch Abhängigkeitsinjektion bereitgestellt werden. Die Argumente müssen jedoch Standardwerte zuweisen.

Wenn Dienste durch `IServiceProvider` oder `ActivatorUtilities` aufgelöst werden, benötigt Constructor Injection einen *öffentlichen* Konstruktor.

Wenn Dienste durch `ActivatorUtilities` aufgelöst werden, erfordert Constructor Injection, dass nur ein anwendbarer Konstruktor vorhanden ist. Konstruktorüberladungen werden unterstützt. Es darf jedoch nur eine Überladung vorhanden sein, deren Argumente alle durch Dependency Injection erfüllt werden können.

## <a name="entity-framework-contexts"></a>Entity Framework-Kontexte

Entity Framework-Kontexte werden einem Dienstcontainer normalerweise mithilfe der [bereichsbezogenen Lebensdauer](#service-lifetimes) hinzugefügt, da Datenbankvorgänge von Web-Apps normalerweise auf den Clientanforderungsbereich bezogen werden. Der Bereich einer Standardlebensdauer wird festgelegt, wenn eine Lebensdauer nicht von einer <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>-Überladung angegeben wird, wenn der Datenbankkontext registriert wird. Dienste einer festgelegten Lebensdauer sollten keinen Datenbankkontext mit einer kürzeren Lebensdauer als die des Dienstes verwenden.

## <a name="lifetime-and-registration-options"></a>Lebensdauer und Registrierungsoptionen

Um den Unterschied zwischen der Lebensdauer und den Registrierungsoptionen zu demonstrieren, betrachten Sie die folgenden Schnittstellen, die Aufgaben als Vorgänge mit einem eindeutigen Bezeichner (`OperationId`) darstellen. Je nachdem, wie die Dienstlebensdauer für die folgenden Schnittstellen konfiguriert ist, stellt der Container auf Anforderung einer Klasse entweder die gleiche oder eine andere Instanz des Diensts zur Verfügung:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Interfaces/IOperation.cs?name=snippet1)]

Die Schnittstellen sind in die Klasse `Operation` implementiert. Der `Operation`-Konstruktor generiert eine GUID, wenn noch keine angegeben ist:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Models/Operation.cs?name=snippet1)]

`OperationService` ist registriert und hängt von jedem anderen `Operation`-Typ ab. Wenn `OperationService` über die Abhängigkeitsinjektion angefordert wird, wird entweder eine neue Instanz jedes Diensts oder eine vorhandene Instanz basierend auf der Lebensdauer des abhängigen Diensts zurückgegeben.

* Wenn vorübergehende Dienste bei der Anforderung aus dem Container erstellt werden, ist die `OperationId` von Dienst `IOperationTransient` anders als die `OperationId` von `OperationService`. `OperationService` erhält eine neue Instanz der `IOperationTransient`-Klasse. Der `OperationId`-Wert der neuen Instanz ist anders.
* Wenn bereichsbezogene Dienste pro Clientanforderung erstellt werden, ist die `OperationId` in Dienst `IOperationScoped` und `OperationService` identisch innerhalb einer Clientanforderung. Clientanforderungsübergreifend haben die beiden Dienste jedoch einen anderen gemeinsamen `OperationId`-Wert.
* Wenn Singleton- und Singletoninstanzdienste einmal erstellt und für alle Clientanforderungen und alle Dienste verwendet werden, ist `OperationId` für alle Dienstanforderungen identisch.

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Services/OperationService.cs?name=snippet1)]

In `Startup.ConfigureServices` wird jeder Typ entsprechend seiner benannten Lebensdauer dem Container hinzugefügt:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Startup.cs?name=snippet1&highlight=6-9,12)]

Der Dienst `IOperationSingletonInstance` verwendet eine bestimmte Instanz mit einer bekannten ID von `Guid.Empty`. Die Verwendung dieses Typs ist eindeutig (die GUID besteht ausschließlich aus Nullen (0)).

Die Beispiel-App zeigt die Objektlebensdauer innerhalb einer Anforderung und zwischen einzelnen Anforderungen an. Ihre Klasse `IndexModel` fordert jeden `IOperation`- und `OperationService`-Typ an. Die Seite zeigt dann alle `OperationId`-Werte der Seitenmodellklasse und des Diensts über Eigenschaftenzuweisungen an:

[!code-csharp[](dependency-injection/samples/2.x/DependencyInjectionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=7-11,14-18,21-25)]

Zwei folgende Ausgaben zeigen die Ergebnisse von zwei Anforderungen:

**Erste Anforderung:**

Controllervorgänge:

Vorübergehend: d233e165-f417-469b-a866-1cf1935d2518  
Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

`OperationService` Vorgänge:

Vorübergehend: c6b049eb-1318-4e31-90f1-eb2dd849ff64  
Bereichsbezogen: 5d997e2d-55f5-4a64-8388-51c4e3a1ad19  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

**Zweite Anforderung:**

Controllervorgänge:

Vorübergehend: b63bd538-0a37-4ff1-90ba-081c5138dda0  
Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

`OperationService` Vorgänge:

Vorübergehend: c4cbacb8-36a2-436d-81c8-8c1b78808aaf  
Bereichsbezogen: 31e820c5-4834-4d22-83fc-a60118acb9f4  
Singleton: 01271bc1-9e31-48e7-8f7c-7261b040ded9  
Instanz: 00000000-0000-0000-0000-000000000000

Beachten Sie, welche der `OperationId`-Werte innerhalb einer Anforderung und zwischen Anforderungen variieren:

* Objekte vom Typ *Vorübergehend* sind immer unterschiedlich. Der vorübergehende `OperationId`-Wert für die ersten und zweiten Clientanforderungen ist für beide `OperationService`-Vorgänge und clientanforderungsübergreifend unterschiedlich. Eine neue Instanz wird für jede Dienstanforderung und jede Clientanforderung bereitgestellt.
* *Bereichsbezogene* Objekte sind innerhalb einer Clientanforderung identisch, clientanforderungsübergreifend sind sie jedoch unterschiedlich.
* Objekte vom Typ *Singleton* sind bei jedem Objekt und in jeder Anforderung identisch (unabhängig davon, ob eine `Operation`-Instanz in `ConfigureServices` bereitgestellt wird).

## <a name="call-services-from-main"></a>Abrufen von Diensten aus „Main“

Erstellen Sie [IServiceScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescope) mit [IServiceScopeFactory.CreateScope](/dotnet/api/microsoft.extensions.dependencyinjection.iservicescopefactory.createscope), um einen bereichsbezogenen Dienst innerhalb des Anwendungsbereichs aufzulösen. Dieser Ansatz eignet sich gut dafür, beim Start auf einen bereichsbezogenen Dienst zuzugreifen und Initialisierungsaufgaben auszuführen. Das folgende Beispiel veranschaulicht, wie man einen Kontext für `MyScopedService` in `Program.Main` erhält:

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

## <a name="scope-validation"></a>Bereichsvalidierung

Wenn die App in der Entwicklungsumgebung ausgeführt wird, überprüft der Standarddienstanbieter, ob:

* Bereichsbezogene Dienste nicht direkt oder indirekt vom Stammdienstanbieter aufgelöst werden
* Bereichsbezogene Dienste nicht direkt oder indirekt in Singletons eingefügt werden

Der Stammdienstanbieter wird erstellt, wenn [BuildServiceProvider](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectioncontainerbuilderextensions.buildserviceprovider) aufgerufen wird. Die Lebensdauer des Stammdienstanbieters entspricht der Lebensdauer der App/des Servers, wenn der Anbieter mit der App erstellt wird und verworfen wird, wenn die App beendet wird.

Bereichsbezogene Dienste werden von dem Container verworfen, der sie erstellt hat. Wenn ein bereichsbezogener Dienst im Stammcontainer erstellt wird, wird die Lebensdauer effektiv auf Singleton heraufgestuft, da er nur vom Stammcontainer verworfen wird, wenn die App/der Server heruntergefahren wird. Die Überprüfung bereichsbezogener Dienste erfasst diese Situationen, wenn `BuildServiceProvider` aufgerufen wird.

Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#scope-validation>.

## <a name="request-services"></a>Anfordern von Diensten

Die Dienste, die innerhalb einer ASP.NET Core-Anforderung von `HttpContext` verfügbar sind, werden über die [HttpContext.RequestService](/dotnet/api/microsoft.aspnetcore.http.httpcontext.requestservices)-Sammlung verfügbar gemacht.

Anforderungsdienste stellen die Dienste dar, die als Teil der App konfiguriert und angefordert werden. Wenn Objekte Abhängigkeiten angeben, werden diese von den in `RequestServices` gefundenen Typen erfüllt (nicht in `ApplicationServices`).

Generell sollte die App diese Eigenschaften nicht direkt verwenden. Fordern Sie stattdessen die von Klassen benötigten Typen über Klassenkonstruktoren an, und lassen Sie das Framework die Abhängigkeiten einfügen. So erhalten Sie Klassen, die einfacher getestet werden können.

> [!NOTE]
> Fordern Sie für den Zugriff auf die `RequestServices`-Sammlung Abhängigkeiten lieber als Konstruktorparameter an.

## <a name="design-services-for-dependency-injection"></a>Entwerfen von Diensten für die Abhängigkeitsinjektion

Best Practices:

* Entwerfen Sie Dienste zur Verwendung der Abhängigkeitsinjektion, um ihre Abhängigkeiten zu erhalten.
* Vermeiden Sie zustandsbehaftete, statische Methodenaufrufe.
* Vermeiden Sie die direkte Instanziierung abhängiger Klassen innerhalb von Diensten. Die direkte Instanziierung koppelt den Code an eine bestimmte Implementierung.
* Erstellen Sie kleine, gut gestaltete und einfach zu testende Apps.

Wenn eine Klasse zu viele eingefügte Abhängigkeiten zu haben scheint, ist dies im Allgemeinen ein Zeichen dafür, dass die Klasse zu viele Aufgaben hat und gegen das [Single-Responsibility-Prinzip (SRP)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#single-responsibility) (Prinzip der eindeutigen Verantwortlichkeit) verstößt. Versuchen Sie, die Klasse umzugestalten, indem Sie einige ihrer Aufgaben in eine neue Klasse verschieben. Beachten Sie, dass der Fokus der Razor Pages-Seitenmodellklassen und MVC-Controllerklassen auf der Benutzeroberfläche liegt. Geschäftsregeln und Implementierungsdetails für den Datenzugriff sollten in Klassen aufbewahrt werden gemäß dem Prinzip [Separation of Concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) (Trennung der Zuständigkeiten).

### <a name="disposal-of-services"></a>Löschen von Diensten

Der Container ruft `Dispose` für die erstellten `IDisposable`-Typen auf. Wenn eine Instanz dem Container per Benutzercode hinzugefügt wird, wird sie nicht automatisch gelöscht.

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

## <a name="default-service-container-replacement"></a>Ersetzen von Standarddienstcontainern

Der integrierte Dienstcontainer dient dazu, die Anforderungen des Frameworks und der meisten Consumer-Apps zu erfüllen. Die Verwendung der integrierten Container wird empfohlen, es sei denn, Sie benötigen ein bestimmtes Feature, das nicht unterstützt wird. Im Folgenden werden einige der Features aufgeführt, die von Containern von Drittanbietern unterstützt werden, aber nicht im integrierten Container enthalten sind:

* Eigenschaftsinjektion
* Auf Namen basierende Injektion
* Untergeordnete Container
* Benutzerdefinierte Verwaltung der Lebensdauer
* `Func<T>` -Unterstützung für die verzögerte Initialisierung

Eine Liste einiger Container, die Adapter unterstützen, finden Sie in der [Datei „readme.md“ zur Dependency Injection](https://github.com/aspnet/Extensions/tree/master/src/DependencyInjection).

Im folgenden Beispiel wird der integrierte Container durch [Autofac](https://autofac.org/) ersetzt:

* Installieren Sie das entsprechende Containerpaket bzw. die entsprechenden Containerpakete:

  * [Autofac](https://www.nuget.org/packages/Autofac/)
  * [Autofac.Extensions.DependencyInjection](https://www.nuget.org/packages/Autofac.Extensions.DependencyInjection/)

* Konfigurieren Sie den Container in `Startup.ConfigureServices`, und geben Sie einen `IServiceProvider`-Wert zurück:

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

    Um einen Drittanbietercontainer zu verwenden, muss `Startup.ConfigureServices` `IServiceProvider` zurückgeben.

* Konfigurieren Sie Autofac in `DefaultModule`:

    ```csharp
    public class DefaultModule : Module
    {
        protected override void Load(ContainerBuilder builder)
        {
            builder.RegisterType<CharacterRepository>().As<ICharacterRepository>();
        }
    }
    ```

Zur Laufzeit wird Autofac verwendet, um Typen aufzulösen und Abhängigkeiten einzufügen. Weitere Informationen zur Verwendung von Autofac mit ASP.NET Core finden Sie in der [Autofac-Dokumentation](https://docs.autofac.org/en/latest/integration/aspnetcore.html).

### <a name="thread-safety"></a>Threadsicherheit

Erstellen Sie threadsichere Singleton-Dienste. Wenn ein Singleton-Dienst eine Abhängigkeit von einem vorübergehenden Dienst aufweist, muss der vorübergehende Dienst abhängig von der Verwendungsweise durch das Singleton ebenfalls threadsicher sein.

Die Factorymethode des einzelnen Diensts, z. B. das zweite Argument für [AddSingleton&lt;TService&gt;(IServiceCollection, Func&lt;IServiceProvider,TService&gt;)](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions.addsingleton#Microsoft_Extensions_DependencyInjection_ServiceCollectionServiceExtensions_AddSingleton__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Func_System_IServiceProvider___0__), muss nicht threadsicher sein. Wie bei `static`-Konstruktoren erfolgt der Aufruf einmalig über einen einzelnen Thread.

## <a name="recommendations"></a>Empfehlungen

* `async/await` - und `Task`-basierte Dienstauflösung wird nicht unterstützt. C# unterstützt keine asynchronen Konstruktoren. Daher wird empfohlen, asynchrone Methoden zu verwenden, nachdem der Dienst synchron aufgelöst wurde.

* Vermeiden Sie das Speichern von Daten und die direkte Konfiguration im Dienstcontainer. Der Einkaufswagen eines Benutzers sollte z. B. normalerweise nicht dem Dienstcontainer hinzugefügt werden. Bei der Konfiguration sollte das [Optionsmuster](xref:fundamentals/configuration/options) verwendet werden. Gleichermaßen sollten Sie „Datencontainer“-Objekte vermeiden, die nur vorhanden sind, um den Zugriff auf einige andere Objekte zuzulassen. Das tatsächlich benötige Element sollte besser über Dependency Injection angefordert werden.

* Vermeiden Sie den statischen Zugriff auf Dienste (z. B. statische Eingabe von [IApplicationBuilder.ApplicationServices](/dotnet/api/microsoft.aspnetcore.builder.iapplicationbuilder.applicationservices) zur Verwendung an anderer Stelle).

* Vermeiden Sie die Verwendung von *Dienstlocator-Mustern*. Rufen Sie beispielsweise nicht <xref:System.IServiceProvider.GetService*> auf, um eine Dienstinstanz zu erhalten, wenn Sie stattdessen Dependency Injection verwenden können:

  **Falsch:**

  ```csharp
  public void MyMethod()
  {
      var options = 
          _services.GetService<IOptionsMonitor<MyOptions>>();
      var option = options.CurrentValue.Option;

      ...
  }
  ```

  **Richtig**:

  ```csharp
  private readonly MyOptions _options;

  public MyClass(IOptionsMonitor<MyOptions> options)
  {
      _options = options.CurrentValue;
  }

  public void MyMethod()
  {
      var option = _options.Option;

      ...
  }
  ```

* Eine andere Dienstlocator-Variante, die Sie vermeiden sollten, ist die Injektion einer Factory, die zur Laufzeit Abhängigkeiten auflöst. Beide Vorgehensweisen kombinieren Strategien zur [Umkehrung der Steuerung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion).

* Vermeiden Sie den statischen Zugriff auf `HttpContext` (z. B. [IHttpContextAccessor.HttpContext](/dotnet/api/microsoft.aspnetcore.http.ihttpcontextaccessor.httpcontext)).

Wie bei allen Empfehlungen treffen Sie möglicherweise auf Situationen, in denen eine Empfehlung ignoriert werden muss. Es gibt nur wenige Ausnahmen &mdash; die sich meistens auf besondere Fälle innerhalb des Frameworks beziehen.

Dependency Injection stellt eine *Alternative* zu statischen bzw. globalen Objektzugriffsmustern dar. Sie werden keinen Nutzen aus der Dependency Injection ziehen können, wenn Sie diese mit dem Zugriff auf statische Objekte kombinieren.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:mvc/views/dependency-injection>
* <xref:mvc/controllers/dependency-injection>
* <xref:security/authorization/dependencyinjection>
* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/extensibility>
* [Schreiben von sauberem Code in ASP.NET Core über Dependency Injection (MSDN)](https://msdn.microsoft.com/magazine/mt703433.aspx)
* [Prinzip der expliziten Abhängigkeiten](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)
* [Umkehrung von Steuerungscontainern und das Abhängigkeitsinjektionsmuster (Martin Fowler) (in englischer Sprache)](https://www.martinfowler.com/articles/injection.html)
* [Registrieren eines Diensts mit mehreren Schnittstellen in ASP.NET Core DI](https://andrewlock.net/how-to-register-a-service-with-multiple-interfaces-for-in-asp-net-core-di/)
