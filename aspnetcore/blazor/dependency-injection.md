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
# <a name="aspnet-core-blazor-dependency-injection"></a>ASP.net Core blazor-Abhängigkeitsinjektion

Von [Rainer stropek](https://www.timecockpit.com)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor unterstützt die [Abhängigkeitsinjektion (di)](xref:fundamentals/dependency-injection). Apps können integrierte Dienste verwenden, indem Sie Sie in Komponenten einfügen. Apps können auch benutzerdefinierte Dienste definieren und registrieren und in der gesamten App über di verfügbar machen.

DI ist eine Technik für den Zugriff auf Dienste, die an einem zentralen Ort konfiguriert sind. Dies kann bei blazor-Apps für folgende Aktionen nützlich sein:

* Freigeben einer einzelnen Instanz einer Dienstklasse für viele Komponenten, die als *Singleton* -Dienst bezeichnet werden.
* Entkoppeln von Komponenten aus konkreten Dienst Klassen mithilfe von Verweis Abstraktionen. Stellen Sie sich beispielsweise eine `IDataAccess` Schnittstelle für den Zugriff auf Daten in der APP vor. Die Schnittstelle wird von einer konkreten `DataAccess` Klasse implementiert und als Dienst im Dienst Container der APP registriert. Wenn eine Komponente di zum Empfangen einer `IDataAccess` Implementierung verwendet, ist die Komponente nicht mit dem konkreten Typ gekoppelt. Die-Implementierung kann ausgetauscht werden, vielleicht für eine Pseudo Implementierung in Komponententests.

## <a name="default-services"></a>Standarddienste

Standarddienste werden automatisch der Dienst Sammlung der app hinzugefügt.

| Dienst | Lebensdauer | Beschreibung |
| ------- | -------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Singleton | Stellt Methoden zum Senden von HTTP-Anforderungen und empfangen von HTTP-Antworten aus einer Ressource bereit, die durch einen URI identifiziert wird Beachten Sie, dass diese `HttpClient` Instanz von den Browser zum Verarbeiten des HTTP-Datenverkehrs im Hintergrund verwendet. [HttpClient. BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) wird automatisch auf das Basis-URI-Präfix der APP festgelegt. Weitere Informationen finden Sie unter <xref:blazor/call-web-api>. |
| `IJSRuntime` | Singleton | Stellt eine Instanz einer JavaScript-Laufzeit dar, in der JavaScript-Aufrufe gesendet werden. Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>. |
| `NavigationManager` | Singleton | Enthält Hilfsprogramme zum Arbeiten mit URIs und dem Navigations Zustand. Weitere Informationen finden Sie unter [URI-und Navigations Zustands Hilfen](xref:blazor/routing#uri-and-navigation-state-helpers). |

Ein benutzerdefinierter Dienstanbieter stellt nicht automatisch die in der Tabelle aufgeführten Standarddienste bereit. Wenn Sie einen benutzerdefinierten Dienstanbieter verwenden und einen der Dienste benötigen, die in der Tabelle angezeigt werden, fügen Sie dem neuen Dienstanbieter die erforderlichen Dienste hinzu.

## <a name="add-services-to-an-app"></a>Hinzufügen von Diensten zu einer APP

Überprüfen Sie nach dem Erstellen einer neuen `Startup.ConfigureServices` APP die-Methode:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

An `ConfigureServices` die-Methode wird <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>ein-Objekt übermittelt, das eine Liste von Dienst<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>Deskriptoren-Objekten () ist. Dienste werden hinzugefügt, indem Dienst Deskriptoren für die Dienst Sammlung bereitgestellt werden. Das folgende Beispiel veranschaulicht das Konzept mit der `IDataAccess` -Schnittstelle und ihrer `DataAccess`konkreten Implementierung:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Dienste können mit der in der folgenden Tabelle angezeigten Lebensdauer konfiguriert werden.

| Lebensdauer | Beschreibung |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Blazor Webassembly-apps haben zurzeit kein Konzept von di-Bereichen. `Scoped`-registrierte Dienste Verhalten sich `Singleton` wie Dienste. Das serverhostingmodell des blazor- `Scoped` Servers unterstützt jedoch die Lebensdauer. In blazor-Server-apps wird eine Bereichs bezogene Dienst Registrierung auf die *Verbindung*beschränkt. Aus diesem Grund wird die Verwendung von Bereichs bezogenen Diensten für Dienste bevorzugt, die auf den aktuellen Benutzer beschränkt werden sollten, auch wenn die aktuelle Absicht ist, die Client seitige Ausführung im Browser auszuführen. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI erstellt eine *einzelne Instanz* des Dienstanbieter. Alle Komponenten, die `Singleton` einen Dienst erfordern, erhalten eine Instanz desselben diensdienstanbieter. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Wenn eine Komponente eine Instanz eines `Transient` Dienstanbieter aus dem Dienst Container abruft, empfängt Sie eine *neue Instanz* des Dienstanbieter. |

Das di-System basiert auf dem System System in ASP.net Core. Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="request-a-service-in-a-component"></a>Anfordern eines Dienstanbieter in einer Komponente

Nachdem die Dienste der Dienst Auflistung hinzugefügt wurden, fügen Sie die Dienste mithilfe der [ \@](xref:mvc/views/razor#inject) Razor-Direktive einfügen in die Komponenten ein. `@inject`verfügt über zwei Parameter:

* Geben &ndash; Sie den Typ des einzuschleusenden Dienstanbieter ein.
* Eigenschaft &ndash; der Name der Eigenschaft, die den injizierten App-Dienst empfängt. Die-Eigenschaft erfordert keine manuelle Erstellung. Der Compiler erstellt die-Eigenschaft.

Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.

Verwenden Sie `@inject` mehrere-Anweisungen, um unterschiedliche Dienste einzufügen.

Das folgende Beispiel veranschaulicht die Verwendung von `@inject`. Der Dienst, `Services.IDataAccess` der implementiert, wird in die- `DataRepository`Eigenschaft der Komponente eingefügt. Beachten Sie, dass der Code nur die `IDataAccess` Abstraktion verwendet:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.razor?highlight=2-3,23)]

Intern wird die generierte Eigenschaft (`DataRepository`) mit dem `InjectAttribute` -Attribut versehen. In der Regel wird dieses Attribut nicht direkt verwendet. Wenn eine Basisklasse für Komponenten erforderlich ist und eingefügte Eigenschaften auch für die Basisklasse erforderlich sind, fügen `InjectAttribute`Sie manuell Folgendes hinzu:

```csharp
public class ComponentBase : IComponent
{
    // DI works even if using the InjectAttribute in a component's base class.
    [Inject]
    protected IDataAccess DataRepository { get; set; }
    ...
}
```

In Komponenten, die von der-Basisklasse `@inject` abgeleitet sind, ist die-Anweisung nicht erforderlich. Der `InjectAttribute` der Basisklasse ist ausreichend:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="use-di-in-services"></a>Verwenden von di in Diensten

Für komplexe Dienste sind möglicherweise zusätzliche Dienste erforderlich. Im vorherigen Beispiel ist `DataAccess` möglicherweise der `HttpClient` Standard Dienst erforderlich. `@inject`(oder) `InjectAttribute`ist für die Verwendung in-Diensten nicht verfügbar. Stattdessen muss die *Konstruktorinjektion* verwendet werden. Erforderliche Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt. Wenn di den Dienst erstellt, werden die erforderlichen Dienste im Konstruktor erkannt und entsprechend bereitstellt.

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

In ASP.net Core-apps werden Bereichs bezogene Dienste in der Regel auf die aktuelle Anforderung festgelegt. Nachdem die Anforderung abgeschlossen ist, werden alle Bereichs bezogenen oder vorübergehenden Dienste vom System System entfernt. In blazor-Server-apps dauert der Anforderungs Bereich für die Dauer der Client Verbindung, was dazu führen kann, dass vorübergehende und Bereichs bezogene Dienste viel länger als erwartet Leben.

Um Dienste auf die Lebensdauer einer Komponente zu beschränken, kann die `OwningComponentBase` - `OwningComponentBase<TService>` Klasse und die-Basisklasse verwenden. Diese Basisklassen machen eine `ScopedServices` Eigenschaft vom Typ `IServiceProvider` verfügbar, die Dienste auflöst, die auf die Lebensdauer der Komponente beschränkt sind. Verwenden Sie die `@inherits` -Direktive, um eine Komponente zu erstellen, die von einer Basisklasse in Razor erbt.

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
> Dienste, die mithilfe `@inject` von `InjectAttribute` oder in die Komponente eingefügt werden, werden nicht im Bereich der Komponente erstellt und sind an den Anforderungs Bereich gebunden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
