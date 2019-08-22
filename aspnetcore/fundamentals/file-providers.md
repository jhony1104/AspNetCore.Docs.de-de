---
title: Dateianbieter in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie ASP.NET Core Dateisystemzugriff durch die Verwendung von Dateianbietern abstrahiert.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: fundamentals/file-providers
ms.openlocfilehash: b93b2df7fad7c173f43ad69aec865f09de6c9c34
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487575"
---
# <a name="file-providers-in-aspnet-core"></a><span data-ttu-id="630b2-103">Dateianbieter in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="630b2-103">File Providers in ASP.NET Core</span></span>

<span data-ttu-id="630b2-104">Von [Steve Smith](https://ardalis.com/) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="630b2-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="630b2-105">ASP.NET Core abstrahiert Dateisystemzugriff durch die Verwendung von Dateianbietern.</span><span class="sxs-lookup"><span data-stu-id="630b2-105">ASP.NET Core abstracts file system access through the use of File Providers.</span></span> <span data-ttu-id="630b2-106">Dateianbieter werden im gesamten ASP.NET Core-Framework verwendet:</span><span class="sxs-lookup"><span data-stu-id="630b2-106">File Providers are used throughout the ASP.NET Core framework:</span></span>

* <span data-ttu-id="630b2-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) stellt das Inhaltsstammverzeichnis und das Webstammverzeichnis der Anwendung als `IFileProvider`-Typen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="630b2-107">[IHostingEnvironment](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment) exposes the app's content root and web root as `IFileProvider` types.</span></span>
* <span data-ttu-id="630b2-108">Die [Middleware der statischen Dateien](xref:fundamentals/static-files) verwendet Dateianbieter, um statische Dateien zu suchen.</span><span class="sxs-lookup"><span data-stu-id="630b2-108">[Static File Middleware](xref:fundamentals/static-files) uses File Providers to locate static files.</span></span>
* <span data-ttu-id="630b2-109">[Razor](xref:mvc/views/razor) verwendet Dateianbieter, um Seiten und Ansichten zu suchen.</span><span class="sxs-lookup"><span data-stu-id="630b2-109">[Razor](xref:mvc/views/razor) uses File Providers to locate pages and views.</span></span>
* <span data-ttu-id="630b2-110">.NET Core-Tools verwenden Dateianbieter und Globmuster, um anzugeben, welche Dateien veröffentlicht werden sollen.</span><span class="sxs-lookup"><span data-stu-id="630b2-110">.NET Core tooling uses File Providers and glob patterns to specify which files should be published.</span></span>

<span data-ttu-id="630b2-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="630b2-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/file-providers/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="file-provider-interfaces"></a><span data-ttu-id="630b2-112">Dateianbieterschnittstellen</span><span class="sxs-lookup"><span data-stu-id="630b2-112">File Provider interfaces</span></span>

<span data-ttu-id="630b2-113">Die primäre Schnittstelle ist [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span><span class="sxs-lookup"><span data-stu-id="630b2-113">The primary interface is [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> <span data-ttu-id="630b2-114">`IFileProvider` stellt Methoden zur Verfügung, zum:</span><span class="sxs-lookup"><span data-stu-id="630b2-114">`IFileProvider` exposes methods to:</span></span>

* <span data-ttu-id="630b2-115">Abrufen von Dateiinformationen ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span><span class="sxs-lookup"><span data-stu-id="630b2-115">Obtain file information ([IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)).</span></span>
* <span data-ttu-id="630b2-116">Abrufen von Verzeichnisinformationen ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span><span class="sxs-lookup"><span data-stu-id="630b2-116">Obtain directory information ([IDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.idirectorycontents)).</span></span>
* <span data-ttu-id="630b2-117">Einrichten von Änderungsbenachrichtigungen (mithilfe eines [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span><span class="sxs-lookup"><span data-stu-id="630b2-117">Set up change notifications (using an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken)).</span></span>

<span data-ttu-id="630b2-118">`IFileInfo` stellt Methoden und Eigenschaften für die Arbeit mit Dateien bereit:</span><span class="sxs-lookup"><span data-stu-id="630b2-118">`IFileInfo` provides methods and properties for working with files:</span></span>

* [<span data-ttu-id="630b2-119">Exists</span><span class="sxs-lookup"><span data-stu-id="630b2-119">Exists</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.exists)
* [<span data-ttu-id="630b2-120">IsDirectory</span><span class="sxs-lookup"><span data-stu-id="630b2-120">IsDirectory</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.isdirectory)
* [<span data-ttu-id="630b2-121">Name</span><span class="sxs-lookup"><span data-stu-id="630b2-121">Name</span></span>](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.name)
* <span data-ttu-id="630b2-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in Bytes)</span><span class="sxs-lookup"><span data-stu-id="630b2-122">[Length](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.length) (in bytes)</span></span>
* <span data-ttu-id="630b2-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified)-Datum</span><span class="sxs-lookup"><span data-stu-id="630b2-123">[LastModified](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.lastmodified) date</span></span>

<span data-ttu-id="630b2-124">Mithilfe der [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream)-Methode können Sie die Datei auslesen.</span><span class="sxs-lookup"><span data-stu-id="630b2-124">You can read from the file using the [IFileInfo.CreateReadStream](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo.createreadstream) method.</span></span>

<span data-ttu-id="630b2-125">Die Beispiel-App demonstriert die Konfiguration eines Dateianbieters in `Startup.ConfigureServices` für die Verwendung in der gesamten App über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="630b2-125">The sample app demonstrates how to configure a File Provider in `Startup.ConfigureServices` for use throughout the app via [dependency injection](xref:fundamentals/dependency-injection).</span></span>

## <a name="file-provider-implementations"></a><span data-ttu-id="630b2-126">Dateianbieterimplementierungen</span><span class="sxs-lookup"><span data-stu-id="630b2-126">File Provider implementations</span></span>

<span data-ttu-id="630b2-127">Es sind drei Implementierungen von `IFileProvider` verfügbar.</span><span class="sxs-lookup"><span data-stu-id="630b2-127">Three implementations of `IFileProvider` are available.</span></span>

| <span data-ttu-id="630b2-128">Implementierung</span><span class="sxs-lookup"><span data-stu-id="630b2-128">Implementation</span></span> | <span data-ttu-id="630b2-129">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="630b2-129">Description</span></span> |
| -------------- | ----------- |
| [<span data-ttu-id="630b2-130">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="630b2-130">PhysicalFileProvider</span></span>](#physicalfileprovider) | <span data-ttu-id="630b2-131">Der physische Anbieter wird verwendet, um auf die physischen Systemdateien zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="630b2-131">The physical provider is used to access the system's physical files.</span></span> |
| [<span data-ttu-id="630b2-132">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="630b2-132">ManifestEmbeddedFileProvider</span></span>](#manifestembeddedfileprovider) | <span data-ttu-id="630b2-133">Der eingebettete Manifesanbieter wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="630b2-133">The manifest embedded provider is used to access files embedded in assemblies.</span></span> |
| [<span data-ttu-id="630b2-134">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="630b2-134">CompositeFileProvider</span></span>](#compositefileprovider) | <span data-ttu-id="630b2-135">Der zusammengesetzte Anbieter wird verwendet, um kombinierten Zugriff auf Dateien und Verzeichnisse von einem oder mehreren anderen Anbietern bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="630b2-135">The composite provider is used to provide combined access to files and directories from one or more other providers.</span></span> |

### <a name="physicalfileprovider"></a><span data-ttu-id="630b2-136">PhysicalFileProvider</span><span class="sxs-lookup"><span data-stu-id="630b2-136">PhysicalFileProvider</span></span>

<span data-ttu-id="630b2-137">Der [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) ermöglicht den Zugriff auf das physische Dateisystem.</span><span class="sxs-lookup"><span data-stu-id="630b2-137">The [PhysicalFileProvider](/dotnet/api/microsoft.extensions.fileproviders.physicalfileprovider) provides access to the physical file system.</span></span> <span data-ttu-id="630b2-138">`PhysicalFileProvider` verwendet den [System.IO.File](/dotnet/api/system.io.file)-Typ (für den physischen Anbieter). Alle Pfade werden einem Verzeichnis und seinen untergeordneten Elementen zugeordnet.</span><span class="sxs-lookup"><span data-stu-id="630b2-138">`PhysicalFileProvider` uses the [System.IO.File](/dotnet/api/system.io.file) type (for the physical provider) and scopes all paths to a directory and its children.</span></span> <span data-ttu-id="630b2-139">Diese Zuordnung verhindert den Zugriff auf das Dateisystem außerhalb des angegebenen Verzeichnisses und den untergeordneten Elementen.</span><span class="sxs-lookup"><span data-stu-id="630b2-139">This scoping prevents access to the file system outside of the specified directory and its children.</span></span> <span data-ttu-id="630b2-140">Beim Instanziieren dieses Anbieters ist ein Verzeichnispfad erforderlich, der als Basispfad für alle Anforderungen über diesen Anbieter dient.</span><span class="sxs-lookup"><span data-stu-id="630b2-140">When instantiating this provider, a directory path is required and serves as the base path for all requests made using the provider.</span></span> <span data-ttu-id="630b2-141">Sie können einen `PhysicalFileProvider`-Anbieter direkt instanziieren oder einen `IFileProvider` in einem Konstruktor über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) anfordern.</span><span class="sxs-lookup"><span data-stu-id="630b2-141">You can instantiate a `PhysicalFileProvider` provider directly, or you can request an `IFileProvider` in a constructor through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="630b2-142">**Statische Typen**</span><span class="sxs-lookup"><span data-stu-id="630b2-142">**Static types**</span></span>

<span data-ttu-id="630b2-143">Der folgende Code zeigt die Erstellung eines `PhysicalFileProvider` und dessen Verwendung zum Abrufen von Verzeichnisinhalten und Dateiinformationen:</span><span class="sxs-lookup"><span data-stu-id="630b2-143">The following code shows how to create a `PhysicalFileProvider` and use it to obtain directory contents and file information:</span></span>

```csharp
var provider = new PhysicalFileProvider(applicationRoot);
var contents = provider.GetDirectoryContents(string.Empty);
var fileInfo = provider.GetFileInfo("wwwroot/js/site.js");
```

<span data-ttu-id="630b2-144">Typen im vorherigen Beispiel:</span><span class="sxs-lookup"><span data-stu-id="630b2-144">Types in the preceding example:</span></span>

* <span data-ttu-id="630b2-145">`provider` ist ein `IFileProvider`.</span><span class="sxs-lookup"><span data-stu-id="630b2-145">`provider` is an `IFileProvider`.</span></span>
* <span data-ttu-id="630b2-146">`contents` ist ein `IDirectoryContents`.</span><span class="sxs-lookup"><span data-stu-id="630b2-146">`contents` is an `IDirectoryContents`.</span></span>
* <span data-ttu-id="630b2-147">`fileInfo` ist ein `IFileInfo`.</span><span class="sxs-lookup"><span data-stu-id="630b2-147">`fileInfo` is an `IFileInfo`.</span></span>

<span data-ttu-id="630b2-148">Der Dateianbieter kann verwendet werden, um das durch `applicationRoot` angegebene Verzeichnis zu durchlaufen oder `GetFileInfo` aufzurufen, um die Informationen einer Datei anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="630b2-148">The File Provider can be used to iterate through the directory specified by `applicationRoot` or call `GetFileInfo` to obtain a file's information.</span></span> <span data-ttu-id="630b2-149">Der Dateianbieter hat keinen Zugriff außerhalb des `applicationRoot`-Verzeichnisses.</span><span class="sxs-lookup"><span data-stu-id="630b2-149">The File Provider has no access outside of the `applicationRoot` directory.</span></span>

<span data-ttu-id="630b2-150">Die Beispiel-App erstellt einen Anbieter in `Startup.ConfigureServices`-Klasse der App mit [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span><span class="sxs-lookup"><span data-stu-id="630b2-150">The sample app creates the provider in the app's `Startup.ConfigureServices` class using [IHostingEnvironment.ContentRootFileProvider](/dotnet/api/microsoft.extensions.hosting.ihostingenvironment.contentrootfileprovider):</span></span>

```csharp
var physicalProvider = _env.ContentRootFileProvider;
```

<span data-ttu-id="630b2-151">**Abrufen der Dateianbietertypen mit Abhängigkeitsinjektion**</span><span class="sxs-lookup"><span data-stu-id="630b2-151">**Obtain File Provider types with dependency injection**</span></span>

<span data-ttu-id="630b2-152">Fügen Sie den Anbieter in jeden Klassenkonstruktor ein, und weisen Sie ihn einem lokalen Feld zu.</span><span class="sxs-lookup"><span data-stu-id="630b2-152">Inject the provider into any class constructor and assign it to a local field.</span></span> <span data-ttu-id="630b2-153">Verwenden Sie das Feld in der Methoden der Klasse für den Dateizugriff.</span><span class="sxs-lookup"><span data-stu-id="630b2-153">Use the field throughout the class's methods to access files.</span></span>

<span data-ttu-id="630b2-154">In der Beispiel-App erhält die `IndexModel`-Klasse eine `IFileProvider`-Instanz, um Inhalt des Verzeichnisses für die den Basispfad der App zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="630b2-154">In the sample app, the `IndexModel` class receives an `IFileProvider` instance to obtain directory contents for the app's base path.</span></span>

<span data-ttu-id="630b2-155">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="630b2-155">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml.cs?name=snippet1)]

<span data-ttu-id="630b2-156">Die `IDirectoryContents` werden auf der Seite durchlaufen.</span><span class="sxs-lookup"><span data-stu-id="630b2-156">The `IDirectoryContents` are iterated in the page.</span></span>

<span data-ttu-id="630b2-157">*Pages/Index.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="630b2-157">*Pages/Index.cshtml*:</span></span>

[!code-cshtml[](file-providers/samples/2.x/FileProviderSample/Pages/Index.cshtml?name=snippet1)]

### <a name="manifestembeddedfileprovider"></a><span data-ttu-id="630b2-158">ManifestEmbeddedFileProvider</span><span class="sxs-lookup"><span data-stu-id="630b2-158">ManifestEmbeddedFileProvider</span></span>

<span data-ttu-id="630b2-159">Der [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) wird verwendet, um auf Dateien zuzugreifen, die in Assemblys eingebettet sind.</span><span class="sxs-lookup"><span data-stu-id="630b2-159">The [ManifestEmbeddedFileProvider](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider) is used to access files embedded within assemblies.</span></span> <span data-ttu-id="630b2-160">Der `ManifestEmbeddedFileProvider` verwendet ein in die Assembly kompiliertes Manifest, um die ursprünglichen Pfade der eingebetteten Dateien zu rekonstruieren.</span><span class="sxs-lookup"><span data-stu-id="630b2-160">The `ManifestEmbeddedFileProvider` uses a manifest compiled into the assembly to reconstruct the original paths of the embedded files.</span></span>

<span data-ttu-id="630b2-161">Um ein Manifest der eingebetteten Dateien zu generieren, legen die `<GenerateEmbeddedFilesManifest>`-Eigenschaft auf `true` fest.</span><span class="sxs-lookup"><span data-stu-id="630b2-161">To generate a manifest of the embedded files, set the `<GenerateEmbeddedFilesManifest>` property to `true`.</span></span> <span data-ttu-id="630b2-162">Geben Sie die einzubettenden Dateien mit [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects) an:</span><span class="sxs-lookup"><span data-stu-id="630b2-162">Specify the files to embed with [&lt;EmbeddedResource&gt;](/dotnet/core/tools/csproj#default-compilation-includes-in-net-core-projects):</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/FileProviderSample.csproj?highlight=6,14)]

<span data-ttu-id="630b2-163">Verwenden Sie [Globmuster](#glob-patterns), um eine oder mehr Dateien anzugeben, die in die Assembly eingebettet werden sollen.</span><span class="sxs-lookup"><span data-stu-id="630b2-163">Use [glob patterns](#glob-patterns) to specify one or more files to embed into the assembly.</span></span>

<span data-ttu-id="630b2-164">Die Beispiel-App erstellt einen `ManifestEmbeddedFileProvider` und übergibt die aktuell ausgeführte Assembly an den Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="630b2-164">The sample app creates an `ManifestEmbeddedFileProvider` and passes the currently executing assembly to its constructor.</span></span>

<span data-ttu-id="630b2-165">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="630b2-165">*Startup.cs*:</span></span>

```csharp
var manifestEmbeddedProvider = 
    new ManifestEmbeddedFileProvider(Assembly.GetEntryAssembly());
```

<span data-ttu-id="630b2-166">Mit zusätzlichen Überladungen können Sie:</span><span class="sxs-lookup"><span data-stu-id="630b2-166">Additional overloads allow you to:</span></span>

* <span data-ttu-id="630b2-167">Einen relativen Dateipfad angeben.</span><span class="sxs-lookup"><span data-stu-id="630b2-167">Specify a relative file path.</span></span>
* <span data-ttu-id="630b2-168">Dateien einem zuletzt geänderten Datum zuordnen.</span><span class="sxs-lookup"><span data-stu-id="630b2-168">Scope files to a last modified date.</span></span>
* <span data-ttu-id="630b2-169">Die eingebettete Ressource benennen, die das eingebettete Dateimanifest enthält.</span><span class="sxs-lookup"><span data-stu-id="630b2-169">Name the embedded resource containing the embedded file manifest.</span></span>

| <span data-ttu-id="630b2-170">Überladung</span><span class="sxs-lookup"><span data-stu-id="630b2-170">Overload</span></span> | <span data-ttu-id="630b2-171">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="630b2-171">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="630b2-172">ManifestEmbeddedFileProvider(Assembly, String)</span><span class="sxs-lookup"><span data-stu-id="630b2-172">ManifestEmbeddedFileProvider(Assembly, String)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_) | <span data-ttu-id="630b2-173">Akzeptiert einen optionalen relativen `root`-Pfadparameter.</span><span class="sxs-lookup"><span data-stu-id="630b2-173">Accepts an optional `root` relative path parameter.</span></span> <span data-ttu-id="630b2-174">Geben Sie `root` an, um Aufrufe an [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) den Ressourcen im bereitgestellten Pfad zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="630b2-174">Specify the `root` to scope calls to [GetDirectoryContents](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.getdirectorycontents) to those resources under the provided path.</span></span> |
| [<span data-ttu-id="630b2-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="630b2-175">ManifestEmbeddedFileProvider(Assembly, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_DateTimeOffset_) | <span data-ttu-id="630b2-176">Akzeptiert einen optionalen relativen `root`-Pfadparameter und einen `lastModified` ([DateTimeOffset](/dotnet/api/system.datetimeoffset))-Datumsparameter.</span><span class="sxs-lookup"><span data-stu-id="630b2-176">Accepts an optional `root` relative path parameter and a `lastModified` date ([DateTimeOffset](/dotnet/api/system.datetimeoffset)) parameter.</span></span> <span data-ttu-id="630b2-177">Das `lastModified`-Datum ordnet das letzte Änderungsdatum für die [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo)-Instanzen zu, die vom [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider) zurückgegeben wurden.</span><span class="sxs-lookup"><span data-stu-id="630b2-177">The `lastModified` date scopes the last modification date for the [IFileInfo](/dotnet/api/microsoft.extensions.fileproviders.ifileinfo) instances returned by the [IFileProvider](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider).</span></span> |
| [<span data-ttu-id="630b2-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span><span class="sxs-lookup"><span data-stu-id="630b2-178">ManifestEmbeddedFileProvider(Assembly, String, String, DateTimeOffset)</span></span>](/dotnet/api/microsoft.extensions.fileproviders.manifestembeddedfileprovider.-ctor#Microsoft_Extensions_FileProviders_ManifestEmbeddedFileProvider__ctor_System_Reflection_Assembly_System_String_System_String_System_DateTimeOffset_) | <span data-ttu-id="630b2-179">Akzeptiert einen optionalen, relativen `root`-Pfad, ein `lastModified`-Datum und `manifestName`-Parameter.</span><span class="sxs-lookup"><span data-stu-id="630b2-179">Accepts an optional `root` relative path, `lastModified` date, and `manifestName` parameters.</span></span> <span data-ttu-id="630b2-180">`manifestName` stellt den Namen der eingebetteten Ressource dar, die das Manifest enthält.</span><span class="sxs-lookup"><span data-stu-id="630b2-180">The `manifestName` represents the name of the embedded resource containing the manifest.</span></span> |

### <a name="compositefileprovider"></a><span data-ttu-id="630b2-181">CompositeFileProvider</span><span class="sxs-lookup"><span data-stu-id="630b2-181">CompositeFileProvider</span></span>

<span data-ttu-id="630b2-182">Der [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) kombiniert `IFileProvider`-Instanzen, die eine einzelne Schnittstelle zum Arbeiten mit Dateien von mehreren Anbietern verfügbar machen.</span><span class="sxs-lookup"><span data-stu-id="630b2-182">The [CompositeFileProvider](/dotnet/api/microsoft.extensions.fileproviders.compositefileprovider) combines `IFileProvider` instances, exposing a single interface for working with files from multiple providers.</span></span> <span data-ttu-id="630b2-183">Beim Erstellen des `CompositeFileProvider` übergeben Sie eine oder mehrere `IFileProvider`-Instanzen an seinen Konstruktor.</span><span class="sxs-lookup"><span data-stu-id="630b2-183">When creating the `CompositeFileProvider`, pass one or more `IFileProvider` instances to its constructor.</span></span>

<span data-ttu-id="630b2-184">In der Beispiel-App stellt ein `PhysicalFileProvider` und ein `ManifestEmbeddedFileProvider` Dateien an ein `CompositeFileProvider`, der im Dienstcontainer der App registriert ist:</span><span class="sxs-lookup"><span data-stu-id="630b2-184">In the sample app, a `PhysicalFileProvider` and a `ManifestEmbeddedFileProvider` provide files to a `CompositeFileProvider` registered in the app's service container:</span></span>

[!code-csharp[](file-providers/samples/2.x/FileProviderSample/Startup.cs?name=snippet1)]

## <a name="watch-for-changes"></a><span data-ttu-id="630b2-185">Überwachen auf Änderungen</span><span class="sxs-lookup"><span data-stu-id="630b2-185">Watch for changes</span></span>

<span data-ttu-id="630b2-186">Die [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch)-Methode bietet ein Szenario, eine oder mehrere Dateien oder Verzeichnisse auf Änderungen zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="630b2-186">The [IFileProvider.Watch](/dotnet/api/microsoft.extensions.fileproviders.ifileprovider.watch) method provides a scenario to watch one or more files or directories for changes.</span></span> <span data-ttu-id="630b2-187">`Watch`akzeptiert eine Pfadzeichenfolge, die [Globmuster](#glob-patterns) verwenden kann, um mehrere Dateien anzugeben.</span><span class="sxs-lookup"><span data-stu-id="630b2-187">`Watch` accepts a path string, which can use [glob patterns](#glob-patterns) to specify multiple files.</span></span> <span data-ttu-id="630b2-188">`Watch` gibt ein [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken) zurück.</span><span class="sxs-lookup"><span data-stu-id="630b2-188">`Watch` returns an [IChangeToken](/dotnet/api/microsoft.extensions.primitives.ichangetoken).</span></span> <span data-ttu-id="630b2-189">Das Änderungstoken macht folgendes verfügbar:</span><span class="sxs-lookup"><span data-stu-id="630b2-189">The change token exposes:</span></span>

* <span data-ttu-id="630b2-190">[HasChanged:](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged) Eine Eigenschaft, die überprüft werden kann, um festzustellen, ob eine Änderung vorgenommen wurde.</span><span class="sxs-lookup"><span data-stu-id="630b2-190">[HasChanged](/dotnet/api/microsoft.extensions.primitives.ichangetoken.haschanged): A property that can be inspected to determine if a change has occurred.</span></span>
* <span data-ttu-id="630b2-191">[RegisterChangeCallback:](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback) Diese Methode wird aufgerufen, wenn Änderungen an der angegebenen Pfadzeichenfolge erkannt werden.</span><span class="sxs-lookup"><span data-stu-id="630b2-191">[RegisterChangeCallback](/dotnet/api/microsoft.extensions.primitives.ichangetoken.registerchangecallback): Called when changes are detected to the specified path string.</span></span> <span data-ttu-id="630b2-192">Jedes Änderungstoken ruft nur seine zugeordneten Rückrufe als Antwort auf eine einzelne Änderung auf.</span><span class="sxs-lookup"><span data-stu-id="630b2-192">Each change token only calls its associated callback in response to a single change.</span></span> <span data-ttu-id="630b2-193">Zur Aktivierung der konstanten Überwachung können Sie wie unten dargestellt eine [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) verwenden oder `IChangeToken`-Instanzen als Reaktion auf Änderungen neu erstellen.</span><span class="sxs-lookup"><span data-stu-id="630b2-193">To enable constant monitoring, use a [TaskCompletionSource](/dotnet/api/system.threading.tasks.taskcompletionsource-1) (shown below) or recreate `IChangeToken` instances in response to changes.</span></span>

<span data-ttu-id="630b2-194">In der Beispiel-App wird die *WatchConsole*-Konsolen-App konfiguriert, damit sie bei einer Änderung einer Textdatei eine Meldung anzeigt:</span><span class="sxs-lookup"><span data-stu-id="630b2-194">In the sample app, the *WatchConsole* console app is configured to display a message whenever a text file is modified:</span></span>

[!code-csharp[](file-providers/samples/2.x/WatchConsole/Program.cs?name=snippet1&highlight=1-2,16,19-20)]

<span data-ttu-id="630b2-195">Einige Dateisysteme, z.B. Docker-Container und Netzwerkfreigaben, senden Änderungsmeldungen möglicherweise nicht zuverlässig .</span><span class="sxs-lookup"><span data-stu-id="630b2-195">Some file systems, such as Docker containers and network shares, may not reliably send change notifications.</span></span> <span data-ttu-id="630b2-196">Legen Sie die `DOTNET_USE_POLLING_FILE_WATCHER`-Umgebungsvariable auf `1` oder `true` fest, um das Dateisystem alle 4 Sekunden nach Änderungen zu untersuchen (nicht konfigurierbar).</span><span class="sxs-lookup"><span data-stu-id="630b2-196">Set the `DOTNET_USE_POLLING_FILE_WATCHER` environment variable to `1` or `true` to poll the file system for changes every four seconds (not configurable).</span></span>

## <a name="glob-patterns"></a><span data-ttu-id="630b2-197">Globmuster</span><span class="sxs-lookup"><span data-stu-id="630b2-197">Glob patterns</span></span>

<span data-ttu-id="630b2-198">Dateisystempfade verwenden Platzhaltermuster namens *Globmuster*.</span><span class="sxs-lookup"><span data-stu-id="630b2-198">File system paths use wildcard patterns called *glob (or globbing) patterns*.</span></span> <span data-ttu-id="630b2-199">Geben Sie Gruppen von Dateien mit diesen Mustern an.</span><span class="sxs-lookup"><span data-stu-id="630b2-199">Specify groups of files with these patterns.</span></span> <span data-ttu-id="630b2-200">Die zwei Platzhalterzeichen sind `*` und `**`:</span><span class="sxs-lookup"><span data-stu-id="630b2-200">The two wildcard characters are `*` and `**`:</span></span>

**`*`**  
<span data-ttu-id="630b2-201">Überprüft alles auf der aktuellen Ordnerebene, jeden Dateinamen oder jede Dateierweiterung.</span><span class="sxs-lookup"><span data-stu-id="630b2-201">Matches anything at the current folder level, any filename, or any file extension.</span></span> <span data-ttu-id="630b2-202">Übereinstimmungen werden durch `/`- und `.`-Zeichen im Dateipfad beendet.</span><span class="sxs-lookup"><span data-stu-id="630b2-202">Matches are terminated by `/` and `.` characters in the file path.</span></span>

**`**`**  
<span data-ttu-id="630b2-203">Überprüft alles auf mehrere Verzeichnisebenen.</span><span class="sxs-lookup"><span data-stu-id="630b2-203">Matches anything across multiple directory levels.</span></span> <span data-ttu-id="630b2-204">Kann verwendet werden, um rekursiv eine Übereinstimmung für viele Dateien innerhalb einer Verzeichnishierarchie zu finden.</span><span class="sxs-lookup"><span data-stu-id="630b2-204">Can be used to recursively match many files within a directory hierarchy.</span></span>

<span data-ttu-id="630b2-205">**Beispiele für Globmuster**</span><span class="sxs-lookup"><span data-stu-id="630b2-205">**Glob pattern examples**</span></span>

**`directory/file.txt`**  
<span data-ttu-id="630b2-206">Entspricht einer bestimmten Datei in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="630b2-206">Matches a specific file in a specific directory.</span></span>

**`directory/*.txt`**  
<span data-ttu-id="630b2-207">Entspricht allen Dateien mit *.txt*-Erweiterung in einem bestimmten Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="630b2-207">Matches all files with *.txt* extension in a specific directory.</span></span>

**`directory/*/appsettings.json`**  
<span data-ttu-id="630b2-208">Entspricht allen `appsettings.json`-Dateien in Verzeichnissen auf der nächsttieferen Ebene des *directory*-Ordners.</span><span class="sxs-lookup"><span data-stu-id="630b2-208">Matches all `appsettings.json` files in directories exactly one level below the *directory* folder.</span></span>

**`directory/**/*.txt`**  
<span data-ttu-id="630b2-209">Entspricht allen Dateien mit *.txt*-Erweiterung, die an einer beliebigen Stelle unter dem *directory*-Ordner gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="630b2-209">Matches all files with *.txt* extension found anywhere under the *directory* folder.</span></span>
