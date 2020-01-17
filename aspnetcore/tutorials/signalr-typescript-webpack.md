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
ms.openlocfilehash: 9094a1d391c087a6f58aa9dd66e3697a79f4af86
ms.sourcegitcommit: ef1720cb733908f36a54825d84c3461c5280bdbe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75737515"
---
# <a name="use-aspnet-core-opno-locsignalr-with-typescript-and-webpack"></a><span data-ttu-id="01b33-103">Verwenden von ASP.NET Core SignalR mit TypeScript und Webpack</span><span class="sxs-lookup"><span data-stu-id="01b33-103">Use ASP.NET Core SignalR with TypeScript and Webpack</span></span>

<span data-ttu-id="01b33-104">Von [Sébastien Sougnez](https://twitter.com/ssougnez) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="01b33-104">By [Sébastien Sougnez](https://twitter.com/ssougnez) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="01b33-105">Mit [Webpack](https://webpack.js.org/) können Entwickler clientseitige Ressourcen einer Web-App bündeln und erstellen.</span><span class="sxs-lookup"><span data-stu-id="01b33-105">[Webpack](https://webpack.js.org/) enables developers to bundle and build the client-side resources of a web app.</span></span> <span data-ttu-id="01b33-106">In diesem Tutorial wird veranschaulicht, wie Webpack in einer ASP.NET Core SignalR-Web-App verwendet wird, deren Client in [TypeScript](https://www.typescriptlang.org/) geschrieben ist.</span><span class="sxs-lookup"><span data-stu-id="01b33-106">This tutorial demonstrates using Webpack in an ASP.NET Core SignalR web app whose client is written in [TypeScript](https://www.typescriptlang.org/).</span></span>

<span data-ttu-id="01b33-107">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="01b33-107">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="01b33-108">Aufbauen einer einfachen ASP.NET Core SignalR-App</span><span class="sxs-lookup"><span data-stu-id="01b33-108">Scaffold a starter ASP.NET Core SignalR app</span></span>
> * <span data-ttu-id="01b33-109">Konfigurieren des SignalR-TypeScript-Clients</span><span class="sxs-lookup"><span data-stu-id="01b33-109">Configure the SignalR TypeScript client</span></span>
> * <span data-ttu-id="01b33-110">Konfigurieren einer Buildpipeline mithilfe von Webpack</span><span class="sxs-lookup"><span data-stu-id="01b33-110">Configure a build pipeline using Webpack</span></span>
> * <span data-ttu-id="01b33-111">Konfigurieren des SignalR-Servers</span><span class="sxs-lookup"><span data-stu-id="01b33-111">Configure the SignalR server</span></span>
> * <span data-ttu-id="01b33-112">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="01b33-112">Enable communication between client and server</span></span>

<span data-ttu-id="01b33-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="01b33-113">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-typescript-webpack/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="01b33-114">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="01b33-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="01b33-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01b33-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="01b33-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="01b33-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="01b33-117">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="01b33-117">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="01b33-118">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="01b33-118">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="01b33-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="01b33-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="01b33-121">.NET Core SDK 3.0 oder höher</span><span class="sxs-lookup"><span data-stu-id="01b33-121">.NET Core SDK 3.0 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="01b33-122">C# für Visual Studio Code Version 1.17.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="01b33-122">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="01b33-123">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="01b33-123">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="01b33-124">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="01b33-124">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="01b33-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01b33-125">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="01b33-126">Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-126">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="01b33-127">Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="01b33-127">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="01b33-128">Führen Sie die folgenden Anweisungen in Visual Studio aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-128">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="01b33-129">Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.</span><span class="sxs-lookup"><span data-stu-id="01b33-129">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="01b33-130">Wählen Sie in der Liste den Eintrag *$(PATH)* aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-130">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="01b33-131">Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="01b33-131">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="01b33-133">Die Konfiguration von Visual Studio ist abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="01b33-133">Visual Studio configuration is completed.</span></span> <span data-ttu-id="01b33-134">Jetzt ist es an der Zeit, das Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="01b33-134">It's time to create the project.</span></span>

1. <span data-ttu-id="01b33-135">Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt**, und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-135">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="01b33-136">Benennen Sie das Projekt *SignalRWebPack*, und wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-136">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="01b33-137">Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 3.0* aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-137">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 3.0* from the framework selector drop-down.</span></span> <span data-ttu-id="01b33-138">Wählen Sie die **leere** Vorlage und dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-138">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="01b33-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="01b33-140">Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-140">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="01b33-141">Eine leere ASP.NET Core-Web-App für .NET Core wird in einem *SignalRWebPack*-Verzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-141">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="01b33-142">Konfigurieren von Webpack und TypeScript</span><span class="sxs-lookup"><span data-stu-id="01b33-142">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="01b33-143">In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="01b33-143">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="01b33-144">Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json*-Datei zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="01b33-144">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="01b33-145">Fügen Sie der *package.json*-Datei die hervorgehobene Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="01b33-145">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/3.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="01b33-146">Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.</span><span class="sxs-lookup"><span data-stu-id="01b33-146">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="01b33-147">Installieren Sie die erforderlichen NPM-Pakete.</span><span class="sxs-lookup"><span data-stu-id="01b33-147">Install the required npm packages.</span></span> <span data-ttu-id="01b33-148">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-148">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="01b33-149">Beachten Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="01b33-149">Some command details to note:</span></span>

    * <span data-ttu-id="01b33-150">Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer.</span><span class="sxs-lookup"><span data-stu-id="01b33-150">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="01b33-151">Die spezifischen Paketversionen werden von NPM installiert.</span><span class="sxs-lookup"><span data-stu-id="01b33-151">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="01b33-152">Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json*-Datei schreibt.</span><span class="sxs-lookup"><span data-stu-id="01b33-152">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="01b33-153">Beispielsweise wird `"webpack": "4.29.3"` anstelle von `"webpack": "^4.29.3"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="01b33-153">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="01b33-154">Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.</span><span class="sxs-lookup"><span data-stu-id="01b33-154">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="01b33-155">Ausführliche Informationen finden Sie in der offiziellen Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="01b33-155">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="01b33-156">Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Codeausschnitt:</span><span class="sxs-lookup"><span data-stu-id="01b33-156">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="01b33-157">Im Folgenden werden die Skripts erklärt:</span><span class="sxs-lookup"><span data-stu-id="01b33-157">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="01b33-158">`build`: Bündelt Ihre clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b33-158">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="01b33-159">Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-159">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="01b33-160">Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“.</span><span class="sxs-lookup"><span data-stu-id="01b33-160">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="01b33-161">Verwenden Sie `build` nur in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="01b33-161">Only use `build` in development.</span></span>
    * <span data-ttu-id="01b33-162">`release`: Bündelt clientseitige Ressourcen im Produktionsmodus.</span><span class="sxs-lookup"><span data-stu-id="01b33-162">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="01b33-163">`publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="01b33-163">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="01b33-164">Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="01b33-164">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="01b33-165">Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-165">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/3.x/webpack.config.js)]

    <span data-ttu-id="01b33-166">Die vorangehende Datei konfiguriert die Webpack-Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="01b33-166">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="01b33-167">Zu beachtende Konfigurationsdetails:</span><span class="sxs-lookup"><span data-stu-id="01b33-167">Some configuration details to note:</span></span>

    * <span data-ttu-id="01b33-168">Die Eigenschaft `output` überschreibt den Standardwert von *dist*.</span><span class="sxs-lookup"><span data-stu-id="01b33-168">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="01b33-169">Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="01b33-169">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="01b33-170">Das `resolve.extensions`-Array schließt *.js* ein, um den JavaScript-Code des SignalR-Clients zu importieren.</span><span class="sxs-lookup"><span data-stu-id="01b33-170">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="01b33-171">Erstellen Sie ein neues *src*-Verzeichnis im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="01b33-171">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="01b33-172">Es dient als Speicherort für clientseitige Ressourcen des Projekts.</span><span class="sxs-lookup"><span data-stu-id="01b33-172">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="01b33-173">Erstellen Sie *src/index.html* mit dem folgenden Inhalt.</span><span class="sxs-lookup"><span data-stu-id="01b33-173">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/3.x/src/index.html)]

    <span data-ttu-id="01b33-174">Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.</span><span class="sxs-lookup"><span data-stu-id="01b33-174">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="01b33-175">Erstellen Sie ein neues *src/css*-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="01b33-175">Create a new *src/css* directory.</span></span> <span data-ttu-id="01b33-176">Es dient als Speicherort für die *CSS*-Dateien des Projekts.</span><span class="sxs-lookup"><span data-stu-id="01b33-176">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="01b33-177">Erstellen Sie *src/css/main.css* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-177">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/3.x/src/css/main.css)]

    <span data-ttu-id="01b33-178">Die vorangehende Datei *main.css* formatiert die App.</span><span class="sxs-lookup"><span data-stu-id="01b33-178">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="01b33-179">Erstellen Sie *src/tsconfig.json* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-179">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/3.x/src/tsconfig.json)]

    <span data-ttu-id="01b33-180">Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="01b33-180">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="01b33-181">Erstellen Sie *src/index.ts* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-181">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="01b33-182">Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:</span><span class="sxs-lookup"><span data-stu-id="01b33-182">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="01b33-183">`keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das Textfeld eingibt, das als `tbMessage` erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-183">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="01b33-184">Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.</span><span class="sxs-lookup"><span data-stu-id="01b33-184">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="01b33-185">`click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt.</span><span class="sxs-lookup"><span data-stu-id="01b33-185">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="01b33-186">Die Funktion `send` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="01b33-186">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="01b33-187">Konfigurieren einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="01b33-187">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="01b33-188">Fügen Sie der Methode `Startup.Configure` Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) hinzu.</span><span class="sxs-lookup"><span data-stu-id="01b33-188">In the `Startup.Configure` method, add calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseStaticDefaultFiles&highlight=2-3)]

   <span data-ttu-id="01b33-189">Mit dem vorangehenden Code wird dem Server ermöglicht, die Datei *index.html* zu finden und bereitzustellen, unabhängig davon, ob der Benutzer die vollständige URL oder die Stamm-URL der Web-App eingibt.</span><span class="sxs-lookup"><span data-stu-id="01b33-189">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="01b33-190">Ordnen Sie am Ende der `Startup.Configure`-Methode eine */hub*-Route dem `ChatHub`-Hub zu.</span><span class="sxs-lookup"><span data-stu-id="01b33-190">At the end of the `Startup.Configure` method, map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="01b33-191">Ersetzen Sie den Code, der *Hello World!* anzeigt,</span><span class="sxs-lookup"><span data-stu-id="01b33-191">Replace the code that displays *Hello World!*</span></span> <span data-ttu-id="01b33-192">durch folgende Zeile:</span><span class="sxs-lookup"><span data-stu-id="01b33-192">with the following line:</span></span> 

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_UseSignalR&highlight=3)]

1. <span data-ttu-id="01b33-193">Rufen Sie in der `Startup.ConfigureServices`-Methode [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) auf.</span><span class="sxs-lookup"><span data-stu-id="01b33-193">In the `Startup.ConfigureServices` method, call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_).</span></span> <span data-ttu-id="01b33-194">Mit ihr werden die SignalR-Dienste in das Projekt eingefügt.</span><span class="sxs-lookup"><span data-stu-id="01b33-194">It adds the SignalR services to your project.</span></span>

   [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="01b33-195">Erstellen Sie ein neues Verzeichnis namens *Hubs* am Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="01b33-195">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="01b33-196">Es dient als Speicherort des SignalR-Hubs, der im nächsten Schritt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-196">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="01b33-197">Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs*:</span><span class="sxs-lookup"><span data-stu-id="01b33-197">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="01b33-198">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="01b33-198">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/3.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="01b33-199">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="01b33-199">Enable client and server communication</span></span>

<span data-ttu-id="01b33-200">Derzeit zeigt die App ein einfaches Formular zum Senden von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="01b33-200">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="01b33-201">Es geschieht jedoch nichts, wenn Sie versuchen, es zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="01b33-201">Nothing happens when you try to do so.</span></span> <span data-ttu-id="01b33-202">Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.</span><span class="sxs-lookup"><span data-stu-id="01b33-202">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="01b33-203">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-203">Execute the following command at the project root:</span></span>

    ```console
    npm install @microsoft/signalr
    ```

    <span data-ttu-id="01b33-204">Der vorangehende Befehl installiert den [SignalR-TypeScript-Client](https://www.npmjs.com/package/@microsoft/signalr), mit dem der Client Nachrichten an den Server senden kann.</span><span class="sxs-lookup"><span data-stu-id="01b33-204">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="01b33-205">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="01b33-205">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="01b33-206">Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server.</span><span class="sxs-lookup"><span data-stu-id="01b33-206">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="01b33-207">Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung.</span><span class="sxs-lookup"><span data-stu-id="01b33-207">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="01b33-208">Die Funktion `withUrl` konfiguriert die Hub-URL.</span><span class="sxs-lookup"><span data-stu-id="01b33-208">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="01b33-209">Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="01b33-209">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="01b33-210">Jede Nachricht verfügt über einen spezifischen Namen.</span><span class="sxs-lookup"><span data-stu-id="01b33-210">Each message has a specific name.</span></span> <span data-ttu-id="01b33-211">Sie können beispielsweise über Nachrichten mit dem Namen `messageReceived` verfügen, die die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen.</span><span class="sxs-lookup"><span data-stu-id="01b33-211">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="01b33-212">Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden.</span><span class="sxs-lookup"><span data-stu-id="01b33-212">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="01b33-213">Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen.</span><span class="sxs-lookup"><span data-stu-id="01b33-213">You can listen to any number of message names.</span></span> <span data-ttu-id="01b33-214">Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht.</span><span class="sxs-lookup"><span data-stu-id="01b33-214">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="01b33-215">Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-215">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="01b33-216">Es wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="01b33-216">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="01b33-217">Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="01b33-217">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="01b33-218">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="01b33-218">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/3.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="01b33-219">Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="01b33-219">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="01b33-220">Der erste Parameter der Methode ist der Name der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="01b33-220">The method's first parameter is the message name.</span></span> <span data-ttu-id="01b33-221">Die Nachrichtendaten befinden sich in den anderen Parametern.</span><span class="sxs-lookup"><span data-stu-id="01b33-221">The message data inhabits the other parameters.</span></span> <span data-ttu-id="01b33-222">In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="01b33-222">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="01b33-223">Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds.</span><span class="sxs-lookup"><span data-stu-id="01b33-223">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="01b33-224">Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.</span><span class="sxs-lookup"><span data-stu-id="01b33-224">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="01b33-225">Fügen Sie der `ChatHub`-Klasse die hervorgehobene Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="01b33-225">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/3.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="01b33-226">Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="01b33-226">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="01b33-227">Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="01b33-227">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="01b33-228">Eine Methode mit demselben Namen wie die Nachricht genügt.</span><span class="sxs-lookup"><span data-stu-id="01b33-228">A method named after the message name suffices.</span></span>

    <span data-ttu-id="01b33-229">In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="01b33-229">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="01b33-230">Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten.</span><span class="sxs-lookup"><span data-stu-id="01b33-230">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="01b33-231">Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="01b33-231">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="01b33-232">Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="01b33-232">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="01b33-233">Testen der App</span><span class="sxs-lookup"><span data-stu-id="01b33-233">Test the app</span></span>

<span data-ttu-id="01b33-234">Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="01b33-234">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="01b33-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01b33-235">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="01b33-236">Führen Sie Webpack im *Releasemodus* aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-236">Run Webpack in *release* mode.</span></span> <span data-ttu-id="01b33-237">Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-237">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="01b33-238">Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="01b33-238">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="01b33-239">Klicken Sie auf **Debuggen** > **Starten ohne Debugging**, um die App in einem Browser zu starten, ohne den Debugger anzufügen.</span><span class="sxs-lookup"><span data-stu-id="01b33-239">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="01b33-240">Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-240">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

   <span data-ttu-id="01b33-241">Wenn Sie Kompilierungsfehlermeldungen erhalten, versuchen Sie, die Projektmappe zu schließen und erneut zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="01b33-241">If you get compile errors, try closing and reopening the solution.</span></span> 

1. <span data-ttu-id="01b33-242">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="01b33-242">Open another browser instance (any browser).</span></span> <span data-ttu-id="01b33-243">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="01b33-243">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="01b33-244">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="01b33-244">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="01b33-245">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="01b33-245">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="01b33-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="01b33-247">Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="01b33-247">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="01b33-248">Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="01b33-248">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="01b33-249">Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="01b33-249">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="01b33-250">Öffnen Sie `http://localhost:<port_number>` im Browser.</span><span class="sxs-lookup"><span data-stu-id="01b33-250">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="01b33-251">Die Datei *wwwroot/index.html* wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-251">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="01b33-252">Kopieren Sie die URL aus der Adressleiste.</span><span class="sxs-lookup"><span data-stu-id="01b33-252">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="01b33-253">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="01b33-253">Open another browser instance (any browser).</span></span> <span data-ttu-id="01b33-254">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="01b33-254">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="01b33-255">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="01b33-255">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="01b33-256">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="01b33-256">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="01b33-258">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="01b33-258">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="01b33-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01b33-259">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="01b33-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="01b33-260">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="01b33-261">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="01b33-261">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="01b33-262">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="01b33-262">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="01b33-263">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-263">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="01b33-264">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-264">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="01b33-265">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="01b33-265">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="01b33-266">C# für Visual Studio Code Version 1.17.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="01b33-266">C# for Visual Studio Code version 1.17.1 or later</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* <span data-ttu-id="01b33-267">[Node.js](https://nodejs.org/) mit [NPM](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="01b33-267">[Node.js](https://nodejs.org/) with [npm](https://www.npmjs.com/)</span></span>

---

## <a name="create-the-aspnet-core-web-app"></a><span data-ttu-id="01b33-268">Erstellen einer ASP.NET Core-Web-App</span><span class="sxs-lookup"><span data-stu-id="01b33-268">Create the ASP.NET Core web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="01b33-269">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01b33-269">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="01b33-270">Konfigurieren Sie Visual Studio, damit in der Umgebungsvariable *PATH* nach NPM gesucht wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-270">Configure Visual Studio to look for npm in the *PATH* environment variable.</span></span> <span data-ttu-id="01b33-271">Visual Studio verwendet standardmäßig die Version von NPM, die sich im Installationsverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="01b33-271">By default, Visual Studio uses the version of npm found in its installation directory.</span></span> <span data-ttu-id="01b33-272">Führen Sie die folgenden Anweisungen in Visual Studio aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-272">Follow these instructions in Visual Studio:</span></span>

1. <span data-ttu-id="01b33-273">Navigieren Sie zu **Extras** > **Optionen** > **Projekte und Projektmappen** > **Webpaketverwaltung** > **Externe Webtools**.</span><span class="sxs-lookup"><span data-stu-id="01b33-273">Navigate to **Tools** > **Options** > **Projects and Solutions** > **Web Package Management** > **External Web Tools**.</span></span>
1. <span data-ttu-id="01b33-274">Wählen Sie in der Liste den Eintrag *$(PATH)* aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-274">Select the *$(PATH)* entry from the list.</span></span> <span data-ttu-id="01b33-275">Klicken Sie auf den Pfeil nach oben, um den Eintrag auf die zweite Position in der Liste zu verschieben.</span><span class="sxs-lookup"><span data-stu-id="01b33-275">Click the up arrow to move the entry to the second position in the list.</span></span>

    ![Visual Studio-Konfiguration](signalr-typescript-webpack/_static/signalr-configure-path-visual-studio.png)

<span data-ttu-id="01b33-277">Die Konfiguration von Visual Studio ist abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="01b33-277">Visual Studio configuration is completed.</span></span> <span data-ttu-id="01b33-278">Jetzt ist es an der Zeit, das Projekt zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="01b33-278">It's time to create the project.</span></span>

1. <span data-ttu-id="01b33-279">Verwenden Sie die Menüoption **Datei** > **Neu** > **Projekt**, und wählen Sie die Vorlage **ASP.NET Core-Web-App** aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-279">Use the **File** > **New** > **Project** menu option and choose the **ASP.NET Core Web Application** template.</span></span>
1. <span data-ttu-id="01b33-280">Benennen Sie das Projekt *SignalRWebPack*, und wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-280">Name the project *SignalRWebPack*, and select **Create**.</span></span>
1. <span data-ttu-id="01b33-281">Wählen Sie in der Dropdownliste mit Zielframeworks *.NET Core* und in der Dropdownliste zur Auswahl des Frameworks *ASP.NET Core 2.2* aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-281">Select *.NET Core* from the target framework drop-down, and select *ASP.NET Core 2.2* from the framework selector drop-down.</span></span> <span data-ttu-id="01b33-282">Wählen Sie die **leere** Vorlage und dann **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-282">Select the **Empty** template, and select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="01b33-283">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-283">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="01b33-284">Führen Sie den folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-284">Run the following command in the **Integrated Terminal**:</span></span>

```dotnetcli
dotnet new web -o SignalRWebPack
```

<span data-ttu-id="01b33-285">Eine leere ASP.NET Core-Web-App für .NET Core wird in einem *SignalRWebPack*-Verzeichnis erstellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-285">An empty ASP.NET Core web app, targeting .NET Core, is created in a *SignalRWebPack* directory.</span></span>

---

## <a name="configure-webpack-and-typescript"></a><span data-ttu-id="01b33-286">Konfigurieren von Webpack und TypeScript</span><span class="sxs-lookup"><span data-stu-id="01b33-286">Configure Webpack and TypeScript</span></span>

<span data-ttu-id="01b33-287">In den folgenden Schritten wird die Konvertierung von TypeScript zu JavaScript und die Bündelung clientseitiger Ressourcen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="01b33-287">The following steps configure the conversion of TypeScript to JavaScript and the bundling of client-side resources.</span></span>

1. <span data-ttu-id="01b33-288">Führen Sie den folgenden Befehl im Projektstamm aus, um eine *package.json*-Datei zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="01b33-288">Execute the following command in the project root to create a *package.json* file:</span></span>

    ```console
    npm init -y
    ```

1. <span data-ttu-id="01b33-289">Fügen Sie der *package.json*-Datei die hervorgehobene Eigenschaft hinzu:</span><span class="sxs-lookup"><span data-stu-id="01b33-289">Add the highlighted property to the *package.json* file:</span></span>

    [!code-json[package.json](signalr-typescript-webpack/sample/2.x/snippets/package1.json?highlight=4)]

    <span data-ttu-id="01b33-290">Durch Festlegen der Eigenschaft `private` auf `true` werden Warnungen bei der Paketinstallation im nächsten Schritt verhindert.</span><span class="sxs-lookup"><span data-stu-id="01b33-290">Setting the `private` property to `true` prevents package installation warnings in the next step.</span></span>

1. <span data-ttu-id="01b33-291">Installieren Sie die erforderlichen NPM-Pakete.</span><span class="sxs-lookup"><span data-stu-id="01b33-291">Install the required npm packages.</span></span> <span data-ttu-id="01b33-292">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-292">Execute the following command from the project root:</span></span>

    ```console
    npm install -D -E clean-webpack-plugin@1.0.1 css-loader@2.1.0 html-webpack-plugin@4.0.0-beta.5 mini-css-extract-plugin@0.5.0 ts-loader@5.3.3 typescript@3.3.3 webpack@4.29.3 webpack-cli@3.2.3
    ```

    <span data-ttu-id="01b33-293">Beachten Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="01b33-293">Some command details to note:</span></span>

    * <span data-ttu-id="01b33-294">Auf das `@`-Zeichen folgt bei jedem Paketnamen eine Versionsnummer.</span><span class="sxs-lookup"><span data-stu-id="01b33-294">A version number follows the `@` sign for each package name.</span></span> <span data-ttu-id="01b33-295">Die spezifischen Paketversionen werden von NPM installiert.</span><span class="sxs-lookup"><span data-stu-id="01b33-295">npm installs those specific package versions.</span></span>
    * <span data-ttu-id="01b33-296">Die Option `-E` deaktiviert das Standardverhalten von NPM, das Bereichsoperatoren für die [semantische Versionierung](https://semver.org/) in die *package.json*-Datei schreibt.</span><span class="sxs-lookup"><span data-stu-id="01b33-296">The `-E` option disables npm's default behavior of writing [semantic versioning](https://semver.org/) range operators to *package.json*.</span></span> <span data-ttu-id="01b33-297">Beispielsweise wird `"webpack": "4.29.3"` anstelle von `"webpack": "^4.29.3"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="01b33-297">For example, `"webpack": "4.29.3"` is used instead of `"webpack": "^4.29.3"`.</span></span> <span data-ttu-id="01b33-298">Diese Option verhindert unbeabsichtigte Upgrades auf neuere Paketversionen.</span><span class="sxs-lookup"><span data-stu-id="01b33-298">This option prevents unintended upgrades to newer package versions.</span></span>

    <span data-ttu-id="01b33-299">Ausführliche Informationen finden Sie in der offiziellen Dokumentation zu [npm-install](https://docs.npmjs.com/cli/install).</span><span class="sxs-lookup"><span data-stu-id="01b33-299">See the official [npm-install](https://docs.npmjs.com/cli/install) docs for more detail.</span></span>

1. <span data-ttu-id="01b33-300">Ersetzen Sie die `scripts`-Eigenschaft der Datei *package.json* durch den folgenden Codeausschnitt:</span><span class="sxs-lookup"><span data-stu-id="01b33-300">Replace the `scripts` property of the *package.json* file with the following snippet:</span></span>

    ```json
    "scripts": {
      "build": "webpack --mode=development --watch",
      "release": "webpack --mode=production",
      "publish": "npm run release && dotnet publish -c Release"
    },
    ```

    <span data-ttu-id="01b33-301">Im Folgenden werden die Skripts erklärt:</span><span class="sxs-lookup"><span data-stu-id="01b33-301">Some explanation of the scripts:</span></span>

    * <span data-ttu-id="01b33-302">`build`: Bündelt Ihre clientseitigen Ressourcen im Entwicklungsmodus und prüft auf Änderungen an Dateien.</span><span class="sxs-lookup"><span data-stu-id="01b33-302">`build`: Bundles your client-side resources in development mode and watches for file changes.</span></span> <span data-ttu-id="01b33-303">Der Dateiwatcher generiert das Bündel jedes Mal neu, wenn eine Änderung an einer Projektdatei vorgenommen wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-303">The file watcher causes the bundle to regenerate each time a project file changes.</span></span> <span data-ttu-id="01b33-304">Die Option `mode` deaktiviert Produktionsoptimierungsvorgänge wie das „Tree Shaking“ und die „Minimierung“.</span><span class="sxs-lookup"><span data-stu-id="01b33-304">The `mode` option disables production optimizations, such as tree shaking and minification.</span></span> <span data-ttu-id="01b33-305">Verwenden Sie `build` nur in der Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="01b33-305">Only use `build` in development.</span></span>
    * <span data-ttu-id="01b33-306">`release`: Bündelt clientseitige Ressourcen im Produktionsmodus.</span><span class="sxs-lookup"><span data-stu-id="01b33-306">`release`: Bundles your client-side resources in production mode.</span></span>
    * <span data-ttu-id="01b33-307">`publish`: Führt das `release`-Skript aus, um die clientseitigen Ressourcen im Produktionsmodus zu bündeln.</span><span class="sxs-lookup"><span data-stu-id="01b33-307">`publish`: Runs the `release` script to bundle the client-side resources in production mode.</span></span> <span data-ttu-id="01b33-308">Es ruft den [publish](/dotnet/core/tools/dotnet-publish)-Befehl der .NET Core-CLI auf, um die App zu veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="01b33-308">It calls the .NET Core CLI's [publish](/dotnet/core/tools/dotnet-publish) command to publish the app.</span></span>

1. <span data-ttu-id="01b33-309">Erstellen Sie eine Datei namens *webpack.config.js* im Projektstamm mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-309">Create a file named *webpack.config.js*, in the project root, with the following content:</span></span>

    [!code-javascript[webpack.config.js](signalr-typescript-webpack/sample/2.x/webpack.config.js)]

    <span data-ttu-id="01b33-310">Die vorangehende Datei konfiguriert die Webpack-Kompilierung.</span><span class="sxs-lookup"><span data-stu-id="01b33-310">The preceding file configures the Webpack compilation.</span></span> <span data-ttu-id="01b33-311">Zu beachtende Konfigurationsdetails:</span><span class="sxs-lookup"><span data-stu-id="01b33-311">Some configuration details to note:</span></span>

    * <span data-ttu-id="01b33-312">Die Eigenschaft `output` überschreibt den Standardwert von *dist*.</span><span class="sxs-lookup"><span data-stu-id="01b33-312">The `output` property overrides the default value of *dist*.</span></span> <span data-ttu-id="01b33-313">Das Bündel wird stattdessen an das Verzeichnis *wwwroot* ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="01b33-313">The bundle is instead emitted in the *wwwroot* directory.</span></span>
    * <span data-ttu-id="01b33-314">Das `resolve.extensions`-Array schließt *.js* ein, um den JavaScript-Code des SignalR-Clients zu importieren.</span><span class="sxs-lookup"><span data-stu-id="01b33-314">The `resolve.extensions` array includes *.js* to import the SignalR client JavaScript.</span></span>

1. <span data-ttu-id="01b33-315">Erstellen Sie ein neues *src*-Verzeichnis im Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="01b33-315">Create a new *src* directory in the project root.</span></span> <span data-ttu-id="01b33-316">Es dient als Speicherort für clientseitige Ressourcen des Projekts.</span><span class="sxs-lookup"><span data-stu-id="01b33-316">Its purpose is to store the project's client-side assets.</span></span>

1. <span data-ttu-id="01b33-317">Erstellen Sie *src/index.html* mit dem folgenden Inhalt.</span><span class="sxs-lookup"><span data-stu-id="01b33-317">Create *src/index.html* with the following content.</span></span>

    [!code-html[index.html](signalr-typescript-webpack/sample/2.x/src/index.html)]

    <span data-ttu-id="01b33-318">Der vorangehende HTML-Code definiert die Markupbausteine der Homepage.</span><span class="sxs-lookup"><span data-stu-id="01b33-318">The preceding HTML defines the homepage's boilerplate markup.</span></span>

1. <span data-ttu-id="01b33-319">Erstellen Sie ein neues *src/css*-Verzeichnis.</span><span class="sxs-lookup"><span data-stu-id="01b33-319">Create a new *src/css* directory.</span></span> <span data-ttu-id="01b33-320">Es dient als Speicherort für die *CSS*-Dateien des Projekts.</span><span class="sxs-lookup"><span data-stu-id="01b33-320">Its purpose is to store the project's *.css* files.</span></span>

1. <span data-ttu-id="01b33-321">Erstellen Sie *src/css/main.css* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-321">Create *src/css/main.css* with the following content:</span></span>

    [!code-css[main.css](signalr-typescript-webpack/sample/2.x/src/css/main.css)]

    <span data-ttu-id="01b33-322">Die vorangehende Datei *main.css* formatiert die App.</span><span class="sxs-lookup"><span data-stu-id="01b33-322">The preceding *main.css* file styles the app.</span></span>

1. <span data-ttu-id="01b33-323">Erstellen Sie *src/tsconfig.json* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-323">Create *src/tsconfig.json* with the following content:</span></span>

    [!code-json[tsconfig.json](signalr-typescript-webpack/sample/2.x/src/tsconfig.json)]

    <span data-ttu-id="01b33-324">Der vorangehende Code konfiguriert den TypeScript-Compiler, um JavaScript-Code zu erstellen, der mit [ECMAScript 5](https://wikipedia.org/wiki/ECMAScript) kompatibel ist.</span><span class="sxs-lookup"><span data-stu-id="01b33-324">The preceding code configures the TypeScript compiler to produce [ECMAScript](https://wikipedia.org/wiki/ECMAScript) 5-compatible JavaScript.</span></span>

1. <span data-ttu-id="01b33-325">Erstellen Sie *src/index.ts* mit dem folgenden Inhalt:</span><span class="sxs-lookup"><span data-stu-id="01b33-325">Create *src/index.ts* with the following content:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index1.ts?name=snippet_IndexTsPhase1File)]

    <span data-ttu-id="01b33-326">Der vorangehende TypeScript-Code ruft Verweise auf DOM-Elemente ab und fügt zwei Ereignishandler an:</span><span class="sxs-lookup"><span data-stu-id="01b33-326">The preceding TypeScript retrieves references to DOM elements and attaches two event handlers:</span></span>

    * <span data-ttu-id="01b33-327">`keyup`: Dieses Ereignis wird ausgelöst, wenn der Benutzer etwas in das Textfeld eingibt, das als `tbMessage` erkannt wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-327">`keyup`: This event fires when the user types something in the textbox identified as `tbMessage`.</span></span> <span data-ttu-id="01b33-328">Die Funktion `send` wird aufgerufen, wenn der Benutzer die **EINGABETASTE** drückt.</span><span class="sxs-lookup"><span data-stu-id="01b33-328">The `send` function is called when the user presses the **Enter** key.</span></span>
    * <span data-ttu-id="01b33-329">`click`: Dieses Ereignis wird ausgelöst, wenn der Benutzer auf die Schaltfläche **Senden** klickt.</span><span class="sxs-lookup"><span data-stu-id="01b33-329">`click`: This event fires when the user clicks the **Send** button.</span></span> <span data-ttu-id="01b33-330">Die Funktion `send` wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="01b33-330">The `send` function is called.</span></span>

## <a name="configure-the-aspnet-core-app"></a><span data-ttu-id="01b33-331">Konfigurieren einer ASP.NET Core-App</span><span class="sxs-lookup"><span data-stu-id="01b33-331">Configure the ASP.NET Core app</span></span>

1. <span data-ttu-id="01b33-332">Der in der Methode `Startup.Configure` bereitgestellte Code zeigt *Hello World!* an.</span><span class="sxs-lookup"><span data-stu-id="01b33-332">The code provided in the `Startup.Configure` method displays *Hello World!*.</span></span> <span data-ttu-id="01b33-333">Ersetzen Sie den Aufruf der Methode `app.Run` durch Aufrufe von [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) und [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span><span class="sxs-lookup"><span data-stu-id="01b33-333">Replace the `app.Run` method call with calls to [UseDefaultFiles](/dotnet/api/microsoft.aspnetcore.builder.defaultfilesextensions.usedefaultfiles#Microsoft_AspNetCore_Builder_DefaultFilesExtensions_UseDefaultFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_) and [UseStaticFiles](/dotnet/api/microsoft.aspnetcore.builder.staticfileextensions.usestaticfiles#Microsoft_AspNetCore_Builder_StaticFileExtensions_UseStaticFiles_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseStaticDefaultFiles)]

    <span data-ttu-id="01b33-334">Mit dem vorangehenden Code wird dem Server ermöglicht, die Datei *index.html* zu finden und bereitzustellen, unabhängig davon, ob der Benutzer die vollständige URL oder die Stamm-URL der Web-App eingibt.</span><span class="sxs-lookup"><span data-stu-id="01b33-334">The preceding code allows the server to locate and serve the *index.html* file, whether the user enters its full URL or the root URL of the web app.</span></span>

1. <span data-ttu-id="01b33-335">Rufen Sie [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in der Methode `Startup.ConfigureServices` auf.</span><span class="sxs-lookup"><span data-stu-id="01b33-335">Call [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr#Microsoft_Extensions_DependencyInjection_SignalRDependencyInjectionExtensions_AddSignalR_Microsoft_Extensions_DependencyInjection_IServiceCollection_) in the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="01b33-336">Mit ihr werden die SignalR-Dienste in das Projekt eingefügt.</span><span class="sxs-lookup"><span data-stu-id="01b33-336">It adds the SignalR services to your project.</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_AddSignalR)]

1. <span data-ttu-id="01b33-337">Ordnen Sie dem `ChatHub`-Hub eine */hub*-Route zu.</span><span class="sxs-lookup"><span data-stu-id="01b33-337">Map a */hub* route to the `ChatHub` hub.</span></span> <span data-ttu-id="01b33-338">Fügen Sie die folgenden Zeilen am Ende der `Startup.Configure`-Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="01b33-338">Add the following lines at the end of the `Startup.Configure` method:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_UseSignalR)]

1. <span data-ttu-id="01b33-339">Erstellen Sie ein neues Verzeichnis namens *Hubs* am Projektstamm.</span><span class="sxs-lookup"><span data-stu-id="01b33-339">Create a new directory, called *Hubs*, in the project root.</span></span> <span data-ttu-id="01b33-340">Es dient als Speicherort des SignalR-Hubs, der im nächsten Schritt erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="01b33-340">Its purpose is to store the SignalR hub, which is created in the next step.</span></span>

1. <span data-ttu-id="01b33-341">Erstellen Sie mit dem folgenden Code den Hub *Hubs/ChatHub.cs*:</span><span class="sxs-lookup"><span data-stu-id="01b33-341">Create hub *Hubs/ChatHub.cs* with the following code:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/snippets/ChatHub.cs?name=snippet_ChatHubStubClass)]

1. <span data-ttu-id="01b33-342">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* ein, um den `ChatHub`-Verweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="01b33-342">Add the following code at the top of the *Startup.cs* file to resolve the `ChatHub` reference:</span></span>

    [!code-csharp[Startup](signalr-typescript-webpack/sample/2.x/Startup.cs?name=snippet_HubsNamespace)]

## <a name="enable-client-and-server-communication"></a><span data-ttu-id="01b33-343">Aktivieren der Kommunikation zwischen Client und Server</span><span class="sxs-lookup"><span data-stu-id="01b33-343">Enable client and server communication</span></span>

<span data-ttu-id="01b33-344">Derzeit zeigt die App ein einfaches Formular zum Senden von Nachrichten an.</span><span class="sxs-lookup"><span data-stu-id="01b33-344">The app currently displays a simple form to send messages.</span></span> <span data-ttu-id="01b33-345">Es geschieht jedoch nichts, wenn Sie versuchen, es zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="01b33-345">Nothing happens when you try to do so.</span></span> <span data-ttu-id="01b33-346">Der Server lauscht einer spezifischen Route, aber er verarbeitet gesendete Nachrichten nicht.</span><span class="sxs-lookup"><span data-stu-id="01b33-346">The server is listening to a specific route but does nothing with sent messages.</span></span>

1. <span data-ttu-id="01b33-347">Führen Sie den folgenden Befehl auf der Ebene des Projektstamms aus:</span><span class="sxs-lookup"><span data-stu-id="01b33-347">Execute the following command at the project root:</span></span>

    ```console
    npm install @aspnet/signalr
    ```

    <span data-ttu-id="01b33-348">Der vorangehende Befehl installiert den [SignalR-TypeScript-Client](https://www.npmjs.com/package/@microsoft/signalr), mit dem der Client Nachrichten an den Server senden kann.</span><span class="sxs-lookup"><span data-stu-id="01b33-348">The preceding command installs the [SignalR TypeScript client](https://www.npmjs.com/package/@microsoft/signalr), which allows the client to send messages to the server.</span></span>

1. <span data-ttu-id="01b33-349">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="01b33-349">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/snippets/index2.ts?name=snippet_IndexTsPhase2File&highlight=2,9-23)]

    <span data-ttu-id="01b33-350">Der vorangehende Code unterstützt das Empfangen von Nachrichten vom Server.</span><span class="sxs-lookup"><span data-stu-id="01b33-350">The preceding code supports receiving messages from the server.</span></span> <span data-ttu-id="01b33-351">Die Klasse `HubConnectionBuilder` erstellt einen neuen Generator für die Konfiguration der Serververbindung.</span><span class="sxs-lookup"><span data-stu-id="01b33-351">The `HubConnectionBuilder` class creates a new builder for configuring the server connection.</span></span> <span data-ttu-id="01b33-352">Die Funktion `withUrl` konfiguriert die Hub-URL.</span><span class="sxs-lookup"><span data-stu-id="01b33-352">The `withUrl` function configures the hub URL.</span></span>

    <span data-ttu-id="01b33-353">Mit SignalR wird der Austausch von Nachrichten zwischen einem Client und einem Server ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="01b33-353">SignalR enables the exchange of messages between a client and a server.</span></span> <span data-ttu-id="01b33-354">Jede Nachricht verfügt über einen spezifischen Namen.</span><span class="sxs-lookup"><span data-stu-id="01b33-354">Each message has a specific name.</span></span> <span data-ttu-id="01b33-355">Sie können beispielsweise über Nachrichten mit dem Namen `messageReceived` verfügen, die die Logik zum Anzeigen neuer Nachrichten im Nachrichtenbereich ausführen.</span><span class="sxs-lookup"><span data-stu-id="01b33-355">For example, you can have messages with the name `messageReceived` that execute the logic responsible for displaying the new message in the messages zone.</span></span> <span data-ttu-id="01b33-356">Mit der `on`-Funktion kann eine spezifische Nachricht belauscht werden.</span><span class="sxs-lookup"><span data-stu-id="01b33-356">Listening to a specific message can be done via the `on` function.</span></span> <span data-ttu-id="01b33-357">Sie können auf eine beliebige Anzahl von Nachrichtennamen lauschen.</span><span class="sxs-lookup"><span data-stu-id="01b33-357">You can listen to any number of message names.</span></span> <span data-ttu-id="01b33-358">Außerdem können Parameter an die Nachricht übergeben werden, z.B. der Name des Autors und der Inhalt der empfangenen Nachricht.</span><span class="sxs-lookup"><span data-stu-id="01b33-358">It's also possible to pass parameters to the message, such as the author's name and the content of the message received.</span></span> <span data-ttu-id="01b33-359">Sobald der Client eine Nachricht empfängt, wird ein neues `div`-Element mit dem Namen des Autors und dem Inhalt der Nachricht im `innerHTML`-Attribut erstellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-359">Once the client receives a message, a new `div` element is created with the author's name and the message content in its `innerHTML` attribute.</span></span> <span data-ttu-id="01b33-360">Es wird dem Hauptelement `div` hinzugefügt, das die Nachrichten anzeigt.</span><span class="sxs-lookup"><span data-stu-id="01b33-360">It's added to the main `div` element displaying the messages.</span></span>

1. <span data-ttu-id="01b33-361">Da der Client nun dazu in der Lage ist, Nachrichten zu empfangen, konfigurieren Sie ihn zum Senden von Nachrichten.</span><span class="sxs-lookup"><span data-stu-id="01b33-361">Now that the client can receive a message, configure it to send messages.</span></span> <span data-ttu-id="01b33-362">Fügen Sie den hervorgehobenen Code in die Datei *src/index.ts* ein:</span><span class="sxs-lookup"><span data-stu-id="01b33-362">Add the highlighted code to the *src/index.ts* file:</span></span>

    [!code-typescript[index.ts](signalr-typescript-webpack/sample/2.x/src/index.ts?highlight=34-35)]

    <span data-ttu-id="01b33-363">Für das Senden einer Nachricht über die WebSockets-Verbindung ist das Aufrufen der Methode `send` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="01b33-363">Sending a message through the WebSockets connection requires calling the `send` method.</span></span> <span data-ttu-id="01b33-364">Der erste Parameter der Methode ist der Name der Nachricht.</span><span class="sxs-lookup"><span data-stu-id="01b33-364">The method's first parameter is the message name.</span></span> <span data-ttu-id="01b33-365">Die Nachrichtendaten befinden sich in den anderen Parametern.</span><span class="sxs-lookup"><span data-stu-id="01b33-365">The message data inhabits the other parameters.</span></span> <span data-ttu-id="01b33-366">In diesem Beispiel wird eine Nachricht, die als `newMessage` erkannt wird, an den Server gesendet.</span><span class="sxs-lookup"><span data-stu-id="01b33-366">In this example, a message identified as `newMessage` is sent to the server.</span></span> <span data-ttu-id="01b33-367">Die Nachricht besteht aus dem Benutzernamen und der Benutzereingabe eines Textfelds.</span><span class="sxs-lookup"><span data-stu-id="01b33-367">The message consists of the username and the user input from a text box.</span></span> <span data-ttu-id="01b33-368">Wenn das Senden funktioniert hat, wird der Textfeldwert gelöscht.</span><span class="sxs-lookup"><span data-stu-id="01b33-368">If the send works, the text box value is cleared.</span></span>

1. <span data-ttu-id="01b33-369">Fügen Sie der `ChatHub`-Klasse die hervorgehobene Methode hinzu:</span><span class="sxs-lookup"><span data-stu-id="01b33-369">Add the highlighted method to the `ChatHub` class:</span></span>

    [!code-csharp[ChatHub](signalr-typescript-webpack/sample/2.x/Hubs/ChatHub.cs?highlight=8-11)]

    <span data-ttu-id="01b33-370">Der vorangehende Code überträgt die empfangenen Nachrichten an alle verbundenen Benutzer, sobald der Server sie empfängt.</span><span class="sxs-lookup"><span data-stu-id="01b33-370">The preceding code broadcasts received messages to all connected users once the server receives them.</span></span> <span data-ttu-id="01b33-371">Es ist nicht notwendig, eine generische `on`-Methode zum Empfangen aller Nachrichten zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="01b33-371">It's unnecessary to have a generic `on` method to receive all the messages.</span></span> <span data-ttu-id="01b33-372">Eine Methode mit demselben Namen wie die Nachricht genügt.</span><span class="sxs-lookup"><span data-stu-id="01b33-372">A method named after the message name suffices.</span></span>

    <span data-ttu-id="01b33-373">In diesem Beispiel sendet der TypeScript-Client eine Nachricht, die als `newMessage` erkannt wurde.</span><span class="sxs-lookup"><span data-stu-id="01b33-373">In this example, the TypeScript client sends a message identified as `newMessage`.</span></span> <span data-ttu-id="01b33-374">Die C#-Methode `NewMessage` erwartet die vom Client gesendeten Daten.</span><span class="sxs-lookup"><span data-stu-id="01b33-374">The C# `NewMessage` method expects the data sent by the client.</span></span> <span data-ttu-id="01b33-375">Die Methode [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) wird auf [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all) aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="01b33-375">A call is made to the [SendAsync](/dotnet/api/microsoft.aspnetcore.signalr.clientproxyextensions.sendasync) method on [Clients.All](/dotnet/api/microsoft.aspnetcore.signalr.ihubclients-1.all).</span></span> <span data-ttu-id="01b33-376">Die empfangenen Nachrichten werden an alle mit dem Hub verbundenen Clients gesendet.</span><span class="sxs-lookup"><span data-stu-id="01b33-376">The received messages are sent to all clients connected to the hub.</span></span>

## <a name="test-the-app"></a><span data-ttu-id="01b33-377">Testen der App</span><span class="sxs-lookup"><span data-stu-id="01b33-377">Test the app</span></span>

<span data-ttu-id="01b33-378">Mit den folgenden Schritten können Sie überprüfen, ob die App funktioniert.</span><span class="sxs-lookup"><span data-stu-id="01b33-378">Confirm that the app works with the following steps.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="01b33-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="01b33-379">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="01b33-380">Führen Sie Webpack im *Releasemodus* aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-380">Run Webpack in *release* mode.</span></span> <span data-ttu-id="01b33-381">Führen Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl im Projektstammverzeichnis aus.</span><span class="sxs-lookup"><span data-stu-id="01b33-381">Using the **Package Manager Console** window, execute the following command in the project root.</span></span> <span data-ttu-id="01b33-382">Wenn Sie sich nicht im Projektstammverzeichnis befinden, geben Sie `cd SignalRWebPack` ein, bevor Sie den Befehl eingeben.</span><span class="sxs-lookup"><span data-stu-id="01b33-382">If you are not in the project root, enter `cd SignalRWebPack` before entering the command.</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="01b33-383">Klicken Sie auf **Debuggen** > **Starten ohne Debugging**, um die App in einem Browser zu starten, ohne den Debugger anzufügen.</span><span class="sxs-lookup"><span data-stu-id="01b33-383">Select **Debug** > **Start without debugging** to launch the app in a browser without attaching the debugger.</span></span> <span data-ttu-id="01b33-384">Die Datei *wwwroot/index.html* wird unter `http://localhost:<port_number>` bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-384">The *wwwroot/index.html* file is served at `http://localhost:<port_number>`.</span></span>

1. <span data-ttu-id="01b33-385">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="01b33-385">Open another browser instance (any browser).</span></span> <span data-ttu-id="01b33-386">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="01b33-386">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="01b33-387">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="01b33-387">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="01b33-388">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="01b33-388">The unique user name and message are displayed on both pages instantly.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="01b33-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="01b33-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="01b33-390">Führen Sie Webpack im *Releasemodus* aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="01b33-390">Run Webpack in *release* mode by executing the following command in the project root:</span></span>

    [!INCLUDE [npm-run-release](../includes/signalr-typescript-webpack/npm-run-release.md)]

1. <span data-ttu-id="01b33-391">Erstellen Sie die App, und führen Sie sie aus, indem Sie den folgenden Befehl im Projektstamm ausführen:</span><span class="sxs-lookup"><span data-stu-id="01b33-391">Build and run the app by executing the following command in the project root:</span></span>

    ```dotnetcli
    dotnet run
    ```

    <span data-ttu-id="01b33-392">Der Webserver beginnt mit der App und stellt sie auf „localhost“ zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="01b33-392">The web server starts the app and makes it available on localhost.</span></span>

1. <span data-ttu-id="01b33-393">Öffnen Sie `http://localhost:<port_number>` im Browser.</span><span class="sxs-lookup"><span data-stu-id="01b33-393">Open a browser to `http://localhost:<port_number>`.</span></span> <span data-ttu-id="01b33-394">Die Datei *wwwroot/index.html* wird bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="01b33-394">The *wwwroot/index.html* file is served.</span></span> <span data-ttu-id="01b33-395">Kopieren Sie die URL aus der Adressleiste.</span><span class="sxs-lookup"><span data-stu-id="01b33-395">Copy the URL from the address bar.</span></span>

1. <span data-ttu-id="01b33-396">Öffnen Sie eine weitere Browserinstanz (beliebiger Browser).</span><span class="sxs-lookup"><span data-stu-id="01b33-396">Open another browser instance (any browser).</span></span> <span data-ttu-id="01b33-397">Fügen Sie die URL in die Adressleiste ein.</span><span class="sxs-lookup"><span data-stu-id="01b33-397">Paste the URL in the address bar.</span></span>

1. <span data-ttu-id="01b33-398">Wählen Sie einen beliebigen Browser aus, geben Sie etwas im Textfeld **Nachricht** ein, und klicken Sie auf **Senden**.</span><span class="sxs-lookup"><span data-stu-id="01b33-398">Choose either browser, type something in the **Message** text box, and click the **Send** button.</span></span> <span data-ttu-id="01b33-399">Der eindeutige Benutzername und die Nachricht werden sofort auf beiden Seiten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="01b33-399">The unique user name and message are displayed on both pages instantly.</span></span>

---

![Die Nachricht wird in beiden Browserfenstern angezeigt](signalr-typescript-webpack/_static/browsers-message-broadcast.png)

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="01b33-401">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="01b33-401">Additional resources</span></span>

* <xref:signalr/javascript-client>
* <xref:signalr/hubs>
