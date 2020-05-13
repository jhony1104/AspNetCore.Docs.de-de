---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: In diesem Artikel wird erklärt, wie Sie eine wiederverwendbare Razor-Benutzeroberfläche mit Teilansichten in einer Klassenbibliothek in ASP.NET Core erstellen.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/ui-class
ms.openlocfilehash: 2c2a2c1e13b2d511ecf8c1c02c235192861fd486
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774274"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="63331-103">Erstellen einer wiederverwendbaren Benutzeroberfläche mithilfe eines Razor-Klassenbibliotheksprojekts in ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="63331-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="63331-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="63331-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="63331-105">Razor-Ansichten, -Seiten, -Controller, -Seitenmodelle, [-Komponenten](xref:blazor/class-libraries), [-Ansichtskomponenten](xref:mvc/views/view-components) und -Datenmodelle können in einer Razor-Klassenbibliothek (RCL, Razor Class Library) zusammengefasst und erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="63331-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="63331-106">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="63331-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="63331-107">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="63331-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="63331-108">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="63331-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="63331-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63331-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="63331-110">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="63331-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="63331-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63331-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="63331-112">Klicken Sie in Visual Studio auf **Neues Projekt erstellen**.</span><span class="sxs-lookup"><span data-stu-id="63331-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="63331-113">Klicken Sie auf **Razor-Klassenbibliothek** > **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="63331-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="63331-114">Benennen Sie die Bibliothek (z. B. „RazorClassLib“), und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="63331-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="63331-115">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="63331-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="63331-116">Klicken Sie auf **Support pages and views** (Seiten und Ansichten unterstützen), wenn Ansichten unterstützt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="63331-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="63331-117">Standardmäßig werden nur Razor-Seiten unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63331-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="63331-118">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="63331-118">Select **Create**.</span></span>

<span data-ttu-id="63331-119">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="63331-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="63331-120">Mit der Option **Support pages and views** (Seiten und Ansichten unterstützen) werden Seiten und Ansichten unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63331-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="63331-121">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="63331-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="63331-122">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="63331-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="63331-123">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="63331-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="63331-124">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="63331-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="63331-125">Übergeben Sie die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`), damit Seiten und Ansichten unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="63331-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="63331-126">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="63331-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="63331-127">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="63331-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="63331-128">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="63331-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="63331-129">Die ASP.NET Core-Vorlagen gehen davon aus, dass die RCL-Inhalte sich im Ordner *Areas* befinden.</span><span class="sxs-lookup"><span data-stu-id="63331-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="63331-130">Im Abschnitt [RCL-Seitenlayout](#rcl-pages-layout) wird gezeigt, wie Sie eine RCL erstellen, die Inhalte aus `~/Pages` statt aus `~/Areas/Pages` verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="63331-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="63331-131">Verweisen auf RCL-Inhalte</span><span class="sxs-lookup"><span data-stu-id="63331-131">Reference RCL content</span></span>

<span data-ttu-id="63331-132">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="63331-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="63331-133">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="63331-133">NuGet package.</span></span> <span data-ttu-id="63331-134">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="63331-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="63331-135">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="63331-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="63331-136">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="63331-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="63331-137">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="63331-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="63331-138">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="63331-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="63331-139">Wenn Sie z. B. *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* zu WebApp1 hinzufügen, hat Page1 in WebApp1 Vorrang vor Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="63331-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="63331-140">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="63331-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="63331-141">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="63331-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="63331-142">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="63331-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="63331-143">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63331-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="63331-144">RCL-Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="63331-144">RCL Pages layout</span></span>

<span data-ttu-id="63331-145">Erstellen Sie ein RCL-Projekt mit der folgenden Dateistruktur, um auf RCL-Inhalte so zu verweisen, als seien diese im Ordner *Pages* der Web-App abgelegt:</span><span class="sxs-lookup"><span data-stu-id="63331-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="63331-146">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="63331-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="63331-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="63331-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="63331-148">Angenommen, *RazorUIClassLib/Pages/Shared* enthält zwei Teildateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="63331-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="63331-149">Die `<partial>`-Tags könnten dann in der Datei *_Layout.cshtml* hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="63331-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="63331-150">Erstellen einer RCL mit statischen Objekten</span><span class="sxs-lookup"><span data-stu-id="63331-150">Create an RCL with static assets</span></span>

<span data-ttu-id="63331-151">Eine RCL kann statische Begleitobjekte erfordern, auf die entweder in der RCL oder in der App, die die RCL verwendet, verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="63331-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="63331-152">ASP.NET Core ermöglicht das Erstellen von RCLs mit statischen Objekten, die verwendenden Apps zur Verfügung stehen.</span><span class="sxs-lookup"><span data-stu-id="63331-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="63331-153">Wenn Sie Begleitobjekte zu einer RCL hinzufügen möchten, müssen Sie einen Ordner namens *wwwroot* in der Klassenbibliothek erstellen und alle erforderlichen Dateien in diesem Ordner ablegen.</span><span class="sxs-lookup"><span data-stu-id="63331-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="63331-154">Beim Packen einer RCL werden alle Begleitobjekte im Ordner *wwwroot* automatisch in das Paket gepackt.</span><span class="sxs-lookup"><span data-stu-id="63331-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="63331-155">Ausschließen statischer Objekte</span><span class="sxs-lookup"><span data-stu-id="63331-155">Exclude static assets</span></span>

<span data-ttu-id="63331-156">Fügen Sie den gewünschten Ausschlusspfad zur Eigenschaftengruppe `$(DefaultItemExcludes)` in der Projektdatei hinzu, um statische Objekte auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="63331-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="63331-157">Trennen Sie Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="63331-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="63331-158">Im folgenden Beispiel wird das Stylesheet *lib.css* im Ordner *wwwroot* nicht als statisches Objekt angesehen und nicht in die veröffentlichte RCL aufgenommen:</span><span class="sxs-lookup"><span data-stu-id="63331-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="63331-159">TypeScript-Integration</span><span class="sxs-lookup"><span data-stu-id="63331-159">Typescript integration</span></span>

<span data-ttu-id="63331-160">Gehen Sie wie folgt vor, um TypeScript-Dateien in eine RCL aufzunehmen:</span><span class="sxs-lookup"><span data-stu-id="63331-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="63331-161">Legen Sie die TypeScript-Dateien ( *.ts*) außerhalb des Ordners *wwwroot* ab.</span><span class="sxs-lookup"><span data-stu-id="63331-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="63331-162">Legen Sie sie zum Beispiel im Ordner *Client* ab.</span><span class="sxs-lookup"><span data-stu-id="63331-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="63331-163">Konfigurieren Sie die TypeScript-Buildausgabe für den Ordner *wwwroot*.</span><span class="sxs-lookup"><span data-stu-id="63331-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="63331-164">Legen Sie die `TypescriptOutDir`-Eigenschaft in einer Eigenschaftengruppe (`PropertyGroup`) wie folgt in der Projektdatei fest:</span><span class="sxs-lookup"><span data-stu-id="63331-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="63331-165">Fügen Sie das TypeScript-Ziel als Abhängigkeit des Ziels `ResolveCurrentProjectStaticWebAssets` hinzu, indem Sie das folgende Ziel in einer Eigenschaftengruppe (`PropertyGroup`) in der Projektdatei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="63331-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="63331-166">Inhalte aus einer RCL, auf die verwiesen wird, verwenden</span><span class="sxs-lookup"><span data-stu-id="63331-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="63331-167">Die Dateien im Ordner *wwwroot* der RCL werden entweder für die RCL oder für die verwendende App unter dem Präfix `_content/{LIBRARY NAME}/` verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="63331-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="63331-168">Für eine Bibliothek mit dem Namen *Razor.Class.Lib* lautet der Pfad zu statischen Inhalten zum Beispiel `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="63331-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="63331-169">Wenn Sie ein NuGet-Paket erstellen und der Assemblyname nicht der Paket-ID entspricht, verwenden Sie die Paket-ID für `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="63331-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="63331-170">Die verwendende App verweist auf statische Objekte, die von der Bibliothek bereitgestellt werden, mit `<script>`, `<style>`, `<img>` und anderen HTML-Tags.</span><span class="sxs-lookup"><span data-stu-id="63331-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="63331-171">Bei der verwendenden App muss [static file support](xref:fundamentals/static-files) (Unterstützung von statischen Dateien) in `Startup.Configure` aktiviert sein:</span><span class="sxs-lookup"><span data-stu-id="63331-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="63331-172">Beim Ausführen der verwendenden App in der Buildausgabe (`dotnet run`) sind statische Webobjekte in der Entwicklungsumgebung standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="63331-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="63331-173">Wenn Sie möchten, dass beim Ausführen in der Buildausgabe Objekte in anderen Umgebungen unterstützt werden, rufen Sie im Host-Generator in *Program.cs* `UseStaticWebAssets` auf:</span><span class="sxs-lookup"><span data-stu-id="63331-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

<span data-ttu-id="63331-174">`UseStaticWebAssets` muss nicht aufgerufen werden, wenn eine App in der veröffentlichten Ausgabe ausgeführt wird (`dotnet publish`).</span><span class="sxs-lookup"><span data-stu-id="63331-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="63331-175">Entwicklungsablauf bei mehreren Projekten</span><span class="sxs-lookup"><span data-stu-id="63331-175">Multi-project development flow</span></span>

<span data-ttu-id="63331-176">Wenn die verwendende App ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="63331-176">When the consuming app runs:</span></span>

* <span data-ttu-id="63331-177">bleiben die Objekte in der RCL in ihren ursprünglichen Ordnern.</span><span class="sxs-lookup"><span data-stu-id="63331-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="63331-178">werden die Objekte nicht in die verwendende App verschoben.</span><span class="sxs-lookup"><span data-stu-id="63331-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="63331-179">Änderungen im Ordner *wwwroot* der RCL werden in der verwendenden App abgebildet, sobald die RCL neu erstellt wurde, ohne dass die verwendende App neu erstellt werden muss.</span><span class="sxs-lookup"><span data-stu-id="63331-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="63331-180">Wenn die RCL erstellt wird, wird ein Manifest erstellt, in dem die Speicherorte der statischen Webobjekte stehen.</span><span class="sxs-lookup"><span data-stu-id="63331-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="63331-181">Die verwendende App liest das Manifest zur Laufzeit, um die Objekte aus den Projekten und Paketen, auf die verwiesen wird, zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="63331-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="63331-182">Wenn ein neues Objekt zu einer RCL hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, damit verwendende Apps auf das neue Objekt zugreifen können.</span><span class="sxs-lookup"><span data-stu-id="63331-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="63331-183">Veröffentlichen</span><span class="sxs-lookup"><span data-stu-id="63331-183">Publish</span></span>

<span data-ttu-id="63331-184">Wenn die App veröffentlicht wird, werden die Begleitobjekte aus allen Projekten und Paketen, auf die verwiesen wird, in den Ordner *wwwroot* der veröffentlichten App unter `_content/{LIBRARY NAME}/` kopiert.</span><span class="sxs-lookup"><span data-stu-id="63331-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="63331-185">Razor-Ansichten, -Seiten, -Controller, -Seitenmodelle, [-Komponenten](xref:blazor/class-libraries), [-Ansichtskomponenten](xref:mvc/views/view-components) und -Datenmodelle können in einer Razor-Klassenbibliothek (RCL, Razor Class Library) zusammengefasst und erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="63331-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="63331-186">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="63331-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="63331-187">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="63331-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="63331-188">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="63331-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="63331-189">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="63331-189">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="63331-190">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="63331-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="63331-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63331-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="63331-192">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="63331-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="63331-193">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="63331-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="63331-194">Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="63331-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="63331-195">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="63331-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="63331-196">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="63331-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="63331-197">Klicken Sie auf **Razor-Klassenbibliothek** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="63331-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="63331-198">Eine RCL verfügt über die folgende Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="63331-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-cli"></a>[<span data-ttu-id="63331-199">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="63331-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="63331-200">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="63331-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="63331-201">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="63331-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="63331-202">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="63331-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="63331-203">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="63331-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="63331-204">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="63331-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="63331-205">Die ASP.NET Core-Vorlagen gehen davon aus, dass die RCL-Inhalte sich im Ordner *Areas* befinden.</span><span class="sxs-lookup"><span data-stu-id="63331-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="63331-206">Im Abschnitt [RCL-Seitenlayout](#rcl-pages-layout) wird gezeigt, wie Sie eine RCL erstellen, die Inhalte aus `~/Pages` statt aus `~/Areas/Pages` verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="63331-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="63331-207">Verweisen auf RCL-Inhalte</span><span class="sxs-lookup"><span data-stu-id="63331-207">Reference RCL content</span></span>

<span data-ttu-id="63331-208">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="63331-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="63331-209">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="63331-209">NuGet package.</span></span> <span data-ttu-id="63331-210">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="63331-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="63331-211">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="63331-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="63331-212">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="63331-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="63331-213">Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und dessen Verwendung in einem Razor Pages-Projekt</span><span class="sxs-lookup"><span data-stu-id="63331-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="63331-214">Sie können das [vollständige Projekt](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="63331-214">You can download the [complete project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="63331-215">Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern.</span><span class="sxs-lookup"><span data-stu-id="63331-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="63331-216">[In diesem GitHub-Issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.</span><span class="sxs-lookup"><span data-stu-id="63331-216">You can leave feedback in [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="63331-217">Testen der heruntergeladenen App</span><span class="sxs-lookup"><span data-stu-id="63331-217">Test the download app</span></span>

<span data-ttu-id="63331-218">Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.</span><span class="sxs-lookup"><span data-stu-id="63331-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="63331-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63331-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="63331-220">Öffnen Sie die *SLN*-Datei in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="63331-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="63331-221">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="63331-221">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="63331-222">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="63331-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="63331-223">Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.</span><span class="sxs-lookup"><span data-stu-id="63331-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="63331-224">Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="63331-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="63331-225">Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test-webapp1).</span><span class="sxs-lookup"><span data-stu-id="63331-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="63331-226">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="63331-226">Create an RCL</span></span>

<span data-ttu-id="63331-227">In diesem Abschnitt wird eine RCL erstellt.</span><span class="sxs-lookup"><span data-stu-id="63331-227">In this section, an RCL is created.</span></span> <span data-ttu-id="63331-228">Dabei werden der RCL Razor-Dateien hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63331-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="63331-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63331-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="63331-230">Erstellen Sie das RCL-Projekt:</span><span class="sxs-lookup"><span data-stu-id="63331-230">Create the RCL project:</span></span>

* <span data-ttu-id="63331-231">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="63331-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="63331-232">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="63331-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="63331-233">Legen Sie als Namen für die App **RazorUIClassLib** fest, und klicken Sie auf >**OK**.</span><span class="sxs-lookup"><span data-stu-id="63331-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="63331-234">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="63331-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="63331-235">Klicken Sie auf **Razor-Klassenbibliothek** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="63331-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="63331-236">Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.</span><span class="sxs-lookup"><span data-stu-id="63331-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="63331-237">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="63331-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="63331-238">Führen Sie die folgenden Befehle über die Befehlszeile aus:</span><span class="sxs-lookup"><span data-stu-id="63331-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="63331-239">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="63331-239">The preceding commands:</span></span>

* <span data-ttu-id="63331-240">Die RCL `RazorUIClassLib` wird erstellt.</span><span class="sxs-lookup"><span data-stu-id="63331-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="63331-241">Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63331-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="63331-242">Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.</span><span class="sxs-lookup"><span data-stu-id="63331-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="63331-243">Die Datei [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) wird erstellt und der RCL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63331-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="63331-244">Die Datei *_ViewStart.cshtml* ist erforderlich, um das Layout des Razor Pages-Projekts (das im nächsten Abschnitt hinzugefügt wird) verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="63331-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="63331-245">Hinzufügen von Razor-Dateien und -Ordnern zum Projekt</span><span class="sxs-lookup"><span data-stu-id="63331-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="63331-246">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="63331-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="63331-247">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="63331-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="63331-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="63331-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="63331-249">Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="63331-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="63331-250">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="63331-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="63331-251">Weitere Informationen zu *_ViewImports.cshtml* finden Sie unter [Importieren gemeinsam verwendeter Direktiven](xref:mvc/views/layout#importing-shared-directives).</span><span class="sxs-lookup"><span data-stu-id="63331-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="63331-252">Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:</span><span class="sxs-lookup"><span data-stu-id="63331-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="63331-253">Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="63331-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="63331-254">*RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="63331-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="63331-255">Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt</span><span class="sxs-lookup"><span data-stu-id="63331-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="63331-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63331-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="63331-257">Erstellen Sie die Web-App mit Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="63331-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="63331-258">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe und anschließend auf **Hinzufügen** > **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="63331-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="63331-259">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="63331-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="63331-260">Legen Sie als Namen für die App **WebApp1** fest.</span><span class="sxs-lookup"><span data-stu-id="63331-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="63331-261">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="63331-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="63331-262">Klicken Sie auf **Webanwendung** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="63331-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="63331-263">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.</span><span class="sxs-lookup"><span data-stu-id="63331-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="63331-264">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Buildabhängigkeiten** > **Projektabhängigkeiten**.</span><span class="sxs-lookup"><span data-stu-id="63331-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="63331-265">Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.</span><span class="sxs-lookup"><span data-stu-id="63331-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="63331-266">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Hinzufügen** > **Verweis**.</span><span class="sxs-lookup"><span data-stu-id="63331-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="63331-267">Aktivieren Sie im Dialogfeld **Verweis-Manager** das Kontrollkästchen neben **RazorUIClassLib**, und klicken Sie anschließend auf >**OK**.</span><span class="sxs-lookup"><span data-stu-id="63331-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="63331-268">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="63331-268">Run the app.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="63331-269">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="63331-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="63331-270">Erstellen Sie eine Razor Pages-Web-App und eine Projektmappendatei, die die Razor Pages-App und die RCL enthält:</span><span class="sxs-lookup"><span data-stu-id="63331-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="63331-271">Erstellen Sie die Web-App, und führen Sie sie aus:</span><span class="sxs-lookup"><span data-stu-id="63331-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="63331-272">Testen des WebApp1-Projekts</span><span class="sxs-lookup"><span data-stu-id="63331-272">Test WebApp1</span></span>

<span data-ttu-id="63331-273">Wechseln Sie zu `/MyFeature/Page1`, um zu überprüfen, ob die Klassenbibliothek der Razor-Benutzeroberfläche verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63331-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="63331-274">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="63331-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="63331-275">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="63331-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="63331-276">Wenn Sie z. B. *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* zu WebApp1 hinzufügen, hat Page1 in WebApp1 Vorrang vor Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="63331-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="63331-277">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="63331-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="63331-278">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="63331-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="63331-279">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="63331-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="63331-280">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63331-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="63331-281">RCL-Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="63331-281">RCL Pages layout</span></span>

<span data-ttu-id="63331-282">Erstellen Sie ein RCL-Projekt mit der folgenden Dateistruktur, um auf RCL-Inhalte so zu verweisen, als seien diese im Ordner *Pages* der Web-App abgelegt:</span><span class="sxs-lookup"><span data-stu-id="63331-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="63331-283">*RazorUIClassLib/Pages*</span><span class="sxs-lookup"><span data-stu-id="63331-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="63331-284">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="63331-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="63331-285">Angenommen, *RazorUIClassLib/Pages/Shared* enthält zwei Teildateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="63331-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="63331-286">Die `<partial>`-Tags könnten dann in der Datei *_Layout.cshtml* hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="63331-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="63331-287">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="63331-287">Additional resources</span></span>

* <xref:blazor/class-libraries>
