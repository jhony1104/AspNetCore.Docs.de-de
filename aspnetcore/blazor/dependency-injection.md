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
ms.openlocfilehash: 6930d721f04fd5f7cad2ba472724497a157fda0f
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159975"
---
# <a name="aspnet-core-opno-locblazor-dependency-injection"></a>ASP.net Core Blazor Abhängigkeitsinjektion

Von [Rainer stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor unterstützt die [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection). Apps können integrierte Dienste verwenden, indem Sie Sie in Komponenten einfügen. Apps können auch benutzerdefinierte Dienste definieren und registrieren und in der gesamten App über di verfügbar machen.

DI ist eine Technik für den Zugriff auf Dienste, die an einem zentralen Ort konfiguriert sind. Dies kann in Blazor-apps nützlich sein:

* Freigeben einer einzelnen Instanz einer Dienstklasse für viele Komponenten, die als *Singleton* -Dienst bezeichnet werden.
* Entkoppeln von Komponenten aus konkreten Dienst Klassen mithilfe von Verweis Abstraktionen. Nehmen Sie beispielsweise eine Schnittstelle `IDataAccess` für den Zugriff auf Daten in der app. Die Schnittstelle wird von einer konkreten `DataAccess` Klasse implementiert und als Dienst im Dienst Container der APP registriert. Wenn eine Komponente di zum Empfangen einer `IDataAccess`-Implementierung verwendet, ist die Komponente nicht mit dem konkreten Typ gekoppelt. Die-Implementierung kann ausgetauscht werden, vielleicht für eine Pseudo Implementierung in Komponententests.

## <a name="default-services"></a>Standarddienste

Standarddienste werden automatisch der Dienst Sammlung der app hinzugefügt.

| Dienst | Lebensdauer | Beschreibung |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Stellt Methoden zum Senden von HTTP-Anforderungen und empfangen von HTTP-Antworten aus einer Ressource bereit, die durch einen URI identifiziert wird<br><br>Die Instanz von `HttpClient` in einer Blazor Webassembly-App verwendet den Browser, um den HTTP-Datenverkehr im Hintergrund zu verarbeiten.<br><br>Blazor Server-Apps enthalten keine `HttpClient`, die standardmäßig als Dienst konfiguriert sind. Stellen Sie eine `HttpClient` für eine Blazor Server-App bereit.<br><br>Weitere Informationen finden Sie unter <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton (Blazor Webassembly)<br>Bereich (Blazor Server) | Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe gesendet werden. Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>. |
| `NavigationManager` | Singleton (Blazor Webassembly)<br>Bereich (Blazor Server) | Enthält Hilfsprogramme zum Arbeiten mit URIs und dem Navigations Zustand. Weitere Informationen finden Sie unter [URI-und Navigations Zustands Hilfen](xref:blazor/routing#uri-and-navigation-state-helpers). |

Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit. Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.

## <a name="add-services-to-an-app"></a>Hinzufügen von Diensten zu einer APP

Nachdem Sie eine neue App erstellt haben, überprüfen Sie die `Startup.ConfigureServices`-Methode:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Der `ConfigureServices`-Methode wird ein <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>übermittelt, das eine Liste von Dienst Deskriptor-Objekten ist (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Dienste werden hinzugefügt, indem Dienst Deskriptoren für die Dienst Sammlung bereitgestellt werden. Im folgenden Beispiel wird das Konzept mit der `IDataAccess`-Schnittstelle und deren konkreten Implementierungs `DataAccess`veranschaulicht:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Dienste können mit der in der folgenden Tabelle angezeigten Lebensdauer konfiguriert werden.

| Lebensdauer | Beschreibung |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor Webassembly-apps verfügen derzeit nicht über ein Konzept von di-Bereichen. `Scoped`registrierte Dienste Verhalten sich wie `Singleton` Dienste. Das Blazor Server-Hostingmodell unterstützt jedoch die `Scoped` Lebensdauer. In Blazor Server-Apps ist eine Bereichs bezogene Dienst Registrierung auf die *Verbindung*beschränkt. Aus diesem Grund wird die Verwendung von Bereichs bezogenen Diensten für Dienste bevorzugt, die auf den aktuellen Benutzer beschränkt werden sollten, auch wenn die aktuelle Absicht ist, die Client seitige Ausführung im Browser auszuführen. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI erstellt eine *einzelne Instanz* des Dienstanbieter. Alle Komponenten, die einen `Singleton` Dienst erfordern, erhalten eine Instanz desselben diensdienstanbieter. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Wenn eine Komponente eine Instanz eines `Transient` Dienstanbieter aus dem Dienst Container erhält, empfängt Sie eine *neue Instanz* des Dienstanbieter. |

Das di-System basiert auf dem System System in ASP.net Core. Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Anfordern eines Dienstanbieter in einer Komponente

Nachdem die Dienste der Dienst Auflistung hinzugefügt wurden, fügen Sie die Dienste mithilfe des [\@](xref:mvc/views/razor#inject) Razor-Direktive in die Komponenten ein. `@inject` verfügt über zwei Parameter:

* Geben Sie &ndash; den Typ des einzuschlettenden Dienstanbieter ein.
* Eigenschaft &ndash; der Name der Eigenschaft, die den injizierten App-Dienst empfängt. Die-Eigenschaft erfordert keine manuelle Erstellung. Der Compiler erstellt die-Eigenschaft.

Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.

Verwenden Sie mehrere `@inject`-Anweisungen, um unterschiedliche Dienste einzufügen.

Das folgende Beispiel veranschaulicht die Verwendung von `@inject`. Der Dienst, der `Services.IDataAccess` implementiert, wird in die-Eigenschaften `DataRepository`der Komponente eingefügt. Beachten Sie, dass der Code nur die `IDataAccess` Abstraktion verwendet:

[!code-razor[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Intern verwendet die generierte Eigenschaft (`DataRepository`) das `InjectAttribute`-Attribut. In der Regel wird dieses Attribut nicht direkt verwendet. Wenn eine Basisklasse für Komponenten erforderlich ist und eingefügte Eigenschaften auch für die Basisklasse erforderlich sind, fügen Sie die `InjectAttribute`manuell hinzu:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

In Komponenten, die von der-Basisklasse abgeleitet sind, ist die `@inject`-Anweisung nicht erforderlich. Die `InjectAttribute` der Basisklasse ist ausreichend:

```razor
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Verwenden von di in Diensten

Für komplexe Dienste sind möglicherweise zusätzliche Dienste erforderlich. Im vorherigen Beispiel ist für `DataAccess` möglicherweise der `HttpClient` Standard Dienst erforderlich. `@inject` (oder das `InjectAttribute`) steht nicht für die Verwendung in-Diensten zur Verfügung. Stattdessen muss die *Konstruktorinjektion* verwendet werden. Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt. Wenn di den Dienst erstellt, werden die erforderlichen Dienste im Konstruktor erkannt und entsprechend bereitstellt.

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

Voraussetzungen für die Konstruktorinjektion:

* Ein Konstruktor muss vorhanden sein, dessen Argumente von di erfüllt werden können. Zusätzliche Parameter, die nicht von di abgedeckt werden, sind zulässig, wenn Sie Standardwerte angeben.
* Der anwendbare Konstruktor muss *öffentlich*sein.
* Ein anwendbarer Konstruktor muss vorhanden sein. Bei einer Mehrdeutigkeit löst di eine Ausnahme aus.

## <a name="utility-base-component-classes-to-manage-a-di-scope"></a>Basiskomponenten Klassen des-Hilfsprogramms zum Verwalten eines di-Bereichs

In ASP.net Core-apps werden Bereichs bezogene Dienste in der Regel auf die aktuelle Anforderung festgelegt. Nachdem die Anforderung abgeschlossen ist, werden alle Bereichs bezogenen oder vorübergehenden Dienste vom System System entfernt. In Blazor Server-apps dauert der Anforderungs Bereich für die Dauer der Client Verbindung, was dazu führen kann, dass vorübergehende und Bereichs bezogene Dienste viel länger als erwartet Leben.

Um Dienste auf die Lebensdauer einer Komponente zu beschränken, kann die `OwningComponentBase`-und `OwningComponentBase<TService>` Basisklassen verwenden. Diese Basisklassen machen eine `ScopedServices`-Eigenschaft des Typs `IServiceProvider` verfügbar, mit der Dienste aufgelöst werden, die auf die Lebensdauer der Komponente beschränkt sind. Verwenden Sie die `@inherits`-Direktive, um eine Komponente zu erstellen, die von einer Basisklasse in Razor erbt.

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
> Dienste, die mithilfe von `@inject` oder der `InjectAttribute` in die Komponente eingefügt werden, werden nicht im Bereich der Komponente erstellt und sind an den Anforderungs Bereich gebunden.

## <a name="additional-resources"></a>Weitere Ressourcen

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
