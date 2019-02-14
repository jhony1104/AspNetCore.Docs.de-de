---
title: Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack
author: ssougnez
description: In diesem Tutorial konfigurieren Sie Webpack zum Bündeln und Erstellen einer ASP.NET Core SignalR-Web-App, deren Client in TypeScript geschrieben ist.
ms.author: bradyg
ms.custom: mvc
ms.date: 02/11/2019
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: aaf9aa59928ed6b17bc0586d97dbdefc9e30362c
ms.sourcegitcommit: 5e3797a02ff3c48bb8cb9ad4320bfd169ebe8aba
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56102951"
---
# <a name="use-aspnet-core-signalr-with-typescript-and-webpack"></a><span data-ttu-id="4470e-103">Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack</span><span class="sxs-lookup"><span data-stu-id="4470e-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="4470e-104">Von [Sébastien Sougnez](https://twitter.com/ssougnez) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="4470e-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="4470e-105">Mit [Webpack](https://webpack.js.org/) können Entwickler clientseitige Ressourcen einer Web-App bündeln und erstellen.</span><span class="sxs-lookup"><span data-stu-id="4470e-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="4470e-106">In diesem Tutorial wird veranschaulicht, wie Webpack in einer ASP.NET Core SignalR-Web-App verwendet wird, deren Client in [TypeScript](https://www.typescriptlang.org/) geschrieben ist.</span><span class="sxs-lookup"><span data-stu-id="4470e-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="4470e-107">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="4470e-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4470e-108">Aufbauen einer einfachen ASP.NET Core SignalR-App</span><span class="sxs-lookup"><span data-stu-id="4470e-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="4470e-109">Konfigurieren des SignalR-TypeScript-Clients</span><span class="sxs-lookup"><span data-stu-id="4470e-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="4470e-110">Konfigurieren einer Buildpipeline mithilfe von Webpack</span><span class="sxs-lookup"><span data-stu-id="4470e-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="4470e-111">Konfigurieren des SignalR-Servers</span><span class="sxs-lookup"><span data-stu-id="4470e-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="4470e-112">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="4470e-112">Enable communication between client and server</span></span>

<span data-ttu-id="4470e-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="4470e-113">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

[!INCLUDE [Prerequisites](~/includes/net-core-prereqs-vs-vsc-2.2.md)]

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="4470e-114">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="4470e-114">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4470e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4470e-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4470e-116">Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird.</span><span class="sxs-lookup"><span data-stu-id="4470e-116">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="4470e-117">Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="4470e-117">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="4470e-118">Führen Sie die folgenden Anweisungen in Visual Studio aus:</span><span class="sxs-lookup"><span data-stu-id="4470e-118">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="4470e-119">Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.</span><span class="sxs-lookup"><span data-stu-id="4470e-119">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="4470e-120">Wählen Sie in der Liste den Eintrag *$(PATH)* aus.</span><span class="sxs-lookup"><span data-stu-id="4470e-120">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="4470e-121">Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="4470e-121">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="4470e-123">Die Konfiguration von Visual Studio ist abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="4470e-123">Visual Studio configuration is completed.</span></span> <span data-ttu-id="4470e-124">Jetzt ist es an der Zeit, das Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="4470e-124">It's time to create the project.</span></span>

1. <span data-ttu-id="4470e-125">Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt**, und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="4470e-125">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="4470e-126">Benennen Sie das Projekt *SignalRWebPack*, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="4470e-126">Name the project *SignalRWebPack*, and select **OK**.</span></span>
1. <span data-ttu-id="4470e-127">Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 2.2* aus.</span><span class="sxs-lookup"><span data-stu-id="4470e-127">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="4470e-128">Wählen Sie die **leere** Vorlage aus, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="4470e-128">Select the **Empty** template, and select **OK**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4470e-129">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4470e-129">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="4470e-130">Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="4470e-130">Run the following command in the **Integrated Terminal**:</span></span>

```console
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="4470e-131">Eine leere ASP.NET Core-Web-App für .NET Core wird in einem *SignalRWebPack*-Verzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="4470e-131">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="4470e-132">Konfigurieren von Webpack und TypeScript</span><span class="sxs-lookup"><span data-stu-id="4470e-132">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="4470e-133">In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="4470e-133">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="4470e-134">Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json*-Datei zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="4470e-134">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="4470e-135">Fügen Sie der *package.json*-Datei die hervorgehobene Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="4470e-135">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/snippets/package1.json?highlight=4)]

    <span data-ttu-id="4470e-136">Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.</span><span class="sxs-lookup"><span data-stu-id="4470e-136">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="4470e-137">Installieren Sie die erforderlichen NPM-Pakete.</span><span class="sxs-lookup"><span data-stu-id="4470e-137">Install the required npm packages.</span></span> <span data-ttu-id="4470e-138">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="4470e-138">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="4470e-139">Beachten Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="4470e-139">Some command details to note:</span></span>

    * <span data-ttu-id="4470e-140">Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer.</span><span class="sxs-lookup"><span data-stu-id="4470e-140">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="4470e-141">Die spezifischen Paketversionen werden von NPM installiert.</span><span class="sxs-lookup"><span data-stu-id="4470e-141">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="4470e-142">Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json*-Datei schreibt.</span><span class="sxs-lookup"><span data-stu-id="4470e-142">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="4470e-143">Beispielsweise wird `"webpack": "4.29.3"` anstelle von `"webpack": "^4.29.3"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="4470e-143">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="4470e-144">Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.</span><span class="sxs-lookup"><span data-stu-id="4470e-144">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="4470e-145">Ausführliche Informationen finden Sie in der offiziellen Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="4470e-145">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="4470e-146">Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Codeausschnitt:</span><span class="sxs-lookup"><span data-stu-id="4470e-146">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="4470e-147">Im Folgenden werden die Skripts erklärt:</span><span class="sxs-lookup"><span data-stu-id="4470e-147">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="4470e-148">`build`: Bündelt Ihre clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien.</span><span class="sxs-lookup"><span data-stu-id="4470e-148">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="4470e-149">Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="4470e-149">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="4470e-150">Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“.</span><span class="sxs-lookup"><span data-stu-id="4470e-150">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="4470e-151">Verwenden Sie `build` nur in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="4470e-151">Only use `build` in development.</span></span>
    * <span data-ttu-id="4470e-152">`release`: Bündelt clientseitige Ressourcen im Produktionsmodus.</span><span class="sxs-lookup"><span data-stu-id="4470e-152">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="4470e-153">`publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="4470e-153">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="4470e-154">Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="4470e-154">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="4470e-155">Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="4470e-155">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/webpack.config.js)]

    <span data-ttu-id="4470e-156">Die vorangehende Datei konfiguriert die Webpack-Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="4470e-156">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="4470e-157">Zu beachtende Konfigurationsdetails:</span><span class="sxs-lookup"><span data-stu-id="4470e-157">Some configuration details to note:</span></span>

    * <span data-ttu-id="4470e-158">Die Eigenschaft `output` überschreibt den Standardwert von *dist*.</span><span class="sxs-lookup"><span data-stu-id="4470e-158">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="4470e-159">Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="4470e-159">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="4470e-160">Das `resolve.extensions`-Array enthält *.js*, um den JavaScript-Code des SignalR-Clients zu importieren.</span><span class="sxs-lookup"><span data-stu-id="4470e-160">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="4470e-161">Erstellen Sie ein neues *src*-Verzeichnis im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="4470e-161">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="4470e-162">Es dient als Speicherort für clientseitige Ressourcen des Projekts.</span><span class="sxs-lookup"><span data-stu-id="4470e-162">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="4470e-163">Erstellen Sie *src/index.html* mit dem folgenden Inhalt.</span><span class="sxs-lookup"><span data-stu-id="4470e-163">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/src/index.html)]

    <span data-ttu-id="4470e-164">Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.</span><span class="sxs-lookup"><span data-stu-id="4470e-164">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="4470e-165">Erstellen Sie ein neues *src/css*-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="4470e-165">Create a new *src/css* directory.</span></span> <span data-ttu-id="4470e-166">Es dient als Speicherort für die *CSS*-Dateien des Projekts.</span><span class="sxs-lookup"><span data-stu-id="4470e-166">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="4470e-167">Erstellen Sie *src/css/main.css* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="4470e-167">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/src/css/main.css)]

    <span data-ttu-id="4470e-168">Die vorangehende Datei *main.css* formatiert die App.</span><span class="sxs-lookup"><span data-stu-id="4470e-168">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="4470e-169">Erstellen Sie *src/tsconfig.json* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="4470e-169">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/src/tsconfig.json)]

    <span data-ttu-id="4470e-170">Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="4470e-170">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="4470e-171">Erstellen Sie *src/index.ts* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="4470e-171">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="4470e-172">Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:</span><span class="sxs-lookup"><span data-stu-id="4470e-172">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="4470e-173">`keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das Textfeld eingibt, das als `tbMessage` erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="4470e-173">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="4470e-174">Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.</span><span class="sxs-lookup"><span data-stu-id="4470e-174">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="4470e-175">`click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt.</span><span class="sxs-lookup"><span data-stu-id="4470e-175">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="4470e-176">Die Funktion `send` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="4470e-176">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="4470e-177">Konfigurieren einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="4470e-177">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="4470e-178">Der in der Methode `Startup.Configure` bereitgestellte Code zeigt *Hello World!* an.</span><span class="sxs-lookup"><span data-stu-id="4470e-178">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="4470e-179">Ersetzen Sie den Aufruf der Methode `app.Run` durch Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="4470e-179">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="4470e-180">Mit dem vorangehenden Code wird dem Server ermöglicht, die Datei *index.html* zu finden und bereitzustellen, unabhängig davon, ob der Benutzer die vollständige URL oder die Stamm-URL der Web-App eingibt.</span><span class="sxs-lookup"><span data-stu-id="4470e-180">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="4470e-181">Rufen Sie [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in der Methode `Startup.ConfigureServices` auf.</span><span class="sxs-lookup"><span data-stu-id="4470e-181">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4470e-182">Mit ihr werden die SignalR-Dienste in das Projekt eingefügt.</span><span class="sxs-lookup"><span data-stu-id="4470e-182">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="4470e-183">Ordnen Sie dem `ChatHub`-Hub eine */hub*-Route zu.</span><span class="sxs-lookup"><span data-stu-id="4470e-183">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="4470e-184">Fügen Sie die folgenden Zeilen am Ende der `Startup.Configure`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4470e-184">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="4470e-185">Erstellen Sie ein neues Verzeichnis namens *Hubs* am Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="4470e-185">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="4470e-186">Es dient als Speicherort des SignalR-Hubs, der im nächsten Schritt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="4470e-186">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="4470e-187">Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs*:</span><span class="sxs-lookup"><span data-stu-id="4470e-187">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="4470e-188">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="4470e-188">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="4470e-189">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="4470e-189">Enable client and server communication</span></span>

<span data-ttu-id="4470e-190">Derzeit zeigt die App ein einfaches Formular zum Senden von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="4470e-190">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="4470e-191">Es geschieht jedoch nichts, wenn Sie versuchen, es zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4470e-191">Nothing happens when you try to do so.</span></span> <span data-ttu-id="4470e-192">Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.</span><span class="sxs-lookup"><span data-stu-id="4470e-192">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="4470e-193">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="4470e-193">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="4470e-194">Der vorangehende Befehl installiert den [SignalR-TypeScript-Client](https://www.npmjs.com/package/@aspnet/signalr), mit dem der Client Nachrichten an den Server senden kann.</span><span class="sxs-lookup"><span data-stu-id="4470e-194">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@aspnet/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="4470e-195">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="4470e-195">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="4470e-196">Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server.</span><span class="sxs-lookup"><span data-stu-id="4470e-196">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="4470e-197">Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung.</span><span class="sxs-lookup"><span data-stu-id="4470e-197">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="4470e-198">Die Funktion `withUrl` konfiguriert die Hub-URL.</span><span class="sxs-lookup"><span data-stu-id="4470e-198">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="4470e-199">Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="4470e-199">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="4470e-200">Jede Nachricht verfügt über einen spezifischen Namen.</span><span class="sxs-lookup"><span data-stu-id="4470e-200">Each message has a specific name.</span></span> <span data-ttu-id="4470e-201">Sie können beispielsweise über Nachrichten mit dem Namen `messageReceived` verfügen, die die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen.</span><span class="sxs-lookup"><span data-stu-id="4470e-201">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="4470e-202">Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden.</span><span class="sxs-lookup"><span data-stu-id="4470e-202">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="4470e-203">Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen.</span><span class="sxs-lookup"><span data-stu-id="4470e-203">You can listen to any number of message names.</span></span> <span data-ttu-id="4470e-204">Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht.</span><span class="sxs-lookup"><span data-stu-id="4470e-204">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="4470e-205">Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt.</span><span class="sxs-lookup"><span data-stu-id="4470e-205">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="4470e-206">Es wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="4470e-206">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="4470e-207">Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="4470e-207">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="4470e-208">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="4470e-208">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/src/index.ts?highlight=34-35)]

    <span data-ttu-id="4470e-209">Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="4470e-209">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="4470e-210">Der erste Parameter der Methode ist der Name der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="4470e-210">The method's first parameter is the message name.</span></span> <span data-ttu-id="4470e-211">Die Nachrichtendaten befinden sich in den anderen Parametern.</span><span class="sxs-lookup"><span data-stu-id="4470e-211">The message data inhabits the other parameters.</span></span> <span data-ttu-id="4470e-212">In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="4470e-212">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="4470e-213">Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds.</span><span class="sxs-lookup"><span data-stu-id="4470e-213">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="4470e-214">Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.</span><span class="sxs-lookup"><span data-stu-id="4470e-214">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="4470e-215">Fügen Sie der `ChatHub`-Klasse die hervorgehobene Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="4470e-215">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="4470e-216">Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="4470e-216">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="4470e-217">Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="4470e-217">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="4470e-218">Eine Methode mit demselben Namen wie die Nachricht genügt.</span><span class="sxs-lookup"><span data-stu-id="4470e-218">A method named after the message name suffices.</span></span>

    <span data-ttu-id="4470e-219">In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="4470e-219">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="4470e-220">Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten.</span><span class="sxs-lookup"><span data-stu-id="4470e-220">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="4470e-221">Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="4470e-221">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="4470e-222">Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="4470e-222">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="4470e-223">Testen der App</span><span class="sxs-lookup"><span data-stu-id="4470e-223">Test the app</span></span>

<span data-ttu-id="4470e-224">Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="4470e-224">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="4470e-225">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4470e-225">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="4470e-226">Führen Sie Webpack im *Releasemodus* aus.</span><span class="sxs-lookup"><span data-stu-id="4470e-226">Run Webpack in *release* mode.</span></span> <span data-ttu-id="4470e-227">Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus.</span><span class="sxs-lookup"><span data-stu-id="4470e-227">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="4470e-228">Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="4470e-228">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="4470e-229">Klicken Sie auf **Debuggen** > **Starten ohne Debugging**, um die App in einem Browser zu starten, ohne den Debugger anzufügen.</span><span class="sxs-lookup"><span data-stu-id="4470e-229">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="4470e-230">Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4470e-230">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="4470e-231">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="4470e-231">Open another browser instance (any browser).</span></span> <span data-ttu-id="4470e-232">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="4470e-232">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4470e-233">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="4470e-233">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="4470e-234">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4470e-234">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="4470e-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="4470e-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="4470e-236">Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="4470e-236">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="4470e-237">Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="4470e-237">Build and run the app by executing the following command in the project root:</span></span>

    ```console
    dotnet run
    ```

    <span data-ttu-id="4470e-238">Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="4470e-238">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="4470e-239">Öffnen Sie `http://localhost:<port_number>` im Browser.</span><span class="sxs-lookup"><span data-stu-id="4470e-239">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="4470e-240">Die Datei *wwwroot/index.html* wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="4470e-240">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="4470e-241">Kopieren Sie die URL aus der Adressleiste.</span><span class="sxs-lookup"><span data-stu-id="4470e-241">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="4470e-242">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="4470e-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="4470e-243">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="4470e-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="4470e-244">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="4470e-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="4470e-245">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="4470e-245">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

## <a name="additional-resources"></a><span data-ttu-id="4470e-247">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="4470e-247">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
