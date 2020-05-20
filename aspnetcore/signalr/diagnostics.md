---
title: Protokollierung und Diagnose in ASP.net CoreSignalR
author: anurse
description: Erfahren Sie, wie Sie Diagnoseinformationen aus Ihrer ASP.net Core- SignalR App erfassen.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/diagnostics
ms.openlocfilehash: 0dda4fb55b1e2275d9cdb2af0b55824b12121dee
ms.sourcegitcommit: 16b3abec1ed70f9a206f0cfa7cf6404eebaf693d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2020
ms.locfileid: "83444216"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="651c2-103">Protokollierung und Diagnose in ASP.net Core signalr</span><span class="sxs-lookup"><span data-stu-id="651c2-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="651c2-104">Von [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="651c2-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="651c2-105">Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus Ihrer ASP.net Core signalr-APP, um Probleme zu beheben.</span><span class="sxs-lookup"><span data-stu-id="651c2-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="651c2-106">Server seitige Protokollierung</span><span class="sxs-lookup"><span data-stu-id="651c2-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="651c2-107">Serverseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="651c2-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="651c2-108">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="651c2-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="651c2-109">Da signalr Teil ASP.net Core ist, verwendet es das ASP.net Core Protokollierungs System.</span><span class="sxs-lookup"><span data-stu-id="651c2-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="651c2-110">In der Standardkonfiguration protokolliert signalr nur wenige Informationen, aber dies kann konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="651c2-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="651c2-111">In der Dokumentation zur [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) finden Sie weitere Informationen zum Konfigurieren der ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="651c2-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="651c2-112">Signalr verwendet zwei Kategorien von Kategorien:</span><span class="sxs-lookup"><span data-stu-id="651c2-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="651c2-113">`Microsoft.AspNetCore.SignalR`&ndash;Protokolle im Zusammenhang mit hubprotokollen, das Aktivieren von Hubs, das Aufrufen von Methoden und andere hubbezogene Aktivitäten.</span><span class="sxs-lookup"><span data-stu-id="651c2-113">`Microsoft.AspNetCore.SignalR` &ndash; for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="651c2-114">`Microsoft.AspNetCore.Http.Connections`&ndash;Protokolle, die sich auf Transporte beziehen, wie z. b. websockets, lange Abruf Ereignisse und Server gesendete Ereignisse und eine signalr-Infrastruktur auf niedriger Ebene.</span><span class="sxs-lookup"><span data-stu-id="651c2-114">`Microsoft.AspNetCore.Http.Connections` &ndash; for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="651c2-115">Um ausführliche Protokolle von signalr zu aktivieren, konfigurieren Sie beide vorangehenden Präfixe auf der `Debug` Ebene in der Datei *appSettings. JSON* , indem Sie die folgenden Elemente zum `LogLevel` unter Abschnitt in hinzufügen `Logging` :</span><span class="sxs-lookup"><span data-stu-id="651c2-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="651c2-116">Sie können dies auch in Code in der- `CreateWebHostBuilder` Methode konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="651c2-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="651c2-117">Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie die folgenden Konfigurationswerte in Ihrem Konfigurationssystem fest:</span><span class="sxs-lookup"><span data-stu-id="651c2-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="651c2-118">Suchen Sie in der Dokumentation nach Informationen zu Ihrem Konfigurationssystem, um zu bestimmen, wie geschachtelte Konfigurationswerte angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="651c2-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="651c2-119">Bei der Verwendung von Umgebungsvariablen werden beispielsweise zwei `_`-Zeichen anstelle von `:` verwendet (z. B. `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="651c2-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="651c2-120">Es empfiehlt sich, die `Debug`-Ebene zu verwenden, wenn detailliertere Diagnosedaten Ihrer App gesammelt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="651c2-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="651c2-121">Die `Trace`-Ebene bietet nur sehr wenige Diagnoseinformationen und wird nur selten verwendet, um App-Probleme zu identifizieren.</span><span class="sxs-lookup"><span data-stu-id="651c2-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="651c2-122">Zugreifen auf serverseitige Protokolle</span><span class="sxs-lookup"><span data-stu-id="651c2-122">Access server-side logs</span></span>

<span data-ttu-id="651c2-123">Wie Sie auf serverseitige Protokolle zugreifen können, hängt von der Ausführungsumgebung ab.</span><span class="sxs-lookup"><span data-stu-id="651c2-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="651c2-124">Als Konsolen-App außerhalb von IIS</span><span class="sxs-lookup"><span data-stu-id="651c2-124">As a console app outside IIS</span></span>

<span data-ttu-id="651c2-125">Wenn die Ausführung in einer Konsolen-App stattfindet, sollte die [Konsolenprotokollierung](xref:fundamentals/logging/index#console) standardmäßig aktiviert sein.</span><span class="sxs-lookup"><span data-stu-id="651c2-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="651c2-126">Signalr-Protokolle werden in der-Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="651c2-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="651c2-127">In IIS Express aus Visual Studio</span><span class="sxs-lookup"><span data-stu-id="651c2-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="651c2-128">Visual Studio zeigt die Protokoll Ausgabe im **Ausgabe** Fenster an.</span><span class="sxs-lookup"><span data-stu-id="651c2-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="651c2-129">Wählen Sie die Dropdown Option **ASP.net Core Webserver** aus.</span><span class="sxs-lookup"><span data-stu-id="651c2-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="651c2-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="651c2-130">Azure App Service</span></span>

<span data-ttu-id="651c2-131">Aktivieren Sie die Option **Anwendungs Protokollierung (File System)** im Abschnitt **Diagnoseprotokolle** des Azure App Service Portals, und konfigurieren Sie die **Ebene** auf `Verbose` .</span><span class="sxs-lookup"><span data-stu-id="651c2-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="651c2-132">Protokolle sollten über den **protokollstreamingdienst** und in Protokollen im Dateisystem des App Service verfügbar sein.</span><span class="sxs-lookup"><span data-stu-id="651c2-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="651c2-133">Weitere Informationen finden Sie unter [Azure-Protokoll Streaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="651c2-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="651c2-134">Andere Umgebungen</span><span class="sxs-lookup"><span data-stu-id="651c2-134">Other environments</span></span>

<span data-ttu-id="651c2-135">Wenn die App in einer anderen Umgebung bereitgestellt wird (z. B. Docker, Kubernetes oder Windows-Dienst) finden Sie unter <xref:fundamentals/logging/index> weitere Informationen zum Konfigurieren von Protokollierungsanbietern, die für die Umgebung geeignet sind.</span><span class="sxs-lookup"><span data-stu-id="651c2-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="651c2-136">JavaScript-Client Protokollierung</span><span class="sxs-lookup"><span data-stu-id="651c2-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="651c2-137">Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="651c2-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="651c2-138">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="651c2-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="651c2-139">Wenn Sie den JavaScript-Client verwenden, können Sie die Protokollierungs Optionen mithilfe der `configureLogging` Methode für konfigurieren `HubConnectionBuilder` :</span><span class="sxs-lookup"><span data-stu-id="651c2-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="651c2-140">Um die Protokollierung vollständig zu deaktivieren, geben Sie `signalR.LogLevel.None` in der `configureLogging` Methode an</span><span class="sxs-lookup"><span data-stu-id="651c2-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="651c2-141">In der folgenden Tabelle werden die für den JavaScript-Client verfügbaren Protokoll Ebenen angezeigt.</span><span class="sxs-lookup"><span data-stu-id="651c2-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="651c2-142">Wenn Sie die Protokollebene auf einen dieser Werte festlegen, wird die Protokollierung auf dieser Ebene und allen darüber liegenden Ebenen in der Tabelle ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="651c2-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="651c2-143">Ebene</span><span class="sxs-lookup"><span data-stu-id="651c2-143">Level</span></span> | <span data-ttu-id="651c2-144">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="651c2-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="651c2-145">Es werden keine Nachrichten protokolliert.</span><span class="sxs-lookup"><span data-stu-id="651c2-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="651c2-146">Meldungen, die auf einen Fehler in der gesamten App hindeuten.</span><span class="sxs-lookup"><span data-stu-id="651c2-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="651c2-147">Meldungen, die auf einen Fehler im aktuellen Vorgang hindeuten.</span><span class="sxs-lookup"><span data-stu-id="651c2-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="651c2-148">Meldungen, die auf ein nicht schwer wiedliches Problem hinweisen.</span><span class="sxs-lookup"><span data-stu-id="651c2-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="651c2-149">Informationsmeldungen.</span><span class="sxs-lookup"><span data-stu-id="651c2-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="651c2-150">Diagnosemeldungen sind für das Debuggen nützlich.</span><span class="sxs-lookup"><span data-stu-id="651c2-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="651c2-151">Sehr ausführliche Diagnosemeldungen, die für die Diagnose bestimmter Probleme entwickelt wurden.</span><span class="sxs-lookup"><span data-stu-id="651c2-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="651c2-152">Nachdem Sie die Ausführlichkeit konfiguriert haben, werden die Protokolle in die Browser Konsole (oder die Standard Ausgabe in einer nodejs-APP) geschrieben.</span><span class="sxs-lookup"><span data-stu-id="651c2-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="651c2-153">Wenn Sie Protokolle an ein benutzerdefiniertes Protokollierungs System senden möchten, können Sie ein JavaScript-Objekt bereitstellen, das die- `ILogger` Schnittstelle implementiert.</span><span class="sxs-lookup"><span data-stu-id="651c2-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="651c2-154">Die einzige Methode, die implementiert werden muss `log` , ist, die die Ebene des Ereignisses und die dem Ereignis zugeordnete Meldung annimmt.</span><span class="sxs-lookup"><span data-stu-id="651c2-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="651c2-155">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="651c2-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="651c2-156">.NET-Client Protokollierung</span><span class="sxs-lookup"><span data-stu-id="651c2-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="651c2-157">Clientseitige Protokolle enthalten möglicherweise vertrauliche Daten aus Ihrer App.</span><span class="sxs-lookup"><span data-stu-id="651c2-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="651c2-158">Veröffentlichten Sie deshalb **niemals** Protokolle von Produktions-Apps auf öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="651c2-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="651c2-159">Um Protokolle vom .NET-Client zu erhalten, können Sie die- `ConfigureLogging` Methode für verwenden `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="651c2-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="651c2-160">Dies funktioniert genauso wie die `ConfigureLogging` -Methode in `WebHostBuilder` und `HostBuilder` .</span><span class="sxs-lookup"><span data-stu-id="651c2-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="651c2-161">Sie können die gleichen Protokollierungs Anbieter konfigurieren, die Sie in ASP.net Core verwenden.</span><span class="sxs-lookup"><span data-stu-id="651c2-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="651c2-162">Allerdings müssen Sie die nuget-Pakete für die einzelnen Protokollierungs Anbieter manuell installieren und aktivieren.</span><span class="sxs-lookup"><span data-stu-id="651c2-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="651c2-163">Konsolenprotokollierung</span><span class="sxs-lookup"><span data-stu-id="651c2-163">Console logging</span></span>

<span data-ttu-id="651c2-164">Fügen Sie das Paket [Microsoft. Extensions. Logging. Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) hinzu, um die Konsolen Protokollierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="651c2-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="651c2-165">Verwenden Sie dann die- `AddConsole` Methode zum Konfigurieren der Konsolen Protokollierung:</span><span class="sxs-lookup"><span data-stu-id="651c2-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="651c2-166">Debuggen des Ausgabe Fensters</span><span class="sxs-lookup"><span data-stu-id="651c2-166">Debug output window logging</span></span>

<span data-ttu-id="651c2-167">Sie können Protokolle auch so konfigurieren, dass Sie zum Fenster **Ausgabe** in Visual Studio wechseln.</span><span class="sxs-lookup"><span data-stu-id="651c2-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="651c2-168">Installieren Sie das Paket " [Microsoft. Extensions. Logging. Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) ", und verwenden Sie die- `AddDebug` Methode:</span><span class="sxs-lookup"><span data-stu-id="651c2-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="651c2-169">Andere Protokollierungs Anbieter</span><span class="sxs-lookup"><span data-stu-id="651c2-169">Other logging providers</span></span>

SignalR<span data-ttu-id="651c2-170">unterstützt andere Protokollierungs Anbieter wie serilog, seq, nlog oder ein beliebiges anderes Protokollierungs System, das in integriert ist `Microsoft.Extensions.Logging` .</span><span class="sxs-lookup"><span data-stu-id="651c2-170"> supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="651c2-171">Wenn Ihr Protokollierungs System ein bereitstellt `ILoggerProvider` , können Sie es mit folgenden Aktionen registrieren `AddProvider` :</span><span class="sxs-lookup"><span data-stu-id="651c2-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="651c2-172">Ausführlichkeit von Steuerelementen</span><span class="sxs-lookup"><span data-stu-id="651c2-172">Control verbosity</span></span>

<span data-ttu-id="651c2-173">Wenn Sie sich an anderen Stellen in ihrer App anmelden, ist das Ändern der Standard Ebene in `Debug` möglicherweise zu ausführlich.</span><span class="sxs-lookup"><span data-stu-id="651c2-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="651c2-174">Sie können einen Filter verwenden, um den Protokolliergrad für Protokolle zu konfigurieren SignalR .</span><span class="sxs-lookup"><span data-stu-id="651c2-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="651c2-175">Dies kann im Code auf die gleiche Weise wie auf dem Server erfolgen:</span><span class="sxs-lookup"><span data-stu-id="651c2-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="651c2-176">Netzwerkablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="651c2-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="651c2-177">Eine Netzwerk Ablauf Verfolgung enthält den gesamten Inhalt jeder Nachricht, die von Ihrer APP gesendet wird.</span><span class="sxs-lookup"><span data-stu-id="651c2-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="651c2-178">Veröffentlichen Sie **niemals** unformatierte Netzwerk Ablauf Verfolgungen aus Produktions-apps in öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="651c2-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="651c2-179">Wenn ein Problem auftritt, kann es vorkommen, dass eine Netzwerk Ablauf Verfolgung viele hilfreiche Informationen liefert.</span><span class="sxs-lookup"><span data-stu-id="651c2-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="651c2-180">Dies ist besonders nützlich, wenn Sie ein Problem in unserer Problemverfolgung melden.</span><span class="sxs-lookup"><span data-stu-id="651c2-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="651c2-181">Erfassen einer Netzwerk Ablauf Verfolgung mit "fddler" (bevorzugte Option)</span><span class="sxs-lookup"><span data-stu-id="651c2-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="651c2-182">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="651c2-182">This method works for all apps.</span></span>

<span data-ttu-id="651c2-183">"Fddler" ist ein sehr leistungsfähiges Tool zum Erfassen von http-Ablauf Verfolgungen.</span><span class="sxs-lookup"><span data-stu-id="651c2-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="651c2-184">Installieren Sie Sie über [Telerik.com/Fiddler](https://www.telerik.com/fiddler), starten Sie Sie, und führen Sie dann die APP aus.</span><span class="sxs-lookup"><span data-stu-id="651c2-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="651c2-185">"Fddler" ist für Windows verfügbar, und es gibt Beta Versionen für macOS und Linux.</span><span class="sxs-lookup"><span data-stu-id="651c2-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="651c2-186">Wenn Sie eine Verbindung über HTTPS herstellen, sind einige zusätzliche Schritte erforderlich, um sicherzustellen, dass der HTTPS-Datenverkehr von fddler entschlüsselt werden kann</span><span class="sxs-lookup"><span data-stu-id="651c2-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="651c2-187">Weitere Informationen finden Sie in der [Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)zu "Dokumentation".</span><span class="sxs-lookup"><span data-stu-id="651c2-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="651c2-188">Nachdem Sie die Ablauf Verfolgung gesammelt haben, können Sie die Ablauf Verfolgung exportieren **File**, indem Sie auf  >  **Save**  >  der Menüleiste auf Datei**alle Sitzungen** speichern klicken.</span><span class="sxs-lookup"><span data-stu-id="651c2-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![Exportieren aller Sitzungen aus "fddler"](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="651c2-190">Erfassen einer Netzwerk Ablauf Verfolgung mit tcpdump (nur macOS und Linux)</span><span class="sxs-lookup"><span data-stu-id="651c2-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="651c2-191">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="651c2-191">This method works for all apps.</span></span>

<span data-ttu-id="651c2-192">Sie können unformatierte TCP-Ablauf Verfolgungen mithilfe von tcpdump erfassen, indem Sie den folgenden Befehl in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="651c2-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="651c2-193">`root` `sudo` Wenn Sie einen Berechtigungs Fehler erhalten, müssen Sie möglicherweise den Befehl mit einem Präfix versehen.</span><span class="sxs-lookup"><span data-stu-id="651c2-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="651c2-194">Ersetzen `[interface]` Sie durch die Netzwerkschnittstelle, die Sie erfassen möchten.</span><span class="sxs-lookup"><span data-stu-id="651c2-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="651c2-195">In der Regel handelt es sich hierbei um `/dev/eth0` (für die standardethernet-Schnittstelle) oder `/dev/lo0` (für localhost-Datenverkehr).</span><span class="sxs-lookup"><span data-stu-id="651c2-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="651c2-196">Weitere Informationen finden `tcpdump` Sie auf der Seite "man" auf Ihrem Host System.</span><span class="sxs-lookup"><span data-stu-id="651c2-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="651c2-197">Erfassen einer Netzwerk Ablauf Verfolgung im Browser</span><span class="sxs-lookup"><span data-stu-id="651c2-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="651c2-198">Diese Methode funktioniert nur bei browserbasierten apps.</span><span class="sxs-lookup"><span data-stu-id="651c2-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="651c2-199">Die meisten Browser Entwicklertools über eine Registerkarte "Netzwerk" verfügen, mit der Sie die Netzwerkaktivität zwischen dem Browser und dem Server erfassen können.</span><span class="sxs-lookup"><span data-stu-id="651c2-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="651c2-200">Diese Ablauf Verfolgungen enthalten jedoch keine WebSocket-und vom Server gesendeten Ereignis Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="651c2-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="651c2-201">Wenn Sie diese Transporte verwenden, ist die Verwendung eines Tools wie z. b. "fddler" oder "tcpdump" (unten beschrieben) ein besserer Ansatz.</span><span class="sxs-lookup"><span data-stu-id="651c2-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="651c2-202">Microsoft Edge und Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="651c2-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="651c2-203">(Die Anweisungen sind für Edge und Internet Explorer identisch.)</span><span class="sxs-lookup"><span data-stu-id="651c2-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="651c2-204">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="651c2-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="651c2-205">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="651c2-205">Click the Network Tab</span></span>
3. <span data-ttu-id="651c2-206">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="651c2-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="651c2-207">Klicken Sie in der Symbolleiste auf das Symbol speichern, um die Ablauf Verfolgung als "har"-Datei zu exportieren:</span><span class="sxs-lookup"><span data-stu-id="651c2-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Symbol "Speichern" auf der Registerkarte "Netzwerk" von Microsoft Edge dev tools](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="651c2-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="651c2-209">Google Chrome</span></span>

1. <span data-ttu-id="651c2-210">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="651c2-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="651c2-211">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="651c2-211">Click the Network Tab</span></span>
3. <span data-ttu-id="651c2-212">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="651c2-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="651c2-213">Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Anforderungsliste, und wählen Sie "Speichern unter har mit Inhalt"</span><span class="sxs-lookup"><span data-stu-id="651c2-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Option "Speichern unter har mit Inhalt" auf der Registerkarte "Google Chrome dev Tools Network"](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="651c2-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="651c2-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="651c2-216">Drücken Sie F12, um die Entwickler Tools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="651c2-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="651c2-217">Klicken Sie auf die Registerkarte Netzwerk</span><span class="sxs-lookup"><span data-stu-id="651c2-217">Click the Network Tab</span></span>
3. <span data-ttu-id="651c2-218">Aktualisieren Sie die Seite (falls erforderlich), und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="651c2-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="651c2-219">Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle in der Liste der Anforderungen, und wählen Sie "alles als har</span><span class="sxs-lookup"><span data-stu-id="651c2-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Option "alle als har speichern" auf der Registerkarte "Mozilla Firefox dev Tools Network"](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="651c2-221">Anfügen von Diagnose Dateien an GitHub-Probleme</span><span class="sxs-lookup"><span data-stu-id="651c2-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="651c2-222">Sie können Diagnose Dateien an GitHub-Probleme anfügen, indem Sie Sie so umbenennen, dass Sie über eine Erweiterung verfügen, und Sie `.txt` dann auf das Problem ziehen und ablegen.</span><span class="sxs-lookup"><span data-stu-id="651c2-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="651c2-223">Fügen Sie den Inhalt von Protokolldateien oder Netzwerk Ablauf Verfolgungen nicht in ein GitHub-Problem ein.</span><span class="sxs-lookup"><span data-stu-id="651c2-223">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="651c2-224">Diese Protokolle und Ablauf Verfolgungen können sehr umfangreich sein, und GitHub verkürzt Sie in der Regel.</span><span class="sxs-lookup"><span data-stu-id="651c2-224">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![Ziehen von Protokolldateien auf ein GitHub-Problem](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="651c2-226">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="651c2-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
