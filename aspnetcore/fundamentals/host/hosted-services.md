---
title: Hintergrundtasks mit gehosteten Diensten in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Hintergrundtasks mit gehosteten Diensten in ASP.NET Core implementieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/18/2019
uid: fundamentals/host/hosted-services
ms.openlocfilehash: 8df86b10d7ba853edb3265df0e02eabbf8a2c058
ms.sourcegitcommit: fa61d882be9d0c48bd681f2efcb97e05522051d0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71205712"
---
# <a name="background-tasks-with-hosted-services-in-aspnet-core"></a><span data-ttu-id="26dd3-103">Hintergrundtasks mit gehosteten Diensten in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="26dd3-103">Background tasks with hosted services in ASP.NET Core</span></span>

<span data-ttu-id="26dd3-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="26dd3-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26dd3-105">In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-105">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="26dd3-106">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="26dd3-106">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="26dd3-107">In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:</span><span class="sxs-lookup"><span data-stu-id="26dd3-107">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="26dd3-108">Hintergrundtasks, die auf einem Timer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-108">Background task that runs on a timer.</span></span>
* <span data-ttu-id="26dd3-109">Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren.</span><span class="sxs-lookup"><span data-stu-id="26dd3-109">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="26dd3-110">Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-110">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>
* <span data-ttu-id="26dd3-111">Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-111">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="26dd3-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26dd3-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="26dd3-113">Die Beispiel-App wird in zwei Versionen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="26dd3-113">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="26dd3-114">Web Host &ndash; Der Webhost eignet sich für das Hosten von Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="26dd3-114">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="26dd3-115">Der in diesem Thema gezeigte Beispielcode stammt aus der Webhostversion des Beispiels.</span><span class="sxs-lookup"><span data-stu-id="26dd3-115">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="26dd3-116">Weitere Informationen finden Sie unter dem Thema [Webhost](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="26dd3-116">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="26dd3-117">Generischer Host &ndash; Der generische Host wurde in ASP.NET Core 2.1 neu eingeführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-117">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="26dd3-118">Weitere Informationen finden Sie unter dem Thema [Generischer Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="26dd3-118">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="worker-service-template"></a><span data-ttu-id="26dd3-119">Vorlage „Workerdienst“</span><span class="sxs-lookup"><span data-stu-id="26dd3-119">Worker Service template</span></span>

<span data-ttu-id="26dd3-120">Die ASP.NET Core-Vorlage „Workerdienst“ dient als Ausgangspunkt für das Schreiben von Dienstanwendungen mit langer Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="26dd3-120">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="26dd3-121">Gehen Sie folgendermaßen vor, wenn Sie die Vorlage als Grundlage für eine Hosted Services-App verwenden möchten:</span><span class="sxs-lookup"><span data-stu-id="26dd3-121">To use the template as a basis for a hosted services app:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="26dd3-122">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="26dd3-122">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="26dd3-123">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-123">Create a new project.</span></span>
1. <span data-ttu-id="26dd3-124">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-124">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="26dd3-125">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="26dd3-125">Select **Next**.</span></span>
1. <span data-ttu-id="26dd3-126">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-126">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="26dd3-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-127">Select **Create**.</span></span>
1. <span data-ttu-id="26dd3-128">Vergewissern Sie sich, dass im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Optionen **.NET Core** und **ASP.NET Core 3.0** ausgewählt sind.</span><span class="sxs-lookup"><span data-stu-id="26dd3-128">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.0** are selected.</span></span>
1. <span data-ttu-id="26dd3-129">Wählen Sie die Vorlage **Workerdienst** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-129">Select the **Worker Service** template.</span></span> <span data-ttu-id="26dd3-130">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-130">Select **Create**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="26dd3-131">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="26dd3-131">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="26dd3-132">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-132">Create a new project.</span></span>
1. <span data-ttu-id="26dd3-133">Wählen Sie in der Randleiste unter **.NET Core** den Eintrag **App** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-133">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="26dd3-134">Wählen Sie unter **ASP.NET Core** den Eintrag **Worker** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-134">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="26dd3-135">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="26dd3-135">Select **Next**.</span></span>
1. <span data-ttu-id="26dd3-136">Wählen Sie **.NET Core 3.0** als **Zielframework** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-136">Select **.NET Core 3.0** for the **Target Framework**.</span></span> <span data-ttu-id="26dd3-137">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="26dd3-137">Select **Next**.</span></span>
1. <span data-ttu-id="26dd3-138">Geben Sie im Feld **Projektname** einen Namen an.</span><span class="sxs-lookup"><span data-stu-id="26dd3-138">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="26dd3-139">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-139">Select **Create**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="26dd3-140">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="26dd3-140">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="26dd3-141">Rufen Sie die Vorlage „Workerdienst“ (`worker`) über eine Befehlsshell mit dem Befehl [dotnet new](/dotnet/core/tools/dotnet-new) auf.</span><span class="sxs-lookup"><span data-stu-id="26dd3-141">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="26dd3-142">Im folgenden Beispiel wird eine Workerdienstanwendung namens `ContosoWorker` erstellt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-142">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="26dd3-143">Der Ordner für die App `ContosoWorker` wird automatisch erstellt, wenn der Befehl ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-143">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---

## <a name="package"></a><span data-ttu-id="26dd3-144">Package</span><span class="sxs-lookup"><span data-stu-id="26dd3-144">Package</span></span>

<span data-ttu-id="26dd3-145">Für ASP.NET Core-Apps wird implizit ein Paketverweis auf das Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-145">A package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package is added implicitly for ASP.NET Core apps.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="26dd3-146">Die IHostedService-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="26dd3-146">IHostedService interface</span></span>

<span data-ttu-id="26dd3-147">Die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="26dd3-147">The <xref:Microsoft.Extensions.Hosting.IHostedService> interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="26dd3-148">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` enthält die Logik zum Starten des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="26dd3-148">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="26dd3-149">`StartAsync` wird *vor* folgenden Vorgängen aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="26dd3-149">`StartAsync` is called *before*:</span></span>

  * <span data-ttu-id="26dd3-150">Die App-Pipeline für die Anforderungsverarbeitung wird konfiguriert (`Startup.Configure`).</span><span class="sxs-lookup"><span data-stu-id="26dd3-150">The app's request processing pipeline is configured (`Startup.Configure`).</span></span>
  * <span data-ttu-id="26dd3-151">Der Server wird gestartet, und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="26dd3-151">The server is started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span>

  <span data-ttu-id="26dd3-152">Das Standardverhalten kann so geändert werden, dass der `StartAsync`-Vorgang des gehosteten Diensts ausgeführt wird, nachdem die Pipeline der App konfiguriert und `ApplicationStarted` aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="26dd3-152">The default behavior can be changed so that the hosted service's `StartAsync` runs after the app's pipeline has been configured and `ApplicationStarted` is called.</span></span> <span data-ttu-id="26dd3-153">Um das Standardverhalten zu ändern, fügen Sie den gehosteten Dienst (`VideosWatcher` im folgenden Beispiel) nach dem Aufruf von `ConfigureWebHostDefaults` hinzu:</span><span class="sxs-lookup"><span data-stu-id="26dd3-153">To change the default behavior, add the hosted service (`VideosWatcher` in the following example) after calling `ConfigureWebHostDefaults`:</span></span>

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

* <span data-ttu-id="26dd3-154">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; wird ausgelöst, wenn der Host das Herunterfahren ordnungsgemäß ausführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-154">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="26dd3-155">`StopAsync` enthält die Logik zum Beenden des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="26dd3-155">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="26dd3-156">Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-156">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="26dd3-157">Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-157">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="26dd3-158">Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="26dd3-158">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="26dd3-159">Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.</span><span class="sxs-lookup"><span data-stu-id="26dd3-159">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="26dd3-160">Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-160">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="26dd3-161">Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="26dd3-161">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="26dd3-162">Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-162">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="26dd3-163">Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-163">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="26dd3-164">Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="26dd3-164">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="26dd3-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-165"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="26dd3-166">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="26dd3-166">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="26dd3-167">Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-167">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="26dd3-168">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="26dd3-168">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="26dd3-169">Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren.</span><span class="sxs-lookup"><span data-stu-id="26dd3-169">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="26dd3-170">Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-170">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="backgroundservice"></a><span data-ttu-id="26dd3-171">BackgroundService</span><span class="sxs-lookup"><span data-stu-id="26dd3-171">BackgroundService</span></span>

<span data-ttu-id="26dd3-172">`BackgroundService` ist eine Basisklasse zur Implementierung eines <xref:Microsoft.Extensions.Hosting.IHostedService> mit langer Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="26dd3-172">`BackgroundService` is a base class for implementing a long running <xref:Microsoft.Extensions.Hosting.IHostedService>.</span></span> <span data-ttu-id="26dd3-173">`BackgroundService` definiert zwei Methoden für Hintergrundvorgänge:</span><span class="sxs-lookup"><span data-stu-id="26dd3-173">`BackgroundService` defines two methods for background operations:</span></span>

* <span data-ttu-id="26dd3-174">`ExecuteAsync(CancellationToken stoppingToken)` &ndash; `ExecuteAsync` wird aufgerufen, wenn der <xref:Microsoft.Extensions.Hosting.IHostedService> gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-174">`ExecuteAsync(CancellationToken stoppingToken)` &ndash; `ExecuteAsync` Called when the <xref:Microsoft.Extensions.Hosting.IHostedService> starts.</span></span> <span data-ttu-id="26dd3-175">Die Implementierung sollte einen `Task`-Wert zurückgeben, der die Lebensdauer der ausgeführten Vorgänge mit langer Laufzeit repräsentiert.</span><span class="sxs-lookup"><span data-stu-id="26dd3-175">The implementation should return a `Task` that represents the lifetime of the long running operations performed.</span></span> <span data-ttu-id="26dd3-176">Das `stoppingToken` wird aufgerufen, wenn [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-176">The `stoppingToken` Triggered when [IHostedService.StopAsync](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) is called.</span></span>
* <span data-ttu-id="26dd3-177">`StopAsync(CancellationToken stoppingToken)` &ndash; `StopAsync` wird ausgelöst, wenn der Anwendungshost ein ordnungsgemäßes Herunterfahren ausführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-177">`StopAsync(CancellationToken stoppingToken)` &ndash; `StopAsync` is triggered when the application host is performing a graceful shutdown.</span></span> <span data-ttu-id="26dd3-178">Das `stoppingToken` weist darauf hin, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="26dd3-178">The `stoppingToken` indicates that the shutdown process should no longer be graceful.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="26dd3-179">Zeitlich festgelegte Hintergrundtasks</span><span class="sxs-lookup"><span data-stu-id="26dd3-179">Timed background tasks</span></span>

<span data-ttu-id="26dd3-180">Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="26dd3-180">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="26dd3-181">Der Timer löst die `DoWork`-Methode des Tasks aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-181">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="26dd3-182">Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:</span><span class="sxs-lookup"><span data-stu-id="26dd3-182">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=16-18,34,41)]

<span data-ttu-id="26dd3-183">Der Dienst wird in `IHostBuilder.ConfigureServices` (*Program.cs*) mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="26dd3-183">The service is registered in `IHostBuilder.ConfigureServices` (*Program.cs*) with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="26dd3-184">Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask</span><span class="sxs-lookup"><span data-stu-id="26dd3-184">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="26dd3-185">Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in einem `BackgroundService` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-185">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within a `BackgroundService`, create a scope.</span></span> <span data-ttu-id="26dd3-186">Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-186">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="26dd3-187">Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="26dd3-187">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="26dd3-188">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="26dd3-188">In the following example:</span></span>

* <span data-ttu-id="26dd3-189">Der Dienst ist asynchron.</span><span class="sxs-lookup"><span data-stu-id="26dd3-189">The service is asynchronous.</span></span> <span data-ttu-id="26dd3-190">Die `DoWork`-Methode gibt `Task` zurück.</span><span class="sxs-lookup"><span data-stu-id="26dd3-190">The `DoWork` method returns a `Task`.</span></span> <span data-ttu-id="26dd3-191">Zu Demonstrationszwecken wird in der `DoWork`-Methode eine Verzögerung von zehn Sekunden verwendet.</span><span class="sxs-lookup"><span data-stu-id="26dd3-191">For demonstration purposes, a delay of ten seconds is awaited in the `DoWork` method.</span></span>
* <span data-ttu-id="26dd3-192">Ein <xref:Microsoft.Extensions.Logging.ILogger> wird in den Dienst eingefügt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-192">An <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="26dd3-193">Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, damit die `DoWork`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-193">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method.</span></span> <span data-ttu-id="26dd3-194">`DoWork` gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird:</span><span class="sxs-lookup"><span data-stu-id="26dd3-194">`DoWork` returns a `Task`, which is awaited in `ExecuteAsync`:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=19,22-35)]

<span data-ttu-id="26dd3-195">Die Dienste werden in `IHostBuilder.ConfigureServices` (*Program.cs*) registriert.</span><span class="sxs-lookup"><span data-stu-id="26dd3-195">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="26dd3-196">Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="26dd3-196">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="26dd3-197">Hintergrundtasks in der Warteschlange</span><span class="sxs-lookup"><span data-stu-id="26dd3-197">Queued background tasks</span></span>

<span data-ttu-id="26dd3-198">Eine Warteschlange für Hintergrundtasks basiert auf dem <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>von .NET 4.x ([die Integration in ASP.NET Core ist vorläufig geplant](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="26dd3-198">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="26dd3-199">Im folgenden Beispiel für `QueueHostedService` gilt:</span><span class="sxs-lookup"><span data-stu-id="26dd3-199">In the following `QueueHostedService` example:</span></span>

* <span data-ttu-id="26dd3-200">Die `BackgroundProcessing`-Methode gibt einen `Task` zurück, auf den in `ExecuteAsync` gewartet wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-200">The `BackgroundProcessing` method returns a `Task`, which is awaited in `ExecuteAsync`.</span></span>
* <span data-ttu-id="26dd3-201">Hintergrundtasks in der Warteschlange werden aus dieser entfernt und in `BackgroundProcessing` ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-201">Background tasks in the queue are dequeued and executed in `BackgroundProcessing`.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=28,39-40,44)]

<span data-ttu-id="26dd3-202">Ein `MonitorLoop`-Dienst verarbeitet das Einreihen von Tasks in die Warteschlange für den gehosteten Dienst, wenn der `w`-Schlüssel auf einem Eingabegerät ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="26dd3-202">A `MonitorLoop` service handles enqueuing tasks for the hosted service whenever the `w` key is selected on an input device:</span></span>

* <span data-ttu-id="26dd3-203">Die `IBackgroundTaskQueue` wird in den `MonitorLoop`-Dienst eingefügt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-203">The `IBackgroundTaskQueue` is injected into the `MonitorLoop` service.</span></span>
* <span data-ttu-id="26dd3-204">`IBackgroundTaskQueue.QueueBackgroundWorkItem` wird aufgerufen, um ein Arbeitselement in die Warteschlange einzureihen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-204">`IBackgroundTaskQueue.QueueBackgroundWorkItem` is called to enqueue a work item.</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Services/MonitorLoop.cs?name=snippet_Monitor&highlight=7,33)]

<span data-ttu-id="26dd3-205">Die Dienste werden in `IHostBuilder.ConfigureServices` (*Program.cs*) registriert.</span><span class="sxs-lookup"><span data-stu-id="26dd3-205">The services are registered in `IHostBuilder.ConfigureServices` (*Program.cs*).</span></span> <span data-ttu-id="26dd3-206">Der gehostete Dienst wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="26dd3-206">The hosted service is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/3.x/BackgroundTasksSample/Program.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26dd3-207">In ASP.NET Core können Hintergrundtasks als *gehostete Dienste* implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-207">In ASP.NET Core, background tasks can be implemented as *hosted services*.</span></span> <span data-ttu-id="26dd3-208">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="26dd3-208">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="26dd3-209">In diesem Artikel sind drei Beispiel für gehostete Dienste enthalten:</span><span class="sxs-lookup"><span data-stu-id="26dd3-209">This topic provides three hosted service examples:</span></span>

* <span data-ttu-id="26dd3-210">Hintergrundtasks, die auf einem Timer ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-210">Background task that runs on a timer.</span></span>
* <span data-ttu-id="26dd3-211">Gehostete Dienste, die einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes) aktivieren.</span><span class="sxs-lookup"><span data-stu-id="26dd3-211">Hosted service that activates a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="26dd3-212">Der bereichsbezogene Dienst kann eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) (Dependency Injection, DI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-212">The scoped service can use [dependency injection (DI)](xref:fundamentals/dependency-injection)</span></span>
* <span data-ttu-id="26dd3-213">Hintergrundtasks in der Warteschlange, die sequenziell ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-213">Queued background tasks that run sequentially.</span></span>

<span data-ttu-id="26dd3-214">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="26dd3-214">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="26dd3-215">Die Beispiel-App wird in zwei Versionen bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="26dd3-215">The sample app is provided in two versions:</span></span>

* <span data-ttu-id="26dd3-216">Web Host &ndash; Der Webhost eignet sich für das Hosten von Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="26dd3-216">Web Host &ndash; Web Host is useful for hosting web apps.</span></span> <span data-ttu-id="26dd3-217">Der in diesem Thema gezeigte Beispielcode stammt aus der Webhostversion des Beispiels.</span><span class="sxs-lookup"><span data-stu-id="26dd3-217">The example code shown in this topic is from Web Host version of the sample.</span></span> <span data-ttu-id="26dd3-218">Weitere Informationen finden Sie unter dem Thema [Webhost](xref:fundamentals/host/web-host).</span><span class="sxs-lookup"><span data-stu-id="26dd3-218">For more information, see the [Web Host](xref:fundamentals/host/web-host) topic.</span></span>
* <span data-ttu-id="26dd3-219">Generischer Host &ndash; Der generische Host wurde in ASP.NET Core 2.1 neu eingeführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-219">Generic Host &ndash; Generic Host is new in ASP.NET Core 2.1.</span></span> <span data-ttu-id="26dd3-220">Weitere Informationen finden Sie unter dem Thema [Generischer Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="26dd3-220">For more information, see the [Generic Host](xref:fundamentals/host/generic-host) topic.</span></span>

## <a name="package"></a><span data-ttu-id="26dd3-221">Package</span><span class="sxs-lookup"><span data-stu-id="26dd3-221">Package</span></span>

<span data-ttu-id="26dd3-222">Verweisen Sie auf das [Microsoft.AspNetCore.App-Metapaket](xref:fundamentals/metapackage-app), oder fügen Sie einen Paketverweis auf das Paket [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) hinzu.</span><span class="sxs-lookup"><span data-stu-id="26dd3-222">Reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting) package.</span></span>

## <a name="ihostedservice-interface"></a><span data-ttu-id="26dd3-223">Die IHostedService-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="26dd3-223">IHostedService interface</span></span>

<span data-ttu-id="26dd3-224">Gehostete Dienste implementieren die <xref:Microsoft.Extensions.Hosting.IHostedService>-Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="26dd3-224">Hosted services implement the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span> <span data-ttu-id="26dd3-225">Die Schnittstelle definiert zwei Methoden für Objekte, die vom Host verwaltet werden:</span><span class="sxs-lookup"><span data-stu-id="26dd3-225">The interface defines two methods for objects that are managed by the host:</span></span>

* <span data-ttu-id="26dd3-226">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` enthält die Logik zum Starten des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="26dd3-226">[StartAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StartAsync*) &ndash; `StartAsync` contains the logic to start the background task.</span></span> <span data-ttu-id="26dd3-227">Bei Verwendung des [Webhosts](xref:fundamentals/host/web-host) wird `StartAsync` aufgerufen, nachdem der Server gestartet und [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) ausgelöst wurde.</span><span class="sxs-lookup"><span data-stu-id="26dd3-227">When using the [Web Host](xref:fundamentals/host/web-host), `StartAsync` is called after the server has started and [IApplicationLifetime.ApplicationStarted](xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime.ApplicationStarted*) is triggered.</span></span> <span data-ttu-id="26dd3-228">Bei Verwendung des [generischen Hosts](xref:fundamentals/host/generic-host) wird `StartAsync` aufgerufen, bevor `ApplicationStarted` ausgelöst wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-228">When using the [Generic Host](xref:fundamentals/host/generic-host), `StartAsync` is called before `ApplicationStarted` is triggered.</span></span>

* <span data-ttu-id="26dd3-229">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; wird ausgelöst, wenn der Host das Herunterfahren ordnungsgemäß ausführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-229">[StopAsync(CancellationToken)](xref:Microsoft.Extensions.Hosting.IHostedService.StopAsync*) &ndash; Triggered when the host is performing a graceful shutdown.</span></span> <span data-ttu-id="26dd3-230">`StopAsync` enthält die Logik zum Beenden des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="26dd3-230">`StopAsync` contains the logic to end the background task.</span></span> <span data-ttu-id="26dd3-231">Implementieren Sie <xref:System.IDisposable> und [Finalizer (Destruktoren)](/dotnet/csharp/programming-guide/classes-and-structs/destructors), um nicht verwaltete Ressourcen zu löschen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-231">Implement <xref:System.IDisposable> and [finalizers (destructors)](/dotnet/csharp/programming-guide/classes-and-structs/destructors) to dispose of any unmanaged resources.</span></span>

  <span data-ttu-id="26dd3-232">Das Abbruchtoken hat standardmäßig ein Zeitlimit von fünf Sekunden, um zu melden, dass der Prozess des Herunterfahrens nicht mehr ordnungsgemäß ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-232">The cancellation token has a default five second timeout to indicate that the shutdown process should no longer be graceful.</span></span> <span data-ttu-id="26dd3-233">Gehen Sie wie folgt vor, wenn ein Abbruch für das Token angefordert wird:</span><span class="sxs-lookup"><span data-stu-id="26dd3-233">When cancellation is requested on the token:</span></span>

  * <span data-ttu-id="26dd3-234">Brechen Sie jegliche von der App ausgeführten Hintergrundoperationen ab.</span><span class="sxs-lookup"><span data-stu-id="26dd3-234">Any remaining background operations that the app is performing should be aborted.</span></span>
  * <span data-ttu-id="26dd3-235">Jegliche Methoden, die in `StopAsync` aufgerufen werden, sollten umgehend zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-235">Any methods called in `StopAsync` should return promptly.</span></span>

  <span data-ttu-id="26dd3-236">Allerdings werden keine Tasks abgebrochen, wenn der Abbruch angefordert wird. Der Aufrufer wartet, bis alle Tasks abgeschlossen sind.</span><span class="sxs-lookup"><span data-stu-id="26dd3-236">However, tasks aren't abandoned after cancellation is requested&mdash;the caller awaits all tasks to complete.</span></span>

  <span data-ttu-id="26dd3-237">Wenn die App unerwartet beendet wird (weil der Prozess der App beispielsweise fehlschlägt), wird `StopAsync` möglicherweise nicht aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-237">If the app shuts down unexpectedly (for example, the app's process fails), `StopAsync` might not be called.</span></span> <span data-ttu-id="26dd3-238">Daher werden die in `StopAsync` aufgerufenen Methoden oder ausgeführten Operationen nicht durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-238">Therefore, any methods called or operations conducted in `StopAsync` might not occur.</span></span>

  <span data-ttu-id="26dd3-239">Um das standardmäßig 5-sekündige Timeout beim Herunterfahren zu verlängern, legen Sie folgendes fest:</span><span class="sxs-lookup"><span data-stu-id="26dd3-239">To extend the default five second shutdown timeout, set:</span></span>

  * <span data-ttu-id="26dd3-240"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*>, wenn Sie den generischen Host verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-240"><xref:Microsoft.Extensions.Hosting.HostOptions.ShutdownTimeout*> when using Generic Host.</span></span> <span data-ttu-id="26dd3-241">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="26dd3-241">For more information, see <xref:fundamentals/host/generic-host#shutdown-timeout>.</span></span>
  * <span data-ttu-id="26dd3-242">Hostkonfigurationseinstellung „Timeout beim Herunterfahren“, wenn Sie den Webhost verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-242">Shutdown timeout host configuration setting when using Web Host.</span></span> <span data-ttu-id="26dd3-243">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#shutdown-timeout>.</span><span class="sxs-lookup"><span data-stu-id="26dd3-243">For more information, see <xref:fundamentals/host/web-host#shutdown-timeout>.</span></span>

<span data-ttu-id="26dd3-244">Der gehostete Dienst wird beim Start der App einmal aktiviert und beim Beenden der App wieder ordnungsgemäß heruntergefahren.</span><span class="sxs-lookup"><span data-stu-id="26dd3-244">The hosted service is activated once at app startup and gracefully shut down at app shutdown.</span></span> <span data-ttu-id="26dd3-245">Wenn während der Ausführung von Hintergrundtasks ein Fehler ausgelöst wird, sollte `Dispose` aufgerufen werden, auch wenn `StopAsync` nicht aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="26dd3-245">If an error is thrown during background task execution, `Dispose` should be called even if `StopAsync` isn't called.</span></span>

## <a name="timed-background-tasks"></a><span data-ttu-id="26dd3-246">Zeitlich festgelegte Hintergrundtasks</span><span class="sxs-lookup"><span data-stu-id="26dd3-246">Timed background tasks</span></span>

<span data-ttu-id="26dd3-247">Zeitlich festgelegte Hintergrundtasks verwenden die Klasse [System.Threading.Timer](xref:System.Threading.Timer).</span><span class="sxs-lookup"><span data-stu-id="26dd3-247">A timed background task makes use of the [System.Threading.Timer](xref:System.Threading.Timer) class.</span></span> <span data-ttu-id="26dd3-248">Der Timer löst die `DoWork`-Methode des Tasks aus.</span><span class="sxs-lookup"><span data-stu-id="26dd3-248">The timer triggers the task's `DoWork` method.</span></span> <span data-ttu-id="26dd3-249">Der Timer wird durch `StopAsync` deaktiviert und freigegeben, wenn der Dienstcontainer durch `Dispose` freigegeben ist:</span><span class="sxs-lookup"><span data-stu-id="26dd3-249">The timer is disabled on `StopAsync` and disposed when the service container is disposed on `Dispose`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/TimedHostedService.cs?name=snippet1&highlight=15-16,30,37)]

<span data-ttu-id="26dd3-250">Der Dienst wird in `Startup.ConfigureServices` mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="26dd3-250">The service is registered in `Startup.ConfigureServices` with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet1)]

## <a name="consuming-a-scoped-service-in-a-background-task"></a><span data-ttu-id="26dd3-251">Verwenden eines bereichsbezogenen Diensts in einem Hintergrundtask</span><span class="sxs-lookup"><span data-stu-id="26dd3-251">Consuming a scoped service in a background task</span></span>

<span data-ttu-id="26dd3-252">Erstellen Sie einen Bereich, um [bereichsbezogene Dienste](xref:fundamentals/dependency-injection#service-lifetimes) in `IHostedService` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="26dd3-252">To use [scoped services](xref:fundamentals/dependency-injection#service-lifetimes) within an `IHostedService`, create a scope.</span></span> <span data-ttu-id="26dd3-253">Bereiche werden für einen gehosteten Dienst nicht standardmäßig erstellt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-253">No scope is created for a hosted service by default.</span></span>

<span data-ttu-id="26dd3-254">Der bereichsbezogene Dienst für Hintergrundtasks enthält die Logik des Hintergrundtasks.</span><span class="sxs-lookup"><span data-stu-id="26dd3-254">The scoped background task service contains the background task's logic.</span></span> <span data-ttu-id="26dd3-255">Im folgenden Beispiel wird <xref:Microsoft.Extensions.Logging.ILogger> in den Dienst eingefügt:</span><span class="sxs-lookup"><span data-stu-id="26dd3-255">In the following example, an <xref:Microsoft.Extensions.Logging.ILogger> is injected into the service:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ScopedProcessingService.cs?name=snippet1)]

<span data-ttu-id="26dd3-256">Der gehostete Dienst erstellt einen Bereich, um den bereichsbezogenen Dienst für Hintergrundtasks aufzulösen, um die `DoWork`-Methode aufzurufen:</span><span class="sxs-lookup"><span data-stu-id="26dd3-256">The hosted service creates a scope to resolve the scoped background task service to call its `DoWork` method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/ConsumeScopedServiceHostedService.cs?name=snippet1&highlight=29-36)]

<span data-ttu-id="26dd3-257">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="26dd3-257">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="26dd3-258">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="26dd3-258">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet2)]

## <a name="queued-background-tasks"></a><span data-ttu-id="26dd3-259">Hintergrundtasks in der Warteschlange</span><span class="sxs-lookup"><span data-stu-id="26dd3-259">Queued background tasks</span></span>

<span data-ttu-id="26dd3-260">Eine Warteschlange für Hintergrundtasks basiert auf dem <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*>von .NET 4.x ([die Integration in ASP.NET Core ist vorläufig geplant](https://github.com/aspnet/Hosting/issues/1280)):</span><span class="sxs-lookup"><span data-stu-id="26dd3-260">A background task queue is based on the .NET 4.x <xref:System.Web.Hosting.HostingEnvironment.QueueBackgroundWorkItem*> ([tentatively scheduled to be built-in for ASP.NET Core](https://github.com/aspnet/Hosting/issues/1280)):</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/BackgroundTaskQueue.cs?name=snippet1)]

<span data-ttu-id="26dd3-261">In `QueueHostedService` werden Hintergrundaufgaben in der Warteschlange aus der Warteschlange entfernt und als <xref:Microsoft.Extensions.Hosting.BackgroundService>, eine Basisklasse zum Bereitstellen von `IHostedService` mit langer Ausführungszeit, ausgeführt:</span><span class="sxs-lookup"><span data-stu-id="26dd3-261">In `QueueHostedService`, background tasks in the queue are dequeued and executed as a <xref:Microsoft.Extensions.Hosting.BackgroundService>, which is a base class for implementing a long running `IHostedService`:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Services/QueuedHostedService.cs?name=snippet1&highlight=21,25)]

<span data-ttu-id="26dd3-262">Die Dienste werden in `Startup.ConfigureServices` registriert.</span><span class="sxs-lookup"><span data-stu-id="26dd3-262">The services are registered in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="26dd3-263">Der Implementierung `IHostedService` wird mit der Erweiterungsmethode `AddHostedService` registriert:</span><span class="sxs-lookup"><span data-stu-id="26dd3-263">The `IHostedService` implementation is registered with the `AddHostedService` extension method:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Startup.cs?name=snippet3)]

<span data-ttu-id="26dd3-264">In der Modellklasse für die Indexseite:</span><span class="sxs-lookup"><span data-stu-id="26dd3-264">In the Index page model class:</span></span>

* <span data-ttu-id="26dd3-265">`IBackgroundTaskQueue` wird in den Konstruktor eingefügt und `Queue` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-265">The `IBackgroundTaskQueue` is injected into the constructor and assigned to `Queue`.</span></span>
* <span data-ttu-id="26dd3-266">Ein <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> wird eingefügt und `_serviceScopeFactory` zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-266">An <xref:Microsoft.Extensions.DependencyInjection.IServiceScopeFactory> is injected and assigned to `_serviceScopeFactory`.</span></span> <span data-ttu-id="26dd3-267">Die Zuordnungsinstanz wird verwendet, um Instanzen von <xref:Microsoft.Extensions.DependencyInjection.IServiceScope> zu erstellen. Dieser wird verwendet, um Dienste in einem Bereich zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="26dd3-267">The factory is used to create instances of <xref:Microsoft.Extensions.DependencyInjection.IServiceScope>, which is used to create services within a scope.</span></span> <span data-ttu-id="26dd3-268">Ein Bereich wird erstellt, um den `AppDbContext` der App (einen [bereichsbezogenen Dienst](xref:fundamentals/dependency-injection#service-lifetimes)) zu verwenden, um Datenbankeinträge in `IBackgroundTaskQueue` (ein Singletondienst) zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="26dd3-268">A scope is created in order to use the app's `AppDbContext` (a [scoped service](xref:fundamentals/dependency-injection#service-lifetimes)) to write database records in the `IBackgroundTaskQueue` (a singleton service).</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="26dd3-269">Wenn auf der Indexseite auf die Schaltfläche **Task hinzufügen** geklickt wird, wird die `OnPostAddTask`-Methode ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26dd3-269">When the **Add Task** button is selected on the Index page, the `OnPostAddTask` method is executed.</span></span> <span data-ttu-id="26dd3-270">`QueueBackgroundWorkItem` wird aufgerufen, um ein Arbeitselement in die Warteschlange einzureihen:</span><span class="sxs-lookup"><span data-stu-id="26dd3-270">`QueueBackgroundWorkItem` is called to enqueue a work item:</span></span>

[!code-csharp[](hosted-services/samples/2.x/BackgroundTasksSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="26dd3-271">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="26dd3-271">Additional resources</span></span>

* [<span data-ttu-id="26dd3-272">Implement background tasks in microservices with IHostedService and the BackgroundService class (Implementieren von Hintergrundtasks in Microservices mit IHostedService und der BackgroundService-Klasse)</span><span class="sxs-lookup"><span data-stu-id="26dd3-272">Implement background tasks in microservices with IHostedService and the BackgroundService class</span></span>](/dotnet/standard/microservices-architecture/multi-container-microservice-net-applications/background-tasks-with-ihostedservice)
* <xref:System.Threading.Timer>
