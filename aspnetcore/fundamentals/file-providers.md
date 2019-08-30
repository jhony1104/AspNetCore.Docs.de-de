---
title: Dateianbieter in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie ASP.NET Core Dateisystemzugriff durch die Verwendung von Dateianbietern abstrahiert.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/26/2019
uid: fundamentals/file-providers
ms.openlocfilehash: 44c439dce893d486668bf8ac3f20cdf7952c5186
ms.sourcegitcommit: 0774a61a3a6c1412a7da0e7d932dc60c506441fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2019
ms.locfileid: "70059101"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="5f4fa-103">Dateianbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5f4fa-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="5f4fa-104">Von [Steve Smith](https://ardalis.com/) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5f4fa-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5f4fa-105">ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="5f4fa-106">Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="5f4fa-107">`IWebHostEnvironment` stellt das Inhaltsstammverzeichnis und das Webstammverzeichnis der Anwendung als `IFileProvider`-Typen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-107">`IWebHostEnvironment` exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="5f4fa-108">Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="5f4fa-109">[Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="5f4fa-110">.NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="5f4fa-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5f4fa-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="5f4fa-112">Dateianbieterschnittstellen</span><span class="sxs-lookup"><span data-stu-id="5f4fa-112">File Provider interfaces</span></span>

<span data-ttu-id="5f4fa-113">Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-113">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="5f4fa-114">`IFileProvider` stellt Methoden zur Verfügung, zum:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="5f4fa-115">Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-115">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="5f4fa-116">Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-116">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="5f4fa-117">Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-117">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="5f4fa-118">`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="5f4fa-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)</span><span class="sxs-lookup"><span data-stu-id="5f4fa-119"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="5f4fa-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum</span><span class="sxs-lookup"><span data-stu-id="5f4fa-120"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="5f4fa-121">Mithilfe der [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)-Methode können Sie die Datei auslesen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-121">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="5f4fa-122">Die Beispiel-App demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-122">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="5f4fa-123">Dateianbieterimplementierungen</span><span class="sxs-lookup"><span data-stu-id="5f4fa-123">File Provider implementations</span></span>

<span data-ttu-id="5f4fa-124">Es sind drei Implementierungen von `IFileProvider` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-124">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="5f4fa-125">Implementierung</span><span class="sxs-lookup"><span data-stu-id="5f4fa-125">Implementation</span></span> | <span data-ttu-id="5f4fa-126">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="5f4fa-126">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="5f4fa-127">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-127">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="5f4fa-128">Der physische Anbieter wird verwendet, um auf die physischen Systemdateien zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-128">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="5f4fa-129">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-129">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="5f4fa-130">Der eingebettete Manifesanbieter wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-130">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="5f4fa-131">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-131">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="5f4fa-132">Der zusammengesetzte Anbieter wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von einem oder mehreren anderen Anbietern bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-132">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="5f4fa-133">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-133">PhysicalFileProvider</span></span>

<span data-ttu-id="5f4fa-134">Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-134">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="5f4fa-135">`PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-135">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="5f4fa-136">Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-136">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="5f4fa-137">Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-137">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5f4fa-138">Beim direkten Instanziieren dieses Anbieters ist ein Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-138">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="5f4fa-139">Der folgende Code zeigt die Erstellung eines `PhysicalFileProvider` und dessen Verwendung zum Abrufen von Verzeichnisinhalten und Dateiinformationen:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-139">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="5f4fa-140">Typen im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-140">Types in the preceding example:</span></span>

* <span data-ttu-id="5f4fa-141">`provider` ist ein `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-141">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="5f4fa-142">`contents` ist ein `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-142">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="5f4fa-143">`fileInfo` ist ein `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-143">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="5f4fa-144">Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-144">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="5f4fa-145">Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-145">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="5f4fa-146">Die Beispiel-App erstellt einen Anbieter in `Startup.ConfigureServices`-Klasse der App mit [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="5f4fa-146">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="5f4fa-147">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-147">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="5f4fa-148">Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-148">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="5f4fa-149">Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-149">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="5f4fa-150">Fügen Sie dem Projekt einen Paketverweis für das Paket [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) hinzu.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-150">Add a package reference to the project for the [Microsoft.Extensions.FileProviders.Embedded](https://www.nuget.org/packages/Microsoft.Extensions.FileProviders.Embedded) package.</span></span>

<span data-ttu-id="5f4fa-151">Um ein Manifest der eingebetteten Dateien zu generieren, legen die `<GenerateEmbeddedFilesManifest>`-Eigenschaft auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-151">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="5f4fa-152">Geben Sie die einzubettenden Dateien mit [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-152">Specify the files to embed with [\<EmbeddedResource>](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="5f4fa-153">Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-153">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="5f4fa-154">Die Beispiel-App erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-154">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="5f4fa-155">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-155">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="5f4fa-156">Mit zusätzlichen Überladungen können Sie:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-156">Additional overloads allow you to:</span></span>

* <span data-ttu-id="5f4fa-157">Einen relativen Dateipfad angeben.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-157">Specify a relative file path.</span></span>
* <span data-ttu-id="5f4fa-158">Dateien einem zuletzt geänderten Datum zuordnen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-158">Scope files to a last modified date.</span></span>
* <span data-ttu-id="5f4fa-159">Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-159">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="5f4fa-160">Überladung</span><span class="sxs-lookup"><span data-stu-id="5f4fa-160">Overload</span></span> | <span data-ttu-id="5f4fa-161">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="5f4fa-161">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="5f4fa-162">Akzeptiert einen optionalen relativen `root`-Pfadparameter.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-162">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="5f4fa-163">Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-163">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="5f4fa-164">Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-164">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="5f4fa-165">Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-165">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="5f4fa-166">Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-166">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="5f4fa-167">`manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-167">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="5f4fa-168">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-168">CompositeFileProvider</span></span>

<span data-ttu-id="5f4fa-169">Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-169">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="5f4fa-170">Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-170">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="5f4fa-171">In der Beispiel-App stellt ein `PhysicalFileProvider` und ein `ManifestEmbeddedFileProvider` Dateien an ein `CompositeFileProvider`, der im Dienstcontainer der App registriert ist:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-171">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/3.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="5f4fa-172">Überwachen auf Änderungen</span><span class="sxs-lookup"><span data-stu-id="5f4fa-172">Watch for changes</span></span>

<span data-ttu-id="5f4fa-173">Die [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)-Methode bietet ein Szenario, eine oder mehrere Dateien oder Verzeichnisse auf Änderungen zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-173">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="5f4fa-174">`Watch`akzeptiert eine Pfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-174">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="5f4fa-175"><xref:Microsoft.Extensions.Primitives.IChangeToken> gibt `Watch` zurück.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-175">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="5f4fa-176">Das Änderungstoken macht folgendes verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-176">The change token exposes:</span></span>

* <span data-ttu-id="5f4fa-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-177"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="5f4fa-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-178"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="5f4fa-179">Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-179">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="5f4fa-180">Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-180">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="5f4fa-181">In der Beispiel-App wird die *WatchConsole*-Konsolen-App konfiguriert, damit sie bei einer Änderung einer Textdatei eine Meldung anzeigt:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-181">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/3.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="5f4fa-182">Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig .</span><span class="sxs-lookup"><span data-stu-id="5f4fa-182">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="5f4fa-183">Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-183">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="5f4fa-184">Globmuster</span><span class="sxs-lookup"><span data-stu-id="5f4fa-184">Glob patterns</span></span>

<span data-ttu-id="5f4fa-185">Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-185">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="5f4fa-186">Geben Sie Gruppen von Dateien mit diesen Mustern an.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-186">Specify groups of files with these patterns.</span></span> <span data-ttu-id="5f4fa-187">Die zwei Platzhalterzeichen sind `*` und `**`:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-187">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="5f4fa-188">Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-188">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="5f4fa-189">Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-189">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="5f4fa-190">Überprüft alles auf mehrere Verzeichnisebenen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-190">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="5f4fa-191">Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-191">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="5f4fa-192">**Beispiele für Globmuster**</span><span class="sxs-lookup"><span data-stu-id="5f4fa-192">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="5f4fa-193">Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-193">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="5f4fa-194">Entspricht allen Dateien mit *.txt*-Erweiterung in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-194">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="5f4fa-195">Entspricht allen `appsettings.json`-Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory*-Ordners.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-195">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="5f4fa-196">Entspricht allen Dateien mit *.txt*-Erweiterung, die an einer beliebigen Stelle unter dem *directory*-Ordner gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-196">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5f4fa-197">ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-197">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="5f4fa-198">Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-198">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="5f4fa-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> stellt das Inhaltsstammverzeichnis und das Webstammverzeichnis der Anwendung als `IFileProvider`-Typen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-199"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment> exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="5f4fa-200">Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-200">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="5f4fa-201">[Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-201">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="5f4fa-202">.NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-202">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="5f4fa-203">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="5f4fa-203">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="5f4fa-204">Dateianbieterschnittstellen</span><span class="sxs-lookup"><span data-stu-id="5f4fa-204">File Provider interfaces</span></span>

<span data-ttu-id="5f4fa-205">Die primäre Schnittstelle ist <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-205">The primary interface is <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> <span data-ttu-id="5f4fa-206">`IFileProvider` stellt Methoden zur Verfügung, zum:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-206">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="5f4fa-207">Abrufen von Dateiinformationen (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-207">Obtain file information (<xref:Microsoft.Extensions.FileProviders.IFileInfo>).</span></span>
* <span data-ttu-id="5f4fa-208">Abrufen von Verzeichnisinformationen (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-208">Obtain directory information (<xref:Microsoft.Extensions.FileProviders.IDirectoryContents>).</span></span>
* <span data-ttu-id="5f4fa-209">Einrichten von Änderungsbenachrichtigungen (mithilfe eines <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-209">Set up change notifications (using an <xref:Microsoft.Extensions.Primitives.IChangeToken>).</span></span>

<span data-ttu-id="5f4fa-210">`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-210">`IFileInfo` provides methods and properties for working with files:</span></span>

* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Exists>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.IsDirectory>
* <xref:Microsoft.Extensions.FileProviders.IFileInfo.Name>
* <span data-ttu-id="5f4fa-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in Bytes)</span><span class="sxs-lookup"><span data-stu-id="5f4fa-211"><xref:Microsoft.Extensions.FileProviders.IFileInfo.Length> (in bytes)</span></span>
* <span data-ttu-id="5f4fa-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> – ein Datum</span><span class="sxs-lookup"><span data-stu-id="5f4fa-212"><xref:Microsoft.Extensions.FileProviders.IFileInfo.LastModified> date</span></span>

<span data-ttu-id="5f4fa-213">Mithilfe der [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*)-Methode können Sie die Datei auslesen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-213">You can read from the file using the [IFileInfo.CreateReadStream](xref:Microsoft.Extensions.FileProviders.IFileInfo.CreateReadStream*) method.</span></span>

<span data-ttu-id="5f4fa-214">Die Beispiel-App demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-214">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="5f4fa-215">Dateianbieterimplementierungen</span><span class="sxs-lookup"><span data-stu-id="5f4fa-215">File Provider implementations</span></span>

<span data-ttu-id="5f4fa-216">Es sind drei Implementierungen von `IFileProvider` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-216">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="5f4fa-217">Implementierung</span><span class="sxs-lookup"><span data-stu-id="5f4fa-217">Implementation</span></span> | <span data-ttu-id="5f4fa-218">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="5f4fa-218">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="5f4fa-219">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-219">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="5f4fa-220">Der physische Anbieter wird verwendet, um auf die physischen Systemdateien zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-220">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="5f4fa-221">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-221">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="5f4fa-222">Der eingebettete Manifesanbieter wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-222">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="5f4fa-223">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-223">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="5f4fa-224">Der zusammengesetzte Anbieter wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von einem oder mehreren anderen Anbietern bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-224">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="5f4fa-225">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-225">PhysicalFileProvider</span></span>

<span data-ttu-id="5f4fa-226">Der <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> ermöglicht den Zugriff auf das physische Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-226">The <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> provides access to the physical file system.</span></span> <span data-ttu-id="5f4fa-227">`PhysicalFileProvider` verwendet den <xref:System.IO.File?displayProperty=fullName>-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und dessen untergeordneten Elementen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-227">`PhysicalFileProvider` uses the <xref:System.IO.File?displayProperty=fullName> type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="5f4fa-228">Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-228">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="5f4fa-229">Das häufigste Szenario, in dem ein `PhysicalFileProvider` erstellt und verwendet wird, ist die Anforderung eines `IFileProvider` in einem Konstruktor über eine [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-229">The most common scenario for creating and using a `PhysicalFileProvider` is to request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5f4fa-230">Beim direkten Instanziieren dieses Anbieters ist ein Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-230">When instantiating this provider directly, a directory path is required and serves as the base path for all requests made using the provider.</span></span>

<span data-ttu-id="5f4fa-231">Der folgende Code zeigt die Erstellung eines `PhysicalFileProvider` und dessen Verwendung zum Abrufen von Verzeichnisinhalten und Dateiinformationen:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-231">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="5f4fa-232">Typen im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-232">Types in the preceding example:</span></span>

* <span data-ttu-id="5f4fa-233">`provider` ist ein `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-233">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="5f4fa-234">`contents` ist ein `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-234">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="5f4fa-235">`fileInfo` ist ein `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-235">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="5f4fa-236">Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-236">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="5f4fa-237">Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-237">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="5f4fa-238">Die Beispiel-App erstellt einen Anbieter in `Startup.ConfigureServices`-Klasse der App mit [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span><span class="sxs-lookup"><span data-stu-id="5f4fa-238">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootFileProvider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="5f4fa-239">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-239">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="5f4fa-240">Der <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-240">The <xref:Microsoft.Extensions.FileProviders.ManifestEmbeddedFileProvider> is used to access files embedded within assemblies.</span></span> <span data-ttu-id="5f4fa-241">Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-241">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="5f4fa-242">Um ein Manifest der eingebetteten Dateien zu generieren, legen die `<GenerateEmbeddedFilesManifest>`-Eigenschaft auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-242">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="5f4fa-243">Geben Sie die einzubettenden Dateien mit [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-243">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="5f4fa-244">Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-244">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="5f4fa-245">Die Beispiel-App erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-245">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="5f4fa-246">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-246">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="5f4fa-247">Mit zusätzlichen Überladungen können Sie:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-247">Additional overloads allow you to:</span></span>

* <span data-ttu-id="5f4fa-248">Einen relativen Dateipfad angeben.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-248">Specify a relative file path.</span></span>
* <span data-ttu-id="5f4fa-249">Dateien einem zuletzt geänderten Datum zuordnen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-249">Scope files to a last modified date.</span></span>
* <span data-ttu-id="5f4fa-250">Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-250">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="5f4fa-251">Überladung</span><span class="sxs-lookup"><span data-stu-id="5f4fa-251">Overload</span></span> | <span data-ttu-id="5f4fa-252">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="5f4fa-252">Description</span></span> |
| -------- | ----------- |
| `ManifestEmbeddedFileProvider(Assembly, String)` | <span data-ttu-id="5f4fa-253">Akzeptiert einen optionalen relativen `root`-Pfadparameter.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-253">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="5f4fa-254">Geben Sie `root` an, um Aufrufe von <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> auf die Ressourcen im angegebenen Pfad zu begrenzen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-254">Specify the `root` to scope calls to <xref:Microsoft.Extensions.FileProviders.IFileProvider.GetDirectoryContents*> to those resources under the provided path.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)` | <span data-ttu-id="5f4fa-255">Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen Datumsparameter für `lastModified` (<xref:System.DateTimeOffset>).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-255">Accepts an optional `root` relative path parameter and a `lastModified` date (<xref:System.DateTimeOffset>) parameter.</span></span> <span data-ttu-id="5f4fa-256">Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die <xref:Microsoft.Extensions.FileProviders.IFileInfo>-Instanzen zu, die vom <xref:Microsoft.Extensions.FileProviders.IFileProvider> zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-256">The `lastModified` date scopes the last modification date for the <xref:Microsoft.Extensions.FileProviders.IFileInfo> instances returned by the <xref:Microsoft.Extensions.FileProviders.IFileProvider>.</span></span> |
| `ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)` | <span data-ttu-id="5f4fa-257">Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-257">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="5f4fa-258">`manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-258">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="5f4fa-259">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="5f4fa-259">CompositeFileProvider</span></span>

<span data-ttu-id="5f4fa-260">Der <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-260">The <xref:Microsoft.Extensions.FileProviders.CompositeFileProvider> combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="5f4fa-261">Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-261">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="5f4fa-262">In der Beispiel-App stellt ein `PhysicalFileProvider` und ein `ManifestEmbeddedFileProvider` Dateien an ein `CompositeFileProvider`, der im Dienstcontainer der App registriert ist:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-262">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="5f4fa-263">Überwachen auf Änderungen</span><span class="sxs-lookup"><span data-stu-id="5f4fa-263">Watch for changes</span></span>

<span data-ttu-id="5f4fa-264">Die [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*)-Methode bietet ein Szenario, eine oder mehrere Dateien oder Verzeichnisse auf Änderungen zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-264">The [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="5f4fa-265">`Watch`akzeptiert eine Pfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-265">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="5f4fa-266"><xref:Microsoft.Extensions.Primitives.IChangeToken> gibt `Watch` zurück.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-266">`Watch` returns an <xref:Microsoft.Extensions.Primitives.IChangeToken>.</span></span> <span data-ttu-id="5f4fa-267">Das Änderungstoken macht folgendes verfügbar:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-267">The change token exposes:</span></span>

* <span data-ttu-id="5f4fa-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-268"><xref:Microsoft.Extensions.Primitives.IChangeToken.HasChanged> &ndash; A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="5f4fa-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-269"><xref:Microsoft.Extensions.Primitives.IChangeToken.RegisterChangeCallback*> &ndash; Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="5f4fa-270">Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-270">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="5f4fa-271">Zur Aktivierung einer konstanten Überwachung verwenden Sie eine <xref:System.Threading.Tasks.TaskCompletionSource`1> (wie unten dargestellt), oder erstellen Sie `IChangeToken`-Instanzen nach Änderungen neu.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-271">To enable constant monitoring, use a <xref:System.Threading.Tasks.TaskCompletionSource`1> (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="5f4fa-272">In der Beispiel-App wird die *WatchConsole*-Konsolen-App konfiguriert, damit sie bei einer Änderung einer Textdatei eine Meldung anzeigt:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-272">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="5f4fa-273">Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig .</span><span class="sxs-lookup"><span data-stu-id="5f4fa-273">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="5f4fa-274">Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).</span><span class="sxs-lookup"><span data-stu-id="5f4fa-274">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="5f4fa-275">Globmuster</span><span class="sxs-lookup"><span data-stu-id="5f4fa-275">Glob patterns</span></span>

<span data-ttu-id="5f4fa-276">Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-276">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="5f4fa-277">Geben Sie Gruppen von Dateien mit diesen Mustern an.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-277">Specify groups of files with these patterns.</span></span> <span data-ttu-id="5f4fa-278">Die zwei Platzhalterzeichen sind `*` und `**`:</span><span class="sxs-lookup"><span data-stu-id="5f4fa-278">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="5f4fa-279">Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-279">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="5f4fa-280">Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-280">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="5f4fa-281">Überprüft alles auf mehrere Verzeichnisebenen.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-281">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="5f4fa-282">Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-282">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="5f4fa-283">**Beispiele für Globmuster**</span><span class="sxs-lookup"><span data-stu-id="5f4fa-283">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="5f4fa-284">Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-284">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="5f4fa-285">Entspricht allen Dateien mit *.txt*-Erweiterung in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-285">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="5f4fa-286">Entspricht allen `appsettings.json`-Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory*-Ordners.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-286">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="5f4fa-287">Entspricht allen Dateien mit *.txt*-Erweiterung, die an einer beliebigen Stelle unter dem *directory*-Ordner gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="5f4fa-287">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>

::: moniker-end
