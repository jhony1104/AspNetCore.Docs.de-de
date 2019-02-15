---
title: Einführung in ASP.NET Core
author: rick-anderson
description: Dieser Artikel enthält eine Einführung in ASP.NET Core, ein plattformübergreifendes, leistungsstarkes Open-Source-Framework für das Erstellen moderner, cloudbasierter Anwendungen, die mit dem Internet verbunden sind.
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: index
ms.openlocfilehash: c3f07814bfab19a0f070e0b48b0d2ef6cfc1594e
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248159"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="40e0b-103">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40e0b-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="40e0b-104">Von [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="40e0b-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

<span data-ttu-id="40e0b-105">ASP.NET Core ist ein plattformübergreifendes, leistungsstarkes [Open-Source](https://github.com/aspnet/home)-Framework zum Erstellen moderner, cloudbasierter mit dem Internet verbundener Anwendungen.</span><span class="sxs-lookup"><span data-stu-id="40e0b-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/aspnet/home) framework for building modern, cloud-based, Internet-connected applications.</span></span> <span data-ttu-id="40e0b-106">ASP.NET Core ermöglicht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="40e0b-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="40e0b-107">Erstellen von Web-Apps und -diensten, [IoT-Apps](https://www.microsoft.com/internet-of-things/) und mobilen Back-Ends.</span><span class="sxs-lookup"><span data-stu-id="40e0b-107">Build web apps and services, [IoT](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="40e0b-108">Verwenden Ihrer bevorzugten Entwicklungstools unter Windows, macOS und Linux</span><span class="sxs-lookup"><span data-stu-id="40e0b-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="40e0b-109">Bereitstellen in der Cloud oder im lokalen System</span><span class="sxs-lookup"><span data-stu-id="40e0b-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="40e0b-110">Ausführen in [.NET Core oder .NET Framework](/dotnet/articles/standard/choosing-core-framework-server)</span><span class="sxs-lookup"><span data-stu-id="40e0b-110">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-use-aspnet-core"></a><span data-ttu-id="40e0b-111">Gründe für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40e0b-111">Why use ASP.NET Core?</span></span>

<span data-ttu-id="40e0b-112">Millionen von Entwicklern setzen bei der Erstellung von Web-Apps auf [ASP.NET 4.x](/aspnet/overview).</span><span class="sxs-lookup"><span data-stu-id="40e0b-112">Millions of developers have used (and continue to use) [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="40e0b-113">Bei ASP.NET Core handelt es sich um eine Neugestaltung von ASP.NET 4.x mit Änderungen an der Architektur, die ein schlankeres Framework mit größerer Modularität ergeben.</span><span class="sxs-lookup"><span data-stu-id="40e0b-113">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="40e0b-114">Erstellen von Web-APIs und Webbenutzeroberflächen mithilfe von ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="40e0b-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="40e0b-115">ASP.NET Core MVC bietet Funktionen zum Erstellen von [Web-APIs](xref:tutorials/first-web-api) und [Web-Apps](xref:tutorials/razor-pages/index):</span><span class="sxs-lookup"><span data-stu-id="40e0b-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="40e0b-116">Das Muster [Model-View-Controller (MVC)](xref:mvc/overview) sorgt dafür, dass Ihre Web-APIs und Web-Apps testfähig sind.</span><span class="sxs-lookup"><span data-stu-id="40e0b-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="40e0b-117">[Razor Pages](xref:razor-pages/index) ist ein seitenbasiertes Programmiermodell, mit dem Sie Webbenutzeroberflächen einfacher und schneller erstellen können.</span><span class="sxs-lookup"><span data-stu-id="40e0b-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="40e0b-118">Das [Razor-Markup](xref:mvc/views/razor) bietet eine produktive Syntax für [Razor Pages](xref:razor-pages/index) und [MVC-Ansichten](xref:mvc/views/overview).</span><span class="sxs-lookup"><span data-stu-id="40e0b-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="40e0b-119">[Taghilfsprogramme](xref:mvc/views/tag-helpers/intro) ermöglichen serverseitigem Code das Mitwirken am Erstellen und Rendern von HTML-Elementen in Razor-Dateien.</span><span class="sxs-lookup"><span data-stu-id="40e0b-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="40e0b-120">Die integrierte Unterstützung für [mehrere Datenformate und Inhaltsaushandlung](xref:web-api/advanced/formatting) ermöglicht Ihren Web-APIs das Erreichen einer breiten Palette von Clients, wie z.B. Browser und Mobilgeräte.</span><span class="sxs-lookup"><span data-stu-id="40e0b-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="40e0b-121">Die [Modellbindung](xref:mvc/models/model-binding) ordnet Daten aus HTTP-Anforderungen automatisch Aktionsmethodenparametern zu.</span><span class="sxs-lookup"><span data-stu-id="40e0b-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="40e0b-122">Die [Modellvalidierung](xref:mvc/models/validation) führt automatisch eine client- und serverseitige Validierung aus.</span><span class="sxs-lookup"><span data-stu-id="40e0b-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="40e0b-123">Clientseitige Entwicklung</span><span class="sxs-lookup"><span data-stu-id="40e0b-123">Client-side development</span></span>

<span data-ttu-id="40e0b-124">ASP.NET Core integriert sich nahtlos in gängige clientseitige Frameworks und Bibliotheken, einschließlich [Razor Components](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react) und [Bootstrap](https://getbootstrap.com/).</span><span class="sxs-lookup"><span data-stu-id="40e0b-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Razor Components](xref:razor-components/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="40e0b-125">Weitere Informationen finden Sie unter [Razor Components](xref:razor-components/index) und verwandten Themen unter *Clientseitige Entwicklung*.</span><span class="sxs-lookup"><span data-stu-id="40e0b-125">For more information, see [Razor Components](xref:razor-components/index) and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="40e0b-126">ASP.NET Core, das .NET Framework anzielt.</span><span class="sxs-lookup"><span data-stu-id="40e0b-126">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="40e0b-127">ASP.NET Core 2.x kann für .NET Core oder .NET Framework verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="40e0b-127">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="40e0b-128">ASP.NET Core-Apps, die .NET Framework anzielen, sind nicht plattformübergreifend, sondern können nur unter Windows ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40e0b-128">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="40e0b-129">Für gewöhnlich besteht ASP.NET Core 2.x aus [.NET Standard](/dotnet/standard/net-standard)-Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="40e0b-129">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="40e0b-130">Solange .NET Standard 2.0 unterstützt wird, können mit .NET Standard 2.0 geschriebene Apps überall ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40e0b-130">Apps written with .NET Standard 2.0 run anywhere that .NET Standard 2.0 is supported.</span></span>

<span data-ttu-id="40e0b-131">ASP.NET Core 2.x wird unter .NET Framework-Versionen unterstützt, die mit dem .NET Standard 2.0 kompatibel sind:</span><span class="sxs-lookup"><span data-stu-id="40e0b-131">ASP.NET Core 2.x is supported on .NET Framework versions compatible with .NET Standard 2.0:</span></span>

* <span data-ttu-id="40e0b-132">.NET Framework 4.7.1 und später wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="40e0b-132">.NET Framework 4.7.1 and later is strongly recommended.</span></span>
* <span data-ttu-id="40e0b-133">.NET Framework 4.6.1 und höher.</span><span class="sxs-lookup"><span data-stu-id="40e0b-133">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="40e0b-134">ASP.NET Core 3.0 und höher kann nur in .NET Core ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40e0b-134">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="40e0b-135">Weitere Informationen zu dieser Änderung finden Sie im Blogbeitrag zu [kommenden Änderungen in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span><span class="sxs-lookup"><span data-stu-id="40e0b-135">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="40e0b-136">Das Anzielen auf .NET Core bringt mit jedem Release mehr und mehr Vorteile mit sich.</span><span class="sxs-lookup"><span data-stu-id="40e0b-136">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="40e0b-137">Einige Vorteile von .NET Core gegenüber .NET Framework sind:</span><span class="sxs-lookup"><span data-stu-id="40e0b-137">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="40e0b-138">Plattformübergreifend</span><span class="sxs-lookup"><span data-stu-id="40e0b-138">Cross-platform.</span></span> <span data-ttu-id="40e0b-139">Wird unter macOS, Linux und Windows ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="40e0b-139">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="40e0b-140">Leistungssteigerung</span><span class="sxs-lookup"><span data-stu-id="40e0b-140">Improved performance</span></span>
* <span data-ttu-id="40e0b-141">Parallele Versionsverwaltung</span><span class="sxs-lookup"><span data-stu-id="40e0b-141">Side-by-side versioning</span></span>
* <span data-ttu-id="40e0b-142">Neue APIs</span><span class="sxs-lookup"><span data-stu-id="40e0b-142">New APIs</span></span>
* <span data-ttu-id="40e0b-143">Quelle öffnen</span><span class="sxs-lookup"><span data-stu-id="40e0b-143">Open source</span></span>

<span data-ttu-id="40e0b-144">Es wird daran gearbeitet, die API-Lücke von .NET Framework zu .NET Core zu schließen.</span><span class="sxs-lookup"><span data-stu-id="40e0b-144">We're working hard to close the API gap from .NET Framework to .NET Core.</span></span> <span data-ttu-id="40e0b-145">Das [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) stellt Tausende nur unter Windows verfügbare APIs in .NET Core zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="40e0b-145">The [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="40e0b-146">Diese APIs waren in .NET Core 1.x nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="40e0b-146">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="how-to-download-a-sample"></a><span data-ttu-id="40e0b-147">Herunterladen eines Beispiels</span><span class="sxs-lookup"><span data-stu-id="40e0b-147">How to download a sample</span></span>

<span data-ttu-id="40e0b-148">Viele der Artikel und Tutorials enthalten Links zu Beispielcode.</span><span class="sxs-lookup"><span data-stu-id="40e0b-148">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="40e0b-149">[Laden Sie die Zip-Datei des ASP.NET Repositorys herunter](https://codeload.github.com/aspnet/Docs/zip/master).</span><span class="sxs-lookup"><span data-stu-id="40e0b-149">[Download the ASP.NET repository zip file](https://codeload.github.com/aspnet/Docs/zip/master).</span></span>
1. <span data-ttu-id="40e0b-150">Entpacken Sie die Datei *Docs-master.zip*.</span><span class="sxs-lookup"><span data-stu-id="40e0b-150">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="40e0b-151">Navigieren Sie mit der URL in der Beispielverknüpfung zum Beispielverzeichnis.</span><span class="sxs-lookup"><span data-stu-id="40e0b-151">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="40e0b-152">Präprozessoranweisungen in Beispielcode</span><span class="sxs-lookup"><span data-stu-id="40e0b-152">Preprocessor directives in sample code</span></span>

<span data-ttu-id="40e0b-153">In Beispiel-Apps werden die C#-Anweisungen `#define` und `#if-#else/#elif-#endif` zum selektiven Kompilieren und Ausführen unterschiedlicher Abschnitte des Beispielcodes verwendet. So werden verschiedene Szenarios veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="40e0b-153">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` C# statements to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="40e0b-154">Für die Beispiele, die diesen Ansatz verwenden, legen Sie die Anweisung `#define` am Anfang der C#-Dateien auf das Symbol fest, das dem Szenario zugeordnet ist, welches Sie ausführen möchten.</span><span class="sxs-lookup"><span data-stu-id="40e0b-154">For those samples that make use of this approach, set the `#define` statement at the top of the C# files to the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="40e0b-155">Für einige Beispiele müssen Sie möglicherweise das Symbol in mehreren Dateien festlegen, um ein Szenario durchführen zu können.</span><span class="sxs-lookup"><span data-stu-id="40e0b-155">Some samples require setting the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="40e0b-156">Die folgende `#define`-Symbolliste gibt beispielsweise an, dass vier Szenarios verfügbar sind (ein Szenario pro Symbol).</span><span class="sxs-lookup"><span data-stu-id="40e0b-156">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="40e0b-157">Die aktuelle Beispielkonfiguration führt das `TemplateCode`-Szenario aus:</span><span class="sxs-lookup"><span data-stu-id="40e0b-157">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="40e0b-158">Damit das Beispiel das `ExpandDefault`-Szenario ausführt, definieren Sie das `ExpandDefault`-Symbol und lassen Sie die übrigen Symbole auskommentiert:</span><span class="sxs-lookup"><span data-stu-id="40e0b-158">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="40e0b-159">Weitere Informationen zur Verwendung von [C#-Präprozessoranweisungen](/dotnet/csharp/language-reference/preprocessor-directives/), um selektiv bestimmte Codeabschnitte zu kompilieren, finden Sie unter [#define (C#-Referenz)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) und [#if (C#-Referenz)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span><span class="sxs-lookup"><span data-stu-id="40e0b-159">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="40e0b-160">Bereiche in Beispielcode</span><span class="sxs-lookup"><span data-stu-id="40e0b-160">Regions in sample code</span></span>

<span data-ttu-id="40e0b-161">Einige Beispiel-Apps enthalten Codeabschnitte, die von den C#-Anweisungen [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) und [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) umschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="40e0b-161">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# statements.</span></span> <span data-ttu-id="40e0b-162">Das Buildsystem der Dokumentation fügt diese Bereiche in den gerenderten Dokumentationsartikel ein.</span><span class="sxs-lookup"><span data-stu-id="40e0b-162">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="40e0b-163">Namen von Bereichen enthalten oft das Wort „snippet“ (Ausschnitt).</span><span class="sxs-lookup"><span data-stu-id="40e0b-163">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="40e0b-164">Im folgenden Beispiel ist ein Bereich mit dem Namen `snippet_FilterInCode` enthalten:</span><span class="sxs-lookup"><span data-stu-id="40e0b-164">The following example shows a region named `snippet_FilterInCode`:</span></span>

```csharp
#region snippet_FilterInCode
WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
        logging.AddFilter("System", LogLevel.Debug)
            .AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Trace))
            .Build();
#endregion
```

<span data-ttu-id="40e0b-165">In der Markdowndatei wird auf den vorherigen C#-Codeausschnitt mit der folgenden Zeile verwiesen:</span><span class="sxs-lookup"><span data-stu-id="40e0b-165">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_FilterInCode)]
```

<span data-ttu-id="40e0b-166">Sie können die Anweisungen `#region` und `#endregion`, die den Code umschließen, ohne Weiteres ignorieren (oder entfernen).</span><span class="sxs-lookup"><span data-stu-id="40e0b-166">You may safely ignore (or remove) the `#region` and `#endregion` statements that surround the code.</span></span> <span data-ttu-id="40e0b-167">Ändern Sie jedoch den Code innerhalb dieser Anweisungen nicht, wenn Sie die im Artikel beschriebenen Beispielszenarios durchführen möchten.</span><span class="sxs-lookup"><span data-stu-id="40e0b-167">Don't alter the code within these statements if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="40e0b-168">Wenn Sie andere Szenarios ausprobieren möchten, können Sie den Code anpassen.</span><span class="sxs-lookup"><span data-stu-id="40e0b-168">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="40e0b-169">Weitere Informationen finden Sie unter [Contribute to the ASP.NET documentation: Code snippets (Mitwirken an der ASP.NET-Dokumentation: Codeausschnitte)](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span><span class="sxs-lookup"><span data-stu-id="40e0b-169">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/aspnet/Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="40e0b-170">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="40e0b-170">Next steps</span></span>

<span data-ttu-id="40e0b-171">Weitere Informationen finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="40e0b-171">For more information, see the following resources:</span></span>

* [<span data-ttu-id="40e0b-172">Erste Schritte mit Razor Pages</span><span class="sxs-lookup"><span data-stu-id="40e0b-172">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="40e0b-173">ASP.NET Core – Grundlagen</span><span class="sxs-lookup"><span data-stu-id="40e0b-173">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="40e0b-174">Im [wöchentlichen ASP.NET Community Standup](https://live.asp.net/) werden die Fortschritte und Pläne des Teams behandelt.</span><span class="sxs-lookup"><span data-stu-id="40e0b-174">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="40e0b-175">Zudem werden neue Blogs und Drittanbietersoftware vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="40e0b-175">It features new blogs and third-party software.</span></span>
