---
title: ASP.NET Core – Grundlagen
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen von ASP.NET Core-Apps kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/31/2019
uid: fundamentals/index
ms.openlocfilehash: a1fed574db0baab391ebb9cfc44664ceddbfa69b
ms.sourcegitcommit: 5995f44e9e13d7e7aa8d193e2825381c42184e47
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/02/2019
ms.locfileid: "58809288"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="26cd2-103">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="26cd2-103">ASP.NET Core fundamentals</span></span>

<span data-ttu-id="26cd2-104">In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="26cd2-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="26cd2-105">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="26cd2-105">The Startup class</span></span>

<span data-ttu-id="26cd2-106">In der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="26cd2-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="26cd2-107">werden alle von der Anwendung benötigten Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="26cd2-107">Any services required by the app are configured.</span></span>
* <span data-ttu-id="26cd2-108">wird die Pipeline zur Anforderungsverarbeitung definiert.</span><span class="sxs-lookup"><span data-stu-id="26cd2-108">The request handling pipeline is defined.</span></span>

* <span data-ttu-id="26cd2-109">wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-109">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="26cd2-110">*Dienste* sind Komponenten, die von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-110">*Services* are components that are used by the app.</span></span> <span data-ttu-id="26cd2-111">Ein Entity Framework Core-Kontextobjekt beispielsweise ist ein Dienst.</span><span class="sxs-lookup"><span data-stu-id="26cd2-111">For example, an Entity Framework Core context object is a service.</span></span>
* <span data-ttu-id="26cd2-112">Code zum Konfigurieren der Pipeline zur Anforderungsverarbeitung wird der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-112">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span> <span data-ttu-id="26cd2-113">Die Pipeline besteht aus mehreren *Middleware*-Komponenten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-113">The pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="26cd2-114">Eine Middleware kann beispielsweise Anforderungen für statische Dateien verarbeiten oder HTTP-Anforderungen zu HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-114">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="26cd2-115">Jede Middleware führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="26cd2-115">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="26cd2-116">Beispiel für eine `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="26cd2-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="26cd2-117">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="26cd2-118">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="26cd2-118">Dependency injection (services)</span></span>

<span data-ttu-id="26cd2-119">ASP.NET Core verfügt über ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste für die Klassen einer App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="26cd2-120">Eine Möglichkeit zum Abrufen einer Instanz eines Diensts in einer Klasse ist das Erstellen eines Konstruktors mit einem Parameter des erforderlichen Typs.</span><span class="sxs-lookup"><span data-stu-id="26cd2-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="26cd2-121">Der Parameter kann der Diensttyp oder eine Schnittstelle sein.</span><span class="sxs-lookup"><span data-stu-id="26cd2-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="26cd2-122">Das DI-System stellt den Dienst zur Laufzeit bereit.</span><span class="sxs-lookup"><span data-stu-id="26cd2-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="26cd2-123">Hier ist eine Klasse, in der Dependency Injection verwendet wird, um ein Entity Framework Core-Kontextobjekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="26cd2-124">Die hervorgehobene Zeile ist ein Beispiel für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="26cd2-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="26cd2-125">Obwohl die Dependency Injection integriert ist, können Sie hier, falls gewünscht, einen IoC-Container eines Drittanbieters einbinden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="26cd2-126">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="26cd2-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="26cd2-127">Middleware</span></span>

<span data-ttu-id="26cd2-128">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="26cd2-129">Jede Komponente führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="26cd2-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="26cd2-130">Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen ihrer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="26cd2-131">Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.</span><span class="sxs-lookup"><span data-stu-id="26cd2-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="26cd2-132">Der hervorgehobene Code in dem folgenden Beispiel konfiguriert die Pipeline zur Anforderungsverarbeitung:</span><span class="sxs-lookup"><span data-stu-id="26cd2-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="26cd2-133">ASP.NET Core enthält eine Reihe umfangreicher integrierter Middleware. Außerdem können Sie benutzerdefinierte Middleware erstellen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="26cd2-134">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

<a id="host"/>

## <a name="the-host"></a><span data-ttu-id="26cd2-135">Der Host</span><span class="sxs-lookup"><span data-stu-id="26cd2-135">The host</span></span>

<span data-ttu-id="26cd2-136">Beim Starten erstellt eine ASP.NET Core-App einen *Host*.</span><span class="sxs-lookup"><span data-stu-id="26cd2-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="26cd2-137">Der Host ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="26cd2-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="26cd2-138">eine HTTP-Serverimplementierung</span><span class="sxs-lookup"><span data-stu-id="26cd2-138">An HTTP server implementation</span></span>
* <span data-ttu-id="26cd2-139">Middlewarekomponenten</span><span class="sxs-lookup"><span data-stu-id="26cd2-139">Middleware components</span></span>
* <span data-ttu-id="26cd2-140">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="26cd2-140">Logging</span></span>
* <span data-ttu-id="26cd2-141">DI</span><span class="sxs-lookup"><span data-stu-id="26cd2-141">DI</span></span>
* <span data-ttu-id="26cd2-142">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="26cd2-142">Configuration</span></span>

<span data-ttu-id="26cd2-143">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="26cd2-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="26cd2-144">Der Code zum Erstellen eines Hosts befindet sich in `Program.Main` und folgt dem [Generatormuster](https://wikipedia.org/wiki/Builder_pattern).</span><span class="sxs-lookup"><span data-stu-id="26cd2-144">The code to create a host is in `Program.Main` and follows the [builder pattern](https://wikipedia.org/wiki/Builder_pattern).</span></span> <span data-ttu-id="26cd2-145">Zum Konfigurieren jeder Ressource, die zum Host gehört, werden Methoden aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-145">Methods are called to configure each resource that is part of the host.</span></span> <span data-ttu-id="26cd2-146">Um alles zu bündeln und das Hostobjekt zu instanziieren, wird eine Generatormethode aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-146">A builder method is called to pull it all together and instantiate the host object.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26cd2-147">In ASP.NET Core 3.0 kann ein generischer Host (`Host`-Klasse) oder ein Webhost (`WebHost`-Klasse) in einer Web-App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-147">In ASP.NET Core 3.0 or later, Generic Host (`Host` class) or Web Host (`WebHost` class) can be used in a web app.</span></span> <span data-ttu-id="26cd2-148">Ein generischer Host wird empfohlen, und ein Webhost ist für Abwärtskompatibilität verfügbar.</span><span class="sxs-lookup"><span data-stu-id="26cd2-148">Generic Host is recommended, and Web Host is available for backwards compatibility.</span></span>

<span data-ttu-id="26cd2-149">Das Framework bietet die Methoden `CreateDefaultBuilder` und `ConfigureWebHostDefaults`, die einen Host mit häufig verwendeten Optionen, wie den folgenden, einrichten:</span><span class="sxs-lookup"><span data-stu-id="26cd2-149">The framework provides the `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="26cd2-150">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-150">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="26cd2-151">Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-151">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="26cd2-152">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="26cd2-152">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="26cd2-153">Hier ist ein Beispielcode für das Erstellen eines Hosts:</span><span class="sxs-lookup"><span data-stu-id="26cd2-153">Here's sample code that builds a host.</span></span> <span data-ttu-id="26cd2-154">Die Methoden zum Einrichten des Hosts mit häufig verwendeten Optionen sind hervorgehoben:</span><span class="sxs-lookup"><span data-stu-id="26cd2-154">The methods that set up the host with commonly used options are highlighted:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs?highlight=9-10)]

<span data-ttu-id="26cd2-155">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-155">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26cd2-156">ASP.NET Core 2.x verwendet einen Webhost (`WebHost`-Klasse) für Web-Apps.</span><span class="sxs-lookup"><span data-stu-id="26cd2-156">ASP.NET Core 2.x uses Web Host (`WebHost` class) for web apps.</span></span> <span data-ttu-id="26cd2-157">Das Framework bietet die `CreateDefaultBuilder`-Methode, die einen Host mit häufig verwendeten Optionen, wie den folgenden, einrichtet:</span><span class="sxs-lookup"><span data-stu-id="26cd2-157">The framework provides `CreateDefaultBuilder` to set up a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="26cd2-158">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-158">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="26cd2-159">Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-159">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="26cd2-160">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="26cd2-160">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="26cd2-161">Hier ist ein Beispielcode für das Erstellen eines Hosts:</span><span class="sxs-lookup"><span data-stu-id="26cd2-161">Here's sample code that builds a host:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs?highlight=9)]

<span data-ttu-id="26cd2-162">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-162">For more information, see <xref:fundamentals/host/web-host>.</span></span>

::: moniker-end

### <a name="advanced-host-scenarios"></a><span data-ttu-id="26cd2-163">Erweiterte Hostszenarios</span><span class="sxs-lookup"><span data-stu-id="26cd2-163">Advanced host scenarios</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26cd2-164">Der generische Host ist für jede .NET Core-App verfügbar – nicht nur für ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="26cd2-164">Generic Host is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="26cd2-165">Mit dem generischen Host (`Host`-Klasse) können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection, Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-165">Generic Host (`Host` class) allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="26cd2-166">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-166">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26cd2-167">Der Webhost wurde entwickelt, um eine HTTP-Serverimplementierung einzuschließen, die für andere Arten von .NET-Apps nicht benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="26cd2-167">Web Host is designed to include an HTTP server implementation, which isn't required for other kinds of .NET apps.</span></span> <span data-ttu-id="26cd2-168">Ab ASP.NET Core 2.1 ist der generische Host (`Host`-Klasse) für jede .NET Core-App verfügbar – nicht nur für ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="26cd2-168">Starting in ASP.NET Core 2.1, the Generic Host (`Host` class) is available for any .NET Core app to use&mdash;not just ASP.NET Core apps.</span></span> <span data-ttu-id="26cd2-169">Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection, Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-169">Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, DI, configuration, and app lifetime management.</span></span> <span data-ttu-id="26cd2-170">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

::: moniker-end

<span data-ttu-id="26cd2-171">Sie können auch den Host zum Ausführen von Hintergrundaufgaben verwenden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-171">You can also use the host to run background tasks.</span></span> <span data-ttu-id="26cd2-172">Weitere Informationen finden Sie unter <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-172">For more information, see <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="26cd2-173">Server</span><span class="sxs-lookup"><span data-stu-id="26cd2-173">Servers</span></span>

<span data-ttu-id="26cd2-174">Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-174">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="26cd2-175">Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="26cd2-175">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="26cd2-176">Windows</span><span class="sxs-lookup"><span data-stu-id="26cd2-176">Windows</span></span>](#tab/windows)

<span data-ttu-id="26cd2-177">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="26cd2-177">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="26cd2-178">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="26cd2-178">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="26cd2-179">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-179">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="26cd2-180">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="26cd2-180">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="26cd2-181">Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet.</span><span class="sxs-lookup"><span data-stu-id="26cd2-181">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="26cd2-182">Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-182">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="26cd2-183">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="26cd2-183">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="26cd2-184">macOS</span><span class="sxs-lookup"><span data-stu-id="26cd2-184">macOS</span></span>](#tab/macos)

<span data-ttu-id="26cd2-185">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="26cd2-185">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="26cd2-186">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="26cd2-186">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="26cd2-187">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-187">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="26cd2-188">Linux</span><span class="sxs-lookup"><span data-stu-id="26cd2-188">Linux</span></span>](#tab/linux)

<span data-ttu-id="26cd2-189">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="26cd2-189">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="26cd2-190">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="26cd2-190">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="26cd2-191">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-191">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windowstabwindows"></a>[<span data-ttu-id="26cd2-192">Windows</span><span class="sxs-lookup"><span data-stu-id="26cd2-192">Windows</span></span>](#tab/windows)

<span data-ttu-id="26cd2-193">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="26cd2-193">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="26cd2-194">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="26cd2-194">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="26cd2-195">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-195">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="26cd2-196">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="26cd2-196">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="26cd2-197">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="26cd2-197">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macostabmacos"></a>[<span data-ttu-id="26cd2-198">macOS</span><span class="sxs-lookup"><span data-stu-id="26cd2-198">macOS</span></span>](#tab/macos)

<span data-ttu-id="26cd2-199">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="26cd2-199">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="26cd2-200">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="26cd2-200">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="26cd2-201">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-201">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linuxtablinux"></a>[<span data-ttu-id="26cd2-202">Linux</span><span class="sxs-lookup"><span data-stu-id="26cd2-202">Linux</span></span>](#tab/linux)

<span data-ttu-id="26cd2-203">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="26cd2-203">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="26cd2-204">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="26cd2-204">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="26cd2-205">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](http://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-205">Kestrel is often run in a reverse proxy configuration with [Nginx](http://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

<span data-ttu-id="26cd2-206">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-206">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="26cd2-207">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="26cd2-207">Configuration</span></span>

<span data-ttu-id="26cd2-208">ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft.</span><span class="sxs-lookup"><span data-stu-id="26cd2-208">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="26cd2-209">Integrierte Konfigurationsanbieter gibt es für zahlreiche Quellen wie *.json*-Dateien, *.xml*-Dateien, Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="26cd2-209">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="26cd2-210">Sie können auch benutzerdefinierte Konfigurationsanbieter schreiben.</span><span class="sxs-lookup"><span data-stu-id="26cd2-210">You can also write custom configuration providers.</span></span>

<span data-ttu-id="26cd2-211">Zum Beispiel könnten Sie angeben, dass die Konfiguration aus *appsettings.json* und Umgebungsvariablen stammt.</span><span class="sxs-lookup"><span data-stu-id="26cd2-211">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="26cd2-212">Wenn dann der *ConnectionString*-Wert angefordert wird, sucht das Framework zuerst in der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="26cd2-212">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="26cd2-213">Wenn der Wert sowohl dort als auch in einer Umgebungsvariablen gefunden wird, hat der Wert aus der Umgebungsvariablen Vorrang.</span><span class="sxs-lookup"><span data-stu-id="26cd2-213">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="26cd2-214">Zum Verwalten von vertraulichen Konfigurationsdaten wie Passwörtern bietet ASP.NET Core ein [Geheimnisverwaltungstool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="26cd2-214">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="26cd2-215">Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="26cd2-215">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="26cd2-216">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-216">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="26cd2-217">Optionen</span><span class="sxs-lookup"><span data-stu-id="26cd2-217">Options</span></span>

<span data-ttu-id="26cd2-218">Wo möglich, folgt ASP.NET Core dem *Optionsmuster* zum Speichern und Abrufen von Konfigurationswerten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-218">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="26cd2-219">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-219">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="26cd2-220">Im folgenden Codebeispiel werden WebSockets-Optionen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="26cd2-220">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="26cd2-221">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-221">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="26cd2-222">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="26cd2-222">Environments</span></span>

<span data-ttu-id="26cd2-223">Ausführungsumgebungen wie *Entwicklung*, *Staging* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="26cd2-223">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="26cd2-224">Um die Umgebung anzugeben, in der eine App ausgeführt wird, stellen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable ein.</span><span class="sxs-lookup"><span data-stu-id="26cd2-224">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="26cd2-225">ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IHostingEnvironment`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="26cd2-225">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="26cd2-226">Das Umgebungsobjekt ist in der gesamten App mithilfe der Dependency Injection verfügbar.</span><span class="sxs-lookup"><span data-stu-id="26cd2-226">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="26cd2-227">Im folgenden Codebeispiel aus der `Startup`-Klasse wird die App so konfiguriert, dass detaillierte Fehlerinformationen nur bereitgestellt werden, wenn die App in der Entwicklung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="26cd2-227">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="26cd2-228">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-228">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="26cd2-229">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="26cd2-229">Logging</span></span>

<span data-ttu-id="26cd2-230">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="26cd2-230">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="26cd2-231">Zu den verfügbaren Anbietern gehören folgende:</span><span class="sxs-lookup"><span data-stu-id="26cd2-231">Available providers include the following:</span></span>

* <span data-ttu-id="26cd2-232">Konsole</span><span class="sxs-lookup"><span data-stu-id="26cd2-232">Console</span></span>
* <span data-ttu-id="26cd2-233">Debug</span><span class="sxs-lookup"><span data-stu-id="26cd2-233">Debug</span></span>
* <span data-ttu-id="26cd2-234">Ereignisablaufverfolgung unter Windows</span><span class="sxs-lookup"><span data-stu-id="26cd2-234">Event Tracing on Windows</span></span>
* <span data-ttu-id="26cd2-235">Windows-Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="26cd2-235">Windows Event Log</span></span>
* <span data-ttu-id="26cd2-236">TraceSource</span><span class="sxs-lookup"><span data-stu-id="26cd2-236">TraceSource</span></span>
* <span data-ttu-id="26cd2-237">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="26cd2-237">Azure App Service</span></span>
* <span data-ttu-id="26cd2-238">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="26cd2-238">Azure Application Insights</span></span>

<span data-ttu-id="26cd2-239">Schreiben Sie Protokolle aus jeder Stelle im Code einer App, indem Sie ein `ILogger`-Objekt aus Dependency Injection abrufen und Protokollmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-239">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="26cd2-240">Hier ist ein Beispielcode, in dem ein `ILogger`-Objekt mit Konstruktorinjektion und den hervorgehobenen Aufrufen der Protokollierungsmethode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="26cd2-240">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="26cd2-241">Mithilfe der `ILogger`-Schnittstelle können Sie eine beliebige Anzahl von Feldern an den Protokollierungsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="26cd2-241">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="26cd2-242">Die Felder werden häufig dazu verwendet, eine Meldungszeichenfolge zu erstellen, der Anbieter kann sie aber auch als einzelne Felder an einen Datenspeicher senden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-242">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="26cd2-243">Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="26cd2-243">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="26cd2-244">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-244">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="26cd2-245">Routing</span><span class="sxs-lookup"><span data-stu-id="26cd2-245">Routing</span></span>

<span data-ttu-id="26cd2-246">Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="26cd2-246">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="26cd2-247">Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware.</span><span class="sxs-lookup"><span data-stu-id="26cd2-247">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="26cd2-248">Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-248">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="26cd2-249">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-249">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="26cd2-250">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="26cd2-250">Error handling</span></span>

<span data-ttu-id="26cd2-251">ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="26cd2-251">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="26cd2-252">eine Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="26cd2-252">A developer exception page</span></span>
* <span data-ttu-id="26cd2-253">benutzerdefinierte Fehlerseiten</span><span class="sxs-lookup"><span data-stu-id="26cd2-253">Custom error pages</span></span>
* <span data-ttu-id="26cd2-254">statische Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="26cd2-254">Static status code pages</span></span>
* <span data-ttu-id="26cd2-255">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="26cd2-255">Startup exception handling</span></span>

<span data-ttu-id="26cd2-256">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-256">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="26cd2-257">Übermitteln von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="26cd2-257">Make HTTP requests</span></span>

<span data-ttu-id="26cd2-258">Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-258">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="26cd2-259">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="26cd2-259">The factory:</span></span>

* <span data-ttu-id="26cd2-260">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-260">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="26cd2-261">Zum Beispiel kann ein *github*-Client für den Zugriff auf GitHub registriert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-261">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="26cd2-262">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-262">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="26cd2-263">Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="26cd2-263">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="26cd2-264">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="26cd2-264">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="26cd2-265">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="26cd2-265">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="26cd2-266">Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="26cd2-266">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="26cd2-267">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="26cd2-267">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="26cd2-268">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-268">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="26cd2-269">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-269">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="26cd2-270">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="26cd2-270">Content root</span></span>

<span data-ttu-id="26cd2-271">Der Inhaltsstamm ist der Basispfad zu einem beliebigen von der App verwendeten privaten Inhalt wie beispielsweise ihren Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="26cd2-271">The content root is the base path to any private content used by the app, such as its Razor files.</span></span> <span data-ttu-id="26cd2-272">Standardmäßig entspricht der Inhaltsstamm dem Basispfad der ausführbaren Datei, mit der die Anwendung gehostet wird.</span><span class="sxs-lookup"><span data-stu-id="26cd2-272">By default, the content root is the base path for the executable hosting the app.</span></span> <span data-ttu-id="26cd2-273">Ein alternativer Speicherort kann beim [Erstellen des Hosts](#host) angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-273">An alternative location can be specified when [building the host](#host).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="26cd2-274">Weitere Informationen finden Sie unter [Inhaltsstamm](xref:fundamentals/host/generic-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="26cd2-274">For more information, see [Content root](xref:fundamentals/host/generic-host#content-root).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="26cd2-275">Weitere Informationen finden Sie unter [Inhaltsstamm](xref:fundamentals/host/web-host#content-root).</span><span class="sxs-lookup"><span data-stu-id="26cd2-275">For more information, see [Content root](xref:fundamentals/host/web-host#content-root).</span></span>

::: moniker-end

## <a name="web-root"></a><span data-ttu-id="26cd2-276">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="26cd2-276">Web root</span></span>

<span data-ttu-id="26cd2-277">Der Webstamm (auch bekannt als *webroot*) ist der Basispfad zu öffentlichen statischen Ressourcen wie CSS, JavaScript und Bilddateien.</span><span class="sxs-lookup"><span data-stu-id="26cd2-277">The web root (also known as *webroot*) is the base path to public, static resources, such as CSS, JavaScript, and image files.</span></span> <span data-ttu-id="26cd2-278">Die Middleware für statische Dateien stellt standardmäßig nur Dateien aus dem Webstammverzeichnis (und Unterverzeichnissen) bereit.</span><span class="sxs-lookup"><span data-stu-id="26cd2-278">The static files middleware will only serve files from the web root directory (and sub-directories) by default.</span></span> <span data-ttu-id="26cd2-279">Der Pfad für den Webstamm ist standardmäßig auf *{Content Root}/wwwroot* festgelegt, beim [Erstellen des Hosts](#host) kann jedoch ein anderer Speicherort angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="26cd2-279">The web root path defaults to *{Content Root}/wwwroot*, but a different location can be specified when [building the host](#host).</span></span>

<span data-ttu-id="26cd2-280">In Razor-Dateien (*.cshtml*) zeigen die Tilde und der Schrägstrich `~/` auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="26cd2-280">In Razor (*.cshtml*) files, the tilde-slash `~/` points to the web root.</span></span> <span data-ttu-id="26cd2-281">Pfade, die mit `~/` beginnen, werden als virtuelle Pfade bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="26cd2-281">Paths beginning with `~/` are referred to as virtual paths.</span></span>

<span data-ttu-id="26cd2-282">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="26cd2-282">For more information, see <xref:fundamentals/static-files>.</span></span>
