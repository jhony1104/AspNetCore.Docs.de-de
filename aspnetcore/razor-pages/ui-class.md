---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: Es wird erläutert, wie wiederverwendbare Teilansichten in einer Klassenbibliothek in ASP.NET Core mit Razor-Benutzeroberfläche zu erstellen.
ms.author: riande
ms.date: 01/25/2020
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 420cc54701394673e2b442b1fdf999e421820fd5
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809119"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="bd523-103">Erstellen wiederverwendbarer Benutzeroberflächen mithilfe des Razor-Klassen Bibliotheks Projekts in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="bd523-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="bd523-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bd523-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bd523-105">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="bd523-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="bd523-106">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bd523-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="bd523-107">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="bd523-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="bd523-108">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="bd523-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="bd523-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd523-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="bd523-110">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="bd523-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd523-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd523-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd523-112">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="bd523-113">Wählen **Sie >** **Razor-Klassenbibliothek** aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="bd523-114">Benennen Sie die Bibliothek (z. b. "razorclasslib"), > **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="bd523-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="bd523-115">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="bd523-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="bd523-116">Wählen Sie **Supportseiten und Sichten** aus, wenn Sie Ansichten unterstützen müssen.</span><span class="sxs-lookup"><span data-stu-id="bd523-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="bd523-117">Standardmäßig werden nur Razor Pages unterstützt.</span><span class="sxs-lookup"><span data-stu-id="bd523-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="bd523-118">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-118">Select **Create**.</span></span>

<span data-ttu-id="bd523-119">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="bd523-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="bd523-120">Die Option **Support Pages und Views** unterstützt Seiten und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="bd523-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd523-121">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bd523-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bd523-122">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="bd523-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd523-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="bd523-124">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="bd523-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="bd523-125">Übergeben Sie die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`), um Unterstützung für Seiten und Ansichten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="bd523-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="bd523-126">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="bd523-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="bd523-127">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="bd523-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="bd523-128">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd523-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="bd523-129">ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner.</span><span class="sxs-lookup"><span data-stu-id="bd523-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="bd523-130">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="bd523-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="bd523-131">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="bd523-131">Reference RCL content</span></span>

<span data-ttu-id="bd523-132">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="bd523-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="bd523-133">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="bd523-133">NuGet package.</span></span> <span data-ttu-id="bd523-134">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="bd523-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="bd523-135">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="bd523-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="bd523-136">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="bd523-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="bd523-137">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="bd523-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="bd523-138">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="bd523-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="bd523-139">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="bd523-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="bd523-140">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="bd523-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="bd523-141">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bd523-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="bd523-142">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="bd523-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="bd523-143">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bd523-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="bd523-144">RCL Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="bd523-144">RCL Pages layout</span></span>

<span data-ttu-id="bd523-145">Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="bd523-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="bd523-146">*RazorUIClassLib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="bd523-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="bd523-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="bd523-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="bd523-148">Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bd523-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="bd523-149">Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="bd523-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="bd523-150">Erstellen einer RCL mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="bd523-150">Create an RCL with static assets</span></span>

<span data-ttu-id="bd523-151">Eine RCL erfordert möglicherweise begleitende statische Ressourcen, auf die entweder von der RCL oder der Verb raubenden App der RCL verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="bd523-151">An RCL may require companion static assets that can be referenced by either the RCL or the consuming app of the RCL.</span></span> <span data-ttu-id="bd523-152">ASP.net Core ermöglicht das Erstellen von rcls, die statische Ressourcen enthalten, die für eine verarbeitende app verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="bd523-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="bd523-153">Wenn Sie begleitende Assets als Teil einer RCL einschließen möchten, erstellen Sie in der Klassenbibliothek einen Ordner *wwwroot* , und fügen Sie alle erforderlichen Dateien in diesen Ordner ein.</span><span class="sxs-lookup"><span data-stu-id="bd523-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="bd523-154">Beim Packen einer RCL werden alle Begleit Objekte im Ordner " *wwwroot* " automatisch in das Paket eingefügt.</span><span class="sxs-lookup"><span data-stu-id="bd523-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="bd523-155">Statische Assets ausschließen</span><span class="sxs-lookup"><span data-stu-id="bd523-155">Exclude static assets</span></span>

<span data-ttu-id="bd523-156">Fügen Sie den gewünschten Ausschluss Pfad zur `$(DefaultItemExcludes)` Eigenschaften Gruppe in der Projektdatei hinzu, um statische Assets auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="bd523-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="bd523-157">Trennen Sie Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="bd523-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="bd523-158">Im folgenden Beispiel wird das *lib. CSS* -Stylesheet im Ordner " *wwwroot* " nicht als statisches Asset angesehen und ist nicht in der veröffentlichten RCL enthalten:</span><span class="sxs-lookup"><span data-stu-id="bd523-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="bd523-159">Typescript-Integration</span><span class="sxs-lookup"><span data-stu-id="bd523-159">Typescript integration</span></span>

<span data-ttu-id="bd523-160">So fügen Sie typescript-Dateien in eine RCL ein:</span><span class="sxs-lookup"><span data-stu-id="bd523-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="bd523-161">Platzieren Sie die typescript-Dateien (*TS*) außerhalb des Ordners *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="bd523-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="bd523-162">Platzieren Sie die Dateien z. b. in einem *Client* Ordner.</span><span class="sxs-lookup"><span data-stu-id="bd523-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="bd523-163">Konfigurieren Sie die typescript-Buildausgabe für den Ordner " *wwwroot* ".</span><span class="sxs-lookup"><span data-stu-id="bd523-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="bd523-164">Legen Sie die `TypescriptOutDir`-Eigenschaft innerhalb einer `PropertyGroup` in der Projektdatei fest:</span><span class="sxs-lookup"><span data-stu-id="bd523-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="bd523-165">Fügen Sie das typescript-Ziel als Abhängigkeit des `ResolveCurrentProjectStaticWebAssets` Ziels ein, indem Sie das folgende Ziel in einer `PropertyGroup` in der Projektdatei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="bd523-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="bd523-166">Nutzen von Inhalten aus einer referenzierten RCL</span><span class="sxs-lookup"><span data-stu-id="bd523-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="bd523-167">Die Dateien, die im Ordner " *wwwroot* " der RCL enthalten sind, werden entweder für die RCL oder die verbrauchende app unter dem Präfix `_content/{LIBRARY NAME}/`verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="bd523-167">The files included in the *wwwroot* folder of the RCL are exposed to either the RCL or the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="bd523-168">Eine Bibliothek mit dem Namen *Razor. Class. lib* führt z. b. zu einem Pfad zu statischem Inhalt `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="bd523-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="bd523-169">Wenn Sie ein nuget-Paket erstellen und der Assemblyname nicht mit der Paket-ID übereinstimmt, verwenden Sie die Paket-ID für `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="bd523-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="bd523-170">Die verarbeitende App verweist auf statische Ressourcen, die von der Bibliothek mit `<script>`, `<style>`, `<img>`und anderen HTML-Tags bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="bd523-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="bd523-171">Für die verarbeitende app muss die [Unterstützung statischer Dateien](xref:fundamentals/static-files) in `Startup.Configure`aktiviert sein:</span><span class="sxs-lookup"><span data-stu-id="bd523-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="bd523-172">Wenn Sie die Verb raubende App aus der Buildausgabe (`dotnet run`) ausführen, werden statische Webressourcen standardmäßig in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="bd523-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="bd523-173">Um Assets in anderen Umgebungen bei Ausführung über die Buildausgabe zu unterstützen, wenden Sie `UseStaticWebAssets` auf dem Host-Generator in *Program.cs*an:</span><span class="sxs-lookup"><span data-stu-id="bd523-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="bd523-174">Das Aufrufen von `UseStaticWebAssets` ist nicht erforderlich, wenn eine APP aus der veröffentlichten Ausgabe (`dotnet publish`) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="bd523-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="bd523-175">Entwicklungsfluss für mehrere Projekte</span><span class="sxs-lookup"><span data-stu-id="bd523-175">Multi-project development flow</span></span>

<span data-ttu-id="bd523-176">Wenn die verarbeitende app ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="bd523-176">When the consuming app runs:</span></span>

* <span data-ttu-id="bd523-177">Die Assets in der RCL bleiben in ihren ursprünglichen Ordnern.</span><span class="sxs-lookup"><span data-stu-id="bd523-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="bd523-178">Die Assets werden nicht in die Verb raubende App verschoben.</span><span class="sxs-lookup"><span data-stu-id="bd523-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="bd523-179">Jede Änderung im Ordner " *wwwroot* " der RCL wird in der nutzenden App widergespiegelt, nachdem die RCL neu erstellt wurde und ohne die Verb raubende APP neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="bd523-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="bd523-180">Wenn die RCL erstellt wurde, wird ein Manifest erstellt, in dem die statischen webasset-Speicherorte beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="bd523-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="bd523-181">Die Verb raubende App liest das Manifest zur Laufzeit, um die Assets aus referenzierten Projekten und Paketen zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="bd523-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="bd523-182">Wenn einer RCL ein neues Asset hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, bevor eine verarbeitende App auf das neue Medienobjekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="bd523-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="bd523-183">Veröffentlichungs</span><span class="sxs-lookup"><span data-stu-id="bd523-183">Publish</span></span>

<span data-ttu-id="bd523-184">Wenn die App veröffentlicht wird, werden die begleitenden Objekte aus allen referenzierten Projekten und Paketen in den Ordner *wwwroot* der veröffentlichten app unter `_content/{LIBRARY NAME}/`kopiert.</span><span class="sxs-lookup"><span data-stu-id="bd523-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bd523-185">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="bd523-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="bd523-186">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="bd523-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="bd523-187">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="bd523-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="bd523-188">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="bd523-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="bd523-189">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="bd523-189">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="bd523-190">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="bd523-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd523-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd523-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="bd523-192">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="bd523-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bd523-193">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bd523-194">Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="bd523-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="bd523-195">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="bd523-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="bd523-196">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="bd523-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="bd523-197">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="bd523-198">Eine RCL hat die folgende Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="bd523-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd523-199">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bd523-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bd523-200">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="bd523-201">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd523-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="bd523-202">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="bd523-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="bd523-203">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="bd523-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="bd523-204">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd523-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="bd523-205">ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner.</span><span class="sxs-lookup"><span data-stu-id="bd523-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="bd523-206">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="bd523-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="bd523-207">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="bd523-207">Reference RCL content</span></span>

<span data-ttu-id="bd523-208">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="bd523-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="bd523-209">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="bd523-209">NuGet package.</span></span> <span data-ttu-id="bd523-210">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="bd523-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="bd523-211">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="bd523-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="bd523-212">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="bd523-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="bd523-213">Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und verwenden aus einem Razor Pages Projekt</span><span class="sxs-lookup"><span data-stu-id="bd523-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="bd523-214">Sie können das [vollständige Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="bd523-214">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="bd523-215">Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern.</span><span class="sxs-lookup"><span data-stu-id="bd523-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="bd523-216">[In diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.</span><span class="sxs-lookup"><span data-stu-id="bd523-216">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="bd523-217">Testen der heruntergeladenen App</span><span class="sxs-lookup"><span data-stu-id="bd523-217">Test the download app</span></span>

<span data-ttu-id="bd523-218">Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.</span><span class="sxs-lookup"><span data-stu-id="bd523-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd523-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd523-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd523-220">Öffnen Sie die *SLN*-Datei in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="bd523-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="bd523-221">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-221">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd523-222">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bd523-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bd523-223">Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.</span><span class="sxs-lookup"><span data-stu-id="bd523-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="bd523-224">Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="bd523-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="bd523-225">Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test-webapp1).</span><span class="sxs-lookup"><span data-stu-id="bd523-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="bd523-226">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="bd523-226">Create an RCL</span></span>

<span data-ttu-id="bd523-227">In diesem Abschnitt wird eine RCL erstellt.</span><span class="sxs-lookup"><span data-stu-id="bd523-227">In this section, an RCL is created.</span></span> <span data-ttu-id="bd523-228">Dabei werden der RCL Razor-Dateien hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bd523-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd523-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd523-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd523-230">Erstellen Sie das RCL-Projekt:</span><span class="sxs-lookup"><span data-stu-id="bd523-230">Create the RCL project:</span></span>

* <span data-ttu-id="bd523-231">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="bd523-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="bd523-232">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bd523-233">Nennen Sie die APP **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bd523-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="bd523-234">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="bd523-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="bd523-235">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="bd523-236">Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd523-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd523-237">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bd523-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bd523-238">Führen Sie die folgenden Befehle über die Befehlszeile aus:</span><span class="sxs-lookup"><span data-stu-id="bd523-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="bd523-239">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="bd523-239">The preceding commands:</span></span>

* <span data-ttu-id="bd523-240">Erstellt den `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="bd523-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="bd523-241">Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="bd523-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="bd523-242">Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.</span><span class="sxs-lookup"><span data-stu-id="bd523-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="bd523-243">Erstellt eine [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) Datei und fügt es der RCL hinzu.</span><span class="sxs-lookup"><span data-stu-id="bd523-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="bd523-244">Die *_ViewStart.cshtml* Datei ist erforderlich, um das Layout des Projekts mit Razor Pages verwenden (die im nächsten Abschnitt hinzugefügt wird).</span><span class="sxs-lookup"><span data-stu-id="bd523-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="bd523-245">Razor-Dateien und Ordner zum Projekt hinzufügen</span><span class="sxs-lookup"><span data-stu-id="bd523-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="bd523-246">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bd523-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="bd523-247">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="bd523-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="bd523-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="bd523-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="bd523-249">Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="bd523-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="bd523-250">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="bd523-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="bd523-251">Weitere Informationen zu *_ViewImports.cshtml*, finden Sie unter [freigegebenen Richtlinien importieren](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="bd523-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="bd523-252">Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:</span><span class="sxs-lookup"><span data-stu-id="bd523-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="bd523-253">Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="bd523-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="bd523-254">*RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="bd523-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="bd523-255">Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt</span><span class="sxs-lookup"><span data-stu-id="bd523-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="bd523-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="bd523-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="bd523-257">Erstellen Sie die Web-App mit Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="bd523-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="bd523-258">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf die Projekt Mappe, > >**Neues Projekt** **hinzuzufügen** .</span><span class="sxs-lookup"><span data-stu-id="bd523-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="bd523-259">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="bd523-260">Legen Sie als Namen für die App **WebApp1** fest.</span><span class="sxs-lookup"><span data-stu-id="bd523-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="bd523-261">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="bd523-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="bd523-262">Wählen Sie **Webanwendung** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="bd523-263">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.</span><span class="sxs-lookup"><span data-stu-id="bd523-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="bd523-264">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie Abhängigkeiten > **Projekt Abhängigkeiten** **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="bd523-265">Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.</span><span class="sxs-lookup"><span data-stu-id="bd523-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="bd523-266">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie > **Verweis** **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="bd523-267">Aktivieren Sie im Dialogfeld **Verweis-Manager** die Option **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="bd523-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="bd523-268">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="bd523-268">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="bd523-269">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="bd523-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="bd523-270">Erstellen Sie eine Razor Pages Web-App und eine Projektmappendatei, die die Razor Pages-APP und die RCL enthält:</span><span class="sxs-lookup"><span data-stu-id="bd523-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="bd523-271">Erstellen Sie die Web-App, und führen Sie sie aus:</span><span class="sxs-lookup"><span data-stu-id="bd523-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="bd523-272">Testen des WebApp1-Projekts</span><span class="sxs-lookup"><span data-stu-id="bd523-272">Test WebApp1</span></span>

<span data-ttu-id="bd523-273">Navigieren Sie zu `/MyFeature/Page1`, um zu überprüfen, ob die Razor-UI-Klassenbibliothek verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bd523-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="bd523-274">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="bd523-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="bd523-275">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="bd523-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="bd523-276">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="bd523-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="bd523-277">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="bd523-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="bd523-278">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bd523-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="bd523-279">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="bd523-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="bd523-280">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="bd523-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="bd523-281">RCL Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="bd523-281">RCL Pages layout</span></span>

<span data-ttu-id="bd523-282">Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="bd523-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="bd523-283">*RazorUIClassLib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="bd523-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="bd523-284">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="bd523-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="bd523-285">Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="bd523-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="bd523-286">Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="bd523-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
