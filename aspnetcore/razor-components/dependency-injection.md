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
# <a name="razor-components-dependency-injection"></a>Abhängigkeitsinjektion für Razor-Komponenten

Durch [Rainer Stropek](https://www.timecockpit.com)

Razor-Komponenten unterstützt [Abhängigkeitsinjektion (Dependency Injection)](xref:fundamentals/dependency-injection). Apps können integrierte Dienste verwenden, indem sie in Komponenten einfügen. Apps können auch definieren und Registrieren von benutzerdefinierten Diensten und in der gesamten app über Dependency Injection zur Verfügung stellen.

## <a name="dependency-injection"></a>Dependency Injection

DI ist eine Technik für den Zugriff auf Dienste an einem zentralen Ort konfiguriert. Dies kann in Razor-Komponenten apps nützlich sein:

* Teilen Sie eine einzelne Instanz einer Dienstklasse in vielen Komponenten, bekannt als eine *Singleton* Service.
* Entkoppeln von konkreten Dienstklassen mit Verweis Abstraktionen. Betrachten Sie beispielsweise eine Schnittstelle `IDataAccess` für den Zugriff auf Daten in der app. Die Schnittstelle wird implementiert, indem Sie eine konkrete `DataAccess` Klasse und als Dienst im Dienstcontainer der app registriert ist. Wenn eine Komponente DI verwendet, zum Empfangen einer `IDataAccess` Implementierung, die Komponente ist nicht mit den konkreten Typ verknüpft. Die Implementierung kann z. B. um eine pseudoimplementierung in Komponententests ausgetauscht werden.

Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="add-services-to-di"></a>Hinzufügen von Diensten zu Dependency Injection

Überprüfen Sie nach dem Erstellen einer neuen app, die `Startup.ConfigureServices` Methode:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add custom services here
}
```

Die `ConfigureServices` -Methode übergeben eine <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>, eine Liste der Dienstobjekte-Deskriptor (<xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor>). Dienste werden hinzugefügt, durch die Bereitstellung von Dienst-Deskriptoren, um die Sammlung von Diensten. Das folgende Beispiel zeigt das Konzept, mit der `IDataAccess` -Schnittstelle und die konkrete Implementierung `DataAccess`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IDataAccess, DataAccess>();
}
```

Dienste können mit der Lebensdauer, die in der folgenden Tabelle gezeigt konfiguriert werden.

| Lebensdauer | Beschreibung |
| -------- | ----------- |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Singleton*> | DI erstellt eine *Einzelinstanz* des Diensts. Alle Komponenten, erfordern eine `Singleton` Service erhalten Sie eine Instanz des gleichen Diensts. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Transient*> | Eine Komponente erhält jedes Mal, wenn eine Instanz von einer `Transient` Dienst aus dem Dienstcontainer, empfängt er eine *neue Instanz* des Diensts. |
| <xref:Microsoft.Extensions.DependencyInjection.ServiceDescriptor.Scoped*> | Die clientseitige Blazor keine derzeit das Konzept der Dependency Injection-Bereiche. `Scoped` verhält sich wie `Singleton`. ASP.NET Core-Razor-Komponenten unterstützen jedoch die `Scoped` Lebensdauer. In einer Razor-Komponente ist eine Bereichsbezogene dienstregistrierung für die Verbindung beschränkt. Aus diesem Grund verwenden Bereichsbezogene Dienste wird bevorzugt für Dienste, die den aktuellen Benutzer zugeordnet werden soll, auch wenn die aktuelle Absicht ist, führen Sie die clientseitige im Browser. |

Das DI-System basiert auf der in ASP.NET Core DI-System. Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.

## <a name="default-services"></a>Standarddienste

Standarddienste werden automatisch auf der app Service-Auflistung hinzugefügt werden.

| Dienst | Beschreibung |
| ------- | ----------- |
| <xref:System.Net.Http.HttpClient> | Stellt Methoden zum Senden von HTTP-Anforderungen und Empfangen von HTTP-Antworten aus einer Ressource identifiziert, die durch einen URI (Singleton). Beachten Sie, dass diese Instanz von `HttpClient` verwendet der Browser für die Verarbeitung des HTTP-Datenverkehrs im Hintergrund. [HttpClient.BaseAddress](xref:System.Net.Http.HttpClient.BaseAddress) wird automatisch auf der Basis-URI-Präfix der app festgelegt. `HttpClient` wird nur für die clientseitige Blazor apps bereitgestellt werden. |
| `IJSRuntime` | Stellt eine Instanz einer JavaScript-Laufzeit, die an den Aufrufe verteilt werden können. Weitere Informationen finden Sie unter <xref:razor-components/javascript-interop>. |
| `IUriHelper` | Enthält Hilfsmethoden für die Arbeit mit URIs und Navigation-Zustand (Singleton). `IUriHelper` sowohl Blazor und Razor-Komponenten-apps bereitgestellt wird. |

Es ist möglich, einen benutzerdefinierten Anbieter anstelle des Standard-Service-Anbieters hinzugefügt, indem die Standardvorlage zu verwenden. Ein benutzerdefinierter Dienst-Anbieter bereit nicht automatisch die Standarddienste, die in der Tabelle aufgeführten. Wenn Sie benutzerdefinierte Dienstanbieter verwenden und in der Tabelle aufgeführten Dienste erforderlich, fügen Sie die erforderlichen Dienste hinzu, an den neuen Dienstanbieter.

## <a name="request-a-service-in-a-component"></a>Anfordern eines Diensts in einer Komponente

Nach Diensten der Dienstsammlung hinzugefügt werden, fügen Sie die Dienste in der Serverkomponenten Razor-Vorlagen mithilfe der [ @inject ](xref:mvc/views/razor#section-4) Razor-Anweisung. `@inject` hat zwei Parameter:

* Typname: Der Typ des Diensts eingefügt werden soll.
* Name der Eigenschaft: Der Name der Eigenschaft, die den eingefügten app-Dienst empfangen werden soll. Beachten Sie, dass die Eigenschaft keine manuelle Erstellung erfordert. Der Compiler erstellt die-Eigenschaft.

Weitere Informationen finden Sie unter <xref:mvc/views/dependency-injection>.

Verwenden Sie mehrere `@inject` Anweisungen zum Einfügen von verschiedenen Diensten.

Das folgende Beispiel veranschaulicht die Verwendung von `@inject`. Der Dienst implementiert `Services.IDataAccess` wird eingefügt, in der Eigenschaft der Komponente `DataRepository`. Beachten Sie, wie der Code nur nutzt die `IDataAccess` Abstraktion:

[!code-cshtml[](dependency-injection/samples_snapshot/3.x/CustomerList.cshtml?highlight=2-3,23)]

Intern wird die generierte Eigenschaft (`DataRepository`) versehen ist, mit der `InjectAttribute` Attribut. In der Regel wird dieses Attribut nicht direkt verwendet. Wenn eine Basisklasse für Komponenten erforderlich ist, und eingefügte Eigenschaften auch erforderlich, für die Basisklasse sind, `InjectAttribute` manuell hinzugefügt werden können:

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

In Komponenten, die von der Basisklasse abgeleitete der `@inject` Richtlinie ist nicht erforderlich. Die `InjectAttribute` der Basisklasse ist ausreichend:

```cshtml
@page "/demo"
@inherits ComponentBase

<h1>Demo Component</h1>
```

## <a name="dependency-injection-in-services"></a>Abhängigkeitsinjektion in Diensten

Komplexen Dienste müssen möglicherweise zusätzliche Dienste. Im vorherigen Beispiel `DataAccess` möglicherweise die `HttpClient` Standarddienst. `@inject` (oder die `InjectAttribute`) ist nicht verfügbar für die Verwendung in Diensten. *Konstruktorinjektion* muss stattdessen verwendet werden. Die erforderlichen Dienste werden durch Hinzufügen von Parametern zum Konstruktor des Diensts hinzugefügt. Abhängigkeitsinjektion auf den Dienst erstellt, erkennt es die Dienste, die im Konstruktor erfordert und stellt diese entsprechend an.

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

* Es muss einen Konstruktor, deren Argumente alle durch Dependency Injection erfüllt werden können. Beachten Sie, dass zusätzliche Parameter, die nicht durch Dependency Injection abgedeckt zulässig sind, wenn sie Standardwerte angeben.
* Anwendbarer Konstruktor muss *öffentliche*.
* Es muss nur ein anwendbarer Konstruktor vorhanden sein. Im Falle einer Mehrdeutigkeit werden von DI eine Ausnahme ausgelöst.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
