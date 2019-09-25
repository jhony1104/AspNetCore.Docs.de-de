---
title: Protokollierung und Diagnose in GrpC unter .net
author: jamesnk
description: Erfahren Sie, wie Sie die Diagnose von ihrer GrpC-app in .net sammeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 7194e91b40a08c4a7ee619b8f207900af2683aa1
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250735"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="5230f-103">Protokollierung und Diagnose in GrpC unter .net</span><span class="sxs-lookup"><span data-stu-id="5230f-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="5230f-104">Von [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="5230f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="5230f-105">Dieser Artikel enthält Anleitungen zum Sammeln von Diagnoseinformationen aus ihrer GrpC-APP, um Probleme bei der Problembehandlung zu beheben.</span><span class="sxs-lookup"><span data-stu-id="5230f-105">This article provides guidance for gathering diagnostics from your gRPC app to help troubleshoot issues.</span></span>

## <a name="grpc-services-logging"></a><span data-ttu-id="5230f-106">GrpC-Dienst Protokollierung</span><span class="sxs-lookup"><span data-stu-id="5230f-106">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="5230f-107">Server seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten.</span><span class="sxs-lookup"><span data-stu-id="5230f-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="5230f-108">Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="5230f-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5230f-109">Da GrpC-Dienste auf ASP.net Core gehostet werden, wird das ASP.net Core Protokollierungs System verwendet.</span><span class="sxs-lookup"><span data-stu-id="5230f-109">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="5230f-110">In der Standardkonfiguration protokolliert GrpC nur wenige Informationen, aber dies kann konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="5230f-110">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="5230f-111">Ausführliche Informationen zum Konfigurieren ASP.net Core Protokollierung finden Sie in der Dokumentation zur [ASP.net Core Protokollierung](xref:fundamentals/logging/index#configuration) .</span><span class="sxs-lookup"><span data-stu-id="5230f-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="5230f-112">GrpC fügt Protokolle unter der `Grpc` Kategorie hinzu.</span><span class="sxs-lookup"><span data-stu-id="5230f-112">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="5230f-113">Um detaillierte Protokolle von GrpC zu aktivieren, konfigurieren `Grpc` Sie die Präfixe `Debug` auf der Ebene in der Datei *appSettings. JSON* , `LogLevel` indem Sie die folgenden Elemente zum unter Abschnitt `Logging`in hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="5230f-113">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="5230f-114">Sie können dies auch in *Startup.cs* mit `ConfigureLogging`folgenden Einstellungen konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="5230f-114">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="5230f-115">Wenn Sie keine JSON-basierte Konfiguration verwenden, legen Sie den folgenden Konfigurations Wert in Ihrem Konfigurationssystem fest:</span><span class="sxs-lookup"><span data-stu-id="5230f-115">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="5230f-116">Überprüfen Sie die Dokumentation Ihres Konfigurations Systems, um zu bestimmen, wie Sie die Werte für die in der Struktur eingested</span><span class="sxs-lookup"><span data-stu-id="5230f-116">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="5230f-117">Wenn z. b. Umgebungsvariablen verwendet werden `_` , werden anstelle `:` von zwei Zeichen verwendet (z `Logging__LogLevel__Grpc`. b.).</span><span class="sxs-lookup"><span data-stu-id="5230f-117">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="5230f-118">Es wird empfohlen, `Debug` die Ebene zu verwenden, wenn Sie eine detailliertere Diagnose für Ihre APP sammeln.</span><span class="sxs-lookup"><span data-stu-id="5230f-118">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="5230f-119">Die `Trace` Ebene erzeugt eine sehr geringe Diagnose und wird nur selten zum Diagnostizieren von Problemen in Ihrer APP benötigt.</span><span class="sxs-lookup"><span data-stu-id="5230f-119">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

### <a name="sample-logging-output"></a><span data-ttu-id="5230f-120">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="5230f-120">Sample logging output</span></span>

<span data-ttu-id="5230f-121">Im folgenden finden Sie ein Beispiel für die Konsolen `Debug` Ausgabe auf der Ebene eines GrpC-Dienstanbieter:</span><span class="sxs-lookup"><span data-stu-id="5230f-121">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

## <a name="access-server-side-logs"></a><span data-ttu-id="5230f-122">Zugreifen auf serverseitige Protokolle</span><span class="sxs-lookup"><span data-stu-id="5230f-122">Access server-side logs</span></span>

<span data-ttu-id="5230f-123">Wie Sie auf serverseitige Protokolle zugreifen, hängt von der Umgebung ab, in der Sie ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="5230f-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app"></a><span data-ttu-id="5230f-124">Als Konsolen-App</span><span class="sxs-lookup"><span data-stu-id="5230f-124">As a console app</span></span>

<span data-ttu-id="5230f-125">Wenn Sie in einer Konsolen-app ausgeführt werden, sollte die [Konsolen](xref:fundamentals/logging/index#console-provider) Protokollierung standardmäßig aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="5230f-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="5230f-126">GrpC-Protokolle werden in der-Konsole angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5230f-126">gRPC logs will appear in the console.</span></span>

### <a name="other-environments"></a><span data-ttu-id="5230f-127">Andere Umgebungen</span><span class="sxs-lookup"><span data-stu-id="5230f-127">Other environments</span></span>

<span data-ttu-id="5230f-128">Wenn die app in einer anderen Umgebung (z. b. Docker, Kubernetes oder Windows-Dienst) bereitgestellt <xref:fundamentals/logging/index> wird, finden Sie weitere Informationen zum Konfigurieren von Protokollierungs Anbietern, die für die Umgebung geeignet sind.</span><span class="sxs-lookup"><span data-stu-id="5230f-128">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="grpc-client-logging"></a><span data-ttu-id="5230f-129">GrpC-Client Protokollierung</span><span class="sxs-lookup"><span data-stu-id="5230f-129">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="5230f-130">Client seitige Protokolle können vertrauliche Informationen aus Ihrer APP enthalten.</span><span class="sxs-lookup"><span data-stu-id="5230f-130">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="5230f-131">Veröffentlichen Sie **niemals** unformatierte Protokolle von Produktions-apps in öffentlichen Foren wie GitHub.</span><span class="sxs-lookup"><span data-stu-id="5230f-131">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="5230f-132">Wenn Sie Protokolle vom .NET-Client erhalten möchten, können Sie `GrpcChannelOptions.LoggerFactory` die-Eigenschaft festlegen, wenn der Client Kanal erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="5230f-132">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="5230f-133">Wenn Sie einen GrpC-Dienst aus einer ASP.net Core-App aufrufen, kann die Protokollierungs Factory aus der Abhängigkeitsinjektion (di) aufgelöst werden:</span><span class="sxs-lookup"><span data-stu-id="5230f-133">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="5230f-134">Eine alternative Möglichkeit zum Aktivieren der Client Protokollierung ist die Verwendung der [GrpC-clientfactory](xref:grpc/clientfactory) zum Erstellen des Clients.</span><span class="sxs-lookup"><span data-stu-id="5230f-134">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="5230f-135">Ein GrpC-Client, der bei der clientfactory registriert und von di aufgelöst wird, verwendet automatisch die konfigurierte Protokollierung der app.</span><span class="sxs-lookup"><span data-stu-id="5230f-135">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="5230f-136">Wenn Ihre APP nicht di verwendet, können Sie mit `ILoggerFactory` [loggerfactory. Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)eine neue Instanz erstellen.</span><span class="sxs-lookup"><span data-stu-id="5230f-136">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="5230f-137">Um auf diese Methode zuzugreifen, fügen Sie der APP das Paket [Microsoft. Extensions. Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) hinzu.</span><span class="sxs-lookup"><span data-stu-id="5230f-137">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a><span data-ttu-id="5230f-138">Beispiel einer Protokollierungsausgabe</span><span class="sxs-lookup"><span data-stu-id="5230f-138">Sample logging output</span></span>

<span data-ttu-id="5230f-139">Im folgenden finden Sie ein Beispiel für die Konsolen `Debug` Ausgabe auf der Ebene eines GrpC-Clients:</span><span class="sxs-lookup"><span data-stu-id="5230f-139">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="5230f-140">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5230f-140">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
