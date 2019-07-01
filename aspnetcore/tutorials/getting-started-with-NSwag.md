---
title: Erste Schritte mit NSwag und ASP.NET Core
author: zuckerthoben
description: Erfahren Sie, wie Sie NSwag zum Generieren von Dokumentationen und Hilfeseiten für eine ASP.NET Core-Web-API-App verwenden können.
ms.author: scaddie
ms.custom: mvc
ms.date: 06/21/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: c5b2dc47328d6d3c271a87579fa8c300109bd734
ms.sourcegitcommit: 06a455d63ff7d6b571ca832e8117f4ac9d646baf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316554"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="aa0f1-103">Erste Schritte mit NSwag und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="aa0f1-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="aa0f1-104">Von [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) und [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="aa0f1-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="aa0f1-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa0f1-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="aa0f1-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="aa0f1-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="aa0f1-107">NSwag bietet die folgenden Funktionen:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="aa0f1-108">Die Möglichkeit, die Swagger-Benutzeroberfläche und den Swagger-Generator zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="aa0f1-109">Flexible Codegenerierung.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="aa0f1-110">Mit NSwag müssen Sie keine API&mdash; besitzen, sondern können APIs von Drittanbietern verwenden, die Swagger integrieren und eine Clientimplementierung generieren.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="aa0f1-111">Mit NSwag können Sie den Entwicklungszyklus beschleunigen und sich problemlos an API-Änderungen anpassen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="aa0f1-112">Registrieren der NSwag-Middleware</span><span class="sxs-lookup"><span data-stu-id="aa0f1-112">Register the NSwag middleware</span></span>

<span data-ttu-id="aa0f1-113">Die NSwag-Middleware wird für folgende Zwecke registriert:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="aa0f1-114">Generieren Sie die Swagger-Spezifikation für die implementierte Web-API.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="aa0f1-115">Stellen Sie die Swagger-Benutzeroberfläche zum Durchsuchen und Testen die Web-API bereit.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="aa0f1-116">Um [NSwag](https://github.com/RicoSuter/NSwag) mit ASP.NET Core-Middleware zu verwenden, installieren Sie das NuGet-Paket [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="aa0f1-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="aa0f1-117">Dieses Paket enthält die Middleware zum Generieren und Bereitstellen der Swagger-Spezifikation, der Swagger-Benutzeroberfläche (v2 und v3) und der [ReDoc-Benutzeroberfläche](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="aa0f1-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="aa0f1-118">Verwenden Sie einen der folgenden Ansätze, um das NuGet-Paket „NSwag“ zu installieren:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa0f1-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa0f1-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aa0f1-120">Aus dem Fenster **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="aa0f1-121">Navigieren Sie zu **Ansicht** > **Weitere Fenster** > **Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="aa0f1-122">Navigieren Sie zu dem Verzeichnis, in dem die *TodoApi.csproj*-Datei gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="aa0f1-123">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="aa0f1-124">Aus dem Dialogfeld **NuGet-Pakete verwalten**:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="aa0f1-125">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="aa0f1-126">Legen Sie die **Paketquelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="aa0f1-127">Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="aa0f1-128">Wählen Sie das Paket „NSwag.AspNetCore“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aa0f1-129">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa0f1-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="aa0f1-130">Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...** .</span><span class="sxs-lookup"><span data-stu-id="aa0f1-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="aa0f1-131">Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="aa0f1-132">Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="aa0f1-133">Wählen Sie das Paket „NSwag.AspNetCore“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="aa0f1-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="aa0f1-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="aa0f1-135">Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-135">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="aa0f1-136">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aa0f1-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="aa0f1-137">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-137">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="aa0f1-138">Hinzufügen und Konfigurieren von Swagger-Middleware</span><span class="sxs-lookup"><span data-stu-id="aa0f1-138">Add and configure Swagger middleware</span></span>

<span data-ttu-id="aa0f1-139">Sie können Swagger in Ihrer ASP.NET Core-App hinzufügen und konfigurieren, indem Sie die folgenden Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-139">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="aa0f1-140">Registrieren Sie in der `Startup.ConfigureServices`-Methode die erforderlichen Swagger-Dienste:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-140">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="aa0f1-141">Aktivieren Sie die Middleware in der `Startup.Configure`-Methode, um die generierte Swagger-Spezifikation und die Swagger-Benutzeroberfläche zu verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-141">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="aa0f1-142">Starten Sie die App.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-142">Launch the app.</span></span> <span data-ttu-id="aa0f1-143">Navigieren Sie zu:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-143">Navigate to:</span></span>
  * <span data-ttu-id="aa0f1-144">`http://localhost:<port>/swagger`, um die Swagger-Benutzeroberfläche anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-144">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="aa0f1-145">`http://localhost:<port>/swagger/v1/swagger.json`, um die Swagger-Spezifikation anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-145">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="aa0f1-146">Codeerzeugung</span><span class="sxs-lookup"><span data-stu-id="aa0f1-146">Code generation</span></span>

<span data-ttu-id="aa0f1-147">Durch die Auswahl einer der folgenden Optionen können Sie die Funktion zur Codegenerierung von NSwag nutzen:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-147">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="aa0f1-148">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash;– Dies ist eine Windows-Desktop-App zum Generieren von API-Clientcode in C# und TypeScript.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-148">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="aa0f1-149">Die NuGet-Pakete [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) oder [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/), um Code innerhalb des Projekts zu generieren.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-149">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="aa0f1-150">NSwag über die [Befehlszeile](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="aa0f1-150">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="aa0f1-151">Das NuGet-Paket [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/MSBuild).</span><span class="sxs-lookup"><span data-stu-id="aa0f1-151">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/MSBuild) NuGet package.</span></span>
* <span data-ttu-id="aa0f1-152">[Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice): Ein mit Visual Studio verbundener Dienst zum Generieren von API-Clientcode in C# oder TypeScript.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-152">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; a Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="aa0f1-153">Es werden auch C#-Controller für OpenAPI-Dienste mit NSwag generiert.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-153">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="aa0f1-154">Generieren von Code mit NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="aa0f1-154">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="aa0f1-155">Installieren Sie NSwagStudio anhand der Anweisungen im [NSwagStudio-GitHub-Repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="aa0f1-155">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="aa0f1-156">Starten Sie NSwagStudio, und geben Sie die Datei-URL *swagger.json* in das Textfeld **URL zur Spezifizierung des Swaggers** ein.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-156">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="aa0f1-157">Beispiel: *http://localhost:44354/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="aa0f1-157">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="aa0f1-158">Klicken Sie auf die Schaltfläche **Lokale Kopie erstellen**, um eine JSON-Darstellung Ihrer Swagger-Spezifikation zu generieren.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-158">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Erstellen einer lokalen Kopie der Swagger-Spezifikation](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="aa0f1-160">Klicken Sie im Bereich **Ausgaben** auf das Kontrollkästchen **CSharp-Client**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-160">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="aa0f1-161">Je nach Ihrem Projekt können Sie auch den **TypeScript Client** oder **CSharp-Web-API-Controller** auswählen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-161">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="aa0f1-162">Bei der Auswahl des **CSharp-Web-API-Controllers** erstellt eine Dienstspezifikation den Dienst neu, und dient als eine umgekehrte Generierung.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-162">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="aa0f1-163">Klicken Sie auf **Ausgaben generieren**, um eine vollständige C#-Clientimplementierung des *TodoApi.NSwag*-Projekts durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-163">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="aa0f1-164">Klicken Sie auf die Registerkarte **CSharp-Client**, damit der generierte Clientcode angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-164">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="aa0f1-165">Die C#-Clientcode wird auf Grundlage Ihrer Auswahl auf der Registerkarte **Einstellungen** generiert. Ändern Sie die Einstellungen, um Aufgaben wie das Umbenennen von Standardnamespaces und die Generierung von synchronen Methoden auszuführen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-165">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="aa0f1-166">Kopieren Sie den generierten C#-Code in eine Datei in das Clientprojekt, das die API verwendet.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-166">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="aa0f1-167">Verwendung der Web-API:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-167">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="aa0f1-168">Anpassen der API-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="aa0f1-168">Customize API documentation</span></span>

<span data-ttu-id="aa0f1-169">Swagger umfasst Optionen zum Dokumentieren des Objektmodells, um die Verwendung der Web-API zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-169">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="aa0f1-170">API-Informationen und -Beschreibung</span><span class="sxs-lookup"><span data-stu-id="aa0f1-170">API info and description</span></span>

<span data-ttu-id="aa0f1-171">In der `Startup.ConfigureServices`-Methode werden Informationen wie Autor, Lizenz und Beschreibung durch die Konfigurationsaktion hinzugefügt, die an die `AddSwaggerDocument`-Methode übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-171">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="aa0f1-172">Auf der Swagger-Benutzeroberfläche werden die Versionsinformationen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-172">The Swagger UI displays the version's information:</span></span>

![Swagger-Benutzeroberfläche mit Versionsinformationen](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="aa0f1-174">XML-Kommentare</span><span class="sxs-lookup"><span data-stu-id="aa0f1-174">XML comments</span></span>

<span data-ttu-id="aa0f1-175">Um XML-Kommentaren zu aktivieren, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-175">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="aa0f1-176">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aa0f1-176">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="aa0f1-177">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **<project_name>.csproj bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-177">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="aa0f1-178">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-178">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="aa0f1-179">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-179">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="aa0f1-180">Überprüfen Sie das Feld **XML-Dokumentationsdatei** im Abschnitt **Ausgabe** der Registerkarte **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-180">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="aa0f1-181">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="aa0f1-181">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="aa0f1-182">Drücken und halten Sie im *Lösungspad* die **CONTROL**-Taste, und klicken Sie auf den Projektnamen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-182">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="aa0f1-183">Navigieren Sie zu **Extras** > **Datei bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-183">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="aa0f1-184">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-184">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="aa0f1-185">Öffnen Sie das Dialogfeld **Projektoptionen** > **Erstellen** > **Compiler**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-185">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="aa0f1-186">Überprüfen Sie das Feld **XML-Dokumentation generieren** im Abschnitt **Allgemeine Optionen**.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-186">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="aa0f1-187">Visual Studio Code / .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="aa0f1-187">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="aa0f1-188">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-188">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="aa0f1-189">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="aa0f1-189">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="aa0f1-190">Da NSwag [Reflektion](/dotnet/csharp/programming-guide/concepts/reflection) verwendet, und der empfohlene Rückgabetyp ist für Web-API-Aktionen [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), kann nicht abgeleitet werden, was Ihre Aktion ausführt und was zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-190">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="aa0f1-191">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-191">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="aa0f1-192">Die vorherige Aktion gibt `IActionResult` zurück. Innerhalb der Aktion gibt sie jedoch [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) oder [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) zurück.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-192">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="aa0f1-193">Verwenden Sie Datenanmerkungen, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-193">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="aa0f1-194">Ergänzen Sie die Aktion mit folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-194">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

 <span data-ttu-id="aa0f1-195">Da NSwag [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) verwendet, und der empfohlene Rückgabetyp für Web-API-Aktionen [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601) ist, kann nur der von `T` definierte Rückgabetyp abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-195">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="aa0f1-196">Sie können nicht automatisch andere mögliche Rückgabetypen ableiten.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-196">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="aa0f1-197">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-197">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="aa0f1-198">Die vorherige Aktion gibt `ActionResult<T>` zurück.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-198">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="aa0f1-199">In der Aktion gibt sie [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) zurück.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-199">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="aa0f1-200">Da der Controller mit dem [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut ausgestattet ist, ist auch eine [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)-Antwort möglich.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-200">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="aa0f1-201">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="aa0f1-201">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="aa0f1-202">Verwenden Sie Datenanmerkungen, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-202">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="aa0f1-203">Ergänzen Sie die Aktion mit folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="aa0f1-203">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="aa0f1-204">In ASP.NET Core 2.2 oder höher können Sie stattdessen Konventionen verwenden, um einzelne Aktionen explizit mit `[ProducesResponseType]` zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-204">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="aa0f1-205">Weitere Informationen finden Sie unter <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-205">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="aa0f1-206">Der Swagger-Generator kann diese Aktion nun genau beschreiben, und generierte Clients wissen, was sie beim Aufrufen des Endpunkts empfangen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-206">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="aa0f1-207">Es wird empfohlen, alle Aktionen mit diesen Attributen zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="aa0f1-207">As a recommendation, decorate all actions with these attributes.</span></span>

<span data-ttu-id="aa0f1-208">Weitere Informationen zu den Richtlinien dazu, welche HTTP-Antworten Ihre API-Aktionen zurückgeben sollten, finden Sie in der [RFC 7231 specification (Spezifikation von RFC 7231)](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="aa0f1-208">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
