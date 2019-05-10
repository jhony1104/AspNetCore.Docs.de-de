---
title: Protokollierung und-Diagnose in ASP.NET Core SignalR
author: anurse
description: Informationen Sie zum Sammeln von Diagnosedaten aus Ihrer ASP.NET Core SignalR-app.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896887"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="20bd0-103">Protokollierung und-Diagnose in ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="20bd0-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="20bd0-104">Durch [Andrew Stanton-Nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="20bd0-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="20bd0-105">Dieser Artikel enthält Anleitungen für das Sammeln von Diagnosen aus Ihrer ASP.NET Core SignalR-app zum Behandeln von Problemen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="20bd0-106">Serverseitige Protokollierung</span><span class="sxs-lookup"><span data-stu-id="20bd0-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="20bd0-107">Serverseitiger Protokolle enthalten möglicherweise vertrauliche Informationen aus Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="20bd0-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="20bd0-108">**Nie** aufbereitete Protokolle aus Produktions-apps in öffentlichen Foren wie GitHub Posten.</span><span class="sxs-lookup"><span data-stu-id="20bd0-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20bd0-109">Da SignalR Teil von ASP.NET Core ist, wird die ASP.NET Core-Protokollierung System verwendet.</span><span class="sxs-lookup"><span data-stu-id="20bd0-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="20bd0-110">Klicken Sie in der Standardkonfiguration SignalR nur sehr wenig Informationen protokolliert, jedoch kann dies konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="20bd0-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="20bd0-111">Finden Sie in der Dokumentation auf [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index#configuration) Weitere Informationen zum Konfigurieren von ASP.NET Core-Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="20bd0-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="20bd0-112">SignalR verwendet zwei Protokollierungskategorien:</span><span class="sxs-lookup"><span data-stu-id="20bd0-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="20bd0-113">`Microsoft.AspNetCore.SignalR` -Aktivieren Sie für die Protokolle im Zusammenhang mit der Hub-Protokolle, Hubs, das Aufrufen von Methoden und anderen Aktivitäten im Zusammenhang mit dem Hub.</span><span class="sxs-lookup"><span data-stu-id="20bd0-113">`Microsoft.AspNetCore.SignalR` - for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="20bd0-114">`Microsoft.AspNetCore.Http.Connections` – für Protokolle im Zusammenhang mit Transporten, z. B. WebSockets, Long Polling und Server-Sent-Ereignisse und Low-Level-SignalR-Infrastruktur.</span><span class="sxs-lookup"><span data-stu-id="20bd0-114">`Microsoft.AspNetCore.Http.Connections` - for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="20bd0-115">Detaillierte Protokolle von SignalR konfigurieren, um sowohl die vorherige Präfixe in der `Debug` Ebene in Ihrer `appsettings.json` -Datei, indem Sie die folgenden Elemente zum Hinzufügen der `LogLevel` im Unterabschnitt `Logging`:</span><span class="sxs-lookup"><span data-stu-id="20bd0-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your `appsettings.json` file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="20bd0-116">Sie können dies auch konfigurieren, im Code in Ihre `CreateWebHostBuilder` Methode:</span><span class="sxs-lookup"><span data-stu-id="20bd0-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="20bd0-117">Wenn Sie JSON-basierte Konfiguration nicht verwenden, legen Sie die folgenden Konfigurationswerte in Ihrem Konfigurationssystem:</span><span class="sxs-lookup"><span data-stu-id="20bd0-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="20bd0-118">Überprüfen Sie die Dokumentation für Ihr Konfigurationssystem, um zu bestimmen, wie Sie geschachtelte Konfigurationswerte angeben.</span><span class="sxs-lookup"><span data-stu-id="20bd0-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="20bd0-119">Beispielsweise bei Verwendung von Umgebungsvariablen, zwei `_` Zeichen werden verwendet, statt die `:` (z. B.: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span><span class="sxs-lookup"><span data-stu-id="20bd0-119">For example, when using environment variables, two `_` characters are used instead of the `:` (such as: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="20bd0-120">Es wird empfohlen, die `Debug` Ebene beim Erfassen, ausführlichere Diagnose für Ihre app.</span><span class="sxs-lookup"><span data-stu-id="20bd0-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="20bd0-121">Die `Trace` erzeugt sehr Low-Level-Diagnose und nur selten erforderlich, um die diagnose von Problemen in Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="20bd0-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="20bd0-122">Serverseitige Zugriffsprotokolle</span><span class="sxs-lookup"><span data-stu-id="20bd0-122">Access server-side logs</span></span>

<span data-ttu-id="20bd0-123">Wie Sie serverseitige Protokolle zugreifen, hängt von der Umgebung, in der Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="20bd0-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="20bd0-124">Als Konsolen-app außerhalb von IIS</span><span class="sxs-lookup"><span data-stu-id="20bd0-124">As a console app outside IIS</span></span>

<span data-ttu-id="20bd0-125">Wenn Sie in einer Konsolen-app ausführen, die [konsolenprotokollierung](xref:fundamentals/logging/index#console-provider) sollte standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="20bd0-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="20bd0-126">SignalR-Protokolle werden in der Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="20bd0-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="20bd0-127">Innerhalb von IIS Express aus Visual Studio</span><span class="sxs-lookup"><span data-stu-id="20bd0-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="20bd0-128">Visual Studio zeigt die Ausgabe des Protokolls in der **Ausgabe** Fenster.</span><span class="sxs-lookup"><span data-stu-id="20bd0-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="20bd0-129">Wählen Sie die **ASP.NET Core-Web-Server** Dropdown-Option.</span><span class="sxs-lookup"><span data-stu-id="20bd0-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="20bd0-130">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="20bd0-130">Azure App Service</span></span>

<span data-ttu-id="20bd0-131">Aktivieren Sie die Option "Anwendungsprotokollierung (Dateisystem)" im Abschnitt "Diagnoseprotokolle" im Azure App Service-Portal und konfigurieren Sie die anzuzeigende Stufe `Verbose`.</span><span class="sxs-lookup"><span data-stu-id="20bd0-131">Enable the "Application Logging (Filesystem)" option in the "Diagnostics logs" section of the Azure App Service portal and configure the Level to `Verbose`.</span></span> <span data-ttu-id="20bd0-132">Protokolle sollten von der "Log"-streamingdienst, auch wie Protokolle im Dateisystem Ihres App Service verfügbar sein.</span><span class="sxs-lookup"><span data-stu-id="20bd0-132">Logs should be available from the "Log streaming" service, as well as in logs on the file system of your App Service.</span></span> <span data-ttu-id="20bd0-133">Weitere Informationen finden Sie in der Dokumentation auf [Azure-protokollstreaming](xref:fundamentals/logging/index#azure-log-streaming).</span><span class="sxs-lookup"><span data-stu-id="20bd0-133">For more information, see the documentation on [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="20bd0-134">Andere Umgebungen</span><span class="sxs-lookup"><span data-stu-id="20bd0-134">Other environments</span></span>

<span data-ttu-id="20bd0-135">Wenn Sie in einer anderen ausführen Umgebung (Docker, Kubernetes, Windows-Dienst usw.), finden Sie die vollständige Dokumentation für [ASP.NET Core-Protokollierung](xref:fundamentals/logging/index) für Weitere Informationen zum Konfigurieren der Anbieter für die Protokollierung für Ihre Umgebung geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="20bd0-135">If you're running in another environment (Docker, Kubernetes, Windows Service, etc.), see the full documentation on [ASP.NET Core Logging](xref:fundamentals/logging/index) for more information on how to configure logging providers suitable to your environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="20bd0-136">JavaScript-Clients, die Protokollierung</span><span class="sxs-lookup"><span data-stu-id="20bd0-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="20bd0-137">Die clientseitige Protokolle enthalten möglicherweise vertrauliche Informationen aus Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="20bd0-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="20bd0-138">**Nie** aufbereitete Protokolle aus Produktions-apps in öffentlichen Foren wie GitHub Posten.</span><span class="sxs-lookup"><span data-stu-id="20bd0-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20bd0-139">Wenn Sie den JavaScript-Client verwenden, können Sie Konfigurieren der Protokollierungsoptionen mithilfe der `configureLogging` Methode `HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="20bd0-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="20bd0-140">Geben Sie zum Deaktivieren der Protokollierung vollständig, `signalR.LogLevel.None` in die `configureLogging` Methode.</span><span class="sxs-lookup"><span data-stu-id="20bd0-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="20bd0-141">Die folgende Tabelle zeigt die verfügbaren Protokollebenen an den JavaScript-Client.</span><span class="sxs-lookup"><span data-stu-id="20bd0-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="20bd0-142">Die Protokollebene auf einen der folgenden Werte festlegen kann die Protokollierung auf dieser Ebene und alle Ebenen darüber, in der Tabelle.</span><span class="sxs-lookup"><span data-stu-id="20bd0-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="20bd0-143">Ebene</span><span class="sxs-lookup"><span data-stu-id="20bd0-143">Level</span></span> | <span data-ttu-id="20bd0-144">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="20bd0-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="20bd0-145">Es werden keine Meldungen protokolliert.</span><span class="sxs-lookup"><span data-stu-id="20bd0-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="20bd0-146">Nachrichten, die einen Fehler in der gesamten app angeben.</span><span class="sxs-lookup"><span data-stu-id="20bd0-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="20bd0-147">Nachrichten, die einen Fehler in den aktuellen Vorgang angeben.</span><span class="sxs-lookup"><span data-stu-id="20bd0-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="20bd0-148">Nachrichten, die ein nicht schwerwiegender Fehler aufgetreten.</span><span class="sxs-lookup"><span data-stu-id="20bd0-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="20bd0-149">Informationsmeldungen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="20bd0-150">Diagnosemeldungen für das Debuggen hilfreich.</span><span class="sxs-lookup"><span data-stu-id="20bd0-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="20bd0-151">Sehr detaillierte diagnosemeldungen zum Diagnostizieren von Problemen mit bestimmten entwickelt.</span><span class="sxs-lookup"><span data-stu-id="20bd0-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="20bd0-152">Nachdem Sie die Ausführlichkeit konfiguriert haben, werden die Protokolle in der Browserkonsole (oder die Standardausgabe in eine NodeJS-app) geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="20bd0-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="20bd0-153">Bei der Sie Protokolle an ein System für die benutzerdefinierte Protokollierung senden möchten, können Sie angeben, ein JavaScript-Objekt implementieren die `ILogger` Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="20bd0-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="20bd0-154">Ist die einzige Methode, die implementiert werden muss `log`, der die Ebene des Ereignisses akzeptiert und die Nachricht, die dem Ereignis zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="20bd0-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="20bd0-155">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="20bd0-155">For example:</span></span>

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="20bd0-156">.NET Client-Protokollierung</span><span class="sxs-lookup"><span data-stu-id="20bd0-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="20bd0-157">Die clientseitige Protokolle enthalten möglicherweise vertrauliche Informationen aus Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="20bd0-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="20bd0-158">**Nie** aufbereitete Protokolle aus Produktions-apps in öffentlichen Foren wie GitHub Posten.</span><span class="sxs-lookup"><span data-stu-id="20bd0-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20bd0-159">Um Protokolle aus dem .NET-Client zu erhalten, können Sie die `ConfigureLogging` Methode `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="20bd0-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="20bd0-160">Dies funktioniert genauso wie die `ConfigureLogging` Methode `WebHostBuilder` und `HostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="20bd0-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="20bd0-161">Sie können dieselben Protokollierungsanbieter, die Sie verwenden, konfigurieren, in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="20bd0-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="20bd0-162">Allerdings müssen Sie manuell installieren und aktivieren die NuGet-Pakete für die einzelnen Protokollierungsanbieter.</span><span class="sxs-lookup"><span data-stu-id="20bd0-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="20bd0-163">Konsolenprotokollierung</span><span class="sxs-lookup"><span data-stu-id="20bd0-163">Console logging</span></span>

<span data-ttu-id="20bd0-164">Um die konsolenprotokollierung zu aktivieren, fügen Sie der [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) Paket.</span><span class="sxs-lookup"><span data-stu-id="20bd0-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="20bd0-165">Verwenden Sie dann die `AddConsole` Methode zum Konfigurieren der konsolenprotokollierung:</span><span class="sxs-lookup"><span data-stu-id="20bd0-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="20bd0-166">Debugprotokollierung bei der Ausgabe-Fenster</span><span class="sxs-lookup"><span data-stu-id="20bd0-166">Debug output window logging</span></span>

<span data-ttu-id="20bd0-167">Sie können auch Protokolle wechseln Sie zum Konfigurieren der **Ausgabe** Fenster in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="20bd0-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="20bd0-168">Installieren Sie die [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) packen sowie die Verwendung der `AddDebug` Methode:</span><span class="sxs-lookup"><span data-stu-id="20bd0-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="20bd0-169">Anderer Protokollierungsanbieter</span><span class="sxs-lookup"><span data-stu-id="20bd0-169">Other logging providers</span></span>

<span data-ttu-id="20bd0-170">SignalR unterstützt anderer Protokollierungsanbieter wie z. B. Serilog, Seq, NLog oder ein anderes Protokollierungssystem, die integriert `Microsoft.Extensions.Logging`.</span><span class="sxs-lookup"><span data-stu-id="20bd0-170">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="20bd0-171">Wenn Ihr Protokollierungssystem bietet eine `ILoggerProvider`, registrieren Sie ihn mit `AddProvider`:</span><span class="sxs-lookup"><span data-stu-id="20bd0-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="20bd0-172">Steuerelement-Ausführlichkeitsgrads</span><span class="sxs-lookup"><span data-stu-id="20bd0-172">Control verbosity</span></span>

<span data-ttu-id="20bd0-173">Wenn Sie an anderen Stellen in Ihrer app anmelden, ändern Sie die Standardebene, `Debug` möglicherweise zu ausführlich.</span><span class="sxs-lookup"><span data-stu-id="20bd0-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="20bd0-174">Sie können einen Filter verwenden, so konfigurieren Sie den Protokolliergrad für SignalR-Protokolle.</span><span class="sxs-lookup"><span data-stu-id="20bd0-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="20bd0-175">Dies kann im Code im Wesentlichen die gleiche Weise wie auf dem Server durchgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="20bd0-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="20bd0-176">Netzwerkablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="20bd0-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="20bd0-177">Eine netzwerkablaufverfolgung enthält den vollständigen Inhalt jeder Nachricht, die von Ihrer app gesendet.</span><span class="sxs-lookup"><span data-stu-id="20bd0-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="20bd0-178">**Nie** Netzwerk-ablaufverfolgungen von Produktions-apps in öffentlichen Foren wie GitHub Posten.</span><span class="sxs-lookup"><span data-stu-id="20bd0-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="20bd0-179">Wenn Sie ein Problem auftritt, bieten eine netzwerkablaufverfolgung manchmal sehr hilfreiche Informationen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="20bd0-180">Dies ist besonders nützlich, wenn Sie zum Melden eines Problems in unserer problemverfolgung also.</span><span class="sxs-lookup"><span data-stu-id="20bd0-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="20bd0-181">Erfassen einer netzwerkablaufverfolgung mit Fiddler (bevorzugte Option)</span><span class="sxs-lookup"><span data-stu-id="20bd0-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="20bd0-182">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="20bd0-182">This method works for all apps.</span></span>

<span data-ttu-id="20bd0-183">Fiddler ist ein äußerst leistungsfähiges Tool zum Sammeln von HTTP-ablaufverfolgungen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="20bd0-184">Installieren Sie sie über [telerik.com/fiddler](https://www.telerik.com/fiddler), starten Sie ihn, und klicken Sie dann die Anwendung ausführen und reproduzieren Sie das Problem.</span><span class="sxs-lookup"><span data-stu-id="20bd0-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="20bd0-185">Fiddler ist verfügbar für Windows, und es gibt Betaversionen für MacOS und Linux.</span><span class="sxs-lookup"><span data-stu-id="20bd0-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="20bd0-186">Wenn Sie eine Verbindung über HTTPS herstellen, sind einige zusätzlichen Schritte ausführen, um sicherzustellen, dass den HTTPS-Datenverkehr von Fiddler entschlüsselt werden kann.</span><span class="sxs-lookup"><span data-stu-id="20bd0-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="20bd0-187">Weitere Informationen finden Sie unter den [Fiddler-Dokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span><span class="sxs-lookup"><span data-stu-id="20bd0-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="20bd0-188">Nachdem Sie die Ablaufverfolgung gesammelt haben, können Sie die Ablaufverfolgung exportieren, indem auswählen **Datei** > **speichern** > **alle Sitzungen...**  in der Menüleiste.</span><span class="sxs-lookup"><span data-stu-id="20bd0-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions...** from the menu bar.</span></span>

![Exportieren alle Sitzungen von Fiddler](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="20bd0-190">Erfassen einer netzwerkablaufverfolgung mit Tcpdump (Mac OS und nur Linux)</span><span class="sxs-lookup"><span data-stu-id="20bd0-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="20bd0-191">Diese Methode funktioniert für alle apps.</span><span class="sxs-lookup"><span data-stu-id="20bd0-191">This method works for all apps.</span></span>

<span data-ttu-id="20bd0-192">Sie können die unformatierte TCP-ablaufverfolgungen, die mit Tcpdump mithilfe des folgenden Befehls über eine Befehlsshell sammeln.</span><span class="sxs-lookup"><span data-stu-id="20bd0-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="20bd0-193">Möglicherweise werden `root` oder den Befehl mit Präfix `sudo` Wenn Sie einen Berechtigungsfehler erhalten:</span><span class="sxs-lookup"><span data-stu-id="20bd0-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="20bd0-194">Ersetzen Sie dies `[interface]` der Netzwerkschnittstelle, die Sie auf erfassen möchten.</span><span class="sxs-lookup"><span data-stu-id="20bd0-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="20bd0-195">In der Regel ist dies so etwas wie `/dev/eth0` (für Ihre standard-Ethernet-Schnittstelle) oder `/dev/lo0` (für Datenverkehr von "localhost").</span><span class="sxs-lookup"><span data-stu-id="20bd0-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="20bd0-196">Weitere Informationen finden Sie unter den `tcpdump` Manpage auf Ihrem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="20bd0-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="20bd0-197">Erfassen einer netzwerkablaufverfolgung im browser</span><span class="sxs-lookup"><span data-stu-id="20bd0-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="20bd0-198">Diese Methode funktioniert nur für browserbasierte apps.</span><span class="sxs-lookup"><span data-stu-id="20bd0-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="20bd0-199">Die meisten Browserentwicklertools haben eine "Netzwerk"-Registerkarte, die Ihnen ermöglicht, die Netzwerkaktivität zwischen dem Browser und dem Server zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="20bd0-200">Diese ablaufverfolgungen enthalten jedoch keine WebSocket und Server-Sent ereignismeldungen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="20bd0-201">Wenn Sie diese Transporte verwenden, ist die mit einem Tool wie Fiddler oder mit TcpDump (siehe unten) ein besserer Ansatz.</span><span class="sxs-lookup"><span data-stu-id="20bd0-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="20bd0-202">Microsoft Edge und InternetExplorer</span><span class="sxs-lookup"><span data-stu-id="20bd0-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="20bd0-203">(Die Anweisungen sind identisch für sowohl Microsoft Edge und Internet Explorer)</span><span class="sxs-lookup"><span data-stu-id="20bd0-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="20bd0-204">Drücken Sie F12, um den Entwicklertools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="20bd0-205">Klicken Sie auf der Registerkarte "Netzwerk"</span><span class="sxs-lookup"><span data-stu-id="20bd0-205">Click the Network Tab</span></span>
3. <span data-ttu-id="20bd0-206">Aktualisieren Sie die Seite (sofern erforderlich) und reproduzieren das problem</span><span class="sxs-lookup"><span data-stu-id="20bd0-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="20bd0-207">Klicken Sie auf das Symbol "Speichern" in der Symbolleiste, um die Ablaufverfolgung als "HAR"-Datei zu exportieren:</span><span class="sxs-lookup"><span data-stu-id="20bd0-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Der Speichervorgang Symbol auf der Microsoft Edge-Entwickler-Tools Registerkarte "Netzwerk"](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="20bd0-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="20bd0-209">Google Chrome</span></span>

1. <span data-ttu-id="20bd0-210">Drücken Sie F12, um den Entwicklertools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="20bd0-211">Klicken Sie auf der Registerkarte "Netzwerk"</span><span class="sxs-lookup"><span data-stu-id="20bd0-211">Click the Network Tab</span></span>
3. <span data-ttu-id="20bd0-212">Aktualisieren Sie die Seite (sofern erforderlich) und reproduzieren das problem</span><span class="sxs-lookup"><span data-stu-id="20bd0-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="20bd0-213">Rechtsklick an beliebiger Stelle in der Liste der Anforderungen, und wählen Sie "Als HAR mit Inhalte speichern":</span><span class="sxs-lookup"><span data-stu-id="20bd0-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Option "Als HAR mit Inhalte speichern", in Google Chrome Dev Tools Registerkarte "Netzwerk"](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="20bd0-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="20bd0-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="20bd0-216">Drücken Sie F12, um den Entwicklertools zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="20bd0-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="20bd0-217">Klicken Sie auf der Registerkarte "Netzwerk"</span><span class="sxs-lookup"><span data-stu-id="20bd0-217">Click the Network Tab</span></span>
3. <span data-ttu-id="20bd0-218">Aktualisieren Sie die Seite (sofern erforderlich) und reproduzieren das problem</span><span class="sxs-lookup"><span data-stu-id="20bd0-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="20bd0-219">Rechtsklick an beliebiger Stelle in der Liste der Anforderungen, und wählen Sie "Speichern Sie alle als HAR"</span><span class="sxs-lookup"><span data-stu-id="20bd0-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

!["Speichern Sie alle als HAR"-Option in Mozilla Firefox Dev Tools Registerkarte "Netzwerk"](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="20bd0-221">Fügen Sie die Diagnosedateien zu GitHub-Problemen</span><span class="sxs-lookup"><span data-stu-id="20bd0-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="20bd0-222">Sie können die Diagnose-Dateien zu GitHub-Problemen anfügen, indem Sie Sie umbenennen, müssen sie sich eine `.txt` -Erweiterung, und klicken Sie dann ziehen und Ablegen an das Problem.</span><span class="sxs-lookup"><span data-stu-id="20bd0-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="20bd0-223">Fügen Sie keine den Inhalt der Protokolldateien oder netzwerkablaufverfolgungen im GitHub-Problem.</span><span class="sxs-lookup"><span data-stu-id="20bd0-223">Please don't paste the content of log files or network traces in GitHub issue.</span></span> <span data-ttu-id="20bd0-224">Diese Protokolle und ablaufverfolgungen können sehr groß sein und GitHub wird in der Regel werden abgeschnitten.</span><span class="sxs-lookup"><span data-stu-id="20bd0-224">These logs and traces can be quite large and GitHub will usually truncate them.</span></span>

![Ziehen die Protokolldateien auf ein GitHub-Problem](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="20bd0-226">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="20bd0-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
