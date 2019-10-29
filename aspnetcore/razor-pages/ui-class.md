---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: Erläutert das Erstellen einer wiederverwendbaren Razor-Benutzeroberfläche mithilfe von partiellen Sichten in einer Klassenbibliothek in ASP.net Core.
ms.author: riande
ms.date: 10/26/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: ff12eea5406c4f5392a466728741000e3dd16fc1
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034231"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="e5875-103">Erstellen wiederverwendbarer Benutzeroberflächen mithilfe des Razor-Klassen Bibliotheks Projekts in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="e5875-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="e5875-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e5875-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e5875-105">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="e5875-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="e5875-106">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e5875-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="e5875-107">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="e5875-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="e5875-108">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="e5875-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="e5875-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e5875-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="e5875-110">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="e5875-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5875-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5875-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5875-112">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="e5875-113">Wählen **Sie >** **Razor-Klassenbibliothek** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="e5875-114">Benennen Sie die Bibliothek (z. b. "razorclasslib"), > **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="e5875-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="e5875-115">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="e5875-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="e5875-116">Wählen Sie **Supportseiten und Sichten** aus, wenn Sie Ansichten unterstützen müssen.</span><span class="sxs-lookup"><span data-stu-id="e5875-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="e5875-117">Standardmäßig werden nur Razor Pages unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e5875-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="e5875-118">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-118">Select **Create**.</span></span>

<span data-ttu-id="e5875-119">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="e5875-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="e5875-120">Die Option **Support Pages und Views** unterstützt Seiten und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="e5875-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e5875-121">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e5875-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e5875-122">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="e5875-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e5875-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="e5875-124">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="e5875-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="e5875-125">Übergeben Sie die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`), um Unterstützung für Seiten und Ansichten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="e5875-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="e5875-126">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="e5875-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="e5875-127">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="e5875-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="e5875-128">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="e5875-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="e5875-129">Die ASP.net Core Vorlagen setzen voraus, dass sich der RCL-Inhalt im Ordner " *Bereiche* " befindet.</span><span class="sxs-lookup"><span data-stu-id="e5875-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="e5875-130">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="e5875-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="e5875-131">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="e5875-131">Reference RCL content</span></span>

<span data-ttu-id="e5875-132">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="e5875-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="e5875-133">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="e5875-133">NuGet package.</span></span> <span data-ttu-id="e5875-134">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="e5875-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="e5875-135">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="e5875-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="e5875-136">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="e5875-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="e5875-137">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="e5875-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="e5875-138">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="e5875-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="e5875-139">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="e5875-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="e5875-140">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="e5875-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="e5875-141">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e5875-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="e5875-142">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="e5875-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="e5875-143">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e5875-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="e5875-144">RCL-Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="e5875-144">RCL Pages layout</span></span>

<span data-ttu-id="e5875-145">Um auf RCL-Inhalte zu verweisen, als wären Sie Teil des Ordners " *pages* " der Web-App, erstellen Sie das RCL-Projekt mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="e5875-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="e5875-146">*Razoruiclasslib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="e5875-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="e5875-147">*Razoruiclasslib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="e5875-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="e5875-148">Angenommen, *razoruiclasslib/Pages/Shared* enthält zwei partielle Dateien: *_Header. cshtml* und *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e5875-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="e5875-149">Die `<partial>` Tags konnten der Datei *_Layout. cshtml* hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e5875-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="e5875-150">Erstellen einer RCL mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="e5875-150">Create an RCL with static assets</span></span>

<span data-ttu-id="e5875-151">Eine RCL erfordert möglicherweise begleitende statische Ressourcen, auf die von der Verb raubenden App der RCL verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="e5875-151">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="e5875-152">ASP.net Core ermöglicht das Erstellen von rcls, die statische Ressourcen enthalten, die für eine verarbeitende app verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="e5875-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="e5875-153">Wenn Sie begleitende Assets als Teil einer RCL einschließen möchten, erstellen Sie in der Klassenbibliothek einen Ordner *wwwroot* , und fügen Sie alle erforderlichen Dateien in diesen Ordner ein.</span><span class="sxs-lookup"><span data-stu-id="e5875-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="e5875-154">Beim Packen einer RCL werden alle Begleit Objekte im Ordner " *wwwroot* " automatisch in das Paket eingefügt.</span><span class="sxs-lookup"><span data-stu-id="e5875-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="e5875-155">Statische Assets ausschließen</span><span class="sxs-lookup"><span data-stu-id="e5875-155">Exclude static assets</span></span>

<span data-ttu-id="e5875-156">Fügen Sie den gewünschten Ausschluss Pfad zur `$(DefaultItemExcludes)` Eigenschaften Gruppe in der Projektdatei hinzu, um statische Assets auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="e5875-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="e5875-157">Trennen Sie Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="e5875-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="e5875-158">Im folgenden Beispiel wird das *lib. CSS* -Stylesheet im Ordner " *wwwroot* " nicht als statisches Asset angesehen und ist nicht in der veröffentlichten RCL enthalten:</span><span class="sxs-lookup"><span data-stu-id="e5875-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="e5875-159">Typescript-Integration</span><span class="sxs-lookup"><span data-stu-id="e5875-159">Typescript integration</span></span>

<span data-ttu-id="e5875-160">So fügen Sie typescript-Dateien in eine RCL ein:</span><span class="sxs-lookup"><span data-stu-id="e5875-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="e5875-161">Platzieren Sie die typescript-Dateien (*TS*) außerhalb des Ordners *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="e5875-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="e5875-162">Platzieren Sie die Dateien z. b. in einem *Client* Ordner.</span><span class="sxs-lookup"><span data-stu-id="e5875-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="e5875-163">Konfigurieren Sie die typescript-Buildausgabe für den Ordner " *wwwroot* ".</span><span class="sxs-lookup"><span data-stu-id="e5875-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="e5875-164">Legen Sie die `TypescriptOutDir`-Eigenschaft innerhalb einer `PropertyGroup` in der Projektdatei fest:</span><span class="sxs-lookup"><span data-stu-id="e5875-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="e5875-165">Fügen Sie das typescript-Ziel als Abhängigkeit des `ResolveCurrentProjectStaticWebAssets` Ziels ein, indem Sie das folgende Ziel in einer `PropertyGroup` in der Projektdatei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="e5875-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="e5875-166">Nutzen von Inhalten aus einer referenzierten RCL</span><span class="sxs-lookup"><span data-stu-id="e5875-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="e5875-167">Die Dateien, die im Ordner " *wwwroot* " der RCL enthalten sind, werden für die verbrauchende app unter dem Präfix `_content/{LIBRARY NAME}/`verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="e5875-167">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="e5875-168">Eine Bibliothek mit dem Namen *Razor. Class. lib* führt z. b. zu einem Pfad zu statischem Inhalt `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="e5875-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span>

<span data-ttu-id="e5875-169">Die verarbeitende App verweist auf statische Ressourcen, die von der Bibliothek mit `<script>`, `<style>`, `<img>`und anderen HTML-Tags bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="e5875-169">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="e5875-170">Für die verarbeitende app muss die [Unterstützung statischer Dateien](xref:fundamentals/static-files) in `Startup.Configure`aktiviert sein:</span><span class="sxs-lookup"><span data-stu-id="e5875-170">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="e5875-171">Wenn Sie die Verb raubende App aus der Buildausgabe (`dotnet run`) ausführen, werden statische Webressourcen standardmäßig in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="e5875-171">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="e5875-172">Um Assets in anderen Umgebungen bei Ausführung über die Buildausgabe zu unterstützen, wenden Sie `UseStaticWebAssets` auf dem Host-Generator in *Program.cs*an:</span><span class="sxs-lookup"><span data-stu-id="e5875-172">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="e5875-173">Das Aufrufen von `UseStaticWebAssets` ist nicht erforderlich, wenn eine APP aus der veröffentlichten Ausgabe (`dotnet publish`) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e5875-173">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="e5875-174">Entwicklungsfluss für mehrere Projekte</span><span class="sxs-lookup"><span data-stu-id="e5875-174">Multi-project development flow</span></span>

<span data-ttu-id="e5875-175">Wenn die verarbeitende app ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="e5875-175">When the consuming app runs:</span></span>

* <span data-ttu-id="e5875-176">Die Assets in der RCL bleiben in ihren ursprünglichen Ordnern.</span><span class="sxs-lookup"><span data-stu-id="e5875-176">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="e5875-177">Die Assets werden nicht in die Verb raubende App verschoben.</span><span class="sxs-lookup"><span data-stu-id="e5875-177">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="e5875-178">Jede Änderung im Ordner " *wwwroot* " der RCL wird in der nutzenden App widergespiegelt, nachdem die RCL neu erstellt wurde und ohne die Verb raubende APP neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e5875-178">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="e5875-179">Wenn die RCL erstellt wurde, wird ein Manifest erstellt, in dem die statischen webasset-Speicherorte beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="e5875-179">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="e5875-180">Die Verb raubende App liest das Manifest zur Laufzeit, um die Assets aus referenzierten Projekten und Paketen zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="e5875-180">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="e5875-181">Wenn einer RCL ein neues Asset hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, bevor eine verarbeitende App auf das neue Medienobjekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="e5875-181">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="e5875-182">Veröffentlichen</span><span class="sxs-lookup"><span data-stu-id="e5875-182">Publish</span></span>

<span data-ttu-id="e5875-183">Wenn die App veröffentlicht wird, werden die begleitenden Objekte aus allen referenzierten Projekten und Paketen in den Ordner *wwwroot* der veröffentlichten app unter `_content/{LIBRARY NAME}/`kopiert.</span><span class="sxs-lookup"><span data-stu-id="e5875-183">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e5875-184">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="e5875-184">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="e5875-185">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e5875-185">The RCL can be packaged and reused.</span></span> <span data-ttu-id="e5875-186">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="e5875-186">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="e5875-187">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="e5875-187">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="e5875-188">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="e5875-188">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="e5875-189">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="e5875-189">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5875-190">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5875-190">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e5875-191">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-191">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e5875-192">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-192">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e5875-193">Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="e5875-193">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="e5875-194">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="e5875-194">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="e5875-195">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="e5875-195">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="e5875-196">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-196">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="e5875-197">Eine RCL hat die folgende Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="e5875-197">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e5875-198">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e5875-198">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e5875-199">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-199">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="e5875-200">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e5875-200">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="e5875-201">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="e5875-201">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="e5875-202">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="e5875-202">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="e5875-203">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="e5875-203">Add Razor files to the RCL.</span></span>

<span data-ttu-id="e5875-204">Die ASP.net Core Vorlagen setzen voraus, dass sich der RCL-Inhalt im Ordner " *Bereiche* " befindet.</span><span class="sxs-lookup"><span data-stu-id="e5875-204">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="e5875-205">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="e5875-205">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="e5875-206">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="e5875-206">Reference RCL content</span></span>

<span data-ttu-id="e5875-207">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="e5875-207">The RCL can be referenced by:</span></span>

* <span data-ttu-id="e5875-208">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="e5875-208">NuGet package.</span></span> <span data-ttu-id="e5875-209">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="e5875-209">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="e5875-210">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="e5875-210">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="e5875-211">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="e5875-211">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="e5875-212">Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und verwenden aus einem Razor Pages Projekt</span><span class="sxs-lookup"><span data-stu-id="e5875-212">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="e5875-213">Sie können das [vollständige Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="e5875-213">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="e5875-214">Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern.</span><span class="sxs-lookup"><span data-stu-id="e5875-214">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="e5875-215">[In diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.</span><span class="sxs-lookup"><span data-stu-id="e5875-215">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="e5875-216">Testen der heruntergeladenen App</span><span class="sxs-lookup"><span data-stu-id="e5875-216">Test the download app</span></span>

<span data-ttu-id="e5875-217">Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.</span><span class="sxs-lookup"><span data-stu-id="e5875-217">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5875-218">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5875-218">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5875-219">Öffnen Sie die *SLN*-Datei in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="e5875-219">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="e5875-220">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-220">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e5875-221">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e5875-221">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e5875-222">Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.</span><span class="sxs-lookup"><span data-stu-id="e5875-222">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="e5875-223">Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="e5875-223">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="e5875-224">Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test-webapp1).</span><span class="sxs-lookup"><span data-stu-id="e5875-224">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="e5875-225">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="e5875-225">Create an RCL</span></span>

<span data-ttu-id="e5875-226">In diesem Abschnitt wird eine RCL erstellt.</span><span class="sxs-lookup"><span data-stu-id="e5875-226">In this section, an RCL is created.</span></span> <span data-ttu-id="e5875-227">Dabei werden der RCL Razor-Dateien hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e5875-227">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5875-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5875-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5875-229">Erstellen Sie das RCL-Projekt:</span><span class="sxs-lookup"><span data-stu-id="e5875-229">Create the RCL project:</span></span>

* <span data-ttu-id="e5875-230">Wählen Sie in Visual Studio im Menü **Datei** die Option **Neu** > **Projekt** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-230">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e5875-231">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-231">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e5875-232">Nennen Sie die APP **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e5875-232">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="e5875-233">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="e5875-233">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="e5875-234">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-234">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="e5875-235">Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.</span><span class="sxs-lookup"><span data-stu-id="e5875-235">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e5875-236">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e5875-236">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e5875-237">Führen Sie die folgenden Befehle über die Befehlszeile aus:</span><span class="sxs-lookup"><span data-stu-id="e5875-237">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="e5875-238">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="e5875-238">The preceding commands:</span></span>

* <span data-ttu-id="e5875-239">Erstellt den `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="e5875-239">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="e5875-240">Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="e5875-240">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="e5875-241">Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.</span><span class="sxs-lookup"><span data-stu-id="e5875-241">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="e5875-242">Erstellt eine [_ViewStart. cshtml](xref:mvc/views/layout#running-code-before-each-view) -Datei und fügt Sie der RCL hinzu.</span><span class="sxs-lookup"><span data-stu-id="e5875-242">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="e5875-243">Die Datei *_ViewStart. cshtml* ist erforderlich, um das Layout des Razor Pages Projekts (das im nächsten Abschnitt hinzugefügt wird) zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e5875-243">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="e5875-244">Hinzufügen von Razor-Dateien und-Ordnern zum Projekt</span><span class="sxs-lookup"><span data-stu-id="e5875-244">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="e5875-245">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="e5875-245">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="e5875-246">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="e5875-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="e5875-247">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="e5875-247">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="e5875-248">Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="e5875-248">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="e5875-249">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="e5875-249">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="e5875-250">Weitere Informationen zu " *_ViewImports. cshtml*" finden Sie unter Importieren von frei [gegebenen Direktiven](xref:mvc/views/layout#importing-shared-directives) .</span><span class="sxs-lookup"><span data-stu-id="e5875-250">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="e5875-251">Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:</span><span class="sxs-lookup"><span data-stu-id="e5875-251">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="e5875-252">Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="e5875-252">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="e5875-253">*RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="e5875-253">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="e5875-254">Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt</span><span class="sxs-lookup"><span data-stu-id="e5875-254">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e5875-255">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e5875-255">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e5875-256">Erstellen Sie die Web-App mit Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="e5875-256">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="e5875-257">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf die Projekt Mappe, > >**Neues Projekt** **hinzuzufügen** .</span><span class="sxs-lookup"><span data-stu-id="e5875-257">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="e5875-258">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-258">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e5875-259">Legen Sie als Namen für die App **WebApp1** fest.</span><span class="sxs-lookup"><span data-stu-id="e5875-259">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="e5875-260">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="e5875-260">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="e5875-261">Wählen Sie **Webanwendung** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-261">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="e5875-262">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.</span><span class="sxs-lookup"><span data-stu-id="e5875-262">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="e5875-263">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie Abhängigkeiten > **Projekt Abhängigkeiten** **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-263">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="e5875-264">Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.</span><span class="sxs-lookup"><span data-stu-id="e5875-264">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="e5875-265">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie > **Verweis** **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-265">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="e5875-266">Aktivieren Sie im Dialogfeld **Verweis-Manager** die Option **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="e5875-266">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="e5875-267">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="e5875-267">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="e5875-268">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e5875-268">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="e5875-269">Erstellen Sie eine Razor Pages Web-App und eine Projektmappendatei, die die Razor Pages-APP und die RCL enthält:</span><span class="sxs-lookup"><span data-stu-id="e5875-269">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="e5875-270">Erstellen Sie die Web-App, und führen Sie sie aus:</span><span class="sxs-lookup"><span data-stu-id="e5875-270">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="e5875-271">Testen des WebApp1-Projekts</span><span class="sxs-lookup"><span data-stu-id="e5875-271">Test WebApp1</span></span>

<span data-ttu-id="e5875-272">Navigieren Sie zu `/MyFeature/Page1`, um zu überprüfen, ob die Razor-UI-Klassenbibliothek verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e5875-272">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="e5875-273">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="e5875-273">Override views, partial views, and pages</span></span>

<span data-ttu-id="e5875-274">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="e5875-274">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="e5875-275">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="e5875-275">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="e5875-276">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="e5875-276">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="e5875-277">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e5875-277">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="e5875-278">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="e5875-278">Update the markup to indicate the new location.</span></span> <span data-ttu-id="e5875-279">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="e5875-279">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="e5875-280">RCL-Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="e5875-280">RCL Pages layout</span></span>

<span data-ttu-id="e5875-281">Um auf RCL-Inhalte zu verweisen, als wären Sie Teil des Ordners " *pages* " der Web-App, erstellen Sie das RCL-Projekt mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="e5875-281">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="e5875-282">*Razoruiclasslib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="e5875-282">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="e5875-283">*Razoruiclasslib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="e5875-283">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="e5875-284">Angenommen, *razoruiclasslib/Pages/Shared* enthält zwei partielle Dateien: *_Header. cshtml* und *_Footer. cshtml*.</span><span class="sxs-lookup"><span data-stu-id="e5875-284">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="e5875-285">Die `<partial>` Tags konnten der Datei *_Layout. cshtml* hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="e5875-285">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
