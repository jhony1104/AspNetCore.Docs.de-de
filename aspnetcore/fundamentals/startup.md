---
title: Anwendungsstart in ASP.NET Core
author: rick-anderson
description: Informationen zur Funktionsweise der Startklasse in ASP.NET Core bei der Konfiguration von Diensten und der Anforderungspipeline einer App.
ms.author: riande
ms.custom: mvc
ms.date: 8/7/2019
uid: fundamentals/startup
ms.openlocfilehash: 9407de4ee91ba43b2c95fa98f0cf479bf8539cab
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310499"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="8af83-103">Anwendungsstart in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8af83-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="8af83-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex) und [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="8af83-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="8af83-105">Die `Startup`-Klasse konfiguriert Dienste und die Anforderungspipeline einer App.</span><span class="sxs-lookup"><span data-stu-id="8af83-105">The `Startup` class configures services and the app's request pipeline.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="8af83-106">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="8af83-106">The Startup class</span></span>

<span data-ttu-id="8af83-107">ASP.NET Core-Apps verwenden eine `Startup`-Klasse, die standardmäßig `Startup` genannt wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="8af83-108">Die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8af83-108">The `Startup` class:</span></span>

* <span data-ttu-id="8af83-109">Kann eine <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>-Methode enthalten, um die *Dienste* der App zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8af83-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="8af83-110">Ein Dienst ist eine wiederverwendbare Komponente, die App-Funktionalität bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="8af83-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="8af83-111">Dienste werden in `ConfigureServices` konfiguriert, was auch als *registrieren* bezeichnet wird, und in der App über [Dependency Injection](xref:fundamentals/dependency-injection) oder <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> genutzt.</span><span class="sxs-lookup"><span data-stu-id="8af83-111">Services are configured&mdash;also described as *registered*&mdash;in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="8af83-112">Enthält eine <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>-Methode, um die Anforderungsverarbeitungspipeline einer App zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8af83-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="8af83-113">`ConfigureServices` und `Configure` werden von der ASP.NET Core-Runtime aufgerufen, wenn die App gestartet wird:</span><span class="sxs-lookup"><span data-stu-id="8af83-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="8af83-114">Das Beispiel oben bezieht sich auf [Razor Pages](xref:razor-pages/index), die MVC-Version ist aber ähnlich.</span><span class="sxs-lookup"><span data-stu-id="8af83-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

::: moniker-end

<span data-ttu-id="8af83-115">Die `Startup`-Klasse wird angegeben, wenn der [Host](xref:fundamentals/index#host) der App erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="8af83-116">Die `Startup`-Klasse wird in der Regel durch Aufrufen der [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*)-Methode für den Hostgenerator angegeben:</span><span class="sxs-lookup"><span data-stu-id="8af83-116">The `Startup` class is typically specified by calling the [`WebHostBuilderExtensions.UseStartup<TStartup>`](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="8af83-117">Der Host stellt Dienste für den `Startup`-Klassenkonstruktor bereit.</span><span class="sxs-lookup"><span data-stu-id="8af83-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="8af83-118">Die App fügt über `ConfigureServices` zusätzliche Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="8af83-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="8af83-119">Sowohl die Host- als auch die App-Dienste sind in `Configure` und über die App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8af83-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="8af83-120">Bei Verwendung von <xref:Microsoft.Extensions.Hosting.IHostBuilder> können nur die folgenden Diensttypen in den `Startup`-Konstruktor eingefügt werden:</span><span class="sxs-lookup"><span data-stu-id="8af83-120">Only the following service types can be injected into the `Startup` constructor when using <xref:Microsoft.Extensions.Hosting.IHostBuilder>:</span></span>

* `IWebHostEnvironment`
* `IHostEnvironment`
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="8af83-121">Die meisten Dienste sind erst verfügbar, wenn die `Configure`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-121">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8af83-122">Der Host stellt Dienste für den `Startup`-Klassenkonstruktor bereit.</span><span class="sxs-lookup"><span data-stu-id="8af83-122">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="8af83-123">Die App fügt über `ConfigureServices` zusätzliche Dienste hinzu.</span><span class="sxs-lookup"><span data-stu-id="8af83-123">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="8af83-124">Daher sind sowohl die Host- als auch die App-Dienste in `Configure` und über die App verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8af83-124">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="8af83-125">[Dependency Injection](xref:fundamentals/dependency-injection) wird häufig im Zusammenhang mit der `Startup`-Klasse verwendet, um Folgendes einzufügen:</span><span class="sxs-lookup"><span data-stu-id="8af83-125">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="8af83-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>, um Dienste nach Umgebung zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8af83-126"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="8af83-127"><xref:Microsoft.Extensions.Configuration.IConfiguration>, um Konfigurationen zu lesen.</span><span class="sxs-lookup"><span data-stu-id="8af83-127"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="8af83-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory>, um eine Protokollierung in `Startup.ConfigureServices` zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8af83-128"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="8af83-129">Die meisten Dienste sind erst verfügbar, wenn die `Configure`-Methode aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-129">Most services are not available until the `Configure` method is called.</span></span>

::: moniker-end

### <a name="multiple-startup"></a><span data-ttu-id="8af83-130">Mehrere StartUp-Klassen</span><span class="sxs-lookup"><span data-stu-id="8af83-130">Multiple StartUp</span></span>

<span data-ttu-id="8af83-131">Wenn die App unterschiedliche `Startup`-Klassen für unterschiedliche Umgebungen definiert (z.B. `StartupDevelopment`), wird zur Laufzeit die entsprechende `Startup`-Klasse ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="8af83-131">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="8af83-132">Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert.</span><span class="sxs-lookup"><span data-stu-id="8af83-132">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="8af83-133">Wenn die App in der Entwicklungsumgebung ausgeführt wird und sowohl eine `Startup`-Klasse als auch eine `StartupDevelopment`-Klasse enthält, wird die `StartupDevelopment`-Klasse verwendet.</span><span class="sxs-lookup"><span data-stu-id="8af83-133">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="8af83-134">Weitere Informationen finden Sie unter [Verwenden mehrerer Umgebungen](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="8af83-134">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="8af83-135">Weitere Informationen zum Host finden Sie unter [Der Host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8af83-135">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="8af83-136">Weitere Informationen zum Umgang mit Fehlern beim Start finden Sie unter [Startup exception handling (Umgang mit Ausnahmen beim Start)](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="8af83-136">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="8af83-137">Die ConfigureServices-Methode</span><span class="sxs-lookup"><span data-stu-id="8af83-137">The ConfigureServices method</span></span>

<span data-ttu-id="8af83-138">Die <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>-Methode:</span><span class="sxs-lookup"><span data-stu-id="8af83-138">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="8af83-139">Optional.</span><span class="sxs-lookup"><span data-stu-id="8af83-139">Optional.</span></span>
* <span data-ttu-id="8af83-140">wird vor der `Configure`-Methode vom Host aufgerufen, um die App-Dienste zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8af83-140">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="8af83-141">Über diese Methode werden standardmäßig [Konfigurationsoptionen](xref:fundamentals/configuration/index) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8af83-141">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="8af83-142">Es kann sein, dass der Host einige Dienste konfiguriert, bevor die `Startup`-Methoden aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="8af83-142">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="8af83-143">Weitere Informationen finden Sie unter [Der Host](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="8af83-143">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="8af83-144">Für Features, die ein umfangreiches Setup erfordern, sind in <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> `Add{Service}`-Erweiterungsmethoden verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8af83-144">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="8af83-145">Beispiel: **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores und **Add**RazorPages:</span><span class="sxs-lookup"><span data-stu-id="8af83-145">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

::: moniker-end

<span data-ttu-id="8af83-146">Wenn Sie Dienste zum Dienstcontainer hinzufügen, können Sie auch über die App und die `Configure`-Methode auf diese zugreifen.</span><span class="sxs-lookup"><span data-stu-id="8af83-146">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="8af83-147">Die Dienste werden über die [Dependency Injection](xref:fundamentals/dependency-injection) oder über <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="8af83-147">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8af83-148">Unter [SetCompatibilityVersion](xref:mvc/compatibility-version) finden Sie weitere Informationen zu `SetCompatibilityVersion`.</span><span class="sxs-lookup"><span data-stu-id="8af83-148">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

::: moniker-end

## <a name="the-configure-method"></a><span data-ttu-id="8af83-149">Die Configure-Methode</span><span class="sxs-lookup"><span data-stu-id="8af83-149">The Configure method</span></span>

<span data-ttu-id="8af83-150">Die <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>-Methode wird verwendet, um festzulegen, wie die App auf HTTP-Anforderungen reagiert.</span><span class="sxs-lookup"><span data-stu-id="8af83-150">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="8af83-151">Sie können die Anforderungspipeline konfigurieren, indem Sie [Middlewarekomponenten](xref:fundamentals/middleware/index) zu einer <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>-Instanz hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8af83-151">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="8af83-152">Die `Configure`-Methode kann auf `IApplicationBuilder` zugreifen, wird aber nicht im Dienstcontainer registriert.</span><span class="sxs-lookup"><span data-stu-id="8af83-152">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="8af83-153">Über das Hosting wird ein `IApplicationBuilder` erstellt, der direkt an `Configure` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-153">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="8af83-154">Die [ASP.NET Core-Vorlagen](/dotnet/core/tools/dotnet-new) konfigurieren die Pipeline mit Unterstützung für:</span><span class="sxs-lookup"><span data-stu-id="8af83-154">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="8af83-155">die Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="8af83-155">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="8af83-156">Ausnahmehandler</span><span class="sxs-lookup"><span data-stu-id="8af83-156">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="8af83-157">HTTP Strict Transport Security (HSTS)</span><span class="sxs-lookup"><span data-stu-id="8af83-157">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="8af83-158">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="8af83-158">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="8af83-159">Statische Dateien</span><span class="sxs-lookup"><span data-stu-id="8af83-159">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="8af83-160">ASP.NET Core-[MVC](xref:mvc/overview) und [Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="8af83-160">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>

::: moniker range="< aspnetcore-3.0"

* [<span data-ttu-id="8af83-161">die Datenschutzgrundverordnung (DSGVO)</span><span class="sxs-lookup"><span data-stu-id="8af83-161">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="8af83-162">Das Beispiel oben bezieht sich auf [Razor Pages](xref:razor-pages/index), die MVC-Version ist aber ähnlich.</span><span class="sxs-lookup"><span data-stu-id="8af83-162">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

::: moniker-end

<span data-ttu-id="8af83-163">Jede `Use`-Erweiterungsmethode fügt mindestens eine Middlewarekomponente zu der Anforderungspipeline hinzu.</span><span class="sxs-lookup"><span data-stu-id="8af83-163">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="8af83-164">Beispielsweise konfiguriert <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> [Middleware](xref:fundamentals/middleware/index) für die Bereitstellung [statischer Dateien](xref:fundamentals/static-files).</span><span class="sxs-lookup"><span data-stu-id="8af83-164">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="8af83-165">Jede Middlewarekomponente in der Anforderungspipeline ist für das Aufrufen der jeweils nächsten Komponente in der Pipeline oder, wenn nötig, für das Kurzschließen der Kette zuständig.</span><span class="sxs-lookup"><span data-stu-id="8af83-165">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8af83-166">In der `Configure`-Methodensignatur können außerdem auch zusätzliche Dienste wie `IWebHostEnvironment`, `ILoggerFactory` oder in `ConfigureServices` definierte Dienste angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8af83-166">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="8af83-167">Diese Dienste werden eingefügt, wenn Sie verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="8af83-167">These services are injected if they're available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8af83-168">In der `Configure`-Methodensignatur können außerdem auch zusätzliche Dienste wie `IHostingEnvironment` und `ILoggerFactory` oder in `ConfigureServices` definierte Dienste angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8af83-168">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="8af83-169">Diese Dienste werden eingefügt, wenn Sie verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="8af83-169">These services are injected if they're available.</span></span>

::: moniker-end

<span data-ttu-id="8af83-170">Weitere Informationen zur Verwendung von `IApplicationBuilder` und der Reihenfolge der Middlewareverarbeitung finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="8af83-170">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="8af83-171">Konfigurieren von Diensten ohne Start</span><span class="sxs-lookup"><span data-stu-id="8af83-171">Configure services without Startup</span></span>

<span data-ttu-id="8af83-172">Rufen Sie die Hilfsmethoden `ConfigureServices` und `Configure` im Hostgenerator auf, um Dienste und die Anforderungsverarbeitungspipeline ohne die `Startup`-Klasse zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8af83-172">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="8af83-173">Wenn `ConfigureServices` mehrmals aufgerufen wird, werden diese Aufrufe einander angefügt.</span><span class="sxs-lookup"><span data-stu-id="8af83-173">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="8af83-174">Wenn es mehrere Aufrufe der Methode `Configure` gibt, wird der letzte `Configure`-Aufruf verwendet.</span><span class="sxs-lookup"><span data-stu-id="8af83-174">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

::: moniker-end

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="8af83-175">Erweitern Sie den Start mit Startfiltern</span><span class="sxs-lookup"><span data-stu-id="8af83-175">Extend Startup with startup filters</span></span>

<span data-ttu-id="8af83-176">Verwenden Sie <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>, um Middleware am Anfang und am Ende der [Configure](#the-configure-method)-Middlewarepipeline einer App zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8af83-176">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> to configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline.</span></span> <span data-ttu-id="8af83-177">`IStartupFilter` wird verwendet, um eine Pipeline aus `Configure`-Methoden zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8af83-177">`IStartupFilter` is used to create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="8af83-178">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) kann festlegen, dass eine Middleware vor oder nach dem Hinzufügen von Middleware durch Bibliotheken ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-178">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="8af83-179">`IStartupFilter` implementiert <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>. Das Element empfängt einen `Action<IApplicationBuilder>` und gibt diesen zurück.</span><span class="sxs-lookup"><span data-stu-id="8af83-179">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="8af83-180">Eine <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>-Schnittstelle definiert Klassen, um die Anforderungspipeline einer App zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="8af83-180">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="8af83-181">Weitere Informationen finden Sie unter [Erstellen einer Middlewarepipeline mit IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="8af83-181">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="8af83-182">Jeder `IStartupFilter` kann mindestens eine Middlewarekomponente in der Anforderungspipeline hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="8af83-182">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="8af83-183">Die Filter werden in der Reihenfolge aufgerufen, in der sie zum Dienstcontainer hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="8af83-183">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="8af83-184">Über Filter kann Middleware hinzugefügt werden, bevor oder nachdem dem nächsten Filter die Kontrolle erteilt wird. D.h., sie wird am Anfang oder am Ende einer App-Pipeline hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8af83-184">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="8af83-185">Im folgenden Beispiel wird veranschaulicht, wie Middleware bei `IStartupFilter` registriert wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-185">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="8af83-186">Die `RequestSetOptionsMiddleware`-Middleware legt einen Optionswert über einen Parameter der Abfragezeichenfolge fest:</span><span class="sxs-lookup"><span data-stu-id="8af83-186">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="8af83-187">Die `RequestSetOptionsMiddleware`-Klasse wird in der `RequestSetOptionsStartupFilter`-Klasse konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="8af83-187">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="8af83-188">Die `RequestSetOptionsMiddleware`-Klasse wird in der `RequestSetOptionsStartupFilter`-Klasse konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="8af83-188">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

::: moniker-end

<span data-ttu-id="8af83-189">Der `IStartupFilter` wird im Dienstcontainer in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> registriert.</span><span class="sxs-lookup"><span data-stu-id="8af83-189">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

::: moniker-end

<span data-ttu-id="8af83-190">Wenn für `option` ein Parameter der Abfragezeichenfolge bereitgestellt wird, verarbeitet die Middleware die Wertzuweisung, bevor die ASP.NET Core-Middleware die Antwort rendert.</span><span class="sxs-lookup"><span data-stu-id="8af83-190">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="8af83-191">Die Reihenfolge der Ausführung der Middleware ist von der Reihenfolge der `IStartupFilter`-Registrierungen abhängig:</span><span class="sxs-lookup"><span data-stu-id="8af83-191">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="8af83-192">Es kann sein, dass mehrere Implementierungen von `IStartupFilter` mit denselben Objekten interagieren.</span><span class="sxs-lookup"><span data-stu-id="8af83-192">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="8af83-193">Wenn die Reihenfolge für Sie wichtig ist, sollten Sie die `IStartupFilter`-Dienstregistrierungen der Implementierungen in der Reihenfolge anordnen, in der die Middleware ausgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="8af83-193">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="8af83-194">Bibliotheken fügen möglicherweise Middleware mit mindestens einer `IStartupFilter`-Implementierung hinzu, die vor oder nach anderer App-Middleware ausgeführt wird, die über `IStartupFilter` registriert wurde.</span><span class="sxs-lookup"><span data-stu-id="8af83-194">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="8af83-195">So rufen Sie `IStartupFilter`-Middleware auf, bevor eine Middleware durch den `IStartupFilter` von einer Bibliothek hinzugefügt wird:</span><span class="sxs-lookup"><span data-stu-id="8af83-195">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="8af83-196">Positionieren Sie die Dienstregistrierung, bevor die Bibliothek dem Dienstcontainer hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="8af83-196">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="8af83-197">Wenn sie danach aufgerufen werden soll, positionieren Sie die Dienstregistrierung, nachdem die Bibliothek hinzugefügt wurde.</span><span class="sxs-lookup"><span data-stu-id="8af83-197">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="8af83-198">Hinzufügen von Konfigurationen aus einer externen Assembly beim Start</span><span class="sxs-lookup"><span data-stu-id="8af83-198">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="8af83-199">Eine <xref:Microsoft.AspNetCore.Hosting.IHostingStartup>-Implementierung ermöglicht das Hinzufügen von Erweiterungen zu einer App beim Start von einer externen Assembly außerhalb der `Startup`-Klasse der App aus.</span><span class="sxs-lookup"><span data-stu-id="8af83-199">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="8af83-200">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8af83-200">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8af83-201">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8af83-201">Additional resources</span></span>

* [<span data-ttu-id="8af83-202">Der Host</span><span class="sxs-lookup"><span data-stu-id="8af83-202">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>
