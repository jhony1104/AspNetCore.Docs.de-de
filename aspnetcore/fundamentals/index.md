---
title: ASP.NET Core – Grundlagen
author: rick-anderson
description: Lernen Sie die grundlegenden Konzepte zum Erstellen von ASP.NET Core-Apps kennen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: fundamentals/index
ms.openlocfilehash: 7533242140c31a937f32cc9082d760103347ce25
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219180"
---
# <a name="aspnet-core-fundamentals"></a><span data-ttu-id="8324b-103">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="8324b-103">ASP.NET Core fundamentals</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8324b-104">In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="8324b-104">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="8324b-105">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="8324b-105">The Startup class</span></span>

<span data-ttu-id="8324b-106">In der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8324b-106">The `Startup` class is where:</span></span>

* <span data-ttu-id="8324b-107">werden die von der App erforderlichen Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8324b-107">Services required by the app are configured.</span></span>
* <span data-ttu-id="8324b-108">wird die Pipeline zur Anforderungsverarbeitung definiert.</span><span class="sxs-lookup"><span data-stu-id="8324b-108">The request handling pipeline is defined.</span></span>

<span data-ttu-id="8324b-109">*Dienste* sind Komponenten, die von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-109">*Services* are components that are used by the app.</span></span> <span data-ttu-id="8324b-110">Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="8324b-110">For example, a logging component is a service.</span></span> <span data-ttu-id="8324b-111">wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8324b-111">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="8324b-112">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren *Middlewarekomponenten*.</span><span class="sxs-lookup"><span data-stu-id="8324b-112">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="8324b-113">Eine Middleware kann beispielsweise Anforderungen für statische Dateien verarbeiten oder HTTP-Anforderungen zu HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="8324b-113">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="8324b-114">Jede Middleware führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8324b-114">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="8324b-115">Code zum Konfigurieren der Pipeline zur Anforderungsverarbeitung wird der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8324b-115">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="8324b-116">Beispiel für eine `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8324b-116">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="8324b-117">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8324b-117">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="8324b-118">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="8324b-118">Dependency injection (services)</span></span>

<span data-ttu-id="8324b-119">ASP.NET Core verfügt über ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste für die Klassen einer App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-119">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="8324b-120">Eine Möglichkeit zum Abrufen einer Instanz eines Diensts in einer Klasse ist das Erstellen eines Konstruktors mit einem Parameter des erforderlichen Typs.</span><span class="sxs-lookup"><span data-stu-id="8324b-120">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="8324b-121">Der Parameter kann der Diensttyp oder eine Schnittstelle sein.</span><span class="sxs-lookup"><span data-stu-id="8324b-121">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="8324b-122">Das DI-System stellt den Dienst zur Laufzeit bereit.</span><span class="sxs-lookup"><span data-stu-id="8324b-122">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="8324b-123">Hier ist eine Klasse, in der Dependency Injection verwendet wird, um ein Entity Framework Core-Kontextobjekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="8324b-123">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="8324b-124">Die hervorgehobene Zeile ist ein Beispiel für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="8324b-124">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="8324b-125">Obwohl die Dependency Injection integriert ist, können Sie hier, falls gewünscht, einen IoC-Container eines Drittanbieters einbinden.</span><span class="sxs-lookup"><span data-stu-id="8324b-125">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="8324b-126">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8324b-126">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="8324b-127">Middleware</span><span class="sxs-lookup"><span data-stu-id="8324b-127">Middleware</span></span>

<span data-ttu-id="8324b-128">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten.</span><span class="sxs-lookup"><span data-stu-id="8324b-128">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="8324b-129">Jede Komponente führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8324b-129">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="8324b-130">Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen ihrer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8324b-130">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="8324b-131">Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.</span><span class="sxs-lookup"><span data-stu-id="8324b-131">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="8324b-132">Der hervorgehobene Code in dem folgenden Beispiel konfiguriert die Pipeline zur Anforderungsverarbeitung:</span><span class="sxs-lookup"><span data-stu-id="8324b-132">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="8324b-133">ASP.NET Core enthält eine Reihe umfangreicher integrierter Middleware. Außerdem können Sie benutzerdefinierte Middleware erstellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-133">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="8324b-134">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-134">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="8324b-135">Host</span><span class="sxs-lookup"><span data-stu-id="8324b-135">Host</span></span>

<span data-ttu-id="8324b-136">Beim Starten erstellt eine ASP.NET Core-App einen *Host*.</span><span class="sxs-lookup"><span data-stu-id="8324b-136">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="8324b-137">Der Host ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="8324b-137">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="8324b-138">eine HTTP-Serverimplementierung</span><span class="sxs-lookup"><span data-stu-id="8324b-138">An HTTP server implementation</span></span>
* <span data-ttu-id="8324b-139">Middlewarekomponenten</span><span class="sxs-lookup"><span data-stu-id="8324b-139">Middleware components</span></span>
* <span data-ttu-id="8324b-140">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="8324b-140">Logging</span></span>
* <span data-ttu-id="8324b-141">DI</span><span class="sxs-lookup"><span data-stu-id="8324b-141">DI</span></span>
* <span data-ttu-id="8324b-142">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8324b-142">Configuration</span></span>

<span data-ttu-id="8324b-143">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="8324b-143">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="8324b-144">Es stehen zwei Hosts zur Verfügung: der generische Host und der Webhost.</span><span class="sxs-lookup"><span data-stu-id="8324b-144">Two hosts are available: the Generic Host and the Web Host.</span></span> <span data-ttu-id="8324b-145">Der generische Host wird empfohlen. Der Webhost ist nur für die Abwärtskompatibilität verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8324b-145">The Generic Host is recommended, and the Web Host is available only for backwards compatibility.</span></span>

<span data-ttu-id="8324b-146">Der Code zum Erstellen eines Hosts befindet sich in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="8324b-146">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/3.x/Program1.cs)]

<span data-ttu-id="8324b-147">Die Methoden `CreateDefaultBuilder` und `ConfigureWebHostDefaults` konfigurieren einen Host mit häufig verwendeten Optionen wie den folgenden:</span><span class="sxs-lookup"><span data-stu-id="8324b-147">The `CreateDefaultBuilder` and `ConfigureWebHostDefaults` methods configure a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="8324b-148">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="8324b-148">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="8324b-149">Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-149">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="8324b-150">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="8324b-150">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="8324b-151">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host>.</span><span class="sxs-lookup"><span data-stu-id="8324b-151">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="8324b-152">Nicht webbasierte Szenarios</span><span class="sxs-lookup"><span data-stu-id="8324b-152">Non-web scenarios</span></span>

<span data-ttu-id="8324b-153">Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="8324b-153">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="8324b-154">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="8324b-154">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="8324b-155">Server</span><span class="sxs-lookup"><span data-stu-id="8324b-155">Servers</span></span>

<span data-ttu-id="8324b-156">Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="8324b-156">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="8324b-157">Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="8324b-157">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

# <a name="windows"></a>[<span data-ttu-id="8324b-158">Windows</span><span class="sxs-lookup"><span data-stu-id="8324b-158">Windows</span></span>](#tab/windows)

<span data-ttu-id="8324b-159">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="8324b-159">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="8324b-160">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="8324b-160">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="8324b-161">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-161">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="8324b-162">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-162">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="8324b-163">Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet.</span><span class="sxs-lookup"><span data-stu-id="8324b-163">*IIS HTTP Server* is a server for Windows that uses IIS.</span></span> <span data-ttu-id="8324b-164">Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-164">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="8324b-165">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8324b-165">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="8324b-166">macOS</span><span class="sxs-lookup"><span data-stu-id="8324b-166">macOS</span></span>](#tab/macos)

<span data-ttu-id="8324b-167">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="8324b-167">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8324b-168">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-168">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8324b-169">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-169">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="8324b-170">Linux</span><span class="sxs-lookup"><span data-stu-id="8324b-170">Linux</span></span>](#tab/linux)

<span data-ttu-id="8324b-171">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="8324b-171">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8324b-172">In ASP.NET Core 2.0 oder höher kann Kestrel als öffentlich zugänglicher Edge-Server ausgeführt werden, der direkt mit dem Internet verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-172">In ASP.NET Core 2.0 or later, Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8324b-173">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-173">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

<span data-ttu-id="8324b-174">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-174">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="8324b-175">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8324b-175">Configuration</span></span>

<span data-ttu-id="8324b-176">ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft.</span><span class="sxs-lookup"><span data-stu-id="8324b-176">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="8324b-177">Integrierte Konfigurationsanbieter gibt es für zahlreiche Quellen wie *.json*-Dateien, *.xml*-Dateien, Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="8324b-177">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="8324b-178">Sie können auch benutzerdefinierte Konfigurationsanbieter schreiben.</span><span class="sxs-lookup"><span data-stu-id="8324b-178">You can also write custom configuration providers.</span></span>

<span data-ttu-id="8324b-179">Zum Beispiel könnten Sie angeben, dass die Konfiguration aus *appsettings.json* und Umgebungsvariablen stammt.</span><span class="sxs-lookup"><span data-stu-id="8324b-179">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="8324b-180">Wenn dann der *ConnectionString*-Wert angefordert wird, sucht das Framework zuerst in der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8324b-180">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="8324b-181">Wenn der Wert sowohl dort als auch in einer Umgebungsvariablen gefunden wird, hat der Wert aus der Umgebungsvariablen Vorrang.</span><span class="sxs-lookup"><span data-stu-id="8324b-181">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="8324b-182">Zum Verwalten von vertraulichen Konfigurationsdaten wie Passwörtern bietet ASP.NET Core ein [Geheimnisverwaltungstool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8324b-182">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8324b-183">Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8324b-183">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="8324b-184">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="8324b-185">Optionen</span><span class="sxs-lookup"><span data-stu-id="8324b-185">Options</span></span>

<span data-ttu-id="8324b-186">Wo möglich, folgt ASP.NET Core dem *Optionsmuster* zum Speichern und Abrufen von Konfigurationswerten.</span><span class="sxs-lookup"><span data-stu-id="8324b-186">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="8324b-187">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-187">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="8324b-188">Im folgenden Codebeispiel werden WebSockets-Optionen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8324b-188">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="8324b-189">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8324b-189">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="8324b-190">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="8324b-190">Environments</span></span>

<span data-ttu-id="8324b-191">Ausführungsumgebungen wie *Entwicklung*, *Staging* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="8324b-191">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="8324b-192">Um die Umgebung anzugeben, in der eine App ausgeführt wird, stellen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable ein.</span><span class="sxs-lookup"><span data-stu-id="8324b-192">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="8324b-193">ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IHostingEnvironment`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="8324b-193">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="8324b-194">Das Umgebungsobjekt ist in der gesamten App mithilfe der Dependency Injection verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8324b-194">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="8324b-195">Im folgenden Codebeispiel aus der `Startup`-Klasse wird die App so konfiguriert, dass detaillierte Fehlerinformationen nur bereitgestellt werden, wenn die App in der Entwicklung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="8324b-195">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="8324b-196">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8324b-196">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="8324b-197">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="8324b-197">Logging</span></span>

<span data-ttu-id="8324b-198">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="8324b-198">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="8324b-199">Zu den verfügbaren Anbietern gehören folgende:</span><span class="sxs-lookup"><span data-stu-id="8324b-199">Available providers include the following:</span></span>

* <span data-ttu-id="8324b-200">Konsole</span><span class="sxs-lookup"><span data-stu-id="8324b-200">Console</span></span>
* <span data-ttu-id="8324b-201">Debug</span><span class="sxs-lookup"><span data-stu-id="8324b-201">Debug</span></span>
* <span data-ttu-id="8324b-202">Ereignisablaufverfolgung unter Windows</span><span class="sxs-lookup"><span data-stu-id="8324b-202">Event Tracing on Windows</span></span>
* <span data-ttu-id="8324b-203">Windows-Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="8324b-203">Windows Event Log</span></span>
* <span data-ttu-id="8324b-204">TraceSource</span><span class="sxs-lookup"><span data-stu-id="8324b-204">TraceSource</span></span>
* <span data-ttu-id="8324b-205">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8324b-205">Azure App Service</span></span>
* <span data-ttu-id="8324b-206">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="8324b-206">Azure Application Insights</span></span>

<span data-ttu-id="8324b-207">Schreiben Sie Protokolle aus jeder Stelle im Code einer App, indem Sie ein `ILogger`-Objekt aus Dependency Injection abrufen und Protokollmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="8324b-207">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="8324b-208">Hier ist ein Beispielcode, in dem ein `ILogger`-Objekt mit Konstruktorinjektion und den hervorgehobenen Aufrufen der Protokollierungsmethode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8324b-208">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="8324b-209">Mithilfe der `ILogger`-Schnittstelle können Sie eine beliebige Anzahl von Feldern an den Protokollierungsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="8324b-209">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="8324b-210">Die Felder werden häufig dazu verwendet, eine Meldungszeichenfolge zu erstellen, der Anbieter kann sie aber auch als einzelne Felder an einen Datenspeicher senden.</span><span class="sxs-lookup"><span data-stu-id="8324b-210">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="8324b-211">Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="8324b-211">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="8324b-212">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-212">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="8324b-213">Routing</span><span class="sxs-lookup"><span data-stu-id="8324b-213">Routing</span></span>

<span data-ttu-id="8324b-214">Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-214">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="8324b-215">Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware.</span><span class="sxs-lookup"><span data-stu-id="8324b-215">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="8324b-216">Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-216">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="8324b-217">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="8324b-217">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="8324b-218">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="8324b-218">Error handling</span></span>

<span data-ttu-id="8324b-219">ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="8324b-219">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="8324b-220">eine Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="8324b-220">A developer exception page</span></span>
* <span data-ttu-id="8324b-221">benutzerdefinierte Fehlerseiten</span><span class="sxs-lookup"><span data-stu-id="8324b-221">Custom error pages</span></span>
* <span data-ttu-id="8324b-222">statische Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="8324b-222">Static status code pages</span></span>
* <span data-ttu-id="8324b-223">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="8324b-223">Startup exception handling</span></span>

<span data-ttu-id="8324b-224">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="8324b-224">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="8324b-225">Übermitteln von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="8324b-225">Make HTTP requests</span></span>

<span data-ttu-id="8324b-226">Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8324b-226">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="8324b-227">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="8324b-227">The factory:</span></span>

* <span data-ttu-id="8324b-228">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="8324b-228">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="8324b-229">Zum Beispiel kann ein *github*-Client für den Zugriff auf GitHub registriert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-229">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="8324b-230">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-230">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="8324b-231">Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-231">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="8324b-232">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8324b-232">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="8324b-233">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="8324b-233">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="8324b-234">Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="8324b-234">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="8324b-235">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="8324b-235">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="8324b-236">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="8324b-236">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="8324b-237">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="8324b-237">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="8324b-238">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="8324b-238">Content root</span></span>

<span data-ttu-id="8324b-239">Der Inhaltsstamm ist der Basispfad zu Folgendem:</span><span class="sxs-lookup"><span data-stu-id="8324b-239">The content root is the base path to the:</span></span>

* <span data-ttu-id="8324b-240">Der ausführbaren Datei ( *.exe*), die die App hostet.</span><span class="sxs-lookup"><span data-stu-id="8324b-240">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="8324b-241">Kompilierten Assemblys, die die App bilden ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="8324b-241">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="8324b-242">Inhaltsdateien ohne Code, die von der App verwendet werden, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="8324b-242">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="8324b-243">Razor-Dateien ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="8324b-243">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="8324b-244">Konfigurationsdateien ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="8324b-244">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="8324b-245">Datendateien ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="8324b-245">Data files (*.db*)</span></span>
* <span data-ttu-id="8324b-246">[Webstamm](#web-root), in der Regel der veröffentlichte Ordner *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8324b-246">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="8324b-247">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="8324b-247">During development:</span></span>

* <span data-ttu-id="8324b-248">Der Inhaltsstamm ist standardmäßig auf das Stammverzeichnis des Projekts festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8324b-248">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="8324b-249">Das Stammverzeichnis des Projekts dient zum Erstellen von Folgendem:</span><span class="sxs-lookup"><span data-stu-id="8324b-249">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="8324b-250">Pfad zu den Inhaltsdateien ohne Code der App im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8324b-250">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="8324b-251">[Webstamm](#web-root), in der Regel der Ordner *wwwroot* im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8324b-251">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="8324b-252">Ein alternativer Inhaltsstammpfad kann beim [Erstellen des Hosts](#host) angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-252">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="8324b-253">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#contentrootpath>.</span><span class="sxs-lookup"><span data-stu-id="8324b-253">For more information, see <xref:fundamentals/host/generic-host#contentrootpath>.</span></span>

## <a name="web-root"></a><span data-ttu-id="8324b-254">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="8324b-254">Web root</span></span>

<span data-ttu-id="8324b-255">Der Webstamm ist der Basispfad zu öffentlichen, statischen Ressourcendateien ohne Code, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="8324b-255">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="8324b-256">Stylesheets ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="8324b-256">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="8324b-257">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="8324b-257">JavaScript (*.js*)</span></span>
* <span data-ttu-id="8324b-258">Bilder ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="8324b-258">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="8324b-259">Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis (samt Unterverzeichnissen) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8324b-259">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="8324b-260">Der Webstammpfad ist standardmäßig auf *{Inhaltsstamm}/wwwroot* festgelegt, doch beim [Erstellen des Hosts](#host) kann ein anderer Webstamm angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-260">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="8324b-261">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#webroot>.</span><span class="sxs-lookup"><span data-stu-id="8324b-261">For more information, see <xref:fundamentals/host/generic-host#webroot>.</span></span>

<span data-ttu-id="8324b-262">Verhindern Sie das Veröffentlichen von Datei in *wwwroot* über [\<Inhalt > Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="8324b-262">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="8324b-263">Im folgenden Beispiel wird verhindert, dass Inhalte im *wwwroot/local*-Verzeichnis und in Unterverzeichnisse veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="8324b-263">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="8324b-264">Weitere Informationen darüber, wie Sie verhindern, dass statische Identitätsobjekte veröffentlicht werden, finden Sie unter <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span><span class="sxs-lookup"><span data-stu-id="8324b-264">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

<span data-ttu-id="8324b-265">In Razor-Dateien ( *.cshtml*) zeigen Tilde und Schrägstrich (`~/`) auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="8324b-265">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="8324b-266">Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="8324b-266">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="8324b-267">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="8324b-267">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8324b-268">In diesem Artikel finden Sie eine Übersicht über die wichtigsten Themen zum Entwickeln von ASP.NET Core-Apps.</span><span class="sxs-lookup"><span data-stu-id="8324b-268">This article is an overview of key topics for understanding how to develop ASP.NET Core apps.</span></span>

## <a name="the-startup-class"></a><span data-ttu-id="8324b-269">Die Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="8324b-269">The Startup class</span></span>

<span data-ttu-id="8324b-270">In der `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8324b-270">The `Startup` class is where:</span></span>

* <span data-ttu-id="8324b-271">werden die von der App erforderlichen Dienste konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8324b-271">Services required by the app are configured.</span></span>
* <span data-ttu-id="8324b-272">wird die Pipeline zur Anforderungsverarbeitung definiert.</span><span class="sxs-lookup"><span data-stu-id="8324b-272">The request handling pipeline is defined.</span></span>

<span data-ttu-id="8324b-273">*Dienste* sind Komponenten, die von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-273">*Services* are components that are used by the app.</span></span> <span data-ttu-id="8324b-274">Eine Protokollierungskomponente stellt beispielsweise einen Dienst dar.</span><span class="sxs-lookup"><span data-stu-id="8324b-274">For example, a logging component is a service.</span></span> <span data-ttu-id="8324b-275">wird Code, der Dienste konfiguriert (oder *registriert*) der `Startup.ConfigureServices`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8324b-275">Code to configure (or *register*) services is added to the `Startup.ConfigureServices` method.</span></span>

<span data-ttu-id="8324b-276">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren *Middlewarekomponenten*.</span><span class="sxs-lookup"><span data-stu-id="8324b-276">The request handling pipeline is composed as a series of *middleware* components.</span></span> <span data-ttu-id="8324b-277">Eine Middleware kann beispielsweise Anforderungen für statische Dateien verarbeiten oder HTTP-Anforderungen zu HTTPS umleiten.</span><span class="sxs-lookup"><span data-stu-id="8324b-277">For example, a middleware might handle requests for static files or redirect HTTP requests to HTTPS.</span></span> <span data-ttu-id="8324b-278">Jede Middleware führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8324b-278">Each middleware performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span> <span data-ttu-id="8324b-279">Code zum Konfigurieren der Pipeline zur Anforderungsverarbeitung wird der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8324b-279">Code to configure the request handling pipeline is added to the `Startup.Configure` method.</span></span>

<span data-ttu-id="8324b-280">Beispiel für eine `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="8324b-280">Here's a sample `Startup` class:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=3,12)]

<span data-ttu-id="8324b-281">Weitere Informationen finden Sie unter <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="8324b-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="dependency-injection-services"></a><span data-ttu-id="8324b-282">Abhängigkeitsinjektion (Dienste)</span><span class="sxs-lookup"><span data-stu-id="8324b-282">Dependency injection (services)</span></span>

<span data-ttu-id="8324b-283">ASP.NET Core verfügt über ein integriertes DI-Framework (Dependency Injection), durch das konfigurierte Dienste für die Klassen einer App bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-283">ASP.NET Core has a built-in dependency injection (DI) framework that makes configured services available to an app's classes.</span></span> <span data-ttu-id="8324b-284">Eine Möglichkeit zum Abrufen einer Instanz eines Diensts in einer Klasse ist das Erstellen eines Konstruktors mit einem Parameter des erforderlichen Typs.</span><span class="sxs-lookup"><span data-stu-id="8324b-284">One way to get an instance of a service in a class is to create a constructor with a parameter of the required type.</span></span> <span data-ttu-id="8324b-285">Der Parameter kann der Diensttyp oder eine Schnittstelle sein.</span><span class="sxs-lookup"><span data-stu-id="8324b-285">The parameter can be the service type or an interface.</span></span> <span data-ttu-id="8324b-286">Das DI-System stellt den Dienst zur Laufzeit bereit.</span><span class="sxs-lookup"><span data-stu-id="8324b-286">The DI system provides the service at runtime.</span></span>

<span data-ttu-id="8324b-287">Hier ist eine Klasse, in der Dependency Injection verwendet wird, um ein Entity Framework Core-Kontextobjekt abzurufen.</span><span class="sxs-lookup"><span data-stu-id="8324b-287">Here's a class that uses DI to get an Entity Framework Core context object.</span></span> <span data-ttu-id="8324b-288">Die hervorgehobene Zeile ist ein Beispiel für die Konstruktorinjektion:</span><span class="sxs-lookup"><span data-stu-id="8324b-288">The highlighted line is an example of constructor injection:</span></span>

[!code-csharp[](index/snapshots/2.x/Index.cshtml.cs?highlight=5)]

<span data-ttu-id="8324b-289">Obwohl die Dependency Injection integriert ist, können Sie hier, falls gewünscht, einen IoC-Container eines Drittanbieters einbinden.</span><span class="sxs-lookup"><span data-stu-id="8324b-289">While DI is built in, it's designed to let you plug in a third-party Inversion of Control (IoC) container if you prefer.</span></span>

<span data-ttu-id="8324b-290">Weitere Informationen finden Sie unter <xref:fundamentals/dependency-injection>.</span><span class="sxs-lookup"><span data-stu-id="8324b-290">For more information, see <xref:fundamentals/dependency-injection>.</span></span>

## <a name="middleware"></a><span data-ttu-id="8324b-291">Middleware</span><span class="sxs-lookup"><span data-stu-id="8324b-291">Middleware</span></span>

<span data-ttu-id="8324b-292">Die Pipeline zur Anforderungsverarbeitung besteht aus mehreren Middlewarekomponenten.</span><span class="sxs-lookup"><span data-stu-id="8324b-292">The request handling pipeline is composed as a series of middleware components.</span></span> <span data-ttu-id="8324b-293">Jede Komponente führt asynchrone Operationen in einem `HttpContext` aus und ruft anschließend entweder die nächste Middleware in der Pipeline auf oder beendet die Anforderung.</span><span class="sxs-lookup"><span data-stu-id="8324b-293">Each component performs asynchronous operations on an `HttpContext` and then either invokes the next middleware in the pipeline or terminates the request.</span></span>

<span data-ttu-id="8324b-294">Gemäß Konvention wird eine Middlewarekomponente der Pipeline durch Aufrufen ihrer `Use...`-Erweiterungsmethode in der `Startup.Configure`-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="8324b-294">By convention, a middleware component is added to the pipeline by invoking its `Use...` extension method in the `Startup.Configure` method.</span></span> <span data-ttu-id="8324b-295">Um beispielsweise das Rendering statischer Dateien zu aktivieren, rufen Sie `UseStaticFiles` auf.</span><span class="sxs-lookup"><span data-stu-id="8324b-295">For example, to enable rendering of static files, call `UseStaticFiles`.</span></span>

<span data-ttu-id="8324b-296">Der hervorgehobene Code in dem folgenden Beispiel konfiguriert die Pipeline zur Anforderungsverarbeitung:</span><span class="sxs-lookup"><span data-stu-id="8324b-296">The highlighted code in the following example configures the request handling pipeline:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup1.cs?highlight=14-16)]

<span data-ttu-id="8324b-297">ASP.NET Core enthält eine Reihe umfangreicher integrierter Middleware. Außerdem können Sie benutzerdefinierte Middleware erstellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-297">ASP.NET Core includes a rich set of built-in middleware, and you can write custom middleware.</span></span>

<span data-ttu-id="8324b-298">Weitere Informationen finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-298">For more information, see <xref:fundamentals/middleware/index>.</span></span>

## <a name="host"></a><span data-ttu-id="8324b-299">Host</span><span class="sxs-lookup"><span data-stu-id="8324b-299">Host</span></span>

<span data-ttu-id="8324b-300">Beim Starten erstellt eine ASP.NET Core-App einen *Host*.</span><span class="sxs-lookup"><span data-stu-id="8324b-300">An ASP.NET Core app builds a *host* on startup.</span></span> <span data-ttu-id="8324b-301">Der Host ist ein Objekt, das alle Ressourcen der App kapselt, z. B.:</span><span class="sxs-lookup"><span data-stu-id="8324b-301">The host is an object that encapsulates all of the app's resources, such as:</span></span>

* <span data-ttu-id="8324b-302">eine HTTP-Serverimplementierung</span><span class="sxs-lookup"><span data-stu-id="8324b-302">An HTTP server implementation</span></span>
* <span data-ttu-id="8324b-303">Middlewarekomponenten</span><span class="sxs-lookup"><span data-stu-id="8324b-303">Middleware components</span></span>
* <span data-ttu-id="8324b-304">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="8324b-304">Logging</span></span>
* <span data-ttu-id="8324b-305">DI</span><span class="sxs-lookup"><span data-stu-id="8324b-305">DI</span></span>
* <span data-ttu-id="8324b-306">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8324b-306">Configuration</span></span>

<span data-ttu-id="8324b-307">Der wichtigste Grund für das Einschließen aller unabhängigen Ressourcen der App in einem Objekt ist die Verwaltung der Lebensdauer: steuern von Start und ordnungsgemäßem Herunterfahren der App.</span><span class="sxs-lookup"><span data-stu-id="8324b-307">The main reason for including all of the app's interdependent resources in one object is lifetime management: control over app startup and graceful shutdown.</span></span>

<span data-ttu-id="8324b-308">Es stehen zwei Hosts zur Verfügung: der Webhost und der generische Host.</span><span class="sxs-lookup"><span data-stu-id="8324b-308">Two hosts are available: the Web Host and the Generic Host.</span></span> <span data-ttu-id="8324b-309">In ASP.NET Core 2.x ist der generische Host nur für nicht webbasierte Szenarios verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8324b-309">In ASP.NET Core 2.x, the Generic Host is only for non-web scenarios.</span></span>

<span data-ttu-id="8324b-310">Der Code zum Erstellen eines Hosts befindet sich in `Program.Main`:</span><span class="sxs-lookup"><span data-stu-id="8324b-310">The code to create a host is in `Program.Main`:</span></span>

[!code-csharp[](index/snapshots/2.x/Program1.cs)]

<span data-ttu-id="8324b-311">Die Methode `CreateDefaultBuilder` konfiguriert einen Host mit häufig verwendeten Optionen wie den folgenden:</span><span class="sxs-lookup"><span data-stu-id="8324b-311">The `CreateDefaultBuilder` method configures a host with commonly used options, such as the following:</span></span>

* <span data-ttu-id="8324b-312">Verwenden von [Kestrel](#servers) als Webserver, und aktivieren der Integration von Internetinformationsdiensten.</span><span class="sxs-lookup"><span data-stu-id="8324b-312">Use [Kestrel](#servers) as the web server and enable IIS integration.</span></span>
* <span data-ttu-id="8324b-313">Laden der Konfiguration aus *appsettings.json*, *appsettings.[EnvironmentName].json*, Umgebungsvariablen, Befehlszeilenargumenten und anderen Konfigurationsquellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-313">Load configuration from *appsettings.json*, *appsettings.{Environment Name}.json*, environment variables, command line arguments, and other configuration sources.</span></span>
* <span data-ttu-id="8324b-314">Senden von Protokollausgaben an die Konsole und Debuggen von Anbietern.</span><span class="sxs-lookup"><span data-stu-id="8324b-314">Send logging output to the console and debug providers.</span></span>

<span data-ttu-id="8324b-315">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host>.</span><span class="sxs-lookup"><span data-stu-id="8324b-315">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="non-web-scenarios"></a><span data-ttu-id="8324b-316">Nicht webbasierte Szenarios</span><span class="sxs-lookup"><span data-stu-id="8324b-316">Non-web scenarios</span></span>

<span data-ttu-id="8324b-317">Mit dem generischen Host können andere App-Typen querschnittliche Frameworkerweiterungen wie Protokollierung, Dependency Injection (DI), Konfiguration und Lebensdauerverwaltung der App verwenden.</span><span class="sxs-lookup"><span data-stu-id="8324b-317">The Generic Host allows other types of apps to use cross-cutting framework extensions, such as logging, dependency injection (DI), configuration, and app lifetime management.</span></span> <span data-ttu-id="8324b-318">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host> und <xref:fundamentals/host/hosted-services>.</span><span class="sxs-lookup"><span data-stu-id="8324b-318">For more information, see <xref:fundamentals/host/generic-host> and <xref:fundamentals/host/hosted-services>.</span></span>

## <a name="servers"></a><span data-ttu-id="8324b-319">Server</span><span class="sxs-lookup"><span data-stu-id="8324b-319">Servers</span></span>

<span data-ttu-id="8324b-320">Eine ASP.NET Core-App verwendet eine HTTP-Serverimplementierung zum Lauschen auf HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="8324b-320">An ASP.NET Core app uses an HTTP server implementation to listen for HTTP requests.</span></span> <span data-ttu-id="8324b-321">Der Server sendet Anforderungen an die App in Form von mehreren [Anforderungsfunktionen](xref:fundamentals/request-features) in einem `HttpContext`.</span><span class="sxs-lookup"><span data-stu-id="8324b-321">The server surfaces requests to the app as a set of [request features](xref:fundamentals/request-features) composed into an `HttpContext`.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="8324b-322">Windows</span><span class="sxs-lookup"><span data-stu-id="8324b-322">Windows</span></span>](#tab/windows)

<span data-ttu-id="8324b-323">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="8324b-323">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="8324b-324">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="8324b-324">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="8324b-325">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-325">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="8324b-326">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-326">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="8324b-327">Der *IIS-HTTP-Server* ist ein Server für Windows, der IIS verwendet.</span><span class="sxs-lookup"><span data-stu-id="8324b-327">*IIS HTTP Server* is a server for windows that uses IIS.</span></span> <span data-ttu-id="8324b-328">Mit diesem Server werden die ASP.NET Core-App und IIS im gleichen Prozess ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-328">With this server, the ASP.NET Core app and IIS run in the same process.</span></span>
* <span data-ttu-id="8324b-329">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8324b-329">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="8324b-330">macOS</span><span class="sxs-lookup"><span data-stu-id="8324b-330">macOS</span></span>](#tab/macos)

<span data-ttu-id="8324b-331">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="8324b-331">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8324b-332">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-332">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8324b-333">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-333">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="8324b-334">Linux</span><span class="sxs-lookup"><span data-stu-id="8324b-334">Linux</span></span>](#tab/linux)

<span data-ttu-id="8324b-335">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="8324b-335">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8324b-336">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-336">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8324b-337">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-337">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="8324b-338">Windows</span><span class="sxs-lookup"><span data-stu-id="8324b-338">Windows</span></span>](#tab/windows)

<span data-ttu-id="8324b-339">Die folgenden Serverimplementierungen werden von ASP.NET Core bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="8324b-339">ASP.NET Core provides the following server implementations:</span></span>

* <span data-ttu-id="8324b-340">*Kestrel* ist ein plattformübergreifender Webserver.</span><span class="sxs-lookup"><span data-stu-id="8324b-340">*Kestrel* is a cross-platform web server.</span></span> <span data-ttu-id="8324b-341">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [IIS](https://www.iis.net/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-341">Kestrel is often run in a reverse proxy configuration using [IIS](https://www.iis.net/).</span></span> <span data-ttu-id="8324b-342">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-342">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span>
* <span data-ttu-id="8324b-343">*HTTP.sys* ist ein Server für Windows, der nicht mit IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8324b-343">*HTTP.sys* is a server for Windows that isn't used with IIS.</span></span>

# <a name="macos"></a>[<span data-ttu-id="8324b-344">macOS</span><span class="sxs-lookup"><span data-stu-id="8324b-344">macOS</span></span>](#tab/macos)

<span data-ttu-id="8324b-345">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="8324b-345">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8324b-346">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-346">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8324b-347">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-347">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

# <a name="linux"></a>[<span data-ttu-id="8324b-348">Linux</span><span class="sxs-lookup"><span data-stu-id="8324b-348">Linux</span></span>](#tab/linux)

<span data-ttu-id="8324b-349">ASP.NET Core bietet die plattformübergreifende Serverimplementierung von *Kestrel*.</span><span class="sxs-lookup"><span data-stu-id="8324b-349">ASP.NET Core provides the *Kestrel* cross-platform server implementation.</span></span> <span data-ttu-id="8324b-350">Kestrel kann als öffentlich zugänglicher Edgeserver ausgeführt werden, der direkt mit dem Internet verbunden ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-350">Kestrel can be run as a public-facing edge server exposed directly to the Internet.</span></span> <span data-ttu-id="8324b-351">Kestrel wird häufig in einer Reverseproxykonfiguration unter Verwendung von [Nginx](https://nginx.org) oder [Apache](https://httpd.apache.org/) ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="8324b-351">Kestrel is often run in a reverse proxy configuration with [Nginx](https://nginx.org) or [Apache](https://httpd.apache.org/).</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8324b-352">Weitere Informationen finden Sie unter <xref:fundamentals/servers/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-352">For more information, see <xref:fundamentals/servers/index>.</span></span>

## <a name="configuration"></a><span data-ttu-id="8324b-353">Konfiguration</span><span class="sxs-lookup"><span data-stu-id="8324b-353">Configuration</span></span>

<span data-ttu-id="8324b-354">ASP.NET Core bietet ein Konfigurationsframework, das Einstellungen als Name/Wert-Paare aus einer geordneten Menge von Konfigurationsanbietern abruft.</span><span class="sxs-lookup"><span data-stu-id="8324b-354">ASP.NET Core provides a configuration framework that gets settings as name-value pairs from an ordered set of configuration providers.</span></span> <span data-ttu-id="8324b-355">Integrierte Konfigurationsanbieter gibt es für zahlreiche Quellen wie *.json*-Dateien, *.xml*-Dateien, Umgebungsvariablen und Befehlszeilenargumente.</span><span class="sxs-lookup"><span data-stu-id="8324b-355">There are built-in configuration providers for a variety of sources, such as *.json* files, *.xml* files, environment variables, and command-line arguments.</span></span> <span data-ttu-id="8324b-356">Sie können auch benutzerdefinierte Konfigurationsanbieter schreiben.</span><span class="sxs-lookup"><span data-stu-id="8324b-356">You can also write custom configuration providers.</span></span>

<span data-ttu-id="8324b-357">Zum Beispiel könnten Sie angeben, dass die Konfiguration aus *appsettings.json* und Umgebungsvariablen stammt.</span><span class="sxs-lookup"><span data-stu-id="8324b-357">For example, you could specify that configuration comes from *appsettings.json* and environment variables.</span></span> <span data-ttu-id="8324b-358">Wenn dann der *ConnectionString*-Wert angefordert wird, sucht das Framework zuerst in der *appsettings.json*-Datei.</span><span class="sxs-lookup"><span data-stu-id="8324b-358">Then when the value of *ConnectionString* is requested, the framework looks first in the *appsettings.json* file.</span></span> <span data-ttu-id="8324b-359">Wenn der Wert sowohl dort als auch in einer Umgebungsvariablen gefunden wird, hat der Wert aus der Umgebungsvariablen Vorrang.</span><span class="sxs-lookup"><span data-stu-id="8324b-359">If the value is found there but also in an environment variable, the value from the environment variable would take precedence.</span></span>

<span data-ttu-id="8324b-360">Zum Verwalten von vertraulichen Konfigurationsdaten wie Passwörtern bietet ASP.NET Core ein [Geheimnisverwaltungstool](xref:security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="8324b-360">For managing confidential configuration data such as passwords, ASP.NET Core provides a [Secret Manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="8324b-361">Für Produktionsgeheimnisse empfehlen wir [Azure Key Vault](xref:security/key-vault-configuration).</span><span class="sxs-lookup"><span data-stu-id="8324b-361">For production secrets, we recommend [Azure Key Vault](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="8324b-362">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-362">For more information, see <xref:fundamentals/configuration/index>.</span></span>

## <a name="options"></a><span data-ttu-id="8324b-363">Optionen</span><span class="sxs-lookup"><span data-stu-id="8324b-363">Options</span></span>

<span data-ttu-id="8324b-364">Wo möglich, folgt ASP.NET Core dem *Optionsmuster* zum Speichern und Abrufen von Konfigurationswerten.</span><span class="sxs-lookup"><span data-stu-id="8324b-364">Where possible, ASP.NET Core follows the *options pattern* for storing and retrieving configuration values.</span></span> <span data-ttu-id="8324b-365">Das Optionsmuster verwendet Klassen, um Gruppen von zusammengehörigen Einstellungen darzustellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-365">The options pattern uses classes to represent groups of related settings.</span></span>

<span data-ttu-id="8324b-366">Im folgenden Codebeispiel werden WebSockets-Optionen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="8324b-366">For example, the following code sets WebSockets options:</span></span>

```csharp
var options = new WebSocketOptions  
{  
   KeepAliveInterval = TimeSpan.FromSeconds(120),  
   ReceiveBufferSize = 4096
};  
app.UseWebSockets(options);
```

<span data-ttu-id="8324b-367">Weitere Informationen finden Sie unter <xref:fundamentals/configuration/options>.</span><span class="sxs-lookup"><span data-stu-id="8324b-367">For more information, see <xref:fundamentals/configuration/options>.</span></span>

## <a name="environments"></a><span data-ttu-id="8324b-368">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="8324b-368">Environments</span></span>

<span data-ttu-id="8324b-369">Ausführungsumgebungen wie *Entwicklung*, *Staging* und *Produktion* sind in ASP.NET Core von besonderer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="8324b-369">Execution environments, such as *Development*, *Staging*, and *Production*, are a first-class notion in ASP.NET Core.</span></span> <span data-ttu-id="8324b-370">Um die Umgebung anzugeben, in der eine App ausgeführt wird, stellen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable ein.</span><span class="sxs-lookup"><span data-stu-id="8324b-370">You can specify the environment an app is running in by setting the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span> <span data-ttu-id="8324b-371">ASP.NET Core liest diese Umgebungsvariable beim Start der App und speichert den Wert in einer `IHostingEnvironment`-Implementierung.</span><span class="sxs-lookup"><span data-stu-id="8324b-371">ASP.NET Core reads that environment variable at app startup and stores the value in an `IHostingEnvironment` implementation.</span></span> <span data-ttu-id="8324b-372">Das Umgebungsobjekt ist in der gesamten App mithilfe der Dependency Injection verfügbar.</span><span class="sxs-lookup"><span data-stu-id="8324b-372">The environment object is available anywhere in the app via DI.</span></span>

<span data-ttu-id="8324b-373">Im folgenden Codebeispiel aus der `Startup`-Klasse wird die App so konfiguriert, dass detaillierte Fehlerinformationen nur bereitgestellt werden, wenn die App in der Entwicklung ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="8324b-373">The following sample code from the `Startup` class configures the app to provide detailed error information only when it runs in development:</span></span>

[!code-csharp[](index/snapshots/2.x/Startup2.cs?highlight=3-6)]

<span data-ttu-id="8324b-374">Weitere Informationen finden Sie unter <xref:fundamentals/environments>.</span><span class="sxs-lookup"><span data-stu-id="8324b-374">For more information, see <xref:fundamentals/environments>.</span></span>

## <a name="logging"></a><span data-ttu-id="8324b-375">Protokollierung</span><span class="sxs-lookup"><span data-stu-id="8324b-375">Logging</span></span>

<span data-ttu-id="8324b-376">ASP.NET Core unterstützt eine Protokollierungs-API, die mit einer Vielzahl von integrierten Protokollierungsanbietern und Drittanbieter-Protokollierungslösungen zusammenarbeitet.</span><span class="sxs-lookup"><span data-stu-id="8324b-376">ASP.NET Core supports a logging API that works with a variety of built-in and third-party logging providers.</span></span> <span data-ttu-id="8324b-377">Zu den verfügbaren Anbietern gehören folgende:</span><span class="sxs-lookup"><span data-stu-id="8324b-377">Available providers include the following:</span></span>

* <span data-ttu-id="8324b-378">Konsole</span><span class="sxs-lookup"><span data-stu-id="8324b-378">Console</span></span>
* <span data-ttu-id="8324b-379">Debug</span><span class="sxs-lookup"><span data-stu-id="8324b-379">Debug</span></span>
* <span data-ttu-id="8324b-380">Ereignisablaufverfolgung unter Windows</span><span class="sxs-lookup"><span data-stu-id="8324b-380">Event Tracing on Windows</span></span>
* <span data-ttu-id="8324b-381">Windows-Ereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="8324b-381">Windows Event Log</span></span>
* <span data-ttu-id="8324b-382">TraceSource</span><span class="sxs-lookup"><span data-stu-id="8324b-382">TraceSource</span></span>
* <span data-ttu-id="8324b-383">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="8324b-383">Azure App Service</span></span>
* <span data-ttu-id="8324b-384">Azure Application Insights</span><span class="sxs-lookup"><span data-stu-id="8324b-384">Azure Application Insights</span></span>

<span data-ttu-id="8324b-385">Schreiben Sie Protokolle aus jeder Stelle im Code einer App, indem Sie ein `ILogger`-Objekt aus Dependency Injection abrufen und Protokollmethoden aufrufen.</span><span class="sxs-lookup"><span data-stu-id="8324b-385">Write logs from anywhere in an app's code by getting an `ILogger` object from DI and calling log methods.</span></span>

<span data-ttu-id="8324b-386">Hier ist ein Beispielcode, in dem ein `ILogger`-Objekt mit Konstruktorinjektion und den hervorgehobenen Aufrufen der Protokollierungsmethode verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="8324b-386">Here's sample code that uses an `ILogger` object, with constructor injection and the logging method calls highlighted.</span></span>

[!code-csharp[](index/snapshots/2.x/TodoController.cs?highlight=5,13,17)]

<span data-ttu-id="8324b-387">Mithilfe der `ILogger`-Schnittstelle können Sie eine beliebige Anzahl von Feldern an den Protokollierungsanbieter übergeben.</span><span class="sxs-lookup"><span data-stu-id="8324b-387">The `ILogger` interface lets you pass any number of fields to the logging provider.</span></span> <span data-ttu-id="8324b-388">Die Felder werden häufig dazu verwendet, eine Meldungszeichenfolge zu erstellen, der Anbieter kann sie aber auch als einzelne Felder an einen Datenspeicher senden.</span><span class="sxs-lookup"><span data-stu-id="8324b-388">The fields are commonly used to construct a message string, but the provider can also send them as separate fields to a data store.</span></span> <span data-ttu-id="8324b-389">Dieses Funktion ermöglicht Protokollierungsanbietern das Implementieren von [semantischer Protokollierung, auch bezeichnet als strukturierte Protokollierung](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span><span class="sxs-lookup"><span data-stu-id="8324b-389">This feature makes it possible for logging providers to implement [semantic logging, also known as structured logging](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).</span></span>

<span data-ttu-id="8324b-390">Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="8324b-390">For more information, see <xref:fundamentals/logging/index>.</span></span>

## <a name="routing"></a><span data-ttu-id="8324b-391">Routing</span><span class="sxs-lookup"><span data-stu-id="8324b-391">Routing</span></span>

<span data-ttu-id="8324b-392">Eine *Route* ist ein URL-Muster, das einem Handler zugeordnet ist.</span><span class="sxs-lookup"><span data-stu-id="8324b-392">A *route* is a URL pattern that is mapped to a handler.</span></span> <span data-ttu-id="8324b-393">Der Handler ist normalerweise eine Razor-Seite, eine Aktionsmethode in einem MVC-Controller oder einer Middleware.</span><span class="sxs-lookup"><span data-stu-id="8324b-393">The handler is typically a Razor page, an action method in an MVC controller, or a middleware.</span></span> <span data-ttu-id="8324b-394">Mit ASP.NET Core-Routing können Sie steuern, welche URLs von Ihrer App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-394">ASP.NET Core routing gives you control over the URLs used by your app.</span></span>

<span data-ttu-id="8324b-395">Weitere Informationen finden Sie unter <xref:fundamentals/routing>.</span><span class="sxs-lookup"><span data-stu-id="8324b-395">For more information, see <xref:fundamentals/routing>.</span></span>

## <a name="error-handling"></a><span data-ttu-id="8324b-396">Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="8324b-396">Error handling</span></span>

<span data-ttu-id="8324b-397">ASP.NET Core verfügt über integrierte Funktionen zur Fehlerbehandlung wie beispielsweise:</span><span class="sxs-lookup"><span data-stu-id="8324b-397">ASP.NET Core has built-in features for handling errors, such as:</span></span>

* <span data-ttu-id="8324b-398">eine Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="8324b-398">A developer exception page</span></span>
* <span data-ttu-id="8324b-399">benutzerdefinierte Fehlerseiten</span><span class="sxs-lookup"><span data-stu-id="8324b-399">Custom error pages</span></span>
* <span data-ttu-id="8324b-400">statische Statuscodeseiten</span><span class="sxs-lookup"><span data-stu-id="8324b-400">Static status code pages</span></span>
* <span data-ttu-id="8324b-401">Fehlerbehandlung während des Starts</span><span class="sxs-lookup"><span data-stu-id="8324b-401">Startup exception handling</span></span>

<span data-ttu-id="8324b-402">Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.</span><span class="sxs-lookup"><span data-stu-id="8324b-402">For more information, see <xref:fundamentals/error-handling>.</span></span>

## <a name="make-http-requests"></a><span data-ttu-id="8324b-403">Übermitteln von HTTP-Anforderungen</span><span class="sxs-lookup"><span data-stu-id="8324b-403">Make HTTP requests</span></span>

<span data-ttu-id="8324b-404">Eine Implementierung von `IHttpClientFactory` ist verfügbar zum Erstellen von `HttpClient`-Instanzen.</span><span class="sxs-lookup"><span data-stu-id="8324b-404">An implementation of `IHttpClientFactory` is available for creating `HttpClient` instances.</span></span> <span data-ttu-id="8324b-405">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="8324b-405">The factory:</span></span>

* <span data-ttu-id="8324b-406">Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten.</span><span class="sxs-lookup"><span data-stu-id="8324b-406">Provides a central location for naming and configuring logical `HttpClient` instances.</span></span> <span data-ttu-id="8324b-407">Zum Beispiel kann ein *github*-Client für den Zugriff auf GitHub registriert und konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-407">For example, a *github* client can be registered and configured to access GitHub.</span></span> <span data-ttu-id="8324b-408">Ein Standard-Client kann für andere Zwecke registriert werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-408">A default client can be registered for other purposes.</span></span>
* <span data-ttu-id="8324b-409">Unterstützt die Registrierung und Verkettung von mehreren delegierenden Handlern, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8324b-409">Supports registration and chaining of multiple delegating handlers to build an outgoing request middleware pipeline.</span></span> <span data-ttu-id="8324b-410">Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="8324b-410">This pattern is similar to the inbound middleware pipeline in ASP.NET Core.</span></span> <span data-ttu-id="8324b-411">Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.</span><span class="sxs-lookup"><span data-stu-id="8324b-411">The pattern provides a mechanism to manage cross-cutting concerns around HTTP requests, including caching, error handling, serialization, and logging.</span></span>
* <span data-ttu-id="8324b-412">Integrierbar in *Polly*, eine beliebte Drittanbieter-Bibliothek zur Behandlung vorübergehender Fehler.</span><span class="sxs-lookup"><span data-stu-id="8324b-412">Integrates with *Polly*, a popular third-party library for transient fault handling.</span></span>
* <span data-ttu-id="8324b-413">Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.</span><span class="sxs-lookup"><span data-stu-id="8324b-413">Manages the pooling and lifetime of underlying `HttpClientMessageHandler` instances to avoid common DNS problems that occur when manually managing `HttpClient` lifetimes.</span></span>
* <span data-ttu-id="8324b-414">Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="8324b-414">Adds a configurable logging experience (via `ILogger`) for all requests sent through clients created by the factory.</span></span>

<span data-ttu-id="8324b-415">Weitere Informationen finden Sie unter <xref:fundamentals/http-requests>.</span><span class="sxs-lookup"><span data-stu-id="8324b-415">For more information, see <xref:fundamentals/http-requests>.</span></span>

## <a name="content-root"></a><span data-ttu-id="8324b-416">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="8324b-416">Content root</span></span>

<span data-ttu-id="8324b-417">Der Inhaltsstamm ist der Basispfad zu Folgendem:</span><span class="sxs-lookup"><span data-stu-id="8324b-417">The content root is the base path to the:</span></span>

* <span data-ttu-id="8324b-418">Der ausführbaren Datei ( *.exe*), die die App hostet.</span><span class="sxs-lookup"><span data-stu-id="8324b-418">Executable hosting the app (*.exe*).</span></span>
* <span data-ttu-id="8324b-419">Kompilierten Assemblys, die die App bilden ( *.dll*).</span><span class="sxs-lookup"><span data-stu-id="8324b-419">Compiled assemblies that make up the app (*.dll*).</span></span>
* <span data-ttu-id="8324b-420">Inhaltsdateien ohne Code, die von der App verwendet werden, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="8324b-420">Non-code content files used by the app, such as:</span></span>
  * <span data-ttu-id="8324b-421">Razor-Dateien ( *.cshtml*, *.razor*)</span><span class="sxs-lookup"><span data-stu-id="8324b-421">Razor files (*.cshtml*, *.razor*)</span></span>
  * <span data-ttu-id="8324b-422">Konfigurationsdateien ( *.json*, *.xml*)</span><span class="sxs-lookup"><span data-stu-id="8324b-422">Configuration files (*.json*, *.xml*)</span></span>
  * <span data-ttu-id="8324b-423">Datendateien ( *.db*)</span><span class="sxs-lookup"><span data-stu-id="8324b-423">Data files (*.db*)</span></span>
* <span data-ttu-id="8324b-424">[Webstamm](#web-root), in der Regel der veröffentlichte Ordner *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="8324b-424">[Web root](#web-root), typically the published *wwwroot* folder.</span></span>

<span data-ttu-id="8324b-425">Entwicklungsphase:</span><span class="sxs-lookup"><span data-stu-id="8324b-425">During development:</span></span>

* <span data-ttu-id="8324b-426">Der Inhaltsstamm ist standardmäßig auf das Stammverzeichnis des Projekts festgelegt.</span><span class="sxs-lookup"><span data-stu-id="8324b-426">The content root defaults to the project's root directory.</span></span>
* <span data-ttu-id="8324b-427">Das Stammverzeichnis des Projekts dient zum Erstellen von Folgendem:</span><span class="sxs-lookup"><span data-stu-id="8324b-427">The project's root directory is used to create the:</span></span>
  * <span data-ttu-id="8324b-428">Pfad zu den Inhaltsdateien ohne Code der App im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8324b-428">Path to the app's non-code content files in the project's root directory.</span></span>
  * <span data-ttu-id="8324b-429">[Webstamm](#web-root), in der Regel der Ordner *wwwroot* im Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8324b-429">[Web root](#web-root), typically the *wwwroot* folder in the project's root directory.</span></span>

<span data-ttu-id="8324b-430">Ein alternativer Inhaltsstammpfad kann beim [Erstellen des Hosts](#host) angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-430">An alternative content root path can be specified when [building the host](#host).</span></span> <span data-ttu-id="8324b-431">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#content-root>.</span><span class="sxs-lookup"><span data-stu-id="8324b-431">For more information, see <xref:fundamentals/host/web-host#content-root>.</span></span>

## <a name="web-root"></a><span data-ttu-id="8324b-432">Webstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="8324b-432">Web root</span></span>

<span data-ttu-id="8324b-433">Der Webstamm ist der Basispfad zu öffentlichen, statischen Ressourcendateien ohne Code, wie z. B.:</span><span class="sxs-lookup"><span data-stu-id="8324b-433">The web root is the base path to public, non-code, static resource files, such as:</span></span>

* <span data-ttu-id="8324b-434">Stylesheets ( *.css*)</span><span class="sxs-lookup"><span data-stu-id="8324b-434">Stylesheets (*.css*)</span></span>
* <span data-ttu-id="8324b-435">JavaScript ( *.js*)</span><span class="sxs-lookup"><span data-stu-id="8324b-435">JavaScript (*.js*)</span></span>
* <span data-ttu-id="8324b-436">Bilder ( *.png*, *.jpg*)</span><span class="sxs-lookup"><span data-stu-id="8324b-436">Images (*.png*, *.jpg*)</span></span>

<span data-ttu-id="8324b-437">Statische Dateien werden standardmäßig nur aus dem Webstammverzeichnis (samt Unterverzeichnissen) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8324b-437">Static files are only served by default from the web root directory (and sub-directories).</span></span>

<span data-ttu-id="8324b-438">Der Webstammpfad ist standardmäßig auf *{Inhaltsstamm}/wwwroot* festgelegt, doch beim [Erstellen des Hosts](#host) kann ein anderer Webstamm angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="8324b-438">The web root path defaults to *{content root}/wwwroot*, but a different web root can be specified when [building the host](#host).</span></span> <span data-ttu-id="8324b-439">Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/host/web-host#web-root).</span><span class="sxs-lookup"><span data-stu-id="8324b-439">For more information, see [Web root](xref:fundamentals/host/web-host#web-root).</span></span>

<span data-ttu-id="8324b-440">Verhindern Sie das Veröffentlichen von Datei in *wwwroot* über [\<Inhalt > Projektelement](/visualstudio/msbuild/common-msbuild-project-items#content) in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="8324b-440">Prevent publishing files in *wwwroot* with the [\<Content> project item](/visualstudio/msbuild/common-msbuild-project-items#content) in the project file.</span></span> <span data-ttu-id="8324b-441">Im folgenden Beispiel wird verhindert, dass Inhalte im *wwwroot/local*-Verzeichnis und in Unterverzeichnisse veröffentlicht werden:</span><span class="sxs-lookup"><span data-stu-id="8324b-441">The following example prevents publishing content in the *wwwroot/local* directory and sub-directories:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot\local\**\*.*" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="8324b-442">In Razor-Dateien ( *.cshtml*) zeigen Tilde und Schrägstrich (`~/`) auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="8324b-442">In Razor (*.cshtml*) files, the tilde-slash (`~/`) points to the web root.</span></span> <span data-ttu-id="8324b-443">Ein mit `~/` beginnender Pfad wird als *virtueller Pfad* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="8324b-443">A path beginning with `~/` is referred to as a *virtual path*.</span></span>

<span data-ttu-id="8324b-444">Weitere Informationen finden Sie unter <xref:fundamentals/static-files>.</span><span class="sxs-lookup"><span data-stu-id="8324b-444">For more information, see <xref:fundamentals/static-files>.</span></span>

::: moniker-end
