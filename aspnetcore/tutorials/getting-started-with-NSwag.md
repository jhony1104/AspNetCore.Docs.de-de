---
title: Erste Schritte mit NSwag und ASP.NET Core
author: zuckerthoben
description: Erfahren Sie, wie Sie NSwag zum Generieren von Dokumentationen und Hilfeseiten für eine ASP.NET Core-Web-API-App verwenden können.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/30/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: af07ad771c582cfad80f297748c3c1049ff4a7d6
ms.sourcegitcommit: 7d6019f762fc5b8cbedcd69801e8310f51a17c18
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58419406"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="0b285-103">Erste Schritte mit NSwag und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b285-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="0b285-104">Von [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com) und [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="0b285-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0b285-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0b285-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0b285-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0b285-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="0b285-107">NSwag bietet die folgenden Funktionen:</span><span class="sxs-lookup"><span data-stu-id="0b285-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="0b285-108">Die Möglichkeit, die Swagger-Benutzeroberfläche und den Swagger-Generator zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="0b285-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="0b285-109">Flexible Codegenerierung.</span><span class="sxs-lookup"><span data-stu-id="0b285-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="0b285-110">Mit NSwag müssen Sie keine API&mdash; besitzen, sondern können APIs von Drittanbietern verwenden, die Swagger integrieren und eine Clientimplementierung generieren.</span><span class="sxs-lookup"><span data-stu-id="0b285-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="0b285-111">Mit NSwag können Sie den Entwicklungszyklus beschleunigen und sich problemlos an API-Änderungen anpassen.</span><span class="sxs-lookup"><span data-stu-id="0b285-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="0b285-112">Registrieren der NSwag-Middleware</span><span class="sxs-lookup"><span data-stu-id="0b285-112">Register the NSwag middleware</span></span>

<span data-ttu-id="0b285-113">Die NSwag-Middleware wird für folgende Zwecke registriert:</span><span class="sxs-lookup"><span data-stu-id="0b285-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="0b285-114">Generieren Sie die Swagger-Spezifikation für die implementierte Web-API.</span><span class="sxs-lookup"><span data-stu-id="0b285-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="0b285-115">Stellen Sie die Swagger-Benutzeroberfläche zum Durchsuchen und Testen die Web-API bereit.</span><span class="sxs-lookup"><span data-stu-id="0b285-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="0b285-116">Um [NSwag](https://github.com/RSuter/NSwag) mit ASP.NET Core-Middleware zu verwenden, installieren Sie das NuGet-Paket [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="0b285-116">To use the [NSwag](https://github.com/RSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="0b285-117">Dieses Paket enthält die Middleware zum Generieren und Bereitstellen der Swagger-Spezifikation, der Swagger-Benutzeroberfläche (v2 und v3) und der [ReDoc-Benutzeroberfläche](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="0b285-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="0b285-118">Verwenden Sie einen der folgenden Ansätze, um das NuGet-Paket „NSwag“ zu installieren:</span><span class="sxs-lookup"><span data-stu-id="0b285-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0b285-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b285-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0b285-120">Aus dem Fenster **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="0b285-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="0b285-121">Navigieren Sie zu **Ansicht** > **Weitere Fenster** > **Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="0b285-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="0b285-122">Navigieren Sie zu dem Verzeichnis, in dem die *TodoApi.csproj*-Datei gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="0b285-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="0b285-123">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0b285-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="0b285-124">Aus dem Dialogfeld **NuGet-Pakete verwalten**:</span><span class="sxs-lookup"><span data-stu-id="0b285-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="0b285-125">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="0b285-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="0b285-126">Legen Sie die **Paketquelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="0b285-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="0b285-127">Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="0b285-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="0b285-128">Wählen Sie das Paket „NSwag.AspNetCore“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="0b285-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0b285-129">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0b285-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="0b285-130">Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.</span><span class="sxs-lookup"><span data-stu-id="0b285-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="0b285-131">Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="0b285-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="0b285-132">Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="0b285-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="0b285-133">Wählen Sie das Paket „NSwag.AspNetCore“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="0b285-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0b285-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0b285-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="0b285-135">Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="0b285-135">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="0b285-136">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="0b285-136">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0b285-137">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="0b285-137">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="0b285-138">Hinzufügen und Konfigurieren von Swagger-Middleware</span><span class="sxs-lookup"><span data-stu-id="0b285-138">Add and configure Swagger middleware</span></span>

 <span data-ttu-id="0b285-139">Durch die Ausführung der folgenden Schritte in der `Startup`-Klasse können Sie Swagger in Ihrer ASP.NET Core-App hinzufügen und konfigurieren:</span><span class="sxs-lookup"><span data-stu-id="0b285-139">Add and configure Swagger in your ASP.NET Core app by performing the following steps in the `Startup` class:</span></span>

* <span data-ttu-id="0b285-140">Importieren Sie die folgenden Namespaces:</span><span class="sxs-lookup"><span data-stu-id="0b285-140">Import the following namespaces:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

* <span data-ttu-id="0b285-141">Registrieren Sie in der `ConfigureServices`-Methode die erforderlichen Swagger-Dienste:</span><span class="sxs-lookup"><span data-stu-id="0b285-141">In the `ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="0b285-142">Aktivieren Sie die Middleware in der `Configure`-Methode, um die generierte Swagger-Spezifikation und die Swagger-Benutzeroberfläche zu verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="0b285-142">In the `Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="0b285-143">Starten Sie die App.</span><span class="sxs-lookup"><span data-stu-id="0b285-143">Launch the app.</span></span> <span data-ttu-id="0b285-144">Navigieren Sie zu:</span><span class="sxs-lookup"><span data-stu-id="0b285-144">Navigate to:</span></span>
  * <span data-ttu-id="0b285-145">`http://localhost:<port>/swagger`, um die Swagger-Benutzeroberfläche anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0b285-145">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="0b285-146">`http://localhost:<port>/swagger/v1/swagger.json`, um die Swagger-Spezifikation anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0b285-146">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="0b285-147">Codeerzeugung</span><span class="sxs-lookup"><span data-stu-id="0b285-147">Code generation</span></span>

<span data-ttu-id="0b285-148">Durch die Auswahl einer der folgenden Optionen können Sie die Funktion zur Codegenerierung von NSwag nutzen:</span><span class="sxs-lookup"><span data-stu-id="0b285-148">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="0b285-149">[NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio) &ndash;– Dies ist eine Windows-Desktop-App zum Generieren von API-Clientcode in C# und TypeScript.</span><span class="sxs-lookup"><span data-stu-id="0b285-149">[NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="0b285-150">Die NuGet-Pakete [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) oder [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/), um Code innerhalb des Projekts zu generieren.</span><span class="sxs-lookup"><span data-stu-id="0b285-150">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="0b285-151">NSwag über die [Befehlszeile](https://github.com/NSwag/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="0b285-151">NSwag from the [command line](https://github.com/NSwag/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="0b285-152">Das NuGet-Paket [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild).</span><span class="sxs-lookup"><span data-stu-id="0b285-152">The [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) NuGet package.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="0b285-153">Generieren von Code mit NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="0b285-153">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="0b285-154">Installieren Sie NSwagStudio anhand der Anweisungen im [NSwagStudio-GitHub-Repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="0b285-154">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="0b285-155">Starten Sie NSwagStudio, und geben Sie die Datei-URL *swagger.json* in das Textfeld **URL zur Spezifizierung des Swaggers** ein.</span><span class="sxs-lookup"><span data-stu-id="0b285-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="0b285-156">Beispiel: *http://localhost:44354/swagger/v1/swagger.json*</span><span class="sxs-lookup"><span data-stu-id="0b285-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="0b285-157">Klicken Sie auf die Schaltfläche **Lokale Kopie erstellen**, um eine JSON-Darstellung Ihrer Swagger-Spezifikation zu generieren.</span><span class="sxs-lookup"><span data-stu-id="0b285-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Erstellen einer lokalen Kopie der Swagger-Spezifikation](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="0b285-159">Klicken Sie im Bereich **Ausgaben** auf das Kontrollkästchen **CSharp-Client**.</span><span class="sxs-lookup"><span data-stu-id="0b285-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="0b285-160">Je nach Ihrem Projekt können Sie auch den **TypeScript Client** oder **CSharp-Web-API-Controller** auswählen.</span><span class="sxs-lookup"><span data-stu-id="0b285-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="0b285-161">Bei der Auswahl des **CSharp-Web-API-Controllers** erstellt eine Dienstspezifikation den Dienst neu, und dient als eine umgekehrte Generierung.</span><span class="sxs-lookup"><span data-stu-id="0b285-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="0b285-162">Klicken Sie auf **Ausgaben generieren**, um eine vollständige C#-Clientimplementierung des *TodoApi.NSwag*-Projekts durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="0b285-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="0b285-163">Klicken Sie auf die Registerkarte **CSharp-Client**, damit der generierte Clientcode angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="0b285-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

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
> <span data-ttu-id="0b285-164">Die C#-Clientcode wird auf Grundlage Ihrer Auswahl auf der Registerkarte **Einstellungen** generiert. Ändern Sie die Einstellungen, um Aufgaben wie das Umbenennen von Standardnamespaces und die Generierung von synchronen Methoden auszuführen.</span><span class="sxs-lookup"><span data-stu-id="0b285-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="0b285-165">Kopieren Sie den generierten C#-Code in eine Datei in das Clientprojekt, das die API verwendet.</span><span class="sxs-lookup"><span data-stu-id="0b285-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="0b285-166">Verwendung der Web-API:</span><span class="sxs-lookup"><span data-stu-id="0b285-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="0b285-167">Anpassen der API-Dokumentation</span><span class="sxs-lookup"><span data-stu-id="0b285-167">Customize API documentation</span></span>

<span data-ttu-id="0b285-168">Swagger umfasst Optionen zum Dokumentieren des Objektmodells, um die Verwendung der Web-API zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="0b285-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="0b285-169">API-Informationen und -Beschreibung</span><span class="sxs-lookup"><span data-stu-id="0b285-169">API info and description</span></span>

<span data-ttu-id="0b285-170">In der `Startup.ConfigureServices`-Methode werden Informationen wie Autor, Lizenz und Beschreibung durch die Konfigurationsaktion hinzugefügt, die an die `AddSwaggerDocument`-Methode übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="0b285-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="0b285-171">Auf der Swagger-Benutzeroberfläche werden die Versionsinformationen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="0b285-171">The Swagger UI displays the version's information:</span></span>

![Swagger-Benutzeroberfläche mit Versionsinformationen](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="0b285-173">XML-Kommentare</span><span class="sxs-lookup"><span data-stu-id="0b285-173">XML comments</span></span>

<span data-ttu-id="0b285-174">Um XML-Kommentaren zu aktivieren, führen Sie die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="0b285-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0b285-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0b285-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0b285-176">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **<project_name>.csproj bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="0b285-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="0b285-177">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="0b285-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0b285-178">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="0b285-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="0b285-179">Überprüfen Sie das Feld **XML-Dokumentationsdatei** im Abschnitt **Ausgabe** der Registerkarte **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="0b285-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0b285-180">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="0b285-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="0b285-181">Drücken und halten Sie im *Lösungspad* die **CONTROL**-Taste, und klicken Sie auf den Projektnamen.</span><span class="sxs-lookup"><span data-stu-id="0b285-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="0b285-182">Navigieren Sie zu **Extras** > **Datei bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="0b285-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="0b285-183">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="0b285-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="0b285-184">Öffnen Sie das Dialogfeld **Projektoptionen** > **Erstellen** > **Compiler**.</span><span class="sxs-lookup"><span data-stu-id="0b285-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="0b285-185">Überprüfen Sie das Feld **XML-Dokumentation generieren** im Abschnitt **Allgemeine Optionen**.</span><span class="sxs-lookup"><span data-stu-id="0b285-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="0b285-186">Visual Studio Code / .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="0b285-186">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="0b285-187">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="0b285-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="0b285-188">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="0b285-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0b285-189">Da NSwag [Reflektion](/dotnet/csharp/programming-guide/concepts/reflection) verwendet, und der empfohlene Rückgabetyp ist für Web-API-Aktionen [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), kann nicht abgeleitet werden, was Ihre Aktion ausführt und was zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="0b285-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="0b285-190">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0b285-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="0b285-191">Die vorherige Aktion gibt `IActionResult` zurück. Innerhalb der Aktion gibt sie jedoch [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) oder [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) zurück.</span><span class="sxs-lookup"><span data-stu-id="0b285-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="0b285-192">Verwenden Sie Datenanmerkungen, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="0b285-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="0b285-193">Ergänzen Sie die Aktion mit folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="0b285-193">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

 <span data-ttu-id="0b285-194">Da NSwag [Reflection](/dotnet/csharp/programming-guide/concepts/reflection) verwendet, und der empfohlene Rückgabetyp für Web-API-Aktionen [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601) ist, kann nur der von `T` definierte Rückgabetyp abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="0b285-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="0b285-195">Sie können nicht automatisch andere mögliche Rückgabetypen ableiten.</span><span class="sxs-lookup"><span data-stu-id="0b285-195">You can't automatically infer other possible return types.</span></span> 

<span data-ttu-id="0b285-196">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="0b285-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="0b285-197">Die vorherige Aktion gibt `ActionResult<T>` zurück.</span><span class="sxs-lookup"><span data-stu-id="0b285-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="0b285-198">In der Aktion gibt sie [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) zurück.</span><span class="sxs-lookup"><span data-stu-id="0b285-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="0b285-199">Da der Controller mit dem [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut ausgestattet ist, ist auch eine [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)-Antwort möglich.</span><span class="sxs-lookup"><span data-stu-id="0b285-199">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="0b285-200">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="0b285-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="0b285-201">Verwenden Sie Datenanmerkungen, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="0b285-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="0b285-202">Ergänzen Sie die Aktion mit folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="0b285-202">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="0b285-203">In ASP.NET Core 2.2 oder höher können Sie stattdessen Konventionen verwenden, um einzelne Aktionen explizit mit `[ProducesResponseType]` zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="0b285-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="0b285-204">Weitere Informationen finden Sie unter <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="0b285-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="0b285-205">Der Swagger-Generator kann diese Aktion nun genau beschreiben, und generierte Clients wissen, was sie beim Aufrufen des Endpunkts empfangen.</span><span class="sxs-lookup"><span data-stu-id="0b285-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="0b285-206">Es wird empfohlen, alle Aktionen mit diesen Attributen zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="0b285-206">As a recommendation, decorate all actions with these attributes.</span></span>

<span data-ttu-id="0b285-207">Weitere Informationen zu den Richtlinien dazu, welche HTTP-Antworten Ihre API-Aktionen zurückgeben sollten, finden Sie in der [RFC 7231 specification (Spezifikation von RFC 7231)](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="0b285-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
