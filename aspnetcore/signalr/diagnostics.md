---
<span data-ttu-id="e7f69-101">Title: "Protokollierung und Diagnose in ASP.net Core SignalR " Author: Description: "erfahren Sie, wie Sie Diagnoseinformationen aus Ihrer ASP.net Core- SignalR App erfassen."</span><span class="sxs-lookup"><span data-stu-id="e7f69-101">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e7f69-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e7f69-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7f69-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-103">'Blazor'</span></span>
- <span data-ttu-id="e7f69-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7f69-104">'Identity'</span></span>
- <span data-ttu-id="e7f69-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7f69-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7f69-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-106">'Razor'</span></span>
- <span data-ttu-id="e7f69-107">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e7f69-107">'SignalR' uid:</span></span> 

---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="e7f69-108">Protokollierung und Diagnose in ASP.net CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="e7f69-108">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="e7f69-109">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="e7f69-109">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="e7f69-110">Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus Ihrer ASP.net Core- SignalR app, um Probleme bei der Problembehandlung zu beheben.</span><span class="sxs-lookup"><span data-stu-id="e7f69-110">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="e7f69-111">Server seitige Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e7f69-111">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="e7f69-112">Serverseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="e7f69-112">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e7f69-113">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="e7f69-113">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e7f69-114">Da SignalR Teil ASP.net Core ist, wird das ASP.net Core Protokollierungs System verwendet.</span><span class="sxs-lookup"><span data-stu-id="e7f69-114">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="e7f69-115">In der Standardkonfiguration protokolliert nur wenige SignalR Informationen, aber dies kann konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e7f69-115">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="e7f69-116">In der Dokumentation zur [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) finden Sie weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="e7f69-116">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

SignalR<span data-ttu-id="e7f69-117">verwendet zwei Kategorien von Kategorien:</span><span class="sxs-lookup"><span data-stu-id="e7f69-117"> uses two logger categories:</span></span>

* <span data-ttu-id="e7f69-118">`Microsoft.AspNetCore.SignalR`: Bei Protokollen im Zusammenhang mit hubprotokollen, beim Aktivieren von Hubs, beim Aufrufen von Methoden und anderen hubbezogenen Aktivitäten.</span><span class="sxs-lookup"><span data-stu-id="e7f69-118">`Microsoft.AspNetCore.SignalR`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="e7f69-119">`Microsoft.AspNetCore.Http.Connections`: Bei Protokollen, die sich auf Transporte beziehen, wie z. b. websockets, langes abrufen, vom Server gesendete Ereignisse und Low-Level- SignalR Infrastrukturen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-119">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="e7f69-120">Um ausführliche Protokolle von zu aktivieren SignalR , konfigurieren Sie beide vorangehenden Präfixe für die `Debug` Ebene in der Datei *appSettings. JSON* , indem Sie die folgenden Elemente zum `LogLevel` unter Abschnitt in hinzufügen `Logging` :</span><span class="sxs-lookup"><span data-stu-id="e7f69-120">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="e7f69-121">Sie können dies auch in Code in der- `CreateWebHostBuilder` Methode konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="e7f69-121">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="e7f69-122">Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie die folgenden Konfigurationswerte in Ihrem Konfigurationssystem fest:</span><span class="sxs-lookup"><span data-stu-id="e7f69-122">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="e7f69-123">Suchen Sie in der Dokumentation nach Informationen zu Ihrem Konfigurationssystem, um zu bestimmen, wie geschachtelte Konfigurationswerte angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="e7f69-123">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="e7f69-124">Bei der Verwendung von Umgebungsvariablen werden beispielsweise zwei `_`-Zeichen anstelle von `:` verwendet (z. B. `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="e7f69-124">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="e7f69-125">Es empfiehlt sich, die `Debug`-Ebene zu verwenden, wenn detailliertere Diagnosedaten Ihrer App gesammelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-125">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="e7f69-126">Die `Trace`-Ebene bietet nur sehr wenige Diagnoseinformationen und wird nur selten verwendet, um App-Probleme zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="e7f69-126">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="e7f69-127">Zugreifen auf serverseitige Protokolle</span><span class="sxs-lookup"><span data-stu-id="e7f69-127">Access server-side logs</span></span>

<span data-ttu-id="e7f69-128">Wie Sie auf serverseitige Protokolle zugreifen können, hängt von der Ausführungsumgebung ab.</span><span class="sxs-lookup"><span data-stu-id="e7f69-128">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="e7f69-129">Als Konsolen-App außerhalb von IIS</span><span class="sxs-lookup"><span data-stu-id="e7f69-129">As a console app outside IIS</span></span>

<span data-ttu-id="e7f69-130">Wenn die Ausführung in einer Konsolen-App stattfindet, sollte die [Konsolenprotokollierung](xref:fundamentals/logging/index#console) standardmäßig aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="e7f69-130">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> SignalR<span data-ttu-id="e7f69-131">die Protokolle werden in der-Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e7f69-131"> logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="e7f69-132">In IIS Express aus Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e7f69-132">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="e7f69-133">Visual Studio zeigt die Protokoll Ausgabe im **Ausgabe** Fenster an.</span><span class="sxs-lookup"><span data-stu-id="e7f69-133">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="e7f69-134">Wählen Sie die Dropdown Option **ASP.net Core Webserver** aus.</span><span class="sxs-lookup"><span data-stu-id="e7f69-134">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="e7f69-135">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="e7f69-135">Azure App Service</span></span>

<span data-ttu-id="e7f69-136">Aktivieren Sie die Option **Anwendungs Protokollierung (File System)** im Abschnitt **Diagnoseprotokolle** des Azure App Service Portals, und konfigurieren Sie die **Ebene** auf `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="e7f69-136">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="e7f69-137">Protokolle sollten über den **protokollstreamingdienst** und in Protokollen im Dateisystem des App Service verfügbar sein.</span><span class="sxs-lookup"><span data-stu-id="e7f69-137">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="e7f69-138">Weitere Informationen finden Sie unter [Azure-Protokoll Streaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="e7f69-138">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="e7f69-139">Andere Umgebungen</span><span class="sxs-lookup"><span data-stu-id="e7f69-139">Other environments</span></span>

<span data-ttu-id="e7f69-140">Wenn die App in einer anderen Umgebung bereitgestellt wird (z. B. Docker, Kubernetes oder Windows-Dienst) finden Sie unter <xref:fundamentals/logging/index> weitere Informationen zum Konfigurieren von Protokollierungsanbietern, die für die Umgebung geeignet sind.</span><span class="sxs-lookup"><span data-stu-id="e7f69-140">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="e7f69-141">JavaScript-Client Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e7f69-141">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e7f69-142">Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="e7f69-142">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e7f69-143">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="e7f69-143">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e7f69-144">Wenn Sie den JavaScript-Client verwenden, können Sie die Protokollierungs Optionen mithilfe der `configureLogging` Methode für konfigurieren `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="e7f69-144">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="e7f69-145">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="e7f69-145">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="e7f69-146">In der folgenden Tabelle werden die für den JavaScript-Client verfügbaren Protokoll Ebenen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e7f69-146">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="e7f69-147">Wenn Sie die Protokollebene auf einen dieser Werte festlegen, wird die Protokollierung auf dieser Ebene und allen darüber liegenden Ebenen in der Tabelle ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="e7f69-147">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="e7f69-148">Ebene</span><span class="sxs-lookup"><span data-stu-id="e7f69-148">Level</span></span> | <span data-ttu-id="e7f69-149">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="e7f69-149">Description</span></span> |
| ----- | ---
<span data-ttu-id="e7f69-150">Title: "Protokollierung und Diagnose in ASP.net Core SignalR " Author: Description: "erfahren Sie, wie Sie Diagnoseinformationen aus Ihrer ASP.net Core- SignalR App erfassen."</span><span class="sxs-lookup"><span data-stu-id="e7f69-150">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e7f69-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e7f69-151">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7f69-152">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-152">'Blazor'</span></span>
- <span data-ttu-id="e7f69-153">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7f69-153">'Identity'</span></span>
- <span data-ttu-id="e7f69-154">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7f69-154">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7f69-155">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-155">'Razor'</span></span>
- <span data-ttu-id="e7f69-156">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e7f69-156">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7f69-157">Title: "Protokollierung und Diagnose in ASP.net Core SignalR " Author: Description: "erfahren Sie, wie Sie Diagnoseinformationen aus Ihrer ASP.net Core- SignalR App erfassen."</span><span class="sxs-lookup"><span data-stu-id="e7f69-157">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e7f69-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e7f69-158">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7f69-159">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-159">'Blazor'</span></span>
- <span data-ttu-id="e7f69-160">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7f69-160">'Identity'</span></span>
- <span data-ttu-id="e7f69-161">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7f69-161">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7f69-162">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-162">'Razor'</span></span>
- <span data-ttu-id="e7f69-163">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e7f69-163">'SignalR' uid:</span></span> 

-
<span data-ttu-id="e7f69-164">Title: "Protokollierung und Diagnose in ASP.net Core SignalR " Author: Description: "erfahren Sie, wie Sie Diagnoseinformationen aus Ihrer ASP.net Core- SignalR App erfassen."</span><span class="sxs-lookup"><span data-stu-id="e7f69-164">title: 'Logging and diagnostics in ASP.NET Core SignalR' author: description: 'Learn how to gather diagnostics from your ASP.NET Core SignalR app.'</span></span>
<span data-ttu-id="e7f69-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="e7f69-165">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e7f69-166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-166">'Blazor'</span></span>
- <span data-ttu-id="e7f69-167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e7f69-167">'Identity'</span></span>
- <span data-ttu-id="e7f69-168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e7f69-168">'Let's Encrypt'</span></span>
- <span data-ttu-id="e7f69-169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e7f69-169">'Razor'</span></span>
- <span data-ttu-id="e7f69-170">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="e7f69-170">'SignalR' uid:</span></span> 

<span data-ttu-id="e7f69-171">------ | | `None` | Es werden keine Nachrichten protokolliert.</span><span class="sxs-lookup"><span data-stu-id="e7f69-171">------ | | `None` | No messages are logged.</span></span> <span data-ttu-id="e7f69-172">| | `Critical` | Meldungen, die auf einen Fehler in der gesamten App hindeuten.</span><span class="sxs-lookup"><span data-stu-id="e7f69-172">| | `Critical` | Messages that indicate a failure in the entire app.</span></span> <span data-ttu-id="e7f69-173">| | `Error` | Meldungen, die auf einen Fehler im aktuellen Vorgang hindeuten.</span><span class="sxs-lookup"><span data-stu-id="e7f69-173">| | `Error` | Messages that indicate a failure in the current operation.</span></span> <span data-ttu-id="e7f69-174">| | `Warning` | Meldungen, die auf ein nicht schwer wiedliches Problem hinweisen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-174">| | `Warning` | Messages that indicate a non-fatal problem.</span></span> <span data-ttu-id="e7f69-175">| | `Information` | Informationsmeldungen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-175">| | `Information` | Informational messages.</span></span> <span data-ttu-id="e7f69-176">| | `Debug` | Diagnosemeldungen sind für das Debuggen nützlich.</span><span class="sxs-lookup"><span data-stu-id="e7f69-176">| | `Debug` | Diagnostic messages useful for debugging.</span></span> <span data-ttu-id="e7f69-177">| | `Trace` | Sehr ausführliche Diagnosemeldungen, die für die Diagnose bestimmter Probleme entwickelt wurden.</span><span class="sxs-lookup"><span data-stu-id="e7f69-177">| | `Trace` | Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="e7f69-178">Nachdem Sie die Ausführlichkeit konfiguriert haben, werden die Protokolle in die Browser Konsole (oder die Standard Ausgabe in einer nodejs-APP) geschrieben.</span><span class="sxs-lookup"><span data-stu-id="e7f69-178">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="e7f69-179">Wenn Sie Protokolle an ein benutzerdefiniertes Protokollierungs System senden möchten, können Sie ein JavaScript-Objekt bereitstellen, das die- `ILogger` Schnittstelle implementiert.</span><span class="sxs-lookup"><span data-stu-id="e7f69-179">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="e7f69-180">Die einzige Methode, die implementiert werden muss `log` , ist, die die Ebene des Ereignisses und die dem Ereignis zugeordnete Meldung annimmt.</span><span class="sxs-lookup"><span data-stu-id="e7f69-180">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="e7f69-181">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e7f69-181">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="e7f69-182">.NET-Client Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e7f69-182">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e7f69-183">Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="e7f69-183">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e7f69-184">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="e7f69-184">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e7f69-185">Um Protokolle vom .NET-Client zu erhalten, können Sie die- `ConfigureLogging` Methode für verwenden `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e7f69-185">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="e7f69-186">Dies funktioniert genauso wie die `ConfigureLogging` -Methode in `WebHostBuilder` und `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="e7f69-186">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="e7f69-187">Sie können die gleichen Protokollierungs Anbieter konfigurieren, die Sie in ASP.net Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="e7f69-187">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="e7f69-188">Allerdings müssen Sie die nuget-Pakete für die einzelnen Protokollierungs Anbieter manuell installieren und aktivieren.</span><span class="sxs-lookup"><span data-stu-id="e7f69-188">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="e7f69-189">Konsolenprotokollierung</span><span class="sxs-lookup"><span data-stu-id="e7f69-189">Console logging</span></span>

<span data-ttu-id="e7f69-190">Fügen Sie das Paket [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) hinzu, um die Konsolen Protokollierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="e7f69-190">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="e7f69-191">Verwenden Sie dann die- `AddConsole` Methode zum Konfigurieren der Konsolen Protokollierung:</span><span class="sxs-lookup"><span data-stu-id="e7f69-191">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="e7f69-192">Debuggen des Ausgabe Fensters</span><span class="sxs-lookup"><span data-stu-id="e7f69-192">Debug output window logging</span></span>

<span data-ttu-id="e7f69-193">Sie können Protokolle auch so konfigurieren, dass Sie zum Fenster **Ausgabe** in Visual Studio wechseln.</span><span class="sxs-lookup"><span data-stu-id="e7f69-193">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="e7f69-194">Installieren Sie das Paket " [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) ", und verwenden Sie die- `AddDebug` Methode:</span><span class="sxs-lookup"><span data-stu-id="e7f69-194">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="e7f69-195">Andere Protokollierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="e7f69-195">Other logging providers</span></span>

SignalR<span data-ttu-id="e7f69-196">unterstützt andere Protokollierungs Anbieter wie serilog, seq, nlog oder ein beliebiges anderes Protokollierungs System, das in integriert ist `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="e7f69-196"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="e7f69-197">Wenn Ihr Protokollierungs System ein bereitstellt `ILoggerProvider` , können Sie es mit folgenden Aktionen registrieren `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="e7f69-197">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="e7f69-198">Ausführlichkeit von Steuerelementen</span><span class="sxs-lookup"><span data-stu-id="e7f69-198">Control verbosity</span></span>

<span data-ttu-id="e7f69-199">Wenn Sie sich an anderen Stellen in ihrer App anmelden, ist das Ändern der Standard Ebene in `Debug` möglicherweise zu ausführlich.</span><span class="sxs-lookup"><span data-stu-id="e7f69-199">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="e7f69-200">Sie können einen Filter verwenden, um den Protokolliergrad für Protokolle zu konfigurieren SignalR .</span><span class="sxs-lookup"><span data-stu-id="e7f69-200">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="e7f69-201">Dies kann im Code auf die gleiche Weise wie auf dem Server erfolgen:</span><span class="sxs-lookup"><span data-stu-id="e7f69-201">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="e7f69-202">Netzwerkablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="e7f69-202">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="e7f69-203">Eine Netzwerk Ablauf Verfolgung enthält den gesamten Inhalt jeder Nachricht, die von Ihrer APP gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="e7f69-203">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="e7f69-204">Veröffentlichen Sie **niemals** unformatierte Netzwerk Ablauf Verfolgungen aus Produktions-apps in öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="e7f69-204">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e7f69-205">Wenn ein Problem auftritt, kann es vorkommen, dass eine Netzwerk Ablauf Verfolgung viele hilfreiche Informationen liefert.</span><span class="sxs-lookup"><span data-stu-id="e7f69-205">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="e7f69-206">Dies ist besonders nützlich, wenn Sie ein Problem in unserer Problemverfolgung melden.</span><span class="sxs-lookup"><span data-stu-id="e7f69-206">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="e7f69-207">Erfassen einer Netzwerk Ablauf Verfolgung mit "fddler" (bevorzugte Option)</span><span class="sxs-lookup"><span data-stu-id="e7f69-207">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="e7f69-208">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="e7f69-208">This method works for all apps.</span></span>

<span data-ttu-id="e7f69-209">"Fddler" ist ein sehr leistungsfähiges Tool zum Erfassen von http-Ablauf Verfolgungen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-209">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="e7f69-210">Installieren Sie Sie über [Telerik.com/Fiddler](https://www.telerik.com/fiddler), starten Sie Sie, und führen Sie dann die APP aus.</span><span class="sxs-lookup"><span data-stu-id="e7f69-210">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="e7f69-211">"Fddler" ist für Windows verfügbar, und es gibt Beta Versionen für macOS und Linux.</span><span class="sxs-lookup"><span data-stu-id="e7f69-211">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="e7f69-212">Wenn Sie eine Verbindung über HTTPS herstellen, sind einige zusätzliche Schritte erforderlich, um sicherzustellen, dass der HTTPS-Datenverkehr von fddler entschlüsselt werden kann</span><span class="sxs-lookup"><span data-stu-id="e7f69-212">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="e7f69-213">Weitere Informationen finden Sie in der [Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)zu "Dokumentation".</span><span class="sxs-lookup"><span data-stu-id="e7f69-213">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="e7f69-214">Nachdem Sie die Ablauf Verfolgung gesammelt haben, können Sie die Ablauf Verfolgung exportieren **File**, indem Sie auf  >  **Save**  >  der Menüleiste auf Datei**alle Sitzungen** speichern klicken.</span><span class="sxs-lookup"><span data-stu-id="e7f69-214">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportieren aller Sitzungen aus "fddler"](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="e7f69-216">Erfassen einer Netzwerk Ablauf Verfolgung mit tcpdump (nur macOS und Linux)</span><span class="sxs-lookup"><span data-stu-id="e7f69-216">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="e7f69-217">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="e7f69-217">This method works for all apps.</span></span>

<span data-ttu-id="e7f69-218">Sie können unformatierte TCP-Ablauf Verfolgungen mithilfe von tcpdump erfassen, indem Sie den folgenden Befehl in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-218">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="e7f69-219">`root` `sudo` Wenn Sie einen Berechtigungs Fehler erhalten, müssen Sie möglicherweise den Befehl mit einem Präfix versehen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-219">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="e7f69-220">Ersetzen `[interface]` Sie durch die Netzwerkschnittstelle, die Sie erfassen möchten.</span><span class="sxs-lookup"><span data-stu-id="e7f69-220">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="e7f69-221">In der Regel handelt es sich hierbei um `/dev/eth0` (für die standardethernet-Schnittstelle) oder `/dev/lo0` (für localhost-Datenverkehr).</span><span class="sxs-lookup"><span data-stu-id="e7f69-221">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="e7f69-222">Weitere Informationen finden `tcpdump` Sie auf der Seite "man" auf Ihrem Host System.</span><span class="sxs-lookup"><span data-stu-id="e7f69-222">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="e7f69-223">Erfassen einer Netzwerk Ablauf Verfolgung im Browser</span><span class="sxs-lookup"><span data-stu-id="e7f69-223">Collect a network trace in the browser</span></span>

<span data-ttu-id="e7f69-224">Diese Methode funktioniert nur bei browserbasierten apps.</span><span class="sxs-lookup"><span data-stu-id="e7f69-224">This method only works for browser-based apps.</span></span>

<span data-ttu-id="e7f69-225">Die meisten Browser Entwicklertools über eine Registerkarte "Netzwerk" verfügen, mit der Sie die Netzwerkaktivität zwischen dem Browser und dem Server erfassen können.</span><span class="sxs-lookup"><span data-stu-id="e7f69-225">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="e7f69-226">Diese Ablauf Verfolgungen enthalten jedoch keine WebSocket-und vom Server gesendeten Ereignis Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="e7f69-226">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="e7f69-227">Wenn Sie diese Transporte verwenden, ist die Verwendung eines Tools wie z. b. "fddler" oder "tcpdump" (unten beschrieben) ein besserer Ansatz.</span><span class="sxs-lookup"><span data-stu-id="e7f69-227">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="e7f69-228">Microsoft Edge und Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="e7f69-228">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="e7f69-229">(Die Anweisungen sind für Edge und Internet Explorer identisch.)</span><span class="sxs-lookup"><span data-stu-id="e7f69-229">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="e7f69-230">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-230">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e7f69-231">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="e7f69-231">Click the Network Tab</span></span>
3. <span data-ttu-id="e7f69-232">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="e7f69-232">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e7f69-233">Klicken Sie in der Symbolleiste auf das Symbol speichern, um die Ablauf Verfolgung als "har"-Datei zu exportieren:</span><span class="sxs-lookup"><span data-stu-id="e7f69-233">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Symbol "Speichern" auf der Registerkarte "Netzwerk" von Microsoft Edge dev tools](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="e7f69-235">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="e7f69-235">Google Chrome</span></span>

1. <span data-ttu-id="e7f69-236">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-236">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e7f69-237">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="e7f69-237">Click the Network Tab</span></span>
3. <span data-ttu-id="e7f69-238">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="e7f69-238">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e7f69-239">Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Anforderungsliste, und wählen Sie "Speichern unter har mit Inhalt"</span><span class="sxs-lookup"><span data-stu-id="e7f69-239">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Option "Speichern unter har mit Inhalt" auf der Registerkarte "Google Chrome dev Tools Network"](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="e7f69-241">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="e7f69-241">Mozilla Firefox</span></span>

1. <span data-ttu-id="e7f69-242">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-242">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="e7f69-243">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="e7f69-243">Click the Network Tab</span></span>
3. <span data-ttu-id="e7f69-244">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="e7f69-244">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="e7f69-245">Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Liste der Anforderungen, und wählen Sie "alles als har</span><span class="sxs-lookup"><span data-stu-id="e7f69-245">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Option "alle als har speichern" auf der Registerkarte "Mozilla Firefox dev Tools Network"](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="e7f69-247">Anfügen von Diagnose Dateien an GitHub-Probleme</span><span class="sxs-lookup"><span data-stu-id="e7f69-247">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="e7f69-248">Sie können Diagnose Dateien an GitHub-Probleme anfügen, indem Sie Sie so umbenennen, dass Sie über eine Erweiterung verfügen, und Sie `.txt` dann auf das Problem ziehen und ablegen.</span><span class="sxs-lookup"><span data-stu-id="e7f69-248">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="e7f69-249">Fügen Sie den Inhalt von Protokolldateien oder Netzwerk Ablauf Verfolgungen nicht in ein GitHub-Problem ein.</span><span class="sxs-lookup"><span data-stu-id="e7f69-249">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="e7f69-250">Diese Protokolle und Ablauf Verfolgungen können sehr umfangreich sein, und GitHub verkürzt Sie in der Regel.</span><span class="sxs-lookup"><span data-stu-id="e7f69-250">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Ziehen von Protokolldateien auf ein GitHub-Problem](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="e7f69-252">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e7f69-252">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
