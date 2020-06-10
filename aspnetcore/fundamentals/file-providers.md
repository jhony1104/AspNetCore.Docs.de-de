---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/file-providers
ms.openlocfilehash: 1e243d31a1c6b1f6ac6c9f7966ce07ecb01ceae5
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106182"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="63417-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-103">'Identity'</span></span>

<span data-ttu-id="63417-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63417-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-105">'Razor'</span></span> <span data-ttu-id="63417-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-106">'SignalR' uid:</span></span> <span data-ttu-id="63417-107">Dateianbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63417-107">File Providers in ASP.NET Core</span></span>

* <span data-ttu-id="63417-108">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="63417-108">By [Steve Smith](https://ardalis.com/)</span></span>
* <span data-ttu-id="63417-109">ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern.</span><span class="sxs-lookup"><span data-stu-id="63417-109">ASP.NET Core abstracts file system access through the use of File Providers.</span></span>
* <span data-ttu-id="63417-110">Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet.</span><span class="sxs-lookup"><span data-stu-id="63417-110">File Providers are used throughout the ASP.NET Core framework.</span></span>
* <span data-ttu-id="63417-111">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="63417-111">For example:</span></span>

<span data-ttu-id="63417-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> macht das [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root) der App als `IFileProvider`-Typen verfügbar.</span><span class="sxs-lookup"><span data-stu-id="63417-112"><xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> exposes the app's [content root](xref:fundamentals/index#content-root) and [web root](xref:fundamentals/index#web-root) as `IFileProvider` types.</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="63417-113">Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.</span><span class="sxs-lookup"><span data-stu-id="63417-113">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>

<span data-ttu-id="63417-114">[Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.</span><span class="sxs-lookup"><span data-stu-id="63417-114">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span> <span data-ttu-id="63417-115">.NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="63417-115">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

* <span data-ttu-id="63417-116">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63417-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>
* <span data-ttu-id="63417-117">Dateianbieterschnittstellen</span><span class="sxs-lookup"><span data-stu-id="63417-117">File Provider interfaces</span></span>
* <span data-ttu-id="63417-118">Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="63417-118">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span>

<span data-ttu-id="63417-119">`IFileProvider` stellt Methoden zur Verfügung, zum:</span><span class="sxs-lookup"><span data-stu-id="63417-119">`IFileProvider` exposes methods to:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="63417-120">Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="63417-120">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="63417-121">Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="63417-121">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>

<span data-ttu-id="63417-122">Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="63417-122">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="63417-123">`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:</span><span class="sxs-lookup"><span data-stu-id="63417-123">`IFileInfo` provides methods and properties for working with files:</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="63417-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)</span><span class="sxs-lookup"><span data-stu-id="63417-124"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>

<span data-ttu-id="63417-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum</span><span class="sxs-lookup"><span data-stu-id="63417-125"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

| <span data-ttu-id="63417-126">Sie können die Datei mithilfe der <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType>-Methode lesen.</span><span class="sxs-lookup"><span data-stu-id="63417-126">You can read from the file using the <xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*?displayProperty=nameWithType> method.</span></span> | <span data-ttu-id="63417-127">Die Beispiel-App *FileProviderSample* demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Dependency Injection](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="63417-127">The *FileProviderSample* sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span> |
| -------------- | ----------- |
| <span data-ttu-id="63417-128">Dateianbieterimplementierungen</span><span class="sxs-lookup"><span data-stu-id="63417-128">File Provider implementations</span></span> | <span data-ttu-id="63417-129">In der folgenden Tabelle werden Implementierungen von `IFileProvider` aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="63417-129">The following table lists implementations of `IFileProvider`.</span></span> |
| <span data-ttu-id="63417-130">Implementierung</span><span class="sxs-lookup"><span data-stu-id="63417-130">Implementation</span></span> | <span data-ttu-id="63417-131">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="63417-131">Description</span></span> |
| <span data-ttu-id="63417-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-132">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="63417-133">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-133">'Blazor'</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="63417-134">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-134">'Identity'</span></span>

<span data-ttu-id="63417-135">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-135">'Let's Encrypt'</span></span> <span data-ttu-id="63417-136">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-136">'Razor'</span></span> <span data-ttu-id="63417-137">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-137">'SignalR' uid:</span></span> <span data-ttu-id="63417-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-138">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="63417-139">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-139">'Blazor'</span></span> <span data-ttu-id="63417-140">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-140">'Identity'</span></span>

<span data-ttu-id="63417-141">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-141">'Let's Encrypt'</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var filePath = Path.Combine("wwwroot", "js", "site.js");
var fileInfo = provider.GetFileInfo(filePath);
```

<span data-ttu-id="63417-142">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-142">'Razor'</span></span>

* <span data-ttu-id="63417-143">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-143">'SignalR' uid:</span></span>
* <span data-ttu-id="63417-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-144">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="63417-145">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-145">'Blazor'</span></span>

<span data-ttu-id="63417-146">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-146">'Identity'</span></span> <span data-ttu-id="63417-147">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-147">'Let's Encrypt'</span></span> <span data-ttu-id="63417-148">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-148">'Razor'</span></span>

<span data-ttu-id="63417-149">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-149">'SignalR' uid:</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="63417-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-150">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="63417-151">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-151">'Blazor'</span></span> <span data-ttu-id="63417-152">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-152">'Identity'</span></span>

<span data-ttu-id="63417-153">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-153">'Let's Encrypt'</span></span>

1. <span data-ttu-id="63417-154">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-154">'Razor'</span></span>
1. <span data-ttu-id="63417-155">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-155">'SignalR' uid:</span></span> <span data-ttu-id="63417-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-156">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

    [!code-xml[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=5,13)]

<span data-ttu-id="63417-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-157">'Blazor'</span></span>

<span data-ttu-id="63417-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-158">'Identity'</span></span>

<span data-ttu-id="63417-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-159">'Let's Encrypt'</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="63417-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-160">'Razor'</span></span>

* <span data-ttu-id="63417-161">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-161">'SignalR' uid:</span></span>
* <span data-ttu-id="63417-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-162">------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="63417-163">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-163">'Blazor'</span></span>

| <span data-ttu-id="63417-164">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-164">'Identity'</span></span> | <span data-ttu-id="63417-165">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-165">'Let's Encrypt'</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="63417-166">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-166">'Razor'</span></span> <span data-ttu-id="63417-167">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-167">'SignalR' uid:</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="63417-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-168">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> <span data-ttu-id="63417-169">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-169">'Blazor'</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="63417-170">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-170">'Identity'</span></span> <span data-ttu-id="63417-171">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-171">'Let's Encrypt'</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="63417-172">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-172">'Razor'</span></span>

<span data-ttu-id="63417-173">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-173">'SignalR' uid:</span></span> <span data-ttu-id="63417-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-174">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="63417-175">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-175">'Blazor'</span></span> <span data-ttu-id="63417-176">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-176">'Identity'</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="63417-177">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-177">'Let's Encrypt'</span></span>

<span data-ttu-id="63417-178">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-178">'Razor'</span></span> <span data-ttu-id="63417-179">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-179">'SignalR' uid:</span></span>

* <span data-ttu-id="63417-180">------ | | [CompositeFileProvider](#compositefileprovider) | Wird zur Bereitstellung von kombiniertem Zugriff auf Dateien und Verzeichnisse über einen oder mehrere andere Anbieter verwendet</span><span class="sxs-lookup"><span data-stu-id="63417-180">------ | | [CompositeFileProvider](#compositefileprovider) | Used to provide combined access to files and directories from one or more other providers.</span></span>
* <span data-ttu-id="63417-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Wird für den Zugriff auf in Assemblys eingebettete Dateien verwendet</span><span class="sxs-lookup"><span data-stu-id="63417-181">| | [ManifestEmbeddedFileProvider](#manifestembeddedfileprovider) | Used to access files embedded in assemblies.</span></span>

<span data-ttu-id="63417-182">| | [PhysicalFileProvider](#physicalfileprovider) | Wird für den Zugriff auf die physischen Systemdateien verwendet</span><span class="sxs-lookup"><span data-stu-id="63417-182">| | [PhysicalFileProvider](#physicalfileprovider) | Used to access the system's physical files.</span></span>

* <span data-ttu-id="63417-183">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="63417-183">PhysicalFileProvider</span></span>
* <span data-ttu-id="63417-184">Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="63417-184">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="63417-185">`PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="63417-185">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="63417-186">Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen.</span><span class="sxs-lookup"><span data-stu-id="63417-186">This scoping prevents access to the file system outside of the specified directory and its children.</span></span>

<span data-ttu-id="63417-187">Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="63417-187">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1)]

<span data-ttu-id="63417-188">Beim direkten Instanziieren dieses Anbieters ist ein absoluter Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient.</span><span class="sxs-lookup"><span data-stu-id="63417-188">When instantiating this provider directly, an absolute directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="63417-189">Globmuster werden im Verzeichnispfad nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63417-189">Glob patterns aren't supported in the directory path.</span></span>

### <a name="glob-patterns"></a><span data-ttu-id="63417-190">Im folgenden Code wird die Verwendung von `PhysicalFileProvider` zum Abrufen von Verzeichnisinhalten und Dateiinformationen veranschaulicht:</span><span class="sxs-lookup"><span data-stu-id="63417-190">The following code shows how to use `PhysicalFileProvider` to obtain directory contents and file information:</span></span>

<span data-ttu-id="63417-191">Typen im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="63417-191">Types in the preceding example:</span></span> <span data-ttu-id="63417-192">`provider` ist ein `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="63417-192">`provider` is an `IFileProvider`.</span></span> <span data-ttu-id="63417-193">`contents` ist ein `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="63417-193">`contents` is an `IDirectoryContents`.</span></span>

**`*`**  
<span data-ttu-id="63417-194">`fileInfo` ist ein `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="63417-194">`fileInfo` is an `IFileInfo`.</span></span> <span data-ttu-id="63417-195">Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="63417-195">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span>

**`**`**  
<span data-ttu-id="63417-196">Globmuster können nicht an die `GetFileInfo`-Methode übergeben werden.</span><span class="sxs-lookup"><span data-stu-id="63417-196">Glob patterns can't be passed to the `GetFileInfo` method.</span></span> <span data-ttu-id="63417-197">Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="63417-197">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="63417-198">Die Beispiel-App *FileProviderSample* erstellt den Anbieter in der `Startup.ConfigureServices`-Methode mithilfe von <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span><span class="sxs-lookup"><span data-stu-id="63417-198">The *FileProviderSample* sample app creates the provider in the `Startup.ConfigureServices` method using <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider?displayProperty=nameWithType>:</span></span>

|<span data-ttu-id="63417-199">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="63417-199">ManifestEmbeddedFileProvider</span></span>  |<span data-ttu-id="63417-200">Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="63417-200">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span>  |
|---------|---------|
|`directory/file.txt`|<span data-ttu-id="63417-201">Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.</span><span class="sxs-lookup"><span data-stu-id="63417-201">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>|
|`directory/*.txt`|<span data-ttu-id="63417-202">So generieren Sie ein Manifest der eingebetteten Dateien:</span><span class="sxs-lookup"><span data-stu-id="63417-202">To generate a manifest of the embedded files:</span></span>|
|`directory/*/appsettings.json`|<span data-ttu-id="63417-203">Fügen Sie das NuGet-Paket [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) zu Ihrem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="63417-203">Add the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) NuGet package to your project.</span></span>|
|`directory/**/*.txt`|<span data-ttu-id="63417-204">Legen Sie die `<GenerateEmbeddedFilesManifest>` -Eigenschaft auf `true`fest.</span><span class="sxs-lookup"><span data-stu-id="63417-204">Set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span>|

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="63417-205">Geben Sie die einzubettenden Dateien mit [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:</span><span class="sxs-lookup"><span data-stu-id="63417-205">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span> <span data-ttu-id="63417-206">Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="63417-206">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

* <span data-ttu-id="63417-207">Die Beispiel-App *FileProviderSample* erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="63417-207">The *FileProviderSample* sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>
* <span data-ttu-id="63417-208">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="63417-208">*Startup.cs*:</span></span>
* <span data-ttu-id="63417-209">Mit zusätzlichen Überladungen können Sie:</span><span class="sxs-lookup"><span data-stu-id="63417-209">Additional overloads allow you to:</span></span>
* <span data-ttu-id="63417-210">Einen relativen Dateipfad angeben.</span><span class="sxs-lookup"><span data-stu-id="63417-210">Specify a relative file path.</span></span>

<span data-ttu-id="63417-211">Dateien einem zuletzt geänderten Datum zuordnen.</span><span class="sxs-lookup"><span data-stu-id="63417-211">Scope files to a last modified date.</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="63417-212">Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.</span><span class="sxs-lookup"><span data-stu-id="63417-212">Name the embedded resource containing the embedded file manifest.</span></span>

<span data-ttu-id="63417-213">Überladung</span><span class="sxs-lookup"><span data-stu-id="63417-213">Overload</span></span> <span data-ttu-id="63417-214">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="63417-214">Description</span></span>

* <span data-ttu-id="63417-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-215">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
* <span data-ttu-id="63417-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-216">'Blazor'</span></span>
* <span data-ttu-id="63417-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-217">'Identity'</span></span>

<span data-ttu-id="63417-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-218">'Let's Encrypt'</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="63417-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-219">'Razor'</span></span>
* <span data-ttu-id="63417-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-220">'SignalR' uid:</span></span>

<span data-ttu-id="63417-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-221">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="63417-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-222">'Blazor'</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="63417-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-223">'Identity'</span></span>

<span data-ttu-id="63417-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-224">'Let's Encrypt'</span></span>

| <span data-ttu-id="63417-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-225">'Razor'</span></span> | <span data-ttu-id="63417-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-226">'SignalR' uid:</span></span> |
| -------------- | ----------- |
| <span data-ttu-id="63417-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-227">---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span> | <span data-ttu-id="63417-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-228">'Blazor'</span></span> |
| <span data-ttu-id="63417-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-229">'Identity'</span></span> | <span data-ttu-id="63417-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-230">'Let's Encrypt'</span></span> |
| <span data-ttu-id="63417-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-231">'Razor'</span></span> | <span data-ttu-id="63417-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-232">'SignalR' uid:</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="63417-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-233">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

<span data-ttu-id="63417-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-234">'Blazor'</span></span> <span data-ttu-id="63417-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-235">'Identity'</span></span> <span data-ttu-id="63417-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-236">'Let's Encrypt'</span></span> <span data-ttu-id="63417-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-237">'Razor'</span></span>

<span data-ttu-id="63417-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-238">'SignalR' uid:</span></span>

<span data-ttu-id="63417-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-239">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="63417-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-240">'Blazor'</span></span>

* <span data-ttu-id="63417-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-241">'Identity'</span></span>
* <span data-ttu-id="63417-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-242">'Let's Encrypt'</span></span>
* <span data-ttu-id="63417-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-243">'Razor'</span></span>

<span data-ttu-id="63417-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-244">'SignalR' uid:</span></span> <span data-ttu-id="63417-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter</span><span class="sxs-lookup"><span data-stu-id="63417-245">------ | | `ManifestEmbeddedFileProvider(Assembly, String)` | Accepts an optional `root` relative path parameter.</span></span>

<span data-ttu-id="63417-246">Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="63417-246">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="63417-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>)</span><span class="sxs-lookup"><span data-stu-id="63417-247">| | `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span>

<span data-ttu-id="63417-248">Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="63417-248">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="63417-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Akzeptiert einen optionalen relativen `root`-Pfadparameter, einen Datumsparameter für `lastModified` und einen `manifestName`-Parameter</span><span class="sxs-lookup"><span data-stu-id="63417-249">| | `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span>

<span data-ttu-id="63417-250">`manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält.</span><span class="sxs-lookup"><span data-stu-id="63417-250">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> <span data-ttu-id="63417-251">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="63417-251">CompositeFileProvider</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="63417-252">Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="63417-252">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span>

<span data-ttu-id="63417-253">Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="63417-253">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="63417-254">In der Beispiel-App *FileProviderSample* stellen die Anbieter `PhysicalFileProvider` und `ManifestEmbeddedFileProvider` Dateien für einen `CompositeFileProvider`-Anbieter bereit, der im Dienstcontainer der App registriert ist.</span><span class="sxs-lookup"><span data-stu-id="63417-254">In the *FileProviderSample* sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container.</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(typeof(Program).Assembly);
```

<span data-ttu-id="63417-255">Der folgende Code befindet sich in der `Startup.ConfigureServices`-Methode des Projekts:</span><span class="sxs-lookup"><span data-stu-id="63417-255">The following code is found in the project's `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="63417-256">Überwachen auf Änderungen</span><span class="sxs-lookup"><span data-stu-id="63417-256">Watch for changes</span></span>
* <span data-ttu-id="63417-257">Die <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType>-Methode bietet ein Szenario zum Überwachen von Änderungen an Dateien oder Verzeichnissen.</span><span class="sxs-lookup"><span data-stu-id="63417-257">The <xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*?displayProperty=nameWithType> method provides a scenario to watch one or more files or directories for changes.</span></span>
* <span data-ttu-id="63417-258">Die `Watch`-Methode:</span><span class="sxs-lookup"><span data-stu-id="63417-258">The `Watch` method:</span></span>

| <span data-ttu-id="63417-259">akzeptiert eine Dateipfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben.</span><span class="sxs-lookup"><span data-stu-id="63417-259">Accepts a file path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> | <span data-ttu-id="63417-260">Gibt einen <xref:Microsoft.Extensions.Primitives.IChangeToken> zurück.</span><span class="sxs-lookup"><span data-stu-id="63417-260">Returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="63417-261">Das resultierende Änderungstoken stellt Folgendes zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="63417-261">The resulting change token exposes:</span></span> <span data-ttu-id="63417-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="63417-262"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged>: A property that can be inspected to determine if a change has occurred.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="63417-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="63417-263"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*>: Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="63417-264">Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf.</span><span class="sxs-lookup"><span data-stu-id="63417-264">Each change token only calls its associated callback in response to a single change.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="63417-265">Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu.</span><span class="sxs-lookup"><span data-stu-id="63417-265">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span> <span data-ttu-id="63417-266">Die Beispiel-App *WatchConsole* schreibt eine Nachricht, wenn eine *TXT*-Datei im Verzeichnis *TextFiles* geändert wird:</span><span class="sxs-lookup"><span data-stu-id="63417-266">The *WatchConsole* sample app writes a message whenever a *.txt* file in the *TextFiles* directory is modified:</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="63417-267">Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig .</span><span class="sxs-lookup"><span data-stu-id="63417-267">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span>

<span data-ttu-id="63417-268">Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).</span><span class="sxs-lookup"><span data-stu-id="63417-268">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span> <span data-ttu-id="63417-269">Globmuster</span><span class="sxs-lookup"><span data-stu-id="63417-269">Glob patterns</span></span>

<span data-ttu-id="63417-270">Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*.</span><span class="sxs-lookup"><span data-stu-id="63417-270">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="63417-271">Geben Sie Gruppen von Dateien mit diesen Mustern an.</span><span class="sxs-lookup"><span data-stu-id="63417-271">Specify groups of files with these patterns.</span></span>

<span data-ttu-id="63417-272">Die zwei Platzhalterzeichen sind `*` und `**`:</span><span class="sxs-lookup"><span data-stu-id="63417-272">The two wildcard characters are `*` and `**`:</span></span> <span data-ttu-id="63417-273">Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung.</span><span class="sxs-lookup"><span data-stu-id="63417-273">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="63417-274">Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.</span><span class="sxs-lookup"><span data-stu-id="63417-274">Matches are terminated by `/` and `.` characters in the file path.</span></span> <span data-ttu-id="63417-275">Überprüft alles auf mehrere Verzeichnisebenen.</span><span class="sxs-lookup"><span data-stu-id="63417-275">Matches anything across multiple directory levels.</span></span>

* <span data-ttu-id="63417-276">Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.</span><span class="sxs-lookup"><span data-stu-id="63417-276">Can be used to recursively match many files within a directory hierarchy.</span></span>
* <span data-ttu-id="63417-277">Die folgende Tabelle enthält gängige Beispiele für Globmuster.</span><span class="sxs-lookup"><span data-stu-id="63417-277">The following table provides common examples of glob patterns.</span></span> <span data-ttu-id="63417-278">Muster</span><span class="sxs-lookup"><span data-stu-id="63417-278">Pattern</span></span> <span data-ttu-id="63417-279">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="63417-279">Description</span></span>

<span data-ttu-id="63417-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="63417-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-281">'Blazor'</span></span> <span data-ttu-id="63417-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-282">'Identity'</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="63417-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-283">'Let's Encrypt'</span></span>

<span data-ttu-id="63417-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-284">'Razor'</span></span> <span data-ttu-id="63417-285">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-285">'SignalR' uid:</span></span> <span data-ttu-id="63417-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

**`*`**  
<span data-ttu-id="63417-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-287">'Blazor'</span></span> <span data-ttu-id="63417-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-288">'Identity'</span></span>

**`**`**  
<span data-ttu-id="63417-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-289">'Let's Encrypt'</span></span> <span data-ttu-id="63417-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63417-290">'Razor'</span></span>

<span data-ttu-id="63417-291">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="63417-291">'SignalR' uid:</span></span>

**`directory/file.txt`**  
<span data-ttu-id="63417-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="63417-292">-----|---
title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>

**`directory/*.txt`**  
<span data-ttu-id="63417-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63417-293">'Blazor'</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="63417-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63417-294">'Identity'</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="63417-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63417-295">'Let's Encrypt'</span></span>

::: moniker-end
