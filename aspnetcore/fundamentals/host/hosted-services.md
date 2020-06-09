---
title: 'title: Hintergrundtasks mit gehosteten Diensten in ASP.NET Core: rick-anderson description: Erfahren Sie, wie Sie Hintergrundtasks mit gehosteten Diensten in ASP.NET Core implementieren.'
author: rick-anderson
description: "monikerRange: '>= aspnetcore-2.1' ms.author: riande ms.custom: mvc ms.date: 02/10/2020 no-loc:"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 47d0bdda7249232af22ec1c97e7baa710310caed
ms.sourcegitcommit: cb6e3e12641375ea9ad02002388f78ec0989d88e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84253681"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="a9e46-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="a9e46-103">'Blazor'</span></span>

<span data-ttu-id="a9e46-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="a9e46-104">'Identity'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9e46-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="a9e46-105">'Let's Encrypt'</span></span> <span data-ttu-id="a9e46-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="a9e46-106">'Razor'</span></span> <span data-ttu-id="a9e46-107">'SignalR' uid: fundamentals/host/hosted-services</span><span class="sxs-lookup"><span data-stu-id="a9e46-107">'SignalR' uid: fundamentals/host/hosted-services</span></span>

* <span data-ttu-id="a9e46-108">Hintergrundtasks mit gehosteten Diensten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9e46-108">Background tasks with hosted services in ASP.NET Core</span></span>
* <span data-ttu-id="a9e46-109">Von [Jeow Li Huan](https://github.com/huan086)</span><span class="sxs-lookup"><span data-stu-id="a9e46-109">By [Jeow Li Huan](https://github.com/huan086)</span></span> <span data-ttu-id="a9e46-110">In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-110">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span>
* <span data-ttu-id="a9e46-111">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="a9e46-111">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="a9e46-112">In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:</span><span class="sxs-lookup"><span data-stu-id="a9e46-112">This topic provides three hosted service examples:</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="a9e46-113">Hintergrundtasks, die auf einem Timer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-113">Background task that runs on a timer.</span></span>

<span data-ttu-id="a9e46-114">Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a9e46-114">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="a9e46-115">Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-115">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

<span data-ttu-id="a9e46-116">Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-116">Queued background tasks that run sequentially.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="package"></a><span data-ttu-id="a9e46-117">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9e46-117">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="a9e46-118">Vorlage „Workerdienst“</span><span class="sxs-lookup"><span data-stu-id="a9e46-118">Worker Service template</span></span> <span data-ttu-id="a9e46-119">Die ASP.NET Core-Vorlage „Workerdienst“ dient als Ausgangspunkt für das Schreiben von Dienstanwendungen mit langer Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="a9e46-119">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span>

<span data-ttu-id="a9e46-120">Eine aus der Workerdienstvorlage erstellte App gibt das Worker SDK in ihrer Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="a9e46-120">An app created from the Worker Service template specifies the Worker SDK in its project file:</span></span> <span data-ttu-id="a9e46-121">Gehen Sie folgendermaßen vor, wenn Sie die Vorlage als Grundlage für eine Hosted Services-App verwenden möchten:</span><span class="sxs-lookup"><span data-stu-id="a9e46-121">To use the template as a basis for a hosted services app:</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a9e46-122">Package</span><span class="sxs-lookup"><span data-stu-id="a9e46-122">Package</span></span>

<span data-ttu-id="a9e46-123">Eine App, die auf der Workerdienstvorlage basiert, verwendet das `Microsoft.NET.Sdk.Worker` SDK und verfügt über einen expliziten Paketverweis auf das [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)-Paket.</span><span class="sxs-lookup"><span data-stu-id="a9e46-123">An app based on the Worker Service template uses the `Microsoft.NET.Sdk.Worker` SDK and has an explicit package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

* <span data-ttu-id="a9e46-124">Sehen Sie sich dazu beispielsweise die Projektdatei der Beispiel-App (*BackgroundTasksSample.csproj*) an.</span><span class="sxs-lookup"><span data-stu-id="a9e46-124">For example, see the sample app's project file (*BackgroundTasksSample.csproj*).</span></span> <span data-ttu-id="a9e46-125">Für Web-Apps, die das `Microsoft.NET.Sdk.Web` SDK verwenden, wird auf das [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)-Paket implizit über das geteilte Framework verwiesen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-125">For web apps that use the `Microsoft.NET.Sdk.Web` SDK, the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is referenced implicitly from the shared framework.</span></span>

  * <span data-ttu-id="a9e46-126">Es ist kein expliziter Paketverweis in der Projektdatei der App erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a9e46-126">An explicit package reference in the app's project file isn't required.</span></span>
  * <span data-ttu-id="a9e46-127">Die IHostedService-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="a9e46-127">IHostedService interface</span></span>

  <span data-ttu-id="a9e46-128">Die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="a9e46-128">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span> <span data-ttu-id="a9e46-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` enthält die Logik zum Starten des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="a9e46-129">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span>

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

* <span data-ttu-id="a9e46-130">`StartAsync` wird *vor* folgenden Vorgängen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="a9e46-130">`StartAsync` is called *before*:</span></span> <span data-ttu-id="a9e46-131">Die App-Pipeline für die Anforderungsverarbeitung wird konfiguriert (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="a9e46-131">The app's request processing pipeline is configured (`Startup.Configure`).</span></span> <span data-ttu-id="a9e46-132">Der Server wird gestartet, und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a9e46-132">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="a9e46-133">Das Standardverhalten kann so geändert werden, dass der `StartAsync`-Vorgang des gehosteten Diensts ausgeführt wird, nachdem die Pipeline der App konfiguriert und `ApplicationStarted` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="a9e46-133">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="a9e46-134">Um das Standardverhalten zu ändern, fügen Sie den gehosteten Dienst (`VideosWatcher` im folgenden Beispiel) nach dem Aufruf von `ConfigureWebHostDefaults` hinzu:</span><span class="sxs-lookup"><span data-stu-id="a9e46-134">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

  * <span data-ttu-id="a9e46-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Wird ausgelöst, wenn der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-135">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span>
  * <span data-ttu-id="a9e46-136">`StopAsync` enthält die Logik zum Beenden des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="a9e46-136">`StopAsync` contains the logic to end the background task.</span></span>

  <span data-ttu-id="a9e46-137">Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-137">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a9e46-138">Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-138">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a9e46-139">Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="a9e46-139">When cancellation is requested on the token:</span></span>

  <span data-ttu-id="a9e46-140">Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.</span><span class="sxs-lookup"><span data-stu-id="a9e46-140">Any remaining background operations that the app is performing should be aborted.</span></span>

  * <span data-ttu-id="a9e46-141">Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-141">Any methods called in `StopAsync` should return promptly.</span></span> <span data-ttu-id="a9e46-142">Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="a9e46-142">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>
  * <span data-ttu-id="a9e46-143">Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-143">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a9e46-144">Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-144">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

<span data-ttu-id="a9e46-145">Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="a9e46-145">To extend the default five second shutdown timeout, set:</span></span> <span data-ttu-id="a9e46-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-146"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span>

## <a name="backgroundservice-base-class"></a><span data-ttu-id="a9e46-147">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a9e46-147">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a9e46-148">Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-148">Shutdown timeout host configuration setting when using Web Host.</span></span>

<span data-ttu-id="a9e46-149">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a9e46-149">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span> <span data-ttu-id="a9e46-150">Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren.</span><span class="sxs-lookup"><span data-stu-id="a9e46-150">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="a9e46-151">Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-151">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span> <span data-ttu-id="a9e46-152">BackgroundService-Basisklasse</span><span class="sxs-lookup"><span data-stu-id="a9e46-152">BackgroundService base class</span></span> <span data-ttu-id="a9e46-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> ist eine Basisklasse zur Implementierung eines <xref:Microsoft.Extensions.Hosting.IHostedService> mit langer Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="a9e46-153"><xref:Microsoft.Extensions.Hosting.BackgroundService> is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span>

<span data-ttu-id="a9e46-154">[ExecuteAsync (CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) wird aufgerufen, um den Hintergrunddienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-154">[ExecuteAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.ExecuteAsync*) is called to run the background service.</span></span> <span data-ttu-id="a9e46-155">Die Implementierung gibt einen <xref:System.Threading.Tasks.Task> zurück, der die gesamte Lebensdauer des Hintergrunddiensts darstellt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-155">The implementation returns a <xref:System.Threading.Tasks.Task> that represents the entire lifetime of the background service.</span></span> <span data-ttu-id="a9e46-156">Es werden keine weiteren Dienste gestartet, bis [ExecuteAsync asynchron wird](https://github.com/dotnet/extensions/issues/2149), etwa durch den Aufruf von `await`.</span><span class="sxs-lookup"><span data-stu-id="a9e46-156">No further services are started until [ExecuteAsync becomes asynchronous](https://github.com/dotnet/extensions/issues/2149), such as by calling `await`.</span></span> <span data-ttu-id="a9e46-157">Vermeiden Sie die Ausführung von langen, blockierenden Initialisierungsarbeiten in `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="a9e46-157">Avoid performing long, blocking initialization work in `ExecuteAsync`.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a9e46-158">Die Hostblöcke in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) warten auf den Abschluss von `ExecuteAsync`.</span><span class="sxs-lookup"><span data-stu-id="a9e46-158">The host blocks in [StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.BackgroundService.StopAsync*) waiting for `ExecuteAsync` to complete.</span></span>

<span data-ttu-id="a9e46-159">Das Abbruchtoken wird beim Aufruf von [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="a9e46-159">The cancellation token is triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span> <span data-ttu-id="a9e46-160">Ihre Implementierung von `ExecuteAsync` sollte unverzüglich beendet werden, wenn das Abbruchtoken ausgelöst wird, um den Dienst ordnungsgemäß herunterzufahren.</span><span class="sxs-lookup"><span data-stu-id="a9e46-160">Your implementation of `ExecuteAsync` should finish promptly when the cancellation token is fired in order to gracefully shut down the service.</span></span> <span data-ttu-id="a9e46-161">Andernfalls wird der Dienst beim Erreichen des Timeouts beim Herunterfahren nicht ordnungsgemäß beendet.</span><span class="sxs-lookup"><span data-stu-id="a9e46-161">Otherwise, the service ungracefully shuts down at the shutdown timeout.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-17,34,41)]

<span data-ttu-id="a9e46-162">Weitere Informationen finden Sie im Abschnitt [IHostedService-Schnittstelle](#ihostedservice-interface).</span><span class="sxs-lookup"><span data-stu-id="a9e46-162">For more information, see the [IHostedService interface](#ihostedservice-interface) section.</span></span> <span data-ttu-id="a9e46-163">Zeitlich festgelegte Hintergrundtasks</span><span class="sxs-lookup"><span data-stu-id="a9e46-163">Timed background tasks</span></span>

<span data-ttu-id="a9e46-164">Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="a9e46-164">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a9e46-165">Der Timer löst die `DoWork`-Methode des Tasks aus.</span><span class="sxs-lookup"><span data-stu-id="a9e46-165">The timer triggers the task's `DoWork` method.</span></span>

<span data-ttu-id="a9e46-166">Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:</span><span class="sxs-lookup"><span data-stu-id="a9e46-166">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span> <span data-ttu-id="a9e46-167"><xref:System.Threading.Timer> wartet nicht auf den Abschluss vorheriger Ausführungen von `DoWork`. Die veranschaulichte Vorgehensweise eignet sich also möglicherweise nicht für alle Szenarios.</span><span class="sxs-lookup"><span data-stu-id="a9e46-167">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span>

<span data-ttu-id="a9e46-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) wird zum Erhöhen des Ausführungszählers mit einem atomischen Vorgang verwendet, wodurch sichergestellt wird, dass `executionCount` nicht durch mehrere Threads gleichzeitig aktualisiert wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-168">[Interlocked.Increment](xref:System.Threading.Interlocked.Increment*) is used to increment the execution counter as an atomic operation, which ensures that multiple threads don't update `executionCount` concurrently.</span></span> <span data-ttu-id="a9e46-169">Der Dienst wird in `IHostBuilder.ConfigureServices` (*Program.cs*) mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="a9e46-169">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

* <span data-ttu-id="a9e46-170">Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask</span><span class="sxs-lookup"><span data-stu-id="a9e46-170">Consuming a scoped service in a background task</span></span> <span data-ttu-id="a9e46-171">Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in einem [BackgroundService](#backgroundservice-base-class) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-171">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a [BackgroundService](#backgroundservice-base-class), create a scope.</span></span> <span data-ttu-id="a9e46-172">Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-172">No scope is created for a hosted service by default.</span></span>
* <span data-ttu-id="a9e46-173">Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="a9e46-173">The scoped background task service contains the background task's logic.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a9e46-174">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="a9e46-174">In the following example:</span></span> <span data-ttu-id="a9e46-175">Der Dienst ist asynchron.</span><span class="sxs-lookup"><span data-stu-id="a9e46-175">The service is asynchronous.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="a9e46-176">Die `DoWork`-Methode gibt `Task` zurück.</span><span class="sxs-lookup"><span data-stu-id="a9e46-176">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="a9e46-177">Zu Demonstrationszwecken wird in der `DoWork`-Methode eine Verzögerung von zehn Sekunden verwendet.</span><span class="sxs-lookup"><span data-stu-id="a9e46-177">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a9e46-178">Ein <xref:Microsoft.Extensions.Logging.ILogger> wird in den Dienst eingefügt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-178">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

<span data-ttu-id="a9e46-179">Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, damit die `DoWork`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-179">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a9e46-180">`DoWork` gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird:</span><span class="sxs-lookup"><span data-stu-id="a9e46-180">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

* <span data-ttu-id="a9e46-181">Die Dienste werden in `IHostBuilder.ConfigureServices` (*Program.cs*) registriert.</span><span class="sxs-lookup"><span data-stu-id="a9e46-181">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span>
* <span data-ttu-id="a9e46-182">Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="a9e46-182">The hosted service is registered with the `AddHostedService` extension method:</span></span>
* <span data-ttu-id="a9e46-183">Hintergrundtasks in der Warteschlange</span><span class="sxs-lookup"><span data-stu-id="a9e46-183">Queued background tasks</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28-29,33)]

<span data-ttu-id="a9e46-184">Eine Warteschlange für Hintergrundaufgaben basiert auf dem .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span><span class="sxs-lookup"><span data-stu-id="a9e46-184">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>:</span></span>

* <span data-ttu-id="a9e46-185">Im folgenden Beispiel für `QueueHostedService` gilt:</span><span class="sxs-lookup"><span data-stu-id="a9e46-185">In the following `QueueHostedService` example:</span></span>
* <span data-ttu-id="a9e46-186">Die `BackgroundProcessing`-Methode gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-186">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="a9e46-187">Hintergrundtasks in der Warteschlange werden aus dieser entfernt und in `BackgroundProcessing` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-187">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>
  * <span data-ttu-id="a9e46-188">Auf Arbeitselemente wird gewartet, bevor der Dienst in `StopAsync` angehalten wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-188">Work items are awaited before the service stops in `StopAsync`.</span></span>
  * <span data-ttu-id="a9e46-189">Ein `MonitorLoop`-Dienst verarbeitet das Einreihen von Tasks in die Warteschlange für den gehosteten Dienst, wenn der `w`-Schlüssel auf einem Eingabegerät ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="a9e46-189">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="a9e46-190">Die `IBackgroundTaskQueue` wird in den `MonitorLoop`-Dienst eingefügt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-190">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span> <span data-ttu-id="a9e46-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` wird aufgerufen, um ein Arbeitselement in die Warteschlange einzureihen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-191">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

<span data-ttu-id="a9e46-192">Das Arbeitselement simuliert eine Hintergrundaufgabe mit langer Ausführungszeit:</span><span class="sxs-lookup"><span data-stu-id="a9e46-192">The work item simulates a long-running background task:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet4)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9e46-193">Drei 5-Sekunden-Verzögerungen werden ausgeführt (`Task.Delay`).</span><span class="sxs-lookup"><span data-stu-id="a9e46-193">Three 5-second delays are executed (`Task.Delay`).</span></span> <span data-ttu-id="a9e46-194">Eine `try-catch`-Anweisung fängt <xref:System.OperationCanceledException> auf, wenn der Task abgebrochen wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-194">A `try-catch` statement traps <xref:System.OperationCanceledException> if the task is cancelled.</span></span> <span data-ttu-id="a9e46-195">Die Dienste werden in `IHostBuilder.ConfigureServices` (*Program.cs*) registriert.</span><span class="sxs-lookup"><span data-stu-id="a9e46-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span>

* <span data-ttu-id="a9e46-196">Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="a9e46-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>
* <span data-ttu-id="a9e46-197">`MonitorLoop` wird in `Program.Main` gestartet:</span><span class="sxs-lookup"><span data-stu-id="a9e46-197">`MonitorLoop` is started in `Program.Main`:</span></span> <span data-ttu-id="a9e46-198">In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-198">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span>
* <span data-ttu-id="a9e46-199">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="a9e46-199">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="a9e46-200">In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:</span><span class="sxs-lookup"><span data-stu-id="a9e46-200">This topic provides three hosted service examples:</span></span>

## <a name="package"></a><span data-ttu-id="a9e46-201">Hintergrundtasks, die auf einem Timer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-201">Background task that runs on a timer.</span></span>

<span data-ttu-id="a9e46-202">Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a9e46-202">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="a9e46-203">Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-203">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>

<span data-ttu-id="a9e46-204">Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-204">Queued background tasks that run sequentially.</span></span> <span data-ttu-id="a9e46-205">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9e46-205">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

* <span data-ttu-id="a9e46-206">Package</span><span class="sxs-lookup"><span data-stu-id="a9e46-206">Package</span></span> <span data-ttu-id="a9e46-207">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9e46-207">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span> <span data-ttu-id="a9e46-208">Die IHostedService-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="a9e46-208">IHostedService interface</span></span>

* <span data-ttu-id="a9e46-209">Gehostete Dienste implementieren die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="a9e46-209">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="a9e46-210">Die Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="a9e46-210">The interface defines two methods for objects that are managed by the host:</span></span> <span data-ttu-id="a9e46-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` enthält die Logik zum Starten des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="a9e46-211">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*): `StartAsync` contains the logic to start the background task.</span></span>

  <span data-ttu-id="a9e46-212">Bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) wird `StartAsync` aufgerufen, nachdem der Server gestartet und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) ausgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="a9e46-212">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="a9e46-213">Bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) wird `StartAsync` aufgerufen, bevor `ApplicationStarted` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-213">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

  * <span data-ttu-id="a9e46-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Wird ausgelöst, wenn der Host ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-214">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*): Triggered when the host is performing a graceful shutdown.</span></span>
  * <span data-ttu-id="a9e46-215">`StopAsync` enthält die Logik zum Beenden des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="a9e46-215">`StopAsync` contains the logic to end the background task.</span></span>

  <span data-ttu-id="a9e46-216">Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-216">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="a9e46-217">Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-217">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="a9e46-218">Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="a9e46-218">When cancellation is requested on the token:</span></span>

  <span data-ttu-id="a9e46-219">Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.</span><span class="sxs-lookup"><span data-stu-id="a9e46-219">Any remaining background operations that the app is performing should be aborted.</span></span>

  * <span data-ttu-id="a9e46-220">Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-220">Any methods called in `StopAsync` should return promptly.</span></span> <span data-ttu-id="a9e46-221">Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="a9e46-221">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>
  * <span data-ttu-id="a9e46-222">Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-222">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="a9e46-223">Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-223">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

<span data-ttu-id="a9e46-224">Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="a9e46-224">To extend the default five second shutdown timeout, set:</span></span> <span data-ttu-id="a9e46-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-225"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="a9e46-226">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a9e46-226">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>

<span data-ttu-id="a9e46-227">Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-227">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="a9e46-228">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="a9e46-228">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span> <span data-ttu-id="a9e46-229">Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren.</span><span class="sxs-lookup"><span data-stu-id="a9e46-229">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="a9e46-230">Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a9e46-230">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

<span data-ttu-id="a9e46-231">Zeitlich festgelegte Hintergrundtasks</span><span class="sxs-lookup"><span data-stu-id="a9e46-231">Timed background tasks</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="a9e46-232">Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="a9e46-232">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span>

<span data-ttu-id="a9e46-233">Der Timer löst die `DoWork`-Methode des Tasks aus.</span><span class="sxs-lookup"><span data-stu-id="a9e46-233">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="a9e46-234">Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:</span><span class="sxs-lookup"><span data-stu-id="a9e46-234">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

<span data-ttu-id="a9e46-235"><xref:System.Threading.Timer> wartet nicht auf den Abschluss vorheriger Ausführungen von `DoWork`. Die veranschaulichte Vorgehensweise eignet sich also möglicherweise nicht für alle Szenarios.</span><span class="sxs-lookup"><span data-stu-id="a9e46-235">The <xref:System.Threading.Timer> doesn't wait for previous executions of `DoWork` to finish, so the approach shown might not be suitable for every scenario.</span></span> <span data-ttu-id="a9e46-236">Der Dienst wird in `Startup.ConfigureServices` mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="a9e46-236">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="a9e46-237">Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask</span><span class="sxs-lookup"><span data-stu-id="a9e46-237">Consuming a scoped service in a background task</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="a9e46-238">Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in `IHostedService` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="a9e46-238">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="a9e46-239">Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.</span><span class="sxs-lookup"><span data-stu-id="a9e46-239">No scope is created for a hosted service by default.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="a9e46-240">Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="a9e46-240">The scoped background task service contains the background task's logic.</span></span>

<span data-ttu-id="a9e46-241">Im folgenden Beispiel wird <xref:Microsoft.Extensions.Logging.ILogger> in den Dienst eingefügt:</span><span class="sxs-lookup"><span data-stu-id="a9e46-241">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="a9e46-242">Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, um die `DoWork`-Methode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="a9e46-242">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="a9e46-243">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="a9e46-243">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9e46-244">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="a9e46-244">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="a9e46-245">Hintergrundtasks in der Warteschlange</span><span class="sxs-lookup"><span data-stu-id="a9e46-245">Queued background tasks</span></span>

* <span data-ttu-id="a9e46-246">Eine Warteschlange für Hintergrundtasks basiert auf dem <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> von .NET Framework 4.x ([die Integration in ASP.NET Core ist vorläufig geplant](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="a9e46-246">A background task queue is based on the .NET Framework 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>
* <span data-ttu-id="a9e46-247">In `QueueHostedService` werden Hintergrundaufgaben in der Warteschlange aus der Warteschlange entfernt und als [BackgroundService](#backgroundservice-base-class), eine Basisklasse zum Bereitstellen von `IHostedService` mit langer Ausführungszeit, ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="a9e46-247">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a [BackgroundService](#backgroundservice-base-class), which is a base class for implementing a long running `IHostedService`:</span></span> <span data-ttu-id="a9e46-248">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="a9e46-248">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="a9e46-249">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="a9e46-249">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="a9e46-250">In der Modellklasse für die Indexseite:</span><span class="sxs-lookup"><span data-stu-id="a9e46-250">In the Index page model class:</span></span> <span data-ttu-id="a9e46-251">`IBackgroundTaskQueue` wird in den Konstruktor eingefügt und `Queue` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-251">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a9e46-252">Ein <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> wird eingefügt und `_serviceScopeFactory` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-252">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span>

* <span data-ttu-id="a9e46-253">Die Zuordnungsinstanz wird verwendet, um Instanzen von <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> zu erstellen. Dieser wird verwendet, um Dienste in einem Bereich zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a9e46-253">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span>
* <span data-ttu-id="a9e46-254">Ein Bereich wird erstellt, um den `AppDbContext` der App (einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes)) zu verwenden, um Datenbankeinträge in `IBackgroundTaskQueue` (ein Singletondienst) zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="a9e46-254">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>
* <xref:System.Threading.Timer>
