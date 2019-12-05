---
title: Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack
author: ssougnez
description: In diesem Tutorial konfigurieren Sie Webpack zum Bündeln und Erstellen einer ASP.NET Core SignalR-Web-App, deren Client in TypeScript geschrieben ist.
ms.author: bradyg
ms.custom: mvc
ms.date: 11/21/2019
no-loc:
- SignalR
uid: tutorials/signalr-typescript-webpack
ms.openlocfilehash: a7c99c9e79647995886aec5b3a91584fd2f24451
ms.sourcegitcommit: 3e503ef510008e77be6dd82ee79213c9f7b97607
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74317487"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="8ea77-103">Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack</span><span class="sxs-lookup"><span data-stu-id="8ea77-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="8ea77-104">Von [Sébastien Sougnez](https://twitter.com/ssougnez) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="8ea77-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="8ea77-105">Mit [Webpack](https://webpack.js.org/) können Entwickler clientseitige Ressourcen einer Web-App bündeln und erstellen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="8ea77-106">In diesem Tutorial wird veranschaulicht, wie Webpack in einer ASP.NET Core SignalR-Web-App verwendet wird, deren Client in [TypeScript](https://www.typescriptlang.org/) geschrieben ist.</span><span class="sxs-lookup"><span data-stu-id="8ea77-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="8ea77-107">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="8ea77-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8ea77-108">Aufbauen einer einfachen ASP.NET Core SignalR-App</span><span class="sxs-lookup"><span data-stu-id="8ea77-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="8ea77-109">Konfigurieren des SignalR-TypeScript-Clients</span><span class="sxs-lookup"><span data-stu-id="8ea77-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="8ea77-110">Konfigurieren einer Buildpipeline mithilfe von Webpack</span><span class="sxs-lookup"><span data-stu-id="8ea77-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="8ea77-111">Konfigurieren des SignalR-Servers</span><span class="sxs-lookup"><span data-stu-id="8ea77-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="8ea77-112">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="8ea77-112">Enable communication between client and server</span></span>

<span data-ttu-id="8ea77-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8ea77-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="8ea77-114">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="8ea77-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ea77-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea77-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ea77-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="8ea77-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="8ea77-117">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="8ea77-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="8ea77-118">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="8ea77-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8ea77-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="8ea77-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="8ea77-121">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="8ea77-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="8ea77-122">C# für Visual Studio Code Version 1.17.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="8ea77-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="8ea77-123">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="8ea77-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="8ea77-124">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="8ea77-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ea77-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea77-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8ea77-126">Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="8ea77-127">Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="8ea77-128">Führen Sie die folgenden Anweisungen in Visual Studio aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="8ea77-129">Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.</span><span class="sxs-lookup"><span data-stu-id="8ea77-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="8ea77-130">Wählen Sie in der Liste den Eintrag *$(PATH)* aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="8ea77-131">Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="8ea77-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="8ea77-133">Die Konfiguration von Visual Studio ist abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="8ea77-134">Jetzt ist es an der Zeit, das Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-134">It's time to create the project.</span></span>

1. <span data-ttu-id="8ea77-135">Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt**, und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="8ea77-136">Benennen Sie das Projekt *SignalRWebPack*, und wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="8ea77-137">Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 3.0* aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="8ea77-138">Wählen Sie die **leere** Vorlage und dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8ea77-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8ea77-140">Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="8ea77-141">Eine leere ASP.NET Core-Web-App für .NET Core wird in einem *SignalRWebPack*-Verzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="8ea77-142">Konfigurieren von Webpack und TypeScript</span><span class="sxs-lookup"><span data-stu-id="8ea77-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="8ea77-143">In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="8ea77-144">Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json*-Datei zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="8ea77-145">Fügen Sie der *package.json*-Datei die hervorgehobene Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="8ea77-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="8ea77-146">Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="8ea77-147">Installieren Sie die erforderlichen NPM-Pakete.</span><span class="sxs-lookup"><span data-stu-id="8ea77-147">Install the required npm packages.</span></span> <span data-ttu-id="8ea77-148">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="8ea77-149">Beachten Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-149">Some command details to note:</span></span>

    * <span data-ttu-id="8ea77-150">Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer.</span><span class="sxs-lookup"><span data-stu-id="8ea77-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="8ea77-151">Die spezifischen Paketversionen werden von NPM installiert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="8ea77-152">Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json*-Datei schreibt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="8ea77-153">Beispielsweise wird `"webpack": "4.29.3"` anstelle von `"webpack": "^4.29.3"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="8ea77-154">Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="8ea77-155">Ausführliche Informationen finden Sie in der offiziellen Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="8ea77-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="8ea77-156">Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Codeausschnitt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="8ea77-157">Im Folgenden werden die Skripts erklärt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="8ea77-158">`build`: Bündelt Ihre clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien.</span><span class="sxs-lookup"><span data-stu-id="8ea77-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="8ea77-159">Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="8ea77-160">Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“.</span><span class="sxs-lookup"><span data-stu-id="8ea77-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="8ea77-161">Verwenden Sie `build` nur in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="8ea77-162">`release`: Bündelt clientseitige Ressourcen im Produktionsmodus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="8ea77-163">`publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="8ea77-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="8ea77-164">Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="8ea77-165">Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="8ea77-166">Die vorangehende Datei konfiguriert die Webpack-Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="8ea77-167">Zu beachtende Konfigurationsdetails:</span><span class="sxs-lookup"><span data-stu-id="8ea77-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="8ea77-168">Die Eigenschaft `output` überschreibt den Standardwert von *dist*.</span><span class="sxs-lookup"><span data-stu-id="8ea77-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="8ea77-169">Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="8ea77-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="8ea77-170">Das `resolve.extensions`-Array schließt *.js* ein, um den JavaScript-Code des SignalR-Clients zu importieren.</span><span class="sxs-lookup"><span data-stu-id="8ea77-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="8ea77-171">Erstellen Sie ein neues *src*-Verzeichnis im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="8ea77-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="8ea77-172">Es dient als Speicherort für clientseitige Ressourcen des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8ea77-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="8ea77-173">Erstellen Sie *src/index.html* mit dem folgenden Inhalt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="8ea77-174">Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.</span><span class="sxs-lookup"><span data-stu-id="8ea77-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="8ea77-175">Erstellen Sie ein neues *src/css*-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="8ea77-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="8ea77-176">Es dient als Speicherort für die *CSS*-Dateien des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8ea77-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="8ea77-177">Erstellen Sie *src/css/main.css* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="8ea77-178">Die vorangehende Datei *main.css* formatiert die App.</span><span class="sxs-lookup"><span data-stu-id="8ea77-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="8ea77-179">Erstellen Sie *src/tsconfig.json* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="8ea77-180">Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="8ea77-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="8ea77-181">Erstellen Sie *src/index.ts* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="8ea77-182">Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:</span><span class="sxs-lookup"><span data-stu-id="8ea77-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="8ea77-183">`keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das Textfeld eingibt, das als `tbMessage` erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="8ea77-184">Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="8ea77-185">`click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="8ea77-186">Die Funktion `send` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="8ea77-187">Konfigurieren einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="8ea77-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="8ea77-188">Fügen Sie der Methode `Startup.Configure` Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) hinzu.</span><span class="sxs-lookup"><span data-stu-id="8ea77-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="8ea77-189">Mit dem vorangehenden Code wird dem Server ermöglicht, die Datei *index.html* zu finden und bereitzustellen, unabhängig davon, ob der Benutzer die vollständige URL oder die Stamm-URL der Web-App eingibt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="8ea77-190">Ordnen Sie am Ende der `Startup.Configure`-Methode eine */hub*-Route dem `ChatHub`-Hub zu.</span><span class="sxs-lookup"><span data-stu-id="8ea77-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="8ea77-191">Ersetzen Sie den Code, der *Hello World!* anzeigt,</span><span class="sxs-lookup"><span data-stu-id="8ea77-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="8ea77-192">durch folgende Zeile:</span><span class="sxs-lookup"><span data-stu-id="8ea77-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="8ea77-193">Rufen Sie in der `Startup.ConfigureServices`-Methode [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) auf.</span><span class="sxs-lookup"><span data-stu-id="8ea77-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="8ea77-194">Mit ihr werden die SignalR-Dienste in das Projekt eingefügt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="8ea77-195">Erstellen Sie ein neues Verzeichnis namens *Hubs* am Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="8ea77-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="8ea77-196">Es dient als Speicherort des SignalR-Hubs, der im nächsten Schritt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="8ea77-197">Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs*:</span><span class="sxs-lookup"><span data-stu-id="8ea77-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="8ea77-198">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="8ea77-199">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="8ea77-199">Enable client and server communication</span></span>

<span data-ttu-id="8ea77-200">Derzeit zeigt die App ein einfaches Formular zum Senden von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="8ea77-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="8ea77-201">Es geschieht jedoch nichts, wenn Sie versuchen, es zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8ea77-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="8ea77-202">Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="8ea77-203">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="8ea77-204">Der vorangehende Befehl installiert den [SignalR-TypeScript-Client](https://www.npmjs.com/package/@microsoft/signalr), mit dem der Client Nachrichten an den Server senden kann.</span><span class="sxs-lookup"><span data-stu-id="8ea77-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="8ea77-205">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="8ea77-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="8ea77-206">Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server.</span><span class="sxs-lookup"><span data-stu-id="8ea77-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="8ea77-207">Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="8ea77-208">Die Funktion `withUrl` konfiguriert die Hub-URL.</span><span class="sxs-lookup"><span data-stu-id="8ea77-208">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="8ea77-209">Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-209">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="8ea77-210">Jede Nachricht verfügt über einen spezifischen Namen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-210">Each message has a specific name.</span></span> <span data-ttu-id="8ea77-211">Sie können beispielsweise über Nachrichten mit dem Namen `messageReceived` verfügen, die die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="8ea77-212">Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden.</span><span class="sxs-lookup"><span data-stu-id="8ea77-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="8ea77-213">Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-213">You can listen to any number of message names.</span></span> <span data-ttu-id="8ea77-214">Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="8ea77-215">Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="8ea77-216">Es wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="8ea77-217">Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="8ea77-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="8ea77-218">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="8ea77-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="8ea77-219">Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8ea77-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="8ea77-220">Der erste Parameter der Methode ist der Name der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="8ea77-221">Die Nachrichtendaten befinden sich in den anderen Parametern.</span><span class="sxs-lookup"><span data-stu-id="8ea77-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="8ea77-222">In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="8ea77-223">Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds.</span><span class="sxs-lookup"><span data-stu-id="8ea77-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="8ea77-224">Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="8ea77-225">Fügen Sie der `ChatHub`-Klasse die hervorgehobene Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="8ea77-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="8ea77-226">Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="8ea77-227">Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8ea77-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="8ea77-228">Eine Methode mit demselben Namen wie die Nachricht genügt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="8ea77-229">In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="8ea77-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="8ea77-230">Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten.</span><span class="sxs-lookup"><span data-stu-id="8ea77-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="8ea77-231">Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="8ea77-232">Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="8ea77-233">Testen der App</span><span class="sxs-lookup"><span data-stu-id="8ea77-233">Test the app</span></span>

<span data-ttu-id="8ea77-234">Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ea77-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea77-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8ea77-236">Führen Sie Webpack im *Releasemodus* aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="8ea77-237">Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="8ea77-238">Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="8ea77-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="8ea77-239">Klicken Sie auf **Debuggen** > **Starten ohne Debugging**, um die App in einem Browser zu starten, ohne den Debugger anzufügen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="8ea77-240">Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="8ea77-241">Wenn Sie Kompilierungsfehlermeldungen erhalten, versuchen Sie, die Projektmappe zu schließen und erneut zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="8ea77-242">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="8ea77-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="8ea77-243">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="8ea77-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ea77-244">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="8ea77-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="8ea77-245">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8ea77-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8ea77-247">Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="8ea77-248">Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="8ea77-249">Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="8ea77-250">Öffnen Sie `http://localhost:<port_number>` im Browser.</span><span class="sxs-lookup"><span data-stu-id="8ea77-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="8ea77-251">Die Datei *wwwroot/index.html* wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="8ea77-252">Kopieren Sie die URL aus der Adressleiste.</span><span class="sxs-lookup"><span data-stu-id="8ea77-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="8ea77-253">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="8ea77-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="8ea77-254">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="8ea77-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ea77-255">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="8ea77-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="8ea77-256">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ea77-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea77-258">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8ea77-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="8ea77-259">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="8ea77-260">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="8ea77-260">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="8ea77-261">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="8ea77-261">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8ea77-262">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-262">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="8ea77-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-263">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="8ea77-264">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="8ea77-264">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="8ea77-265">C# für Visual Studio Code Version 1.17.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="8ea77-265">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="8ea77-266">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="8ea77-266">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="8ea77-267">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="8ea77-267">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ea77-268">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea77-268">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8ea77-269">Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-269">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="8ea77-270">Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-270">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="8ea77-271">Führen Sie die folgenden Anweisungen in Visual Studio aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-271">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="8ea77-272">Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.</span><span class="sxs-lookup"><span data-stu-id="8ea77-272">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="8ea77-273">Wählen Sie in der Liste den Eintrag *$(PATH)* aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-273">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="8ea77-274">Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="8ea77-274">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="8ea77-276">Die Konfiguration von Visual Studio ist abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-276">Visual Studio configuration is completed.</span></span> <span data-ttu-id="8ea77-277">Jetzt ist es an der Zeit, das Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-277">It's time to create the project.</span></span>

1. <span data-ttu-id="8ea77-278">Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt**, und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-278">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="8ea77-279">Benennen Sie das Projekt *SignalRWebPack*, und wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-279">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="8ea77-280">Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 2.2* aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-280">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="8ea77-281">Wählen Sie die **leere** Vorlage und dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-281">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8ea77-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8ea77-283">Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-283">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="8ea77-284">Eine leere ASP.NET Core-Web-App für .NET Core wird in einem *SignalRWebPack*-Verzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-284">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="8ea77-285">Konfigurieren von Webpack und TypeScript</span><span class="sxs-lookup"><span data-stu-id="8ea77-285">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="8ea77-286">In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-286">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="8ea77-287">Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json*-Datei zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-287">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="8ea77-288">Fügen Sie der *package.json*-Datei die hervorgehobene Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="8ea77-288">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="8ea77-289">Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-289">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="8ea77-290">Installieren Sie die erforderlichen NPM-Pakete.</span><span class="sxs-lookup"><span data-stu-id="8ea77-290">Install the required npm packages.</span></span> <span data-ttu-id="8ea77-291">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-291">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="8ea77-292">Beachten Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-292">Some command details to note:</span></span>

    * <span data-ttu-id="8ea77-293">Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer.</span><span class="sxs-lookup"><span data-stu-id="8ea77-293">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="8ea77-294">Die spezifischen Paketversionen werden von NPM installiert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-294">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="8ea77-295">Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json*-Datei schreibt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-295">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="8ea77-296">Beispielsweise wird `"webpack": "4.29.3"` anstelle von `"webpack": "^4.29.3"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-296">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="8ea77-297">Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-297">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="8ea77-298">Ausführliche Informationen finden Sie in der offiziellen Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="8ea77-298">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="8ea77-299">Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Codeausschnitt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-299">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="8ea77-300">Im Folgenden werden die Skripts erklärt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-300">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="8ea77-301">`build`: Bündelt Ihre clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien.</span><span class="sxs-lookup"><span data-stu-id="8ea77-301">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="8ea77-302">Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-302">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="8ea77-303">Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“.</span><span class="sxs-lookup"><span data-stu-id="8ea77-303">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="8ea77-304">Verwenden Sie `build` nur in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-304">Only use `build` in development.</span></span>
    * <span data-ttu-id="8ea77-305">`release`: Bündelt clientseitige Ressourcen im Produktionsmodus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-305">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="8ea77-306">`publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="8ea77-306">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="8ea77-307">Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-307">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="8ea77-308">Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-308">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="8ea77-309">Die vorangehende Datei konfiguriert die Webpack-Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-309">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="8ea77-310">Zu beachtende Konfigurationsdetails:</span><span class="sxs-lookup"><span data-stu-id="8ea77-310">Some configuration details to note:</span></span>

    * <span data-ttu-id="8ea77-311">Die Eigenschaft `output` überschreibt den Standardwert von *dist*.</span><span class="sxs-lookup"><span data-stu-id="8ea77-311">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="8ea77-312">Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="8ea77-312">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="8ea77-313">Das `resolve.extensions`-Array schließt *.js* ein, um den JavaScript-Code des SignalR-Clients zu importieren.</span><span class="sxs-lookup"><span data-stu-id="8ea77-313">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="8ea77-314">Erstellen Sie ein neues *src*-Verzeichnis im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="8ea77-314">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="8ea77-315">Es dient als Speicherort für clientseitige Ressourcen des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8ea77-315">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="8ea77-316">Erstellen Sie *src/index.html* mit dem folgenden Inhalt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-316">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="8ea77-317">Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.</span><span class="sxs-lookup"><span data-stu-id="8ea77-317">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="8ea77-318">Erstellen Sie ein neues *src/css*-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="8ea77-318">Create a new *src/css* directory.</span></span> <span data-ttu-id="8ea77-319">Es dient als Speicherort für die *CSS*-Dateien des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8ea77-319">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="8ea77-320">Erstellen Sie *src/css/main.css* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-320">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="8ea77-321">Die vorangehende Datei *main.css* formatiert die App.</span><span class="sxs-lookup"><span data-stu-id="8ea77-321">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="8ea77-322">Erstellen Sie *src/tsconfig.json* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-322">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="8ea77-323">Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="8ea77-323">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="8ea77-324">Erstellen Sie *src/index.ts* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="8ea77-324">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="8ea77-325">Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:</span><span class="sxs-lookup"><span data-stu-id="8ea77-325">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="8ea77-326">`keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das Textfeld eingibt, das als `tbMessage` erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-326">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="8ea77-327">Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-327">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="8ea77-328">`click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-328">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="8ea77-329">Die Funktion `send` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-329">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="8ea77-330">Konfigurieren einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="8ea77-330">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="8ea77-331">Der in der Methode `Startup.Configure` bereitgestellte Code zeigt *Hello World!* an.</span><span class="sxs-lookup"><span data-stu-id="8ea77-331">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="8ea77-332">Ersetzen Sie den Aufruf der Methode `app.Run` durch Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="8ea77-332">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="8ea77-333">Mit dem vorangehenden Code wird dem Server ermöglicht, die Datei *index.html* zu finden und bereitzustellen, unabhängig davon, ob der Benutzer die vollständige URL oder die Stamm-URL der Web-App eingibt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-333">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="8ea77-334">Rufen Sie [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in der Methode `Startup.ConfigureServices` auf.</span><span class="sxs-lookup"><span data-stu-id="8ea77-334">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="8ea77-335">Mit ihr werden die SignalR-Dienste in das Projekt eingefügt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-335">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="8ea77-336">Ordnen Sie dem `ChatHub`-Hub eine */hub*-Route zu.</span><span class="sxs-lookup"><span data-stu-id="8ea77-336">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="8ea77-337">Fügen Sie die folgenden Zeilen am Ende der `Startup.Configure`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="8ea77-337">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="8ea77-338">Erstellen Sie ein neues Verzeichnis namens *Hubs* am Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="8ea77-338">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="8ea77-339">Es dient als Speicherort des SignalR-Hubs, der im nächsten Schritt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="8ea77-339">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="8ea77-340">Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs*:</span><span class="sxs-lookup"><span data-stu-id="8ea77-340">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="8ea77-341">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-341">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="8ea77-342">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="8ea77-342">Enable client and server communication</span></span>

<span data-ttu-id="8ea77-343">Derzeit zeigt die App ein einfaches Formular zum Senden von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="8ea77-343">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="8ea77-344">Es geschieht jedoch nichts, wenn Sie versuchen, es zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8ea77-344">Nothing happens when you try to do so.</span></span> <span data-ttu-id="8ea77-345">Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-345">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="8ea77-346">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="8ea77-346">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="8ea77-347">Der vorangehende Befehl installiert den [SignalR-TypeScript-Client](https://www.npmjs.com/package/@microsoft/signalr), mit dem der Client Nachrichten an den Server senden kann.</span><span class="sxs-lookup"><span data-stu-id="8ea77-347">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="8ea77-348">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="8ea77-348">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="8ea77-349">Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server.</span><span class="sxs-lookup"><span data-stu-id="8ea77-349">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="8ea77-350">Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-350">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="8ea77-351">Die Funktion `withUrl` konfiguriert die Hub-URL.</span><span class="sxs-lookup"><span data-stu-id="8ea77-351">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="8ea77-352">Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-352">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="8ea77-353">Jede Nachricht verfügt über einen spezifischen Namen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-353">Each message has a specific name.</span></span> <span data-ttu-id="8ea77-354">Sie können beispielsweise über Nachrichten mit dem Namen `messageReceived` verfügen, die die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-354">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="8ea77-355">Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden.</span><span class="sxs-lookup"><span data-stu-id="8ea77-355">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="8ea77-356">Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-356">You can listen to any number of message names.</span></span> <span data-ttu-id="8ea77-357">Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-357">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="8ea77-358">Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-358">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="8ea77-359">Es wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-359">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="8ea77-360">Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="8ea77-360">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="8ea77-361">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="8ea77-361">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="8ea77-362">Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8ea77-362">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="8ea77-363">Der erste Parameter der Methode ist der Name der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-363">The method's first parameter is the message name.</span></span> <span data-ttu-id="8ea77-364">Die Nachrichtendaten befinden sich in den anderen Parametern.</span><span class="sxs-lookup"><span data-stu-id="8ea77-364">The message data inhabits the other parameters.</span></span> <span data-ttu-id="8ea77-365">In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-365">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="8ea77-366">Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds.</span><span class="sxs-lookup"><span data-stu-id="8ea77-366">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="8ea77-367">Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.</span><span class="sxs-lookup"><span data-stu-id="8ea77-367">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="8ea77-368">Fügen Sie der `ChatHub`-Klasse die hervorgehobene Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="8ea77-368">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="8ea77-369">Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-369">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="8ea77-370">Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="8ea77-370">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="8ea77-371">Eine Methode mit demselben Namen wie die Nachricht genügt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-371">A method named after the message name suffices.</span></span>

    <span data-ttu-id="8ea77-372">In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="8ea77-372">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="8ea77-373">Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten.</span><span class="sxs-lookup"><span data-stu-id="8ea77-373">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="8ea77-374">Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-374">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="8ea77-375">Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="8ea77-375">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="8ea77-376">Testen der App</span><span class="sxs-lookup"><span data-stu-id="8ea77-376">Test the app</span></span>

<span data-ttu-id="8ea77-377">Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="8ea77-377">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8ea77-378">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8ea77-378">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8ea77-379">Führen Sie Webpack im *Releasemodus* aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-379">Run Webpack in *release* mode.</span></span> <span data-ttu-id="8ea77-380">Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus.</span><span class="sxs-lookup"><span data-stu-id="8ea77-380">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="8ea77-381">Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="8ea77-381">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="8ea77-382">Klicken Sie auf **Debuggen** > **Starten ohne Debugging**, um die App in einem Browser zu starten, ohne den Debugger anzufügen.</span><span class="sxs-lookup"><span data-stu-id="8ea77-382">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="8ea77-383">Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-383">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="8ea77-384">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="8ea77-384">Open another browser instance (any browser).</span></span> <span data-ttu-id="8ea77-385">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="8ea77-385">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ea77-386">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="8ea77-386">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="8ea77-387">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-387">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8ea77-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8ea77-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8ea77-389">Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-389">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="8ea77-390">Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="8ea77-390">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="8ea77-391">Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="8ea77-391">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="8ea77-392">Öffnen Sie `http://localhost:<port_number>` im Browser.</span><span class="sxs-lookup"><span data-stu-id="8ea77-392">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="8ea77-393">Die Datei *wwwroot/index.html* wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-393">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="8ea77-394">Kopieren Sie die URL aus der Adressleiste.</span><span class="sxs-lookup"><span data-stu-id="8ea77-394">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="8ea77-395">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="8ea77-395">Open another browser instance (any browser).</span></span> <span data-ttu-id="8ea77-396">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="8ea77-396">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8ea77-397">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="8ea77-397">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="8ea77-398">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="8ea77-398">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8ea77-400">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="8ea77-400">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
