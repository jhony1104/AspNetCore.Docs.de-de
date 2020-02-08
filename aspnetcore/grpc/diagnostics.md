---
title: Protokollierung und Diagnose in GrpC unter .net
author: jamesnk
description: Erfahren Sie, wie Sie die Diagnose von ihrer GrpC-app in .net sammeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 17607b734e6d777de9516aa14e81c97f87b61023
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074522"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="e64b0-103">Protokollierung und Diagnose in GrpC unter .net</span><span class="sxs-lookup"><span data-stu-id="e64b0-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="e64b0-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="e64b0-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="e64b0-105">Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus einer GrpC-APP, um Probleme bei der Problembehandlung zu beheben.</span><span class="sxs-lookup"><span data-stu-id="e64b0-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="e64b0-106">Folgende Themen werden behandelt:</span><span class="sxs-lookup"><span data-stu-id="e64b0-106">Topics covered include:</span></span>

* <span data-ttu-id="e64b0-107">**Protokollierung** : strukturierte Protokolle, die in [.net Core-Protokollierung](xref:fundamentals/logging/index)geschrieben werden</span><span class="sxs-lookup"><span data-stu-id="e64b0-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e64b0-108"><xref:Microsoft.Extensions.Logging.ILogger> wird von App-Frameworks verwendet, um Protokolle zu schreiben, und von Benutzern für die eigene Protokollierung in einer App.</span><span class="sxs-lookup"><span data-stu-id="e64b0-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="e64b0-109">Ablauf **Verfolgung** : Ereignisse im Zusammenhang mit einem Vorgang, der mit `DiaganosticSource` und `Activity`geschrieben wurde.</span><span class="sxs-lookup"><span data-stu-id="e64b0-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="e64b0-110">Ablauf Verfolgungen aus der Diagnose Quelle werden häufig verwendet, um App-Telemetriedaten nach Bibliotheken wie [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) und [opentelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)zu erfassen.</span><span class="sxs-lookup"><span data-stu-id="e64b0-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="e64b0-111">**Metriken** : Darstellung von Daten Messungen über Zeiträume, z. b. Anforderungen pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="e64b0-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="e64b0-112">Metriken werden mithilfe von `EventCounter` ausgegeben und können mithilfe des Befehlszeilen Tools " [dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) " oder mit [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)beobachtet werden.</span><span class="sxs-lookup"><span data-stu-id="e64b0-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="e64b0-113">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e64b0-113">Logging</span></span>

<span data-ttu-id="e64b0-114">GrpC-Dienste und der GrpC-Client schreiben Protokolle mithilfe der [.net Core-Protokollierung](xref:fundamentals/logging/index).</span><span class="sxs-lookup"><span data-stu-id="e64b0-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="e64b0-115">Protokolle sind ein guter Ausgangspunkt, wenn Sie unerwartetes Verhalten in ihren apps Debuggen müssen.</span><span class="sxs-lookup"><span data-stu-id="e64b0-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="e64b0-116">GrpC-Dienst Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e64b0-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="e64b0-117">Server seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten.</span><span class="sxs-lookup"><span data-stu-id="e64b0-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e64b0-118">Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="e64b0-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e64b0-119">Da GrpC-Dienste auf ASP.net Core gehostet werden, wird das ASP.net Core Protokollierungs System verwendet.</span><span class="sxs-lookup"><span data-stu-id="e64b0-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="e64b0-120">In der Standardkonfiguration protokolliert GrpC nur wenige Informationen, aber dies kann konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="e64b0-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="e64b0-121">Ausführliche Informationen zum Konfigurieren ASP.net Core Protokollierung finden Sie in der Dokumentation zur [ASP.net Core Protokollierung](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="e64b0-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="e64b0-122">GrpC fügt Protokolle unter der Kategorie `Grpc` hinzu.</span><span class="sxs-lookup"><span data-stu-id="e64b0-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="e64b0-123">Um detaillierte Protokolle von GrpC zu aktivieren, konfigurieren Sie die `Grpc` Präfixe auf `Debug` Ebene in der Datei *appSettings. JSON* , indem Sie die folgenden Elemente zum `LogLevel` unter Abschnitt in `Logging`hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="e64b0-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="e64b0-124">Sie können dies auch in *Startup.cs* mit `ConfigureLogging`konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="e64b0-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="e64b0-125">Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie den folgenden Konfigurations Wert in Ihrem Konfigurationssystem fest:</span><span class="sxs-lookup"><span data-stu-id="e64b0-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="e64b0-126">Überprüfen Sie die Dokumentation Ihres Konfigurations Systems, um zu bestimmen, wie Sie die Werte für die in der Struktur eingested</span><span class="sxs-lookup"><span data-stu-id="e64b0-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="e64b0-127">Wenn z. b. Umgebungsvariablen verwendet werden, werden anstelle der `:` zwei `_` Zeichen verwendet (z. b. `Logging__LogLevel__Grpc`).</span><span class="sxs-lookup"><span data-stu-id="e64b0-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="e64b0-128">Es wird empfohlen, die `Debug` Ebene zu verwenden, wenn Sie eine detailliertere Diagnose für Ihre APP sammeln.</span><span class="sxs-lookup"><span data-stu-id="e64b0-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="e64b0-129">Die `Trace` Ebene erzeugt eine sehr geringe Diagnose und wird nur selten zum Diagnostizieren von Problemen in Ihrer APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="e64b0-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="e64b0-130">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="e64b0-130">Sample logging output</span></span>

<span data-ttu-id="e64b0-131">Im folgenden finden Sie ein Beispiel für die Konsolenausgabe auf `Debug` Ebene eines GrpC-Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="e64b0-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a><span data-ttu-id="e64b0-132">Zugreifen auf serverseitige Protokolle</span><span class="sxs-lookup"><span data-stu-id="e64b0-132">Access server-side logs</span></span>

<span data-ttu-id="e64b0-133">Wie Sie auf serverseitige Protokolle zugreifen, hängt von der Umgebung ab, in der Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e64b0-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="e64b0-134">Als Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="e64b0-134">As a console app</span></span>

<span data-ttu-id="e64b0-135">Wenn Sie in einer Konsolen-app ausgeführt werden, sollte die [Konsolen](xref:fundamentals/logging/index#console-provider) Protokollierung standardmäßig aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="e64b0-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="e64b0-136">GrpC-Protokolle werden in der-Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e64b0-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="e64b0-137">Andere Umgebungen</span><span class="sxs-lookup"><span data-stu-id="e64b0-137">Other environments</span></span>

<span data-ttu-id="e64b0-138">Wenn die app in einer anderen Umgebung (z. b. Docker, Kubernetes oder Windows-Dienst) bereitgestellt wird, finden Sie unter <xref:fundamentals/logging/index> Weitere Informationen zum Konfigurieren von Protokollierungs Anbietern, die für die Umgebung geeignet sind.</span><span class="sxs-lookup"><span data-stu-id="e64b0-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="e64b0-139">GrpC-Client Protokollierung</span><span class="sxs-lookup"><span data-stu-id="e64b0-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="e64b0-140">Client seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten.</span><span class="sxs-lookup"><span data-stu-id="e64b0-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="e64b0-141">Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="e64b0-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="e64b0-142">Wenn Sie Protokolle vom .NET-Client erhalten möchten, können Sie die `GrpcChannelOptions.LoggerFactory`-Eigenschaft festlegen, wenn der Client Kanal erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="e64b0-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="e64b0-143">Wenn Sie einen GrpC-Dienst aus einer ASP.net Core-App aufrufen, kann die Protokollierungs Factory aus der Abhängigkeitsinjektion (di) aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="e64b0-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="e64b0-144">Eine alternative Möglichkeit zum Aktivieren der Client Protokollierung ist die Verwendung der [GrpC-clientfactory](xref:grpc/clientfactory) zum Erstellen des Clients.</span><span class="sxs-lookup"><span data-stu-id="e64b0-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="e64b0-145">Ein GrpC-Client, der bei der clientfactory registriert und von di aufgelöst wird, verwendet automatisch die konfigurierte Protokollierung der app.</span><span class="sxs-lookup"><span data-stu-id="e64b0-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="e64b0-146">Wenn Ihre APP nicht di verwendet, können Sie mit [loggerfactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)eine neue `ILoggerFactory`-Instanz erstellen.</span><span class="sxs-lookup"><span data-stu-id="e64b0-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="e64b0-147">Um auf diese Methode zuzugreifen, fügen Sie der APP das Paket [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="e64b0-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="e64b0-148">GrpC-Client Protokoll Bereiche</span><span class="sxs-lookup"><span data-stu-id="e64b0-148">gRPC client log scopes</span></span>

<span data-ttu-id="e64b0-149">Der GrpC-Client fügt Protokolle, die während eines GrpC-Aufrufes aufgerufen wurden, einen [Protokollierungs Bereich](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes)</span><span class="sxs-lookup"><span data-stu-id="e64b0-149">The gRPC client adds a [logging scope](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="e64b0-150">Der Bereich enthält Metadaten im Zusammenhang mit dem GrpC-Befehl:</span><span class="sxs-lookup"><span data-stu-id="e64b0-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="e64b0-151">**Grpcmethodtype** : der Typ der GrpC-Methode.</span><span class="sxs-lookup"><span data-stu-id="e64b0-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="e64b0-152">Mögliche Werte sind Namen aus `Grpc.Core.MethodType` Enumeration, z. b. unäres</span><span class="sxs-lookup"><span data-stu-id="e64b0-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="e64b0-153">**Grpcursor** : der relative URI der GrpC-Methode, z. b./Greet. Greeter/sayhellos</span><span class="sxs-lookup"><span data-stu-id="e64b0-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="e64b0-154">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="e64b0-154">Sample logging output</span></span>

<span data-ttu-id="e64b0-155">Im folgenden finden Sie ein Beispiel für die Konsolenausgabe auf der `Debug` Ebene eines GrpC-Clients:</span><span class="sxs-lookup"><span data-stu-id="e64b0-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a><span data-ttu-id="e64b0-156">Ablaufverfolgung</span><span class="sxs-lookup"><span data-stu-id="e64b0-156">Tracing</span></span>

<span data-ttu-id="e64b0-157">GrpC-Dienste und der GrpC-Client stellen Informationen zu GrpC-aufrufen mithilfe von [diagnosticsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) und [Aktivität](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)bereit.</span><span class="sxs-lookup"><span data-stu-id="e64b0-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="e64b0-158">.Net-GrpC verwendet eine Aktivität zur Darstellung eines GrpC-Aufrufes.</span><span class="sxs-lookup"><span data-stu-id="e64b0-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="e64b0-159">Ablauf Verfolgungs Ereignisse werden am Anfang und am Ende der Aktivität "GrpC-Anrufe" in die Diagnose Quelle geschrieben.</span><span class="sxs-lookup"><span data-stu-id="e64b0-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="e64b0-160">Die Ablauf Verfolgung erfasst keine Informationen darüber, wann Nachrichten über die Lebensdauer von GrpC Streaming-aufrufen gesendet werden.</span><span class="sxs-lookup"><span data-stu-id="e64b0-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="e64b0-161">GrpC-Dienst Ablauf Verfolgung</span><span class="sxs-lookup"><span data-stu-id="e64b0-161">gRPC service tracing</span></span>

<span data-ttu-id="e64b0-162">GrpC-Dienste werden auf ASP.net Core gehostet, die Ereignisse über eingehende HTTP-Anforderungen meldet.</span><span class="sxs-lookup"><span data-stu-id="e64b0-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="e64b0-163">GrpC-spezifische Metadaten werden der vorhandenen HTTP-Anforderungs Diagnose hinzugefügt, die ASP.net Core bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="e64b0-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="e64b0-164">Der Name der Diagnose Quelle ist `Microsoft.AspNetCore`.</span><span class="sxs-lookup"><span data-stu-id="e64b0-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="e64b0-165">Der Aktivitäts Name ist `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span><span class="sxs-lookup"><span data-stu-id="e64b0-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="e64b0-166">Der Name der vom GrpC-Aufruf aufgerufenen GrpC-Methode wird als Tag mit dem Namen `grpc.method`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e64b0-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="e64b0-167">Der Status Code des GrpC-Aufrufens, wenn er fertig ist, wird als Tag mit dem Namen `grpc.status_code`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e64b0-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="e64b0-168">GrpC-Client Ablauf Verfolgung</span><span class="sxs-lookup"><span data-stu-id="e64b0-168">gRPC client tracing</span></span>

<span data-ttu-id="e64b0-169">Der .net-GrpC-Client verwendet `HttpClient`, um GrpC-Aufrufe durchführen.</span><span class="sxs-lookup"><span data-stu-id="e64b0-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="e64b0-170">Obwohl `HttpClient` Diagnose Ereignisse schreibt, stellt der .net-GrpC-Client eine benutzerdefinierte Diagnose Quelle, Aktivität und Ereignisse bereit, sodass umfassende Informationen zu einem GrpC-Rückruf gesammelt werden können.</span><span class="sxs-lookup"><span data-stu-id="e64b0-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="e64b0-171">Der Name der Diagnose Quelle ist `Grpc.Net.Client`.</span><span class="sxs-lookup"><span data-stu-id="e64b0-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="e64b0-172">Der Aktivitäts Name ist `Grpc.Net.Client.GrpcOut`.</span><span class="sxs-lookup"><span data-stu-id="e64b0-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="e64b0-173">Der Name der vom GrpC-Aufruf aufgerufenen GrpC-Methode wird als Tag mit dem Namen `grpc.method`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e64b0-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="e64b0-174">Der Status Code des GrpC-Aufrufens, wenn er fertig ist, wird als Tag mit dem Namen `grpc.status_code`hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e64b0-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="e64b0-175">Erfassen der Ablauf Verfolgung</span><span class="sxs-lookup"><span data-stu-id="e64b0-175">Collecting tracing</span></span>

<span data-ttu-id="e64b0-176">Die einfachste Möglichkeit, `DiagnosticSource` zu verwenden, besteht darin, eine telemetriebibliothek wie [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) oder [opentelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in der APP zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="e64b0-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="e64b0-177">Die Bibliothek verarbeitet Informationen zu GrpC-aufrufen neben anderen APP-Telemetriedaten.</span><span class="sxs-lookup"><span data-stu-id="e64b0-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="e64b0-178">Die Ablauf Verfolgung kann in einem verwalteten Dienst wie Application Insights angezeigt werden, oder Sie können ein eigenes verteiltes Ablauf Verfolgungssystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="e64b0-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="e64b0-179">Opentelemetry unterstützt das Exportieren von Ablauf Verfolgungs Daten in [Jaeger](https://www.jaegertracing.io/) und [Zipkin](https://zipkin.io/).</span><span class="sxs-lookup"><span data-stu-id="e64b0-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="e64b0-180">`DiagnosticSource` können Ablauf Verfolgungs Ereignisse im Code mithilfe `DiagnosticListener`verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="e64b0-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="e64b0-181">Informationen zum lauschen auf eine Diagnose Quelle mit Code finden Sie im [Benutzerhandbuch für diagnosticsource](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span><span class="sxs-lookup"><span data-stu-id="e64b0-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="e64b0-182">Mit telemetriebibliotheken werden zurzeit keine GrpC-spezifischen `Grpc.Net.Client.GrpcOut` Telemetriedaten erfasst.</span><span class="sxs-lookup"><span data-stu-id="e64b0-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="e64b0-183">Arbeiten, um telemetriebibliotheken zu verbessern, die diese Ablauf Verfolgung erfassen</span><span class="sxs-lookup"><span data-stu-id="e64b0-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="e64b0-184">metrics</span><span class="sxs-lookup"><span data-stu-id="e64b0-184">Metrics</span></span>

<span data-ttu-id="e64b0-185">Metriken sind eine Darstellung von Daten Messungen über Zeiträume, z. b. Anforderungen pro Sekunde.</span><span class="sxs-lookup"><span data-stu-id="e64b0-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="e64b0-186">Metrikdaten ermöglichen die Überwachung des Zustands einer APP auf hoher Ebene.</span><span class="sxs-lookup"><span data-stu-id="e64b0-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="e64b0-187">.Net-GrpC-Metriken werden mithilfe von `EventCounter`ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="e64b0-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="e64b0-188">GrpC-dienstmetriken</span><span class="sxs-lookup"><span data-stu-id="e64b0-188">gRPC service metrics</span></span>

<span data-ttu-id="e64b0-189">GrpC-servermetriken werden auf `Grpc.AspNetCore.Server` Ereignis Quelle gemeldet.</span><span class="sxs-lookup"><span data-stu-id="e64b0-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="e64b0-190">Name</span><span class="sxs-lookup"><span data-stu-id="e64b0-190">Name</span></span>                      | <span data-ttu-id="e64b0-191">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="e64b0-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="e64b0-192">Aufrufe gesamt</span><span class="sxs-lookup"><span data-stu-id="e64b0-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="e64b0-193">Aktuelle Aufrufe</span><span class="sxs-lookup"><span data-stu-id="e64b0-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="e64b0-194">Fehlgeschlagene Aufrufe gesamt</span><span class="sxs-lookup"><span data-stu-id="e64b0-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="e64b0-195">Stichtag für Anrufe überschritten.</span><span class="sxs-lookup"><span data-stu-id="e64b0-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="e64b0-196">Gesamtzahl der gesendeten Nachrichten“</span><span class="sxs-lookup"><span data-stu-id="e64b0-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="e64b0-197">Empfangene Nachrichten insgesamt</span><span class="sxs-lookup"><span data-stu-id="e64b0-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="e64b0-198">Nicht implementierte Aufrufe gesamt</span><span class="sxs-lookup"><span data-stu-id="e64b0-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="e64b0-199">ASP.net Core bietet auch eine eigene Metrik für `Microsoft.AspNetCore.Hosting` Ereignis Quelle.</span><span class="sxs-lookup"><span data-stu-id="e64b0-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="e64b0-200">GrpC-Clientmetriken</span><span class="sxs-lookup"><span data-stu-id="e64b0-200">gRPC client metrics</span></span>

<span data-ttu-id="e64b0-201">GrpC-Clientmetriken werden auf `Grpc.Net.Client` Ereignis Quelle gemeldet.</span><span class="sxs-lookup"><span data-stu-id="e64b0-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="e64b0-202">Name</span><span class="sxs-lookup"><span data-stu-id="e64b0-202">Name</span></span>                      | <span data-ttu-id="e64b0-203">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="e64b0-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="e64b0-204">Aufrufe gesamt</span><span class="sxs-lookup"><span data-stu-id="e64b0-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="e64b0-205">Aktuelle Aufrufe</span><span class="sxs-lookup"><span data-stu-id="e64b0-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="e64b0-206">Fehlgeschlagene Aufrufe gesamt</span><span class="sxs-lookup"><span data-stu-id="e64b0-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="e64b0-207">Stichtag für Anrufe überschritten.</span><span class="sxs-lookup"><span data-stu-id="e64b0-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="e64b0-208">Gesamtzahl der gesendeten Nachrichten“</span><span class="sxs-lookup"><span data-stu-id="e64b0-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="e64b0-209">Empfangene Nachrichten insgesamt</span><span class="sxs-lookup"><span data-stu-id="e64b0-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="e64b0-210">Metriken beobachten</span><span class="sxs-lookup"><span data-stu-id="e64b0-210">Observe metrics</span></span>

<span data-ttu-id="e64b0-211">[dotnet-Counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) ist ein Leistungs Überwachungs Tool für die Ad-hoc-Integritäts Überwachung und Leistungs Untersuchung auf erster Ebene.</span><span class="sxs-lookup"><span data-stu-id="e64b0-211">[dotnet-counters](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="e64b0-212">Überwachen Sie eine .net-App mit `Grpc.AspNetCore.Server` oder `Grpc.Net.Client` als Anbieter Namen.</span><span class="sxs-lookup"><span data-stu-id="e64b0-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

<span data-ttu-id="e64b0-213">Eine weitere Möglichkeit, um GrpC-Metriken zu beobachten, besteht darin, Indikator Daten mithilfe des [Microsoft. applicationinsights. eventcountercollector-Pakets](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)von Application Insights aufzuzeichnen.</span><span class="sxs-lookup"><span data-stu-id="e64b0-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="e64b0-214">Nach der Einrichtung sammelt Application Insights allgemeine .NET-Leistungsindikatoren zur Laufzeit.</span><span class="sxs-lookup"><span data-stu-id="e64b0-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="e64b0-215">die Leistungsindikatoren von GrpC werden standardmäßig nicht erfasst, App Insights kann jedoch so angepasst werden, dass [zusätzliche Indikatoren enthalten](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)sind.</span><span class="sxs-lookup"><span data-stu-id="e64b0-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="e64b0-216">Geben Sie die zu sammelnden GrpC-Indikatoren für Application Insight in *Startup.cs*an:</span><span class="sxs-lookup"><span data-stu-id="e64b0-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a><span data-ttu-id="e64b0-217">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e64b0-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
