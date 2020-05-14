---
title: Abhängigkeitsinjektion in ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Blazor-Apps Dienste in Komponenten einfügen können.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: e96698bd0bd8f3f3b290ba24bc8169efb16f1d03
ms.sourcegitcommit: 84b46594f57608f6ac4f0570172c7051df507520
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82967531"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>Abhängigkeitsinjektion in ASP.NET Core Blazor

Von [Rainer Stropek](https://www.timecockpit.com) und [Mike Rousos](https://github.com/mjrousos)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor unterstützt die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection). Apps können integrierte Dienste verwenden, indem Sie sie in Komponenten einfügen. Apps können auch benutzerdefinierte Dienste definieren und registrieren und sie über die Abhängigkeitsinjektion in der gesamten App verfügbar machen.

Die Abhängigkeitsinjektion ist ein Verfahren für den Zugriff auf Dienste, die an einer zentralen Stelle konfiguriert sind. Dies kann in Blazor-Apps für Folgendes nützlich sein:

* Freigabe einer einzelnen Instanz einer Dienstklasse über viele Komponenten hinweg, bekannt als *Singleton*-Dienst.
* Entkoppeln von Komponenten von konkreten Dienstklassen durch Verwendung von Referenzabstraktionen. Betrachten Sie zum Beispiel eine `IDataAccess`-Schnittstelle für den Zugriff auf Daten in der App. Die Schnittstelle wird durch eine konkrete `DataAccess`-Klasse implementiert und als Dienst im Dienstcontainer der App registriert. Wenn eine Komponente die Abhängigkeitsinjektion verwendet, um eine `IDataAccess`-Implementierung zu erhalten, ist die Komponente nicht an den konkreten Typ gekoppelt. Die Implementierung kann ausgetauscht werden, z. B. gegen eine Pseudoimplementierung in Komponententests.

## <a name="default-services"></a>Standarddienste

Standarddienste werden automatisch zur Dienstsammlung der App hinzugefügt.

| Dienst | Lebensdauer | Beschreibung |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Transient (vorübergehend) | Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource bereit, die von einem URI identifiziert wird.<br><br>Die Instanz von `HttpClient` in einer Blazor WebAssembly-App verwendet den Browser für die Behandlung des HTTP-Datenverkehrs im Hintergrund.<br><br>Blazor Server-Apps enthalten standardmäßig kein `HttpClient`, das als Dienst konfiguriert ist. Stellen Sie ein `HttpClient` für eine Blazor Server-App bereit.<br><br>Weitere Informationen finden Sie unter <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor WebAssembly)<br>Bereichsbezogen (Blazor Server) | Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe verteilt werden. Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>. |
| `NavigationManager` | Singleton (Blazor WebAssembly)<br>Bereichsbezogen (Blazor Server) | Enthält Hilfsprogramme für die Arbeit mit URIs und dem Navigationszustand. Weitere Informationen finden Sie unter [Hilfsprogramme für URI und Navigationszustand](xref:blazor/routing#uri-and-navigation-state-helpers). |

Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit. Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.

## <a name="add-services-to-an-app"></a>Hinzufügen von Diensten zu einer App

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Konfigurieren Sie Dienste für die Dienstsammlung der App in der `Main`-Methode von *Program.cs*. Im folgenden Beispiel ist die `MyDependency`-Implementierung für `IMyDependency` registriert:

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

Nachdem der Host erstellt wurde, kann auf die Dienste vom Stammbereich der Abhängigkeitsinjektion aus zugegriffen werden, bevor irgendwelche Komponenten bereitgestellt werden. Dies kann für die Ausführung der Initialisierungslogik vor dem Rendern von Inhalten nützlich sein:

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

Der Host stellt auch eine zentrale Konfigurationsinstanz für die App zur Verfügung. Ausgehend vom vorhergehenden Beispiel wird die URL des Wetterdiensts von einer Standardkonfigurationsquelle (z. B. *appsettings.json*) an `InitializeWeatherAsync` übergeben:

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

### <a name="blazor-server"></a>Blazor Server

Nachdem Sie eine neue App erstellt haben, untersuchen Sie die `Startup.ConfigureServices`-Methode:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Der Methode `ConfigureServices` wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> übergeben, das eine Liste von Dienstdeskriptorobjekten (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) ist. Dienste werden durch die Bereitstellung von Dienstdeskriptoren in die Dienstsammlung aufgenommen. Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess`-Schnittstelle und seiner konkreten Implementierung `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Lebensdauer von Diensten

Die Dienste können mit den in der folgenden Tabelle angegebenen Lebensdauern konfiguriert werden.

| Lebensdauer | Beschreibung |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Blazor WebAssembly-Apps verfügen derzeit nicht über ein Konzept für Bereiche von Abhängigkeitsinjektionen. `Scoped`-registrierte Dienste verhalten sich wie `Singleton`-Dienste. Das Blazor Server-Hostingmodell unterstützt jedoch die Lebensdauer `Scoped`. In Blazor Server-Apps wird die Registrierung eines bereichsbezogenen Diensts der *Verbindung* zugeordnet. Aus diesem Grund wird die Verwendung von bereichsbezogenen Diensten für Dienste bevorzugt, die dem aktuellen Benutzer zugeordnet werden sollen, auch wenn die aktuelle Absicht darin besteht, clientseitig im Browser ausgeführt zu werden. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Die Abhängigkeitsinjektion erstellt eine *Einzelinstanz* des Diensts. Alle Komponenten, die einen `Singleton`-Dienst erfordern, erhalten eine Instanz desselben Diensts. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Immer wenn eine Komponente eine Instanz eines `Transient`-Diensts aus dem Dienstcontainer erhält, erhält sie eine *neue Instanz* des Diensts. |

Das Abhängigkeitsinjektionssystem basiert auf dem Abhängigkeitsinjektionssystem in ASP.NET Core. Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Anfordern eines Diensts in einer Komponente

Nachdem die Dienste der Dienstsammlung hinzugefügt wurden, fügen Sie die Dienste mit der Razor-Anweisung [\@inject](xref:mvc/views/razor#inject) in die Komponenten ein. `@inject` besitzt zwei Parameter:

* Typ &ndash;: Der Typ des einzufügenden Diensts.
* Eigenschaft &ndash;: Der Name der Eigenschaft, die den eingefügten App-Dienst erhält. Die Eigenschaft erfordert keine manuelle Erstellung. Der Compiler erstellt die Eigenschaft.

Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.

Verwenden Sie mehrere `@inject`-Anweisungen, um verschiedene Dienste einzufügen.

Das folgende Beispiel veranschaulicht die Verwendung von `@inject`. Der Dienst, der `Services.IDataAccess` implementiert, wird in die Eigenschaft `DataRepository` der Komponente eingefügt. Beachten Sie, dass der Code nur die Abstraktion `IDataAccess` verwendet:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Intern verwendet die generierte Eigenschaft (`DataRepository`) das Attribut `InjectAttribute`. In der Regel wird dieses Attribut nicht direkt verwendet. Wenn eine Basisklasse für Komponenten erforderlich ist und die eingefügten Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie manuell `InjectAttribute` hinzu:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

In Komponenten, die von der Basisklasse abgeleitet sind, ist die `@inject`-Direktive nicht erforderlich. Das `InjectAttribute` der Basisklasse ist ausreichend:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Verwenden der Abhängigkeitsinjektion in Diensten

Komplexe Dienste können zusätzliche Dienste erfordern. Im vorherigen Beispiel könnte `DataAccess` den Standarddienst `HttpClient` erfordern. `@inject` (oder das `InjectAttribute`) ist nicht für die Verwendung in Diensten verfügbar. Stattdessen muss die *Constructor Injection* verwendet werden. Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt. Wenn die Abhängigkeitsinjektion den Dienst erstellt, erkennt sie die erforderlichen Dienste im Konstruktor und stellt sie entsprechend zur Verfügung.

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

Voraussetzungen für die Constructor Injection:

* Es muss einen Konstruktor geben, dessen Argumente alle von der Abhängigkeitsinjektion erfüllt werden können. Zusätzliche, nicht durch die Abhängigkeitsinjektion abgedeckte Parameter sind zulässig, wenn sie Standardwerte angeben.
* Der anwendbare Konstruktor muss *öffentlich* sein.
* Es muss ein anwendbarer Konstruktor vorhanden sein. Im Falle einer Mehrdeutigkeit löst die Abhängigkeitsinjektion eine Ausnahme aus.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion

In ASP.NET Core-Apps werden bereichsbezogene Dienste in der Regel der aktuellen Anforderung zugeordnet. Nachdem die Anforderung abgeschlossen ist, werden alle bereichsbezogenen oder vorübergehenden Dienste vom Abhängigkeitsinjektionssystem entsorgt. In Blazor Server-Apps besteht der Anforderungsbereich für die Dauer der Clientverbindung, was dazu führen kann, dass vorübergehende und bereichsbezogene Dienste viel länger als erwartet leben. In Blazor WebAssembly-Apps werden Dienste, die mit einer bereichsbezogenen Lebensdauer registriert sind, als Singletons behandelt, sodass sie länger leben als bereichsbezogene Dienste in typischen ASP.NET Core-Apps.

Ein Ansatz, der die Lebensdauer eines Diensts in Blazor-Apps begrenzt, ist die Verwendung des Typs `OwningComponentBase`. `OwningComponentBase` ist ein abstrakter, von `ComponentBase` abgeleiteter Typ, der einen der Lebensdauer der Komponente entsprechenden Bereich für die Abhängigkeitsinjektion erstellt. Mit diesem Bereich ist es möglich, Abhängigkeitsinjektionsdienste mit einer bereichsbezogenen Lebensdauer zu nutzen und sie so lange wie die Komponente zu nutzen. Wenn die Komponente zerstört wird, werden auch die Dienste des bereichsbezogenen Dienstanbieters der Komponente entsorgt. Dies kann für Dienste nützlich sein, für die Folgendes gilt:

* Sie sollten innerhalb einer Komponente wiederverwendet werden, da die vorübergehende Lebensdauer unangemessen ist.
* Sie sollten nicht komponentenübergreifend freigegeben werden, da die Singleton-Lebensdauer unangemessen ist.

Zwei Versionen des Typs `OwningComponentBase` sind verfügbar:

* `OwningComponentBase` ist ein abstraktes, verwerfbares, untergeordnetes Element vom Typ `ComponentBase` mit einer geschützten `ScopedServices`-Eigenschaft vom Typ `IServiceProvider`. Dieser Anbieter kann zur Auflösung von Diensten verwendet werden, die der Lebensdauer der Komponente zugeordnet sind.

  Abhängigkeitsinjektionsdienste, die mit `@inject` oder `InjectAttribute` (`[Inject]`) in die Komponente eingefügt werden, werden nicht im Gültigkeitsbereich der Komponente erstellt. Um den Bereich der Komponente zu verwenden, müssen die Dienste mit `ScopedServices.GetRequiredService` oder `ScopedServices.GetService` aufgelöst werden. Allen Diensten, die unter Verwendung des `ScopedServices`-Anbieters aufgelöst werden, werden ihre Abhängigkeiten aus demselben Bereich bereitgestellt.

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

* `OwningComponentBase<T>` ist abgeleitet von `OwningComponentBase` und fügt eine Eigenschaft `Service` hinzu, die eine Instanz von `T` vom bereichsbezogenen Abhängigkeitsinjektionsanbieter zurückgibt. Dieser Typ ist eine komfortable Methode für den Zugriff auf bereichsbezogene Dienste, ohne eine Instanz von `IServiceProvider` zu verwenden, wenn es einen primären Dienst gibt, den die Anwendung aus dem Abhängigkeitsinjektionscontainer unter Verwendung des Bereichs der Komponente benötigt. Die Eigenschaft `ScopedServices` ist verfügbar, sodass die App bei Bedarf auch Dienste mit anderen Typen erhalten kann.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Verwenden von Entity Framework-DbContext von der Abhängigkeitsinjektion

Ein allgemeiner Diensttyp zum Abrufen der Abhängigkeitsinjektion in Web-Apps sind EF-`DbContext`-Objekte (Entity Framework). Beim Registrieren von EF-Diensten mithilfe von `IServiceCollection.AddDbContext` wird `DbContext` standardmäßig als bereichsbezogener Dienst hinzugefügt. Die Registrierung als bereichsbezogener Dienst kann zu Problemen in Blazor-Apps führen, da sie dazu führt, dass `DbContext`-Instanzen langlebig sind und in der gesamten App gemeinsam genutzt werden. `DbContext` ist nicht threadsicher und darf nicht gleichzeitig verwendet werden.

Je nach App kann die Verwendung von `OwningComponentBase` zur Begrenzung des Bereichs von `DbContext` auf eine einzelne Komponente das Problem *möglicherweise* lösen. Wenn eine Komponente nicht parallel ein `DbContext` verwendet, reicht es aus, die Komponente aus `OwningComponentBase` abzuleiten und den `DbContext` aus `ScopedServices` abzurufen, da Folgendes sichergestellt wird:

* Separate Komponenten teilen sich kein `DbContext`.
* Der `DbContext` lebt nur so lange, wie die von ihm abhängige Komponente.

Wenn eine einzelne Komponente gleichzeitig einen `DbContext` verwenden könnte (z. B. jedes Mal, wenn ein Benutzer eine Schaltfläche auswählt), vermeidet selbst die Verwendung von `OwningComponentBase` die Probleme mit gleichzeitigen EF-Vorgängen nicht. Verwenden Sie in diesem Fall für jeden logischen EF-Vorgang einen anderen `DbContext`. Verwenden Sie einen der folgenden Ansätze:

* Erstellen Sie den `DbContext` direkt mit `DbContextOptions<TContext>` als Argument, das von der Abhängigkeitsinjektion abgerufen werden kann und threadsicher ist.

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

* Registrieren Sie den `DbContext` im Dienstcontainer mit einer vorübergehenden Lebensdauer:
  * Verwenden Sie bei der Registrierung des Kontexts `ServiceLifetime.Transient`. Die `AddDbContext`-Erweiterungsmethode nimmt zwei optionale Parameter vom Typ `ServiceLifetime` an. Um diesen Ansatz zu verwenden, muss nur der Parameter `contextLifetime` entsprechend `ServiceLifetime.Transient` sein. `optionsLifetime` kann seinen Standardwert von `ServiceLifetime.Scoped` beibehalten.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Der vorübergehende `DbContext` kann wie üblich (mit `@inject`) in Komponenten eingefügt werden, die nicht mehrere EF-Vorgänge parallel ausführen werden. Diejenigen, die mehrere EF-Vorgänge gleichzeitig durchführen können, können mit `IServiceProvider.GetRequiredService` separate `DbContext`-Objekte für jeden parallelen Vorgang anfordern.

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

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
