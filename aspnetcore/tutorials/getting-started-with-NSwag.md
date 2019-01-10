---
title: Erste Schritte mit NSwag und ASP.NET Core
author: zuckerthoben
description: Erfahren Sie, wie Sie NSwag zum Generieren von Dokumentationen und Hilfeseiten für eine ASP.NET Core-Web-API-App verwenden können.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/18/2018
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 8af5bed1e042c4f6d83043b05084c51b3064a548
ms.sourcegitcommit: ea215df889e89db44037a6ac2f01baede0450da9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53595359"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="237f4-103">Erste Schritte mit NSwag und ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="237f4-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="237f4-104">Von [Christoph Nienaber](https://twitter.com/zuckerthoben) und [Rico Suter](https://rsuter.com)</span><span class="sxs-lookup"><span data-stu-id="237f4-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben) and [Rico Suter](https://rsuter.com)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="237f4-105">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="237f4-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="237f4-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="237f4-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="237f4-107">Registrieren Sie die NSwag-Middlewares bei:</span><span class="sxs-lookup"><span data-stu-id="237f4-107">Register the NSwag middlewares to:</span></span>

* <span data-ttu-id="237f4-108">Generieren Sie die Swagger-Spezifikation für die implementierte Web-API.</span><span class="sxs-lookup"><span data-stu-id="237f4-108">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="237f4-109">Stellen Sie die Swagger-Benutzeroberfläche zum Durchsuchen und Testen die Web-API bereit.</span><span class="sxs-lookup"><span data-stu-id="237f4-109">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="237f4-110">Um [NSwag](https://github.com/RSuter/NSwag) mit ASP.NET Core-Middleware zu verwenden, installieren Sie das NuGet-Paket [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/).</span><span class="sxs-lookup"><span data-stu-id="237f4-110">To use the [NSwag](https://github.com/RSuter/NSwag) ASP.NET Core middlewares, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="237f4-111">Dieses Paket enthält die Middleware zum Generieren und Bereitstellen der Swagger-Spezifikation, der Swagger-Benutzeroberfläche (v2 und v3) und der [ReDoc-Benutzeroberfläche](https://github.com/Rebilly/ReDoc).</span><span class="sxs-lookup"><span data-stu-id="237f4-111">This package contains the middlewares to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="237f4-112">Außerdem wird dringend empfohlen, die Funktionen zur Codegenerierung von NSwag zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="237f4-112">Additionally, it's highly recommended to make use of NSwag's code generation capabilities.</span></span> <span data-ttu-id="237f4-113">Wählen Sie eine der folgenden Optionen aus, um die Codegenerierungsfunktionen zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="237f4-113">Choose one of the following options to use the code generation capabilities:</span></span>

* <span data-ttu-id="237f4-114">Verwenden Sie [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio). Dies ist eine Windows-Desktop-App zum Generieren von Clientcode in C# und TypeScript für Ihre API.</span><span class="sxs-lookup"><span data-stu-id="237f4-114">Use [NSwagStudio](https://github.com/NSwag/NSwag/wiki/NSwagStudio), a Windows desktop app for generating client code in C# and TypeScript for your API.</span></span>
* <span data-ttu-id="237f4-115">Verwenden Sie die NuGet-Pakete [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) oder [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/), um Code innerhalb des Projekts zu generieren.</span><span class="sxs-lookup"><span data-stu-id="237f4-115">Use the [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages to do code generation inside your project.</span></span>
* <span data-ttu-id="237f4-116">Verwenden Sie NSwag über die [Befehlszeile](https://github.com/NSwag/NSwag/wiki/CommandLine).</span><span class="sxs-lookup"><span data-stu-id="237f4-116">Use NSwag from the [command line](https://github.com/NSwag/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="237f4-117">Verwenden Sie das NuGet-Paket [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild).</span><span class="sxs-lookup"><span data-stu-id="237f4-117">Use the [NSwag.MSBuild](https://github.com/NSwag/NSwag/wiki/MSBuild) NuGet package.</span></span>

## <a name="features"></a><span data-ttu-id="237f4-118">Features</span><span class="sxs-lookup"><span data-stu-id="237f4-118">Features</span></span>

<span data-ttu-id="237f4-119">Der Hauptgrund für die Verwendung von NSwag liegt darin, dass nicht nur die Swagger-Benutzeroberfläche und der Swagger-Generator, sondern auch die flexiblen Funktionen für die Codegenerierung verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="237f4-119">The main reason to use NSwag is the ability to not only introduce the Swagger UI and Swagger generator, but to also make use of the flexible code generation capabilities.</span></span> <span data-ttu-id="237f4-120">Sie müssen keine API besitzen, sondern können APIs von Drittanbietern verwenden, die Swagger integrieren, und NSwag eine Clientimplementierung generieren lassen.</span><span class="sxs-lookup"><span data-stu-id="237f4-120">You don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and let NSwag generate a client implementation.</span></span> <span data-ttu-id="237f4-121">In jedem Fall wird der Entwicklungszyklus beschleunigt und Sie können sich schnell an API-Änderungen anpassen.</span><span class="sxs-lookup"><span data-stu-id="237f4-121">Either way, the development cycle is expedited and you can more easily adapt to API changes.</span></span>

## <a name="package-installation"></a><span data-ttu-id="237f4-122">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="237f4-122">Package installation</span></span>

<span data-ttu-id="237f4-123">Das NuGet-Paket „NSwag“ kann folgendermaßen hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="237f4-123">The NSwag NuGet package can be added with the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="237f4-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="237f4-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="237f4-125">Aus dem Fenster **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="237f4-125">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="237f4-126">Navigieren Sie zu **Ansicht** > **Weitere Fenster** > **Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="237f4-126">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="237f4-127">Navigieren Sie zu dem Verzeichnis, in dem die *TodoApi.csproj*-Datei gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="237f4-127">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="237f4-128">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="237f4-128">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="237f4-129">Aus dem Dialogfeld **NuGet-Pakete verwalten**:</span><span class="sxs-lookup"><span data-stu-id="237f4-129">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="237f4-130">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="237f4-130">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="237f4-131">Legen Sie die **Paketquelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="237f4-131">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="237f4-132">Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="237f4-132">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="237f4-133">Wählen Sie das Paket „NSwag.AspNetCore“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="237f4-133">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="237f4-134">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="237f4-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="237f4-135">Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.</span><span class="sxs-lookup"><span data-stu-id="237f4-135">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="237f4-136">Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="237f4-136">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="237f4-137">Geben Sie „NSwag.AspNetCore“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="237f4-137">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="237f4-138">Wählen Sie das Paket „NSwag.AspNetCore“ aus dem Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="237f4-138">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="237f4-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="237f4-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="237f4-140">Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="237f4-140">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="237f4-141">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="237f4-141">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="237f4-142">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="237f4-142">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="237f4-143">Hinzufügen und Konfigurieren von Swagger-Middleware</span><span class="sxs-lookup"><span data-stu-id="237f4-143">Add and configure Swagger middleware</span></span>

<span data-ttu-id="237f4-144">Importieren Sie folgende Namespaces in die `Startup`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="237f4-144">Import the following namespaces in the `Startup` class:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_StartupConfigureImports)]

<span data-ttu-id="237f4-145">Registrieren Sie in der `Startup.ConfigureServices`-Methode die erforderlichen Swagger-Dienste:</span><span class="sxs-lookup"><span data-stu-id="237f4-145">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span> 

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="237f4-146">Aktivieren Sie die Middleware in der `Startup.Configure`-Methode, um die generierte Swagger-Spezifikation und die Swagger-Benutzeroberfläche (v3) bereitzustellen:</span><span class="sxs-lookup"><span data-stu-id="237f4-146">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI v3:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-10)]

<span data-ttu-id="237f4-147">Starten Sie die App.</span><span class="sxs-lookup"><span data-stu-id="237f4-147">Launch the app.</span></span> <span data-ttu-id="237f4-148">Navigieren Sie zu `http://localhost:<port>/swagger`, um die Swagger-Benutzeroberfläche anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="237f4-148">Navigate to `http://localhost:<port>/swagger` to view the Swagger UI.</span></span> <span data-ttu-id="237f4-149">Navigieren Sie zu `http://localhost:<port>/swagger/v1/swagger.json`, um die Swagger-Spezifikation anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="237f4-149">Navigate to `http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="237f4-150">Codeerzeugung</span><span class="sxs-lookup"><span data-stu-id="237f4-150">Code generation</span></span>

### <a name="via-nswagstudio"></a><span data-ttu-id="237f4-151">Über NSwagStudio</span><span class="sxs-lookup"><span data-stu-id="237f4-151">Via NSwagStudio</span></span>

* <span data-ttu-id="237f4-152">Installieren Sie NSwagStudio über das offizielle [GitHub-Repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span><span class="sxs-lookup"><span data-stu-id="237f4-152">Install NSwagStudio from the official [GitHub repository](https://github.com/RSuter/NSwag/wiki/NSwagStudio).</span></span>
* <span data-ttu-id="237f4-153">Starten Sie NSwagStudio.</span><span class="sxs-lookup"><span data-stu-id="237f4-153">Launch NSwagStudio.</span></span> <span data-ttu-id="237f4-154">Geben Sie die Datei-URL *swagger.json* in das Textfeld **Swagger Specification URL** (URL zur Spezifizierung des Swaggers) ein, und klicken Sie auf die Schaltfläche **Create local Copy** (Lokale Kopie erstellen).</span><span class="sxs-lookup"><span data-stu-id="237f4-154">Enter the *swagger.json* file URL in the **Swagger Specification URL** textbox, and click the **Create local Copy** button.</span></span>
* <span data-ttu-id="237f4-155">Klicken Sie auf den Clientausgabetyp **CSharp Client**.</span><span class="sxs-lookup"><span data-stu-id="237f4-155">Select the **CSharp Client** client output type.</span></span> <span data-ttu-id="237f4-156">Zu den Optionen zählen außerdem der **TypeScript-Client** und der **CSharp-Web-API-Controller**.</span><span class="sxs-lookup"><span data-stu-id="237f4-156">Other options include **TypeScript Client** and **CSharp Web API Controller**.</span></span> <span data-ttu-id="237f4-157">Bei der Verwendung eines Web-API-Controllers handelt es sich im Grunde um eine umgekehrte Generierung.</span><span class="sxs-lookup"><span data-stu-id="237f4-157">Using a Web API Controller is basically a reverse generation.</span></span> <span data-ttu-id="237f4-158">Dieser verwendet die Spezifikation eines Diensts, um den Dienst erneut zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="237f4-158">It uses a specification of a service to rebuild the service.</span></span>
* <span data-ttu-id="237f4-159">Klicken Sie auf die Schaltfläche **Ausgaben generieren**.</span><span class="sxs-lookup"><span data-stu-id="237f4-159">Click the **Generate Outputs** button.</span></span> <span data-ttu-id="237f4-160">Dann wird eine vollständige Clientimplementierung des *TodoApi.NSwag*-Projekts in C# durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="237f4-160">A complete C# client implementation of the *TodoApi.NSwag* project is produced.</span></span> <span data-ttu-id="237f4-161">Klicken Sie auf die Registerkarte **CSharp-Client** im Abschnitt **Ausgaben**, damit der generierte Clientcode angezeigt wird:</span><span class="sxs-lookup"><span data-stu-id="237f4-161">Click the **CSharp Client** tab of the **Outputs** section to see the generated client code:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable // Disable all warnings

    [System.CodeDom.Compiler.GeneratedCode("NSwag",
        "11.17.3.0 (NJsonSchema v9.10.46.0 (Newtonsoft.Json v9.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "http://localhost:50499";
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient()
        {
            _settings = new System.Lazy
                <Newtonsoft.Json.JsonSerializerSettings>(() =>
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
> <span data-ttu-id="237f4-162">Der C#-Clientcode wird basierend auf den Einstellungen generiert, die über die Registerkarte **Einstellungen** der Registerkarte **CSharp-Client** definiert werden. Ändern Sie die Einstellungen, um Aufgaben wie das Umbenennen von Standardnamespaces und die Generierung von synchronen Methoden auszuführen.</span><span class="sxs-lookup"><span data-stu-id="237f4-162">The C# client code is generated based on settings defined in the **Settings** tab of the **CSharp Client** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="237f4-163">Kopieren Sie den generierten C#_code in eine Datei in einem Clientprojekt (z.B. zu einer [Xamarin.Forms](/xamarin/xamarin-forms/)-App).</span><span class="sxs-lookup"><span data-stu-id="237f4-163">Copy the generated C# code into a file in a client project (for example, a [Xamarin.Forms](/xamarin/xamarin-forms/) app).</span></span>
* <span data-ttu-id="237f4-164">Verwendung der Web-API:</span><span class="sxs-lookup"><span data-stu-id="237f4-164">Start consuming the web API:</span></span>

```csharp
var todoClient = new TodoClient();

// Gets all to-dos from the API
var allTodos = await todoClient.GetAllAsync();

// Create a new TodoItem, and save it in the API
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

> [!NOTE]
> <span data-ttu-id="237f4-165">Sie können eine Basis-URL und/oder einen HTTP-Client in den API-Client einfügen.</span><span class="sxs-lookup"><span data-stu-id="237f4-165">You can inject a base URL and/or a HTTP client into the API client.</span></span> <span data-ttu-id="237f4-166">Eine bewährte Methode besteht darin, [HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/) immer wieder zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="237f4-166">The best practice is to always [reuse the HttpClient](https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/).</span></span>

### <a name="other-ways-to-generate-client-code"></a><span data-ttu-id="237f4-167">Weitere Möglichkeiten zum Generieren von Clientcode</span><span class="sxs-lookup"><span data-stu-id="237f4-167">Other ways to generate client code</span></span>

<span data-ttu-id="237f4-168">Sie können den Clientcode auf andere Arten generieren, die besser zu Ihrem Workflow passen:</span><span class="sxs-lookup"><span data-stu-id="237f4-168">You can generate the client code in other ways, more suited to your workflow:</span></span>

* [<span data-ttu-id="237f4-169">MSBuild</span><span class="sxs-lookup"><span data-stu-id="237f4-169">MSBuild</span></span>](https://www.nuget.org/packages/NSwag.MSBuild/)

* [<span data-ttu-id="237f4-170">Im Code</span><span class="sxs-lookup"><span data-stu-id="237f4-170">In code</span></span>](https://github.com/NSwag/NSwag/wiki/SwaggerToCSharpClientGenerator)

* [<span data-ttu-id="237f4-171">T4-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="237f4-171">T4 templates</span></span>](https://github.com/NSwag/NSwag/wiki/T4)

## <a name="customize"></a><span data-ttu-id="237f4-172">Anpassen</span><span class="sxs-lookup"><span data-stu-id="237f4-172">Customize</span></span>

<span data-ttu-id="237f4-173">Swagger umfasst Optionen zum Dokumentieren des Objektmodells, um die Verwendung der Web-API zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="237f4-173">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="237f4-174">API-Informationen und -Beschreibung</span><span class="sxs-lookup"><span data-stu-id="237f4-174">API info and description</span></span>

<span data-ttu-id="237f4-175">In der `Startup.Configure`-Methode werden Informationen wie Autor, Lizenz und Beschreibung durch die Konfigurationsaktion hinzugefügt, die an die `UseSwagger`-Methode übergeben wird:</span><span class="sxs-lookup"><span data-stu-id="237f4-175">In the `Startup.Configure` method, a configuration action passed to the `UseSwagger` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_UseSwagger)]

<span data-ttu-id="237f4-176">Auf der Swagger-Benutzeroberfläche werden die Versionsinformationen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="237f4-176">The Swagger UI displays the version's information:</span></span>

![Swagger-Benutzeroberfläche mit Versionsinformationen](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="237f4-178">XML-Kommentare</span><span class="sxs-lookup"><span data-stu-id="237f4-178">XML comments</span></span>

<span data-ttu-id="237f4-179">XML-Kommentare werden mithilfe von folgenden Ansätzen aktiviert:</span><span class="sxs-lookup"><span data-stu-id="237f4-179">XML comments are enabled with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio-xml"></a>[<span data-ttu-id="237f4-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="237f4-180">Visual Studio</span></span>](#tab/visual-studio-xml/)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="237f4-181">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **<project_name>.csproj bearbeiten** aus.</span><span class="sxs-lookup"><span data-stu-id="237f4-181">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="237f4-182">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="237f4-182">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="237f4-183">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="237f4-183">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="237f4-184">Überprüfen Sie das Feld **XML-Dokumentationsdatei** im Abschnitt **Ausgabe** der Registerkarte **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="237f4-184">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mactabvisual-studio-mac-xml"></a>[<span data-ttu-id="237f4-185">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="237f4-185">Visual Studio for Mac</span></span>](#tab/visual-studio-mac-xml/)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="237f4-186">Drücken und halten Sie im *Lösungspad* die **CONTROL**-Taste, und klicken Sie auf den Projektnamen.</span><span class="sxs-lookup"><span data-stu-id="237f4-186">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="237f4-187">Navigieren Sie zu **Extras** > **Datei bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="237f4-187">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="237f4-188">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="237f4-188">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="237f4-189">Öffnen Sie das Dialogfeld **Projektoptionen** > **Erstellen** > **Compiler**.</span><span class="sxs-lookup"><span data-stu-id="237f4-189">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="237f4-190">Überprüfen Sie das Feld **XML-Dokumentation generieren** im Abschnitt **Allgemeine Optionen**.</span><span class="sxs-lookup"><span data-stu-id="237f4-190">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="visual-studio-codetabvisual-studio-code-xml"></a>[<span data-ttu-id="237f4-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="237f4-191">Visual Studio Code</span></span>](#tab/visual-studio-code-xml/)

<span data-ttu-id="237f4-192">Fügen Sie die hervorgehobenen Zeilen manuell der *CSPROJ*-Datei hinzu:</span><span class="sxs-lookup"><span data-stu-id="237f4-192">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="237f4-193">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="237f4-193">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="237f4-194">NSwag verwendet die [Reflektion](/dotnet/csharp/programming-guide/concepts/reflection), und der für Web-API-Aktionen empfohlene Rückgabetyp lautet [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult).</span><span class="sxs-lookup"><span data-stu-id="237f4-194">NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult).</span></span> <span data-ttu-id="237f4-195">Folglich kann NSwag nicht ableiten, was die Aktion ausführt und was sie zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="237f4-195">Consequently, NSwag can't infer what your action is doing and what it returns.</span></span> <span data-ttu-id="237f4-196">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="237f4-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="237f4-197">Die vorherige Aktion gibt `IActionResult` zurück. Innerhalb der Aktion gibt sie jedoch [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) oder [BadRequest](/dotnet/api/system.web.http.apicontroller.badrequest) zurück.</span><span class="sxs-lookup"><span data-stu-id="237f4-197">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](/dotnet/api/system.web.http.apicontroller.createdatroute) or [BadRequest](/dotnet/api/system.web.http.apicontroller.badrequest).</span></span> <span data-ttu-id="237f4-198">Datenanmerkungen werden verwendet, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="237f4-198">Data annotations are used to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="237f4-199">Ergänzen Sie die Aktion mit folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="237f4-199">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="237f4-200">NSwag verwendet [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), und der empfohlene Rückgabetyp für Web-API-Aktionen ist [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1).</span><span class="sxs-lookup"><span data-stu-id="237f4-200">NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1).</span></span> <span data-ttu-id="237f4-201">D.h., NSwag kann nur den von `T` definierten Rückgabetypen ableiten.</span><span class="sxs-lookup"><span data-stu-id="237f4-201">Consequently, NSwag can only infer the return type defined by `T`.</span></span> <span data-ttu-id="237f4-202">Es können keine weiteren Rückgabetypen in dieser Aktion abgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="237f4-202">Other possible return types in the action cannot be inferred.</span></span> <span data-ttu-id="237f4-203">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="237f4-203">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="237f4-204">Die vorherige Aktion gibt `ActionResult<T>` zurück.</span><span class="sxs-lookup"><span data-stu-id="237f4-204">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="237f4-205">In der Aktion gibt sie [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) zurück.</span><span class="sxs-lookup"><span data-stu-id="237f4-205">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="237f4-206">Da der Controller mit dem [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)-Attribut ausgestattet ist, ist auch eine [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)-Antwort möglich.</span><span class="sxs-lookup"><span data-stu-id="237f4-206">Since the controller is decorated with the [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="237f4-207">Weitere Informationen finden Sie unter [Automatische HTTP 400-Antworten](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="237f4-207">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="237f4-208">Datenanmerkungen werden verwendet, um Clients mitzuteilen, welche HTTP-Statuscodes diese Aktion in der Regel zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="237f4-208">Data annotations are used to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="237f4-209">Ergänzen Sie die Aktion mit folgenden Attributen:</span><span class="sxs-lookup"><span data-stu-id="237f4-209">Decorate the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="237f4-210">In ASP.NET Core 2.2 oder höher können Konventionen als Alternative verwendet werden, um einzelne Aktionen explizit mit `[ProducesResponseType]` zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="237f4-210">In ASP.NET Core 2.2 or later, conventions can be used as an alternative to explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="237f4-211">Weitere Informationen finden Sie unter <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="237f4-211">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="237f4-212">Der Swagger-Generator kann diese Aktion nun genau beschreiben, und generierte Clients wissen, was sie beim Aufrufen des Endpunkts empfangen.</span><span class="sxs-lookup"><span data-stu-id="237f4-212">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="237f4-213">Es wird empfohlen, alle Aktionen mit diesen Attributen zu ergänzen.</span><span class="sxs-lookup"><span data-stu-id="237f4-213">Decorating all actions with these attributes is highly recommended.</span></span> <span data-ttu-id="237f4-214">Weitere Informationen zu den Richtlinien dazu, welche HTTP-Antworten Ihre API-Aktionen zurückgeben sollten, finden Sie in der [RFC 7231 specification (Spezifikation von RFC 7231)](https://tools.ietf.org/html/rfc7231#section-4.3).</span><span class="sxs-lookup"><span data-stu-id="237f4-214">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
