---
title: Wiederverwendbare Razor-Benutzeroberfläche in Klassenbibliotheken mit ASP.NET Core
author: Rick-Anderson
description: Es wird erläutert, wie wiederverwendbare Teilansichten in einer Klassenbibliothek in ASP.NET Core mit Razor-Benutzeroberfläche zu erstellen.
ms.author: riande
ms.date: 10/26/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: b5235f4e31f6edd21fb410824fb215ab2d4a41b6
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727288"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a><span data-ttu-id="89a16-103">Erstellen wiederverwendbarer Benutzeroberflächen mithilfe des Razor-Klassen Bibliotheks Projekts in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="89a16-103">Create reusable UI using the Razor class library project in ASP.NET Core</span></span>

<span data-ttu-id="89a16-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="89a16-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="89a16-105">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="89a16-105">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="89a16-106">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="89a16-106">The RCL can be packaged and reused.</span></span> <span data-ttu-id="89a16-107">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="89a16-107">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="89a16-108">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="89a16-108">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="89a16-109">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89a16-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="89a16-110">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="89a16-110">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89a16-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89a16-111">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89a16-112">Wählen Sie in Visual Studio die Option **Neues Projekt erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-112">From Visual Studio select **Create new a new project**.</span></span>
* <span data-ttu-id="89a16-113">Wählen **Sie >** **Razor-Klassenbibliothek** aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-113">Select **Razor Class Library** > **Next**.</span></span>
* <span data-ttu-id="89a16-114">Benennen Sie die Bibliothek (z. b. "razorclasslib"), > **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="89a16-114">Name the library (for example, "RazorClassLib"), > **Create**.</span></span> <span data-ttu-id="89a16-115">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="89a16-115">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="89a16-116">Wählen Sie **Supportseiten und Sichten** aus, wenn Sie Ansichten unterstützen müssen.</span><span class="sxs-lookup"><span data-stu-id="89a16-116">Select **Support pages and views** if you need to support views.</span></span> <span data-ttu-id="89a16-117">Standardmäßig werden nur Razor Pages unterstützt.</span><span class="sxs-lookup"><span data-stu-id="89a16-117">By default, only Razor Pages are supported.</span></span> <span data-ttu-id="89a16-118">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-118">Select **Create**.</span></span>

<span data-ttu-id="89a16-119">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="89a16-119">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="89a16-120">Die Option **Support Pages und Views** unterstützt Seiten und Ansichten.</span><span class="sxs-lookup"><span data-stu-id="89a16-120">The **Support pages and views** option supports pages and views.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="89a16-121">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="89a16-121">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89a16-122">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-122">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="89a16-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="89a16-123">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="89a16-124">Die Vorlage der Razor-Klassenbibliothek (Razor Class Library, RCL) gilt standardmäßig für die Razor-Komponentenentwicklung.</span><span class="sxs-lookup"><span data-stu-id="89a16-124">The Razor class library (RCL) template defaults to Razor component development by default.</span></span> <span data-ttu-id="89a16-125">Übergeben Sie die Option `--support-pages-and-views` (`dotnet new razorclasslib --support-pages-and-views`), um Unterstützung für Seiten und Ansichten bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="89a16-125">Pass the `--support-pages-and-views` option (`dotnet new razorclasslib --support-pages-and-views`) to provide support for pages and views.</span></span>

<span data-ttu-id="89a16-126">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="89a16-126">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="89a16-127">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="89a16-127">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="89a16-128">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="89a16-128">Add Razor files to the RCL.</span></span>

<span data-ttu-id="89a16-129">ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner.</span><span class="sxs-lookup"><span data-stu-id="89a16-129">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="89a16-130">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="89a16-130">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="89a16-131">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="89a16-131">Reference RCL content</span></span>

<span data-ttu-id="89a16-132">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="89a16-132">The RCL can be referenced by:</span></span>

* <span data-ttu-id="89a16-133">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="89a16-133">NuGet package.</span></span> <span data-ttu-id="89a16-134">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="89a16-134">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="89a16-135">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="89a16-135">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="89a16-136">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="89a16-136">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="89a16-137">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="89a16-137">Override views, partial views, and pages</span></span>

<span data-ttu-id="89a16-138">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="89a16-138">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="89a16-139">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="89a16-139">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="89a16-140">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="89a16-140">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="89a16-141">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="89a16-141">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="89a16-142">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="89a16-142">Update the markup to indicate the new location.</span></span> <span data-ttu-id="89a16-143">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="89a16-143">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="89a16-144">RCL Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="89a16-144">RCL Pages layout</span></span>

<span data-ttu-id="89a16-145">Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="89a16-145">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="89a16-146">*RazorUIClassLib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="89a16-146">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="89a16-147">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="89a16-147">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="89a16-148">Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="89a16-148">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="89a16-149">Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="89a16-149">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a><span data-ttu-id="89a16-150">Erstellen einer RCL mit statischen Assets</span><span class="sxs-lookup"><span data-stu-id="89a16-150">Create an RCL with static assets</span></span>

<span data-ttu-id="89a16-151">Eine RCL erfordert möglicherweise begleitende statische Ressourcen, auf die von der Verb raubenden App der RCL verwiesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="89a16-151">An RCL may require companion static assets that can be referenced by the consuming app of the RCL.</span></span> <span data-ttu-id="89a16-152">ASP.net Core ermöglicht das Erstellen von rcls, die statische Ressourcen enthalten, die für eine verarbeitende app verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="89a16-152">ASP.NET Core allows creating RCLs that include static assets that are available to a consuming app.</span></span>

<span data-ttu-id="89a16-153">Wenn Sie begleitende Assets als Teil einer RCL einschließen möchten, erstellen Sie in der Klassenbibliothek einen Ordner *wwwroot* , und fügen Sie alle erforderlichen Dateien in diesen Ordner ein.</span><span class="sxs-lookup"><span data-stu-id="89a16-153">To include companion assets as part of an RCL, create a *wwwroot* folder in the class library and include any required files in that folder.</span></span>

<span data-ttu-id="89a16-154">Beim Packen einer RCL werden alle Begleit Objekte im Ordner " *wwwroot* " automatisch in das Paket eingefügt.</span><span class="sxs-lookup"><span data-stu-id="89a16-154">When packing an RCL, all companion assets in the *wwwroot* folder are automatically included in the package.</span></span>

### <a name="exclude-static-assets"></a><span data-ttu-id="89a16-155">Statische Assets ausschließen</span><span class="sxs-lookup"><span data-stu-id="89a16-155">Exclude static assets</span></span>

<span data-ttu-id="89a16-156">Fügen Sie den gewünschten Ausschluss Pfad zur `$(DefaultItemExcludes)` Eigenschaften Gruppe in der Projektdatei hinzu, um statische Assets auszuschließen.</span><span class="sxs-lookup"><span data-stu-id="89a16-156">To exclude static assets, add the desired exclusion path to the `$(DefaultItemExcludes)` property group in the project file.</span></span> <span data-ttu-id="89a16-157">Trennen Sie Einträge durch ein Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="89a16-157">Separate entries with a semicolon (`;`).</span></span>

<span data-ttu-id="89a16-158">Im folgenden Beispiel wird das *lib. CSS* -Stylesheet im Ordner " *wwwroot* " nicht als statisches Asset angesehen und ist nicht in der veröffentlichten RCL enthalten:</span><span class="sxs-lookup"><span data-stu-id="89a16-158">In the following example, the *lib.css* stylesheet in the *wwwroot* folder isn't considered a static asset and isn't included in the published RCL:</span></span>

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a><span data-ttu-id="89a16-159">Typescript-Integration</span><span class="sxs-lookup"><span data-stu-id="89a16-159">Typescript integration</span></span>

<span data-ttu-id="89a16-160">So fügen Sie typescript-Dateien in eine RCL ein:</span><span class="sxs-lookup"><span data-stu-id="89a16-160">To include TypeScript files in an RCL:</span></span>

1. <span data-ttu-id="89a16-161">Platzieren Sie die typescript-Dateien (*TS*) außerhalb des Ordners *wwwroot* .</span><span class="sxs-lookup"><span data-stu-id="89a16-161">Place the TypeScript files (*.ts*) outside of the *wwwroot* folder.</span></span> <span data-ttu-id="89a16-162">Platzieren Sie die Dateien z. b. in einem *Client* Ordner.</span><span class="sxs-lookup"><span data-stu-id="89a16-162">For example, place the files in a *Client* folder.</span></span>

1. <span data-ttu-id="89a16-163">Konfigurieren Sie die typescript-Buildausgabe für den Ordner " *wwwroot* ".</span><span class="sxs-lookup"><span data-stu-id="89a16-163">Configure the TypeScript build output for the *wwwroot* folder.</span></span> <span data-ttu-id="89a16-164">Legen Sie die `TypescriptOutDir`-Eigenschaft innerhalb einer `PropertyGroup` in der Projektdatei fest:</span><span class="sxs-lookup"><span data-stu-id="89a16-164">Set the `TypescriptOutDir` property inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. <span data-ttu-id="89a16-165">Fügen Sie das typescript-Ziel als Abhängigkeit des `ResolveCurrentProjectStaticWebAssets` Ziels ein, indem Sie das folgende Ziel in einer `PropertyGroup` in der Projektdatei hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="89a16-165">Include the TypeScript target as a dependency of the `ResolveCurrentProjectStaticWebAssets` target by adding the following target inside of a `PropertyGroup` in the project file:</span></span>

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a><span data-ttu-id="89a16-166">Nutzen von Inhalten aus einer referenzierten RCL</span><span class="sxs-lookup"><span data-stu-id="89a16-166">Consume content from a referenced RCL</span></span>

<span data-ttu-id="89a16-167">Die Dateien, die im Ordner " *wwwroot* " der RCL enthalten sind, werden für die verbrauchende app unter dem Präfix `_content/{LIBRARY NAME}/`verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="89a16-167">The files included in the *wwwroot* folder of the RCL are exposed to the consuming app under the prefix `_content/{LIBRARY NAME}/`.</span></span> <span data-ttu-id="89a16-168">Eine Bibliothek mit dem Namen *Razor. Class. lib* führt z. b. zu einem Pfad zu statischem Inhalt `_content/Razor.Class.Lib/`.</span><span class="sxs-lookup"><span data-stu-id="89a16-168">For example, a library named *Razor.Class.Lib* results in a path to static content at `_content/Razor.Class.Lib/`.</span></span> <span data-ttu-id="89a16-169">Wenn Sie ein nuget-Paket erstellen und der Assemblyname nicht mit der Paket-ID übereinstimmt, verwenden Sie die Paket-ID für `{LIBRARY NAME}`.</span><span class="sxs-lookup"><span data-stu-id="89a16-169">When producing a NuGet package and the assembly name isn't the same as the package ID, use the the package ID for `{LIBRARY NAME}`.</span></span>

<span data-ttu-id="89a16-170">Die verarbeitende App verweist auf statische Ressourcen, die von der Bibliothek mit `<script>`, `<style>`, `<img>`und anderen HTML-Tags bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="89a16-170">The consuming app references static assets provided by the library with `<script>`, `<style>`, `<img>`, and other HTML tags.</span></span> <span data-ttu-id="89a16-171">Für die verarbeitende app muss die [Unterstützung statischer Dateien](xref:fundamentals/static-files) in `Startup.Configure`aktiviert sein:</span><span class="sxs-lookup"><span data-stu-id="89a16-171">The consuming app must have [static file support](xref:fundamentals/static-files) enabled in `Startup.Configure`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

<span data-ttu-id="89a16-172">Wenn Sie die Verb raubende App aus der Buildausgabe (`dotnet run`) ausführen, werden statische Webressourcen standardmäßig in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="89a16-172">When running the consuming app from build output (`dotnet run`), static web assets are enabled by default in the Development environment.</span></span> <span data-ttu-id="89a16-173">Um Assets in anderen Umgebungen bei Ausführung über die Buildausgabe zu unterstützen, wenden Sie `UseStaticWebAssets` auf dem Host-Generator in *Program.cs*an:</span><span class="sxs-lookup"><span data-stu-id="89a16-173">To support assets in other environments when running from build output, call `UseStaticWebAssets` on the host builder in *Program.cs*:</span></span>

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

<span data-ttu-id="89a16-174">Das Aufrufen von `UseStaticWebAssets` ist nicht erforderlich, wenn eine APP aus der veröffentlichten Ausgabe (`dotnet publish`) ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="89a16-174">Calling `UseStaticWebAssets` isn't required when running an app from published output (`dotnet publish`).</span></span>

### <a name="multi-project-development-flow"></a><span data-ttu-id="89a16-175">Entwicklungsfluss für mehrere Projekte</span><span class="sxs-lookup"><span data-stu-id="89a16-175">Multi-project development flow</span></span>

<span data-ttu-id="89a16-176">Wenn die verarbeitende app ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="89a16-176">When the consuming app runs:</span></span>

* <span data-ttu-id="89a16-177">Die Assets in der RCL bleiben in ihren ursprünglichen Ordnern.</span><span class="sxs-lookup"><span data-stu-id="89a16-177">The assets in the RCL stay in their original folders.</span></span> <span data-ttu-id="89a16-178">Die Assets werden nicht in die Verb raubende App verschoben.</span><span class="sxs-lookup"><span data-stu-id="89a16-178">The assets aren't moved to the consuming app.</span></span>
* <span data-ttu-id="89a16-179">Jede Änderung im Ordner " *wwwroot* " der RCL wird in der nutzenden App widergespiegelt, nachdem die RCL neu erstellt wurde und ohne die Verb raubende APP neu zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="89a16-179">Any change within the RCL's *wwwroot* folder is reflected in the consuming app after the RCL is rebuilt and without rebuilding the consuming app.</span></span>

<span data-ttu-id="89a16-180">Wenn die RCL erstellt wurde, wird ein Manifest erstellt, in dem die statischen webasset-Speicherorte beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="89a16-180">When the RCL is built, a manifest is produced that describes the static web asset locations.</span></span> <span data-ttu-id="89a16-181">Die Verb raubende App liest das Manifest zur Laufzeit, um die Assets aus referenzierten Projekten und Paketen zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="89a16-181">The consuming app reads the manifest at runtime to consume the assets from referenced projects and packages.</span></span> <span data-ttu-id="89a16-182">Wenn einer RCL ein neues Asset hinzugefügt wird, muss die RCL neu erstellt werden, um das Manifest zu aktualisieren, bevor eine verarbeitende App auf das neue Medienobjekt zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="89a16-182">When a new asset is added to an RCL, the RCL must be rebuilt to update its manifest before a consuming app can access the new asset.</span></span>

### <a name="publish"></a><span data-ttu-id="89a16-183">Veröffentlichungs</span><span class="sxs-lookup"><span data-stu-id="89a16-183">Publish</span></span>

<span data-ttu-id="89a16-184">Wenn die App veröffentlicht wird, werden die begleitenden Objekte aus allen referenzierten Projekten und Paketen in den Ordner *wwwroot* der veröffentlichten app unter `_content/{LIBRARY NAME}/`kopiert.</span><span class="sxs-lookup"><span data-stu-id="89a16-184">When the app is published, the companion assets from all referenced projects and packages are copied into the *wwwroot* folder of the published app under `_content/{LIBRARY NAME}/`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89a16-185">Razor-Ansichten, Seiten, Controller, Seiten Modelle, [Razor-Komponenten](xref:blazor/class-libraries), [Ansichts Komponenten](xref:mvc/views/view-components)und Datenmodelle können in eine Razor-Klassenbibliothek (RCL) integriert werden.</span><span class="sxs-lookup"><span data-stu-id="89a16-185">Razor views, pages, controllers, page models, [Razor components](xref:blazor/class-libraries), [View components](xref:mvc/views/view-components), and data models can be built into a Razor class library (RCL).</span></span> <span data-ttu-id="89a16-186">Die RCL kann verpackt und wiederverwendet werden.</span><span class="sxs-lookup"><span data-stu-id="89a16-186">The RCL can be packaged and reused.</span></span> <span data-ttu-id="89a16-187">Sie kann in Anwendungen eingebunden werden, wodurch sich die in der RCL enthaltenen Ansichten und Seiten überschreiben lassen.</span><span class="sxs-lookup"><span data-stu-id="89a16-187">Applications can include the RCL and override the views and pages it contains.</span></span> <span data-ttu-id="89a16-188">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="89a16-188">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span>

<span data-ttu-id="89a16-189">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="89a16-189">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="create-a-class-library-containing-razor-ui"></a><span data-ttu-id="89a16-190">Erstellen einer Klassenbibliothek, die eine Razor-Benutzeroberfläche enthält</span><span class="sxs-lookup"><span data-stu-id="89a16-190">Create a class library containing Razor UI</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89a16-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89a16-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="89a16-192">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="89a16-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="89a16-193">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-193">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="89a16-194">Geben Sie der Bibliothek einen Namen (z.B. „RazorClassLib“), und klicken Sie anschließend auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="89a16-194">Name the library (for example, "RazorClassLib") > **OK**.</span></span> <span data-ttu-id="89a16-195">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="89a16-195">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>
* <span data-ttu-id="89a16-196">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="89a16-196">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="89a16-197">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-197">Select **Razor Class Library** > **OK**.</span></span>

<span data-ttu-id="89a16-198">Eine RCL hat die folgende Projektdatei:</span><span class="sxs-lookup"><span data-stu-id="89a16-198">An RCL has the following project file:</span></span>

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="89a16-199">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="89a16-199">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89a16-200">Führen Sie `dotnet new razorclasslib` über die Befehlszeile aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-200">From the command line, run `dotnet new razorclasslib`.</span></span> <span data-ttu-id="89a16-201">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="89a16-201">For example:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

<span data-ttu-id="89a16-202">Weitere Informationen finden Sie unter [dotnet new](/dotnet/core/tools/dotnet-new).</span><span class="sxs-lookup"><span data-stu-id="89a16-202">For more information, see [dotnet new](/dotnet/core/tools/dotnet-new).</span></span> <span data-ttu-id="89a16-203">Stellen Sie sicher, dass der Bibliotheksname nicht auf `.Views` endet, um zu verhindern, dass es zu einem Dateinamenkonflikt mit der generierten Ansichtsbibliothek kommt.</span><span class="sxs-lookup"><span data-stu-id="89a16-203">To avoid a file name collision with the generated view library, ensure the library name doesn't end in `.Views`.</span></span>

---

<span data-ttu-id="89a16-204">Fügen Sie der RCL Razor-Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="89a16-204">Add Razor files to the RCL.</span></span>

<span data-ttu-id="89a16-205">ASP.NET Core-Vorlagen wird davon ausgegangen, der RCL Inhalt befindet sich in der *Bereiche* Ordner.</span><span class="sxs-lookup"><span data-stu-id="89a16-205">The ASP.NET Core templates assume the RCL content is in the *Areas* folder.</span></span> <span data-ttu-id="89a16-206">Weitere Informationen finden Sie unter [RCL Pages Layout](#rcl-pages-layout) zum Erstellen einer RCL, die Inhalte in `~/Pages` und nicht in `~/Areas/Pages`verfügbar macht.</span><span class="sxs-lookup"><span data-stu-id="89a16-206">See [RCL Pages layout](#rcl-pages-layout) to create an RCL that exposes content in `~/Pages` rather than `~/Areas/Pages`.</span></span>

## <a name="reference-rcl-content"></a><span data-ttu-id="89a16-207">Verweisen auf den RCL-Inhalt</span><span class="sxs-lookup"><span data-stu-id="89a16-207">Reference RCL content</span></span>

<span data-ttu-id="89a16-208">Folgende Komponenten können auf die RCL verweisen:</span><span class="sxs-lookup"><span data-stu-id="89a16-208">The RCL can be referenced by:</span></span>

* <span data-ttu-id="89a16-209">NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="89a16-209">NuGet package.</span></span> <span data-ttu-id="89a16-210">Weitere Informationen finden Sie unter [Creating NuGet packages](/nuget/create-packages/creating-a-package) (Erstellen von NuGet-Paketen), [dotnet add package](/dotnet/core/tools/dotnet-add-package) und [Erstellen und Veröffentlichen eines NuGet-Pakets](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span><span class="sxs-lookup"><span data-stu-id="89a16-210">See [Creating NuGet packages](/nuget/create-packages/creating-a-package) and [dotnet add package](/dotnet/core/tools/dotnet-add-package) and [Create and publish a NuGet package](/nuget/quickstart/create-and-publish-a-package-using-visual-studio).</span></span>
* <span data-ttu-id="89a16-211">*{ProjectName}.csproj*-Dateien.</span><span class="sxs-lookup"><span data-stu-id="89a16-211">*{ProjectName}.csproj*.</span></span> <span data-ttu-id="89a16-212">Weitere Informationen finden Sie unter [dotnet add reference](/dotnet/core/tools/dotnet-add-reference).</span><span class="sxs-lookup"><span data-stu-id="89a16-212">See [dotnet-add reference](/dotnet/core/tools/dotnet-add-reference).</span></span>

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a><span data-ttu-id="89a16-213">Exemplarische Vorgehensweise: Erstellen eines RCL-Projekts und verwenden aus einem Razor Pages Projekt</span><span class="sxs-lookup"><span data-stu-id="89a16-213">Walkthrough: Create an RCL project and use from a Razor Pages project</span></span>

<span data-ttu-id="89a16-214">Sie können das [vollständige Projekt](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) herunterladen und testen, anstatt es zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="89a16-214">You can download the [complete project](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples) and test it rather than creating it.</span></span> <span data-ttu-id="89a16-215">Der Beispieldownload enthält zusätzlichen Code sowie Links, die das Testen des Projekts erleichtern.</span><span class="sxs-lookup"><span data-stu-id="89a16-215">The sample download contains additional code and links that make the project easy to test.</span></span> <span data-ttu-id="89a16-216">[In diesem GitHub-Issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) können Sie in Form von Kommentaren Feedback zu Unterschieden zwischen Beispieldownloads und ausführlichen Anleitungen geben.</span><span class="sxs-lookup"><span data-stu-id="89a16-216">You can leave feedback in [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/6098) with your comments on download samples versus step-by-step instructions.</span></span>

### <a name="test-the-download-app"></a><span data-ttu-id="89a16-217">Testen der heruntergeladenen App</span><span class="sxs-lookup"><span data-stu-id="89a16-217">Test the download app</span></span>

<span data-ttu-id="89a16-218">Wenn Sie die vollständige App nicht heruntergeladen haben und das Beispielprojekt selbst erstellen möchten, können Sie mit dem [nächsten Abschnitt](#create-an-rcl) fortfahren.</span><span class="sxs-lookup"><span data-stu-id="89a16-218">If you haven't downloaded the completed app and would rather create the walkthrough project, skip to the [next section](#create-an-rcl).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89a16-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89a16-219">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89a16-220">Öffnen Sie die *SLN*-Datei in Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="89a16-220">Open the *.sln* file in Visual Studio.</span></span> <span data-ttu-id="89a16-221">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-221">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="89a16-222">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="89a16-222">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89a16-223">Erstellen Sie über die Eingabeaufforderung im *cli*-Verzeichnis die RCL und die Web-App.</span><span class="sxs-lookup"><span data-stu-id="89a16-223">From a command prompt in the *cli* directory, build the RCL and web app.</span></span>

```dotnetcli
dotnet build
```

<span data-ttu-id="89a16-224">Wechseln Sie ins Verzeichnis *WebApp1*, und führen Sie die App aus:</span><span class="sxs-lookup"><span data-stu-id="89a16-224">Move to the *WebApp1* directory and run the app:</span></span>

```dotnetcli
dotnet run
```

---

<span data-ttu-id="89a16-225">Folgen Sie den Anweisungen in [Testen des WebApp1-Projekts](#test-webapp1).</span><span class="sxs-lookup"><span data-stu-id="89a16-225">Follow the instructions in [Test WebApp1](#test-webapp1)</span></span>

## <a name="create-an-rcl"></a><span data-ttu-id="89a16-226">Erstellen einer RCL</span><span class="sxs-lookup"><span data-stu-id="89a16-226">Create an RCL</span></span>

<span data-ttu-id="89a16-227">In diesem Abschnitt wird eine RCL erstellt.</span><span class="sxs-lookup"><span data-stu-id="89a16-227">In this section, an RCL is created.</span></span> <span data-ttu-id="89a16-228">Dabei werden der RCL Razor-Dateien hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="89a16-228">Razor files are added to the RCL.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89a16-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89a16-229">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89a16-230">Erstellen Sie das RCL-Projekt:</span><span class="sxs-lookup"><span data-stu-id="89a16-230">Create the RCL project:</span></span>

* <span data-ttu-id="89a16-231">Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="89a16-231">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="89a16-232">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-232">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="89a16-233">Nennen Sie die APP **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="89a16-233">Name the app **RazorUIClassLib** > **OK**.</span></span>
* <span data-ttu-id="89a16-234">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="89a16-234">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="89a16-235">Wählen Sie **Razor-Klassenbibliothek** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-235">Select **Razor Class Library** > **OK**.</span></span>
* <span data-ttu-id="89a16-236">Fügen Sie eine Razor-Datei für die Teilansicht mit dem Namen „_Message.cshtml“ unter *RazorUIClassLib/Areas/MyFeature/Pages/Shared* hinzu.</span><span class="sxs-lookup"><span data-stu-id="89a16-236">Add a Razor partial view file named *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="89a16-237">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="89a16-237">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89a16-238">Führen Sie die folgenden Befehle über die Befehlszeile aus:</span><span class="sxs-lookup"><span data-stu-id="89a16-238">From the command line, run the following:</span></span>

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

<span data-ttu-id="89a16-239">Die obenstehenden Befehle haben folgende Konsequenzen:</span><span class="sxs-lookup"><span data-stu-id="89a16-239">The preceding commands:</span></span>

* <span data-ttu-id="89a16-240">Erstellt den `RazorUIClassLib` RCL.</span><span class="sxs-lookup"><span data-stu-id="89a16-240">Creates the `RazorUIClassLib` RCL.</span></span>
* <span data-ttu-id="89a16-241">Die Seite „Razor_Message“ wird erstellt und der RCL hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="89a16-241">Creates a Razor _Message page, and adds it to the RCL.</span></span> <span data-ttu-id="89a16-242">Durch den `-np`-Parameter wird die Seite ohne `PageModel` erstellt.</span><span class="sxs-lookup"><span data-stu-id="89a16-242">The `-np` parameter creates the page without a `PageModel`.</span></span>
* <span data-ttu-id="89a16-243">Erstellt eine [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) Datei und fügt es der RCL hinzu.</span><span class="sxs-lookup"><span data-stu-id="89a16-243">Creates a [_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view) file and adds it to the RCL.</span></span>

<span data-ttu-id="89a16-244">Die *_ViewStart.cshtml* Datei ist erforderlich, um das Layout des Projekts mit Razor Pages verwenden (die im nächsten Abschnitt hinzugefügt wird).</span><span class="sxs-lookup"><span data-stu-id="89a16-244">The *_ViewStart.cshtml* file is required to use the layout of the Razor Pages project (which is added in the next section).</span></span>

---

### <a name="add-razor-files-and-folders-to-the-project"></a><span data-ttu-id="89a16-245">Razor-Dateien und Ordner zum Projekt hinzufügen</span><span class="sxs-lookup"><span data-stu-id="89a16-245">Add Razor files and folders to the project</span></span>

* <span data-ttu-id="89a16-246">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="89a16-246">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* <span data-ttu-id="89a16-247">Ersetzen Sie das Markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* durch den folgenden Code:</span><span class="sxs-lookup"><span data-stu-id="89a16-247">Replace the markup in *RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml* with the following code:</span></span>

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  <span data-ttu-id="89a16-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` ist erforderlich, um die Teilansicht `<partial name="_Message" />` verwenden zu können.</span><span class="sxs-lookup"><span data-stu-id="89a16-248">`@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` is required to use the partial view (`<partial name="_Message" />`).</span></span> <span data-ttu-id="89a16-249">Wenn Sie nicht die `@addTagHelper`-Anweisung einbinden möchten, können Sie die Datei *_ViewImports.cshtml* hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="89a16-249">Rather than including the `@addTagHelper` directive, you can add a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="89a16-250">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="89a16-250">For example:</span></span>

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  <span data-ttu-id="89a16-251">Weitere Informationen zu *_ViewImports.cshtml*, finden Sie unter [freigegebenen Richtlinien importieren](xref:mvc/views/layout#importing-shared-directives)</span><span class="sxs-lookup"><span data-stu-id="89a16-251">For more information on *_ViewImports.cshtml*, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives)</span></span>

* <span data-ttu-id="89a16-252">Erstellen Sie die Klassenbibliothek, um sicherzustellen, dass keine Compilerfehler vorliegen:</span><span class="sxs-lookup"><span data-stu-id="89a16-252">Build the class library to verify there are no compiler errors:</span></span>

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

<span data-ttu-id="89a16-253">Die Buildausgabe enthält *RazorUIClassLib.dll* und *RazorUIClassLib.Views.dll*.</span><span class="sxs-lookup"><span data-stu-id="89a16-253">The build output contains *RazorUIClassLib.dll* and *RazorUIClassLib.Views.dll*.</span></span> <span data-ttu-id="89a16-254">*RazorUIClassLib.Views.dll* enthält den kompilierten Razor-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="89a16-254">*RazorUIClassLib.Views.dll* contains the compiled Razor content.</span></span>

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a><span data-ttu-id="89a16-255">Verwenden der Razor-Benutzeroberflächenbibliothek über ein Razor-Seiten-Projekt</span><span class="sxs-lookup"><span data-stu-id="89a16-255">Use the Razor UI library from a Razor Pages project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="89a16-256">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="89a16-256">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="89a16-257">Erstellen Sie die Web-App mit Razor-Seiten:</span><span class="sxs-lookup"><span data-stu-id="89a16-257">Create the Razor Pages web app:</span></span>

* <span data-ttu-id="89a16-258">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf die Projekt Mappe, > >**Neues Projekt** **hinzuzufügen** .</span><span class="sxs-lookup"><span data-stu-id="89a16-258">From **Solution Explorer**, right-click the solution > **Add** >  **New Project**.</span></span>
* <span data-ttu-id="89a16-259">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-259">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="89a16-260">Legen Sie als Namen für die App **WebApp1** fest.</span><span class="sxs-lookup"><span data-stu-id="89a16-260">Name the app **WebApp1**.</span></span>
* <span data-ttu-id="89a16-261">Überprüfen Sie, ob **ASP.NET Core 2.1** oder höher ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="89a16-261">Verify **ASP.NET Core 2.1** or later is selected.</span></span>
* <span data-ttu-id="89a16-262">Wählen Sie **Webanwendung** > **OK**aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-262">Select **Web Application** > **OK**.</span></span>

* <span data-ttu-id="89a16-263">Klicken Sie im **Projektmappen-Explorer** zuerst mit der rechten Maustaste auf **WebApp1** und anschließend auf **Als Startprojekt festlegen**.</span><span class="sxs-lookup"><span data-stu-id="89a16-263">From **Solution Explorer**, right-click on **WebApp1** and select **Set as StartUp Project**.</span></span>
* <span data-ttu-id="89a16-264">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie Abhängigkeiten > **Projekt Abhängigkeiten** **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-264">From **Solution Explorer**, right-click on **WebApp1** and select **Build Dependencies** > **Project Dependencies**.</span></span>
* <span data-ttu-id="89a16-265">Aktivieren Sie für **WebApp1** die Abhängigkeit **RazorUIClassLib**.</span><span class="sxs-lookup"><span data-stu-id="89a16-265">Check **RazorUIClassLib** as a dependency of **WebApp1**.</span></span>
* <span data-ttu-id="89a16-266">Klicken Sie in **Projektmappen-Explorer**mit der rechten Maustaste auf **"WebApp1"** , und wählen Sie > **Verweis** **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-266">From **Solution Explorer**, right-click on **WebApp1** and select **Add** > **Reference**.</span></span>
* <span data-ttu-id="89a16-267">Aktivieren Sie im Dialogfeld **Verweis-Manager** die Option **razoruiclasslib** > **OK**.</span><span class="sxs-lookup"><span data-stu-id="89a16-267">In the **Reference Manager** dialog, check **RazorUIClassLib** > **OK**.</span></span>

<span data-ttu-id="89a16-268">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="89a16-268">Run the app.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="89a16-269">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="89a16-269">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="89a16-270">Erstellen Sie eine Razor Pages Web-App und eine Projektmappendatei, die die Razor Pages-APP und die RCL enthält:</span><span class="sxs-lookup"><span data-stu-id="89a16-270">Create a Razor Pages web app and a solution file containing the Razor Pages app and the RCL:</span></span>

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

<span data-ttu-id="89a16-271">Erstellen Sie die Web-App, und führen Sie sie aus:</span><span class="sxs-lookup"><span data-stu-id="89a16-271">Build and run the web app:</span></span>

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a><span data-ttu-id="89a16-272">Testen des WebApp1-Projekts</span><span class="sxs-lookup"><span data-stu-id="89a16-272">Test WebApp1</span></span>

<span data-ttu-id="89a16-273">Navigieren Sie zu `/MyFeature/Page1`, um zu überprüfen, ob die Razor-UI-Klassenbibliothek verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="89a16-273">Browse to `/MyFeature/Page1` to verify that the Razor UI class library is in use.</span></span>

## <a name="override-views-partial-views-and-pages"></a><span data-ttu-id="89a16-274">Überschreiben von Ansichten, Teilansichten und Seiten</span><span class="sxs-lookup"><span data-stu-id="89a16-274">Override views, partial views, and pages</span></span>

<span data-ttu-id="89a16-275">Wenn eine Ansicht, Teilansicht oder Razor-Seite sowohl in der Web-App als auch in der RCL enthalten ist, hat das Razor-Markup (die *CSHTML*-Datei) in der Web-App Vorrang.</span><span class="sxs-lookup"><span data-stu-id="89a16-275">When a view, partial view, or Razor Page is found in both the web app and the RCL, the Razor markup (*.cshtml* file) in the web app takes precedence.</span></span> <span data-ttu-id="89a16-276">Fügen Sie z. b. *"WebApp1"/Areas/myfeature/Pages/"Page1. cshtml* zu" WebApp1 "hinzu, und" Page1 im "WebApp1" hat Vorrang vor "Page1 in der RCL.</span><span class="sxs-lookup"><span data-stu-id="89a16-276">For example, add *WebApp1/Areas/MyFeature/Pages/Page1.cshtml* to WebApp1, and Page1 in the WebApp1 will take precedence over Page1 in the RCL.</span></span>

<span data-ttu-id="89a16-277">Ob Page1 in WebApp1 tatsächlich die Priorität zugeordnet wird, können Sie testen, indem Sie im Beispieldownload *WebApp1/Areas/MyFeature2* in *WebApp1/Areas/MyFeature* umbenennen.</span><span class="sxs-lookup"><span data-stu-id="89a16-277">In the sample download, rename *WebApp1/Areas/MyFeature2* to *WebApp1/Areas/MyFeature* to test precedence.</span></span>

<span data-ttu-id="89a16-278">Kopieren Sie die Teilansicht *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* nach *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="89a16-278">Copy the *RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml* partial view to *WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml*.</span></span> <span data-ttu-id="89a16-279">Aktualisieren Sie das Markup, um den neuen Speicherort anzugeben.</span><span class="sxs-lookup"><span data-stu-id="89a16-279">Update the markup to indicate the new location.</span></span> <span data-ttu-id="89a16-280">Erstellen Sie die App, und führen Sie sie aus, um sicherzustellen, dass die Version mit der Teilansicht der App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="89a16-280">Build and run the app to verify the app's version of the partial is being used.</span></span>

### <a name="rcl-pages-layout"></a><span data-ttu-id="89a16-281">RCL Seitenlayout</span><span class="sxs-lookup"><span data-stu-id="89a16-281">RCL Pages layout</span></span>

<span data-ttu-id="89a16-282">Auf Inhalt RCL, als wäre es Teil der Web-app-Verweis *Seiten* Ordner das RCL-Projekt erstellen, mit der folgenden Dateistruktur:</span><span class="sxs-lookup"><span data-stu-id="89a16-282">To reference RCL content as though it is part of the web app's *Pages* folder, create the RCL project with the following file structure:</span></span>

* <span data-ttu-id="89a16-283">*RazorUIClassLib/Seiten*</span><span class="sxs-lookup"><span data-stu-id="89a16-283">*RazorUIClassLib/Pages*</span></span>
* <span data-ttu-id="89a16-284">*RazorUIClassLib/Pages/Shared*</span><span class="sxs-lookup"><span data-stu-id="89a16-284">*RazorUIClassLib/Pages/Shared*</span></span>

<span data-ttu-id="89a16-285">Nehmen wir an *RazorUIClassLib/Pages/Shared* enthält zwei partielle Dateien: *_Header.cshtml* und *_Footer.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="89a16-285">Suppose *RazorUIClassLib/Pages/Shared* contains two partial files: *_Header.cshtml* and *_Footer.cshtml*.</span></span> <span data-ttu-id="89a16-286">Die `<partial>` können Tags hinzugefügt werden *"_Layout.cshtml"* Datei:</span><span class="sxs-lookup"><span data-stu-id="89a16-286">The `<partial>` tags could be added to *_Layout.cshtml* file:</span></span>

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
