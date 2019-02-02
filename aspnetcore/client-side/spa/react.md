---
title: Verwenden der React-Projektvorlage mit ASP.NET Core
author: SteveSandersonMS
description: Erfahren Sie, wie Sie sich mit der Projektvorlage für die Einzelseitenanwendung (Single-Page Application, SPA) von ASP.NET Core für React und create-react-app vertraut machen.
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/21/2018
uid: spa/react
ms.openlocfilehash: d83bff8abcd5b59d8bc4a51a101510755394f0c4
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667686"
---
# <a name="use-the-react-project-template-with-aspnet-core"></a><span data-ttu-id="63789-103">Verwenden der React-Projektvorlage mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63789-103">Use the React project template with ASP.NET Core</span></span>

::: moniker range="= aspnetcore-2.0"

> [!NOTE]
> <span data-ttu-id="63789-104">Diese Dokumentation befasst sich nicht mit der in ASP.NET Core 2.0 enthaltenen React-Projektvorlage.</span><span class="sxs-lookup"><span data-stu-id="63789-104">This documentation isn't about the React project template included in ASP.NET Core 2.0.</span></span> <span data-ttu-id="63789-105">Sie befasst sich mit der neueren React-Vorlage, die manuell aktualisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="63789-105">It's about the newer React template to which you can update manually.</span></span> <span data-ttu-id="63789-106">Die Vorlage ist standardmäßig in ASP.NET Core 2.1 enthalten.</span><span class="sxs-lookup"><span data-stu-id="63789-106">The template is included in ASP.NET Core 2.1 by default.</span></span>

::: moniker-end

<span data-ttu-id="63789-107">Die aktualisierte React-Projektvorlage stellt einen geeigneten Anfangspunkt für ASP.NET Core-Apps dar, die React und CRA-Konventionen ([create-react-app](https://github.com/facebookincubator/create-react-app)) für die Implementierung einer umfangreichen, clientseitigen Benutzerschnittstelle (User Interface, UI) verwenden.</span><span class="sxs-lookup"><span data-stu-id="63789-107">The updated React project template provides a convenient starting point for ASP.NET Core apps using React and [create-react-app](https://github.com/facebookincubator/create-react-app) (CRA) conventions to implement a rich, client-side user interface (UI).</span></span>

<span data-ttu-id="63789-108">Mit der Vorlage können ein ASP.NET Core-Projekt, das als API-Back-End fungieren soll, und ein CRA React-Projekt, das als Benutzeroberfläche fungieren soll, erstellt werden. Sie bietet jedoch den Komfort, dass beide Projekte in einem einzigen App-Projekt gehostet werden, das als eine Einheit erstellt und veröffentlicht werden kann.</span><span class="sxs-lookup"><span data-stu-id="63789-108">The template is equivalent to creating both an ASP.NET Core project to act as an API backend, and a standard CRA React project to act as a UI, but with the convenience of hosting both in a single app project that can be built and published as a single unit.</span></span>

## <a name="create-a-new-app"></a><span data-ttu-id="63789-109">Erstellen einer neuen App</span><span class="sxs-lookup"><span data-stu-id="63789-109">Create a new app</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="63789-110">Stellen Sie bei Verwendung von ASP.NET Core 2.0 sicher, dass Sie [die aktualisierte React-Projektvorlage installiert](xref:spa/index#installation) haben.</span><span class="sxs-lookup"><span data-stu-id="63789-110">If using ASP.NET Core 2.0, ensure you've [installed the updated React project template](xref:spa/index#installation).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="63789-111">Wenn ASP.NET Core 2.1 auf Ihrem Computer installiert ist, müssen Sie die Vorlage für React-Projekte nicht installieren.</span><span class="sxs-lookup"><span data-stu-id="63789-111">If you have ASP.NET Core 2.1 installed, there's no need to install the React project template.</span></span>

::: moniker-end

<span data-ttu-id="63789-112">Erstellen Sie über eine Eingabeaufforderung mit dem Befehl `dotnet new react` in einem leeren Verzeichnis ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="63789-112">Create a new project from a command prompt using the command `dotnet new react` in an empty directory.</span></span> <span data-ttu-id="63789-113">Mit den folgenden Befehlen wird die App beispielsweise im Verzeichnis *my-new-app* erstellt und zu diesem Verzeichnis gewechselt:</span><span class="sxs-lookup"><span data-stu-id="63789-113">For example, the following commands create the app in a *my-new-app* directory and switch to that directory:</span></span>

```console
dotnet new react -o my-new-app
cd my-new-app
```

<span data-ttu-id="63789-114">Führen Sie die App über Visual Studio oder die .NET Core CLI aus:</span><span class="sxs-lookup"><span data-stu-id="63789-114">Run the app from either Visual Studio or the .NET Core CLI:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="63789-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="63789-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="63789-116">Öffnen Sie die generierte *CSPROJ*-Datei, und führen Sie die App von dort wie gewohnt aus.</span><span class="sxs-lookup"><span data-stu-id="63789-116">Open the generated *.csproj* file, and run the app as normal from there.</span></span>

<span data-ttu-id="63789-117">Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern.</span><span class="sxs-lookup"><span data-stu-id="63789-117">The build process restores npm dependencies on the first run, which can take several minutes.</span></span> <span data-ttu-id="63789-118">Nachfolgende Builds sind wesentlich schneller.</span><span class="sxs-lookup"><span data-stu-id="63789-118">Subsequent builds are much faster.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="63789-119">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="63789-119">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="63789-120">Stellen Sie sicher, dass Sie über eine Umgebungsvariable mit dem Namen `ASPNETCORE_Environment` und dem Wert `Development` verfügen.</span><span class="sxs-lookup"><span data-stu-id="63789-120">Ensure you have an environment variable called `ASPNETCORE_Environment` with value of `Development`.</span></span> <span data-ttu-id="63789-121">Führen Sie unter Windows (bei Eingabeaufforderungen außerhalb von PowerShell) `SET ASPNETCORE_Environment=Development` aus.</span><span class="sxs-lookup"><span data-stu-id="63789-121">On Windows (in non-PowerShell prompts), run `SET ASPNETCORE_Environment=Development`.</span></span> <span data-ttu-id="63789-122">Führen Sie unter Linux oder macOS `export ASPNETCORE_Environment=Development` aus.</span><span class="sxs-lookup"><span data-stu-id="63789-122">On Linux or macOS, run `export ASPNETCORE_Environment=Development`.</span></span>

<span data-ttu-id="63789-123">Führen Sie [dotnet build](/dotnet/core/tools/dotnet-build) aus, um zu überprüfen, ob Ihre App ordnungsgemäß erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="63789-123">Run [dotnet build](/dotnet/core/tools/dotnet-build) to verify your app builds correctly.</span></span> <span data-ttu-id="63789-124">Während der Erstellung werden bei der ersten Ausführung npm-Abhängigkeiten wiederhergestellt. Dies kann einige Minuten dauern.</span><span class="sxs-lookup"><span data-stu-id="63789-124">On the first run, the build process restores npm dependencies, which can take several minutes.</span></span> <span data-ttu-id="63789-125">Nachfolgende Builds sind wesentlich schneller.</span><span class="sxs-lookup"><span data-stu-id="63789-125">Subsequent builds are much faster.</span></span>

<span data-ttu-id="63789-126">Führen Sie [dotnet run](/dotnet/core/tools/dotnet-run) aus, um die App zu starten.</span><span class="sxs-lookup"><span data-stu-id="63789-126">Run [dotnet run](/dotnet/core/tools/dotnet-run) to start the app.</span></span>

---

<span data-ttu-id="63789-127">Mit der Projektvorlage werden eine ASP.NET Core-App und eine React-App erstellt.</span><span class="sxs-lookup"><span data-stu-id="63789-127">The project template creates an ASP.NET Core app and a React app.</span></span> <span data-ttu-id="63789-128">Die ASP.NET Core-App ist für die Verwendung für den Datenzugriff, die Autorisierung und weitere serverseitige Belange vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="63789-128">The ASP.NET Core app is intended to be used for data access, authorization, and other server-side concerns.</span></span> <span data-ttu-id="63789-129">Die React-App, die sich im Unterverzeichnis *ClientApp* befindet, ist für sämtliche Belange der Benutzeroberfläche vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="63789-129">The React app, residing in the *ClientApp* subdirectory, is intended to be used for all UI concerns.</span></span>

## <a name="add-pages-images-styles-modules-etc"></a><span data-ttu-id="63789-130">Hinzufügen von Seiten, Images, Formatvorlagen, Modulen usw.</span><span class="sxs-lookup"><span data-stu-id="63789-130">Add pages, images, styles, modules, etc.</span></span>

<span data-ttu-id="63789-131">Das Verzeichnis *ClientApp* ist eine CRA-React-Standard-App.</span><span class="sxs-lookup"><span data-stu-id="63789-131">The *ClientApp* directory is a standard CRA React app.</span></span> <span data-ttu-id="63789-132">Weitere Informationen finden Sie in der offiziellen [CRA-Dokumentation](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md).</span><span class="sxs-lookup"><span data-stu-id="63789-132">See the official [CRA documentation](https://github.com/facebookincubator/create-react-app/blob/master/packages/react-scripts/template/README.md) for more information.</span></span>

<span data-ttu-id="63789-133">Zwischen der React-App, die mit dieser Vorlage erstellt wurde, und der App, die von CRA selbst erstellt wurde, gibt es jedoch leichte Unterschiede; die Funktionen der App sind jedoch unverändert.</span><span class="sxs-lookup"><span data-stu-id="63789-133">There are slight differences between the React app created by this template and the one created by CRA itself; however, the app's capabilities are unchanged.</span></span> <span data-ttu-id="63789-134">Die mit der Vorlage erstellte App enthält ein auf [Bootstrap](https://getbootstrap.com/) basiertes Layout und ein Beispiel für grundlegendes Routing.</span><span class="sxs-lookup"><span data-stu-id="63789-134">The app created by the template contains a [Bootstrap](https://getbootstrap.com/)-based layout and a basic routing example.</span></span>

## <a name="install-npm-packages"></a><span data-ttu-id="63789-135">NPM-Pakete installieren</span><span class="sxs-lookup"><span data-stu-id="63789-135">Install npm packages</span></span>

<span data-ttu-id="63789-136">Verwenden Sie für die Installation von npm-Paketen von Drittanbietern eine Eingabeaufforderung im Unterverzeichnis *ClientApp*.</span><span class="sxs-lookup"><span data-stu-id="63789-136">To install third-party npm packages, use a command prompt in the *ClientApp* subdirectory.</span></span> <span data-ttu-id="63789-137">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="63789-137">For example:</span></span>

```console
cd ClientApp
npm install --save <package_name>
```

## <a name="publish-and-deploy"></a><span data-ttu-id="63789-138">Veröffentlichen und Bereitstellen</span><span class="sxs-lookup"><span data-stu-id="63789-138">Publish and deploy</span></span>

<span data-ttu-id="63789-139">Bei der Entwicklung wird die App in einem für Entwickler optimierten Modus ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="63789-139">In development, the app runs in a mode optimized for developer convenience.</span></span> <span data-ttu-id="63789-140">JavaScript-Pakete enthalten beispielsweise Quellzuordnungen (damit Sie beim Debuggen Ihren ursprünglichen Quellcode anzeigen können).</span><span class="sxs-lookup"><span data-stu-id="63789-140">For example, JavaScript bundles include source maps (so that when debugging, you can see your original source code).</span></span> <span data-ttu-id="63789-141">Die App überwacht JavaScript-, HTML- und CSS-Dateiänderungen auf dem Datenträger und führt bei Feststellung dieser Dateiänderungen automatisch eine Neukompilierung und ein erneutes Laden durch.</span><span class="sxs-lookup"><span data-stu-id="63789-141">The app watches JavaScript, HTML, and CSS file changes on disk and automatically recompiles and reloads when it sees those files change.</span></span>

<span data-ttu-id="63789-142">Stellen Sie bei der Produktion eine für Leistung optimierte Version Ihrer App bereit.</span><span class="sxs-lookup"><span data-stu-id="63789-142">In production, serve a version of your app that's optimized for performance.</span></span> <span data-ttu-id="63789-143">Dies erfolgt gemäß der Konfiguration automatisch.</span><span class="sxs-lookup"><span data-stu-id="63789-143">This is configured to happen automatically.</span></span> <span data-ttu-id="63789-144">Beim Veröffentlichen gibt die Buildkonfiguration einen verkleinerten, transpilierten Build Ihres clientseitigen Codes aus.</span><span class="sxs-lookup"><span data-stu-id="63789-144">When you publish, the build configuration emits a minified, transpiled build of your client-side code.</span></span> <span data-ttu-id="63789-145">Im Gegensatz zum Entwicklungsbuild muss Node.js beim Produktionsbuild nicht auf dem Server installiert sein.</span><span class="sxs-lookup"><span data-stu-id="63789-145">Unlike the development build, the production build doesn't require Node.js to be installed on the server.</span></span>

<span data-ttu-id="63789-146">Sie können [ASP.NET Core-Standardhosting- und -bereitstellungsmethoden](xref:host-and-deploy/index) verwenden.</span><span class="sxs-lookup"><span data-stu-id="63789-146">You can use standard [ASP.NET Core hosting and deployment methods](xref:host-and-deploy/index).</span></span>

## <a name="run-the-cra-server-independently"></a><span data-ttu-id="63789-147">Unabhängiges Ausführen des CRA-Servers</span><span class="sxs-lookup"><span data-stu-id="63789-147">Run the CRA server independently</span></span>

<span data-ttu-id="63789-148">Das Projekt ist so konfiguriert, dass die eigene Instanz des CRA-Entwicklungsservers im Hintergrund gestartet wird, wenn die ASP.NET Core-App im Entwicklungsmodus gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="63789-148">The project is configured to start its own instance of the CRA development server in the background when the ASP.NET Core app starts in development mode.</span></span> <span data-ttu-id="63789-149">Dies ist nützlich, da es bedeutet, dass Sie keinen separaten Server manuell ausführen müssen.</span><span class="sxs-lookup"><span data-stu-id="63789-149">This is convenient because it means you don't have to run a separate server manually.</span></span>

<span data-ttu-id="63789-150">Bei diesem Standardsetup gibt es einen Nachteil.</span><span class="sxs-lookup"><span data-stu-id="63789-150">There's a drawback to this default setup.</span></span> <span data-ttu-id="63789-151">Jedes Mal, wenn Sie Ihren C#-Code ändern und Ihre ASP.NET Core-App neu gestartet werden muss, wird auch der CRA-Server neu gestartet.</span><span class="sxs-lookup"><span data-stu-id="63789-151">Each time you modify your C# code and your ASP.NET Core app needs to restart, the CRA server restarts.</span></span> <span data-ttu-id="63789-152">Es dauert einige Sekunden, bis der Sicherungsvorgang gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="63789-152">A few seconds are required to start back up.</span></span> <span data-ttu-id="63789-153">Wenn Sie Ihren C#-Code häufig bearbeiten und nicht warten möchten, bis der CRA-Server neu gestartet wurde, können Sie den CRA-Server extern ausführen, unabhängig vom ASP.NET Core-Prozess.</span><span class="sxs-lookup"><span data-stu-id="63789-153">If you're making frequent C# code edits and don't want to wait for the CRA server to restart, run the CRA server externally, independently of the ASP.NET Core process.</span></span> <span data-ttu-id="63789-154">Gehen Sie hierzu wie folgt vor:</span><span class="sxs-lookup"><span data-stu-id="63789-154">To do so:</span></span>

1. <span data-ttu-id="63789-155">Hinzufügen einer *env* -Datei in die *ClientApp* Unterverzeichnis mit folgender Einstellung:</span><span class="sxs-lookup"><span data-stu-id="63789-155">Add a *.env* file to the *ClientApp* subdirectory with the following setting:</span></span>

    ```
    BROWSER=none
    ```
    
    <span data-ttu-id="63789-156">Dadurch wird Ihrem Webbrowser öffnen zu verhindern, dass beim Starten des Servers von CRA extern.</span><span class="sxs-lookup"><span data-stu-id="63789-156">This will prevent your web browser from opening when starting the CRA server externally.</span></span>

2. <span data-ttu-id="63789-157">Wechseln Sie in einer Eingabeaufforderung zu dem Unterverzeichnis *ClientApp*, und starten Sie den CRA-Bereitstellungsserver:</span><span class="sxs-lookup"><span data-stu-id="63789-157">In a command prompt, switch to the *ClientApp* subdirectory, and launch the CRA development server:</span></span>

    ```console
    cd ClientApp
    npm start
    ```

3. <span data-ttu-id="63789-158">Ändern Sie Ihre ASP.NET Core-App so, dass die externe CRA-Serverinstanz verwendet wird, statt eine eigene Instanz zu starten.</span><span class="sxs-lookup"><span data-stu-id="63789-158">Modify your ASP.NET Core app to use the external CRA server instance instead of launching one of its own.</span></span> <span data-ttu-id="63789-159">Ersetzen Sie den `spa.UseReactDevelopmentServer`-Aufruf in Ihrer *Startklasse* durch Folgendes:</span><span class="sxs-lookup"><span data-stu-id="63789-159">In your *Startup* class, replace the `spa.UseReactDevelopmentServer` invocation with the following:</span></span>

    ```csharp
    spa.UseProxyToSpaDevelopmentServer("http://localhost:3000");
    ```

<span data-ttu-id="63789-160">Wenn Sie Ihre ASP.NET Core-App starten, wird kein CRA-Server gestartet.</span><span class="sxs-lookup"><span data-stu-id="63789-160">When you start your ASP.NET Core app, it won't launch a CRA server.</span></span> <span data-ttu-id="63789-161">Stattdessen wird die Instanz verwendet, die Sie manuell gestartet haben.</span><span class="sxs-lookup"><span data-stu-id="63789-161">The instance you started manually is used instead.</span></span> <span data-ttu-id="63789-162">Dadurch kann sie schneller gestartet und neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="63789-162">This enables it to start and restart faster.</span></span> <span data-ttu-id="63789-163">So müssen Sie nicht mehr jedes Mal warten, bis Ihre React-App neu erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="63789-163">It's no longer waiting for your React app to rebuild each time.</span></span>
