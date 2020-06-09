---
title: "title: 'Blazor-Abhängigkeitsinjektion in ASP.NET Core' author: guardrex description: 'Erfahren Sie, wie Blazor-Apps Dienste in Komponenten einfügen können.'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date: 05/19/2020 no-loc:"
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/dependency-injection
ms.openlocfilehash: d5b0747fb0505499197d1751511600bd91fed41d
ms.sourcegitcommit: 5fe724d143825ca799e5bd03fb45b632ea4aa7d5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/02/2020
ms.locfileid: "84271782"
---
# <a name="aspnet-core-blazor-dependency-injection"></a>'Blazor'

'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid: blazor/dependency-injection

Abhängigkeitsinjektion in ASP.NET Core Blazor Von [Rainer Stropek](https://www.timecockpit.com) und [Mike Rousos](https://github.com/mjrousos)

* Blazor unterstützt die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI).
* Apps können integrierte Dienste verwenden, indem Sie sie in Komponenten einfügen. Apps können auch benutzerdefinierte Dienste definieren und registrieren und sie über die Abhängigkeitsinjektion in der gesamten App verfügbar machen. Die Abhängigkeitsinjektion ist ein Verfahren für den Zugriff auf Dienste, die an einer zentralen Stelle konfiguriert sind. Dies kann in Blazor-Apps für Folgendes nützlich sein: Freigabe einer einzelnen Instanz einer Dienstklasse über viele Komponenten hinweg, bekannt als *Singleton*-Dienst.

## <a name="default-services"></a>Entkoppeln von Komponenten von konkreten Dienstklassen durch Verwendung von Referenzabstraktionen.

Betrachten Sie zum Beispiel eine `IDataAccess`-Schnittstelle für den Zugriff auf Daten in der App.

| Die Schnittstelle wird durch eine konkrete `DataAccess`-Klasse implementiert und als Dienst im Dienstcontainer der App registriert. | Wenn eine Komponente die Abhängigkeitsinjektion verwendet, um eine `IDataAccess`-Implementierung zu erhalten, ist die Komponente nicht an den konkreten Typ gekoppelt. | Die Implementierung kann ausgetauscht werden, z. B. gegen eine Pseudoimplementierung in Komponententests. |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Standarddienste | Standarddienste werden automatisch zur Dienstsammlung der App hinzugefügt.<br><br>Dienst<br><br>Lebensdauer Beschreibung<br><br>Transient (vorübergehend) |
| <xref:Microsoft.JSInterop.IJSRuntime> | Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource bereit, die von einem URI identifiziert wird.<br>Die Instanz von <xref:System.Net.Http.HttpClient> in einer Blazor WebAssembly-App verwendet den Browser für die Behandlung des HTTP-Datenverkehrs im Hintergrund. | Blazor Server-Apps enthalten standardmäßig keinen <xref:System.Net.Http.HttpClient>, der als Dienst konfiguriert ist. Stellen Sie einen <xref:System.Net.Http.HttpClient> für eine Blazor Server-App bereit. |
| <xref:Microsoft.AspNetCore.Components.NavigationManager> | Weitere Informationen finden Sie unter <xref:blazor/call-web-api>.<br>Singleton (Blazor WebAssembly) | Bereichsbezogen (Blazor Server) Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe verteilt werden. |

Weitere Informationen finden Sie unter <xref:blazor/call-javascript-from-dotnet>. Singleton (Blazor WebAssembly)

## <a name="add-services-to-an-app"></a>Bereichsbezogen (Blazor Server)

### <a name="blazor-webassembly"></a>Enthält Hilfsprogramme für die Arbeit mit URIs und dem Navigationszustand.

Weitere Informationen finden Sie unter [Hilfsprogramme für URI und Navigationszustand](xref:blazor/routing#uri-and-navigation-state-helpers). Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit.

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

Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu. Hinzufügen von Diensten zu einer App

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

Blazor WebAssembly Konfigurieren Sie Dienste für die Dienstsammlung der App in der `Main`-Methode von *Program.cs*.

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

### <a name="blazor-server"></a>Im folgenden Beispiel ist die `MyDependency`-Implementierung für `IMyDependency` registriert:

Nachdem der Host erstellt wurde, kann auf die Dienste vom Stammbereich der Abhängigkeitsinjektion aus zugegriffen werden, bevor irgendwelche Komponenten bereitgestellt werden.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Dies kann für die Ausführung der Initialisierungslogik vor dem Rendern von Inhalten nützlich sein: Der Host stellt auch eine zentrale Konfigurationsinstanz für die App zur Verfügung. Ausgehend vom vorhergehenden Beispiel wird die URL des Wetterdiensts von einer Standardkonfigurationsquelle (z. B. *appsettings.json*) an `InitializeWeatherAsync` übergeben:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

### <a name="service-lifetime"></a>Blazor Server

Nachdem Sie eine neue App erstellt haben, untersuchen Sie die `Startup.ConfigureServices`-Methode:

| Der Methode <xref:Microsoft.Extensions.Hosting.IHostBuilder.ConfigureServices%2A> wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> übergeben, das eine Liste von Dienstdeskriptorobjekten (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>) ist. | Dienste werden durch die Bereitstellung von Dienstdeskriptoren in die Dienstsammlung aufgenommen. |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped%2A> | Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess`-Schnittstelle und seiner konkreten Implementierung `DataAccess`: Lebensdauer von Diensten Die Dienste können mit den in der folgenden Tabelle angegebenen Lebensdauern konfiguriert werden. Lebensdauer Beschreibung |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton%2A> | Blazor WebAssembly-Apps verfügen derzeit nicht über ein Konzept für Bereiche von Abhängigkeitsinjektionen. `Scoped`-registrierte Dienste verhalten sich wie `Singleton`-Dienste. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient%2A> | Das Blazor Server-Hostingmodell unterstützt jedoch die Lebensdauer `Scoped`. |

In Blazor Server-Apps wird die Registrierung eines bereichsbezogenen Diensts der *Verbindung* zugeordnet. Aus diesem Grund wird die Verwendung von bereichsbezogenen Diensten für Dienste bevorzugt, die dem aktuellen Benutzer zugeordnet werden sollen, auch wenn die aktuelle Absicht darin besteht, clientseitig im Browser ausgeführt zu werden.

## <a name="request-a-service-in-a-component"></a>Die Abhängigkeitsinjektion erstellt eine *Einzelinstanz* des Diensts.

Alle Komponenten, die einen `Singleton`-Dienst erfordern, erhalten eine Instanz desselben Diensts. Immer wenn eine Komponente eine Instanz eines `Transient`-Diensts aus dem Dienstcontainer erhält, erhält sie eine *neue Instanz* des Diensts.

* Das Abhängigkeitsinjektionssystem basiert auf dem Abhängigkeitsinjektionssystem in ASP.NET Core.
* Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>. Anfordern eines Diensts in einer Komponente Nachdem die Dienste der Dienstsammlung hinzugefügt wurden, fügen Sie die Dienste mit der Razor-Anweisung [\@inject](xref:mvc/views/razor#inject) in die Komponenten ein.

[`@inject`](xref:mvc/views/razor#inject) besitzt zwei Parameter:

Typ: Der Typ des einzufügenden Diensts.

Eigenschaft: Der Name der Eigenschaft, die den eingefügten App-Dienst erhält. Die Eigenschaft erfordert keine manuelle Erstellung. Der Compiler erstellt die Eigenschaft.

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,20)]

Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>. Verwenden Sie mehrere [`@inject`](xref:mvc/views/razor#inject)-Anweisungen, um verschiedene Dienste einzufügen. Das folgende Beispiel veranschaulicht die Verwendung von [`@inject`](xref:mvc/views/razor#inject).

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

Der Dienst, der `Services.IDataAccess` implementiert, wird in die Eigenschaft `DataRepository` der Komponente eingefügt. Beachten Sie, dass der Code nur die Abstraktion `IDataAccess` verwendet:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Intern verwendet die generierte Eigenschaft (`DataRepository`) das Attribut [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute).

In der Regel wird dieses Attribut nicht direkt verwendet. Wenn eine Basisklasse für Komponenten erforderlich ist und die eingefügten Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut manuell hinzu: In Komponenten, die von der Basisklasse abgeleitet sind, ist die [`@inject`](xref:mvc/views/razor#inject)-Direktive nicht erforderlich. Das <xref:Microsoft.AspNetCore.Components.InjectAttribute> der Basisklasse ist ausreichend: Verwenden der Abhängigkeitsinjektion in Diensten Komplexe Dienste können zusätzliche Dienste erfordern.

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

Im vorherigen Beispiel könnte `DataAccess` den Standarddienst <xref:System.Net.Http.HttpClient> erfordern.

* [`@inject`](xref:mvc/views/razor#inject) (oder das [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut) ist nicht für die Verwendung in Diensten verfügbar. Stattdessen muss die *Constructor Injection* verwendet werden.
* Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt.
* Wenn die Abhängigkeitsinjektion den Dienst erstellt, erkennt sie die erforderlichen Dienste im Konstruktor und stellt sie entsprechend zur Verfügung. Voraussetzungen für die Constructor Injection:

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Es muss einen Konstruktor geben, dessen Argumente alle von der Abhängigkeitsinjektion erfüllt werden können.

Zusätzliche, nicht durch die Abhängigkeitsinjektion abgedeckte Parameter sind zulässig, wenn sie Standardwerte angeben. Der anwendbare Konstruktor muss *öffentlich* sein. Es muss ein anwendbarer Konstruktor vorhanden sein. Im Falle einer Mehrdeutigkeit löst die Abhängigkeitsinjektion eine Ausnahme aus.

Hilfsprogramm-Basiskomponentenklassen zur Verwaltung eines Bereichs für die Abhängigkeitsinjektion In ASP.NET Core-Apps werden bereichsbezogene Dienste in der Regel der aktuellen Anforderung zugeordnet. Nachdem die Anforderung abgeschlossen ist, werden alle bereichsbezogenen oder vorübergehenden Dienste vom Abhängigkeitsinjektionssystem entsorgt. In Blazor Server-Apps besteht der Anforderungsbereich für die Dauer der Clientverbindung, was dazu führen kann, dass vorübergehende und bereichsbezogene Dienste viel länger als erwartet leben. In Blazor WebAssembly-Apps werden Dienste, die mit einer bereichsbezogenen Lebensdauer registriert sind, als Singletons behandelt, sodass sie länger leben als bereichsbezogene Dienste in typischen ASP.NET Core-Apps.

* Ein Ansatz, der die Lebensdauer eines Diensts in Blazor-Apps begrenzt, ist die Verwendung des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase>.
* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstrakter, von <xref:Microsoft.AspNetCore.Components.ComponentBase> abgeleiteter Typ, der einen der Lebensdauer der Komponente entsprechenden Bereich für die Abhängigkeitsinjektion erstellt.

Mit diesem Bereich ist es möglich, Abhängigkeitsinjektionsdienste mit einer bereichsbezogenen Lebensdauer zu nutzen und sie so lange wie die Komponente zu nutzen.

* Wenn die Komponente zerstört wird, werden auch die Dienste des bereichsbezogenen Dienstanbieters der Komponente entsorgt. Dies kann für Dienste nützlich sein, für die Folgendes gilt:

  Sie sollten innerhalb einer Komponente wiederverwendet werden, da die vorübergehende Lebensdauer unangemessen ist. Sie sollten nicht komponentenübergreifend freigegeben werden, da die Singleton-Lebensdauer unangemessen ist. Zwei Versionen des Typs <xref:Microsoft.AspNetCore.Components.OwningComponentBase> sind verfügbar:

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

* <xref:Microsoft.AspNetCore.Components.OwningComponentBase> ist ein abstraktes, verwerfbares, untergeordnetes Element vom Typ <xref:Microsoft.AspNetCore.Components.ComponentBase> mit einer geschützten <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Eigenschaft vom Typ <xref:System.IServiceProvider>. Dieser Anbieter kann zur Auflösung von Diensten verwendet werden, die der Lebensdauer der Komponente zugeordnet sind. Abhängigkeitsinjektionsdienste, die mit [`@inject`](xref:mvc/views/razor#inject) oder dem [`[Inject]`](xref:Microsoft.AspNetCore.Components.InjectAttribute)-Attribut in die Komponente eingefügt werden, werden nicht im Gültigkeitsbereich der Komponente erstellt.

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

## <a name="use-of-entity-framework-dbcontext-from-di"></a>Um den Bereich der Komponente zu verwenden, müssen die Dienste mit <xref:Microsoft.Extensions.DependencyInjection.ServiceProviderServiceExtensions.GetRequiredService%2A> oder <xref:System.IServiceProvider.GetService%2A> aufgelöst werden.

Allen Diensten, die unter Verwendung des <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices>-Anbieters aufgelöst werden, werden ihre Abhängigkeiten aus demselben Bereich bereitgestellt. <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601> ist abgeleitet von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> und fügt eine Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase%601.Service%2A> hinzu, die eine Instanz von `T` vom bereichsbezogenen Abhängigkeitsinjektionsanbieter zurückgibt. Dieser Typ ist eine komfortable Methode für den Zugriff auf bereichsbezogene Dienste, ohne eine Instanz von <xref:System.IServiceProvider> zu verwenden, wenn es einen primären Dienst gibt, den die Anwendung aus dem Abhängigkeitsinjektionscontainer unter Verwendung des Bereichs der Komponente benötigt. Die Eigenschaft <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> ist verfügbar, sodass die App bei Bedarf auch Dienste mit anderen Typen erhalten kann.

Verwenden von Entity Framework-DbContext von der Abhängigkeitsinjektion Ein allgemeiner Diensttyp zum Abrufen der Abhängigkeitsinjektion in Web-Apps sind EF-<xref:Microsoft.EntityFrameworkCore.DbContext>-Objekte (Entity Framework).

* Beim Registrieren von EF-Diensten mithilfe von <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A> wird <xref:Microsoft.EntityFrameworkCore.DbContext> standardmäßig als bereichsbezogener Dienst hinzugefügt.
* Die Registrierung als bereichsbezogener Dienst kann zu Problemen in Blazor-Apps führen, da sie dazu führt, dass <xref:Microsoft.EntityFrameworkCore.DbContext>-Instanzen langlebig sind und in der gesamten App gemeinsam genutzt werden.

<xref:Microsoft.EntityFrameworkCore.DbContext> ist nicht threadsicher und darf nicht gleichzeitig verwendet werden. Je nach App kann die Verwendung von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> zur Begrenzung des Bereichs von <xref:Microsoft.EntityFrameworkCore.DbContext> auf eine einzelne Komponente das Problem *möglicherweise* lösen. Wenn eine Komponente nicht parallel ein <xref:Microsoft.EntityFrameworkCore.DbContext> verwendet, reicht es aus, die Komponente aus <xref:Microsoft.AspNetCore.Components.OwningComponentBase> abzuleiten und den <xref:Microsoft.EntityFrameworkCore.DbContext> aus <xref:Microsoft.AspNetCore.Components.OwningComponentBase.ScopedServices> abzurufen, da Folgendes sichergestellt wird:

* Separate Komponenten teilen sich kein <xref:Microsoft.EntityFrameworkCore.DbContext>.

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

* Der <xref:Microsoft.EntityFrameworkCore.DbContext> lebt nur so lange, wie die von ihm abhängige Komponente.
  * Wenn eine einzelne Komponente gleichzeitig einen <xref:Microsoft.EntityFrameworkCore.DbContext> verwenden könnte (z. B. jedes Mal, wenn ein Benutzer eine Schaltfläche auswählt), vermeidet selbst die Verwendung von <xref:Microsoft.AspNetCore.Components.OwningComponentBase> die Probleme mit gleichzeitigen EF-Vorgängen nicht. Verwenden Sie in diesem Fall für jeden logischen EF-Vorgang einen anderen <xref:Microsoft.EntityFrameworkCore.DbContext>. Verwenden Sie einen der folgenden Ansätze: Erstellen Sie den <xref:Microsoft.EntityFrameworkCore.DbContext> direkt mit <xref:Microsoft.EntityFrameworkCore.DbContextOptions%601> als Argument, das von der Abhängigkeitsinjektion abgerufen werden kann und threadsicher ist.

    ```csharp
    services.AddDbContext<AppDbContext>(options =>
         options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")),
         ServiceLifetime.Transient);
    ```  

  * Registrieren Sie den <xref:Microsoft.EntityFrameworkCore.DbContext> im Dienstcontainer mit einer vorübergehenden Lebensdauer: Verwenden Sie bei der Registrierung des Kontexts <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType>.

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

## <a name="additional-resources"></a>Die <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext%2A>-Erweiterungsmethode nimmt zwei optionale Parameter vom Typ <xref:Microsoft.Extensions.DependencyInjection.ServiceLifetime> an.

* <xref:fundamentals/dependency-injection>
* Um diesen Ansatz zu verwenden, muss nur der Parameter `contextLifetime` entsprechend <xref:Microsoft.OData.ServiceLifetime.Transient?displayProperty=nameWithType> sein.
* <xref:mvc/views/dependency-injection>
