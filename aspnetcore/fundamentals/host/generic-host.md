---
title: Generischer .NET-Host
author: guardrex
description: Erfahren Sie mehr über den generischen Host in ASP.NET Core, der für das Starten der App und das Verwalten der Lebensdauer verantwortlich ist.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/28/2018
uid: fundamentals/host/generic-host
ms.openlocfilehash: 817b7b3b420520992f18f1f207b412bc4555bdfa
ms.sourcegitcommit: a1c43150ed46aa01572399e8aede50d4668745ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58327390"
---
# <a name="net-generic-host"></a>Generischer .NET-Host

Von [Luke Latham](https://github.com/guardrex)

::: moniker range="<= aspnetcore-2.2"

Durch ASP.NET Core-Apps kann ein Host gestartet und konfiguriert werden. Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.

In diesem Artikel wird der generische ASP.NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) erläutert, der für das Hosten von Apps verwendet wird, die keine HTTP-Anforderungen verarbeiten.

Das Ziel des generischen Hosts besteht darin, die HTTP-Pipeline von der Webhost-API zu entkoppeln, um mehr Hostszenarios zu ermöglichen. Messaging, Hintergrundtasks und andere Nicht-HTTP-Workloads, die auf dem generischen Host basieren, profitieren von übergreifenden Funktionen wie der Konfiguration, Dependency Injection (DI) und der Protokollerstellung.

Der generische Host ist neu in ASP.NET Core 2.1 und ist nicht für Webhostingszenarios geeignet. Verwenden Sie den [Webhost](xref:fundamentals/host/web-host) für Webhostingszenarios. Der generische Host wird den Webhost in einem zukünftigen Release ersetzen und als primäre Host-API in HTTP- und Nicht-HTTP-Szenarios fungieren.

::: moniker-end

::: moniker range="> aspnetcore-2.2"

Durch ASP.NET Core-Apps kann ein Host gestartet und konfiguriert werden. Der Host ist verantwortlich für das Starten der App und das Verwalten der Lebensdauer.

In diesem Artikel wird der generische .NET Core-Host (<xref:Microsoft.Extensions.Hosting.HostBuilder>) erläutert.

Der generische Host unterscheidet sich vom Webhost darin, dass er die HTTP-Pipeline von der Webhost-API entkoppelt, um mehr Host-Szenarios zu ermöglichen. Messaging, Hintergrundtasks und andere Nicht-HTTP-Workloads können den generischen Host verwenden und von übergreifenden Funktionen wie der Konfiguration, Dependency Injection (DI) und der Protokollerstellung profitieren.

Ab ASP.NET Core 3.0 wird der generische Host für HTTP- und Nicht-HTTP-Workloads empfohlen. Sofern eingeschlossen wird eine HTTP-Serverimplementierung als eine Implementierung von <xref:Microsoft.Extensions.Hosting.IHostedService> ausgeführt. `IHostedService` ist eine Schnittstelle, die auch für andere Workloads verwendet werden kann.

Der Webhost wird nicht mehr für Web-Apps empfohlen, ist aber aus Gründen der Abwärtskompatibilität weiterhin verfügbar.

> [!NOTE]
> Dieser Rest des Artikels wurde noch nicht für die Version 3.0 aktualisiert.

::: moniker-end

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Wenn Sie die Beispiel-App in [Visual Studio Code](https://code.visualstudio.com/) ausführen, verwenden Sie ein *externes oder integriertes Terminal*. Führen Sie das Beispiel nicht in `internalConsole` aus.

So legen Sie die Konsole in Visual Studio Code fest:

1. Öffnen Sie die Datei *.vscode/launch.json*.
1. Suchen Sie in der Konfiguration **.NET Core-Start (Konsole)** den Eintrag **Konsole**. Legen Sie den Wert auf `externalTerminal` oder `integratedTerminal` fest.

## <a name="introduction"></a>Einführung

Die generische Hostbibliothek ist im Namespace <xref:Microsoft.Extensions.Hosting> verfügbar und wird vom Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting/) bereitgestellt. Das Paket `Microsoft.Extensions.Hosting` ist im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 oder höher) enthalten.

<xref:Microsoft.Extensions.Hosting.IHostedService> ist der Einstiegspunkt für die Ausführung des Codes. Jede Implementierung von `IHostedService` wird in der Reihenfolge der [Dienstregistrierung in ConfigureServices](#configureservices) ausgeführt. <xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*> wird in jeder `IHostedService`-Schnittstelle aufgerufen, wenn der Host gestartet wird, und <xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*> wird in umgekehrter Registrierungsreihenfolge aufgerufen, wenn der Host ordnungsgemäß heruntergefahren wird.

## <a name="set-up-a-host"></a>Einrichten eines Hosts

<xref:Microsoft.Extensions.Hosting.IHostBuilder> ist die Hauptkomponente, die Bibliotheken und Apps für die Initialisierung, Erstellung und Ausführung des Hosts verwenden:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_HostBuilder)]

## <a name="options"></a>Optionen

<xref:Microsoft.Extensions.Hosting.HostOptions>-Konfigurationsoptionen für <xref:Microsoft.Extensions.Hosting.IHost>.

### <a name="shutdown-timeout"></a>Timeout beim Herunterfahren

<xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> legt den Timeout für <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> fest. Der Standardwert beträgt fünf Sekunden.

Die folgende Optionskonfiguration in `Program.Main` erhöht den standardmäßigen Timeout beim Herunterfahren von fünf Sekunden auf 20 Sekunden:

```csharp
var host = new HostBuilder()
    .ConfigureServices((hostContext, services) =>
    {
        services.Configure<HostOptions>(option =>
        {
            option.ShutdownTimeout = System.TimeSpan.FromSeconds(20);
        });
    })
    .Build();
```

## <a name="default-services"></a>Standarddienste

Die folgenden Dienste werden bei der Hostinitialisierung registriert:

* [Umgebung](xref:fundamentals/environments) (<xref:Microsoft.Extensions.Hosting.IHostingEnvironment>)
* <xref:Microsoft.Extensions.Hosting.HostBuilderContext>
* [Konfiguration](xref:fundamentals/configuration/index) (<xref:Microsoft.Extensions.Configuration.IConfiguration>)
* <xref:Microsoft.Extensions.Hosting.IApplicationLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ApplicationLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHostLifetime> (<xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime>)
* <xref:Microsoft.Extensions.Hosting.IHost>
* [Optionen](xref:fundamentals/configuration/options) (<xref:Microsoft.Extensions.DependencyInjection.OptionsServiceCollectionExtensions.AddOptions*>)
* [Protokollierung](xref:fundamentals/logging/index) (<xref:Microsoft.Extensions.DependencyInjection.LoggingServiceCollectionExtensions.AddLogging*>)

## <a name="host-configuration"></a>Konfiguration des Hosts

Die Konfiguration des Hosts wird durch Folgendes erstellt:

* Aufrufen von Erweiterungsmethoden in <xref:Microsoft.Extensions.Hosting.IHostBuilder>, um das [Inhaltsstammverzeichnis](#content-root) und die [Umgebung](#environment) festzulegen
* Lesen der Konfiguration von Konfigurationsanbietern in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureHostConfiguration*>

### <a name="extension-methods"></a>Erweiterungsmethoden

### <a name="application-key-name"></a>Anwendungsschlüssel (Name)

Die Eigenschaft [IHostingEnvironment.ApplicationName](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ApplicationName*) wird von der Hostkonfiguration während der Hostkonstruktion festgelegt. Um den Wert explizit festzulegen, verwenden Sie den [HostDefaults.ApplicationKey](xref:Microsoft.Extensions.Hosting.HostDefaults.ApplicationKey):

**Schlüssel:** Anwendungsname  
**Typ:** *Zeichenfolge*  
**Standardwert**: Der Name der Assembly, die den Einstiegspunkt der App enthält.  
**Festlegen mit:** `HostBuilderContext.HostingEnvironment.ApplicationName`  
**Umgebungsvariable:** `<PREFIX_>APPLICATIONNAME` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))

### <a name="content-root"></a>Inhaltsstammverzeichnis

Diese Einstellung bestimmt, wo der Host mit der Suche nach Inhaltsdateien beginnt.

**Schlüssel:** contentRoot  
**Typ:** *Zeichenfolge*  
**Standardwert**: Entspricht standardmäßig dem Ordner, in dem die App-Assembly gespeichert ist.  
**Festlegen mit:** `UseContentRoot`  
**Umgebungsvariable:** `<PREFIX_>CONTENTROOT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))

Wenn der Pfad nicht vorhanden ist, kann der Host nicht gestartet werden.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseContentRoot)]

### <a name="environment"></a>Umgebung

Legt die [Umgebung](xref:fundamentals/environments) der App fest.

**Schlüssel:** environment  
**Typ:** *Zeichenfolge*  
**Standardwert**: Produktion  
**Festlegen mit:** `UseEnvironment`  
**Umgebungsvariable:** `<PREFIX_>ENVIRONMENT` (`<PREFIX_>` ist [optional und benutzerdefiniert](#configurehostconfiguration))

Die Umgebung kann auf einen beliebigen Wert festgelegt werden. Zu den durch Frameworks definierten Werten zählen `Development`, `Staging` und `Production`. Die Groß-/Kleinschreibung wird bei Werten nicht beachtet.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseEnvironment)]

### <a name="configurehostconfiguration"></a>ConfigureHostConfiguration

`ConfigureHostConfiguration` verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für den Host zu erstellen. Die Hostkonfiguration dient zum Initialisieren der <xref:Microsoft.Extensions.Hosting.IHostingEnvironment> für die Verwendung im App-Buildprozesses.

`ConfigureHostConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden. Der Host verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt.

Die Hostkonfiguration wird automatisch auf die App-Konfiguration übertragen ([ConfigureAppConfiguration](#configureappconfiguration) und den Rest der App).

Standardmäßig sind keine Anbieter enthalten. Sie müssen explizit angeben, welche Konfigurationsanbieter die App in `ConfigureHostConfiguration` erfordert, einschließlich Folgendes:

* Dateikonfiguration (z.B. von einer Datei namens *hostsettings.json*)
* Konfiguration von Umgebungsvariablen
* Konfiguration von Befehlszeilenargumenten
* Alle anderen erforderlichen Konfigurationsanbieter

Die Dateikonfiguration des Hosts erfolgt durch Angabe des App-Basispfads mit `SetBasePath` gefolgt von einem Aufruf eines der [Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider). Die Beispiel-App verwendet die JSON-Datei *hostsettings.json* und ruft <xref:Microsoft.Extensions.Configuration.JsonConfigurationExtensions.AddJsonFile*> auf, um die Hostkonfigurationseinstellungen der Datei zu nutzen.

Um die [Umgebungsvariablenkonfiguration](xref:fundamentals/configuration/index#environment-variables-configuration-provider) des Hosts hinzuzufügen, rufen Sie <xref:Microsoft.Extensions.Configuration.EnvironmentVariablesExtensions.AddEnvironmentVariables*> im Host-Generator auf. `AddEnvironmentVariables` akzeptiert ein optionales benutzerdefiniertes Präfix. Die Beispiel-App verwendet das Präfix `PREFIX_`. Das Präfix wird entfernt, wenn die Umgebungsvariablen gelesen werden. Wenn der Host der Beispiel-App konfiguriert wird, wird der Wert der Umgebungsvariable für `PREFIX_ENVIRONMENT` der Hostkonfigurationswert für den `environment`-Schlüssel.

Wenn Sie [Visual Studio](https://www.visualstudio.com/) oder eine App mit `dotnet run` während der Entwicklung verwenden, können Umgebungsvariablen in der Datei *Properties/launchSettings.json* festgelegt werden. In [Visual Studio Code](https://code.visualstudio.com/) können Umgebungsvariablen während der Entwicklung in der Datei *.vscode/launch.json* festgelegt werden. Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

Die [Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) wird durch Aufrufen von <xref:Microsoft.Extensions.Configuration.CommandLineConfigurationExtensions.AddCommandLine*> hinzugefügt. Die Befehlszeilenkonfiguration wird zuletzt hinzugefügt, damit Befehlszeilenargumente die Konfiguration überschreiben können, die von früheren Konfigurationsanbietern bereitgestellt wurden.

*hostsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/hostsettings.json)]

Weitere Konfigurationen können durch die Schlüssel [applicationName](#application-key-name) und [contentRoot](#content-root) bereitgestellt werden.

Beispielkonfiguration für `HostBuilder` mithilfe von `ConfigureHostConfiguration`:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureHostConfiguration)]

## <a name="configureappconfiguration"></a>ConfigureAppConfiguration

Die App-Konfiguration wird durch Aufrufen von <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> in der <xref:Microsoft.Extensions.Hosting.IHostBuilder>-Implementierung erstellt. `ConfigureAppConfiguration` verwendet einen <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder>, um eine <xref:Microsoft.Extensions.Configuration.IConfiguration> für die App zu erstellen. `ConfigureAppConfiguration` kann mehrfach mit additiven Ergebnissen aufgerufen werden. Die App verwendet die Option, die zuletzt einen Wert für einen bestimmten Schlüssel festlegt. Die von `ConfigureAppConfiguration` erstellte Konfiguration ist unter [HostBuilderContext.Configuration](xref:Microsoft.Extensions.Hosting.HostBuilderContext.Configuration*) verfügbar und kann für nachfolgende Vorgänge und in <xref:Microsoft.Extensions.Hosting.IHost.Services*> verwendet werden.

Auf die App-Konfiguration wird automatisch die Hostkonfiguration angewendet, die von [ConfigureHostConfiguration](#configurehostconfiguration) bereitgestellt wird.

Beispielkonfiguration für die App mithilfe von `ConfigureAppConfiguration`:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureAppConfiguration)]

*appsettings.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.json)]

*appsettings.Development.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Development.json)]

*appsettings.Production.json*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/appsettings.Production.json)]

Um Einstellungsdateien in das Ausgabeverzeichnis zu verschieben, geben Sie die Einstellungsdateien als [MSBuild-Projektelemente](/visualstudio/msbuild/common-msbuild-project-items) in der Projektdatei an. Die Beispiel-App verschiebt ihre JSON-App-Einstellungsdateien und *hostsettings.json* mit dem folgenden `<Content>`-Element:

```xml
<ItemGroup>
  <Content Include="**\*.json" Exclude="bin\**\*;obj\**\*" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

## <a name="configureservices"></a>ConfigureServices

Mithilfe von <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureServices*> werden Dienste zum Container [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) der App hinzugefügt. `ConfigureServices` kann mehrfach mit additiven Ergebnissen aufgerufen werden.

Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert. Weitere Informationen finden Sie unter <xref:fundamentals/host/hosted-services>.

Die [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Erweiterungsmethode `AddHostedService`, um einen Dienst für Lebensdauerereignisse (`LifetimeEventsHostedService`) und einen zeitgesteuerten Hintergrundtask (`TimedHostedService`) zur App hinzuzufügen:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureServices)]

## <a name="configurelogging"></a>ConfigureLogging

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.ConfigureLogging*> fügt einen Delegaten für die Konfiguration des bereitgestellten <xref:Microsoft.Extensions.Logging.ILoggingBuilder> hinzu. `ConfigureLogging` kann mehrfach mit additiven Ergebnissen aufgerufen werden.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ConfigureLogging)]

### <a name="useconsolelifetime"></a>UseConsoleLifetime

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseConsoleLifetime*> wartet auf `Ctrl+C`/SIGINT oder SIGTERM und ruft <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> auf, um das Herunterfahren zu beginnen. `UseConsoleLifetime` hebt die Blockierung von Erweiterungen wie [RunAsync](#runasync) und [WaitForShutdownAsync](#waitforshutdownasync) auf. <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> ist vorab als Standardimplementierung für die Lebensdauer registriert. Die letzte registrierte Lebensdauer wird verwendet.

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_UseConsoleLifetime)]

## <a name="container-configuration"></a>Containerkonfiguration

Der Host kann eine <xref:Microsoft.Extensions.DependencyInjection.IServiceProviderFactory%601>-Schnittstelle verwenden, um die Integration in andere Container zu unterstützten. Das Bereitstellen einer Factory ist kein Teil der DI-Containerregistrierung, sondern ein Host, der systemintern verwendet wird, um den entsprechenden DI-Container zu erstellen. [UseServiceProviderFactory(IServiceProviderFactory&lt;TContainerBuilder&gt;)](xref:Microsoft.Extensions.Hosting.HostBuilder.UseServiceProviderFactory*) überschreibt die Standardfactory, die zum Erstellen des Dienstanbieters der App verwendet wurde.

Die benutzerdefinierte Containerkonfiguration wird von der Methode <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureContainer*> verwaltet. `ConfigureContainer` stellt eine stark typisierte Möglichkeit für das Konfigurieren des Containers auf Basis der zugrunde liegenden Host-API bereit. `ConfigureContainer` kann mehrfach mit additiven Ergebnissen aufgerufen werden.

Erstellen eines Dienstcontainers für die App:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainer.cs)]

Bereitstellen einer Factory für den Dienstcontainer:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/ServiceContainerFactory.cs)]

Verwenden Sie die Factory, und konfigurieren Sie den benutzerdefinierten Dienstcontainer für die App:

[!code-csharp[](generic-host/samples-snapshot/2.x/GenericHostSample/Program.cs?name=snippet_ContainerConfiguration)]

## <a name="extensibility"></a>Erweiterungen

Die Erweiterung des Hosts wird mit der Erweiterungsmethode in `IHostBuilder` durchgeführt. Im folgenden Beispiel wird dargestellt, wie eine Erweiterungsmethode eine `IHostBuilder`-Implementierung mit dem [TimedHostedService](xref:fundamentals/host/hosted-services#timed-background-tasks)-Beispiel erweitert, das in <xref:fundamentals/host/hosted-services> gezeigt wird.

```csharp
var host = new HostBuilder()
    .UseHostedService<TimedHostedService>()
    .Build();

await host.StartAsync();
```

Eine App richtet die `UseHostedService`-Erweiterungsmethode zum Registrieren des in `T` übergebenen gehosteten Diensts ein:

```csharp
using System;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

public static class Extensions
{
    public static IHostBuilder UseHostedService<T>(this IHostBuilder hostBuilder)
        where T : class, IHostedService, IDisposable
    {
        return hostBuilder.ConfigureServices(services =>
            services.AddHostedService<T>());
    }
}
```

## <a name="manage-the-host"></a>Verwalten des Hosts

Die Implementierung von <xref:Microsoft.Extensions.Hosting.IHost> ist für das Starten und Beenden der Implementierungen von `IHostedService` verantwortlich, die im Dienstcontainer registriert sind.

### <a name="run"></a>Run

Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Run*> wird die App gestartet und der aufrufende Thread blockiert, bis der Host heruntergefahren wird:

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        host.Run();
    }
}
```

### <a name="runasync"></a>RunAsync

Durch <xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.RunAsync*> wird die App ausgeführt und eine `Task`-Methode ausgegeben, die abgeschlossen wird, wenn das Abbruchtoken oder das Herunterfahren ausgelöst wird:

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        await host.RunAsync();
    }
}
```

### <a name="runconsoleasync"></a>RunConsoleAsync

<xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.RunConsoleAsync*> aktiviert die Unterstützung der Konsole, erstellt und startet den Host und wartet auf `Ctrl+C`/SIGINT oder SIGTERM, um das Herunterfahren auszulösen.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var hostBuilder = new HostBuilder();

        await hostBuilder.RunConsoleAsync();
    }
}
```

### <a name="start-and-stopasync"></a>Start und StopAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.Start*> startet den Host synchron.

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.StopAsync*> versucht, den Host innerhalb des angegebenen Timeouts zu beenden.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            await host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

### <a name="startasync-and-stopasync"></a>StartAsync und StopAsync

<xref:Microsoft.Extensions.Hosting.IHost.StartAsync*> startet die App.

<xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> hält die App an.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.StopAsync();
        }
    }
}
```

### <a name="waitforshutdown"></a>WaitForShutdown

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdown*> wird über die <xref:Microsoft.Extensions.Hosting.IHostLifetime> ausgelöst, z.B. <xref:Microsoft.Extensions.Hosting.Internal.ConsoleLifetime> (lauscht auf `Ctrl+C`/SIGINT oder SIGTERM). `WaitForShutdown` ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.

```csharp
public class Program
{
    public void Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            host.Start();

            host.WaitForShutdown();
        }
    }
}
```

### <a name="waitforshutdownasync"></a>WaitForShutdownAsync

<xref:Microsoft.Extensions.Hosting.HostingAbstractionsHostExtensions.WaitForShutdownAsync*> gibt eine `Task`-Methode zurück, die abgeschlossen wird, wenn das Herunterfahren über das angegebene Token ausgelöst wird, und ruft <xref:Microsoft.Extensions.Hosting.IHost.StopAsync*> auf.

```csharp
public class Program
{
    public static async Task Main(string[] args)
    {
        var host = new HostBuilder()
            .Build();

        using (host)
        {
            await host.StartAsync();

            await host.WaitForShutdownAsync();
        }

    }
}
```

### <a name="external-control"></a>Externe Steuerung

Die externe Steuerung des Hosts kann mithilfe von Methoden durchgeführt werden, die extern aufgerufen werden können:

```csharp
public class Program
{
    private IHost _host;

    public Program()
    {
        _host = new HostBuilder()
            .Build();
    }

    public async Task StartAsync()
    {
        _host.StartAsync();
    }

    public async Task StopAsync()
    {
        using (_host)
        {
            await _host.StopAsync(TimeSpan.FromSeconds(5));
        }
    }
}
```

<xref:Microsoft.Extensions.Hosting.IHostLifetime.WaitForStartAsync*> wird am Anfang der <xref:Microsoft.Extensions.Hosting.IHost.StartAsync*>-Methode aufgerufen, die den Vorgang erst fortsetzt, wenn sie abgeschlossen ist. Dies kann verwendet werden, um den Start zu verzögern, bis dieser durch ein externes Ereignis initiiert wird.

## <a name="ihostingenvironment-interface"></a>IHostingEnvironment-Schnittstelle

<xref:Microsoft.Extensions.Hosting.IHostingEnvironment> stellt Informationen über die Hostingumgebung der App bereit. Verwenden Sie [Constructor Injection](xref:fundamentals/dependency-injection) zum Abrufen der `IHostingEnvironment`-Schnittstelle, um deren Eigenschaften und Erweiterungsmethoden zu verwenden:

```csharp
public class MyClass
{
    private readonly IHostingEnvironment _env;

    public MyClass(IHostingEnvironment env)
    {
        _env = env;
    }

    public void DoSomething()
    {
        var environmentName = _env.EnvironmentName;
    }
}
```

Weitere Informationen finden Sie unter <xref:fundamentals/environments>.

## <a name="iapplicationlifetime-interface"></a>IApplicationLifetime-Schnittstelle

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime> ermöglicht Aktivitäten nach dem Starten und beim Herunterfahren (einschließlich Anforderungen für ordnungsgemäßes Herunterfahren). Bei drei der Eigenschaften der Schnittstelle handelt es sich um Abbruchtokens, die zum Registrieren von `Action`-Methoden verwendet werden, durch die Ereignisse beim Starten und Herunterfahren definiert werden.

| Abbruchtoken | Wird ausgelöst, wenn&#8230; |
| ------------------ | --------------------- |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStarted*> | Der Host vollständig gestartet wurde. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopped*> | Der Host ein ordnungsgemäßes Herunterfahren abschließt. Alle Anforderungen sollten verarbeitet worden sein. Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist. |
| <xref:Microsoft.Extensions.Hosting.IApplicationLifetime.ApplicationStopping*> | Der Host ein ordnungsgemäßes Herunterfahren ausführt. Anforderungen möglicherweise noch verarbeitet werden. Das Herunterfahren wird blockiert, bis das Ereignis abgeschlossen ist. |

Fügen Sie den `IApplicationLifetime`-Dienst über einen Konstruktor in eine beliebige Klasse ein. Die [Beispiel-App](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/host/generic-host/samples/) verwendet die Konstruktorinjektion für eine `LifetimeEventsHostedService`-Klasse (eine `IHostedService`-Implementierung), um die Ereignisse zu registrieren.

*LifetimeEventsHostedService.cs*:

[!code-csharp[](generic-host/samples/2.x/GenericHostSample/LifetimeEventsHostedService.cs?name=snippet1)]

<xref:Microsoft.Extensions.Hosting.IApplicationLifetime.StopApplication*> fordert das Beenden der App an. Die folgende Klasse verwendet `StopApplication`, um eine App ordnungsgemäß herunterzufahren, wenn die `Shutdown`-Methode der Klasse aufgerufen wird:

```csharp
public class MyClass
{
    private readonly IApplicationLifetime _appLifetime;

    public MyClass(IApplicationLifetime appLifetime)
    {
        _appLifetime = appLifetime;
    }

    public void Shutdown()
    {
        _appLifetime.StopApplication();
    }
}
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/host/hosted-services>
* [Hosten von Beispielrepositorys auf GitHub](https://github.com/aspnet/Hosting/tree/release/2.1/samples)
