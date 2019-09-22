---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: Es wird erläutert, wie wiederverwendbare Teilansichten in einer Klassenbibliothek in ASP.NET Core mit Razor-Benutzeroberfläche zu erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 09/21/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 1b544e208be049f02d01e35daa6eb3bfba94265a
ms.sourcegitcommit: 04ce94b3c1b01d167f30eed60c1c95446dfe759d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/21/2019
ms.locfileid: "71176476"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="eaa21-103">Erstellen wiederverwendbarer Benutzeroberflächen mithilfe des Razor-Klassen Bibliotheks Projekts in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="eaa21-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="eaa21-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="eaa21-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="eaa21-105">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="eaa21-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="eaa21-106">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eaa21-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="eaa21-107">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="eaa21-108">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="eaa21-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="eaa21-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa21-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="eaa21-110">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="eaa21-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eaa21-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa21-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eaa21-112">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-112">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="eaa21-113">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-113">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="eaa21-114">Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="eaa21-114">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="eaa21-115">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="eaa21-116">Vergewissern Sie sich, dass **ASP.net Core 3,0** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="eaa21-116">Verify **ASP.NET Core 3.0** or later is selected.</span></span>
* <span data-ttu-id="eaa21-117">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-117">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="eaa21-118">Die Vorlage für die Razor-Klassenbibliothek (Razor Class Library, RCL) ist standardmäßig standardmäßig auf die Entwicklung</span><span class="sxs-lookup"><span data-stu-id="eaa21-118">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="eaa21-119">Eine Vorlagen Option in Visual Studio bietet Vorlagen Unterstützung für Seiten und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="eaa21-119">A template option in Visual Studio provides template support for pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eaa21-120">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="eaa21-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eaa21-121">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-121">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="eaa21-122">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eaa21-122">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="eaa21-123">Die Vorlage für die Razor-Klassenbibliothek (Razor Class Library, RCL) ist standardmäßig standardmäßig auf die Entwicklung</span><span class="sxs-lookup"><span data-stu-id="eaa21-123">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="eaa21-124">Übergeben Sie `-support-pages-and-views` die Option`dotnet new razorclasslib -support-pages-and-views`(), um Unterstützung für Seiten und Ansichten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-124">Pass the `-support-pages-and-views` option (`dotnet new razorclasslib -support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="eaa21-125">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="eaa21-125">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="eaa21-126">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-126">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="eaa21-127">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="eaa21-127">Add Razor files to the RCL.</span></span>

<span data-ttu-id="eaa21-128">ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner.</span><span class="sxs-lookup"><span data-stu-id="eaa21-128">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="eaa21-129">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL `~/Pages` , die `~/Areas/Pages`Inhalte in anstelle von verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="eaa21-129">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="eaa21-130">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="eaa21-130">Reference RCL content</span></span>

<span data-ttu-id="eaa21-131">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="eaa21-131">The RCL can be referenced by:</span></span>

* <span data-ttu-id="eaa21-132">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="eaa21-132">NuGet package.</span></span> <span data-ttu-id="eaa21-133">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="eaa21-133">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="eaa21-134">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="eaa21-134">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="eaa21-135">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="eaa21-135">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="eaa21-136">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="eaa21-136">Override views, partial views, and pages</span></span>

<span data-ttu-id="eaa21-137">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="eaa21-137">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="eaa21-138">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="eaa21-138">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="eaa21-139">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-139">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="eaa21-140">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaa21-140">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="eaa21-141">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="eaa21-141">Update the markup to indicate the new location.</span></span> <span data-ttu-id="eaa21-142">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="eaa21-142">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="eaa21-143">RCL Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="eaa21-143">RCL Pages layout</span></span>

<span data-ttu-id="eaa21-144">Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="eaa21-144">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="eaa21-145">*RazorUIClassLib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="eaa21-145">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="eaa21-146">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="eaa21-146">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="eaa21-147">Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaa21-147">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="eaa21-148">Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="eaa21-148">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="eaa21-149">Erstellen einer RCL mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="eaa21-149">Create an RCL with static assets</span></span>

<span data-ttu-id="eaa21-150">Eine RCL erfordert möglicherweise begleitende statische Ressourcen, auf die von der Verb raubenden App der RCL verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="eaa21-150">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="eaa21-151">ASP.net Core ermöglicht das Erstellen von rcls, die statische Ressourcen enthalten, die für eine verarbeitende app verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="eaa21-151">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="eaa21-152">Wenn Sie begleitende Assets als Teil einer RCL einschließen möchten, erstellen Sie in der Klassenbibliothek einen Ordner *wwwroot* , und fügen Sie alle erforderlichen Dateien in diesen Ordner ein.</span><span class="sxs-lookup"><span data-stu-id="eaa21-152">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="eaa21-153">Beim Packen einer RCL werden alle Begleit Objekte im Ordner " *wwwroot* " automatisch in das Paket eingefügt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-153">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="eaa21-154">Statische Assets ausschließen</span><span class="sxs-lookup"><span data-stu-id="eaa21-154">Exclude static assets</span></span>

<span data-ttu-id="eaa21-155">Fügen Sie der `$(DefaultItemExcludes)` Eigenschaften Gruppe in der Projektdatei den gewünschten Ausschluss Pfad hinzu, um statische Assets auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-155">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="eaa21-156">Trennen Sie Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="eaa21-156">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="eaa21-157">Im folgenden Beispiel wird das *lib. CSS* -Stylesheet im Ordner " *wwwroot* " nicht als statisches Asset angesehen und ist nicht in der veröffentlichten RCL enthalten:</span><span class="sxs-lookup"><span data-stu-id="eaa21-157">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="eaa21-158">Typescript-Integration</span><span class="sxs-lookup"><span data-stu-id="eaa21-158">Typescript integration</span></span>

<span data-ttu-id="eaa21-159">So fügen Sie typescript-Dateien in eine RCL ein:</span><span class="sxs-lookup"><span data-stu-id="eaa21-159">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="eaa21-160">Platzieren Sie die typescript-Dateien (*TS*) außerhalb des Ordners *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="eaa21-160">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="eaa21-161">Platzieren Sie die Dateien z. b. in einem *Client* Ordner.</span><span class="sxs-lookup"><span data-stu-id="eaa21-161">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="eaa21-162">Konfigurieren Sie die typescript-Buildausgabe für den Ordner " *wwwroot* ".</span><span class="sxs-lookup"><span data-stu-id="eaa21-162">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="eaa21-163">Legen Sie `TypescriptOutDir` die-Eigenschaft innerhalb `PropertyGroup` einer in der Projektdatei fest:</span><span class="sxs-lookup"><span data-stu-id="eaa21-163">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="eaa21-164">Fügen Sie das typescript-Ziel als Abhängigkeit des `ResolveCurrentProjectStaticWebAssets` Ziels ein, indem Sie das folgende Ziel in `PropertyGroup` einer in der Projektdatei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="eaa21-164">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     TypeScriptCompile;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="eaa21-165">Nutzen von Inhalten aus einer referenzierten RCL</span><span class="sxs-lookup"><span data-stu-id="eaa21-165">Consume content from a referenced RCL</span></span>

<span data-ttu-id="eaa21-166">Die Dateien, die im Ordner " *wwwroot* " der RCL enthalten sind, werden für die verbrauchende app unter dem Präfix `_content/{LIBRARY NAME}/`verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="eaa21-166">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="eaa21-167">Eine Bibliothek mit dem Namen *Razor. Class. lib* führt z. b. zu einem Pfad zu `_content/Razor.Class.Lib/`statischem Inhalt unter.</span><span class="sxs-lookup"><span data-stu-id="eaa21-167">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="eaa21-168">Die verarbeitende App verweist auf statische Ressourcen, die von der `<script>`Bibliothek `<style>`mit `<img>`,, und anderen HTML-Tags bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="eaa21-168">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="eaa21-169">In der nutzenden app muss die [Unterstützung statischer Dateien](xref:fundamentals/static-files) in `Startup.Configure`aktiviert sein:</span><span class="sxs-lookup"><span data-stu-id="eaa21-169">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="eaa21-170">Beim Ausführen der nutzenden App aus der Buildausgabe (`dotnet run`) werden statische Webressourcen standardmäßig in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="eaa21-170">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="eaa21-171">Um Assets in anderen Umgebungen bei Ausführung `UseStaticWebAssets` über die Buildausgabe zu unterstützen, müssen Sie auf dem Host-Generator in *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="eaa21-171">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="eaa21-172">Das `UseStaticWebAssets` Aufrufen von ist nicht erforderlich, wenn eine APP aus`dotnet publish`der veröffentlichten Ausgabe () ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="eaa21-172">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="eaa21-173">Entwicklungsfluss für mehrere Projekte</span><span class="sxs-lookup"><span data-stu-id="eaa21-173">Multi-project development flow</span></span>

<span data-ttu-id="eaa21-174">Wenn die verarbeitende app ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="eaa21-174">When the consuming app runs:</span></span>

* <span data-ttu-id="eaa21-175">Die Assets in der RCL bleiben in ihren ursprünglichen Ordnern.</span><span class="sxs-lookup"><span data-stu-id="eaa21-175">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="eaa21-176">Die Assets werden nicht in die Verb raubende App verschoben.</span><span class="sxs-lookup"><span data-stu-id="eaa21-176">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="eaa21-177">Jede Änderung im Ordner " *wwwroot* " der RCL wird in der nutzenden App widergespiegelt, nachdem die RCL neu erstellt wurde und ohne die Verb raubende APP neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-177">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="eaa21-178">Wenn die RCL erstellt wurde, wird ein Manifest erstellt, in dem die statischen webasset-Speicherorte beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="eaa21-178">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="eaa21-179">Die Verb raubende App liest das Manifest zur Laufzeit, um die Assets aus referenzierten Projekten und Paketen zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-179">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="eaa21-180">Wenn einer RCL ein neues Asset hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, bevor eine verarbeitende App auf das neue Medienobjekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="eaa21-180">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="eaa21-181">Veröffentlichen</span><span class="sxs-lookup"><span data-stu-id="eaa21-181">Publish</span></span>

<span data-ttu-id="eaa21-182">Wenn die App veröffentlicht wird, werden die begleitenden Objekte aus allen referenzierten Projekten und Paketen in den Ordner *wwwroot* der veröffentlichten app unter `_content/{LIBRARY NAME}/`kopiert.</span><span class="sxs-lookup"><span data-stu-id="eaa21-182">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="eaa21-183">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="eaa21-183">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="eaa21-184">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eaa21-184">The RCL can be packaged and reused.</span></span> <span data-ttu-id="eaa21-185">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-185">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="eaa21-186">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="eaa21-186">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="eaa21-187">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="eaa21-187">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="eaa21-188">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="eaa21-188">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eaa21-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa21-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="eaa21-190">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="eaa21-191">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-191">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="eaa21-192">Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="eaa21-192">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="eaa21-193">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-193">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="eaa21-194">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="eaa21-194">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="eaa21-195">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-195">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="eaa21-196">Eine RCL hat die folgende Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="eaa21-196">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eaa21-197">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="eaa21-197">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eaa21-198">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-198">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="eaa21-199">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eaa21-199">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="eaa21-200">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="eaa21-200">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="eaa21-201">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-201">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="eaa21-202">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="eaa21-202">Add Razor files to the RCL.</span></span>

<span data-ttu-id="eaa21-203">ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner.</span><span class="sxs-lookup"><span data-stu-id="eaa21-203">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="eaa21-204">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL `~/Pages` , die `~/Areas/Pages`Inhalte in anstelle von verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="eaa21-204">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="eaa21-205">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="eaa21-205">Reference RCL content</span></span>

<span data-ttu-id="eaa21-206">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="eaa21-206">The RCL can be referenced by:</span></span>

* <span data-ttu-id="eaa21-207">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="eaa21-207">NuGet package.</span></span> <span data-ttu-id="eaa21-208">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="eaa21-208">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="eaa21-209">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="eaa21-209">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="eaa21-210">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="eaa21-210">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="eaa21-211">Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und verwenden aus einem Razor Pages Projekt</span><span class="sxs-lookup"><span data-stu-id="eaa21-211">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="eaa21-212">Sie können das [vollständige Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-212">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="eaa21-213">Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern.</span><span class="sxs-lookup"><span data-stu-id="eaa21-213">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="eaa21-214">[In diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.</span><span class="sxs-lookup"><span data-stu-id="eaa21-214">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="eaa21-215">Testen der heruntergeladenen App</span><span class="sxs-lookup"><span data-stu-id="eaa21-215">Test the download app</span></span>

<span data-ttu-id="eaa21-216">Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.</span><span class="sxs-lookup"><span data-stu-id="eaa21-216">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eaa21-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa21-217">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa21-218">Öffnen Sie die *SLN*-Datei in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="eaa21-218">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="eaa21-219">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-219">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eaa21-220">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="eaa21-220">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eaa21-221">Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.</span><span class="sxs-lookup"><span data-stu-id="eaa21-221">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="eaa21-222">Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="eaa21-222">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="eaa21-223">Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test-webapp1).</span><span class="sxs-lookup"><span data-stu-id="eaa21-223">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="eaa21-224">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="eaa21-224">Create an RCL</span></span>

<span data-ttu-id="eaa21-225">In diesem Abschnitt wird eine RCL erstellt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-225">In this section, an RCL is created.</span></span> <span data-ttu-id="eaa21-226">Dabei werden der RCL Razor-Dateien hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-226">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eaa21-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa21-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa21-228">Erstellen Sie das RCL-Projekt:</span><span class="sxs-lookup"><span data-stu-id="eaa21-228">Create the RCL project:</span></span>

* <span data-ttu-id="eaa21-229">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-229">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="eaa21-230">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-230">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="eaa21-231">Nennen Sie die APP **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="eaa21-231">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="eaa21-232">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="eaa21-232">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="eaa21-233">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-233">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="eaa21-234">Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.</span><span class="sxs-lookup"><span data-stu-id="eaa21-234">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eaa21-235">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="eaa21-235">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eaa21-236">Führen Sie die folgenden Befehle über die Befehlszeile aus:</span><span class="sxs-lookup"><span data-stu-id="eaa21-236">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="eaa21-237">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="eaa21-237">The preceding commands:</span></span>

* <span data-ttu-id="eaa21-238">Erstellt die `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="eaa21-238">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="eaa21-239">Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-239">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="eaa21-240">Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-240">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="eaa21-241">Erstellt eine [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) Datei und fügt es der RCL hinzu.</span><span class="sxs-lookup"><span data-stu-id="eaa21-241">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="eaa21-242">Die *_ViewStart.cshtml* Datei ist erforderlich, um das Layout des Projekts mit Razor Pages verwenden (die im nächsten Abschnitt hinzugefügt wird).</span><span class="sxs-lookup"><span data-stu-id="eaa21-242">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="eaa21-243">Razor-Dateien und Ordner zum Projekt hinzufügen</span><span class="sxs-lookup"><span data-stu-id="eaa21-243">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="eaa21-244">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="eaa21-244">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="eaa21-245">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="eaa21-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="eaa21-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="eaa21-246">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="eaa21-247">Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-247">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="eaa21-248">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="eaa21-248">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="eaa21-249">Weitere Informationen zu *_ViewImports.cshtml*, finden Sie unter [freigegebenen Richtlinien importieren](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="eaa21-249">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="eaa21-250">Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:</span><span class="sxs-lookup"><span data-stu-id="eaa21-250">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="eaa21-251">Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="eaa21-251">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="eaa21-252">*RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="eaa21-252">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="eaa21-253">Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt</span><span class="sxs-lookup"><span data-stu-id="eaa21-253">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="eaa21-254">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="eaa21-254">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="eaa21-255">Erstellen Sie die Web-App mit Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="eaa21-255">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="eaa21-256">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf die Projekt Mappe > **Neues Projekt** **Hinzufügen** >.</span><span class="sxs-lookup"><span data-stu-id="eaa21-256">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="eaa21-257">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-257">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="eaa21-258">Legen Sie als Namen für die App **WebApp1** fest.</span><span class="sxs-lookup"><span data-stu-id="eaa21-258">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="eaa21-259">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="eaa21-259">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="eaa21-260">Wählen Sie **Webanwendung** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-260">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="eaa21-261">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.</span><span class="sxs-lookup"><span data-stu-id="eaa21-261">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="eaa21-262">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie **Abhängigkeiten** > **Projekt Abhängigkeiten**erstellen aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-262">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="eaa21-263">Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.</span><span class="sxs-lookup"><span data-stu-id="eaa21-263">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="eaa21-264">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie **Verweis** **Hinzufügen** > aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-264">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="eaa21-265">Aktivieren Sie im Dialogfeld **Verweis-Manager** die Option **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="eaa21-265">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="eaa21-266">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="eaa21-266">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="eaa21-267">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="eaa21-267">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="eaa21-268">Erstellen Sie eine Razor Pages Web-App und eine Projektmappendatei, die die Razor Pages-APP und die RCL enthält:</span><span class="sxs-lookup"><span data-stu-id="eaa21-268">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="eaa21-269">Erstellen Sie die Web-App, und führen Sie sie aus:</span><span class="sxs-lookup"><span data-stu-id="eaa21-269">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="eaa21-270">Testen des WebApp1-Projekts</span><span class="sxs-lookup"><span data-stu-id="eaa21-270">Test WebApp1</span></span>

<span data-ttu-id="eaa21-271">Navigieren Sie `/MyFeature/Page1` zu, um zu überprüfen, ob die Razor UI-Klassenbibliothek verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="eaa21-271">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="eaa21-272">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="eaa21-272">Override views, partial views, and pages</span></span>

<span data-ttu-id="eaa21-273">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="eaa21-273">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="eaa21-274">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="eaa21-274">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="eaa21-275">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="eaa21-275">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="eaa21-276">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaa21-276">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="eaa21-277">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="eaa21-277">Update the markup to indicate the new location.</span></span> <span data-ttu-id="eaa21-278">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="eaa21-278">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="eaa21-279">RCL Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="eaa21-279">RCL Pages layout</span></span>

<span data-ttu-id="eaa21-280">Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="eaa21-280">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="eaa21-281">*RazorUIClassLib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="eaa21-281">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="eaa21-282">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="eaa21-282">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="eaa21-283">Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="eaa21-283">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="eaa21-284">Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="eaa21-284">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
