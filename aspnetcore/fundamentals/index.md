---
title: ASP.NET Core – Grundlagen
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen von ASP.NET Core-Apps kennen.
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: fundamentals/index
ms.openlocfilehash: a56beebd796448705c7b84f47699e9739f451419
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099233"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="4282e-103">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="4282e-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="4282e-104">Eine ASP.NET Core-App ist eine Konsolen-App, in deren `Program.Main`-Methode ein Webserver erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4282e-104">An ASP.NET Core app is a console app that creates a web server in its `Program.Main` method.</span></span> <span data-ttu-id="4282e-105">Die `Main`-Methode ist der *verwaltete Einstiegspunkt* der App:</span><span class="sxs-lookup"><span data-stu-id="4282e-105">The `Main` method is the app's *managed entry point*:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs)]

<span data-ttu-id="4282e-106">Der .NET Core-Host:</span><span class="sxs-lookup"><span data-stu-id="4282e-106">The .NET Core Host:</span></span>

* <span data-ttu-id="4282e-107">Lädt die [.NET Core-Runtime](https://github.com/dotnet/coreclr).</span><span class="sxs-lookup"><span data-stu-id="4282e-107">Loads the [.NET Core runtime](https://github.com/dotnet/coreclr).</span></span>
* <span data-ttu-id="4282e-108">Verwendet das erste Argument in der Befehlszeile als Pfad zur verwalteten Binärdatei, die den Einstiegspunkt (`Main`) enthält, und beginnt mit der Ausführung des Codes.</span><span class="sxs-lookup"><span data-stu-id="4282e-108">Uses the first command-line argument as the path to the managed binary that contains the entry point (`Main`) and begins code execution.</span></span>

<span data-ttu-id="4282e-109">Die `Main`-Methode ruft die [WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*)[-Methode auf, die nach dem ](https://wikipedia.org/wiki/Builder_pattern)Builder-Muster einen Webhost erstellt.</span><span class="sxs-lookup"><span data-stu-id="4282e-109">The `Main` method invokes [WebHost.CreateDefaultBuilder](xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*), which follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern) to create a web host.</span></span> <span data-ttu-id="4282e-110">Der Builder verfügt über Methoden, die einen Webserver (z. B. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) und die Startklasse (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>) definieren.</span><span class="sxs-lookup"><span data-stu-id="4282e-110">The builder has methods that define a web server (for example, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) and the startup class (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span></span> <span data-ttu-id="4282e-111">Im obigen Beispiel wird der [Kestrel](xref:fundamentals/servers/kestrel)-Webserver automatisch zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="4282e-111">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is automatically allocated.</span></span> <span data-ttu-id="4282e-112">Wenn IIS verfügbar ist, wird versucht, den Webhost von ASP.NET Core auf [IIS (Internetinformationsdienste)](https://www.iis.net/) auszuführen.</span><span class="sxs-lookup"><span data-stu-id="4282e-112">ASP.NET Core's web host attempts to run on [Internet Information Services (IIS)](https://www.iis.net/), if available.</span></span> <span data-ttu-id="4282e-113">Andere Webserver wie [HTTP.sys](xref:fundamentals/servers/httpsys) können durch den Aufruf der entsprechenden Erweiterungsmethode verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4282e-113">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="4282e-114">`UseStartup` wird im Abschnitt [Start](#startup) näher erläutert.</span><span class="sxs-lookup"><span data-stu-id="4282e-114">`UseStartup` is explained further in the [Startup](#startup) section.</span></span>

<span data-ttu-id="4282e-115">Der Rückgabetyp <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> des `WebHost.CreateDefaultBuilder`-Aufrufs stellt viele optionale Methoden zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="4282e-115"><xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder>, the return type of the `WebHost.CreateDefaultBuilder` invocation, provides many optional methods.</span></span> <span data-ttu-id="4282e-116">Zu diesen Methoden gehören `UseHttpSys` für das Hosten der Anwendung in HTTP.sys und <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> für das Festlegen des Stamminhaltsverzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="4282e-116">Some of these methods include `UseHttpSys` for hosting the app in HTTP.sys and <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> for specifying the root content directory.</span></span> <span data-ttu-id="4282e-117">Mit den Methoden <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> und <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> wird das <xref:Microsoft.AspNetCore.Hosting.IWebHost>-Objekt erstellt, das die Anwendung hostet und auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="4282e-117">The <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> and <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> methods build the <xref:Microsoft.AspNetCore.Hosting.IWebHost> object that hosts the app and begins listening for HTTP requests.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs)]

<span data-ttu-id="4282e-118">Der .NET Core-Host:</span><span class="sxs-lookup"><span data-stu-id="4282e-118">The .NET Core Host:</span></span>

* <span data-ttu-id="4282e-119">Lädt die [.NET Core-Runtime](https://github.com/dotnet/coreclr).</span><span class="sxs-lookup"><span data-stu-id="4282e-119">Loads the [.NET Core runtime](https://github.com/dotnet/coreclr).</span></span>
* <span data-ttu-id="4282e-120">Verwendet das erste Argument in der Befehlszeile als Pfad zur verwalteten Binärdatei, die den Einstiegspunkt (`Main`) enthält, und beginnt mit der Ausführung des Codes.</span><span class="sxs-lookup"><span data-stu-id="4282e-120">Uses the first command-line argument as the path to the managed binary that contains the entry point (`Main`) and begins code execution.</span></span>

<span data-ttu-id="4282e-121">Die `Main`-Methode verwendet eine Instanz von <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, die nach dem [Builder-Muster](https://wikipedia.org/wiki/Builder_pattern) einen Web-App-Host erstellt.</span><span class="sxs-lookup"><span data-stu-id="4282e-121">The `Main` method uses <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder>, which follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern) to create a web app host.</span></span> <span data-ttu-id="4282e-122">Der Builder verfügt über Methoden, die den Webserver (z.B. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) und die Startklasse (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>) definieren.</span><span class="sxs-lookup"><span data-stu-id="4282e-122">The builder has methods that define the web server (for example, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>) and the startup class (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*>).</span></span> <span data-ttu-id="4282e-123">Im obigen Beispiel wird der [Kestrel](xref:fundamentals/servers/kestrel)-Webserver verwendet.</span><span class="sxs-lookup"><span data-stu-id="4282e-123">In the preceding example, the [Kestrel](xref:fundamentals/servers/kestrel) web server is used.</span></span> <span data-ttu-id="4282e-124">Andere Webserver wie [HTTP.sys](xref:fundamentals/servers/httpsys) können durch den Aufruf der entsprechenden Erweiterungsmethode verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="4282e-124">Other web servers, such as [HTTP.sys](xref:fundamentals/servers/httpsys), can be used by invoking the appropriate extension method.</span></span> <span data-ttu-id="4282e-125">`UseStartup` wird im Abschnitt [Start](#startup) näher erläutert.</span><span class="sxs-lookup"><span data-stu-id="4282e-125">`UseStartup` is explained further in the [Startup](#startup) section.</span></span>

<span data-ttu-id="4282e-126">`WebHostBuilder` stellt viele optionale Methoden wie z.B. <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> für das Hosten in IIS und IIS Express und <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> für das Festlegen des Inhaltsstammverzeichnisses zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="4282e-126">`WebHostBuilder` provides many optional methods, including <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> for hosting in IIS and IIS Express and <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseContentRoot*> for specifying the root content directory.</span></span> <span data-ttu-id="4282e-127">Mit den Methoden <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> und <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> wird das <xref:Microsoft.AspNetCore.Hosting.IWebHost>-Objekt erstellt, das die Anwendung hostet und auf HTTP-Anforderungen lauscht.</span><span class="sxs-lookup"><span data-stu-id="4282e-127">The <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder.Build*> and <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> methods build the <xref:Microsoft.AspNetCore.Hosting.IWebHost> object that hosts the app and begins listening for HTTP requests.</span></span>

::: moniker-end

## <a name="startup"></a><span data-ttu-id="4282e-128">Start</span><span class="sxs-lookup"><span data-stu-id="4282e-128">Startup</span></span>

<span data-ttu-id="4282e-129">Die `UseStartup`-Methode in `WebHostBuilder` gibt die `Startup`-Klasse für Ihre Anwendung an:</span><span class="sxs-lookup"><span data-stu-id="4282e-129">The `UseStartup` method on `WebHostBuilder` specifies the `Startup` class for your app:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Program.cs?highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Program.cs?highlight=7)]

::: moniker-end

<span data-ttu-id="4282e-130">In der `Startup`-Klasse werden alle von der Anwendung benötigten Dienste konfiguriert. Außerdem wird in dieser Klasse die Pipeline zur Anforderungsverarbeitung definiert.</span><span class="sxs-lookup"><span data-stu-id="4282e-130">The `Startup` class is where any services required by the app are configured and the request handling pipeline is defined.</span></span> <span data-ttu-id="4282e-131">Die `Startup`-Klasse muss öffentlich sein und enthält üblicherweise die folgenden Methoden.</span><span class="sxs-lookup"><span data-stu-id="4282e-131">The `Startup` class must be public and usually contains the following methods.</span></span> <span data-ttu-id="4282e-132">`Startup.ConfigureServices` ist optional.</span><span class="sxs-lookup"><span data-stu-id="4282e-132">`Startup.ConfigureServices` is optional.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](index/snapshots/2.x/Startup.cs)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](index/snapshots/1.x/Startup.cs)]

::: moniker-end

<span data-ttu-id="4282e-133"><xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> definiert die [Dienste](#dependency-injection-services) Ihrer Anwendung (z.B. ASP.NET Core MVC, Entity Framework Core, Identity).</span><span class="sxs-lookup"><span data-stu-id="4282e-133"><xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> defines the [Services](#dependency-injection-services) used by your app (for example, ASP.NET Core MVC, Entity Framework Core, Identity).</span></span> <span data-ttu-id="4282e-134"><xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> definiert die [Middleware](xref:fundamentals/middleware/index), die in der Anforderungspipeline aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="4282e-134"><xref:Microsoft.AspNetCore.Hosting.IStartup.Configure*> defines the [middleware](xref:fundamentals/middleware/index) called in the request pipeline.</span></span>

<span data-ttu-id="4282e-135">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="4282e-135">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="content-root"></a><span data-ttu-id="4282e-136">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="4282e-136">Content root</span></span>

<span data-ttu-id="4282e-137">Das Inhaltsstammverzeichnis ist der Basispfad zu allen von der Anwendung verwendeten Inhalten wie, [Razor Pages](xref:razor-pages/index), MVC-Ansichten und statischen Objekten.</span><span class="sxs-lookup"><span data-stu-id="4282e-137">The content root is the base path to any content used by the app, such as [Razor Pages](xref:razor-pages/index), MVC views, and static assets.</span></span> <span data-ttu-id="4282e-138">Standardmäßig entspricht das Inhaltsstammverzeichnis dem App-Basispfad der ausführbaren Datei, mit der die App gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="4282e-138">By default, the content root is the same location as the app base path for the executable hosting the app.</span></span>

## <a name="web-root-webroot"></a><span data-ttu-id="4282e-139">Webstamm (webroot)</span><span class="sxs-lookup"><span data-stu-id="4282e-139">Web root (webroot)</span></span>

<span data-ttu-id="4282e-140">Das Webstammverzeichnis einer Anwendung ist das Projektverzeichnis, in dem sich öffentliche statische Ressourcen wie etwa CSS-, JavaScript- und Imagedateien befinden.</span><span class="sxs-lookup"><span data-stu-id="4282e-140">The webroot of an app is the directory in the project containing public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="4282e-141">*wwwroot* ist standardmäßig der Webstamm.</span><span class="sxs-lookup"><span data-stu-id="4282e-141">By default, *wwwroot* is the webroot.</span></span>

<span data-ttu-id="4282e-142">Für Razor-Dateien (*.cshtml*) zeigen die Tilde und der Schrägstrich `~/` auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="4282e-142">For Razor (*.cshtml*) files, the tilde-slash  `~/` points to the webroot.</span></span> <span data-ttu-id="4282e-143">Pfade, die mit `~/` beginnen, werden als virtuelle Pfade bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="4282e-143">Paths beginning with `~/` are referred to as virtual paths.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="4282e-144">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="4282e-144">Dependency injection (services)</span></span>

<span data-ttu-id="4282e-145">Ein *Dienst* ist eine Komponente, die für die gemeinsame Nutzung in einer App vorgesehen ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-145">A *service* is a component that's intended for common consumption in an app.</span></span> <span data-ttu-id="4282e-146">Dienste werden über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) zur Verfügung gestellt.</span><span class="sxs-lookup"><span data-stu-id="4282e-146">Services are made available through [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="4282e-147">ASP.NET Core enthält einen nativen IoC-Container (Inversion of Control, Steuerungsumkehr), der die [Konstruktorinjektion](xref:mvc/controllers/dependency-injection#constructor-injection) standardmäßig unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4282e-147">ASP.NET Core includes a native Inversion of Control (IoC) container that supports [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) by default.</span></span> <span data-ttu-id="4282e-148">Bei Bedarf können Sie den Standardcontainer durch einen anderen ersetzen.</span><span class="sxs-lookup"><span data-stu-id="4282e-148">You can replace the default container if you wish.</span></span> <span data-ttu-id="4282e-149">Neben der [losen Kopplung](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation) besteht ein weiterer Vorteil darin, dass von der DI Dienste in der gesamten App zur Verfügung gestellt werden (z.B. die [Protokollierung](xref:fundamentals/logging/index)).</span><span class="sxs-lookup"><span data-stu-id="4282e-149">In addition to its [loose coupling benefit](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#encapsulation), DI makes services, such as [logging](xref:fundamentals/logging/index), available throughout your app.</span></span>

<span data-ttu-id="4282e-150">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="4282e-150">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="4282e-151">Middleware</span><span class="sxs-lookup"><span data-stu-id="4282e-151">Middleware</span></span>

<span data-ttu-id="4282e-152">In ASP.NET Core erstellen Sie Ihre Anforderungspipeline mithilfe von [Middleware](xref:fundamentals/middleware/index).</span><span class="sxs-lookup"><span data-stu-id="4282e-152">In ASP.NET Core, you compose your request pipeline using [middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="4282e-153">ASP.NET Core-Middleware führt asynchrone Operationen im `HttpContext` aus und ruft anschließend die nächste Middlewareanwendung in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="4282e-153">ASP.NET Core middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="4282e-154">Eine Middlewarekomponente mit dem Namen „XYZ“ wird gemäß der Konvention durch den Aufruf einer `UseXYZ`-Erweiterungsmethode in der `Configure`-Methode der Pipeline hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4282e-154">By convention, a middleware component called "XYZ" is added to the pipeline by invoking a `UseXYZ` extension method in the `Configure` method.</span></span>

<span data-ttu-id="4282e-155">ASP.NET Core enthält eine Reihe umfangreicher Middleware, und außerdem können Sie benutzerdefinierte Middleware erstellen.</span><span class="sxs-lookup"><span data-stu-id="4282e-155">ASP.NET Core includes a rich set of built-in middleware, and you can write your own custom middleware.</span></span> <span data-ttu-id="4282e-156">[Open Web Interface for .NET (OWIN)](xref:fundamentals/owin) ermöglicht Web-Apps das Entkoppeln von Webservern und wird in ASP.NET Core unterstützt.</span><span class="sxs-lookup"><span data-stu-id="4282e-156">[Open Web Interface for .NET (OWIN)](xref:fundamentals/owin), which allows web apps to be decoupled from web servers, is supported in ASP.NET Core apps.</span></span>

<span data-ttu-id="4282e-157">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index> und <xref:fundamentals/owin>.</span><span class="sxs-lookup"><span data-stu-id="4282e-157">For more information, see <xref:fundamentals/middleware/index> and <xref:fundamentals/owin>.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="initiate-http-requests"></a><span data-ttu-id="4282e-158">Initiieren von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="4282e-158">Initiate HTTP requests</span></span>

<span data-ttu-id="4282e-159"><xref:System.Net.Http.IHttpClientFactory> ist für den Zugriff auf <xref:System.Net.Http.HttpClient>-Instanzen verfügbar, um HTTP-Anforderung durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="4282e-159"><xref:System.Net.Http.IHttpClientFactory> is available to access <xref:System.Net.Http.HttpClient> instances to make HTTP requests.</span></span>

<span data-ttu-id="4282e-160">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="4282e-160">For more information, see <xref:fundamentals/http-requests>.</span></span>

::: moniker-end

## <a name="environments"></a><span data-ttu-id="4282e-161">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="4282e-161">Environments</span></span>

<span data-ttu-id="4282e-162">Umgebungen wie *Entwicklung* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung und können über Umgebungsvariablen, die Einstellungsdatei sowie das Befehlszeilenargument festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="4282e-162">Environments, such as *Development* and *Production*, are a first-class notion in ASP.NET Core and can be set using an environment variable, settings file, and command-line argument.</span></span>

<span data-ttu-id="4282e-163">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="4282e-163">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="hosting"></a><span data-ttu-id="4282e-164">Hosting</span><span class="sxs-lookup"><span data-stu-id="4282e-164">Hosting</span></span>

<span data-ttu-id="4282e-165">ASP.NET Core-Apps konfigurieren und starten einen *Host*, der für das Starten der App und das Verwalten deren Lebensdauer verantwortlich ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-165">ASP.NET Core apps configure and launch a *host*, which is responsible for app startup and lifetime management.</span></span>

<span data-ttu-id="4282e-166">Weitere Informationen finden Sie unter <xref:fundamentals/host/index>.</span><span class="sxs-lookup"><span data-stu-id="4282e-166">For more information, see <xref:fundamentals/host/index>.</span></span>

## <a name="servers"></a><span data-ttu-id="4282e-167">Server</span><span class="sxs-lookup"><span data-stu-id="4282e-167">Servers</span></span>

<span data-ttu-id="4282e-168">Das Hostingmodell von ASP.NET Core lauscht nicht direkt auf Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="4282e-168">The ASP.NET Core hosting model doesn't directly listen for requests.</span></span> <span data-ttu-id="4282e-169">Das Hostingmodell ist darauf angewiesen, dass eine HTTP-Serverimplementierung die Anforderungen an die App weiterleitet.</span><span class="sxs-lookup"><span data-stu-id="4282e-169">The hosting model relies on an HTTP server implementation to forward the request to the app.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="4282e-170">Windows</span><span class="sxs-lookup"><span data-stu-id="4282e-170">Windows</span></span>](#tab/windows)

<span data-ttu-id="4282e-171">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="4282e-171">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="4282e-172">[Kestrel](xref:fundamentals/servers/kestrel) Server ist ein verwalteter, plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="4282e-172">[Kestrel](xref:fundamentals/servers/kestrel) server is a managed, cross-platform web server.</span></span> <span data-ttu-id="4282e-173">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4282e-173">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="4282e-174">Kestrel kann ebenfalls als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet in ASP.NET Core 2.0 oder höher verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-174">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="4282e-175">IIS-HTTP-Server (`IISHttpServer`) ist ein [In-Process-Server](xref:fundamentals/servers/index#in-process-hosting-model) für IIS.</span><span class="sxs-lookup"><span data-stu-id="4282e-175">IIS HTTP Server (`IISHttpServer`) is an [in-process server](xref:fundamentals/servers/index#in-process-hosting-model) for IIS.</span></span>
* <span data-ttu-id="4282e-176">[HTTP.sys](xref:fundamentals/servers/httpsys) Server ist ein Webserver für ASP.NET Core unter Windows.</span><span class="sxs-lookup"><span data-stu-id="4282e-176">[HTTP.sys](xref:fundamentals/servers/httpsys) server is a web server for ASP.NET Core on Windows.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="4282e-177">macOS</span><span class="sxs-lookup"><span data-stu-id="4282e-177">macOS</span></span>](#tab/macos)

<span data-ttu-id="4282e-178">ASP.NET Core verwendet die [Kestrel](xref:fundamentals/servers/kestrel)-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="4282e-178">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="4282e-179">Kestrel ist ein verwalteter, plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="4282e-179">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="4282e-180">Kestrel kann ebenfalls als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet in ASP.NET Core 2.0 oder höher verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-180">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="4282e-181">Linux</span><span class="sxs-lookup"><span data-stu-id="4282e-181">Linux</span></span>](#tab/linux)

<span data-ttu-id="4282e-182">ASP.NET Core verwendet die [Kestrel](xref:fundamentals/servers/kestrel)-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="4282e-182">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="4282e-183">Kestrel ist ein verwalteter, plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="4282e-183">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="4282e-184">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4282e-184">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="4282e-185">Kestrel kann ebenfalls als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet in ASP.NET Core 2.0 oder höher verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-185">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="4282e-186">Windows</span><span class="sxs-lookup"><span data-stu-id="4282e-186">Windows</span></span>](#tab/windows)

<span data-ttu-id="4282e-187">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="4282e-187">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="4282e-188">[Kestrel](xref:fundamentals/servers/kestrel) Server ist ein verwalteter, plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="4282e-188">[Kestrel](xref:fundamentals/servers/kestrel) server is a managed, cross-platform web server.</span></span> <span data-ttu-id="4282e-189">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4282e-189">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="4282e-190">Kestrel kann ebenfalls als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet in ASP.NET Core 2.0 oder höher verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-190">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="4282e-191">[HTTP.sys](xref:fundamentals/servers/httpsys) Server ist ein Webserver für ASP.NET Core unter Windows.</span><span class="sxs-lookup"><span data-stu-id="4282e-191">[HTTP.sys](xref:fundamentals/servers/httpsys) server is a web server for ASP.NET Core on Windows.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="4282e-192">macOS</span><span class="sxs-lookup"><span data-stu-id="4282e-192">macOS</span></span>](#tab/macos)

<span data-ttu-id="4282e-193">ASP.NET Core verwendet die [Kestrel](xref:fundamentals/servers/kestrel)-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="4282e-193">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="4282e-194">Kestrel ist ein verwalteter, plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="4282e-194">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="4282e-195">Kestrel kann ebenfalls als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet in ASP.NET Core 2.0 oder höher verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-195">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="4282e-196">Linux</span><span class="sxs-lookup"><span data-stu-id="4282e-196">Linux</span></span>](#tab/linux)

<span data-ttu-id="4282e-197">ASP.NET Core verwendet die [Kestrel](xref:fundamentals/servers/kestrel)-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="4282e-197">ASP.NET Core uses the [Kestrel](xref:fundamentals/servers/kestrel) server implementation.</span></span> <span data-ttu-id="4282e-198">Kestrel ist ein verwalteter, plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="4282e-198">Kestrel is a managed, cross-platform web server.</span></span> <span data-ttu-id="4282e-199">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="4282e-199">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="4282e-200">Kestrel kann ebenfalls als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet in ASP.NET Core 2.0 oder höher verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="4282e-200">Kestrel can also be run as a public-facing edge server exposed directly to the Internet in ASP.NET Core 2.0 or later.</span></span>

---

::: moniker-end

<span data-ttu-id="4282e-201">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="4282e-201">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="4282e-202">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="4282e-202">Configuration</span></span>

<span data-ttu-id="4282e-203">ASP.NET Core verwendet ein Konfigurationsmodell, das auf Name/Wert-Paaren basiert.</span><span class="sxs-lookup"><span data-stu-id="4282e-203">ASP.NET Core uses a configuration model based on name-value pairs.</span></span> <span data-ttu-id="4282e-204">Das Konfigurationsmodell basiert weder auf <xref:System.Configuration> noch auf *web.config*. Die Konfiguration erhält Einstellungen von einer geordneten Menge an Konfigurationsanbietern.</span><span class="sxs-lookup"><span data-stu-id="4282e-204">The configuration model isn't based on <xref:System.Configuration> or *web.config*. Configuration obtains settings from an ordered set of configuration providers.</span></span> <span data-ttu-id="4282e-205">Die integrierten Konfigurationsanbieter unterstützen zahlreiche Dateiformate (XML, JSON, INI), Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="4282e-205">The built-in configuration providers support a variety of file formats (XML, JSON, INI), environment variables, and command-line arguments.</span></span> <span data-ttu-id="4282e-206">Sie können auch benutzerdefinierte Konfigurationsanbieter erstellen.</span><span class="sxs-lookup"><span data-stu-id="4282e-206">You can also write your own custom configuration providers.</span></span>

<span data-ttu-id="4282e-207">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="4282e-207">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="logging"></a><span data-ttu-id="4282e-208">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="4282e-208">Logging</span></span>

<span data-ttu-id="4282e-209">ASP.NET Core unterstützt eine Protokollierungs-API, die mit mehreren verschiedenen Protokollanbietern funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4282e-209">ASP.NET Core supports a Logging API that works with a variety of logging providers.</span></span> <span data-ttu-id="4282e-210">Integrierte Anbieter unterstützen das Senden von Protokollen an mindestens einen Zielanbieter.</span><span class="sxs-lookup"><span data-stu-id="4282e-210">Built-in providers support sending logs to one or more destinations.</span></span> <span data-ttu-id="4282e-211">Protokollierungsframeworks von Drittanbietern sind ebenso zulässig.</span><span class="sxs-lookup"><span data-stu-id="4282e-211">Third-party logging frameworks can be used.</span></span>

<span data-ttu-id="4282e-212">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="4282e-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="4282e-213">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="4282e-213">Error handling</span></span>

<span data-ttu-id="4282e-214">ASP.NET Core verfügt über integrierte Szenarios zur Fehlerbehandlung in Apps. Dazu zählen die Ausnahmenseite für Entwickler, Seiten für benutzerdefinierte Fehler, Seiten für statischen Statuscode sowie die Startausnahmebehandlung.</span><span class="sxs-lookup"><span data-stu-id="4282e-214">ASP.NET Core has built-in scenarios for handling errors in apps, including a developer exception page, custom error pages, static status code pages, and startup exception handling.</span></span>

<span data-ttu-id="4282e-215">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="4282e-215">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="routing"></a><span data-ttu-id="4282e-216">Routing</span><span class="sxs-lookup"><span data-stu-id="4282e-216">Routing</span></span>

<span data-ttu-id="4282e-217">ASP.NET Core verfügt über Szenarios zum Routing von App-Anforderung an Routenhandler.</span><span class="sxs-lookup"><span data-stu-id="4282e-217">ASP.NET Core offers scenarios for routing of app requests to route handlers.</span></span>

<span data-ttu-id="4282e-218">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="4282e-218">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="background-tasks"></a><span data-ttu-id="4282e-219">Hintergrundaufgaben</span><span class="sxs-lookup"><span data-stu-id="4282e-219">Background tasks</span></span>

<span data-ttu-id="4282e-220">Hintergrundaufgaben werden als *gehostete Dienste* implementiert.</span><span class="sxs-lookup"><span data-stu-id="4282e-220">Background tasks are implemented as *hosted services*.</span></span> <span data-ttu-id="4282e-221">Ein gehosteter Dienst ist eine Klasse mit Logik für Hintergrundaufgaben, die die Schnittstelle <xref:Microsoft.Extensions.Hosting.IHostedService> implementiert.</span><span class="sxs-lookup"><span data-stu-id="4282e-221">A hosted service is a class with background task logic that implements the <xref:Microsoft.Extensions.Hosting.IHostedService> interface.</span></span>

<span data-ttu-id="4282e-222">Weitere Informationen finden Sie unter <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="4282e-222">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="access-httpcontext"></a><span data-ttu-id="4282e-223">Zugriff auf HttpContext</span><span class="sxs-lookup"><span data-stu-id="4282e-223">Access HttpContext</span></span>

<span data-ttu-id="4282e-224">`HttpContext` ist automatisch verfügbar, wenn Anforderungen mit Razor Pages und MVC verarbeitet werden.</span><span class="sxs-lookup"><span data-stu-id="4282e-224">`HttpContext` is automatically available when processing requests with Razor Pages and MVC.</span></span> <span data-ttu-id="4282e-225">In Fällen, in denen `HttpContext` nicht unmittelbar verfügbar ist, können Sie auf `HttpContext` über die <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor>-Schnittstelle und deren Standardimplementierung, <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>, zugreifen.</span><span class="sxs-lookup"><span data-stu-id="4282e-225">In circumstances where `HttpContext` isn't readily available, you can access the `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation, <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span>

<span data-ttu-id="4282e-226">Weitere Informationen finden Sie unter <xref:fundamentals/httpcontext>.</span><span class="sxs-lookup"><span data-stu-id="4282e-226">For more information, see <xref:fundamentals/httpcontext>.</span></span>
