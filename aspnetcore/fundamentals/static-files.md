---
title: Statische Dateien in ASP.NET Core
author: rick-anderson
description: Hier erfahren Sie, wie statische Dateien bereitgestellt und gesichert werden und wie das Verhalten von Middleware beim Hosting statischer Dateien in einer ASP.NET Core-Web-App konfiguriert wird.
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: fundamentals/static-files
ms.openlocfilehash: bcd179bd89b90d80fc81645b24296a1fa70cca0a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64888385"
---
# <a name="static-files-in-aspnet-core"></a><span data-ttu-id="a52d7-103">Statische Dateien in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a52d7-103">Static files in ASP.NET Core</span></span>

<span data-ttu-id="a52d7-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="a52d7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="a52d7-105">Bei statischen Dateien wie HTML, CSS, Images und JavaScript handelt es sich um Objekte, die Clients von einer ASP.NET Core-App direkt bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="a52d7-105">Static files, such as HTML, CSS, images, and JavaScript, are assets an ASP.NET Core app serves directly to clients.</span></span> <span data-ttu-id="a52d7-106">Damit diese Dateien bereitgestellt werden können, sind einige Konfigurationsschritte erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a52d7-106">Some configuration is required to enable serving of these files.</span></span>

<span data-ttu-id="a52d7-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a52d7-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/static-files/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="serve-static-files"></a><span data-ttu-id="a52d7-108">Bereitstellen statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="a52d7-108">Serve static files</span></span>

<span data-ttu-id="a52d7-109">Statische Dateien werden im Webstammverzeichnis Ihres Projekts gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a52d7-109">Static files are stored within your project's web root directory.</span></span> <span data-ttu-id="a52d7-110">Das Standardverzeichnis lautet *\<content_root>/wwwroot*, es kann jedoch mit der Methode [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) geändert werden.</span><span class="sxs-lookup"><span data-stu-id="a52d7-110">The default directory is *\<content_root>/wwwroot*, but it can be changed via the [UseWebRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usewebroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseWebRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) method.</span></span> <span data-ttu-id="a52d7-111">Weitere Informationen finden Sie unter [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) und [Webstammverzeichnis](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a52d7-111">See [Content root](xref:fundamentals/index#content-root) and [Web root](xref:fundamentals/index#web-root) for more information.</span></span>

<span data-ttu-id="a52d7-112">Der Web-Host der App muss über das Inhaltsstammverzeichnis informiert werden.</span><span class="sxs-lookup"><span data-stu-id="a52d7-112">The app's web host must be made aware of the content root directory.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="a52d7-113">Die Methode `WebHost.CreateDefaultBuilder` legt das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest:</span><span class="sxs-lookup"><span data-stu-id="a52d7-113">The `WebHost.CreateDefaultBuilder` method sets the content root to the current directory:</span></span>

[!code-csharp[](../common/samples/WebApplication1DotNetCore2.0App/Program.cs?name=snippet_Main&highlight=9)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="a52d7-114">Legen Sie das Inhaltsstammverzeichnis auf das aktuelle Verzeichnis fest, indem Sie [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) innerhalb von `Program.Main` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="a52d7-114">Set the content root to the current directory by invoking [UseContentRoot](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usecontentroot#Microsoft_AspNetCore_Hosting_HostingAbstractionsWebHostBuilderExtensions_UseContentRoot_Microsoft_AspNetCore_Hosting_IWebHostBuilder_System_String_) inside of `Program.Main`:</span></span>

[!code-csharp[](static-files/samples/1x/Program.cs?name=snippet_ProgramClass&highlight=7)]

::: moniker-end

<span data-ttu-id="a52d7-115">Auf statische Dateien kann über einen Pfad relativ zum Webstammverzeichnis zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="a52d7-115">Static files are accessible via a path relative to the web root.</span></span> <span data-ttu-id="a52d7-116">Die Projektvorlage der **Webanwendung** verfügt beispielsweise über mehrere Ordner innerhalb des Ordners *wwwroot*:</span><span class="sxs-lookup"><span data-stu-id="a52d7-116">For example, the **Web Application** project template contains several folders within the *wwwroot* folder:</span></span>

* <span data-ttu-id="a52d7-117">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="a52d7-117">**wwwroot**</span></span>
  * <span data-ttu-id="a52d7-118">**css**</span><span class="sxs-lookup"><span data-stu-id="a52d7-118">**css**</span></span>
  * <span data-ttu-id="a52d7-119">**images**</span><span class="sxs-lookup"><span data-stu-id="a52d7-119">**images**</span></span>
  * <span data-ttu-id="a52d7-120">**js**</span><span class="sxs-lookup"><span data-stu-id="a52d7-120">**js**</span></span>

<span data-ttu-id="a52d7-121">Das URI-Format für den Zugriff auf eine Datei im Unterordner *Images* lautet *http://\<server_address>/images/\<image_file_name>*.</span><span class="sxs-lookup"><span data-stu-id="a52d7-121">The URI format to access a file in the *images* subfolder is *http://\<server_address>/images/\<image_file_name>*.</span></span> <span data-ttu-id="a52d7-122">Beispiel: *http://localhost:9189/images/banner3.svg*</span><span class="sxs-lookup"><span data-stu-id="a52d7-122">For example, *http://localhost:9189/images/banner3.svg*.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a52d7-123">Wenn .NET Framework die Zielkomponente ist, fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zu Ihrem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="a52d7-123">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="a52d7-124">Wenn .NET Core die Zielkomponente ist, ist dieses Paket im Metapaket [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) enthalten.</span><span class="sxs-lookup"><span data-stu-id="a52d7-124">If targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) includes this package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a52d7-125">Wenn .NET Framework die Zielkomponente ist, fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zu Ihrem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="a52d7-125">If targeting .NET Framework, add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span> <span data-ttu-id="a52d7-126">Wenn .NET Core die Zielkomponente ist, ist dieses Paket im Metapaket [Microsoft.AspNetCore.All](xref:fundamentals/metapackage) enthalten.</span><span class="sxs-lookup"><span data-stu-id="a52d7-126">If targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) includes this package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="a52d7-127">Fügen Sie das Paket [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) zu Ihrem Projekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="a52d7-127">Add the [Microsoft.AspNetCore.StaticFiles](https://www.nuget.org/packages/Microsoft.AspNetCore.StaticFiles/) package to your project.</span></span>

::: moniker-end

<span data-ttu-id="a52d7-128">Konfigurieren Sie die [Middleware](xref:fundamentals/middleware/index), über die Sie statische Dateien bereitstellen können.</span><span class="sxs-lookup"><span data-stu-id="a52d7-128">Configure the [middleware](xref:fundamentals/middleware/index) which enables the serving of static files.</span></span>

### <a name="serve-files-inside-of-web-root"></a><span data-ttu-id="a52d7-129">Bereitstellen von Dateien innerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="a52d7-129">Serve files inside of web root</span></span>

<span data-ttu-id="a52d7-130">Rufen Sie die Methode [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) in `Startup.Configure` auf:</span><span class="sxs-lookup"><span data-stu-id="a52d7-130">Invoke the [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method within `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupStaticFiles.cs?name=snippet_ConfigureMethod&highlight=3)]

<span data-ttu-id="a52d7-131">Die parameterlose Überladung der Methode `UseStaticFiles` markiert die Dateien im Webstammverzeichnis als bereitstellbar.</span><span class="sxs-lookup"><span data-stu-id="a52d7-131">The parameterless `UseStaticFiles` method overload marks the files in web root as servable.</span></span> <span data-ttu-id="a52d7-132">Folgendes Markup verweist auf *wwwroot/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="a52d7-132">The following markup references *wwwroot/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_wwwroot)]

<span data-ttu-id="a52d7-133">Im vorhergehenden Code verweist die Tilde (`~/`) auf den Webstamm.</span><span class="sxs-lookup"><span data-stu-id="a52d7-133">In the preceding code, the tilde character `~/` points to webroot.</span></span> <span data-ttu-id="a52d7-134">Weitere Informationen finden Sie unter [Webstamm](xref:fundamentals/index#web-root).</span><span class="sxs-lookup"><span data-stu-id="a52d7-134">For more information, see [Web root](xref:fundamentals/index#web-root).</span></span>

### <a name="serve-files-outside-of-web-root"></a><span data-ttu-id="a52d7-135">Bereitstellen von Dateien außerhalb des Webstammverzeichnisses</span><span class="sxs-lookup"><span data-stu-id="a52d7-135">Serve files outside of web root</span></span>

<span data-ttu-id="a52d7-136">Ziehen Sie eine Verzeichnishierarchie in Betracht, bei der sich die bereitzustellenden statischen Dateien außerhalb des Webstammverzeichnisses befinden:</span><span class="sxs-lookup"><span data-stu-id="a52d7-136">Consider a directory hierarchy in which the static files to be served reside outside of the web root:</span></span>

* <span data-ttu-id="a52d7-137">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="a52d7-137">**wwwroot**</span></span>
  * <span data-ttu-id="a52d7-138">**css**</span><span class="sxs-lookup"><span data-stu-id="a52d7-138">**css**</span></span>
  * <span data-ttu-id="a52d7-139">**images**</span><span class="sxs-lookup"><span data-stu-id="a52d7-139">**images**</span></span>
  * <span data-ttu-id="a52d7-140">**js**</span><span class="sxs-lookup"><span data-stu-id="a52d7-140">**js**</span></span>
* <span data-ttu-id="a52d7-141">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="a52d7-141">**MyStaticFiles**</span></span>
  * <span data-ttu-id="a52d7-142">**images**</span><span class="sxs-lookup"><span data-stu-id="a52d7-142">**images**</span></span>
    * <span data-ttu-id="a52d7-143">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="a52d7-143">*banner1.svg*</span></span>

<span data-ttu-id="a52d7-144">Über eine Anforderung kann auf die Datei *banner1.svg* zugegriffen werden, indem die Middleware für statische Dateien wie folgt konfiguriert wird:</span><span class="sxs-lookup"><span data-stu-id="a52d7-144">A request can access the *banner1.svg* file by configuring the Static File Middleware as follows:</span></span>

[!code-csharp[](static-files/samples/1x/StartupTwoStaticFiles.cs?name=snippet_ConfigureMethod&highlight=5-10)]

<span data-ttu-id="a52d7-145">Im vorangehenden Code wird die *MyStaticFiles*-Verzeichnishierarchie öffentlich über das URI-Segment *StaticFiles* verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="a52d7-145">In the preceding code, the *MyStaticFiles* directory hierarchy is exposed publicly via the *StaticFiles* URI segment.</span></span> <span data-ttu-id="a52d7-146">Über eine Anforderung an *http://\<server_address>/StaticFiles/images/banner1.svg* wird die Datei *banner1.svg* bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a52d7-146">A request to *http://\<server_address>/StaticFiles/images/banner1.svg* serves the *banner1.svg* file.</span></span>

<span data-ttu-id="a52d7-147">Folgendes Markup verweist auf *MyStaticFiles/images/banner1.svg*:</span><span class="sxs-lookup"><span data-stu-id="a52d7-147">The following markup references *MyStaticFiles/images/banner1.svg*:</span></span>

[!code-cshtml[](static-files/samples/1x/Views/Home/Index.cshtml?name=snippet_static_file_outside)]

### <a name="set-http-response-headers"></a><span data-ttu-id="a52d7-148">Festlegen von HTTP-Antwortheadern</span><span class="sxs-lookup"><span data-stu-id="a52d7-148">Set HTTP response headers</span></span>

<span data-ttu-id="a52d7-149">Mit einem [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions)-Objekt können HTTP-Antwortheader festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="a52d7-149">A [StaticFileOptions](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions) object can be used to set HTTP response headers.</span></span> <span data-ttu-id="a52d7-150">Neben der Konfiguration statischer, über das Webstammverzeichnis bereitgestellter Dateien wird mit dem folgenden Code der Header `Cache-Control` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a52d7-150">In addition to configuring static file serving from the web root, the following code sets the `Cache-Control` header:</span></span>

[!code-csharp[](static-files/samples/1x/StartupAddHeader.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="a52d7-151">Die Methode [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) ist im Paket [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) enthalten.</span><span class="sxs-lookup"><span data-stu-id="a52d7-151">The [HeaderDictionaryExtensions.Append](/dotnet/api/microsoft.aspnetcore.http.headerdictionaryextensions.append) method exists in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

<span data-ttu-id="a52d7-152">Die Dateien wurden 10 Minuten lang (600 Sekunden) in der Entwicklungsumgebung öffentlich zwischenspeicherbar gemacht:</span><span class="sxs-lookup"><span data-stu-id="a52d7-152">The files have been made publicly cacheable for 10 minutes (600 seconds) in the Development environment:</span></span>

![Antwortheader mit dem Cache-Control-Header wurden hinzugefügt](static-files/_static/add-header.png)

## <a name="static-file-authorization"></a><span data-ttu-id="a52d7-154">Autorisierung statischer Dateien</span><span class="sxs-lookup"><span data-stu-id="a52d7-154">Static file authorization</span></span>

<span data-ttu-id="a52d7-155">Die Middleware für statische Dateien bietet keine Autorisierungsüberprüfungen.</span><span class="sxs-lookup"><span data-stu-id="a52d7-155">The Static File Middleware doesn't provide authorization checks.</span></span> <span data-ttu-id="a52d7-156">Alle über die Middleware bereitgestellten Dateien, Dateien unter *wwwroot* inbegriffen, sind öffentlich zugänglich.</span><span class="sxs-lookup"><span data-stu-id="a52d7-156">Any files served by it, including those under *wwwroot*, are publicly accessible.</span></span> <span data-ttu-id="a52d7-157">So stellen Sie Dateien basierend auf der Autorisierung bereit:</span><span class="sxs-lookup"><span data-stu-id="a52d7-157">To serve files based on authorization:</span></span>

* <span data-ttu-id="a52d7-158">Speichern Sie sie außerhalb von *wwwroot* sowie außerhalb sämtlicher Verzeichnisse, auf die die Middleware für statische Dateien zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="a52d7-158">Store them outside of *wwwroot* and any directory accessible to the Static File Middleware.</span></span>
* <span data-ttu-id="a52d7-159">Stellen Sie sie über eine Aktionsmethode bereit, für die die Autorisierung gilt.</span><span class="sxs-lookup"><span data-stu-id="a52d7-159">Serve them via an action method to which authorization is applied.</span></span> <span data-ttu-id="a52d7-160">Geben Sie ein [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult)-Objekt zurück:</span><span class="sxs-lookup"><span data-stu-id="a52d7-160">Return a [FileResult](/dotnet/api/microsoft.aspnetcore.mvc.fileresult) object:</span></span>

  [!code-csharp[](static-files/samples/1x/Controllers/HomeController.cs?name=snippet_BannerImageAction)]

## <a name="enable-directory-browsing"></a><span data-ttu-id="a52d7-161">Aktivieren der Verzeichnissuche</span><span class="sxs-lookup"><span data-stu-id="a52d7-161">Enable directory browsing</span></span>

<span data-ttu-id="a52d7-162">Über die Verzeichnissuche können Benutzer Ihrer Web-App eine Verzeichnisliste und Dateien innerhalb eines angegebenen Verzeichnisses anzeigen.</span><span class="sxs-lookup"><span data-stu-id="a52d7-162">Directory browsing allows users of your web app to see a directory listing and files within a specified directory.</span></span> <span data-ttu-id="a52d7-163">Die Verzeichnissuche ist aus Sicherheitsgründen standardmäßig deaktiviert (siehe [Überlegungen](#considerations)).</span><span class="sxs-lookup"><span data-stu-id="a52d7-163">Directory browsing is disabled by default for security reasons (see [Considerations](#considerations)).</span></span> <span data-ttu-id="a52d7-164">Aktivieren Sie die Verzeichnissuche, indem Sie die Methode [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) in `Startup.Configure` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="a52d7-164">Enable directory browsing by invoking the [UseDirectoryBrowser](/dotnet/api/microsoft.aspnetcore.builder.directorybrowserextensions.usedirectorybrowser#Microsoft_AspNetCore_Builder_DirectoryBrowserExtensions_UseDirectoryBrowser_Microsoft_AspNetCore_Builder_IApplicationBuilder_Microsoft_AspNetCore_Builder_DirectoryBrowserOptions_) method in `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=12-17)]

<span data-ttu-id="a52d7-165">Fügen Sie erforderliche Dienste hinzu, indem Sie die Methode [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) von `Startup.ConfigureServices` aufrufen:</span><span class="sxs-lookup"><span data-stu-id="a52d7-165">Add required services by invoking the [AddDirectoryBrowser](/dotnet/api/microsoft.extensions.dependencyinjection.directorybrowserserviceextensions.adddirectorybrowser#Microsoft_Extensions_DependencyInjection_DirectoryBrowserServiceExtensions_AddDirectoryBrowser_Microsoft_Extensions_DependencyInjection_IServiceCollection_) method from `Startup.ConfigureServices`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureServicesMethod&highlight=3)]

<span data-ttu-id="a52d7-166">Der vorangehende Code ermöglicht die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* mit Links zu den einzelnen Dateien und Ordnern:</span><span class="sxs-lookup"><span data-stu-id="a52d7-166">The preceding code allows directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*, with links to each file and folder:</span></span>

![Verzeichnissuche](static-files/_static/dir-browse.png)

<span data-ttu-id="a52d7-168">Weitere Informationen zu den Sicherheitsrisiken bei der Aktivierung der Suche finden Sie unter [Überlegungen](#considerations).</span><span class="sxs-lookup"><span data-stu-id="a52d7-168">See [Considerations](#considerations) on the security risks when enabling browsing.</span></span>

<span data-ttu-id="a52d7-169">Beachten Sie die beiden `UseStaticFiles`-Aufrufe im folgenden Beispiel.</span><span class="sxs-lookup"><span data-stu-id="a52d7-169">Note the two `UseStaticFiles` calls in the following example.</span></span> <span data-ttu-id="a52d7-170">Durch den ersten Aufruf wird die Bereitstellung statischer Dateien im Ordner *wwwroot* aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a52d7-170">The first call enables the serving of static files in the *wwwroot* folder.</span></span> <span data-ttu-id="a52d7-171">Durch den zweiten Aufruf wird die Verzeichnissuche im Ordner *wwwroot/images* über die URL *http://\<server_address>/MyImages* aktiviert:</span><span class="sxs-lookup"><span data-stu-id="a52d7-171">The second call enables directory browsing of the *wwwroot/images* folder using the URL *http://\<server_address>/MyImages*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupBrowse.cs?name=snippet_ConfigureMethod&highlight=3,5)]

## <a name="serve-a-default-document"></a><span data-ttu-id="a52d7-172">Bereitstellen eines Standarddokuments</span><span class="sxs-lookup"><span data-stu-id="a52d7-172">Serve a default document</span></span>

<span data-ttu-id="a52d7-173">Durch das Festlegen einer Standardstartseite wird Besuchern Ihrer Website ein logischer Ausgangspunkt geboten.</span><span class="sxs-lookup"><span data-stu-id="a52d7-173">Setting a default home page provides visitors a logical starting point when visiting your site.</span></span> <span data-ttu-id="a52d7-174">Rufen Sie die Methode [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) über `Startup.Configure` auf, um eine Standardseite bereitzustellen, ohne dass der Benutzer den URI vollständig qualifizieren muss:</span><span class="sxs-lookup"><span data-stu-id="a52d7-174">To serve a default page without the user fully qualifying the URI, call the [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) method from `Startup.Configure`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupEmpty.cs?name=snippet_ConfigureMethod&highlight=3)]

> [!IMPORTANT]
> <span data-ttu-id="a52d7-175">`UseDefaultFiles` muss vor `UseStaticFiles` aufgerufen werden, damit die Standarddatei bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a52d7-175">`UseDefaultFiles` must be called before `UseStaticFiles` to serve the default file.</span></span> <span data-ttu-id="a52d7-176">`UseDefaultFiles` ist ein URL-Rewriter, der die Datei nicht verarbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="a52d7-176">`UseDefaultFiles` is a URL rewriter that doesn't actually serve the file.</span></span> <span data-ttu-id="a52d7-177">Aktivieren Sie die Middleware für statische Dateien über `UseStaticFiles`, um die Datei bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="a52d7-177">Enable Static File Middleware via `UseStaticFiles` to serve the file.</span></span>

<span data-ttu-id="a52d7-178">Bei `UseDefaultFiles` werden bei Anforderungen an einen Ordner folgende Dateien durchsucht:</span><span class="sxs-lookup"><span data-stu-id="a52d7-178">With `UseDefaultFiles`, requests to a folder search for:</span></span>

* <span data-ttu-id="a52d7-179">*default.htm*</span><span class="sxs-lookup"><span data-stu-id="a52d7-179">*default.htm*</span></span>
* <span data-ttu-id="a52d7-180">*default.html*</span><span class="sxs-lookup"><span data-stu-id="a52d7-180">*default.html*</span></span>
* <span data-ttu-id="a52d7-181">*index.htm*</span><span class="sxs-lookup"><span data-stu-id="a52d7-181">*index.htm*</span></span>
* <span data-ttu-id="a52d7-182">*index.html*</span><span class="sxs-lookup"><span data-stu-id="a52d7-182">*index.html*</span></span>

<span data-ttu-id="a52d7-183">Die erste in der Liste gefundene Datei wird bereitgestellt, als wäre die Anforderung der vollständig qualifizierte URI.</span><span class="sxs-lookup"><span data-stu-id="a52d7-183">The first file found from the list is served as though the request were the fully qualified URI.</span></span> <span data-ttu-id="a52d7-184">Die Browser-URL spiegelt weiterhin den angeforderten URI wider.</span><span class="sxs-lookup"><span data-stu-id="a52d7-184">The browser URL continues to reflect the URI requested.</span></span>

<span data-ttu-id="a52d7-185">Mit dem folgenden Code wird der Standarddateiname in *mydefault.html* geändert:</span><span class="sxs-lookup"><span data-stu-id="a52d7-185">The following code changes the default file name to *mydefault.html*:</span></span>

[!code-csharp[](static-files/samples/1x/StartupDefault.cs?name=snippet_ConfigureMethod)]

## <a name="usefileserver"></a><span data-ttu-id="a52d7-186">UseFileServer</span><span class="sxs-lookup"><span data-stu-id="a52d7-186">UseFileServer</span></span>

<span data-ttu-id="a52d7-187">In [UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) werden die Funktionen von `UseStaticFiles`, `UseDefaultFiles` und `UseDirectoryBrowser` miteinander kombiniert.</span><span class="sxs-lookup"><span data-stu-id="a52d7-187">[UseFileServer](/dotnet/api/microsoft.aspnetcore.builder.fileserverextensions.usefileserver#Microsoft_AspNetCore_Builder_FileServerExtensions_UseFileServer_Microsoft_AspNetCore_Builder_IApplicationBuilder_) combines the functionality of `UseStaticFiles`, `UseDefaultFiles`, and `UseDirectoryBrowser`.</span></span>

<span data-ttu-id="a52d7-188">Mit dem folgenden Code wird die Bereitstellung statischer Dateien und der Standarddatei aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a52d7-188">The following code enables the serving of static files and the default file.</span></span> <span data-ttu-id="a52d7-189">Die Verzeichnissuche ist nicht aktiviert.</span><span class="sxs-lookup"><span data-stu-id="a52d7-189">Directory browsing isn't enabled.</span></span>

```csharp
app.UseFileServer();
```

<span data-ttu-id="a52d7-190">Der folgende Code baut auf der parameterlosen Überladung auf, indem die Verzeichnissuche aktiviert wird:</span><span class="sxs-lookup"><span data-stu-id="a52d7-190">The following code builds upon the parameterless overload by enabling directory browsing:</span></span>

```csharp
app.UseFileServer(enableDirectoryBrowsing: true);
```

<span data-ttu-id="a52d7-191">Betrachten Sie die folgende Verzeichnishierarchie:</span><span class="sxs-lookup"><span data-stu-id="a52d7-191">Consider the following directory hierarchy:</span></span>

* <span data-ttu-id="a52d7-192">**wwwroot**</span><span class="sxs-lookup"><span data-stu-id="a52d7-192">**wwwroot**</span></span>
  * <span data-ttu-id="a52d7-193">**css**</span><span class="sxs-lookup"><span data-stu-id="a52d7-193">**css**</span></span>
  * <span data-ttu-id="a52d7-194">**images**</span><span class="sxs-lookup"><span data-stu-id="a52d7-194">**images**</span></span>
  * <span data-ttu-id="a52d7-195">**js**</span><span class="sxs-lookup"><span data-stu-id="a52d7-195">**js**</span></span>
* <span data-ttu-id="a52d7-196">**MyStaticFiles**</span><span class="sxs-lookup"><span data-stu-id="a52d7-196">**MyStaticFiles**</span></span>
  * <span data-ttu-id="a52d7-197">**images**</span><span class="sxs-lookup"><span data-stu-id="a52d7-197">**images**</span></span>
    * <span data-ttu-id="a52d7-198">*banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="a52d7-198">*banner1.svg*</span></span>
  * <span data-ttu-id="a52d7-199">*default.html*</span><span class="sxs-lookup"><span data-stu-id="a52d7-199">*default.html*</span></span>

<span data-ttu-id="a52d7-200">Mit dem folgenden Code werden statische Dateien, Standarddateien und die Verzeichnissuche von `MyStaticFiles` aktiviert:</span><span class="sxs-lookup"><span data-stu-id="a52d7-200">The following code enables static files, default files, and directory browsing of `MyStaticFiles`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureMethod&highlight=5-11)]

<span data-ttu-id="a52d7-201">`AddDirectoryBrowser` muss aufgerufen werden, wenn der `EnableDirectoryBrowsing`-Eigenschaftswert `true` lautet:</span><span class="sxs-lookup"><span data-stu-id="a52d7-201">`AddDirectoryBrowser` must be called when the `EnableDirectoryBrowsing` property value is `true`:</span></span>

[!code-csharp[](static-files/samples/1x/StartupUseFileServer.cs?name=snippet_ConfigureServicesMethod)]

<span data-ttu-id="a52d7-202">Unter Verwendung der Dateihierarchie und des vorangehenden Codes werden URLs wie folgt aufgelöst:</span><span class="sxs-lookup"><span data-stu-id="a52d7-202">Using the file hierarchy and preceding code, URLs resolve as follows:</span></span>

| <span data-ttu-id="a52d7-203">URI</span><span class="sxs-lookup"><span data-stu-id="a52d7-203">URI</span></span>            |                             <span data-ttu-id="a52d7-204">Antwort</span><span class="sxs-lookup"><span data-stu-id="a52d7-204">Response</span></span>  |
| ------- | ------|
| <span data-ttu-id="a52d7-205">*http://\<server_address>/StaticFiles/images/banner1.svg*</span><span class="sxs-lookup"><span data-stu-id="a52d7-205">*http://\<server_address>/StaticFiles/images/banner1.svg*</span></span>    |      <span data-ttu-id="a52d7-206">MyStaticFiles/images/banner1.svg</span><span class="sxs-lookup"><span data-stu-id="a52d7-206">MyStaticFiles/images/banner1.svg</span></span> |
| <span data-ttu-id="a52d7-207">*http://\<server_address>/StaticFiles*</span><span class="sxs-lookup"><span data-stu-id="a52d7-207">*http://\<server_address>/StaticFiles*</span></span>             |     <span data-ttu-id="a52d7-208">MyStaticFiles/default.html</span><span class="sxs-lookup"><span data-stu-id="a52d7-208">MyStaticFiles/default.html</span></span> |

<span data-ttu-id="a52d7-209">Wenn im Verzeichnis *MyStaticFiles* keine Datei mit Standardnamen vorhanden ist, gibt *http://\<server_address>/StaticFiles* die Verzeichnisliste mit klickbaren Links zurück:</span><span class="sxs-lookup"><span data-stu-id="a52d7-209">If no default-named file exists in the *MyStaticFiles* directory, *http://\<server_address>/StaticFiles* returns the directory listing with clickable links:</span></span>

![Liste der statischen Dateien](static-files/_static/db2.png)

> [!NOTE]
> <span data-ttu-id="a52d7-211"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> und <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> führen eine clientseitige Umleitung von `http://{SERVER ADDRESS}/StaticFiles` (ohne nachstehenden Schrägstrich) zu `http://{SERVER ADDRESS}/StaticFiles/` (mit nachstehendem Schrägstrich) durch.</span><span class="sxs-lookup"><span data-stu-id="a52d7-211"><xref:Microsoft.AspNetCore.Builder.DefaultFilesExtensions.UseDefaultFiles*> and <xref:Microsoft.AspNetCore.Builder.DirectoryBrowserExtensions.UseDirectoryBrowser*> perform a client-side redirect from `http://{SERVER ADDRESS}/StaticFiles` (without a trailing slash) to `http://{SERVER ADDRESS}/StaticFiles/` (with a trailing slash).</span></span> <span data-ttu-id="a52d7-212">Relative URLs im Verzeichnis *StaticFiles* gelten ohne nachstehenden Schrägstrich als ungültig.</span><span class="sxs-lookup"><span data-stu-id="a52d7-212">Relative URLs within the *StaticFiles* directory are invalid without a trailing slash.</span></span>

## <a name="fileextensioncontenttypeprovider"></a><span data-ttu-id="a52d7-213">FileExtensionContentTypeProvider</span><span class="sxs-lookup"><span data-stu-id="a52d7-213">FileExtensionContentTypeProvider</span></span>

<span data-ttu-id="a52d7-214">Die Klasse [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) enthält eine `Mappings`-Eigenschaft, die als Zuordnung von Dateierweiterungen zu MIME-Inhaltstypen dient.</span><span class="sxs-lookup"><span data-stu-id="a52d7-214">The [FileExtensionContentTypeProvider](/dotnet/api/microsoft.aspnetcore.staticfiles.fileextensioncontenttypeprovider) class contains a `Mappings` property serving as a mapping of file extensions to MIME content types.</span></span> <span data-ttu-id="a52d7-215">Im folgenden Beispiel werden mehrere Dateierweiterungen bei bekannten MIME-Typen registriert.</span><span class="sxs-lookup"><span data-stu-id="a52d7-215">In the following sample, several file extensions are registered to known MIME types.</span></span> <span data-ttu-id="a52d7-216">Die Erweiterung *.rtf* wird ersetzt, und *.mp4* wird entfernt.</span><span class="sxs-lookup"><span data-stu-id="a52d7-216">The *.rtf* extension is replaced, and *.mp4* is removed.</span></span>

[!code-csharp[](static-files/samples/1x/StartupFileExtensionContentTypeProvider.cs?name=snippet_ConfigureMethod&highlight=3-12,19)]

<span data-ttu-id="a52d7-217">Weitere Informationen finden Sie unter [MIME-Inhaltstypen](http://www.iana.org/assignments/media-types/media-types.xhtml).</span><span class="sxs-lookup"><span data-stu-id="a52d7-217">See [MIME content types](http://www.iana.org/assignments/media-types/media-types.xhtml).</span></span>

## <a name="non-standard-content-types"></a><span data-ttu-id="a52d7-218">Inhaltstypen, die vom Standard abweichen</span><span class="sxs-lookup"><span data-stu-id="a52d7-218">Non-standard content types</span></span>

<span data-ttu-id="a52d7-219">Die Middleware für statische Dateien erkennt fast 400 bekannte Dateiinhaltstypen.</span><span class="sxs-lookup"><span data-stu-id="a52d7-219">Static File Middleware understands almost 400 known file content types.</span></span> <span data-ttu-id="a52d7-220">Wenn ein Benutzer eine Datei mit einem unbekannten Dateityp anfordert, übergibt die Middleware für statische Dateien die Anforderung an die nächste Middleware in der Pipeline.</span><span class="sxs-lookup"><span data-stu-id="a52d7-220">If the user requests a file with an unknown file type, Static File Middleware passes the request to the next middleware in the pipeline.</span></span> <span data-ttu-id="a52d7-221">Wenn keine Middleware die Anforderung verarbeitet, wird die Meldung *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a52d7-221">If no middleware handles the request, a *404 Not Found* response is returned.</span></span> <span data-ttu-id="a52d7-222">Wenn die Verzeichnissuche aktiviert ist, wird ein Link zur Datei in einer Verzeichnisliste angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a52d7-222">If directory browsing is enabled, a link to the file is displayed in a directory listing.</span></span>

<span data-ttu-id="a52d7-223">Mit dem folgenden Code wird die Bereitstellung unbekannter Typen aktiviert und die unbekannte Datei als Image gerendert:</span><span class="sxs-lookup"><span data-stu-id="a52d7-223">The following code enables serving unknown types and renders the unknown file as an image:</span></span>

[!code-csharp[](static-files/samples/1x/StartupServeUnknownFileTypes.cs?name=snippet_ConfigureMethod)]

<span data-ttu-id="a52d7-224">Mit dem vorangehenden Code wird eine Anforderung für eine Datei mit unbekanntem Inhaltstyp als Image zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="a52d7-224">With the preceding code, a request for a file with an unknown content type is returned as an image.</span></span>

> [!WARNING]
> <span data-ttu-id="a52d7-225">Die Aktivierung von [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) stellt ein Sicherheitsrisiko dar.</span><span class="sxs-lookup"><span data-stu-id="a52d7-225">Enabling [ServeUnknownFileTypes](/dotnet/api/microsoft.aspnetcore.builder.staticfileoptions.serveunknownfiletypes#Microsoft_AspNetCore_Builder_StaticFileOptions_ServeUnknownFileTypes) is a security risk.</span></span> <span data-ttu-id="a52d7-226">Diese Eigenschaft ist standardmäßig deaktiviert, von einer Verwendung wird abgeraten.</span><span class="sxs-lookup"><span data-stu-id="a52d7-226">It's disabled by default, and its use is discouraged.</span></span> <span data-ttu-id="a52d7-227">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) bietet eine sicherere Alternative für die Bereitstellung von Dateien mit vom Standard abweichenden Erweiterungen.</span><span class="sxs-lookup"><span data-stu-id="a52d7-227">[FileExtensionContentTypeProvider](#fileextensioncontenttypeprovider) provides a safer alternative to serving files with non-standard extensions.</span></span>

### <a name="considerations"></a><span data-ttu-id="a52d7-228">Weitere Überlegungen</span><span class="sxs-lookup"><span data-stu-id="a52d7-228">Considerations</span></span>

> [!WARNING]
> <span data-ttu-id="a52d7-229">`UseDirectoryBrowser` und `UseStaticFiles` können Geheimnisse aufdecken.</span><span class="sxs-lookup"><span data-stu-id="a52d7-229">`UseDirectoryBrowser` and `UseStaticFiles` can leak secrets.</span></span> <span data-ttu-id="a52d7-230">Eine Deaktivierung der Verzeichnissuche in der Produktionsumgebung wird dringend empfohlen.</span><span class="sxs-lookup"><span data-stu-id="a52d7-230">Disabling directory browsing in production is highly recommended.</span></span> <span data-ttu-id="a52d7-231">Überprüfen Sie sorgfältig, welche Verzeichnisse über `UseStaticFiles` oder `UseDirectoryBrowser` aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="a52d7-231">Carefully review which directories are enabled via `UseStaticFiles` or `UseDirectoryBrowser`.</span></span> <span data-ttu-id="a52d7-232">Das gesamte Verzeichnis und die zugehörigen Unterverzeichnisse werden öffentlich zugänglich gemacht.</span><span class="sxs-lookup"><span data-stu-id="a52d7-232">The entire directory and its sub-directories become publicly accessible.</span></span> <span data-ttu-id="a52d7-233">Speichern Sie Dateien, die für eine Bereitstellung in der Öffentlichkeit geeignet sind, in einem dafür vorgesehenen Verzeichnis wie z.B. *\<content_root>/wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="a52d7-233">Store files suitable for serving to the public in a dedicated directory, such as *\<content_root>/wwwroot*.</span></span> <span data-ttu-id="a52d7-234">Trennen Sie diese Dateien von MVC-Ansichten, Razor Pages (nur 2.x), Konfigurationsdateien usw.</span><span class="sxs-lookup"><span data-stu-id="a52d7-234">Separate these files from MVC views, Razor Pages (2.x only), configuration files, etc.</span></span>

* <span data-ttu-id="a52d7-235">Die URLs für Inhalte, die mit `UseDirectoryBrowser` und `UseStaticFiles` verfügbar gemacht wurden, unterliegen der Groß-/Kleinschreibung und den Zeichenbeschränkungen des zugrunde liegenden Dateisystems.</span><span class="sxs-lookup"><span data-stu-id="a52d7-235">The URLs for content exposed with `UseDirectoryBrowser` and `UseStaticFiles` are subject to the case sensitivity and character restrictions of the underlying file system.</span></span> <span data-ttu-id="a52d7-236">Bei Windows wird die Groß-/Kleinschreibung beispielsweise beachtet, bei macOS und Linux hingegen nicht.</span><span class="sxs-lookup"><span data-stu-id="a52d7-236">For example, Windows is case insensitive&mdash;macOS and Linux aren't.</span></span>

* <span data-ttu-id="a52d7-237">In IIS gehostete ASP.NET Core-Apps leiten über das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) alle Anforderungen an die App weiter, darunter die Anforderungen von statischen Dateien.</span><span class="sxs-lookup"><span data-stu-id="a52d7-237">ASP.NET Core apps hosted in IIS use the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to forward all requests to the app, including static file requests.</span></span> <span data-ttu-id="a52d7-238">Der statische IIS-Dateihandler wird nicht verwendet.</span><span class="sxs-lookup"><span data-stu-id="a52d7-238">The IIS static file handler isn't used.</span></span> <span data-ttu-id="a52d7-239">Er kann Anforderungen erst verarbeiten, nachdem sie vom Modul verarbeitet wurden.</span><span class="sxs-lookup"><span data-stu-id="a52d7-239">It has no chance to handle requests before they're handled by the module.</span></span>

* <span data-ttu-id="a52d7-240">Führen Sie im IIS-Manager die folgenden Schritte aus, um den statischen IIS-Dateihandler auf Server- oder Website-Ebene zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="a52d7-240">Complete the following steps in IIS Manager to remove the IIS static file handler at the server or website level:</span></span>
    1. <span data-ttu-id="a52d7-241">Navigieren Sie zum Feature **Module**.</span><span class="sxs-lookup"><span data-stu-id="a52d7-241">Navigate to the **Modules** feature.</span></span>
    1. <span data-ttu-id="a52d7-242">Wählen Sie **StaticFileModule** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="a52d7-242">Select **StaticFileModule** in the list.</span></span>
    1. <span data-ttu-id="a52d7-243">Klicken Sie in der Randleiste **Aktionen** auf **Entfernen**.</span><span class="sxs-lookup"><span data-stu-id="a52d7-243">Click **Remove** in the **Actions** sidebar.</span></span>

> [!WARNING]
> <span data-ttu-id="a52d7-244">Wenn der statische IIS-Dateihandler aktiviert ist **und** das ASP.NET Core-Modul falsch konfiguriert wurde, werden statische Dateien bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a52d7-244">If the IIS static file handler is enabled **and** the ASP.NET Core Module is configured incorrectly, static files are served.</span></span> <span data-ttu-id="a52d7-245">Dies geschieht beispielsweise, wenn die Datei *web.config* nicht bereitgestellt worden ist.</span><span class="sxs-lookup"><span data-stu-id="a52d7-245">This happens, for example, if the *web.config* file isn't deployed.</span></span>

* <span data-ttu-id="a52d7-246">Platzieren Sie Codedateien (einschließlich *.cs* und *.cshtml*) außerhalb des Webstammverzeichnisses des App-Projekts.</span><span class="sxs-lookup"><span data-stu-id="a52d7-246">Place code files (including *.cs* and *.cshtml*) outside of the app project's web root.</span></span> <span data-ttu-id="a52d7-247">Aus diesem Grund wird eine logische Trennung zwischen den clientseitigen Inhalten der App und dem serverbasierten Code erschaffen.</span><span class="sxs-lookup"><span data-stu-id="a52d7-247">A logical separation is therefore created between the app's client-side content and server-based code.</span></span> <span data-ttu-id="a52d7-248">Dadurch wird verhindert, dass serverseitiger Code aufgedeckt wird.</span><span class="sxs-lookup"><span data-stu-id="a52d7-248">This prevents server-side code from being leaked.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a52d7-249">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a52d7-249">Additional resources</span></span>

* [<span data-ttu-id="a52d7-250">Middleware</span><span class="sxs-lookup"><span data-stu-id="a52d7-250">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="a52d7-251">Einführung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a52d7-251">Introduction to ASP.NET Core</span></span>](xref:index)
