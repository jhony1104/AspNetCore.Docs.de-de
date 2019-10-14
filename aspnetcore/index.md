---
title: Einführung in ASP.NET Core
author: rick-anderson
description: Dieser Artikel enthält eine Einführung in ASP.NET Core, ein plattformübergreifendes, leistungsstarkes Open-Source-Framework für das Erstellen moderner, cloudbasierter Anwendungen, die mit dem Internet verbunden sind.
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2019
uid: index
ms.openlocfilehash: 1ccc1f5d095833e89fc20127ee23b8fa3dc4c79f
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2019
ms.locfileid: "72289051"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="f22a1-103">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="f22a1-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="f22a1-104">Von [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="f22a1-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="f22a1-105">ASP.NET Core ist ein plattformübergreifendes, leistungsstarkes [Open-Source](https://github.com/aspnet/home)-Framework zum Erstellen moderner, cloudbasierter mit dem Internet verbundener Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="f22a1-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="f22a1-106">ASP.NET Core ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="f22a1-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="f22a1-107">Erstellen von Web-Apps und -diensten, [IoT-Apps](https://www.microsoft.com/internet-of-things/) und mobilen Back-Ends.</span><span class="sxs-lookup"><span data-stu-id="f22a1-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="f22a1-108">Verwenden Ihrer bevorzugten Entwicklungstools unter Windows, macOS und Linux</span><span class="sxs-lookup"><span data-stu-id="f22a1-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="f22a1-109">Bereitstellen in der Cloud oder im lokalen System</span><span class="sxs-lookup"><span data-stu-id="f22a1-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="f22a1-110">Ausführen in [.NET Core oder .NET Framework](/dotnet/articles/standard/choosing-core-framework-server)</span><span class="sxs-lookup"><span data-stu-id="f22a1-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="f22a1-111">Was spricht für ASP.NET Core?</span><span class="sxs-lookup"><span data-stu-id="f22a1-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="f22a1-112">Millionen von Entwicklern setzen bei der Erstellung von Web-Apps auf [ASP.NET 4.x](/aspnet/overview).</span><span class="sxs-lookup"><span data-stu-id="f22a1-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="f22a1-113">Bei ASP.NET Core handelt es sich um eine Neugestaltung von ASP.NET 4.x mit Änderungen an der Architektur, die ein schlankeres Framework mit größerer Modularität ergeben.</span><span class="sxs-lookup"><span data-stu-id="f22a1-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="f22a1-114">Erstellen von Web-APIs und Webbenutzeroberflächen mithilfe von ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="f22a1-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="f22a1-115">ASP.NET Core MVC bietet Funktionen zum Erstellen von [Web-APIs](xref:tutorials/first-web-api) und [Web-Apps](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="f22a1-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="f22a1-116">Das Muster [Model-View-Controller (MVC)](xref:mvc/overview) sorgt dafür, dass Ihre Web-APIs und Web-Apps testfähig sind.</span><span class="sxs-lookup"><span data-stu-id="f22a1-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="f22a1-117">[Razor Pages](xref:razor-pages/index) ist ein seitenbasiertes Programmiermodell, mit dem Sie Webbenutzeroberflächen einfacher und schneller erstellen können.</span><span class="sxs-lookup"><span data-stu-id="f22a1-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="f22a1-118">Das [Razor-Markup](xref:mvc/views/razor) bietet eine produktive Syntax für [Razor Pages](xref:razor-pages/index) und [MVC-Ansichten](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="f22a1-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="f22a1-119">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="f22a1-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="f22a1-120">Die integrierte Unterstützung für [mehrere Datenformate und Inhaltsaushandlung](xref:web-api/advanced/formatting) ermöglicht Ihren Web-APIs das Erreichen einer breiten Palette von Clients, wie z.B. Browser und Mobilgeräte.</span><span class="sxs-lookup"><span data-stu-id="f22a1-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="f22a1-121">Die [Modellbindung](xref:mvc/models/model-binding) ordnet Daten aus HTTP-Anforderungen automatisch Aktionsmethodenparametern zu.</span><span class="sxs-lookup"><span data-stu-id="f22a1-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="f22a1-122">Die [Modellvalidierung](xref:mvc/models/validation) führt automatisch eine client- und serverseitige Validierung aus.</span><span class="sxs-lookup"><span data-stu-id="f22a1-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="f22a1-123">Clientseitige Entwicklung</span><span class="sxs-lookup"><span data-stu-id="f22a1-123">Client-side development</span></span>

<span data-ttu-id="f22a1-124">ASP.NET Core integriert sich nahtlos in gängige clientseitige Frameworks und Bibliotheken, einschließlich [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react) und [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="f22a1-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="f22a1-125">Weitere Informationen finden Sie unter <xref:blazor/index> und verwandten Themen unter *Clientseitige Entwicklung*.</span><span class="sxs-lookup"><span data-stu-id="f22a1-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="f22a1-126">ASP.NET Core, das .NET Framework anzielt.</span><span class="sxs-lookup"><span data-stu-id="f22a1-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="f22a1-127">ASP.NET Core 2.x kann für .NET Core oder .NET Framework verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="f22a1-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="f22a1-128">ASP.NET Core-Apps, die .NET Framework anzielen, sind nicht plattformübergreifend, sondern können nur unter Windows ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="f22a1-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="f22a1-129">Für gewöhnlich besteht ASP.NET Core 2.x aus [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="f22a1-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="f22a1-130">Bibliotheken, die mit .NET Standard 2.0 geschrieben wurden, werden auf [jeder .NET-Plattform ausgeführt, die .NET Standard 2.0 implementiert](/dotnet/standard/net-standard#net-implementation-support).</span><span class="sxs-lookup"><span data-stu-id="f22a1-130">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="f22a1-131">ASP.NET Core 2.x wird unter .NET Framework-Versionen unterstützt, die .NET Standard 2.0 implementieren:</span><span class="sxs-lookup"><span data-stu-id="f22a1-131">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="f22a1-132">Die neueste Version von .NET Framework wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="f22a1-132">.NET Framework latest version is strongly recommended.</span></span>
* <span data-ttu-id="f22a1-133">.NET Framework 4.6.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="f22a1-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="f22a1-134">ASP.NET Core 3.0 und höher kann nur in .NET Core ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="f22a1-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="f22a1-135">Weitere Informationen zu dieser Änderung finden Sie im Blogbeitrag zu [kommenden Änderungen in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="f22a1-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="f22a1-136">Das Anzielen auf .NET Core bringt mit jedem Release mehr und mehr Vorteile mit sich.</span><span class="sxs-lookup"><span data-stu-id="f22a1-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="f22a1-137">Einige Vorteile von .NET Core gegenüber .NET Framework sind:</span><span class="sxs-lookup"><span data-stu-id="f22a1-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="f22a1-138">Plattformübergreifend</span><span class="sxs-lookup"><span data-stu-id="f22a1-138">Cross-platform.</span></span> <span data-ttu-id="f22a1-139">Wird unter macOS, Linux und Windows ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="f22a1-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="f22a1-140">Leistungssteigerung</span><span class="sxs-lookup"><span data-stu-id="f22a1-140">Improved performance</span></span>
* [<span data-ttu-id="f22a1-141">Parallele Versionsverwaltung</span><span class="sxs-lookup"><span data-stu-id="f22a1-141">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#a-need-for-side-by-side-of-net-versions-per-application-level)
* <span data-ttu-id="f22a1-142">Neue APIs</span><span class="sxs-lookup"><span data-stu-id="f22a1-142">New APIs</span></span>
* <span data-ttu-id="f22a1-143">Quelle öffnen</span><span class="sxs-lookup"><span data-stu-id="f22a1-143">Open source</span></span>

<span data-ttu-id="f22a1-144">Es wird daran gearbeitet, die API-Lücke von .NET Framework zu .NET Core zu schließen.</span><span class="sxs-lookup"><span data-stu-id="f22a1-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="f22a1-145">Das [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) stellt Tausende nur unter Windows verfügbare APIs in .NET Core zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="f22a1-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="f22a1-146">Diese APIs waren in .NET Core 1.x nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="f22a1-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="f22a1-147">Empfohlener Lernpfad</span><span class="sxs-lookup"><span data-stu-id="f22a1-147">Recommended learning path</span></span>

<span data-ttu-id="f22a1-148">Als Einführung in die Entwicklung von ASP.NET Core-Apps empfehlen wir die folgenden Tutorials und Artikel:</span><span class="sxs-lookup"><span data-stu-id="f22a1-148">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="f22a1-149">Folgen Sie einem Tutorial, das für den Typ von App bestimmt ist, den Sie entwickeln oder verwalten möchten:</span><span class="sxs-lookup"><span data-stu-id="f22a1-149">Follow a tutorial for the type of app you want to develop or maintain:</span></span>

   |<span data-ttu-id="f22a1-150">App-Typ</span><span class="sxs-lookup"><span data-stu-id="f22a1-150">App type</span></span>  |<span data-ttu-id="f22a1-151">Szenario</span><span class="sxs-lookup"><span data-stu-id="f22a1-151">Scenario</span></span>  |<span data-ttu-id="f22a1-152">Lernprogramm</span><span class="sxs-lookup"><span data-stu-id="f22a1-152">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="f22a1-153">Web-App</span><span class="sxs-lookup"><span data-stu-id="f22a1-153">Web app</span></span>                   | <span data-ttu-id="f22a1-154">Für Neuentwicklungen</span><span class="sxs-lookup"><span data-stu-id="f22a1-154">For new development</span></span>        |[<span data-ttu-id="f22a1-155">Erste Schritte mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="f22a1-155">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="f22a1-156">Web-App</span><span class="sxs-lookup"><span data-stu-id="f22a1-156">Web app</span></span>                   | <span data-ttu-id="f22a1-157">Für die Verwaltung einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="f22a1-157">For maintaining an MVC app</span></span> |[<span data-ttu-id="f22a1-158">Erste Schritte mit MVC</span><span class="sxs-lookup"><span data-stu-id="f22a1-158">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="f22a1-159">Web-API</span><span class="sxs-lookup"><span data-stu-id="f22a1-159">Web API</span></span>                   |                            |<span data-ttu-id="f22a1-160">[Erstellen einer Web-API](xref:tutorials/first-web-api)\*</span><span class="sxs-lookup"><span data-stu-id="f22a1-160">[Create a web API](xref:tutorials/first-web-api)\*</span></span>  |
   |<span data-ttu-id="f22a1-161">Echtzeit-App</span><span class="sxs-lookup"><span data-stu-id="f22a1-161">Real-time app</span></span>             |                            |[<span data-ttu-id="f22a1-162">Erste Schritte mit SignalR</span><span class="sxs-lookup"><span data-stu-id="f22a1-162">Get started with SignalR</span></span>](xref:tutorials/signalr) |
   |<span data-ttu-id="f22a1-163">Blazor-App</span><span class="sxs-lookup"><span data-stu-id="f22a1-163">Blazor app</span></span>                |                            |[<span data-ttu-id="f22a1-164">Erste Schritte mit Blazor</span><span class="sxs-lookup"><span data-stu-id="f22a1-164">Get started with Blazor</span></span>](xref:blazor/get-started) |
   |<span data-ttu-id="f22a1-165">Apps für Remoteprozeduraufrufe</span><span class="sxs-lookup"><span data-stu-id="f22a1-165">Remote Procedure Call app</span></span> |                            |[<span data-ttu-id="f22a1-166">Erste Schritte mit dem gRPC-Dienst</span><span class="sxs-lookup"><span data-stu-id="f22a1-166">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |

1. <span data-ttu-id="f22a1-167">Folgen Sie einem Tutorial, in dem die Grundlagen des Datenzugriffs erklärt werden:</span><span class="sxs-lookup"><span data-stu-id="f22a1-167">Follow a tutorial that shows how to do basic data access:</span></span>

   |<span data-ttu-id="f22a1-168">Szenario</span><span class="sxs-lookup"><span data-stu-id="f22a1-168">Scenario</span></span>  |<span data-ttu-id="f22a1-169">Lernprogramm</span><span class="sxs-lookup"><span data-stu-id="f22a1-169">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="f22a1-170">Für Neuentwicklungen</span><span class="sxs-lookup"><span data-stu-id="f22a1-170">For new development</span></span>        |[<span data-ttu-id="f22a1-171">Razor Pages mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f22a1-171">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="f22a1-172">Für die Verwaltung einer MVC-App</span><span class="sxs-lookup"><span data-stu-id="f22a1-172">For maintaining an MVC app</span></span> |[<span data-ttu-id="f22a1-173">MVC mit Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="f22a1-173">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

1. <span data-ttu-id="f22a1-174">Übersicht über ASP.NET Core-Features, die alle App-Typen bieten:</span><span class="sxs-lookup"><span data-stu-id="f22a1-174">Read an overview of ASP.NET Core features that apply to all app types:</span></span>

   * [<span data-ttu-id="f22a1-175">Grundlagen</span><span class="sxs-lookup"><span data-stu-id="f22a1-175">Fundamentals</span></span>](xref:fundamentals/index)

1. <span data-ttu-id="f22a1-176">Suchen Sie im Inhaltsverzeichnis nach weiteren interessanten Themen.</span><span class="sxs-lookup"><span data-stu-id="f22a1-176">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="f22a1-177">\* Es gibt ein neues [Web-API-Tutorial, dem Sie vollständig im Browser folgen können](https://docs.microsoft.com/learn/modules/build-web-api-net-core), ohne dass eine lokale Entwicklungsumgebung installiert werden muss.</span><span class="sxs-lookup"><span data-stu-id="f22a1-177">\* There is a new [web API tutorial that you follow entirely in the browser](https://docs.microsoft.com/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span>  <span data-ttu-id="f22a1-178">Der Code wird in einer [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) ausgeführt, und zum Testen wird [curl](https://curl.haxx.se/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="f22a1-178">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="f22a1-179">Herunterladen eines Beispiels</span><span class="sxs-lookup"><span data-stu-id="f22a1-179">How to download a sample</span></span>

<span data-ttu-id="f22a1-180">Viele der Artikel und Tutorials enthalten Links zu Beispielcode.</span><span class="sxs-lookup"><span data-stu-id="f22a1-180">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="f22a1-181">[Laden Sie die Zip-Datei des ASP.NET Repositorys herunter](https://codeload.github.com/aspnet/AspNetCore.Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="f22a1-181">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="f22a1-182">Entpacken Sie die Datei *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="f22a1-182">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="f22a1-183">Navigieren Sie mit der URL in der Beispielverknüpfung zum Beispielverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="f22a1-183">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="f22a1-184">Präprozessoranweisungen in Beispielcode</span><span class="sxs-lookup"><span data-stu-id="f22a1-184">Preprocessor directives in sample code</span></span>

<span data-ttu-id="f22a1-185">In Beispiel-Apps werden die Präprozessoranweisungen `#define` und `#if-#else/#elif-#endif` zum selektiven Kompilieren und Ausführen unterschiedlicher Abschnitte des Beispielcodes verwendet. So werden verschiedene Szenarios veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="f22a1-185">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="f22a1-186">Für die Beispiele, die diesen Ansatz verwenden, legen Sie die Anweisung `#define` am Anfang der C#-Dateien auf das Symbol fest, das dem Szenario zugeordnet ist, welches Sie ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="f22a1-186">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="f22a1-187">Für einige Beispiele müssen Sie möglicherweise das Symbol in mehreren Dateien definieren, um ein Szenario durchführen zu können.</span><span class="sxs-lookup"><span data-stu-id="f22a1-187">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="f22a1-188">Die folgende `#define`-Symbolliste gibt beispielsweise an, dass vier Szenarios verfügbar sind (ein Szenario pro Symbol).</span><span class="sxs-lookup"><span data-stu-id="f22a1-188">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="f22a1-189">Die aktuelle Beispielkonfiguration führt das `TemplateCode`-Szenario aus:</span><span class="sxs-lookup"><span data-stu-id="f22a1-189">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="f22a1-190">Damit das Beispiel das `ExpandDefault`-Szenario ausführt, definieren Sie das `ExpandDefault`-Symbol und lassen Sie die übrigen Symbole auskommentiert:</span><span class="sxs-lookup"><span data-stu-id="f22a1-190">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="f22a1-191">Weitere Informationen zur Verwendung von [C#-Präprozessoranweisungen](/dotnet/csharp/language-reference/preprocessor-directives/), um selektiv bestimmte Codeabschnitte zu kompilieren, finden Sie unter [#define (C#-Referenz)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) und [#if (C#-Referenz)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="f22a1-191">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="f22a1-192">Bereiche in Beispielcode</span><span class="sxs-lookup"><span data-stu-id="f22a1-192">Regions in sample code</span></span>

<span data-ttu-id="f22a1-193">Einige Beispiel-Apps enthalten Codeabschnitte, die von den C#-Anweisungen [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) und [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) umschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="f22a1-193">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="f22a1-194">Das Buildsystem der Dokumentation fügt diese Bereiche in den gerenderten Dokumentationsartikel ein.</span><span class="sxs-lookup"><span data-stu-id="f22a1-194">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="f22a1-195">Namen von Bereichen enthalten oft das Wort „snippet“ (Ausschnitt).</span><span class="sxs-lookup"><span data-stu-id="f22a1-195">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="f22a1-196">Im folgenden Beispiel ist ein Bereich mit dem Namen `snippet_WebHostDefaults` enthalten:</span><span class="sxs-lookup"><span data-stu-id="f22a1-196">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="f22a1-197">In der Markdowndatei wird auf den vorherigen C#-Codeausschnitt mit der folgenden Zeile verwiesen:</span><span class="sxs-lookup"><span data-stu-id="f22a1-197">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="f22a1-198">Sie können die Anweisungen `#region` und `#endregion`, die den Code umschließen, ohne Weiteres ignorieren (oder entfernen).</span><span class="sxs-lookup"><span data-stu-id="f22a1-198">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="f22a1-199">Ändern Sie jedoch den Code innerhalb dieser Anweisungen nicht, wenn Sie die im Artikel beschriebenen Beispielszenarios durchführen möchten.</span><span class="sxs-lookup"><span data-stu-id="f22a1-199">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="f22a1-200">Wenn Sie andere Szenarios ausprobieren möchten, können Sie den Code anpassen.</span><span class="sxs-lookup"><span data-stu-id="f22a1-200">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="f22a1-201">Weitere Informationen finden Sie unter [Contribute to the ASP.NET documentation: Code snippets (Mitwirken an der ASP.NET-Dokumentation: Codeausschnitte)](https://github.com/aspnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="f22a1-201">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/aspnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="f22a1-202">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="f22a1-202">Next steps</span></span>

<span data-ttu-id="f22a1-203">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="f22a1-203">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="f22a1-204">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="f22a1-204">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="f22a1-205">Im [wöchentlichen ASP.NET Community Standup](https://live.asp.net/) werden die Fortschritte und Pläne des Teams behandelt.</span><span class="sxs-lookup"><span data-stu-id="f22a1-205">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="f22a1-206">Zudem werden neue Blogs und Drittanbietersoftware vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="f22a1-206">It features new blogs and third-party software.</span></span>
