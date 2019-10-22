---
title: Hintergrundtasks mit gehosteten Diensten in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Hintergrundtasks mit gehosteten Diensten in ASP.NET Core implementieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/26/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: c1fbb5ae8ffc4ee506f42df6a4cbbe845b2b903d
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333658"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="23133-103">Hintergrundtasks mit gehosteten Diensten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="23133-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="23133-104">Von [Luke Latham](https://github.com/guardrex) und [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="23133-104">By [Luke Latham](https://github.com/guardrex) and [Jeow Li Huan](https://github.com/huan086)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="23133-105">In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="23133-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="23133-106">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="23133-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="23133-107">In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:</span><span class="sxs-lookup"><span data-stu-id="23133-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="23133-108">Hintergrundtasks, die auf einem Timer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="23133-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="23133-109">Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren.</span><span class="sxs-lookup"><span data-stu-id="23133-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="23133-110">Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="23133-111">Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="23133-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="23133-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="23133-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="23133-113">Die Beispiel-App wird in zwei Versionen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="23133-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="23133-114">Web Host &ndash; Der Webhost eignet sich für das Hosten von Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="23133-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="23133-115">Der in diesem Thema gezeigte Beispielcode stammt aus der Webhostversion des Beispiels.</span><span class="sxs-lookup"><span data-stu-id="23133-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="23133-116">Weitere Informationen finden Sie unter dem Thema [Webhost](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="23133-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="23133-117">Generischer Host &ndash; Der generische Host wurde in ASP.NET Core 2.1 neu eingeführt.</span><span class="sxs-lookup"><span data-stu-id="23133-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="23133-118">Weitere Informationen finden Sie unter dem Thema [Generischer Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="23133-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="23133-119">Vorlage „Workerdienst“</span><span class="sxs-lookup"><span data-stu-id="23133-119">Worker Service template</span></span>

<span data-ttu-id="23133-120">Die ASP.NET Core-Vorlage „Workerdienst“ dient als Ausgangspunkt für das Schreiben von Dienstanwendungen mit langer Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="23133-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="23133-121">Gehen Sie folgendermaßen vor, wenn Sie die Vorlage als Grundlage für eine Hosted Services-App verwenden möchten:</span><span class="sxs-lookup"><span data-stu-id="23133-121">To use the template as a basis for a hosted services app:</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

---

## <a name="package"></a><span data-ttu-id="23133-122">Package</span><span class="sxs-lookup"><span data-stu-id="23133-122">Package</span></span>

<span data-ttu-id="23133-123">Für ASP.NET Core-Apps wird implizit ein Paketverweis auf das Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="23133-123">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="23133-124">Die IHostedService-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="23133-124">IHostedService interface</span></span>

<span data-ttu-id="23133-125">Die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="23133-125">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="23133-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` enthält die Logik zum Starten des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="23133-126">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="23133-127">`StartAsync` wird *vor* folgenden Vorgängen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="23133-127">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="23133-128">Die App-Pipeline für die Anforderungsverarbeitung wird konfiguriert (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="23133-128">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="23133-129">Der Server wird gestartet, und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="23133-129">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="23133-130">Das Standardverhalten kann so geändert werden, dass der `StartAsync`-Vorgang des gehosteten Diensts ausgeführt wird, nachdem die Pipeline der App konfiguriert und `ApplicationStarted` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="23133-130">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="23133-131">Um das Standardverhalten zu ändern, fügen Sie den gehosteten Dienst (`VideosWatcher` im folgenden Beispiel) nach dem Aufruf von `ConfigureWebHostDefaults` hinzu:</span><span class="sxs-lookup"><span data-stu-id="23133-131">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Hosting;
  using Microsoft.Extensions.DependencyInjection;
  using Microsoft.Extensions.Hosting;

  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseStartup<Startup>();
              })
              .ConfigureServices(services =>
              {
                  services.AddHostedService<VideosWatcher>();
              });
  }
  ```

* <span data-ttu-id="23133-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; wird ausgelöst, wenn der Host das Herunterfahren ordnungsgemäß ausführt.</span><span class="sxs-lookup"><span data-stu-id="23133-132">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="23133-133">`StopAsync` enthält die Logik zum Beenden des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="23133-133">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="23133-134">Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="23133-134">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="23133-135">Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="23133-135">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="23133-136">Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="23133-136">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="23133-137">Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.</span><span class="sxs-lookup"><span data-stu-id="23133-137">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="23133-138">Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="23133-138">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="23133-139">Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="23133-139">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="23133-140">Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="23133-140">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="23133-141">Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="23133-141">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="23133-142">Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="23133-142">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="23133-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-143"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="23133-144">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="23133-144">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="23133-145">Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-145">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="23133-146">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="23133-146">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="23133-147">Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren.</span><span class="sxs-lookup"><span data-stu-id="23133-147">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="23133-148">Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="23133-148">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="23133-149">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="23133-149">BackgroundService</span></span>

<span data-ttu-id="23133-150">`BackgroundService` ist eine Basisklasse zur Implementierung eines <xref:Microsoft.Extensions.Hosting.IHostedService> mit langer Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="23133-150">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="23133-151">`BackgroundService` bietet die abstrakte `ExecuteAsync(CancellationToken stoppingToken)`-Methode, die die Dienstlogik enthält.</span><span class="sxs-lookup"><span data-stu-id="23133-151">`BackgroundService` provides the `ExecuteAsync(CancellationToken stoppingToken)` abstract method to contain the service's logic.</span></span> <span data-ttu-id="23133-152">Das `stoppingToken` wird aufgerufen, wenn [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="23133-152">The `stoppingToken` is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="23133-153">Die Implementierung dieser Methode gibt einen `Task` zurück, der die gesamte Lebensdauer des Hintergrunddiensts darstellt.</span><span class="sxs-lookup"><span data-stu-id="23133-153">The implementation of this method returns a `Task` that represents the entire lifetime of the background service.</span></span>

<span data-ttu-id="23133-154">Setzen Sie *optional* die unter `IHostedService` definierten Methoden außer Kraft, um den Code zum Starten und Herunterfahren auszuführen.</span><span class="sxs-lookup"><span data-stu-id="23133-154">In addition, *optionally* override the methods defined on `IHostedService` to run startup and shutdown code for your service:</span></span>

* <span data-ttu-id="23133-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` wird aufgerufen, wenn der Anwendungshost ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="23133-155">`StopAsync(CancellationToken cancellationToken)` &ndash; `StopAsync` is called when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="23133-156">`cancellationToken` wird signalisiert, wenn der Host beschließt, das Beenden des Diensts zu erzwingen.</span><span class="sxs-lookup"><span data-stu-id="23133-156">The `cancellationToken` is signalled when the host decides to forcibly terminate the service.</span></span> <span data-ttu-id="23133-157">Wenn diese Methode außer Kraft gesetzt wird, **müssen** Sie die Basisklassenmethode aufrufen (und `await`), um sicherzustellen, dass der Dienst ordnungsgemäß heruntergefahren wird.</span><span class="sxs-lookup"><span data-stu-id="23133-157">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service shuts down properly.</span></span>
* <span data-ttu-id="23133-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` wird aufgerufen, um den Hintergrunddienst zu starten.</span><span class="sxs-lookup"><span data-stu-id="23133-158">`StartAsync(CancellationToken cancellationToken)` &ndash; `StartAsync` is called to start the background service.</span></span> <span data-ttu-id="23133-159">`cancellationToken` wird signalisiert, wenn der Startprozess unterbrochen wird.</span><span class="sxs-lookup"><span data-stu-id="23133-159">The `cancellationToken` is signalled if the startup process is interrupted.</span></span> <span data-ttu-id="23133-160">Die Implementierung gibt einen `Task` zurück, der den Startprozess für den Dienst darstellt.</span><span class="sxs-lookup"><span data-stu-id="23133-160">The implementation returns a `Task` that represents the startup process for the service.</span></span> <span data-ttu-id="23133-161">Es werden keine Dienste gestartet, bis `Task` nicht abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="23133-161">No further services are started until this `Task` completes.</span></span> <span data-ttu-id="23133-162">Wenn diese Methode außer Kraft gesetzt wird, **müssen** Sie die Basisklassenmethode aufrufen (und `await`), um sicherzustellen, dass der Dienst ordnungsgemäß gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="23133-162">If this method is overridden, you **must** call (and `await`) the base class method to ensure the service starts properly.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="23133-163">Zeitlich festgelegte Hintergrundtasks</span><span class="sxs-lookup"><span data-stu-id="23133-163">Timed background tasks</span></span>

<span data-ttu-id="23133-164">Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="23133-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="23133-165">Der Timer löst die `DoWork`-Methode des Tasks aus.</span><span class="sxs-lookup"><span data-stu-id="23133-165">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="23133-166">Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:</span><span class="sxs-lookup"><span data-stu-id="23133-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="23133-167">Der Dienst wird in `IHostBuilder.ConfigureServices` (*Program.cs*) mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="23133-167">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="23133-168">Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask</span><span class="sxs-lookup"><span data-stu-id="23133-168">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="23133-169">Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in einem `BackgroundService` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-169">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="23133-170">Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.</span><span class="sxs-lookup"><span data-stu-id="23133-170">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="23133-171">Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="23133-171">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="23133-172">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="23133-172">In the following example:</span></span>

* <span data-ttu-id="23133-173">Der Dienst ist asynchron.</span><span class="sxs-lookup"><span data-stu-id="23133-173">The service is asynchronous.</span></span> <span data-ttu-id="23133-174">Die `DoWork`-Methode gibt `Task` zurück.</span><span class="sxs-lookup"><span data-stu-id="23133-174">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="23133-175">Zu Demonstrationszwecken wird in der `DoWork`-Methode eine Verzögerung von zehn Sekunden verwendet.</span><span class="sxs-lookup"><span data-stu-id="23133-175">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="23133-176">Ein <xref:Microsoft.Extensions.Logging.ILogger> wird in den Dienst eingefügt.</span><span class="sxs-lookup"><span data-stu-id="23133-176">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="23133-177">Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, damit die `DoWork`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="23133-177">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="23133-178">`DoWork` gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird:</span><span class="sxs-lookup"><span data-stu-id="23133-178">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="23133-179">Die Dienste werden in `IHostBuilder.ConfigureServices` (*Program.cs*) registriert.</span><span class="sxs-lookup"><span data-stu-id="23133-179">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="23133-180">Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="23133-180">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="23133-181">Hintergrundtasks in der Warteschlange</span><span class="sxs-lookup"><span data-stu-id="23133-181">Queued background tasks</span></span>

<span data-ttu-id="23133-182">Eine Warteschlange für Hintergrundtasks basiert auf dem <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>von .NET 4.x ([die Integration in ASP.NET Core ist vorläufig geplant](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="23133-182">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="23133-183">Im folgenden Beispiel für `QueueHostedService` gilt:</span><span class="sxs-lookup"><span data-stu-id="23133-183">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="23133-184">Die `BackgroundProcessing`-Methode gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird.</span><span class="sxs-lookup"><span data-stu-id="23133-184">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="23133-185">Hintergrundtasks in der Warteschlange werden aus dieser entfernt und in `BackgroundProcessing` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="23133-185">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
* <span data-ttu-id="23133-186">Auf Arbeitselemente wird gewartet, bevor der Dienst in `StopAsync` angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="23133-186">Work items are awaited before the service stops in `StopAsync`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="23133-187">Ein `MonitorLoop`-Dienst verarbeitet das Einreihen von Tasks in die Warteschlange für den gehosteten Dienst, wenn der `w`-Schlüssel auf einem Eingabegerät ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="23133-187">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="23133-188">Die `IBackgroundTaskQueue` wird in den `MonitorLoop`-Dienst eingefügt.</span><span class="sxs-lookup"><span data-stu-id="23133-188">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="23133-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` wird aufgerufen, um ein Arbeitselement in die Warteschlange einzureihen.</span><span class="sxs-lookup"><span data-stu-id="23133-189">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>
* <span data-ttu-id="23133-190">Das Arbeitselement simuliert eine Hintergrundaufgabe mit langer Ausführungszeit:</span><span class="sxs-lookup"><span data-stu-id="23133-190">The work item simulates a long-running background task:</span></span>
  * <span data-ttu-id="23133-191">Drei 5-Sekunden-Verzögerungen werden ausgeführt (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="23133-191">Three 5-second delays are executed (`Task.Delay`).</span></span>
  * <span data-ttu-id="23133-192">Eine `try-catch`-Anweisung fängt <xref:System.OperationCanceledException> auf, wenn der Task abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="23133-192">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="23133-193">Die Dienste werden in `IHostBuilder.ConfigureServices` (*Program.cs*) registriert.</span><span class="sxs-lookup"><span data-stu-id="23133-193">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="23133-194">Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="23133-194">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="23133-195">In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="23133-195">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="23133-196">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="23133-196">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="23133-197">In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:</span><span class="sxs-lookup"><span data-stu-id="23133-197">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="23133-198">Hintergrundtasks, die auf einem Timer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="23133-198">Background task that runs on a timer.</span></span>
* <span data-ttu-id="23133-199">Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren.</span><span class="sxs-lookup"><span data-stu-id="23133-199">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="23133-200">Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-200">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="23133-201">Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="23133-201">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="23133-202">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="23133-202">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="23133-203">Die Beispiel-App wird in zwei Versionen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="23133-203">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="23133-204">Web Host &ndash; Der Webhost eignet sich für das Hosten von Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="23133-204">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="23133-205">Der in diesem Thema gezeigte Beispielcode stammt aus der Webhostversion des Beispiels.</span><span class="sxs-lookup"><span data-stu-id="23133-205">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="23133-206">Weitere Informationen finden Sie unter dem Thema [Webhost](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="23133-206">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="23133-207">Generischer Host &ndash; Der generische Host wurde in ASP.NET Core 2.1 neu eingeführt.</span><span class="sxs-lookup"><span data-stu-id="23133-207">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="23133-208">Weitere Informationen finden Sie unter dem Thema [Generischer Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="23133-208">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="23133-209">Package</span><span class="sxs-lookup"><span data-stu-id="23133-209">Package</span></span>

<span data-ttu-id="23133-210">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) hinzu.</span><span class="sxs-lookup"><span data-stu-id="23133-210">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="23133-211">Die IHostedService-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="23133-211">IHostedService interface</span></span>

<span data-ttu-id="23133-212">Gehostete Dienste implementieren die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="23133-212">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="23133-213">Die Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="23133-213">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="23133-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` enthält die Logik zum Starten des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="23133-214">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="23133-215">Bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) wird `StartAsync` aufgerufen, nachdem der Server gestartet und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) ausgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="23133-215">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="23133-216">Bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) wird `StartAsync` aufgerufen, bevor `ApplicationStarted` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="23133-216">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="23133-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; wird ausgelöst, wenn der Host das Herunterfahren ordnungsgemäß ausführt.</span><span class="sxs-lookup"><span data-stu-id="23133-217">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="23133-218">`StopAsync` enthält die Logik zum Beenden des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="23133-218">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="23133-219">Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="23133-219">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="23133-220">Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="23133-220">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="23133-221">Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="23133-221">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="23133-222">Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.</span><span class="sxs-lookup"><span data-stu-id="23133-222">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="23133-223">Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="23133-223">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="23133-224">Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="23133-224">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="23133-225">Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="23133-225">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="23133-226">Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="23133-226">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="23133-227">Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="23133-227">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="23133-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-228"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="23133-229">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="23133-229">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="23133-230">Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-230">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="23133-231">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="23133-231">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="23133-232">Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren.</span><span class="sxs-lookup"><span data-stu-id="23133-232">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="23133-233">Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="23133-233">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="23133-234">Zeitlich festgelegte Hintergrundtasks</span><span class="sxs-lookup"><span data-stu-id="23133-234">Timed background tasks</span></span>

<span data-ttu-id="23133-235">Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="23133-235">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="23133-236">Der Timer löst die `DoWork`-Methode des Tasks aus.</span><span class="sxs-lookup"><span data-stu-id="23133-236">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="23133-237">Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:</span><span class="sxs-lookup"><span data-stu-id="23133-237">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="23133-238">Der Dienst wird in `Startup.ConfigureServices` mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="23133-238">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="23133-239">Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask</span><span class="sxs-lookup"><span data-stu-id="23133-239">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="23133-240">Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in `IHostedService` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="23133-240">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="23133-241">Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.</span><span class="sxs-lookup"><span data-stu-id="23133-241">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="23133-242">Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="23133-242">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="23133-243">Im folgenden Beispiel wird <xref:Microsoft.Extensions.Logging.ILogger> in den Dienst eingefügt:</span><span class="sxs-lookup"><span data-stu-id="23133-243">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="23133-244">Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, um die `DoWork`-Methode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="23133-244">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="23133-245">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="23133-245">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="23133-246">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="23133-246">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="23133-247">Hintergrundtasks in der Warteschlange</span><span class="sxs-lookup"><span data-stu-id="23133-247">Queued background tasks</span></span>

<span data-ttu-id="23133-248">Eine Warteschlange für Hintergrundtasks basiert auf dem <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> von .NET Framework 4.x ([die Integration in ASP.NET Core ist vorläufig geplant](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="23133-248">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="23133-249">In `QueueHostedService` werden Hintergrundaufgaben in der Warteschlange aus der Warteschlange entfernt und als <xref:Microsoft.Extensions.Hosting.BackgroundService>, eine Basisklasse zum Bereitstellen von `IHostedService` mit langer Ausführungszeit, ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="23133-249">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="23133-250">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="23133-250">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="23133-251">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="23133-251">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="23133-252">In der Modellklasse für die Indexseite:</span><span class="sxs-lookup"><span data-stu-id="23133-252">In the Index page model class:</span></span>

* <span data-ttu-id="23133-253">`IBackgroundTaskQueue` wird in den Konstruktor eingefügt und `Queue` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="23133-253">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="23133-254">Ein <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> wird eingefügt und `_serviceScopeFactory` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="23133-254">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="23133-255">Die Zuordnungsinstanz wird verwendet, um Instanzen von <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> zu erstellen. Dieser wird verwendet, um Dienste in einem Bereich zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="23133-255">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="23133-256">Ein Bereich wird erstellt, um den `AppDbContext` der App (einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes)) zu verwenden, um Datenbankeinträge in `IBackgroundTaskQueue` (ein Singletondienst) zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="23133-256">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="23133-257">Wenn auf der Indexseite auf die Schaltfläche **Task hinzufügen** geklickt wird, wird die `OnPostAddTask`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="23133-257">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="23133-258">`QueueBackgroundWorkItem` wird aufgerufen, um ein Arbeitselement in die Warteschlange einzureihen:</span><span class="sxs-lookup"><span data-stu-id="23133-258">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="23133-259">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="23133-259">Additional resources</span></span>

* [<span data-ttu-id="23133-260">Implement background tasks in microservices with IHostedService and the BackgroundService class (Implementieren von Hintergrundtasks in Microservices mit IHostedService und der BackgroundService-Klasse)</span><span class="sxs-lookup"><span data-stu-id="23133-260">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
