---
title: Integritätsprüfungen in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Integritätsprüfungen für ASP.NET Core-Infrastrukturelemente wie Apps und Datenbanken einrichten.
monikerRange: '>= aspnetcore-2.2'
ms.author: riande
ms.custom: mvc
ms.date: 09/27/2019
uid: host-and-deploy/health-checks
ms.openlocfilehash: e4b2a577815335078f7e0c9128144a514e42a6c3
ms.sourcegitcommit: 5d25a7f22c50ca6fdd0f8ecd8e525822e1b35b7a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2019
ms.locfileid: "71482052"
---
# <a name="health-checks-in-aspnet-core"></a><span data-ttu-id="7a2c2-103">Integritätsprüfungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7a2c2-103">Health checks in ASP.NET Core</span></span>

<span data-ttu-id="7a2c2-104">Von [Luke Latham](https://github.com/guardrex) und [Glenn Condron](https://github.com/glennc)</span><span class="sxs-lookup"><span data-stu-id="7a2c2-104">By [Luke Latham](https://github.com/guardrex) and [Glenn Condron](https://github.com/glennc)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7a2c2-105">ASP.NET Core bietet Middleware für Integritätsprüfungen und Bibliotheken für die Berichterstellung für Komponenten der App-Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-105">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="7a2c2-106">Integritätsprüfungen werden von einer App als HTTP-Endpunkte verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-106">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="7a2c2-107">Integritätsprüfungs-Endpunkte können für eine Vielzahl von Echtzeit-Überwachungsszenarien konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-107">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="7a2c2-108">Integritätstests können von Containerorchestratoren und Lastenausgleichsmodulen verwendet werden, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-108">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="7a2c2-109">Ein Containerorchestrator kann z.B. auf eine fehlerhafte Integritätsprüfung reagieren, indem die Ausführung einer Bereitstellung angehalten oder ein Container neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-109">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="7a2c2-110">Ein Lastenausgleichsmodul kann auf eine fehlerhafte App reagieren, indem Datenverkehr von der fehlerhaften zu einer fehlerfreien Instanz umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-110">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="7a2c2-111">Die Nutzung von Arbeitsspeicher, Datenträgern und anderen physischen Serverressourcen kann auf einen fehlerfreien Status überwacht werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-111">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="7a2c2-112">Integritätsprüfungen können die Abhängigkeiten einer App testen, wie z.B. Datenbanken und externe Dienstendpunkte, um Verfügbarkeit und normale Funktionsweise zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-112">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="7a2c2-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7a2c2-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7a2c2-114">Die Beispiel-App enthält Beispiele der Szenarien, die in diesem Thema beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-114">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="7a2c2-115">Um die Beispiel-App für ein bestimmtes Szenario auszuführen, verwenden Sie den Befehl [dotnet run](/dotnet/core/tools/dotnet-run) aus dem Ordner des Projekts in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-115">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="7a2c2-116">Informationen zur Verwendung der Beispiel-App finden Sie in der Datei *README.md* der Beispiel-App und den Szenariobeschreibungen in diesem Thema.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-116">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a2c2-117">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="7a2c2-117">Prerequisites</span></span>

<span data-ttu-id="7a2c2-118">Integritätsprüfungen werden in der Regel mit einem externen Überwachungsdienst oder Containerorchestrator verwendet, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-118">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="7a2c2-119">Bevor Sie Integritätsprüfungen zu einer App hinzufügen, entscheiden Sie, welches Überwachungssystem verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-119">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="7a2c2-120">Das Überwachungssystem bestimmt, welche Arten von Integritätsprüfungen erstellt und wie die jeweiligen Endpunkte konfiguriert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-120">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="7a2c2-121">Fügen Sie einen Paketverweis auf das [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks)-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-121">Add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span> <span data-ttu-id="7a2c2-122">Fügen Sie zum Durchführen von Integritätsprüfungen mit Entity Framework Core einen Paketverweis auf das [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore)-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-122">To perform health checks using Entity Framework Core, add a package reference to the [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore) package.</span></span>

<span data-ttu-id="7a2c2-123">Die Beispiel-App stellt Startcode bereit, um die Integritätsprüfungen für verschiedene Szenarien zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-123">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="7a2c2-124">Das Szenario [Datenbanktest](#database-probe) überprüft die Integrität einer Datenbankverbindung mithilfe von [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-124">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="7a2c2-125">Das Szenario [DbContext-Test](#entity-framework-core-dbcontext-probe) überprüft eine Datenbank mithilfe eines EF Core-`DbContext`-Elements.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-125">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="7a2c2-126">Für ein Erkunden der Datenbankszenarios gilt für die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-126">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="7a2c2-127">Sie erstellt eine Datenbank und stellt deren Verbindungszeichenfolge in der Datei *appsettings.json* bereit.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-127">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="7a2c2-128">Sie hat die folgenden Paketverweise in ihrer Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-128">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="7a2c2-129">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7a2c2-129">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="7a2c2-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7a2c2-130">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="7a2c2-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-131">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="7a2c2-132">Ein anderes Szenario für Integritätsprüfungen zeigt, wie Sie Integritätsprüfungen auf einen Verwaltungsport filtern können.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-132">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="7a2c2-133">Für die Beispiel-App müssen Sie eine *Properties/launchSettings.json*-Datei erstellen, die die Verwaltungs-URL und den Verwaltungsport enthält.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-133">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="7a2c2-134">Weitere Informationen finden Sie im Abschnitt [Filtern nach Port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-134">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="7a2c2-135">Grundlegender Integritätstest</span><span class="sxs-lookup"><span data-stu-id="7a2c2-135">Basic health probe</span></span>

<span data-ttu-id="7a2c2-136">In vielen Apps genügt zur Ermittlung des App-Status eine grundlegende Integritätstestkonfiguration, die die Verfügbarkeit der App für die Verarbeitung von Anforderungen (*Lebendigkeit*) meldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-136">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="7a2c2-137">Die grundlegende Konfiguration registriert Integritätsprüfungsdienste und ruft die Middleware für Integritätsprüfungen auf, damit diese an einem URL-Endpunkt mit dem Integritätsstatus antwortet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-137">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="7a2c2-138">Standardmäßig werden keine spezifischen Integritätsprüfungen registriert, um eine bestimmte Abhängigkeit oder ein bestimmtes Subsystem zu testen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-138">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="7a2c2-139">Die App wird als fehlerfrei angesehen, wenn sie an der URL des Integritätsendpunkts antworten kann.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-139">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="7a2c2-140">Der standardmäßige Antwortwriter schreibt den Status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) als Klartextantwort zurück an den Client und gibt den Status als [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) oder [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) an.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-140">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="7a2c2-141">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-141">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-142">Erstellen Sie einen Integritätsprüfungs-Endpunkt durch Aufrufen von `MapHealthChecks` in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-142">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="7a2c2-143">In der Beispiel-App wird der Integritätsprüfungs-Endpunkt in `/health` (*BasicStartup.cs*) erstellt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-143">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseRouting();

        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHealthChecks("/health");
        });
    }
}
```

<span data-ttu-id="7a2c2-144">Um das Szenario für die grundlegende Konfiguration mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-144">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="7a2c2-145">Docker-Beispiel</span><span class="sxs-lookup"><span data-stu-id="7a2c2-145">Docker example</span></span>

<span data-ttu-id="7a2c2-146">[Docker](xref:host-and-deploy/docker/index) bietet eine integrierte `HEALTHCHECK`-Direktive, mit der Sie den Status einer App überprüfen können, die die grundlegende Konfiguration für Integritätsprüfungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-146">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="7a2c2-147">Erstellen von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-147">Create health checks</span></span>

<span data-ttu-id="7a2c2-148">Integritätsprüfungen werden durch Implementieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle erstellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-148">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="7a2c2-149">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>-Methode gibt ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurück, das die Integrität als `Healthy`, `Degraded` oder `Unhealthy` angibt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-149">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="7a2c2-150">Das Ergebnis wird als Klartextantwort mit einem konfigurierbaren Statuscode geschrieben (diese Konfiguration wird im Abschnitt [Optionen für die Integritätsprüfung](#health-check-options) beschrieben).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-150">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="7a2c2-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> kann auch optionale Schlüssel-Wert-Paare zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-151"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

<span data-ttu-id="7a2c2-152">Die folgende `ExampleHealthCheck`-Klasse veranschaulicht das Layout einer Integritätsprüfung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-152">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="7a2c2-153">Die Integritätsprüfungslogik wird in der `CheckHealthAsync`-Methode platziert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-153">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="7a2c2-154">Im folgenden Beispiel wird eine Dummyvariable `healthCheckResultHealthy` auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-154">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="7a2c2-155">Wenn der Wert von `healthCheckResultHealthy` auf `false` festgelegt ist, wird der [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)-Status zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-155">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("A healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("An unhealthy result."));
    }
}
```

## <a name="register-health-check-services"></a><span data-ttu-id="7a2c2-156">Registrieren von Integritätsprüfungsdiensten</span><span class="sxs-lookup"><span data-stu-id="7a2c2-156">Register health check services</span></span>

<span data-ttu-id="7a2c2-157">Der `ExampleHealthCheck`-Typ wird mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` den Integritätsprüfungsdiensten hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-157">The `ExampleHealthCheck` type is added to health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="7a2c2-158">Die in der folgenden Abbildung gezeigte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>-Überladung legt den Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) fest, der angegeben werden soll, wenn die Integritätsprüfung einen Fehler meldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-158">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="7a2c2-159">Wenn der Fehlerstatus auf `null` (Standardwert) festgelegt ist, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) gemeldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-159">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="7a2c2-160">Diese Überladung ist ein nützliches Szenario für Bibliotheksersteller: Bei einem Integritätsprüfungsfehler wird der durch die Bibliothek angegebene Fehlerstatus von der App erzwungen, wenn die Implementierung der Integritätsprüfung die Einstellung berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-160">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="7a2c2-161">*Tags* können zum Filtern von Integritätsprüfungen verwendet werden (dies wird im Abschnitt [Filtern von Integritätsprüfungen](#filter-health-checks) genauer beschrieben).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-161">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="7a2c2-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> kann auch eine Lambdafunktion ausführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-162"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="7a2c2-163">Im folgenden Beispiel wird der Name der Integritätsprüfung als `Example` angegeben, und die Prüfung gibt immer einen fehlerfreien Status zurück:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-163">In the following example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

## <a name="use-health-checks-routing"></a><span data-ttu-id="7a2c2-164">Verwenden von Integritätsprüfungsrouting</span><span class="sxs-lookup"><span data-stu-id="7a2c2-164">Use Health Checks Routing</span></span>

<span data-ttu-id="7a2c2-165">Rufen Sie in `Startup.Configure` auf der Endpunkterstellung mit der Endpunkt-URL oder dem relativen Pfad `MapHealthChecks` auf:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-165">In `Startup.Configure`, call `MapHealthChecks` on the endpoint builder with the endpoint URL or relative path:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
});
```

### <a name="require-host"></a><span data-ttu-id="7a2c2-166">Anfordern des Hosts</span><span class="sxs-lookup"><span data-stu-id="7a2c2-166">Require host</span></span>

<span data-ttu-id="7a2c2-167">Rufen Sie `RequireHost` auf, um einen oder mehrere zugelassene Hosts für den Integritätsprüfungs-Endpunkt anzugeben.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-167">Call `RequireHost` to specify one or more permitted hosts for the health check endpoint.</span></span> <span data-ttu-id="7a2c2-168">Hosts sollten Unicode anstelle von Punycode verwenden und können einen Port enthalten.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-168">Hosts should be Unicode rather than punycode and may include a port.</span></span> <span data-ttu-id="7a2c2-169">Wenn keine Sammlung bereitgestellt wird, wird jeder Host akzeptiert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-169">If a collection isn't supplied, any host is accepted.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireHost("www.contoso.com:5001");
});
```

<span data-ttu-id="7a2c2-170">Weitere Informationen finden Sie im Abschnitt [Filtern nach Port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-170">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

### <a name="require-authorization"></a><span data-ttu-id="7a2c2-171">Anfordern der Autorisierung</span><span class="sxs-lookup"><span data-stu-id="7a2c2-171">Require authorization</span></span>

<span data-ttu-id="7a2c2-172">Rufen Sie `RequireAuthorization` auf, um die Autorisierungsmiddleware auf dem Integritätsprüfungs-Anforderungsendpunkt auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-172">Call `RequireAuthorization` to run Authorization Middleware on the health check request endpoint.</span></span> <span data-ttu-id="7a2c2-173">Eine `RequireAuthorization`-Überladung akzeptiert eine oder mehrere Autorisierungsrichtlinien.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-173">A `RequireAuthorization` overload accepts one or more authorization policies.</span></span> <span data-ttu-id="7a2c2-174">Wenn keine Richtlinie bereitgestellt wird, wird die standardmäßige Autorisierungsrichtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-174">If a policy isn't provided, the default authorization policy is used.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health").RequireAuthorization();
});
```

### <a name="enable-cross-origin-requests-cors"></a><span data-ttu-id="7a2c2-175">Aktivieren ursprungsübergreifender Anforderungen (CORS)</span><span class="sxs-lookup"><span data-stu-id="7a2c2-175">Enable Cross-Origin Requests (CORS)</span></span>

<span data-ttu-id="7a2c2-176">Obwohl das manuelle Ausführen von Integritätsprüfungen über einen Browser kein gängiges Szenario ist, kann CORS-Middleware durch Aufrufen von `RequireCors` für Integritätsprüfungs-Endpunkte aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-176">Although performing health checks manually from a browser isn't a common use scenario, CORS Middleware can be enabled by calling `RequireCors` on health checks endpoints.</span></span> <span data-ttu-id="7a2c2-177">Eine `RequireCors`-Überladung akzeptiert einen CORS-Richtliniengenerator-Delegaten (`CorsPolicyBuilder`) oder einen Richtliniennamen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-177">A `RequireCors` overload accepts a CORS policy builder delegate (`CorsPolicyBuilder`) or a policy name.</span></span> <span data-ttu-id="7a2c2-178">Wenn keine Richtlinie bereitgestellt wird, wird die standardmäßige CORS-Richtlinie verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-178">If a policy isn't provided, the default CORS policy is used.</span></span> <span data-ttu-id="7a2c2-179">Weitere Informationen finden Sie unter <xref:security/cors>.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-179">For more information, see <xref:security/cors>.</span></span>

## <a name="health-check-options"></a><span data-ttu-id="7a2c2-180">Optionen für die Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="7a2c2-180">Health check options</span></span>

<span data-ttu-id="7a2c2-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> ermöglichen die Anpassung des Verhaltens von Integritätsprüfungen:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-181"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="7a2c2-182">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-182">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="7a2c2-183">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="7a2c2-183">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="7a2c2-184">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="7a2c2-184">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="7a2c2-185">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="7a2c2-185">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="7a2c2-186">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-186">Filter health checks</span></span>

<span data-ttu-id="7a2c2-187">Standardmäßig führt die Middleware für Integritätsprüfungen alle registrierten Integritätsprüfungen aus.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-187">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="7a2c2-188">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die einen booleschen Wert an die Option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-188">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="7a2c2-189">Im folgenden Beispiel wird die `Bar`-Integritätsprüfung anhand ihres Tags (`bar_tag`) in der Bedingungsanweisung der Funktion herausgefiltert. Dabei wird `true` nur zurückgegeben, wenn die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags>-Eigenschaft der Integritätsprüfung mit `foo_tag` oder `baz_tag` übereinstimmt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-189">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

<span data-ttu-id="7a2c2-190">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-190">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Foo", () =>
        HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
    .AddCheck("Bar", () =>
        HealthCheckResult.Unhealthy("Bar is unhealthy!"), tags: new[] { "bar_tag" })
    .AddCheck("Baz", () =>
        HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
```

<span data-ttu-id="7a2c2-191">In `Startup.Configure` filtert `Predicate` die „Bar“-Integritätsprüfung heraus.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-191">In `Startup.Configure`, the `Predicate` filters out the 'Bar' health check.</span></span> <span data-ttu-id="7a2c2-192">Nur Foo und Baz werden ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-192">Only Foo and Baz execute.:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
});
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="7a2c2-193">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="7a2c2-193">Customize the HTTP status code</span></span>

<span data-ttu-id="7a2c2-194">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>, um die Zuordnung des Integritätsstatus zu HTTP-Statuscodes anzupassen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-194">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="7a2c2-195">Die folgenden <xref:Microsoft.AspNetCore.Http.StatusCodes>-Zuweisungen stellen die von der Middleware verwendeten Standardwerte dar.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-195">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="7a2c2-196">Ändern Sie die Statuscodewerte so, dass sie Ihren Anforderungen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-196">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="7a2c2-197">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-197">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResultStatusCodes =
        {
            [HealthStatus.Healthy] = StatusCodes.Status200OK,
            [HealthStatus.Degraded] = StatusCodes.Status200OK,
            [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
        }
    });
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="7a2c2-198">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="7a2c2-198">Suppress cache headers</span></span>

<span data-ttu-id="7a2c2-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> steuert, ob die Middleware für Integritätsprüfungen einer Testantwort HTTP-Header hinzufügt, um das Zwischenspeichern von Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-199"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="7a2c2-200">Wenn der Wert `false` (Standard) lautet, legt die Middleware die Header `Cache-Control`, `Expires` und `Pragma` fest oder überschreibt sie, um eine Zwischenspeicherung der Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-200">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="7a2c2-201">Wenn der Wert `true` lautet, ändert die Middleware die Cacheheader der Antwort nicht.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-201">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="7a2c2-202">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-202">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        AllowCachingResponses = false
    });
});
```

### <a name="customize-output"></a><span data-ttu-id="7a2c2-203">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="7a2c2-203">Customize output</span></span>

<span data-ttu-id="7a2c2-204">Mit der Option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> wird ein Delegat abgerufen oder festgelegt, der zum Schreiben der Antwort verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-204">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span>

<span data-ttu-id="7a2c2-205">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-205">In `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
});
```

<span data-ttu-id="7a2c2-206">Der Standarddelegat schreibt eine minimale Klartextantwort mit dem Zeichenfolgenwert [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-206">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="7a2c2-207">Der folgende benutzerdefinierte Delegat, `WriteResponse`, gibt eine benutzerdefinierte JSON-Antwort aus:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-207">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="7a2c2-208">Das Integritätsprüfungssystem bietet keine integrierte Unterstützung für komplexe JSON-Rückgabeformate, da das Format für Ihre Überwachungssystemauswahl spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-208">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="7a2c2-209">Sie können das `JObject` im vorherigen Beispiel nach Bedarf anpassen, sodass Ihre Anforderungen erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-209">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="7a2c2-210">Datenbanktest</span><span class="sxs-lookup"><span data-stu-id="7a2c2-210">Database probe</span></span>

<span data-ttu-id="7a2c2-211">Eine Integritätsprüfung kann eine Datenbankabfrage angeben, die als boolescher Test ausgeführt wird, um zu ermitteln, ob die Datenbank normal reagiert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-211">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="7a2c2-212">Die Beispiel-App verwendet [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), eine Bibliothek für Integritätsprüfungen für ASP.NET Core-Apps, um eine Integritätsprüfung für eine SQL Server-Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-212">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="7a2c2-213">`AspNetCore.Diagnostics.HealthChecks` führt eine `SELECT 1`-Abfrage in der Datenbank aus, um zu bestätigen, dass die Verbindung mit der Datenbank fehlerfrei ist.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-213">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="7a2c2-214">Wenn Sie die Datenbankverbindung mithilfe einer Abfrage überprüfen, wählen Sie eine Abfrage aus, die eine schnelle Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-214">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="7a2c2-215">Bei einer Abfrage besteht immer das Risiko, dass die Datenbank überladen und ihre Leistung beeinträchtigt wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-215">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="7a2c2-216">In den meisten Fällen ist es nicht notwendig, eine Testabfrage auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-216">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="7a2c2-217">Es genügt zumeist, einfach erfolgreich eine Verbindung mit der Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-217">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="7a2c2-218">Wenn Sie eine Abfrage ausführen müssen, wählen Sie eine einfache SELECT-Abfrage aus, wie z.B. `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-218">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="7a2c2-219">Fügen Sie einen Paketverweis auf [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-219">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="7a2c2-220">Geben Sie in der Datei *appsettings.json* der App eine gültige Zeichenfolge für die Datenbankverbindung an.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-220">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="7a2c2-221">Die App verwendet eine SQL Server-Datenbank namens `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-221">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/3.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="7a2c2-222">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-222">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-223">Die Beispiel-App ruft die `AddSqlServer`-Methode mit der Verbindungszeichenfolge der Datenbank (*DbHealthStartup.cs*) auf:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-223">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7a2c2-224">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-224">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="7a2c2-225">Um das Szenario für den Datenbanktest mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-225">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="7a2c2-226">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-226">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="7a2c2-227">Entity Framework Core-DbContext-Test</span><span class="sxs-lookup"><span data-stu-id="7a2c2-227">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="7a2c2-228">Die `DbContext`-Überprüfung bestätigt, dass die App mit der Datenbank kommunizieren kann, die für einen EF Core-`DbContext` konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-228">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="7a2c2-229">Die `DbContext` Überprüfung wird in Apps unterstützt, für die gilt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-229">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="7a2c2-230">In ihnen wird [Entity Framework Core (EF Core)](/ef/core/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-230">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="7a2c2-231">Sie enthalten einen Paketverweis auf [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-231">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="7a2c2-232">`AddDbContextCheck<TContext>` registriert eine Integritätsprüfung für einen `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-232">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="7a2c2-233">Der `DbContext` wird als der `TContext` für die Methode bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-233">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="7a2c2-234">Eine Überladung ist verfügbar, um den Fehlerstatus, Tags sowie eine benutzerdefinierte Testabfrage zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-234">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="7a2c2-235">Standardmäßig:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-235">By default:</span></span>

* <span data-ttu-id="7a2c2-236">`DbContextHealthCheck` ruft die `CanConnectAsync`-Methode von EF Core auf.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-236">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="7a2c2-237">Sie können festlegen, welcher Vorgang ausgeführt wird, wenn die Integrität mit `AddDbContextCheck`-Methodenüberladungen überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-237">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="7a2c2-238">Der Name der Integritätsprüfung ist der Name des `TContext`-Typs.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-238">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="7a2c2-239">In der Beispiel-App wird `AppDbContext` für `AddDbContextCheck` bereitgestellt und als Dienst in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) registriert:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-239">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7a2c2-240">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-240">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health");
}
```

<span data-ttu-id="7a2c2-241">Stellen Sie sicher, dass die mit der Verbindungszeichenfolge angegebene Datenbank in der SQL Server-Instanz nicht vorhanden ist, um das `DbContext`-Testszenario mithilfe der Beispiel-App ausführen zu können.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-241">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="7a2c2-242">Falls die Datenbank vorhanden ist, löschen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-242">If the database exists, delete it.</span></span>

<span data-ttu-id="7a2c2-243">Führen Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-243">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="7a2c2-244">Wenn die App ausgeführt wird, überprüfen Sie den Integritätsstatus, indem Sie in einem Browser eine Anforderung an den `/health`-Endpunkt senden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-244">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="7a2c2-245">Datenbank und `AppDbContext` sind nicht vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-245">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="7a2c2-246">Fordern Sie die Beispiel-App auf, die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-246">Trigger the sample app to create the database.</span></span> <span data-ttu-id="7a2c2-247">Senden Sie eine `/createdatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-247">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="7a2c2-248">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-248">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="7a2c2-249">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-249">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="7a2c2-250">Datenbank und Kontext sind vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-250">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="7a2c2-251">Fordern Sie die Beispiel-App auf, die Datenbank zu löschen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-251">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="7a2c2-252">Senden Sie eine `/deletedatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-252">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="7a2c2-253">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-253">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="7a2c2-254">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-254">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="7a2c2-255">Die App meldet einen fehlerhaften Status:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-255">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="7a2c2-256">Separate Tests für Bereitschaft und Lebendigkeit</span><span class="sxs-lookup"><span data-stu-id="7a2c2-256">Separate readiness and liveness probes</span></span>

<span data-ttu-id="7a2c2-257">In einigen Hostingszenarien wird ein Integritätsprüfungspaar verwendet, bei dem zwischen zwei App-Status unterschieden wird:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-257">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="7a2c2-258">Die App funktioniert, ist aber noch nicht für den Empfang von Anforderungen bereit.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-258">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="7a2c2-259">Dieser Status gibt die *Bereitschaft* der App wieder.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-259">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="7a2c2-260">Die App funktioniert und antwortet auf Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-260">The app is functioning and responding to requests.</span></span> <span data-ttu-id="7a2c2-261">Dieser Status gibt die *Lebendigkeit* der App wieder.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-261">This state is the app's *liveness*.</span></span>

<span data-ttu-id="7a2c2-262">Die Bereitschaftsprüfung führt in der Regel eine Reihe umfassenderer und zeitaufwendigerer Überprüfungen durch, um zu ermitteln, ob alle Subsysteme und Ressourcen der App verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-262">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="7a2c2-263">Eine Lebendigkeitsprüfung führt nur eine schnelle Überprüfung aus, um zu ermitteln, ob die App für die Verarbeitung von Anforderungen verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-263">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="7a2c2-264">Nachdem die App die Bereitschaftsprüfung einmal bestanden hat, muss die App nicht weiter mit diesen Prüfungen belastet werden – weitere Prüfungen müssen dann nur noch für die Lebendigkeit erfolgen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-264">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="7a2c2-265">Die Beispiel-App enthält eine Integritätsprüfung, um den Abschluss eines Starttasks mit langer Ausführungsdauer in einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) zu melden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-265">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="7a2c2-266">`StartupHostedServiceHealthCheck` macht die Eigenschaft `StartupTaskCompleted` verfügbar, die der gehostete Dienst auf `true` festlegen kann, wenn der Task mit langer Ausführungsdauer abgeschlossen ist (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-266">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="7a2c2-267">Der Hintergrundtask mit langer Ausführungsdauer wird von einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*) gestartet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-267">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="7a2c2-268">Nach Abschluss des Tasks wird `StartupHostedServiceHealthCheck.StartupTaskCompleted` auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-268">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="7a2c2-269">Die Integritätsprüfung wird mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zusammen mit dem gehosteten Dienst in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-269">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="7a2c2-270">Da der gehostete Dienst die Eigenschaft in der Integritätsprüfung festlegen muss, wird die Integritätsprüfung ebenfalls im Dienstcontainer (*LivenessProbeStartup.cs*) registriert:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-270">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7a2c2-271">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-271">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="7a2c2-272">In der Beispiel-App wird der Integritätsprüfungs-Endpunkt erstellt unter:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-272">In the sample app, the health check endpoints are created at:</span></span>

* <span data-ttu-id="7a2c2-273">`/health/ready` für die Bereitschaftsprüfung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-273">`/health/ready` for the readiness check.</span></span> <span data-ttu-id="7a2c2-274">Die Bereitschaftsprüfung filtert auf Integritätsprüfungen mit dem `ready`-Tag.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-274">The readiness check filters health checks to the health check with the `ready` tag.</span></span>
* <span data-ttu-id="7a2c2-275">`/health/live` für die Lebendigkeitsprüfung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-275">`/health/live` for the liveness check.</span></span> <span data-ttu-id="7a2c2-276">Die Lebendigkeitsprüfung filtert `StartupHostedServiceHealthCheck` durch Rückgabe von `false` in [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) heraus (weitere Informationen finden Sie unter [Filtern von Integritätsprüfungen](#filter-health-checks)).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-276">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks))</span></span>

<span data-ttu-id="7a2c2-277">Im folgenden Beispielcode gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-277">In the following example code:</span></span>

* <span data-ttu-id="7a2c2-278">Die Bereitschaftsprüfung verwendet alle registrierten Überprüfungen mit dem Tag „Bereit“.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-278">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="7a2c2-279">`Predicate` schließt alle Überprüfungen aus und gibt ein „200-Ok“ zurück.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-279">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

<span data-ttu-id="7a2c2-280">Um das Szenario für die Konfiguration von Bereitschafts-/Lebendigkeitsprüfungen mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-280">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="7a2c2-281">Besuchen Sie `/health/ready` mehrmals in einem Browser, bis 15 Sekunden verstrichen sind.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-281">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="7a2c2-282">Die Integritätsprüfung meldet während der ersten 15 Sekunden *Fehlerhaft*.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-282">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="7a2c2-283">Nach 15 Sekunden meldet der Endpunkt *Fehlerfrei*, was darauf hindeutet, dass die Ausführung des Tasks mit langer Ausführungsdauer durch den gehosteten Dienst abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-283">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="7a2c2-284">In diesem Beispiel wird auch ein Herausgeber der Integritätsprüfung erstellt (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung), der die erste Bereitschaftsprüfung mit einer Verzögerung von zwei Sekunden ausführt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-284">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="7a2c2-285">Weitere Informationen finden Sie im Abschnitt [Herausgeber der Integritätsprüfung](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-285">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="7a2c2-286">Kubernetes-Beispiel</span><span class="sxs-lookup"><span data-stu-id="7a2c2-286">Kubernetes example</span></span>

<span data-ttu-id="7a2c2-287">Die Verwendung von Bereitschafts- und Lebendigkeitstests ist in Umgebungen wie [Kubernetes](https://kubernetes.io/) sehr nützlich.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-287">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="7a2c2-288">In Kubernetes muss eine App möglicherweise zeitaufwendige Startaufgaben ausführen, bevor sie Anforderungen wie z.B. das Testen der Verfügbarkeit der zugrunde liegenden Datenbank annehmen kann.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-288">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="7a2c2-289">Durch Verwendung separater Überprüfungen kann der Orchestrator unterscheiden, ob eine App funktioniert, aber noch nicht bereit ist, oder ob die App nicht gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-289">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="7a2c2-290">Weitere Informationen zu Bereitschafts- und Lebendigkeitstests in Kubernetes finden Sie in der Kubernetes-Dokumentation unter [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Konfigurieren von Lebendigkeits- und Bereitschaftstests).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-290">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="7a2c2-291">Das folgende Beispiel veranschaulicht die Konfiguration eines Bereitschaftstests in Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-291">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="7a2c2-292">Metrikbasierter Test mit einem benutzerdefinierten Antwortwriter</span><span class="sxs-lookup"><span data-stu-id="7a2c2-292">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="7a2c2-293">Die Beispiel-App veranschaulicht eine Arbeitsspeicher-Integritätsprüfung mit einem benutzerdefinierten Antwortwriter.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-293">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="7a2c2-294">`MemoryHealthCheck` meldet einen beeinträchtigten Status, wenn die App mehr Arbeitsspeicher verwendet, als für den Schwellenwert festgelegt wurde (1 GB in der Beispiel-App).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-294">`MemoryHealthCheck` reports a degraded status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="7a2c2-295">Das <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> enthält Garbage Collector-Informationen (GC) für die App (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-295">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="7a2c2-296">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-296">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-297">Die Integritätsprüfung wird nicht durch Übergabe an <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> aktiviert, stattdessen wird `MemoryHealthCheck` als Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-297">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="7a2c2-298">Alle bei <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrierten Dienste stehen für die Dienste und Middleware für Integritätsprüfungen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-298">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="7a2c2-299">Es wird empfohlen, Integritätsprüfungsdienste als Singleton-Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-299">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="7a2c2-300">In der Beispiel-App (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-300">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="7a2c2-301">Ein Integritätsprüfungs-Endpunkt wird durch Aufrufen von `MapHealthChecks` in `Startup.Configure` erstellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-301">A health check endpoint is created by calling `MapHealthChecks` in `Startup.Configure`.</span></span> <span data-ttu-id="7a2c2-302">Ein `WriteResponse`-Delegat wird in der `ResponseWriter`-Eigenschaft angegeben, um eine benutzerdefinierte JSON-Antwort auszugeben, wenn die Integritätsprüfung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-302">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health", new HealthCheckOptions()
    {
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="7a2c2-303">Die `WriteResponse`-Methode formatiert das `CompositeHealthCheckResult` als JSON-Objekt und führt zu folgender JSON-Ausgabe für die Integritätsprüfungsantwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-303">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="7a2c2-304">Um den metrikbasierten Test mit benutzerdefinierter Ausgabe des Antwortwriters mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-304">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="7a2c2-305">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Szenarien für metrikbasierte Integritätsprüfungen, einschließlich Prüfungen des Datenträgerspeichers und Lebendigkeitsprüfungen mit Maximalwert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-305">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="7a2c2-306">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-306">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="7a2c2-307">Filtern nach Port</span><span class="sxs-lookup"><span data-stu-id="7a2c2-307">Filter by port</span></span>

<span data-ttu-id="7a2c2-308">Rufen Sie `RequireHost` auf `MapHealthChecks` mit einem URL-Muster auf, das einen Port angibt, um die an diesen Port gerichteten Integritätsprüfungsanforderungen einzuschränken.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-308">Call `RequireHost` on `MapHealthChecks` with a URL pattern that specifies a port to restrict health check requests to the port specified.</span></span> <span data-ttu-id="7a2c2-309">Dieses Vorgehen wird normalerweise in einer Containerumgebung angewendet, um einen Port für Überwachungsdienste verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-309">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="7a2c2-310">Die Beispiel-App konfiguriert den Port mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-310">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="7a2c2-311">Der Port wird in der Datei *launchSettings.json* festgelegt und über eine Umgebungsvariable an den Konfigurationsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-311">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="7a2c2-312">Sie müssen den Server auch so konfigurieren, dass er am Verwaltungsport auf Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-312">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="7a2c2-313">Um die Beispiel-App zum Veranschaulichen der Konfiguration des Verwaltungsports zu verwenden, erstellen Sie die Datei *launchSettings.json* in einem *Eigenschaften*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-313">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="7a2c2-314">Die folgende *Properties/launchSettings.json*-Datei in der Beispiel-App ist in den Projektdateien der Beispiel-App nicht vorhanden und muss manuell erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-314">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="7a2c2-315">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-315">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-316">Erstellen Sie einen Integritätsprüfungs-Endpunkt durch Aufrufen von `MapHealthChecks` in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-316">Create a health check endpoint by calling `MapHealthChecks` in `Startup.Configure`.</span></span>

<span data-ttu-id="7a2c2-317">In der Beispiel-App gibt ein Aufruf von `RequireHost` auf dem Endpunkt in `Startup.Configure` den Verwaltungsport aus der Konfiguration an:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-317">In the sample app, a call to `RequireHost` on the endpoint in `Startup.Configure` specifies the management port from configuration:</span></span>

```csharp
endpoints.MapHealthChecks("/health")
    .RequireHost($"*:{Configuration["ManagementPort"]}");
```

<span data-ttu-id="7a2c2-318">Endpunkte werden in der Beispiel-App in `Startup.Configure` erstellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-318">Endpoints are created in the sample app in `Startup.Configure`.</span></span> <span data-ttu-id="7a2c2-319">Im folgenden Beispielcode gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-319">In the following example code:</span></span>

* <span data-ttu-id="7a2c2-320">Die Bereitschaftsprüfung verwendet alle registrierten Überprüfungen mit dem Tag „Bereit“.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-320">The readiness check uses all registered checks with the 'ready' tag.</span></span>
* <span data-ttu-id="7a2c2-321">`Predicate` schließt alle Überprüfungen aus und gibt ein „200-Ok“ zurück.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-321">The `Predicate` excludes all checks and return a 200-Ok.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapHealthChecks("/health/ready", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("ready"),
    });

    endpoints.MapHealthChecks("/health/live", new HealthCheckOptions()
    {
        Predicate = (_) => false
    });
}
```

> [!NOTE]
> <span data-ttu-id="7a2c2-322">Sie können vermeiden, die Datei *launchSettings.json* in der Beispiel-App erstellen zu müssen, indem Sie den Verwaltungsport explizit im Code festlegen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-322">You can avoid creating the *launchSettings.json* file in the sample app by setting the management port explicitly in code.</span></span> <span data-ttu-id="7a2c2-323">Fügen Sie in *Program.cs* an der Stelle, an der <xref:Microsoft.Extensions.Hosting.HostBuilder> erstellt wird, einen Aufruf von <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> hinzu, und geben Sie den Endpunkt des Verwaltungsports der App an.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-323">In *Program.cs* where the <xref:Microsoft.Extensions.Hosting.HostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenAnyIP*> and provide the app's management port endpoint.</span></span> <span data-ttu-id="7a2c2-324">Geben Sie in `Configure` von *ManagementPortStartup.cs* den Verwaltungsport mit `RequireHost` an:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-324">In `Configure` of *ManagementPortStartup.cs*, specify the management port with `RequireHost`:</span></span>
>
> <span data-ttu-id="7a2c2-325">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-325">*Program.cs*:</span></span>
>
> ```csharp
> return new HostBuilder()
>     .ConfigureWebHostDefaults(webBuilder =>
>     {
>         webBuilder.UseKestrel()
>             .ConfigureKestrel(serverOptions =>
>             {
>                 serverOptions.ListenAnyIP(5001);
>             })
>             .UseStartup(startupType);
>     })
>     .Build();
> ```
>
> <span data-ttu-id="7a2c2-326">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-326">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseEndpoints(endpoints =>
> {
>     endpoints.MapHealthChecks("/health").RequireHost("*:5001");
> });
> ```

<span data-ttu-id="7a2c2-327">Um das Szenario für die Konfiguration des Verwaltungsports mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-327">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="7a2c2-328">Verteilen einer Integritätsprüfungsbibliothek</span><span class="sxs-lookup"><span data-stu-id="7a2c2-328">Distribute a health check library</span></span>

<span data-ttu-id="7a2c2-329">So verteilen Sie eine Integritätsprüfung als Bibliothek:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-329">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="7a2c2-330">Schreiben Sie eine Integritätsprüfung, die die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle als eigenständige Klasse implementiert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-330">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="7a2c2-331">Die Klasse kann [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI), Typaktivierung und [benannte Optionen](xref:fundamentals/configuration/options) verwenden, um auf Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-331">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="7a2c2-332">In der Integritätsprüfungslogik von `CheckHealthAsync` gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-332">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="7a2c2-333">`data1` und `data2` werden in der Methode verwendet, um die Integritätsprüfungslogik des Tests auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-333">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="7a2c2-334">`AccessViolationException` wird verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-334">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="7a2c2-335">Wenn eine <xref:System.AccessViolationException> auftritt, wird der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> mit dem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurückgegeben, damit Benutzer den Integritätsprüfungs-Fehlerstatus konfigurieren können.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-335">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   namespace SampleApp
   {
       public class ExampleHealthCheck : IHealthCheck
       {
           private readonly string _data1;
           private readonly int? _data2;

           public ExampleHealthCheck(string data1, int? data2)
           {
               _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
               _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
           }

           public async Task<HealthCheckResult> CheckHealthAsync(
               HealthCheckContext context, CancellationToken cancellationToken)
           {
               try
               {
                   return HealthCheckResult.Healthy();
               }
               catch (AccessViolationException ex)
               {
                   return new HealthCheckResult(
                       context.Registration.FailureStatus,
                       description: "An access violation occurred during the check.",
                       exception: ex,
                       data: null);
               }
           }
       }
   }
   ```

1. <span data-ttu-id="7a2c2-336">Schreiben Sie eine Erweiterungsmethode mit Parametern, die von der nutzenden App in ihrer `Startup.Configure`-Methode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-336">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="7a2c2-337">Nehmen Sie im folgenden Beispiel die folgende Signatur für die Integritätsprüfungsmethode an:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-337">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="7a2c2-338">Die oben stehende Signatur gibt an, dass `ExampleHealthCheck` weitere Daten benötigt, um die Testlogik für die Integritätsprüfung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-338">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="7a2c2-339">Die Daten werden für den Delegaten bereitgestellt, der zum Erstellen der Integritätsprüfungsinstanz verwendet wird, wenn die Integritätsprüfung bei einer Erweiterungsmethode registriert wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-339">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="7a2c2-340">Im folgenden Beispiel gibt der Aufrufer optional Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-340">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="7a2c2-341">Name der Integritätsprüfung (`name`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-341">health check name (`name`).</span></span> <span data-ttu-id="7a2c2-342">Wenn der Wert `null` ist, wird `example_health_check` verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-342">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="7a2c2-343">string-Datenpunkt für die Integritätsprüfung (`data1`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-343">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="7a2c2-344">integer-Datenpunkt für die Integritätsprüfung (`data2`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-344">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="7a2c2-345">Wenn der Wert `null` ist, wird `1` verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-345">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="7a2c2-346">Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-346">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="7a2c2-347">Die Standardeinstellung ist `null`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-347">The default is `null`.</span></span> <span data-ttu-id="7a2c2-348">Wenn `null`, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) als Fehlerstatus gemeldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-348">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="7a2c2-349">Tags (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-349">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="7a2c2-350">Herausgeber der Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="7a2c2-350">Health Check Publisher</span></span>

<span data-ttu-id="7a2c2-351">Wenn dem Dienstcontainer ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> hinzugefügt wird, führt das Integritätsprüfungssystem Ihre Integritätsprüfungen regelmäßig aus und ruft `PublishAsync` mit dem Ergebnis auf.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-351">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="7a2c2-352">Dies ist nützlich in einem Szenario mit pushbasiertem Integritätsüberwachungssystem, in dem jeder Prozess das Überwachungssystem regelmäßig aufrufen muss, um die Integrität zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-352">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="7a2c2-353">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Schnittstelle weist eine einzige Methode auf:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-353">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="7a2c2-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> ermöglichen Ihnen, Folgendes festzulegen:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-354"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="7a2c2-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; Die nach der App angewendete anfängliche Verzögerung beginnt vor der Ausführung von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-355"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="7a2c2-356">Die Verzögerung wird einmal beim Start angewendet und gilt nicht für die nachfolgenden Iterationen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-356">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="7a2c2-357">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-357">The default value is five seconds.</span></span>
* <span data-ttu-id="7a2c2-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; Der Zeitraum der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Ausführung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-358"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="7a2c2-359">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-359">The default value is 30 seconds.</span></span>
* <span data-ttu-id="7a2c2-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Wenn <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` ist (Standard), führt der Herausgeber der Integritätsprüfung alle registrierten Integritätsüberprüfungen durch.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-360"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="7a2c2-361">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die die Menge der Prüfungen filtert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-361">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="7a2c2-362">Das Prädikat wird in jedem Zeitraum ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-362">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="7a2c2-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; Das Timeout für die Ausführung der Integritätsprüfungen für alle <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-363"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="7a2c2-364">Verwenden Sie <xref:System.Threading.Timeout.InfiniteTimeSpan> zur Ausführung ohne Timeout.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-364">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="7a2c2-365">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-365">The default value is 30 seconds.</span></span>

<span data-ttu-id="7a2c2-366">In der Beispiel-App ist `ReadinessPublisher` eine <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-366">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="7a2c2-367">Der Integritätsprüfungsstatus wird für jede Überprüfung auf folgenden Protokollebenen protokolliert:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-367">The health check status is logged for each check at a log level of:</span></span>

* <span data-ttu-id="7a2c2-368">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), wenn der Status der Integritätsprüfungen <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-368">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="7a2c2-369">Fehler (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), wenn der Status <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> oder <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-369">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="7a2c2-370">Im Beispiel `LivenessProbeStartup` der Beispiel-App `StartupHostedService` hat die Bereitschaftsprüfung eine Startverzögerung von zwei Sekunden und führt die Prüfung alle 30 Sekunden aus.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-370">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="7a2c2-371">Zum Aktivieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Implementierung registriert das Beispiel `ReadinessPublisher` als Singletondienst im [Abhängigkeitsinjektionscontainer (Dependency Injection, DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-371">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/3.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

> [!NOTE]
> <span data-ttu-id="7a2c2-372">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Herausgeber für verschiedene Systeme, einschließlich [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-372">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="7a2c2-373">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-373">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="7a2c2-374">Einschränken von Integritätsprüfungen mit MapWhen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-374">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="7a2c2-375">Verwenden Sie <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, um die Anforderungspipeline für Endpunkte von Integritätsprüfungen bedingt zu branchen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-375">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="7a2c2-376">Im folgenden Beispiel verzweigt `MapWhen` die Anforderungspipeline so, dass die Middleware für Integritätsprüfungen aktiviert wird, wenn eine GET-Anforderung für den Endpunkt `api/HealthCheck` empfangen wird:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-376">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseEndpoints(endpoints =>
{
    endpoints.MapRazorPages();
});
```

<span data-ttu-id="7a2c2-377">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-377">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7a2c2-378">ASP.NET Core bietet Middleware für Integritätsprüfungen und Bibliotheken für die Berichterstellung für Komponenten der App-Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-378">ASP.NET Core offers Health Checks Middleware and libraries for reporting the health of app infrastructure components.</span></span>

<span data-ttu-id="7a2c2-379">Integritätsprüfungen werden von einer App als HTTP-Endpunkte verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-379">Health checks are exposed by an app as HTTP endpoints.</span></span> <span data-ttu-id="7a2c2-380">Integritätsprüfungs-Endpunkte können für eine Vielzahl von Echtzeit-Überwachungsszenarien konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-380">Health check endpoints can be configured for a variety of real-time monitoring scenarios:</span></span>

* <span data-ttu-id="7a2c2-381">Integritätstests können von Containerorchestratoren und Lastenausgleichsmodulen verwendet werden, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-381">Health probes can be used by container orchestrators and load balancers to check an app's status.</span></span> <span data-ttu-id="7a2c2-382">Ein Containerorchestrator kann z.B. auf eine fehlerhafte Integritätsprüfung reagieren, indem die Ausführung einer Bereitstellung angehalten oder ein Container neu gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-382">For example, a container orchestrator may respond to a failing health check by halting a rolling deployment or restarting a container.</span></span> <span data-ttu-id="7a2c2-383">Ein Lastenausgleichsmodul kann auf eine fehlerhafte App reagieren, indem Datenverkehr von der fehlerhaften zu einer fehlerfreien Instanz umgeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-383">A load balancer might react to an unhealthy app by routing traffic away from the failing instance to a healthy instance.</span></span>
* <span data-ttu-id="7a2c2-384">Die Nutzung von Arbeitsspeicher, Datenträgern und anderen physischen Serverressourcen kann auf einen fehlerfreien Status überwacht werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-384">Use of memory, disk, and other physical server resources can be monitored for healthy status.</span></span>
* <span data-ttu-id="7a2c2-385">Integritätsprüfungen können die Abhängigkeiten einer App testen, wie z.B. Datenbanken und externe Dienstendpunkte, um Verfügbarkeit und normale Funktionsweise zu bestätigen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-385">Health checks can test an app's dependencies, such as databases and external service endpoints, to confirm availability and normal functioning.</span></span>

<span data-ttu-id="7a2c2-386">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="7a2c2-386">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/health-checks/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7a2c2-387">Die Beispiel-App enthält Beispiele der Szenarien, die in diesem Thema beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-387">The sample app includes examples of the scenarios described in this topic.</span></span> <span data-ttu-id="7a2c2-388">Um die Beispiel-App für ein bestimmtes Szenario auszuführen, verwenden Sie den Befehl [dotnet run](/dotnet/core/tools/dotnet-run) aus dem Ordner des Projekts in einer Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-388">To run the sample app for a given scenario, use the [dotnet run](/dotnet/core/tools/dotnet-run) command from the project's folder in a command shell.</span></span> <span data-ttu-id="7a2c2-389">Informationen zur Verwendung der Beispiel-App finden Sie in der Datei *README.md* der Beispiel-App und den Szenariobeschreibungen in diesem Thema.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-389">See the sample app's *README.md* file and the scenario descriptions in this topic for details on how to use the sample app.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7a2c2-390">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="7a2c2-390">Prerequisites</span></span>

<span data-ttu-id="7a2c2-391">Integritätsprüfungen werden in der Regel mit einem externen Überwachungsdienst oder Containerorchestrator verwendet, um den Status einer App zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-391">Health checks are usually used with an external monitoring service or container orchestrator to check the status of an app.</span></span> <span data-ttu-id="7a2c2-392">Bevor Sie Integritätsprüfungen zu einer App hinzufügen, entscheiden Sie, welches Überwachungssystem verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-392">Before adding health checks to an app, decide on which monitoring system to use.</span></span> <span data-ttu-id="7a2c2-393">Das Überwachungssystem bestimmt, welche Arten von Integritätsprüfungen erstellt und wie die jeweiligen Endpunkte konfiguriert werden müssen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-393">The monitoring system dictates what types of health checks to create and how to configure their endpoints.</span></span>

<span data-ttu-id="7a2c2-394">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-394">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.AspNetCore.Diagnostics.HealthChecks](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.HealthChecks) package.</span></span>

<span data-ttu-id="7a2c2-395">Die Beispiel-App stellt Startcode bereit, um die Integritätsprüfungen für verschiedene Szenarien zu veranschaulichen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-395">The sample app provides startup code to demonstrate health checks for several scenarios.</span></span> <span data-ttu-id="7a2c2-396">Das Szenario [Datenbanktest](#database-probe) überprüft die Integrität einer Datenbankverbindung mithilfe von [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-396">The [database probe](#database-probe) scenario checks the health of a database connection using [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks).</span></span> <span data-ttu-id="7a2c2-397">Das Szenario [DbContext-Test](#entity-framework-core-dbcontext-probe) überprüft eine Datenbank mithilfe eines EF Core-`DbContext`-Elements.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-397">The [DbContext probe](#entity-framework-core-dbcontext-probe) scenario checks a database using an EF Core `DbContext`.</span></span> <span data-ttu-id="7a2c2-398">Für ein Erkunden der Datenbankszenarios gilt für die Beispiel-App:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-398">To explore the database scenarios, the sample app:</span></span>

* <span data-ttu-id="7a2c2-399">Sie erstellt eine Datenbank und stellt deren Verbindungszeichenfolge in der Datei *appsettings.json* bereit.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-399">Creates a database and provides its connection string in the *appsettings.json* file.</span></span>
* <span data-ttu-id="7a2c2-400">Sie hat die folgenden Paketverweise in ihrer Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-400">Has the following package references in its project file:</span></span>
  * [<span data-ttu-id="7a2c2-401">AspNetCore.HealthChecks.SqlServer</span><span class="sxs-lookup"><span data-stu-id="7a2c2-401">AspNetCore.HealthChecks.SqlServer</span></span>](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/)
  * [<span data-ttu-id="7a2c2-402">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="7a2c2-402">Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/)

> [!NOTE]
> <span data-ttu-id="7a2c2-403">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-403">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

<span data-ttu-id="7a2c2-404">Ein anderes Szenario für Integritätsprüfungen zeigt, wie Sie Integritätsprüfungen auf einen Verwaltungsport filtern können.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-404">Another health check scenario demonstrates how to filter health checks to a management port.</span></span> <span data-ttu-id="7a2c2-405">Für die Beispiel-App müssen Sie eine *Properties/launchSettings.json*-Datei erstellen, die die Verwaltungs-URL und den Verwaltungsport enthält.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-405">The sample app requires you to create a *Properties/launchSettings.json* file that includes the management URL and management port.</span></span> <span data-ttu-id="7a2c2-406">Weitere Informationen finden Sie im Abschnitt [Filtern nach Port](#filter-by-port).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-406">For more information, see the [Filter by port](#filter-by-port) section.</span></span>

## <a name="basic-health-probe"></a><span data-ttu-id="7a2c2-407">Grundlegender Integritätstest</span><span class="sxs-lookup"><span data-stu-id="7a2c2-407">Basic health probe</span></span>

<span data-ttu-id="7a2c2-408">In vielen Apps genügt zur Ermittlung des App-Status eine grundlegende Integritätstestkonfiguration, die die Verfügbarkeit der App für die Verarbeitung von Anforderungen (*Lebendigkeit*) meldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-408">For many apps, a basic health probe configuration that reports the app's availability to process requests (*liveness*) is sufficient to discover the status of the app.</span></span>

<span data-ttu-id="7a2c2-409">Die grundlegende Konfiguration registriert Integritätsprüfungsdienste und ruft die Middleware für Integritätsprüfungen auf, damit diese an einem URL-Endpunkt mit dem Integritätsstatus antwortet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-409">The basic configuration registers health check services and calls the Health Checks Middleware to respond at a URL endpoint with a health response.</span></span> <span data-ttu-id="7a2c2-410">Standardmäßig werden keine spezifischen Integritätsprüfungen registriert, um eine bestimmte Abhängigkeit oder ein bestimmtes Subsystem zu testen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-410">By default, no specific health checks are registered to test any particular dependency or subsystem.</span></span> <span data-ttu-id="7a2c2-411">Die App wird als fehlerfrei angesehen, wenn sie an der URL des Integritätsendpunkts antworten kann.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-411">The app is considered healthy if it's capable of responding at the health endpoint URL.</span></span> <span data-ttu-id="7a2c2-412">Der standardmäßige Antwortwriter schreibt den Status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) als Klartextantwort zurück an den Client und gibt den Status als [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) oder [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) an.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-412">The default response writer writes the status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) as a plaintext response back to the client, indicating either a [HealthStatus.Healthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus), [HealthStatus.Degraded](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) or [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) status.</span></span>

<span data-ttu-id="7a2c2-413">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-413">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-414">Fügen Sie mit <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in der Anforderungsverarbeitungspipeline von `Startup.Configure` einen Endpunkt für die Middleware für Integritätsprüfungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-414">Add an endpoint for Health Checks Middleware with <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the request processing pipeline of `Startup.Configure`.</span></span>

<span data-ttu-id="7a2c2-415">In der Beispiel-App wird der Integritätsprüfungs-Endpunkt in `/health` (*BasicStartup.cs*) erstellt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-415">In the sample app, the health check endpoint is created at `/health` (*BasicStartup.cs*):</span></span>

```csharp
public class BasicStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddHealthChecks();
    }

    public void Configure(IApplicationBuilder app)
    {
        app.UseHealthChecks("/health");
    }
}
```

<span data-ttu-id="7a2c2-416">Um das Szenario für die grundlegende Konfiguration mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-416">To run the basic configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario basic
```

### <a name="docker-example"></a><span data-ttu-id="7a2c2-417">Docker-Beispiel</span><span class="sxs-lookup"><span data-stu-id="7a2c2-417">Docker example</span></span>

<span data-ttu-id="7a2c2-418">[Docker](xref:host-and-deploy/docker/index) bietet eine integrierte `HEALTHCHECK`-Direktive, mit der Sie den Status einer App überprüfen können, die die grundlegende Konfiguration für Integritätsprüfungen verwendet:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-418">[Docker](xref:host-and-deploy/docker/index) offers a built-in `HEALTHCHECK` directive that can be used to check the status of an app that uses the basic health check configuration:</span></span>

```
HEALTHCHECK CMD curl --fail http://localhost:5000/health || exit
```

## <a name="create-health-checks"></a><span data-ttu-id="7a2c2-419">Erstellen von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-419">Create health checks</span></span>

<span data-ttu-id="7a2c2-420">Integritätsprüfungen werden durch Implementieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle erstellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-420">Health checks are created by implementing the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface.</span></span> <span data-ttu-id="7a2c2-421">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*>-Methode gibt ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurück, das die Integrität als `Healthy`, `Degraded` oder `Unhealthy` angibt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-421">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck.CheckHealthAsync*> method returns a <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> that indicates the health as `Healthy`, `Degraded`, or `Unhealthy`.</span></span> <span data-ttu-id="7a2c2-422">Das Ergebnis wird als Klartextantwort mit einem konfigurierbaren Statuscode geschrieben (diese Konfiguration wird im Abschnitt [Optionen für die Integritätsprüfung](#health-check-options) beschrieben).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-422">The result is written as a plaintext response with a configurable status code (configuration is described in the [Health check options](#health-check-options) section).</span></span> <span data-ttu-id="7a2c2-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> kann auch optionale Schlüssel-Wert-Paare zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-423"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> can also return optional key-value pairs.</span></span>

### <a name="example-health-check"></a><span data-ttu-id="7a2c2-424">Beispiel für eine Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="7a2c2-424">Example health check</span></span>

<span data-ttu-id="7a2c2-425">Die folgende `ExampleHealthCheck`-Klasse veranschaulicht das Layout einer Integritätsprüfung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-425">The following `ExampleHealthCheck` class demonstrates the layout of a health check.</span></span> <span data-ttu-id="7a2c2-426">Die Integritätsprüfungslogik wird in der `CheckHealthAsync`-Methode platziert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-426">The health checks logic is placed in the `CheckHealthAsync` method.</span></span> <span data-ttu-id="7a2c2-427">Im folgenden Beispiel wird eine Dummyvariable `healthCheckResultHealthy` auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-427">The following example sets a dummy variable, `healthCheckResultHealthy`, to `true`.</span></span> <span data-ttu-id="7a2c2-428">Wenn der Wert von `healthCheckResultHealthy` auf `false` festgelegt ist, wird der [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*)-Status zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-428">If the value of `healthCheckResultHealthy` is set to `false`, the [HealthCheckResult.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult.Unhealthy*) status is returned.</span></span>

```csharp
public class ExampleHealthCheck : IHealthCheck
{
    public Task<HealthCheckResult> CheckHealthAsync(
        HealthCheckContext context,
        CancellationToken cancellationToken = default(CancellationToken))
    {
        var healthCheckResultHealthy = true;

        if (healthCheckResultHealthy)
        {
            return Task.FromResult(
                HealthCheckResult.Healthy("The check indicates a healthy result."));
        }

        return Task.FromResult(
            HealthCheckResult.Unhealthy("The check indicates an unhealthy result."));
    }
}
```

### <a name="register-health-check-services"></a><span data-ttu-id="7a2c2-429">Registrieren von Integritätsprüfungsdiensten</span><span class="sxs-lookup"><span data-stu-id="7a2c2-429">Register health check services</span></span>

<span data-ttu-id="7a2c2-430">Der `ExampleHealthCheck`-Typ wird den Integritätsprüfungsdiensten in `Startup.ConfigureServices` mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-430">The `ExampleHealthCheck` type is added to health check services in `Startup.ConfigureServices` with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>("example_health_check");
```

<span data-ttu-id="7a2c2-431">Die in der folgenden Abbildung gezeigte <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>-Überladung legt den Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) fest, der angegeben werden soll, wenn die Integritätsprüfung einen Fehler meldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-431">The <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> overload shown in the following example sets the failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>) to report when the health check reports a failure.</span></span> <span data-ttu-id="7a2c2-432">Wenn der Fehlerstatus auf `null` (Standardwert) festgelegt ist, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) gemeldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-432">If the failure status is set to `null` (default), [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported.</span></span> <span data-ttu-id="7a2c2-433">Diese Überladung ist ein nützliches Szenario für Bibliotheksersteller: Bei einem Integritätsprüfungsfehler wird der durch die Bibliothek angegebene Fehlerstatus von der App erzwungen, wenn die Implementierung der Integritätsprüfung die Einstellung berücksichtigt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-433">This overload is a useful scenario for library authors, where the failure status indicated by the library is enforced by the app when a health check failure occurs if the health check implementation honors the setting.</span></span>

<span data-ttu-id="7a2c2-434">*Tags* können zum Filtern von Integritätsprüfungen verwendet werden (dies wird im Abschnitt [Filtern von Integritätsprüfungen](#filter-health-checks) genauer beschrieben).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-434">*Tags* can be used to filter health checks (described further in the [Filter health checks](#filter-health-checks) section).</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck<ExampleHealthCheck>(
        "example_health_check",
        failureStatus: HealthStatus.Degraded,
        tags: new[] { "example" });
```

<span data-ttu-id="7a2c2-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> kann auch eine Lambdafunktion ausführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-435"><xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> can also execute a lambda function.</span></span> <span data-ttu-id="7a2c2-436">Im folgenden `Startup.ConfigureServices`-Beispiel wird der Name der Integritätsprüfung als `Example` angegeben, und die Prüfung gibt immer einen fehlerfreien Status zurück:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-436">In the following `Startup.ConfigureServices` example, the health check name is specified as `Example` and the check always returns a healthy state:</span></span>

```csharp
services.AddHealthChecks()
    .AddCheck("Example", () =>
        HealthCheckResult.Healthy("Example is OK!"), tags: new[] { "example" });
```

### <a name="use-health-checks-middleware"></a><span data-ttu-id="7a2c2-437">Verwenden von Middleware für Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-437">Use Health Checks Middleware</span></span>

<span data-ttu-id="7a2c2-438">Rufen Sie <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in der Verarbeitungspipeline von `Startup.Configure` mit der Endpunkt-URL oder dem relativen Pfad auf:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-438">In `Startup.Configure`, call <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> in the processing pipeline with the endpoint URL or relative path:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="7a2c2-439">Wenn die Integritätsprüfungen an einem bestimmten Port lauschen sollen, verwenden Sie eine Überladung von <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*>, um den Port festzulegen (dies wird im Abschnitt [Filtern nach Port](#filter-by-port) genauer beschrieben):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-439">If the health checks should listen on a specific port, use an overload of <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> to set the port (described further in the [Filter by port](#filter-by-port) section):</span></span>

```csharp
app.UseHealthChecks("/health", port: 8000);
```

## <a name="health-check-options"></a><span data-ttu-id="7a2c2-440">Optionen für die Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="7a2c2-440">Health check options</span></span>

<span data-ttu-id="7a2c2-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> ermöglichen die Anpassung des Verhaltens von Integritätsprüfungen:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-441"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions> provide an opportunity to customize health check behavior:</span></span>

* [<span data-ttu-id="7a2c2-442">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-442">Filter health checks</span></span>](#filter-health-checks)
* [<span data-ttu-id="7a2c2-443">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="7a2c2-443">Customize the HTTP status code</span></span>](#customize-the-http-status-code)
* [<span data-ttu-id="7a2c2-444">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="7a2c2-444">Suppress cache headers</span></span>](#suppress-cache-headers)
* [<span data-ttu-id="7a2c2-445">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="7a2c2-445">Customize output</span></span>](#customize-output)

### <a name="filter-health-checks"></a><span data-ttu-id="7a2c2-446">Filtern von Integritätsprüfungen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-446">Filter health checks</span></span>

<span data-ttu-id="7a2c2-447">Standardmäßig führt die Middleware für Integritätsprüfungen alle registrierten Integritätsprüfungen aus.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-447">By default, Health Checks Middleware runs all registered health checks.</span></span> <span data-ttu-id="7a2c2-448">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die einen booleschen Wert an die Option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-448">To run a subset of health checks, provide a function that returns a boolean to the <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate> option.</span></span> <span data-ttu-id="7a2c2-449">Im folgenden Beispiel wird die `Bar`-Integritätsprüfung anhand ihres Tags (`bar_tag`) in der Bedingungsanweisung der Funktion herausgefiltert. Dabei wird `true` nur zurückgegeben, wenn die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags>-Eigenschaft der Integritätsprüfung mit `foo_tag` oder `baz_tag` übereinstimmt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-449">In the following example, the `Bar` health check is filtered out by its tag (`bar_tag`) in the function's conditional statement, where `true` is only returned if the health check's <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.Tags> property matches `foo_tag` or `baz_tag`:</span></span>

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Diagnostics.HealthChecks;
using Microsoft.Extensions.Diagnostics.HealthChecks;

public void ConfigureServices(IServiceCollection services)
{
    services.AddHealthChecks()
        .AddCheck("Foo", () =>
            HealthCheckResult.Healthy("Foo is OK!"), tags: new[] { "foo_tag" })
        .AddCheck("Bar", () =>
            HealthCheckResult.Unhealthy("Bar is unhealthy!"), 
                tags: new[] { "bar_tag" })
        .AddCheck("Baz", () =>
            HealthCheckResult.Healthy("Baz is OK!"), tags: new[] { "baz_tag" });
}

public void Configure(IApplicationBuilder app)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        Predicate = (check) => check.Tags.Contains("foo_tag") ||
            check.Tags.Contains("baz_tag")
    });
}
```

### <a name="customize-the-http-status-code"></a><span data-ttu-id="7a2c2-450">Anpassen des HTTP-Statuscodes</span><span class="sxs-lookup"><span data-stu-id="7a2c2-450">Customize the HTTP status code</span></span>

<span data-ttu-id="7a2c2-451">Verwenden Sie <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes>, um die Zuordnung des Integritätsstatus zu HTTP-Statuscodes anzupassen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-451">Use <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResultStatusCodes> to customize the mapping of health status to HTTP status codes.</span></span> <span data-ttu-id="7a2c2-452">Die folgenden <xref:Microsoft.AspNetCore.Http.StatusCodes>-Zuweisungen stellen die von der Middleware verwendeten Standardwerte dar.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-452">The following <xref:Microsoft.AspNetCore.Http.StatusCodes> assignments are the default values used by the middleware.</span></span> <span data-ttu-id="7a2c2-453">Ändern Sie die Statuscodewerte so, dass sie Ihren Anforderungen entsprechen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-453">Change the status code values to meet your requirements.</span></span>

<span data-ttu-id="7a2c2-454">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-454">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResultStatusCodes =
    {
        [HealthStatus.Healthy] = StatusCodes.Status200OK,
        [HealthStatus.Degraded] = StatusCodes.Status200OK,
        [HealthStatus.Unhealthy] = StatusCodes.Status503ServiceUnavailable
    }
});
```

### <a name="suppress-cache-headers"></a><span data-ttu-id="7a2c2-455">Unterdrücken von Cacheheadern</span><span class="sxs-lookup"><span data-stu-id="7a2c2-455">Suppress cache headers</span></span>

<span data-ttu-id="7a2c2-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> steuert, ob die Middleware für Integritätsprüfungen einer Testantwort HTTP-Header hinzufügt, um das Zwischenspeichern von Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-456"><xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.AllowCachingResponses> controls whether the Health Checks Middleware adds HTTP headers to a probe response to prevent response caching.</span></span> <span data-ttu-id="7a2c2-457">Wenn der Wert `false` (Standard) lautet, legt die Middleware die Header `Cache-Control`, `Expires` und `Pragma` fest oder überschreibt sie, um eine Zwischenspeicherung der Antworten zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-457">If the value is `false` (default), the middleware sets or overrides the `Cache-Control`, `Expires`, and `Pragma` headers to prevent response caching.</span></span> <span data-ttu-id="7a2c2-458">Wenn der Wert `true` lautet, ändert die Middleware die Cacheheader der Antwort nicht.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-458">If the value is `true`, the middleware doesn't modify the cache headers of the response.</span></span>

<span data-ttu-id="7a2c2-459">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-459">In `Startup.Configure`:</span></span>

```csharp
//using Microsoft.AspNetCore.Diagnostics.HealthChecks;
//using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    AllowCachingResponses = false
});
```

### <a name="customize-output"></a><span data-ttu-id="7a2c2-460">Anpassen der Ausgabe</span><span class="sxs-lookup"><span data-stu-id="7a2c2-460">Customize output</span></span>

<span data-ttu-id="7a2c2-461">Mit der Option <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> wird ein Delegat abgerufen oder festgelegt, der zum Schreiben der Antwort verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-461">The <xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.ResponseWriter> option gets or sets a delegate used to write the response.</span></span> <span data-ttu-id="7a2c2-462">Der Standarddelegat schreibt eine minimale Klartextantwort mit dem Zeichenfolgenwert [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-462">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span>

<span data-ttu-id="7a2c2-463">In `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-463">In `Startup.Configure`:</span></span>

```csharp
// using Microsoft.AspNetCore.Diagnostics.HealthChecks;
// using Microsoft.Extensions.Diagnostics.HealthChecks;

app.UseHealthChecks("/health", new HealthCheckOptions()
{
    ResponseWriter = WriteResponse
});
```

<span data-ttu-id="7a2c2-464">Der Standarddelegat schreibt eine minimale Klartextantwort mit dem Zeichenfolgenwert [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-464">The default delegate writes a minimal plaintext response with the string value of [HealthReport.Status](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthReport.Status).</span></span> <span data-ttu-id="7a2c2-465">Der folgende benutzerdefinierte Delegat, `WriteResponse`, gibt eine benutzerdefinierte JSON-Antwort aus:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-465">The following custom delegate, `WriteResponse`, outputs a custom JSON response:</span></span>

```csharp
private static Task WriteResponse(HttpContext httpContext, HealthReport result)
{
    httpContext.Response.ContentType = "application/json";

    var json = new JObject(
        new JProperty("status", result.Status.ToString()),
        new JProperty("results", new JObject(result.Entries.Select(pair =>
            new JProperty(pair.Key, new JObject(
                new JProperty("status", pair.Value.Status.ToString()),
                new JProperty("description", pair.Value.Description),
                new JProperty("data", new JObject(pair.Value.Data.Select(
                    p => new JProperty(p.Key, p.Value))))))))));
    return httpContext.Response.WriteAsync(
        json.ToString(Formatting.Indented));
}
```

<span data-ttu-id="7a2c2-466">Das Integritätsprüfungssystem bietet keine integrierte Unterstützung für komplexe JSON-Rückgabeformate, da das Format für Ihre Überwachungssystemauswahl spezifisch ist.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-466">The health checks system doesn't provide built-in support for complex JSON return formats because the format is specific to your choice of monitoring system.</span></span> <span data-ttu-id="7a2c2-467">Sie können das `JObject` im vorherigen Beispiel nach Bedarf anpassen, sodass Ihre Anforderungen erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-467">Feel free to customize the `JObject` in the preceding example as necessary to meet your needs.</span></span>

## <a name="database-probe"></a><span data-ttu-id="7a2c2-468">Datenbanktest</span><span class="sxs-lookup"><span data-stu-id="7a2c2-468">Database probe</span></span>

<span data-ttu-id="7a2c2-469">Eine Integritätsprüfung kann eine Datenbankabfrage angeben, die als boolescher Test ausgeführt wird, um zu ermitteln, ob die Datenbank normal reagiert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-469">A health check can specify a database query to run as a boolean test to indicate if the database is responding normally.</span></span>

<span data-ttu-id="7a2c2-470">Die Beispiel-App verwendet [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), eine Bibliothek für Integritätsprüfungen für ASP.NET Core-Apps, um eine Integritätsprüfung für eine SQL Server-Datenbank auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-470">The sample app uses [AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks), a health check library for ASP.NET Core apps, to perform a health check on a SQL Server database.</span></span> <span data-ttu-id="7a2c2-471">`AspNetCore.Diagnostics.HealthChecks` führt eine `SELECT 1`-Abfrage in der Datenbank aus, um zu bestätigen, dass die Verbindung mit der Datenbank fehlerfrei ist.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-471">`AspNetCore.Diagnostics.HealthChecks` executes a `SELECT 1` query against the database to confirm the connection to the database is healthy.</span></span>

> [!WARNING]
> <span data-ttu-id="7a2c2-472">Wenn Sie die Datenbankverbindung mithilfe einer Abfrage überprüfen, wählen Sie eine Abfrage aus, die eine schnelle Antwort zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-472">When checking a database connection with a query, choose a query that returns quickly.</span></span> <span data-ttu-id="7a2c2-473">Bei einer Abfrage besteht immer das Risiko, dass die Datenbank überladen und ihre Leistung beeinträchtigt wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-473">The query approach runs the risk of overloading the database and degrading its performance.</span></span> <span data-ttu-id="7a2c2-474">In den meisten Fällen ist es nicht notwendig, eine Testabfrage auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-474">In most cases, running a test query isn't necessary.</span></span> <span data-ttu-id="7a2c2-475">Es genügt zumeist, einfach erfolgreich eine Verbindung mit der Datenbank herzustellen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-475">Merely making a successful connection to the database is sufficient.</span></span> <span data-ttu-id="7a2c2-476">Wenn Sie eine Abfrage ausführen müssen, wählen Sie eine einfache SELECT-Abfrage aus, wie z.B. `SELECT 1`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-476">If you find it necessary to run a query, choose a simple SELECT query, such as `SELECT 1`.</span></span>

<span data-ttu-id="7a2c2-477">Fügen Sie einen Paketverweis auf [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-477">Include a package reference to [AspNetCore.HealthChecks.SqlServer](https://www.nuget.org/packages/AspNetCore.HealthChecks.SqlServer/).</span></span>

<span data-ttu-id="7a2c2-478">Geben Sie in der Datei *appsettings.json* der App eine gültige Zeichenfolge für die Datenbankverbindung an.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-478">Supply a valid database connection string in the *appsettings.json* file of the sample app.</span></span> <span data-ttu-id="7a2c2-479">Die App verwendet eine SQL Server-Datenbank namens `HealthCheckSample`:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-479">The app uses a SQL Server database named `HealthCheckSample`:</span></span>

[!code-json[](health-checks/samples/2.x/HealthChecksSample/appsettings.json?highlight=3)]

<span data-ttu-id="7a2c2-480">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-480">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-481">Die Beispiel-App ruft die `AddSqlServer`-Methode mit der Verbindungszeichenfolge der Datenbank (*DbHealthStartup.cs*) auf:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-481">The sample app calls the `AddSqlServer` method with the database's connection string (*DbHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7a2c2-482">Rufen Sie die Middleware für Integritätsprüfungen in der App-Verarbeitungspipeline in `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-482">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`:</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="7a2c2-483">Um das Szenario für den Datenbanktest mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-483">To run the database probe scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario db
```

> [!NOTE]
> <span data-ttu-id="7a2c2-484">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-484">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="entity-framework-core-dbcontext-probe"></a><span data-ttu-id="7a2c2-485">Entity Framework Core-DbContext-Test</span><span class="sxs-lookup"><span data-stu-id="7a2c2-485">Entity Framework Core DbContext probe</span></span>

<span data-ttu-id="7a2c2-486">Die `DbContext`-Überprüfung bestätigt, dass die App mit der Datenbank kommunizieren kann, die für einen EF Core-`DbContext` konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-486">The `DbContext` check confirms that the app can communicate with the database configured for an EF Core `DbContext`.</span></span> <span data-ttu-id="7a2c2-487">Die `DbContext` Überprüfung wird in Apps unterstützt, für die gilt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-487">The `DbContext` check is supported in apps that:</span></span>

* <span data-ttu-id="7a2c2-488">In ihnen wird [Entity Framework Core (EF Core)](/ef/core/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-488">Use [Entity Framework (EF) Core](/ef/core/).</span></span>
* <span data-ttu-id="7a2c2-489">Sie enthalten einen Paketverweis auf [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-489">Include a package reference to [Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.Extensions.Diagnostics.HealthChecks.EntityFrameworkCore/).</span></span>

<span data-ttu-id="7a2c2-490">`AddDbContextCheck<TContext>` registriert eine Integritätsprüfung für einen `DbContext`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-490">`AddDbContextCheck<TContext>` registers a health check for a `DbContext`.</span></span> <span data-ttu-id="7a2c2-491">Der `DbContext` wird als der `TContext` für die Methode bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-491">The `DbContext` is supplied as the `TContext` to the method.</span></span> <span data-ttu-id="7a2c2-492">Eine Überladung ist verfügbar, um den Fehlerstatus, Tags sowie eine benutzerdefinierte Testabfrage zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-492">An overload is available to configure the failure status, tags, and a custom test query.</span></span>

<span data-ttu-id="7a2c2-493">Standardmäßig:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-493">By default:</span></span>

* <span data-ttu-id="7a2c2-494">`DbContextHealthCheck` ruft die `CanConnectAsync`-Methode von EF Core auf.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-494">The `DbContextHealthCheck` calls EF Core's `CanConnectAsync` method.</span></span> <span data-ttu-id="7a2c2-495">Sie können festlegen, welcher Vorgang ausgeführt wird, wenn die Integrität mit `AddDbContextCheck`-Methodenüberladungen überprüft wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-495">You can customize what operation is run when checking health using `AddDbContextCheck` method overloads.</span></span>
* <span data-ttu-id="7a2c2-496">Der Name der Integritätsprüfung ist der Name des `TContext`-Typs.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-496">The name of the health check is the name of the `TContext` type.</span></span>

<span data-ttu-id="7a2c2-497">In der Beispiel-App wird `AppDbContext` für `AddDbContextCheck` bereitgestellt und als Dienst in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*) registriert:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-497">In the sample app, `AppDbContext` is provided to `AddDbContextCheck` and registered as a service in `Startup.ConfigureServices` (*DbContextHealthStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/DbContextHealthStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7a2c2-498">In der Beispiel-App fügt `UseHealthChecks` die Middleware für Integritätsprüfungen in `Startup.Configure` hinzu.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-498">In the sample app, `UseHealthChecks` adds the Health Checks Middleware in `Startup.Configure`.</span></span>

```csharp
app.UseHealthChecks("/health");
```

<span data-ttu-id="7a2c2-499">Stellen Sie sicher, dass die mit der Verbindungszeichenfolge angegebene Datenbank in der SQL Server-Instanz nicht vorhanden ist, um das `DbContext`-Testszenario mithilfe der Beispiel-App ausführen zu können.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-499">To run the `DbContext` probe scenario using the sample app, confirm that the database specified by the connection string doesn't exist in the SQL Server instance.</span></span> <span data-ttu-id="7a2c2-500">Falls die Datenbank vorhanden ist, löschen Sie sie.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-500">If the database exists, delete it.</span></span>

<span data-ttu-id="7a2c2-501">Führen Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-501">Execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario dbcontext
```

<span data-ttu-id="7a2c2-502">Wenn die App ausgeführt wird, überprüfen Sie den Integritätsstatus, indem Sie in einem Browser eine Anforderung an den `/health`-Endpunkt senden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-502">After the app is running, check the health status by making a request to the `/health` endpoint in a browser.</span></span> <span data-ttu-id="7a2c2-503">Datenbank und `AppDbContext` sind nicht vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-503">The database and `AppDbContext` don't exist, so app provides the following response:</span></span>

```
Unhealthy
```

<span data-ttu-id="7a2c2-504">Fordern Sie die Beispiel-App auf, die Datenbank zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-504">Trigger the sample app to create the database.</span></span> <span data-ttu-id="7a2c2-505">Senden Sie eine `/createdatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-505">Make a request to `/createdatabase`.</span></span> <span data-ttu-id="7a2c2-506">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-506">The app responds:</span></span>

```
Creating the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="7a2c2-507">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-507">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="7a2c2-508">Datenbank und Kontext sind vorhanden, also sendet die App die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-508">The database and context exist, so app responds:</span></span>

```
Healthy
```

<span data-ttu-id="7a2c2-509">Fordern Sie die Beispiel-App auf, die Datenbank zu löschen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-509">Trigger the sample app to delete the database.</span></span> <span data-ttu-id="7a2c2-510">Senden Sie eine `/deletedatabase`-Anforderung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-510">Make a request to `/deletedatabase`.</span></span> <span data-ttu-id="7a2c2-511">Die App sendet die folgende Antwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-511">The app responds:</span></span>

```
Deleting the database...
Done!
Navigate to /health to see the health status.
```

<span data-ttu-id="7a2c2-512">Senden Sie eine Anforderung an den `/health`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-512">Make a request to the `/health` endpoint.</span></span> <span data-ttu-id="7a2c2-513">Die App meldet einen fehlerhaften Status:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-513">The app provides an unhealthy response:</span></span>

```
Unhealthy
```

## <a name="separate-readiness-and-liveness-probes"></a><span data-ttu-id="7a2c2-514">Separate Tests für Bereitschaft und Lebendigkeit</span><span class="sxs-lookup"><span data-stu-id="7a2c2-514">Separate readiness and liveness probes</span></span>

<span data-ttu-id="7a2c2-515">In einigen Hostingszenarien wird ein Integritätsprüfungspaar verwendet, bei dem zwischen zwei App-Status unterschieden wird:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-515">In some hosting scenarios, a pair of health checks are used that distinguish two app states:</span></span>

* <span data-ttu-id="7a2c2-516">Die App funktioniert, ist aber noch nicht für den Empfang von Anforderungen bereit.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-516">The app is functioning but not yet ready to receive requests.</span></span> <span data-ttu-id="7a2c2-517">Dieser Status gibt die *Bereitschaft* der App wieder.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-517">This state is the app's *readiness*.</span></span>
* <span data-ttu-id="7a2c2-518">Die App funktioniert und antwortet auf Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-518">The app is functioning and responding to requests.</span></span> <span data-ttu-id="7a2c2-519">Dieser Status gibt die *Lebendigkeit* der App wieder.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-519">This state is the app's *liveness*.</span></span>

<span data-ttu-id="7a2c2-520">Die Bereitschaftsprüfung führt in der Regel eine Reihe umfassenderer und zeitaufwendigerer Überprüfungen durch, um zu ermitteln, ob alle Subsysteme und Ressourcen der App verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-520">The readiness check usually performs a more extensive and time-consuming set of checks to determine if all of the app's subsystems and resources are available.</span></span> <span data-ttu-id="7a2c2-521">Eine Lebendigkeitsprüfung führt nur eine schnelle Überprüfung aus, um zu ermitteln, ob die App für die Verarbeitung von Anforderungen verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-521">A liveness check merely performs a quick check to determine if the app is available to process requests.</span></span> <span data-ttu-id="7a2c2-522">Nachdem die App die Bereitschaftsprüfung einmal bestanden hat, muss die App nicht weiter mit diesen Prüfungen belastet werden – weitere Prüfungen müssen dann nur noch für die Lebendigkeit erfolgen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-522">After the app passes its readiness check, there's no need to burden the app further with the expensive set of readiness checks&mdash;further checks only require checking for liveness.</span></span>

<span data-ttu-id="7a2c2-523">Die Beispiel-App enthält eine Integritätsprüfung, um den Abschluss eines Starttasks mit langer Ausführungsdauer in einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) zu melden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-523">The sample app contains a health check to report the completion of long-running startup task in a [Hosted Service](xref:fundamentals/host/hosted-services).</span></span> <span data-ttu-id="7a2c2-524">`StartupHostedServiceHealthCheck` macht die Eigenschaft `StartupTaskCompleted` verfügbar, die der gehostete Dienst auf `true` festlegen kann, wenn der Task mit langer Ausführungsdauer abgeschlossen ist (*StartupHostedServiceHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-524">The `StartupHostedServiceHealthCheck` exposes a property, `StartupTaskCompleted`, that the hosted service can set to `true` when its long-running task is finished (*StartupHostedServiceHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/StartupHostedServiceHealthCheck.cs?name=snippet1&highlight=7-11)]

<span data-ttu-id="7a2c2-525">Der Hintergrundtask mit langer Ausführungsdauer wird von einem [gehosteten Dienst](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*) gestartet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-525">The long-running background task is started by a [Hosted Service](xref:fundamentals/host/hosted-services) (*Services/StartupHostedService*).</span></span> <span data-ttu-id="7a2c2-526">Nach Abschluss des Tasks wird `StartupHostedServiceHealthCheck.StartupTaskCompleted` auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-526">At the conclusion of the task, `StartupHostedServiceHealthCheck.StartupTaskCompleted` is set to `true`:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/Services/StartupHostedService.cs?name=snippet1&highlight=18-20)]

<span data-ttu-id="7a2c2-527">Die Integritätsprüfung wird mit <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> zusammen mit dem gehosteten Dienst in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-527">The health check is registered with <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> in `Startup.ConfigureServices` along with the hosted service.</span></span> <span data-ttu-id="7a2c2-528">Da der gehostete Dienst die Eigenschaft in der Integritätsprüfung festlegen muss, wird die Integritätsprüfung ebenfalls im Dienstcontainer (*LivenessProbeStartup.cs*) registriert:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-528">Because the hosted service must set the property on the health check, the health check is also registered in the service container (*LivenessProbeStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7a2c2-529">Rufen Sie die Middleware für Integritätsprüfungen in der App-Verarbeitungspipeline in `Startup.Configure` auf.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-529">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="7a2c2-530">In der Beispiel-App werden die Integritätsprüfungs-Endpunkte in `/health/ready` für die Bereitschaftsprüfung und in `/health/live` für die Lebendigkeitsprüfung erstellt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-530">In the sample app, the health check endpoints are created at `/health/ready` for the readiness check and `/health/live` for the liveness check.</span></span> <span data-ttu-id="7a2c2-531">Die Bereitschaftsprüfung filtert auf Integritätsprüfungen mit dem `ready`-Tag.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-531">The readiness check filters health checks to the health check with the `ready` tag.</span></span> <span data-ttu-id="7a2c2-532">Die Lebendigkeitsprüfung filtert `StartupHostedServiceHealthCheck` durch Rückgabe von `false` in [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) heraus (weitere Informationen finden Sie unter [Filtern von Integritätsprüfungen](#filter-health-checks)):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-532">The liveness check filters out the `StartupHostedServiceHealthCheck` by returning `false` in the [HealthCheckOptions.Predicate](xref:Microsoft.AspNetCore.Diagnostics.HealthChecks.HealthCheckOptions.Predicate) (for more information, see [Filter health checks](#filter-health-checks)):</span></span>

```csharp
app.UseHealthChecks("/health/ready", new HealthCheckOptions()
{
    Predicate = (check) => check.Tags.Contains("ready"), 
});

app.UseHealthChecks("/health/live", new HealthCheckOptions()
{
    Predicate = (_) => false
});
```

<span data-ttu-id="7a2c2-533">Um das Szenario für die Konfiguration von Bereitschafts-/Lebendigkeitsprüfungen mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-533">To run the readiness/liveness configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario liveness
```

<span data-ttu-id="7a2c2-534">Besuchen Sie `/health/ready` mehrmals in einem Browser, bis 15 Sekunden verstrichen sind.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-534">In a browser, visit `/health/ready` several times until 15 seconds have passed.</span></span> <span data-ttu-id="7a2c2-535">Die Integritätsprüfung meldet während der ersten 15 Sekunden *Fehlerhaft*.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-535">The health check reports *Unhealthy* for the first 15 seconds.</span></span> <span data-ttu-id="7a2c2-536">Nach 15 Sekunden meldet der Endpunkt *Fehlerfrei*, was darauf hindeutet, dass die Ausführung des Tasks mit langer Ausführungsdauer durch den gehosteten Dienst abgeschlossen wurde.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-536">After 15 seconds, the endpoint reports *Healthy*, which reflects the completion of the long-running task by the hosted service.</span></span>

<span data-ttu-id="7a2c2-537">In diesem Beispiel wird auch ein Herausgeber der Integritätsprüfung erstellt (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung), der die erste Bereitschaftsprüfung mit einer Verzögerung von zwei Sekunden ausführt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-537">This example also creates a Health Check Publisher (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation) that runs the first readiness check with a two second delay.</span></span> <span data-ttu-id="7a2c2-538">Weitere Informationen finden Sie im Abschnitt [Herausgeber der Integritätsprüfung](#health-check-publisher).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-538">For more information, see the [Health Check Publisher](#health-check-publisher) section.</span></span>

### <a name="kubernetes-example"></a><span data-ttu-id="7a2c2-539">Kubernetes-Beispiel</span><span class="sxs-lookup"><span data-stu-id="7a2c2-539">Kubernetes example</span></span>

<span data-ttu-id="7a2c2-540">Die Verwendung von Bereitschafts- und Lebendigkeitstests ist in Umgebungen wie [Kubernetes](https://kubernetes.io/) sehr nützlich.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-540">Using separate readiness and liveness checks is useful in an environment such as [Kubernetes](https://kubernetes.io/).</span></span> <span data-ttu-id="7a2c2-541">In Kubernetes muss eine App möglicherweise zeitaufwendige Startaufgaben ausführen, bevor sie Anforderungen wie z.B. das Testen der Verfügbarkeit der zugrunde liegenden Datenbank annehmen kann.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-541">In Kubernetes, an app might be required to perform time-consuming startup work before accepting requests, such as a test of the underlying database availability.</span></span> <span data-ttu-id="7a2c2-542">Durch Verwendung separater Überprüfungen kann der Orchestrator unterscheiden, ob eine App funktioniert, aber noch nicht bereit ist, oder ob die App nicht gestartet wurde.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-542">Using separate checks allows the orchestrator to distinguish whether the app is functioning but not yet ready or if the app has failed to start.</span></span> <span data-ttu-id="7a2c2-543">Weitere Informationen zu Bereitschafts- und Lebendigkeitstests in Kubernetes finden Sie in der Kubernetes-Dokumentation unter [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) (Konfigurieren von Lebendigkeits- und Bereitschaftstests).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-543">For more information on readiness and liveness probes in Kubernetes, see [Configure Liveness and Readiness Probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) in the Kubernetes documentation.</span></span>

<span data-ttu-id="7a2c2-544">Das folgende Beispiel veranschaulicht die Konfiguration eines Bereitschaftstests in Kubernetes:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-544">The following example demonstrates a Kubernetes readiness probe configuration:</span></span>

```
spec:
  template:
  spec:
    readinessProbe:
      # an http probe
      httpGet:
        path: /health/ready
        port: 80
      # length of time to wait for a pod to initialize
      # after pod startup, before applying health checking
      initialDelaySeconds: 30
      timeoutSeconds: 1
    ports:
      - containerPort: 80
```

## <a name="metric-based-probe-with-a-custom-response-writer"></a><span data-ttu-id="7a2c2-545">Metrikbasierter Test mit einem benutzerdefinierten Antwortwriter</span><span class="sxs-lookup"><span data-stu-id="7a2c2-545">Metric-based probe with a custom response writer</span></span>

<span data-ttu-id="7a2c2-546">Die Beispiel-App veranschaulicht eine Arbeitsspeicher-Integritätsprüfung mit einem benutzerdefinierten Antwortwriter.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-546">The sample app demonstrates a memory health check with a custom response writer.</span></span>

<span data-ttu-id="7a2c2-547">`MemoryHealthCheck` meldet einen Fehlerstatus, wenn die App mehr Arbeitsspeicher verwendet, als für den Schwellenwert festgelegt wurde (1 GB in der Beispiel-App).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-547">`MemoryHealthCheck` reports an unhealthy status if the app uses more than a given threshold of memory (1 GB in the sample app).</span></span> <span data-ttu-id="7a2c2-548">Das <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> enthält Garbage Collector-Informationen (GC) für die App (*MemoryHealthCheck.cs*):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-548">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> includes Garbage Collector (GC) information for the app (*MemoryHealthCheck.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/MemoryHealthCheck.cs?name=snippet1)]

<span data-ttu-id="7a2c2-549">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-549">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-550">Die Integritätsprüfung wird nicht durch Übergabe an <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*> aktiviert, stattdessen wird `MemoryHealthCheck` als Dienst registriert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-550">Instead of enabling the health check by passing it to <xref:Microsoft.Extensions.DependencyInjection.HealthChecksBuilderAddCheckExtensions.AddCheck*>, the `MemoryHealthCheck` is registered as a service.</span></span> <span data-ttu-id="7a2c2-551">Alle bei <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registrierten Dienste stehen für die Dienste und Middleware für Integritätsprüfungen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-551">All <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> registered services are available to the health check services and middleware.</span></span> <span data-ttu-id="7a2c2-552">Es wird empfohlen, Integritätsprüfungsdienste als Singleton-Dienste zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-552">We recommend registering health check services as Singleton services.</span></span>

<span data-ttu-id="7a2c2-553">In der Beispiel-App (*CustomWriterStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-553">In the sample app (*CustomWriterStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="7a2c2-554">Rufen Sie die Middleware für Integritätsprüfungen in der App-Verarbeitungspipeline in `Startup.Configure` auf.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-554">Call Health Checks Middleware in the app processing pipeline in `Startup.Configure`.</span></span> <span data-ttu-id="7a2c2-555">Ein `WriteResponse`-Delegat wird in der `ResponseWriter`-Eigenschaft angegeben, um eine benutzerdefinierte JSON-Antwort auszugeben, wenn die Integritätsprüfung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-555">A `WriteResponse` delegate is provided to the `ResponseWriter` property to output a custom JSON response when the health check executes:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseHealthChecks("/health", new HealthCheckOptions()
    {
        // This custom writer formats the detailed status as JSON.
        ResponseWriter = WriteResponse
    });
}
```

<span data-ttu-id="7a2c2-556">Die `WriteResponse`-Methode formatiert das `CompositeHealthCheckResult` als JSON-Objekt und führt zu folgender JSON-Ausgabe für die Integritätsprüfungsantwort:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-556">The `WriteResponse` method formats the `CompositeHealthCheckResult` into a JSON object and yields JSON output for the health check response:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/CustomWriterStartup.cs?name=snippet_WriteResponse)]

<span data-ttu-id="7a2c2-557">Um den metrikbasierten Test mit benutzerdefinierter Ausgabe des Antwortwriters mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-557">To run the metric-based probe with custom response writer output using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario writer
```

> [!NOTE]
> <span data-ttu-id="7a2c2-558">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Szenarien für metrikbasierte Integritätsprüfungen, einschließlich Prüfungen des Datenträgerspeichers und Lebendigkeitsprüfungen mit Maximalwert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-558">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes metric-based health check scenarios, including disk storage and maximum value liveness checks.</span></span>
>
> <span data-ttu-id="7a2c2-559">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-559">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="filter-by-port"></a><span data-ttu-id="7a2c2-560">Filtern nach Port</span><span class="sxs-lookup"><span data-stu-id="7a2c2-560">Filter by port</span></span>

<span data-ttu-id="7a2c2-561">Wenn <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> mit einem Port aufgerufen wird, werden Anforderungen durch Integritätsprüfungen auf den angegebenen Port beschränkt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-561">Calling <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> with a port restricts health check requests to the port specified.</span></span> <span data-ttu-id="7a2c2-562">Dieses Vorgehen wird normalerweise in einer Containerumgebung angewendet, um einen Port für Überwachungsdienste verfügbar zu machen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-562">This is typically used in a container environment to expose a port for monitoring services.</span></span>

<span data-ttu-id="7a2c2-563">Die Beispiel-App konfiguriert den Port mithilfe des [Umgebungsvariablen-Konfigurationsanbieters](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-563">The sample app configures the port using the [Environment Variable Configuration Provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider).</span></span> <span data-ttu-id="7a2c2-564">Der Port wird in der Datei *launchSettings.json* festgelegt und über eine Umgebungsvariable an den Konfigurationsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-564">The port is set in the *launchSettings.json* file and passed to the configuration provider via an environment variable.</span></span> <span data-ttu-id="7a2c2-565">Sie müssen den Server auch so konfigurieren, dass er am Verwaltungsport auf Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-565">You must also configure the server to listen to requests on the management port.</span></span>

<span data-ttu-id="7a2c2-566">Um die Beispiel-App zum Veranschaulichen der Konfiguration des Verwaltungsports zu verwenden, erstellen Sie die Datei *launchSettings.json* in einem *Eigenschaften*-Ordner.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-566">To use the sample app to demonstrate management port configuration, create the *launchSettings.json* file in a *Properties* folder.</span></span>

<span data-ttu-id="7a2c2-567">Die folgende *Properties/launchSettings.json*-Datei in der Beispiel-App ist in den Projektdateien der Beispiel-App nicht vorhanden und muss manuell erstellt werden:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-567">The following *Properties/launchSettings.json* file in the sample app isn't included in the sample app's project files and must be created manually:</span></span>

```json
{
  "profiles": {
    "SampleApp": {
      "commandName": "Project",
      "commandLineArgs": "",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "ASPNETCORE_URLS": "http://localhost:5000/;http://localhost:5001/",
        "ASPNETCORE_MANAGEMENTPORT": "5001"
      },
      "applicationUrl": "http://localhost:5000/"
    }
  }
}
```

<span data-ttu-id="7a2c2-568">Registrieren Sie Integritätsprüfungsdienste mit <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-568">Register health check services with <xref:Microsoft.Extensions.DependencyInjection.HealthCheckServiceCollectionExtensions.AddHealthChecks*> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="7a2c2-569">Der Aufruf von <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> gibt den Verwaltungsport an (*ManagementPortStartup.cs*):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-569">The call to <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> specifies the management port (*ManagementPortStartup.cs*):</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ManagementPortStartup.cs?name=snippet1&highlight=17)]

> [!NOTE]
> <span data-ttu-id="7a2c2-570">Sie können es vermeiden, die Datei *launchSettings.json* in der Beispiel-App erstellen zu müssen, indem Sie die URLs und den Verwaltungsport explizit im Code festlegen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-570">You can avoid creating the *launchSettings.json* file in the sample app by setting the URLs and management port explicitly in code.</span></span> <span data-ttu-id="7a2c2-571">Fügen Sie in *Program.cs* an der Stelle, an der <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> erstellt wird, einen Aufruf von <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> hinzu, und geben Sie den normalen Antwortendpunkt der App und den Endpunkt des Verwaltungsports an.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-571">In *Program.cs* where the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> is created, add a call to <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*> and provide the app's normal response endpoint and the management port endpoint.</span></span> <span data-ttu-id="7a2c2-572">Geben Sie in *ManagementPortStartup.cs* an der Stelle, an der <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> aufgerufen wird, explizit den Verwaltungsport an.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-572">In *ManagementPortStartup.cs* where <xref:Microsoft.AspNetCore.Builder.HealthCheckApplicationBuilderExtensions.UseHealthChecks*> is called, specify the management port explicitly.</span></span>
>
> <span data-ttu-id="7a2c2-573">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-573">*Program.cs*:</span></span>
>
> ```csharp
> return new WebHostBuilder()
>     .UseConfiguration(config)
>     .UseUrls("http://localhost:5000/;http://localhost:5001/")
>     .ConfigureLogging(builder =>
>     {
>         builder.SetMinimumLevel(LogLevel.Trace);
>         builder.AddConfiguration(config);
>         builder.AddConsole();
>     })
>     .UseKestrel()
>     .UseStartup(startupType)
>     .Build();
> ```
>
> <span data-ttu-id="7a2c2-574">*ManagementPortStartup.cs*:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-574">*ManagementPortStartup.cs*:</span></span>
>
> ```csharp
> app.UseHealthChecks("/health", port: 5001);
> ```

<span data-ttu-id="7a2c2-575">Um das Szenario für die Konfiguration des Verwaltungsports mithilfe der Beispiel-App auszuführen, verwenden Sie den folgenden Befehl aus dem Ordner des Projekts in einer Befehlsshell:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-575">To run the management port configuration scenario using the sample app, execute the following command from the project's folder in a command shell:</span></span>

```dotnetcli
dotnet run --scenario port
```

## <a name="distribute-a-health-check-library"></a><span data-ttu-id="7a2c2-576">Verteilen einer Integritätsprüfungsbibliothek</span><span class="sxs-lookup"><span data-stu-id="7a2c2-576">Distribute a health check library</span></span>

<span data-ttu-id="7a2c2-577">So verteilen Sie eine Integritätsprüfung als Bibliothek:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-577">To distribute a health check as a library:</span></span>

1. <span data-ttu-id="7a2c2-578">Schreiben Sie eine Integritätsprüfung, die die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck>-Schnittstelle als eigenständige Klasse implementiert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-578">Write a health check that implements the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheck> interface as a standalone class.</span></span> <span data-ttu-id="7a2c2-579">Die Klasse kann [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI), Typaktivierung und [benannte Optionen](xref:fundamentals/configuration/options) verwenden, um auf Konfigurationsdaten zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-579">The class can rely on [dependency injection (DI)](xref:fundamentals/dependency-injection), type activation, and [named options](xref:fundamentals/configuration/options) to access configuration data.</span></span>

   <span data-ttu-id="7a2c2-580">In der Integritätsprüfungslogik von `CheckHealthAsync` gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-580">In the health checks logic of `CheckHealthAsync`:</span></span>

   * <span data-ttu-id="7a2c2-581">`data1` und `data2` werden in der Methode verwendet, um die Integritätsprüfungslogik des Tests auszuführen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-581">`data1` and `data2` are used in the method to run the probe's health check logic.</span></span>
   * <span data-ttu-id="7a2c2-582">`AccessViolationException` wird verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-582">`AccessViolationException` is handled.</span></span>

   <span data-ttu-id="7a2c2-583">Wenn eine <xref:System.AccessViolationException> auftritt, wird der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> mit dem <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> zurückgegeben, damit Benutzer den Integritätsprüfungs-Fehlerstatus konfigurieren können.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-583">When an <xref:System.AccessViolationException> occurs, the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckRegistration.FailureStatus> is returned with the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckResult> to allow users to configure the health checks failure status.</span></span>

   ```csharp
   using System;
   using System.Threading;
   using System.Threading.Tasks;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public class ExampleHealthCheck : IHealthCheck
   {
       private readonly string _data1;
       private readonly int? _data2;

       public ExampleHealthCheck(string data1, int? data2)
       {
           _data1 = data1 ?? throw new ArgumentNullException(nameof(data1));
           _data2 = data2 ?? throw new ArgumentNullException(nameof(data2));
       }

       public async Task<HealthCheckResult> CheckHealthAsync(
           HealthCheckContext context, CancellationToken cancellationToken)
       {
           try
           {
               return HealthCheckResult.Healthy();
           }
           catch (AccessViolationException ex)
           {
               return new HealthCheckResult(
                   context.Registration.FailureStatus,
                   description: "An access violation occurred during the check.",
                   exception: ex,
                   data: null);
           }
       }
   }
   ```

1. <span data-ttu-id="7a2c2-584">Schreiben Sie eine Erweiterungsmethode mit Parametern, die von der nutzenden App in ihrer `Startup.Configure`-Methode aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-584">Write an extension method with parameters that the consuming app calls in its `Startup.Configure` method.</span></span> <span data-ttu-id="7a2c2-585">Nehmen Sie im folgenden Beispiel die folgende Signatur für die Integritätsprüfungsmethode an:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-585">In the following example, assume the following health check method signature:</span></span>

   ```csharp
   ExampleHealthCheck(string, string, int )
   ```

   <span data-ttu-id="7a2c2-586">Die oben stehende Signatur gibt an, dass `ExampleHealthCheck` weitere Daten benötigt, um die Testlogik für die Integritätsprüfung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-586">The preceding signature indicates that the `ExampleHealthCheck` requires additional data to process the health check probe logic.</span></span> <span data-ttu-id="7a2c2-587">Die Daten werden für den Delegaten bereitgestellt, der zum Erstellen der Integritätsprüfungsinstanz verwendet wird, wenn die Integritätsprüfung bei einer Erweiterungsmethode registriert wird.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-587">The data is provided to the delegate used to create the health check instance when the health check is registered with an extension method.</span></span> <span data-ttu-id="7a2c2-588">Im folgenden Beispiel gibt der Aufrufer optional Folgendes an:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-588">In the following example, the caller specifies optional:</span></span>

   * <span data-ttu-id="7a2c2-589">Name der Integritätsprüfung (`name`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-589">health check name (`name`).</span></span> <span data-ttu-id="7a2c2-590">Wenn der Wert `null` ist, wird `example_health_check` verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-590">If `null`, `example_health_check` is used.</span></span>
   * <span data-ttu-id="7a2c2-591">string-Datenpunkt für die Integritätsprüfung (`data1`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-591">string data point for the health check (`data1`).</span></span>
   * <span data-ttu-id="7a2c2-592">integer-Datenpunkt für die Integritätsprüfung (`data2`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-592">integer data point for the health check (`data2`).</span></span> <span data-ttu-id="7a2c2-593">Wenn der Wert `null` ist, wird `1` verwendet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-593">If `null`, `1` is used.</span></span>
   * <span data-ttu-id="7a2c2-594">Fehlerstatus (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-594">failure status (<xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus>).</span></span> <span data-ttu-id="7a2c2-595">Die Standardeinstellung ist `null`.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-595">The default is `null`.</span></span> <span data-ttu-id="7a2c2-596">Wenn `null`, wird [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) als Fehlerstatus gemeldet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-596">If `null`, [HealthStatus.Unhealthy](xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus) is reported for a failure status.</span></span>
   * <span data-ttu-id="7a2c2-597">Tags (`IEnumerable<string>`).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-597">tags (`IEnumerable<string>`).</span></span>

   ```csharp
   using System.Collections.Generic;
   using Microsoft.Extensions.Diagnostics.HealthChecks;

   public static class ExampleHealthCheckBuilderExtensions
   {
       const string DefaultName = "example_health_check";

       public static IHealthChecksBuilder AddExampleHealthCheck(
           this IHealthChecksBuilder builder,
           string name = default,
           string data1,
           int data2 = 1,
           HealthStatus? failureStatus = default,
           IEnumerable<string> tags = default)
       {
           return builder.Add(new HealthCheckRegistration(
               name ?? DefaultName,
               sp => new ExampleHealthCheck(data1, data2),
               failureStatus,
               tags));
       }
   }
   ```

## <a name="health-check-publisher"></a><span data-ttu-id="7a2c2-598">Herausgeber der Integritätsprüfung</span><span class="sxs-lookup"><span data-stu-id="7a2c2-598">Health Check Publisher</span></span>

<span data-ttu-id="7a2c2-599">Wenn dem Dienstcontainer ein <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> hinzugefügt wird, führt das Integritätsprüfungssystem Ihre Integritätsprüfungen regelmäßig aus und ruft `PublishAsync` mit dem Ergebnis auf.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-599">When an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> is added to the service container, the health check system periodically executes your health checks and calls `PublishAsync` with the result.</span></span> <span data-ttu-id="7a2c2-600">Dies ist nützlich in einem Szenario mit pushbasiertem Integritätsüberwachungssystem, in dem jeder Prozess das Überwachungssystem regelmäßig aufrufen muss, um die Integrität zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-600">This is useful in a push-based health monitoring system scenario that expects each process to call the monitoring system periodically in order to determine health.</span></span>

<span data-ttu-id="7a2c2-601">Die <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Schnittstelle weist eine einzige Methode auf:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-601">The <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> interface has a single method:</span></span>

```csharp
Task PublishAsync(HealthReport report, CancellationToken cancellationToken);
```

<span data-ttu-id="7a2c2-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> ermöglichen Ihnen, Folgendes festzulegen:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-602"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions> allow you to set:</span></span>

* <span data-ttu-id="7a2c2-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; Die nach der App angewendete anfängliche Verzögerung beginnt vor der Ausführung von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-603"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> &ndash; The initial delay applied after the app starts before executing <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="7a2c2-604">Die Verzögerung wird einmal beim Start angewendet und gilt nicht für die nachfolgenden Iterationen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-604">The delay is applied once at startup and doesn't apply to subsequent iterations.</span></span> <span data-ttu-id="7a2c2-605">Der Standardwert beträgt fünf Sekunden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-605">The default value is five seconds.</span></span>
* <span data-ttu-id="7a2c2-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; Der Zeitraum der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Ausführung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-606"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> &ndash; The period of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> execution.</span></span> <span data-ttu-id="7a2c2-607">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-607">The default value is 30 seconds.</span></span>
* <span data-ttu-id="7a2c2-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; Wenn <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> `null` ist (Standard), führt der Herausgeber der Integritätsprüfung alle registrierten Integritätsüberprüfungen durch.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-608"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> &ndash; If <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Predicate> is `null` (default), the health check publisher service runs all registered health checks.</span></span> <span data-ttu-id="7a2c2-609">Um eine Teilmenge von Integritätsprüfungen auszuführen, stellen Sie eine Funktion bereit, die die Menge der Prüfungen filtert.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-609">To run a subset of health checks, provide a function that filters the set of checks.</span></span> <span data-ttu-id="7a2c2-610">Das Prädikat wird in jedem Zeitraum ausgewertet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-610">The predicate is evaluated each period.</span></span>
* <span data-ttu-id="7a2c2-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; Das Timeout für die Ausführung der Integritätsprüfungen für alle <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-611"><xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Timeout> &ndash; The timeout for executing the health checks for all <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instances.</span></span> <span data-ttu-id="7a2c2-612">Verwenden Sie <xref:System.Threading.Timeout.InfiniteTimeSpan> zur Ausführung ohne Timeout.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-612">Use <xref:System.Threading.Timeout.InfiniteTimeSpan> to execute without a timeout.</span></span> <span data-ttu-id="7a2c2-613">Der Standardwert ist 30 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-613">The default value is 30 seconds.</span></span>

> [!WARNING]
> <span data-ttu-id="7a2c2-614">Im Release ASP.NET Core 2.2 wird die Einstellung <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> nicht von der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung berücksichtigt; sie legt den Wert von <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay> fest.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-614">In the ASP.NET Core 2.2 release, setting <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Period> isn't honored by the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation; it sets the value of <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherOptions.Delay>.</span></span> <span data-ttu-id="7a2c2-615">Dieses Problem wurde in ASP.NET Core 3.0 behandelt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-615">This issue has been addressed in ASP.NET Core 3.0.</span></span>

<span data-ttu-id="7a2c2-616">In der Beispiel-App ist `ReadinessPublisher` eine <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-616">In the sample app, `ReadinessPublisher` is an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation.</span></span> <span data-ttu-id="7a2c2-617">Der Integritätsprüfungsstatus wird für jede Überprüfung auf folgende Weise protokolliert:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-617">The health check status is logged for each check as either:</span></span>

* <span data-ttu-id="7a2c2-618">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>), wenn der Status der Integritätsprüfungen <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-618">Information (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>) if the health checks status is <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Healthy>.</span></span>
* <span data-ttu-id="7a2c2-619">Fehler (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>), wenn der Status <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> oder <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy> lautet.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-619">Error (<xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError*>) if the status is either <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Degraded> or <xref:Microsoft.Extensions.Diagnostics.HealthChecks.HealthStatus.Unhealthy>.</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/ReadinessPublisher.cs?name=snippet_ReadinessPublisher&highlight=18-27)]

<span data-ttu-id="7a2c2-620">Im Beispiel `LivenessProbeStartup` der Beispiel-App `StartupHostedService` hat die Bereitschaftsprüfung eine Startverzögerung von zwei Sekunden und führt die Prüfung alle 30 Sekunden aus.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-620">In the sample app's `LivenessProbeStartup` example, the `StartupHostedService` readiness check has a two second startup delay and runs the check every 30 seconds.</span></span> <span data-ttu-id="7a2c2-621">Zum Aktivieren der <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> Implementierung registriert das Beispiel `ReadinessPublisher` als Singletondienst im [Abhängigkeitsinjektionscontainer (Dependency Injection, DI)](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="7a2c2-621">To activate the <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> implementation, the sample registers `ReadinessPublisher` as a singleton service in the [dependency injection (DI)](xref:fundamentals/dependency-injection) container:</span></span>

[!code-csharp[](health-checks/samples/2.x/HealthChecksSample/LivenessProbeStartup.cs?name=snippet_ConfigureServices&highlight=12-17,28)]

> [!NOTE]
> <span data-ttu-id="7a2c2-622">Die folgende Problemumgehung ermöglicht das Hinzufügen einer <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher>-Instanz zum Dienstcontainer, wenn einer oder mehrere andere gehostete Dienste der App bereits hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-622">The following workaround permits adding an <xref:Microsoft.Extensions.Diagnostics.HealthChecks.IHealthCheckPublisher> instance to the service container when one or more other hosted services have already been added to the app.</span></span> <span data-ttu-id="7a2c2-623">Diese Problemumgehung ist in ASP.NET Core 3.0 nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-623">This workaround won't isn't required in ASP.NET Core 3.0.</span></span>
>
> ```csharp
> private const string HealthCheckServiceAssembly =
>     "Microsoft.Extensions.Diagnostics.HealthChecks.HealthCheckPublisherHostedService";
>
> services.TryAddEnumerable(
>     ServiceDescriptor.Singleton(typeof(IHostedService),
>         typeof(HealthCheckPublisherOptions).Assembly
>             .GetType(HealthCheckServiceAssembly)));
> ```

> [!NOTE]
> <span data-ttu-id="7a2c2-624">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) enthält Herausgeber für verschiedene Systeme, einschließlich [Application Insights](/azure/application-insights/app-insights-overview).</span><span class="sxs-lookup"><span data-stu-id="7a2c2-624">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) includes publishers for several systems, including [Application Insights](/azure/application-insights/app-insights-overview).</span></span>
>
> <span data-ttu-id="7a2c2-625">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) wird von Microsoft nicht gewartet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-625">[AspNetCore.Diagnostics.HealthChecks](https://github.com/Xabaril/AspNetCore.Diagnostics.HealthChecks) isn't maintained or supported by Microsoft.</span></span>

## <a name="restrict-health-checks-with-mapwhen"></a><span data-ttu-id="7a2c2-626">Einschränken von Integritätsprüfungen mit MapWhen</span><span class="sxs-lookup"><span data-stu-id="7a2c2-626">Restrict health checks with MapWhen</span></span>

<span data-ttu-id="7a2c2-627">Verwenden Sie <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*>, um die Anforderungspipeline für Endpunkte von Integritätsprüfungen bedingt zu branchen.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-627">Use <xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> to conditionally branch the request pipeline for health check endpoints.</span></span>

<span data-ttu-id="7a2c2-628">Im folgenden Beispiel verzweigt `MapWhen` die Anforderungspipeline so, dass die Middleware für Integritätsprüfungen aktiviert wird, wenn eine GET-Anforderung für den Endpunkt `api/HealthCheck` empfangen wird:</span><span class="sxs-lookup"><span data-stu-id="7a2c2-628">In the following example, `MapWhen` branches the request pipeline to activate Health Checks Middleware if a GET request is received for the `api/HealthCheck` endpoint:</span></span>

```csharp
app.MapWhen(
    context => context.Request.Method == HttpMethod.Get.Method && 
        context.Request.Path.StartsWith("/api/HealthCheck"),
    builder => builder.UseHealthChecks());

app.UseMvc();
```

<span data-ttu-id="7a2c2-629">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index#use-run-and-map>.</span><span class="sxs-lookup"><span data-stu-id="7a2c2-629">For more information, see <xref:fundamentals/middleware/index#use-run-and-map>.</span></span>

::: moniker-end
