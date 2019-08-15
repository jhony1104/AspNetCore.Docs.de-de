---
title: Hintergrundtasks mit gehosteten Diensten in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Hintergrundtasks mit gehosteten Diensten in ASP.NET Core implementieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/03/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 3d4279a291182da60c0cb2fbb93a3922ed673cde
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68914025"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="b28c9-103">Hintergrundtasks mit gehosteten Diensten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b28c9-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="b28c9-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="b28c9-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="b28c9-105">In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="b28c9-106">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="b28c9-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b28c9-107">In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:</span><span class="sxs-lookup"><span data-stu-id="b28c9-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="b28c9-108">Hintergrundtasks, die auf einem Timer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="b28c9-109">Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren.</span><span class="sxs-lookup"><span data-stu-id="b28c9-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="b28c9-110">Der bereichsbezogene Dienst kann Dependency Injection verwenden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-110">The scoped service can use dependency injection.</span></span>
* <span data-ttu-id="b28c9-111">Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="b28c9-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b28c9-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="b28c9-113">Die Beispiel-App wird in zwei Versionen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="b28c9-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="b28c9-114">Web Host &ndash; Der Webhost eignet sich für das Hosten von Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="b28c9-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="b28c9-115">Der in diesem Thema gezeigte Beispielcode stammt aus der Webhostversion des Beispiels.</span><span class="sxs-lookup"><span data-stu-id="b28c9-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="b28c9-116">Weitere Informationen finden Sie unter dem Thema [Webhost](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="b28c9-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="b28c9-117">Generischer Host &ndash; Der generische Host wurde in ASP.NET Core 2.1 neu eingeführt.</span><span class="sxs-lookup"><span data-stu-id="b28c9-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="b28c9-118">Weitere Informationen finden Sie unter dem Thema [Generischer Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="b28c9-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="worker-service-template"></a><span data-ttu-id="b28c9-119">Vorlage „Workerdienst“</span><span class="sxs-lookup"><span data-stu-id="b28c9-119">Worker Service template</span></span>

<span data-ttu-id="b28c9-120">Die ASP.NET Core-Vorlage „Workerdienst“ dient als Ausgangspunkt für das Schreiben von Dienstanwendungen mit langer Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="b28c9-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="b28c9-121">Gehen Sie folgendermaßen vor, wenn Sie die Vorlage als Grundlage für eine Hosted Services-App verwenden möchten:</span><span class="sxs-lookup"><span data-stu-id="b28c9-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b28c9-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b28c9-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b28c9-123">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="b28c9-123">Create a new project.</span></span>
1. <span data-ttu-id="b28c9-124">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="b28c9-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="b28c9-125">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="b28c9-125">Select **Next**.</span></span>
1. <span data-ttu-id="b28c9-126">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="b28c9-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="b28c9-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b28c9-127">Select **Create**.</span></span>
1. <span data-ttu-id="b28c9-128">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="b28c9-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="b28c9-129">Wählen Sie die Vorlage **Workerdienst** aus.</span><span class="sxs-lookup"><span data-stu-id="b28c9-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="b28c9-130">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="b28c9-130">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b28c9-131">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b28c9-131">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="b28c9-132">Rufen Sie die Vorlage „Workerdienst“ (`worker`) über eine Befehlsshell mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) auf.</span><span class="sxs-lookup"><span data-stu-id="b28c9-132">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="b28c9-133">Im folgenden Beispiel wird eine Workerdienstanwendung namens `ContosoWorkerService` erstellt.</span><span class="sxs-lookup"><span data-stu-id="b28c9-133">In the following example, a Worker Service app is created named `ContosoWorkerService`.</span></span> <span data-ttu-id="b28c9-134">Der Ordner für die App `ContosoWorkerService` wird automatisch erstellt, wenn der Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b28c9-134">A folder for the `ContosoWorkerService` app is created automatically when the command is executed.</span></span>

```console
dotnet new worker -o ContosoWorkerService
```

---

::: moniker-end

## <a name="package"></a><span data-ttu-id="b28c9-135">Package</span><span class="sxs-lookup"><span data-stu-id="b28c9-135">Package</span></span>

<span data-ttu-id="b28c9-136">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) hinzu.</span><span class="sxs-lookup"><span data-stu-id="b28c9-136">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="b28c9-137">Die IHostedService-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="b28c9-137">IHostedService interface</span></span>

<span data-ttu-id="b28c9-138">Gehostete Dienste implementieren die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="b28c9-138">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="b28c9-139">Die Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="b28c9-139">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="b28c9-140">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` enthält die Logik zum Starten des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="b28c9-140">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="b28c9-141">Bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) wird `StartAsync` aufgerufen, nachdem der Server gestartet und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) ausgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="b28c9-141">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="b28c9-142">Bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) wird `StartAsync` aufgerufen, bevor `ApplicationStarted` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="b28c9-142">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="b28c9-143">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; wird ausgelöst, wenn der Host das Herunterfahren ordnungsgemäß ausführt.</span><span class="sxs-lookup"><span data-stu-id="b28c9-143">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="b28c9-144">`StopAsync` enthält die Logik zum Beenden des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="b28c9-144">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="b28c9-145">Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="b28c9-145">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="b28c9-146">Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="b28c9-146">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="b28c9-147">Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="b28c9-147">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="b28c9-148">Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.</span><span class="sxs-lookup"><span data-stu-id="b28c9-148">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="b28c9-149">Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-149">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="b28c9-150">Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="b28c9-150">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="b28c9-151">Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="b28c9-151">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="b28c9-152">Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="b28c9-152">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="b28c9-153">Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="b28c9-153">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="b28c9-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-154"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="b28c9-155">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="b28c9-155">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="b28c9-156">Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-156">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="b28c9-157">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="b28c9-157">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="b28c9-158">Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren.</span><span class="sxs-lookup"><span data-stu-id="b28c9-158">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="b28c9-159">Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="b28c9-159">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="b28c9-160">Zeitlich festgelegte Hintergrundtasks</span><span class="sxs-lookup"><span data-stu-id="b28c9-160">Timed background tasks</span></span>

<span data-ttu-id="b28c9-161">Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="b28c9-161">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="b28c9-162">Der Timer löst die `DoWork`-Methode des Tasks aus.</span><span class="sxs-lookup"><span data-stu-id="b28c9-162">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="b28c9-163">Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:</span><span class="sxs-lookup"><span data-stu-id="b28c9-163">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="b28c9-164">Der Dienst wird in `Startup.ConfigureServices` mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="b28c9-164">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="b28c9-165">Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask</span><span class="sxs-lookup"><span data-stu-id="b28c9-165">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="b28c9-166">Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in `IHostedService` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="b28c9-166">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="b28c9-167">Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.</span><span class="sxs-lookup"><span data-stu-id="b28c9-167">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="b28c9-168">Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="b28c9-168">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="b28c9-169">Im folgenden Beispiel wird <xref:Microsoft.Extensions.Logging.ILogger> in den Dienst eingefügt:</span><span class="sxs-lookup"><span data-stu-id="b28c9-169">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="b28c9-170">Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, um die `DoWork`-Methode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="b28c9-170">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="b28c9-171">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="b28c9-171">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b28c9-172">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="b28c9-172">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="b28c9-173">Hintergrundtasks in der Warteschlange</span><span class="sxs-lookup"><span data-stu-id="b28c9-173">Queued background tasks</span></span>

<span data-ttu-id="b28c9-174">Eine Warteschlange für Hintergrundtasks basiert auf dem Element <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>von .NET 4.x ([es ist vorläufig geplant, dieses in ASP.NET Core 3.0 zu integrieren](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="b28c9-174">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core 3.0](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="b28c9-175">In `QueueHostedService` werden Hintergrundaufgaben in der Warteschlange aus der Warteschlange entfernt und als <xref:Microsoft.Extensions.Hosting.BackgroundService>, eine Basisklasse zum Bereitstellen von `IHostedService` mit langer Ausführungszeit, ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="b28c9-175">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="b28c9-176">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="b28c9-176">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b28c9-177">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="b28c9-177">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Startup.cs?name=snippet3)]

<span data-ttu-id="b28c9-178">In der Modellklasse für die Indexseite:</span><span class="sxs-lookup"><span data-stu-id="b28c9-178">In the Index page model class:</span></span>

* <span data-ttu-id="b28c9-179">`IBackgroundTaskQueue` wird in den Konstruktor eingefügt und `Queue` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="b28c9-179">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="b28c9-180">Ein <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> wird eingefügt und `_serviceScopeFactory` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="b28c9-180">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="b28c9-181">Die Zuordnungsinstanz wird verwendet, um Instanzen von <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> zu erstellen. Dieser wird verwendet, um Dienste in einem Bereich zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b28c9-181">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="b28c9-182">Ein Bereich wird erstellt, um den `AppDbContext` der App (einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes)) zu verwenden, um Datenbankeinträge in `IBackgroundTaskQueue` (ein Singletondienst) zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="b28c9-182">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="b28c9-183">Wenn auf der Indexseite auf die Schaltfläche **Task hinzufügen** geklickt wird, wird die `OnPostAddTask`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="b28c9-183">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="b28c9-184">`QueueBackgroundWorkItem` wird aufgerufen, um das Arbeitselement in die Warteschlange einzureihen:</span><span class="sxs-lookup"><span data-stu-id="b28c9-184">`QueueBackgroundWorkItem` is called to enqueue the work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample-WebHost/Pages/Index.cshtml.cs?name=snippet2)]

## <a name="additional-resources"></a><span data-ttu-id="b28c9-185">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b28c9-185">Additional resources</span></span>

* [<span data-ttu-id="b28c9-186">Implement background tasks in microservices with IHostedService and the BackgroundService class (Implementieren von Hintergrundtasks in Microservices mit IHostedService und der BackgroundService-Klasse)</span><span class="sxs-lookup"><span data-stu-id="b28c9-186">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
