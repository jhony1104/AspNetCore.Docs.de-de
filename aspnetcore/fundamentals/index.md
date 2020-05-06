---
title: ASP.NET Core – Grundlagen
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen von ASP.NET Core-Apps kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/index
ms.openlocfilehash: 0f0e97246b6e1381b85866bd831ee9b4b150650d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774326"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="83dbc-103">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="83dbc-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="83dbc-104">In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="83dbc-104">This article provides an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="83dbc-105">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="83dbc-105">The Startup class</span></span>

<span data-ttu-id="83dbc-106">In der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="83dbc-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="83dbc-107">werden die von der App erforderlichen Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="83dbc-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="83dbc-108">Die Anforderungsverarbeitungspipeline der App wird mit mehreren Middlewarekomponenten definiert.</span><span class="sxs-lookup"><span data-stu-id="83dbc-108">The app's request handling pipeline is defined, as a series of middleware components.</span></span>

<span data-ttu-id="83dbc-109">Beispiel für eine `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="83dbc-109">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="83dbc-110">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-110">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="83dbc-111">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="83dbc-111">Dependency injection (services)</span></span>

<span data-ttu-id="83dbc-112">ASP.NET Core umfasst ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste in einer App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-112">ASP.NET Core includes a built-in dependency injection (DI) framework that makes configured services available throughout an app.</span></span> <span data-ttu-id="83dbc-113">Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="83dbc-113">For example, a logging component is a service.</span></span>

<span data-ttu-id="83dbc-114">wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-114">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="83dbc-115">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="83dbc-115">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/ConfigureServices.cs)]

<span data-ttu-id="83dbc-116">Dienste werden üblicherweise mit einer Constructor Injection aus der DI aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="83dbc-116">Services are typically resolved from DI using constructor injection.</span></span> <span data-ttu-id="83dbc-117">Bei der Constructor Injection deklariert eine Klasse einen Konstruktorparameter des erforderlichen Typs oder einer Schnittstelle.</span><span class="sxs-lookup"><span data-stu-id="83dbc-117">With constructor injection, a class declares a constructor parameter of either the required type or an interface.</span></span> <span data-ttu-id="83dbc-118">Das DI-Framework stellt zur Laufzeit eine Instanz dieses Diensts bereit.</span><span class="sxs-lookup"><span data-stu-id="83dbc-118">The DI framework provides an instance of this service at runtime.</span></span>

<span data-ttu-id="83dbc-119">Im folgenden Beispiel wird die Constructor Injection verwendet, um `RazorPagesMovieContext` aus der DI zu lösen:</span><span class="sxs-lookup"><span data-stu-id="83dbc-119">The following example uses constructor injection to resolve a `RazorPagesMovieContext` from DI:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="83dbc-120">Wenn der integrierte IoC-Container (Inversion of Control, Steuerungsumkehr) nicht alle Anforderungen einer App erfüllt, kann stattdessen ein IoC-Drittanbietercontainer verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-120">If the built-in Inversion of Control (IoC) container doesn't meet all of an app's needs, a third-party IoC container can be used instead.</span></span>

<span data-ttu-id="83dbc-121">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-121">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="83dbc-122">Middleware</span><span class="sxs-lookup"><span data-stu-id="83dbc-122">Middleware</span></span>

<span data-ttu-id="83dbc-123">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-123">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="83dbc-124">Jede Komponente führt Vorgänge in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-124">Each component performs operations on an `HttpContext` and either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="83dbc-125">Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen einer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-125">By convention, a middleware component is added to the pipeline by invoking a `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="83dbc-126">Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.</span><span class="sxs-lookup"><span data-stu-id="83dbc-126">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="83dbc-127">Im folgenden Beispiel wird einer Anforderungsverarbeitungspipeline konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="83dbc-127">The following example configures a request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Configure.cs)]

<span data-ttu-id="83dbc-128">ASP.NET Core enthält zahlreiche integrierte Middlewareanwendungen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-128">ASP.NET Core includes a rich set of built-in middleware.</span></span> <span data-ttu-id="83dbc-129">Es können auch benutzerdefinierte Middlewarekomponenten geschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-129">Custom middleware components can also be written.</span></span>

<span data-ttu-id="83dbc-130">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-130">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="83dbc-131">Host</span><span class="sxs-lookup"><span data-stu-id="83dbc-131">Host</span></span>

<span data-ttu-id="83dbc-132">Beim Starten erstellt eine ASP.NET Core-App einen *Host*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-132">On startup, an ASP.NET Core app builds a *host*.</span></span> <span data-ttu-id="83dbc-133">Der Host kapselt alle Ressourcen der App, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="83dbc-133">The host encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="83dbc-134">eine HTTP-Serverimplementierung</span><span class="sxs-lookup"><span data-stu-id="83dbc-134">An HTTP server implementation</span></span>
* <span data-ttu-id="83dbc-135">Middlewarekomponenten</span><span class="sxs-lookup"><span data-stu-id="83dbc-135">Middleware components</span></span>
* <span data-ttu-id="83dbc-136">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="83dbc-136">Logging</span></span>
* <span data-ttu-id="83dbc-137">DI-Dienste</span><span class="sxs-lookup"><span data-stu-id="83dbc-137">Dependency injection (DI) services</span></span>
* <span data-ttu-id="83dbc-138">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="83dbc-138">Configuration</span></span>

<span data-ttu-id="83dbc-139">Es gibt zwei verschiedene Hosts:</span><span class="sxs-lookup"><span data-stu-id="83dbc-139">There are two different hosts:</span></span> 

* <span data-ttu-id="83dbc-140">Generischer .NET-Host</span><span class="sxs-lookup"><span data-stu-id="83dbc-140">.NET Generic Host</span></span>
* <span data-ttu-id="83dbc-141">ASP.NET Core-Webhost</span><span class="sxs-lookup"><span data-stu-id="83dbc-141">ASP.NET Core Web Host</span></span>

<span data-ttu-id="83dbc-142">Der generische .NET-Host wird empfohlen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-142">The .NET Generic Host is recommended.</span></span> <span data-ttu-id="83dbc-143">Der ASP.NET Core-Webhost wird lediglich für die Abwärtskompatibilität zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-143">The ASP.NET Core Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="83dbc-144">Im folgenden Beispiel wird ein neuer generischer .NET-Host erstellt:</span><span class="sxs-lookup"><span data-stu-id="83dbc-144">The following example creates a .NET Generic Host:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/Program.cs)]

<span data-ttu-id="83dbc-145">Mit den Methoden `CreateDefaultBuilder` und `ConfigureWebHostDefaults` wird ein Host mit mehreren Standardoptionen konfiguriert, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="83dbc-145">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with a set of default options, such as:</span></span>

* <span data-ttu-id="83dbc-146">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-146">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="83dbc-147">Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-147">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="83dbc-148">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="83dbc-148">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="83dbc-149">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-149">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="83dbc-150">Nicht webbasierte Szenarios</span><span class="sxs-lookup"><span data-stu-id="83dbc-150">Non-web scenarios</span></span>

<span data-ttu-id="83dbc-151">Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-151">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="83dbc-152">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-152">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="83dbc-153">Server</span><span class="sxs-lookup"><span data-stu-id="83dbc-153">Servers</span></span>

<span data-ttu-id="83dbc-154">Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-154">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="83dbc-155">Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="83dbc-155">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="83dbc-156">Windows</span><span class="sxs-lookup"><span data-stu-id="83dbc-156">Windows</span></span>](#tab/windows)

<span data-ttu-id="83dbc-157">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="83dbc-157">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="83dbc-158">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="83dbc-158">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="83dbc-159">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-159">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="83dbc-160">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-160">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="83dbc-161">Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-161">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="83dbc-162">Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-162">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="83dbc-163">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="83dbc-163">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="83dbc-164">macOS</span><span class="sxs-lookup"><span data-stu-id="83dbc-164">macOS</span></span>](#tab/macos)

<span data-ttu-id="83dbc-165">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-165">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83dbc-166">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-166">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83dbc-167">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-167">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="83dbc-168">Linux</span><span class="sxs-lookup"><span data-stu-id="83dbc-168">Linux</span></span>](#tab/linux)

<span data-ttu-id="83dbc-169">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-169">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83dbc-170">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-170">In ASP.NET Core 2.0 or later, Kestrel can run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83dbc-171">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-171">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="83dbc-172">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-172">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="83dbc-173">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="83dbc-173">Configuration</span></span>

<span data-ttu-id="83dbc-174">ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft.</span><span class="sxs-lookup"><span data-stu-id="83dbc-174">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="83dbc-175">Integrierte Konfigurationsanbieter stehen für eine Vielzahl von Quellen zur Verfügung, z. B. für *JSON*-Dateien, *XML*-Dateien, Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="83dbc-175">Built-in configuration providers are available for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="83dbc-176">Schreiben Sie benutzerdefinierte Konfigurationsanbieter, um andere Quellen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-176">Write custom configuration providers to support other sources.</span></span>

<span data-ttu-id="83dbc-177">ASP.NET Core-Apps werden [standardmäßig](xref:fundamentals/configuration/index#default) zum Lesen aus *appsettings.json*, Umgebungsvariablen, der Befehlszeile und mehr konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="83dbc-177">By [default](xref:fundamentals/configuration/index#default), ASP.NET Core apps are configured to read from *appsettings.json*, environment variables, the command line, and more.</span></span> <span data-ttu-id="83dbc-178">Wenn die Konfiguration der App geladen wird, überschreiben Werte aus Umgebungsvariablen die Werte von *appsettings.json*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-178">When the app's configuration is loaded, values from environment variables override values from *appsettings.json*.</span></span>

<span data-ttu-id="83dbc-179">Die bevorzugte Methode für das Lesen zugehöriger Konfigurationswerte ist die Verwendung des [Optionsmusters](xref:fundamentals/configuration/options).</span><span class="sxs-lookup"><span data-stu-id="83dbc-179">The preferred way to read related configuration values is using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="83dbc-180">Weitere Informationen finden Sie unter [Binden hierarchischer Konfigurationsdaten mit dem Optionsmuster](xref:fundamentals/configuration/index#optpat).</span><span class="sxs-lookup"><span data-stu-id="83dbc-180">For more information, see [Bind hierarchical configuration data using the options pattern](xref:fundamentals/configuration/index#optpat).</span></span>

<span data-ttu-id="83dbc-181">Zum Verwalten vertraulicher Konfigurationsdaten wie Kennwörter bietet ASP.NET Core den [Secret Manager](xref:security/app-secrets#secret-manager).</span><span class="sxs-lookup"><span data-stu-id="83dbc-181">For managing confidential configuration data such as passwords, ASP.NET Core provides the [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="83dbc-182">Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="83dbc-182">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="83dbc-183">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-183">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="environments"></a><span data-ttu-id="83dbc-184">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="83dbc-184">Environments</span></span>

<span data-ttu-id="83dbc-185">Ausführungsumgebungen wie `Development`, `Staging` und `Production` sind in ASP.NET Core von besonderer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-185">Execution environments, such as `Development`, `Staging`, and `Production`, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="83dbc-186">Legen Sie die Umgebung fest, in der eine App ausgeführt wird, indem Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-186">Specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="83dbc-187">ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IWebHostEnvironment`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-187">ASP.NET Core reads that environment variable at app startup and stores the value in an `IWebHostEnvironment` implementation.</span></span> <span data-ttu-id="83dbc-188">Diese Implementierung ist per DI überall in einer App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="83dbc-188">This implementation is available anywhere in an app via dependency injection (DI).</span></span>

<span data-ttu-id="83dbc-189">Im Folgenden Beispiel wird die App so konfiguriert, dass sie ausführliche Fehlerinformationen angibt, wenn sie in der `Development`-Umgebung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="83dbc-189">The following example configures the app to provide detailed error information when running in the `Development` environment:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="83dbc-190">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-190">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="83dbc-191">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="83dbc-191">Logging</span></span>

<span data-ttu-id="83dbc-192">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-192">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="83dbc-193">Folgende Anbieter sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="83dbc-193">Available providers include:</span></span>

* <span data-ttu-id="83dbc-194">Konsole</span><span class="sxs-lookup"><span data-stu-id="83dbc-194">Console</span></span>
* <span data-ttu-id="83dbc-195">Debug</span><span class="sxs-lookup"><span data-stu-id="83dbc-195">Debug</span></span>
* <span data-ttu-id="83dbc-196">Ereignisablaufverfolgung unter Windows</span><span class="sxs-lookup"><span data-stu-id="83dbc-196">Event Tracing on Windows</span></span>
* <span data-ttu-id="83dbc-197">Windows-Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="83dbc-197">Windows Event Log</span></span>
* <span data-ttu-id="83dbc-198">TraceSource</span><span class="sxs-lookup"><span data-stu-id="83dbc-198">TraceSource</span></span>
* <span data-ttu-id="83dbc-199">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="83dbc-199">Azure App Service</span></span>
* <span data-ttu-id="83dbc-200">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="83dbc-200">Azure Application Insights</span></span>

<span data-ttu-id="83dbc-201">Zum Erstellen von Protokollen lösen Sie einen <xref:Microsoft.Extensions.Logging.ILogger%601>-Dienst aus DI, und rufen Sie Protokollierungsmethoden wie <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> auf.</span><span class="sxs-lookup"><span data-stu-id="83dbc-201">To create logs, resolve an <xref:Microsoft.Extensions.Logging.ILogger%601> service from dependency injection (DI) and call logging methods such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*>.</span></span> <span data-ttu-id="83dbc-202">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="83dbc-202">For example:</span></span>

[!code-csharp[](index/samples_snapshot/3.x/TodoController.cs?highlight=5,13,19)]

<span data-ttu-id="83dbc-203">Protokollierungsmethoden wie `LogInformation` unterstützen eine beliebige Anzahl von Feldern.</span><span class="sxs-lookup"><span data-stu-id="83dbc-203">Logging methods such as `LogInformation` support any number of fields.</span></span> <span data-ttu-id="83dbc-204">Diese Felder werden häufig zum Erstellen einer `string`-Meldung verwendet, einige Protokollierungsanbieter senden sie jedoch als separate Felder an einen Datenspeicher.</span><span class="sxs-lookup"><span data-stu-id="83dbc-204">These fields are commonly used to construct a message `string`, but some logging providers send these to a data store as separate fields.</span></span> <span data-ttu-id="83dbc-205">Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="83dbc-205">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="83dbc-206">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-206">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="83dbc-207">Routing</span><span class="sxs-lookup"><span data-stu-id="83dbc-207">Routing</span></span>

<span data-ttu-id="83dbc-208">Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-208">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="83dbc-209">Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware.</span><span class="sxs-lookup"><span data-stu-id="83dbc-209">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="83dbc-210">Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-210">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="83dbc-211">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-211">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="83dbc-212">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="83dbc-212">Error handling</span></span>

<span data-ttu-id="83dbc-213">ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="83dbc-213">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="83dbc-214">eine Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="83dbc-214">A developer exception page</span></span>
* <span data-ttu-id="83dbc-215">benutzerdefinierte Fehlerseiten</span><span class="sxs-lookup"><span data-stu-id="83dbc-215">Custom error pages</span></span>
* <span data-ttu-id="83dbc-216">statische Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="83dbc-216">Static status code pages</span></span>
* <span data-ttu-id="83dbc-217">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="83dbc-217">Startup exception handling</span></span>

<span data-ttu-id="83dbc-218">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-218">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="83dbc-219">Übermitteln von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="83dbc-219">Make HTTP requests</span></span>

<span data-ttu-id="83dbc-220">Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-220">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="83dbc-221">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="83dbc-221">The factory:</span></span>

* <span data-ttu-id="83dbc-222">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-222">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="83dbc-223">Registrieren und konfigurieren Sie beispielsweise einen *GitHub*-Client für den Zugriff auf GitHub.</span><span class="sxs-lookup"><span data-stu-id="83dbc-223">For example, register and configure a *github* client for accessing GitHub.</span></span> <span data-ttu-id="83dbc-224">Registrieren und konfigurieren Sie einen Standardclient für andere Zwecke.</span><span class="sxs-lookup"><span data-stu-id="83dbc-224">Register and configure a default client for other purposes.</span></span>
* <span data-ttu-id="83dbc-225">Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-225">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="83dbc-226">Dieses Muster ähnelt der eingehenden Middlewarepipeline von ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83dbc-226">This pattern is similar to ASP.NET Core's inbound middleware pipeline.</span></span> <span data-ttu-id="83dbc-227">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen für HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-227">The pattern provides a mechanism to manage cross-cutting concerns for HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="83dbc-228">Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="83dbc-228">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="83dbc-229">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-229">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when managing `HttpClient` lifetimes manually.</span></span>
* <span data-ttu-id="83dbc-230">Eine konfigurierbare Protokollierungsfunktion wird über <xref:Microsoft.Extensions.Logging.ILogger> für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-230">Adds a configurable logging experience via <xref:Microsoft.Extensions.Logging.ILogger> for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="83dbc-231">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-231">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="83dbc-232">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="83dbc-232">Content root</span></span>

<span data-ttu-id="83dbc-233">Der Inhaltsstamm ist der Basispfad für:</span><span class="sxs-lookup"><span data-stu-id="83dbc-233">The content root is the base path for:</span></span>

* <span data-ttu-id="83dbc-234">Die ausführbare Datei, die die App hostet ( *.exe*).</span><span class="sxs-lookup"><span data-stu-id="83dbc-234">The executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="83dbc-235">Kompilierten Assemblys, die die App bilden ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="83dbc-235">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="83dbc-236">Inhaltsdateien, die von der App verwendet werden, z. B.:</span><span class="sxs-lookup"><span data-stu-id="83dbc-236">Content files used by the app, such as:</span></span>
  * <span data-ttu-id="83dbc-237">Razor-Dateien ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-237">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="83dbc-238">Konfigurationsdateien ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-238">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="83dbc-239">Datendateien ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-239">Data files (*.db*)</span></span>
* <span data-ttu-id="83dbc-240">Der [Webstamm](#web-root), in der Regel der Ordner *wwwroot*</span><span class="sxs-lookup"><span data-stu-id="83dbc-240">The [Web root](#web-root), typically the *wwwroot* folder.</span></span>

<span data-ttu-id="83dbc-241">Während der Entwicklung wird standardmäßig das Stammverzeichnis des Projekts als Inhaltsstamm verwendet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-241">During development, the content root defaults to the project's root directory.</span></span> <span data-ttu-id="83dbc-242">Dieses Verzeichnis ist auch der Basispfad für die Inhaltsdateien der App und den [Webstamm](#web-root).</span><span class="sxs-lookup"><span data-stu-id="83dbc-242">This directory is also the base path for both the app's content files and the [Web root](#web-root).</span></span> <span data-ttu-id="83dbc-243">Sie können einen anderen Inhaltsstamm festlegen, indem Sie den entsprechenden Pfad beim [Erstellen des Hosts](#host) festlegen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-243">Specify a different content root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="83dbc-244">Weitere Informationen finden Sie unter [Inhaltsstamm](xref:fundamentals/host/generic-host#contentroot).</span><span class="sxs-lookup"><span data-stu-id="83dbc-244">For more information, see [Content root](xref:fundamentals/host/generic-host#contentroot).</span></span>

## <a name="web-root"></a><span data-ttu-id="83dbc-245">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="83dbc-245">Web root</span></span>

<span data-ttu-id="83dbc-246">Der Webstamm ist der Basispfad für öffentliche, statische Ressourcendateien, zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="83dbc-246">The web root is the base path for public, static resource files, such as:</span></span>

* <span data-ttu-id="83dbc-247">Stylesheets ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-247">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="83dbc-248">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-248">JavaScript (*.js*)</span></span>
* <span data-ttu-id="83dbc-249">Bilder ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-249">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="83dbc-250">Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis und dessen Unterverzeichnissen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-250">By default, static files are served only from the web root directory and its sub-directories.</span></span> <span data-ttu-id="83dbc-251">Standardmäßig lautet der Webstammpfad *{Inhaltsstamm}/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-251">The web root path defaults to *{content root}/wwwroot*.</span></span> <span data-ttu-id="83dbc-252">Sie können einen anderen Webstamm festlegen, indem Sie den entsprechenden Pfad beim [Erstellen des Hosts](#host) festlegen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-252">Specify a different web root by setting its path when [building the host](#host).</span></span> <span data-ttu-id="83dbc-253">Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/host/generic-host#webroot).</span><span class="sxs-lookup"><span data-stu-id="83dbc-253">For more information, see [Web root](xref:fundamentals/host/generic-host#webroot).</span></span>

<span data-ttu-id="83dbc-254">Verhindern Sie das Veröffentlichen von Datei in *wwwroot* über [\<Inhalt > Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="83dbc-254">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="83dbc-255">Im folgenden Beispiel wird verhindert, dass Inhalte im Verzeichnis *wwwroot/local* und dessen Unterverzeichnissen veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="83dbc-255">The following example prevents publishing content in *wwwroot/local* and its sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="83dbc-256">In *CSHTML*-Dateien von Razor zeigen Tilden mit anschließendem Schrägstrich (`~/`) auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="83dbc-256">In Razor *.cshtml* files, tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="83dbc-257">Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-257">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="83dbc-258">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-258">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83dbc-259">In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="83dbc-259">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="83dbc-260">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="83dbc-260">The Startup class</span></span>

<span data-ttu-id="83dbc-261">In der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="83dbc-261">The `Startup` class is where:</span></span>

* <span data-ttu-id="83dbc-262">werden die von der App erforderlichen Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="83dbc-262">Services required by the app are configured.</span></span>
* <span data-ttu-id="83dbc-263">wird die Pipeline zur Anforderungsverarbeitung definiert.</span><span class="sxs-lookup"><span data-stu-id="83dbc-263">The request handling pipeline is defined.</span></span>

<span data-ttu-id="83dbc-264">*Dienste* sind Komponenten, die von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-264">*Services* are components that are used by the app.</span></span> <span data-ttu-id="83dbc-265">Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="83dbc-265">For example, a logging component is a service.</span></span> <span data-ttu-id="83dbc-266">wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-266">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="83dbc-267">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren *Middlewarekomponenten*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-267">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="83dbc-268">Eine Middleware kann beispielsweise Anforderungen für statische Dateien verarbeiten oder HTTP-Anforderungen zu HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-268">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="83dbc-269">Jede Middleware führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-269">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="83dbc-270">Code zum Konfigurieren der Pipeline zur Anforderungsverarbeitung wird der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-270">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="83dbc-271">Beispiel für eine `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="83dbc-271">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=3,12)]

<span data-ttu-id="83dbc-272">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="83dbc-273">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="83dbc-273">Dependency injection (services)</span></span>

<span data-ttu-id="83dbc-274">ASP.NET Core verfügt über ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste für die Klassen einer App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-274">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="83dbc-275">Eine Möglichkeit zum Abrufen einer Instanz eines Diensts in einer Klasse ist das Erstellen eines Konstruktors mit einem Parameter des erforderlichen Typs.</span><span class="sxs-lookup"><span data-stu-id="83dbc-275">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="83dbc-276">Der Parameter kann der Diensttyp oder eine Schnittstelle sein.</span><span class="sxs-lookup"><span data-stu-id="83dbc-276">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="83dbc-277">Das DI-System stellt den Dienst zur Laufzeit bereit.</span><span class="sxs-lookup"><span data-stu-id="83dbc-277">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="83dbc-278">Hier ist eine Klasse, in der Dependency Injection verwendet wird, um ein Entity Framework Core-Kontextobjekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-278">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="83dbc-279">Die hervorgehobene Zeile ist ein Beispiel für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="83dbc-279">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="83dbc-280">Obwohl die Dependency Injection integriert ist, können Sie hier, falls gewünscht, einen IoC-Container eines Drittanbieters einbinden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-280">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="83dbc-281">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-281">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="83dbc-282">Middleware</span><span class="sxs-lookup"><span data-stu-id="83dbc-282">Middleware</span></span>

<span data-ttu-id="83dbc-283">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-283">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="83dbc-284">Jede Komponente führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-284">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="83dbc-285">Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen ihrer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-285">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="83dbc-286">Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.</span><span class="sxs-lookup"><span data-stu-id="83dbc-286">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="83dbc-287">Der hervorgehobene Code in dem folgenden Beispiel konfiguriert die Pipeline zur Anforderungsverarbeitung:</span><span class="sxs-lookup"><span data-stu-id="83dbc-287">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Startup.cs?highlight=14-16)]

<span data-ttu-id="83dbc-288">ASP.NET Core enthält eine Reihe umfangreicher integrierter Middleware. Außerdem können Sie benutzerdefinierte Middleware erstellen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-288">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="83dbc-289">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-289">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="83dbc-290">Host</span><span class="sxs-lookup"><span data-stu-id="83dbc-290">Host</span></span>

<span data-ttu-id="83dbc-291">Beim Starten erstellt eine ASP.NET Core-App einen *Host*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-291">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="83dbc-292">Der Host ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="83dbc-292">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="83dbc-293">eine HTTP-Serverimplementierung</span><span class="sxs-lookup"><span data-stu-id="83dbc-293">An HTTP server implementation</span></span>
* <span data-ttu-id="83dbc-294">Middlewarekomponenten</span><span class="sxs-lookup"><span data-stu-id="83dbc-294">Middleware components</span></span>
* <span data-ttu-id="83dbc-295">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="83dbc-295">Logging</span></span>
* <span data-ttu-id="83dbc-296">DI</span><span class="sxs-lookup"><span data-stu-id="83dbc-296">DI</span></span>
* <span data-ttu-id="83dbc-297">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="83dbc-297">Configuration</span></span>

<span data-ttu-id="83dbc-298">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="83dbc-298">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="83dbc-299">Es stehen zwei Hosts zur Verfügung: der Webhost und der generische Host.</span><span class="sxs-lookup"><span data-stu-id="83dbc-299">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="83dbc-300">In ASP.NET Core 2.x ist der generische Host nur für nicht webbasierte Szenarios verfügbar.</span><span class="sxs-lookup"><span data-stu-id="83dbc-300">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="83dbc-301">Der Code zum Erstellen eines Hosts befindet sich in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="83dbc-301">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/Program.cs)]

<span data-ttu-id="83dbc-302">Die Methode `CreateDefaultBuilder` konfiguriert einen Host mit häufig verwendeten Optionen wie den folgenden:</span><span class="sxs-lookup"><span data-stu-id="83dbc-302">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="83dbc-303">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-303">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="83dbc-304">Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-304">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="83dbc-305">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="83dbc-305">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="83dbc-306">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-306">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="83dbc-307">Nicht webbasierte Szenarios</span><span class="sxs-lookup"><span data-stu-id="83dbc-307">Non-web scenarios</span></span>

<span data-ttu-id="83dbc-308">Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-308">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="83dbc-309">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-309">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="83dbc-310">Server</span><span class="sxs-lookup"><span data-stu-id="83dbc-310">Servers</span></span>

<span data-ttu-id="83dbc-311">Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-311">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="83dbc-312">Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="83dbc-312">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="83dbc-313">Windows</span><span class="sxs-lookup"><span data-stu-id="83dbc-313">Windows</span></span>](#tab/windows)

<span data-ttu-id="83dbc-314">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="83dbc-314">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="83dbc-315">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="83dbc-315">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="83dbc-316">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-316">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="83dbc-317">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-317">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="83dbc-318">Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-318">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="83dbc-319">Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-319">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="83dbc-320">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="83dbc-320">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="83dbc-321">macOS</span><span class="sxs-lookup"><span data-stu-id="83dbc-321">macOS</span></span>](#tab/macos)

<span data-ttu-id="83dbc-322">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-322">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83dbc-323">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-323">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83dbc-324">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-324">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="83dbc-325">Linux</span><span class="sxs-lookup"><span data-stu-id="83dbc-325">Linux</span></span>](#tab/linux)

<span data-ttu-id="83dbc-326">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-326">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83dbc-327">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-327">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83dbc-328">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-328">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="83dbc-329">Windows</span><span class="sxs-lookup"><span data-stu-id="83dbc-329">Windows</span></span>](#tab/windows)

<span data-ttu-id="83dbc-330">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="83dbc-330">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="83dbc-331">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="83dbc-331">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="83dbc-332">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-332">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="83dbc-333">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-333">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="83dbc-334">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="83dbc-334">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="83dbc-335">macOS</span><span class="sxs-lookup"><span data-stu-id="83dbc-335">macOS</span></span>](#tab/macos)

<span data-ttu-id="83dbc-336">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-336">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83dbc-337">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-337">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83dbc-338">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-338">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="83dbc-339">Linux</span><span class="sxs-lookup"><span data-stu-id="83dbc-339">Linux</span></span>](#tab/linux)

<span data-ttu-id="83dbc-340">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-340">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="83dbc-341">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-341">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="83dbc-342">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-342">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="83dbc-343">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-343">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="83dbc-344">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="83dbc-344">Configuration</span></span>

<span data-ttu-id="83dbc-345">ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft.</span><span class="sxs-lookup"><span data-stu-id="83dbc-345">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="83dbc-346">Integrierte Konfigurationsanbieter gibt es für zahlreiche Quellen wie *.json*-Dateien, *.xml*-Dateien, Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="83dbc-346">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="83dbc-347">Sie können auch benutzerdefinierte Konfigurationsanbieter schreiben.</span><span class="sxs-lookup"><span data-stu-id="83dbc-347">You can also write custom configuration providers.</span></span>

<span data-ttu-id="83dbc-348">Zum Beispiel könnten Sie angeben, dass die Konfiguration aus *appsettings.json* und Umgebungsvariablen stammt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-348">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="83dbc-349">Wenn dann der *ConnectionString*-Wert angefordert wird, sucht das Framework zuerst in der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="83dbc-349">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="83dbc-350">Wenn der Wert sowohl dort als auch in einer Umgebungsvariablen gefunden wird, hat der Wert aus der Umgebungsvariablen Vorrang.</span><span class="sxs-lookup"><span data-stu-id="83dbc-350">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="83dbc-351">Zum Verwalten von vertraulichen Konfigurationsdaten wie Passwörtern bietet ASP.NET Core ein [Geheimnisverwaltungstool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="83dbc-351">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="83dbc-352">Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="83dbc-352">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="83dbc-353">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-353">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="83dbc-354">Optionen</span><span class="sxs-lookup"><span data-stu-id="83dbc-354">Options</span></span>

<span data-ttu-id="83dbc-355">Wo möglich, folgt ASP.NET Core dem *Optionsmuster* zum Speichern und Abrufen von Konfigurationswerten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-355">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="83dbc-356">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-356">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="83dbc-357">Im folgenden Codebeispiel werden WebSockets-Optionen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="83dbc-357">For example, the following code sets WebSockets options:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/UseWebSockets.cs)]

<span data-ttu-id="83dbc-358">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-358">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="83dbc-359">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="83dbc-359">Environments</span></span>

<span data-ttu-id="83dbc-360">Ausführungsumgebungen wie *Entwicklung*, *Staging* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-360">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="83dbc-361">Um die Umgebung anzugeben, in der eine App ausgeführt wird, stellen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable ein.</span><span class="sxs-lookup"><span data-stu-id="83dbc-361">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="83dbc-362">ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IHostingEnvironment`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-362">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="83dbc-363">Das Umgebungsobjekt ist in der gesamten App mithilfe der Dependency Injection verfügbar.</span><span class="sxs-lookup"><span data-stu-id="83dbc-363">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="83dbc-364">Im folgenden Codebeispiel aus der `Startup`-Klasse wird die App so konfiguriert, dass detaillierte Fehlerinformationen nur bereitgestellt werden, wenn die App in der Entwicklung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="83dbc-364">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/samples_snapshot/2.x/StartupConfigure.cs?highlight=3-6)]

<span data-ttu-id="83dbc-365">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-365">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="83dbc-366">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="83dbc-366">Logging</span></span>

<span data-ttu-id="83dbc-367">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-367">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="83dbc-368">Zu den verfügbaren Anbietern gehören folgende:</span><span class="sxs-lookup"><span data-stu-id="83dbc-368">Available providers include the following:</span></span>

* <span data-ttu-id="83dbc-369">Konsole</span><span class="sxs-lookup"><span data-stu-id="83dbc-369">Console</span></span>
* <span data-ttu-id="83dbc-370">Debug</span><span class="sxs-lookup"><span data-stu-id="83dbc-370">Debug</span></span>
* <span data-ttu-id="83dbc-371">Ereignisablaufverfolgung unter Windows</span><span class="sxs-lookup"><span data-stu-id="83dbc-371">Event Tracing on Windows</span></span>
* <span data-ttu-id="83dbc-372">Windows-Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="83dbc-372">Windows Event Log</span></span>
* <span data-ttu-id="83dbc-373">TraceSource</span><span class="sxs-lookup"><span data-stu-id="83dbc-373">TraceSource</span></span>
* <span data-ttu-id="83dbc-374">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="83dbc-374">Azure App Service</span></span>
* <span data-ttu-id="83dbc-375">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="83dbc-375">Azure Application Insights</span></span>

<span data-ttu-id="83dbc-376">Schreiben Sie Protokolle aus jeder Stelle im Code einer App, indem Sie ein `ILogger`-Objekt aus Dependency Injection abrufen und Protokollmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-376">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="83dbc-377">Hier ist ein Beispielcode, in dem ein `ILogger`-Objekt mit Konstruktorinjektion und den hervorgehobenen Aufrufen der Protokollierungsmethode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="83dbc-377">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/samples_snapshot/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="83dbc-378">Mithilfe der `ILogger`-Schnittstelle können Sie eine beliebige Anzahl von Feldern an den Protokollierungsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="83dbc-378">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="83dbc-379">Die Felder werden häufig dazu verwendet, eine Meldungszeichenfolge zu erstellen, der Anbieter kann sie aber auch als einzelne Felder an einen Datenspeicher senden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-379">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="83dbc-380">Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="83dbc-380">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="83dbc-381">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-381">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="83dbc-382">Routing</span><span class="sxs-lookup"><span data-stu-id="83dbc-382">Routing</span></span>

<span data-ttu-id="83dbc-383">Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="83dbc-383">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="83dbc-384">Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware.</span><span class="sxs-lookup"><span data-stu-id="83dbc-384">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="83dbc-385">Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-385">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="83dbc-386">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-386">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="83dbc-387">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="83dbc-387">Error handling</span></span>

<span data-ttu-id="83dbc-388">ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="83dbc-388">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="83dbc-389">eine Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="83dbc-389">A developer exception page</span></span>
* <span data-ttu-id="83dbc-390">benutzerdefinierte Fehlerseiten</span><span class="sxs-lookup"><span data-stu-id="83dbc-390">Custom error pages</span></span>
* <span data-ttu-id="83dbc-391">statische Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="83dbc-391">Static status code pages</span></span>
* <span data-ttu-id="83dbc-392">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="83dbc-392">Startup exception handling</span></span>

<span data-ttu-id="83dbc-393">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-393">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="83dbc-394">Übermitteln von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="83dbc-394">Make HTTP requests</span></span>

<span data-ttu-id="83dbc-395">Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-395">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="83dbc-396">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="83dbc-396">The factory:</span></span>

* <span data-ttu-id="83dbc-397">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-397">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="83dbc-398">Zum Beispiel kann ein *github*-Client für den Zugriff auf GitHub registriert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-398">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="83dbc-399">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-399">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="83dbc-400">Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="83dbc-400">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="83dbc-401">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="83dbc-401">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="83dbc-402">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="83dbc-402">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="83dbc-403">Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="83dbc-403">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="83dbc-404">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="83dbc-404">Manages the pooling and lifetime of underlying `HttpClientHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="83dbc-405">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-405">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="83dbc-406">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-406">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="83dbc-407">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="83dbc-407">Content root</span></span>

<span data-ttu-id="83dbc-408">Der Inhaltsstamm ist der Basispfad zu Folgendem:</span><span class="sxs-lookup"><span data-stu-id="83dbc-408">The content root is the base path to the:</span></span>

* <span data-ttu-id="83dbc-409">Der ausführbaren Datei ( *.exe*), die die App hostet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-409">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="83dbc-410">Kompilierten Assemblys, die die App bilden ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="83dbc-410">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="83dbc-411">Inhaltsdateien ohne Code, die von der App verwendet werden, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="83dbc-411">Non-code content files used by the app, such as:</span></span>
  * Razor<span data-ttu-id="83dbc-412">-Dateien ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-412"> files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="83dbc-413">Konfigurationsdateien ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-413">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="83dbc-414">Datendateien ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-414">Data files (*.db*)</span></span>
* <span data-ttu-id="83dbc-415">[Webstamm](#web-root), in der Regel der veröffentlichte Ordner *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="83dbc-415">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="83dbc-416">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="83dbc-416">During development:</span></span>

* <span data-ttu-id="83dbc-417">Der Inhaltsstamm ist standardmäßig auf das Stammverzeichnis des Projekts festgelegt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-417">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="83dbc-418">Das Stammverzeichnis des Projekts dient zum Erstellen von Folgendem:</span><span class="sxs-lookup"><span data-stu-id="83dbc-418">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="83dbc-419">Pfad zu den Inhaltsdateien ohne Code der App im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="83dbc-419">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="83dbc-420">[Webstamm](#web-root), in der Regel der Ordner *wwwroot* im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="83dbc-420">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="83dbc-421">Ein alternativer Inhaltsstammpfad kann beim [Erstellen des Hosts](#host) angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-421">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="83dbc-422">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-422">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="83dbc-423">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="83dbc-423">Web root</span></span>

<span data-ttu-id="83dbc-424">Der Webstamm ist der Basispfad zu öffentlichen, statischen Ressourcendateien ohne Code, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="83dbc-424">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="83dbc-425">Stylesheets ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-425">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="83dbc-426">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-426">JavaScript (*.js*)</span></span>
* <span data-ttu-id="83dbc-427">Bilder ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="83dbc-427">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="83dbc-428">Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis (samt Unterverzeichnissen) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="83dbc-428">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="83dbc-429">Der Webstammpfad ist standardmäßig auf *{Inhaltsstamm}/wwwroot* festgelegt, doch beim [Erstellen des Hosts](#host) kann ein anderer Webstamm angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="83dbc-429">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="83dbc-430">Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="83dbc-430">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="83dbc-431">Verhindern Sie das Veröffentlichen von Datei in *wwwroot* über [\<Inhalt > Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="83dbc-431">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="83dbc-432">Im folgenden Beispiel wird verhindert, dass Inhalte im *wwwroot/local*-Verzeichnis und in Unterverzeichnisse veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="83dbc-432">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="83dbc-433">In Razor-Dateien ( *.cshtml*) zeigen Tilde und Schrägstrich (`~/`) auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="83dbc-433">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="83dbc-434">Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="83dbc-434">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="83dbc-435">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="83dbc-435">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
