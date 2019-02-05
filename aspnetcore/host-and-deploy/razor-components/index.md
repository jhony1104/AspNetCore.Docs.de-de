---
title: Hosten und Bereitstellen von Razor Components
author: guardrex
description: Hier erfahren Sie, wie Sie Razor Components- und Blazor-Apps mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: host-and-deploy/razor-components/index
ms.openlocfilehash: 9debd75128ceecb805fc673a8182a785fc9f7942
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667928"
---
# <a name="host-and-deploy-razor-components"></a><span data-ttu-id="c0cba-103">Hosten und Bereitstellen von Razor Components</span><span class="sxs-lookup"><span data-stu-id="c0cba-103">Host and deploy Razor Components</span></span>

<span data-ttu-id="c0cba-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="c0cba-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="c0cba-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="c0cba-105">Publish the app</span></span>

<span data-ttu-id="c0cba-106">Apps werden für die Bereitstellung in Releasekonfigurationen mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="c0cba-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="c0cba-107">In einer IDE kann der Befehl `dotnet publish` mithilfe der integrierten Veröffentlichungsfunktionen automatisch ausgeführt werden, sodass der Befehl je nach verwendetem Entwicklungstool möglicherweise nicht manuell über eine Befehlszeile ausgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="c0cba-107">An IDE may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="c0cba-108">Mit `dotnet publish` wird eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts ausgelöst und das Projekt [erstellt](/dotnet/core/tools/dotnet-build), bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="c0cba-109">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="c0cba-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="c0cba-110">Die Bereitstellung wird im Ordner */bin/Release/&lt;target-framework&gt;/publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-110">The deployment is created in the */bin/Release/&lt;target-framework&gt;/publish* folder.</span></span>

<span data-ttu-id="c0cba-111">Die Objekte im Ordner *publish* werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="c0cba-112">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="c0cba-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="c0cba-113">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="c0cba-113">Host configuration values</span></span>

<span data-ttu-id="c0cba-114">Bei Razor Components-Apps, für die das [serverseitige Hostingmodell](xref:razor-components/hosting-models#server-side-hosting-model) verwendet wird, können [Webhostkonfigurationswerte](xref:fundamentals/host/web-host#host-configuration-values) verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-114">Razor Components apps that use the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model) can accept [Web Host configuration values](xref:fundamentals/host/web-host#host-configuration-values).</span></span>

<span data-ttu-id="c0cba-115">Bei Blazor-Apps, für die das [clientseitige Hostingmodell](xref:razor-components/hosting-models#client-side-hosting-model) verwendet wird, können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Laufzeit in der Entwicklungsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-115">Blazor apps that use the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="c0cba-116">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="c0cba-116">Content Root</span></span>

<span data-ttu-id="c0cba-117">Mit dem Argument `--contentroot` wird der absolute Pfad zu dem Verzeichnis festgelegt, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="c0cba-117">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span>

* <span data-ttu-id="c0cba-118">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="c0cba-118">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c0cba-119">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="c0cba-119">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* <span data-ttu-id="c0cba-120">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="c0cba-120">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="c0cba-121">Diese Einstellung wird beim Ausführen der App mit Visual Studio Debugger sowie beim Ausführen der App über eine Befehlszeile mit `dotnet run` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="c0cba-121">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* <span data-ttu-id="c0cba-122">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="c0cba-122">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c0cba-123">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-123">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a><span data-ttu-id="c0cba-124">Basispfad</span><span class="sxs-lookup"><span data-stu-id="c0cba-124">Path base</span></span>

<span data-ttu-id="c0cba-125">Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem virtuellen Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="c0cba-125">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="c0cba-126">Weitere Informationen finden Sie im Abschnitt [Basispfad einer App](#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="c0cba-126">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c0cba-127">Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-127">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="c0cba-128">Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/" />` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.</span><span class="sxs-lookup"><span data-stu-id="c0cba-128">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/" />` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="c0cba-129">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="c0cba-129">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c0cba-130">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="c0cba-130">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* <span data-ttu-id="c0cba-131">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="c0cba-131">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="c0cba-132">Diese Einstellung wird beim Ausführen der App mit Visual Studio Debugger sowie beim Ausführen der App über eine Befehlszeile mit `dotnet run` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="c0cba-132">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* <span data-ttu-id="c0cba-133">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="c0cba-133">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c0cba-134">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-134">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a><span data-ttu-id="c0cba-135">URLs</span><span class="sxs-lookup"><span data-stu-id="c0cba-135">URLs</span></span>

<span data-ttu-id="c0cba-136">Mit dem Argument `--urls` werden die IP-Adressen und Hostadressen mit Ports und Protokollen angegeben, denen für Anforderungen gelauscht werden soll.</span><span class="sxs-lookup"><span data-stu-id="c0cba-136">The `--urls` argument indicates the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="c0cba-137">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="c0cba-137">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="c0cba-138">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="c0cba-138">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="c0cba-139">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="c0cba-139">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="c0cba-140">Diese Einstellung wird beim Ausführen der App mit Visual Studio Debugger sowie beim Ausführen der App über eine Befehlszeile mit `dotnet run` abgerufen.</span><span class="sxs-lookup"><span data-stu-id="c0cba-140">This setting is picked up when running the app with the Visual Studio Debugger and when running the app from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="c0cba-141">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="c0cba-141">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="c0cba-142">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-142">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a><span data-ttu-id="c0cba-143">Bereitstellen einer clientseitigen Blazor-App</span><span class="sxs-lookup"><span data-stu-id="c0cba-143">Deploy a client-side Blazor app</span></span>

<span data-ttu-id="c0cba-144">Mit dem [clientseitigen Hostingmodell](xref:razor-components/hosting-models#client-side-hosting-model) ist Folgendes möglich:</span><span class="sxs-lookup"><span data-stu-id="c0cba-144">With the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model):</span></span>

* <span data-ttu-id="c0cba-145">Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen.</span><span class="sxs-lookup"><span data-stu-id="c0cba-145">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="c0cba-146">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-146">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="c0cba-147">Dabei wird eine der folgenden beiden Strategien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c0cba-147">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="c0cba-148">Die Blazor-App wird von einer ASP.NET Core-App unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-148">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="c0cba-149">Eine Beschreibung hierzu finden Sie im Abschnitt [Clientseitige, von Blazor gehostete Bereitstellung mit ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core).</span><span class="sxs-lookup"><span data-stu-id="c0cba-149">Covered in the [Client-side Blazor hosted deployment with ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="c0cba-150">Die Blazor-App wird auf einem statischen Hosting-Webserver oder -Webservice abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c0cba-150">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="c0cba-151">Eine Beschreibung hierzu finden Sie im Abschnitt [Clientseitige, eigenständige Blazor-Bereitstellung](#client-side-blazor-standalone-deployment).</span><span class="sxs-lookup"><span data-stu-id="c0cba-151">Covered in the [Client-side Blazor standalone deployment](#client-side-blazor-standalone-deployment) section.</span></span>
  
### <a name="configure-the-linker"></a><span data-ttu-id="c0cba-152">Konfigurieren des Linkers</span><span class="sxs-lookup"><span data-stu-id="c0cba-152">Configure the Linker</span></span>

<span data-ttu-id="c0cba-153">Blazor führt auf jedem Build eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte IL aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="c0cba-153">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="c0cba-154">Die Assemblyverknüpfung auf Builds kann gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-154">You can control assembly linking on build.</span></span> <span data-ttu-id="c0cba-155">Weitere Informationen finden Sie unter <xref:host-and-deploy/razor-components/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="c0cba-155">For more information, see <xref:host-and-deploy/razor-components/configure-linker>.</span></span>

### <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="c0cba-156">Erneutes Generieren von URLs für ein ordnungsgemäßes Routing</span><span class="sxs-lookup"><span data-stu-id="c0cba-156">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="c0cba-157">Routinganforderungen für Seitenkomponenten in einer clientseitigen App sind etwas komplexer als Routinganforderungen an eine serverseitige, gehostete App.</span><span class="sxs-lookup"><span data-stu-id="c0cba-157">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="c0cba-158">Betrachten wir eine clientseitige App mit zwei Seiten:</span><span class="sxs-lookup"><span data-stu-id="c0cba-158">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="c0cba-159">**_Main.cshtml:_** Lädt den Anwendungsstamm und enthält einen Link zur Infoseite (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="c0cba-159">**_Main.cshtml_** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="c0cba-160">**_About.cshtml:_** Infoseite.</span><span class="sxs-lookup"><span data-stu-id="c0cba-160">**_About.cshtml_** &ndash; About page.</span></span>

<span data-ttu-id="c0cba-161">Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c0cba-161">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="c0cba-162">Der Browser sendet eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="c0cba-162">The browser makes a request.</span></span>
1. <span data-ttu-id="c0cba-163">Die Standardseite wird zurückgegeben, in der Regel *index.html*.</span><span class="sxs-lookup"><span data-stu-id="c0cba-163">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="c0cba-164">*index.html* startet die App.</span><span class="sxs-lookup"><span data-stu-id="c0cba-164">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="c0cba-165">Der Router von Blazor wird geladen, und die Hauptseite von Razor (*Main.cshtml*) wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-165">Blazor's router loads and the Razor Main page (*Main.cshtml*) is displayed.</span></span>

<span data-ttu-id="c0cba-166">Wenn auf der Hauptseite der Link zur Infoseite ausgewählt wird, wird die Infoseite geladen.</span><span class="sxs-lookup"><span data-stu-id="c0cba-166">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="c0cba-167">Der Link zur Infoseite kann auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die Infoseite selbst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-167">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="c0cba-168">Alle Anforderungen von internen Seiten *innerhalb der clientseitigen App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="c0cba-168">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="c0cba-169">Der Router verarbeitet Anforderungen intern.</span><span class="sxs-lookup"><span data-stu-id="c0cba-169">The router handles the requests internally.</span></span>

<span data-ttu-id="c0cba-170">Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="c0cba-170">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="c0cba-171">Diese Ressource ist im Internethost der Anwendung nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="c0cba-171">No such resource exists on the app's Internet host, so a *404 Not found* response is returned.</span></span>

<span data-ttu-id="c0cba-172">Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite *index.html* erneut generieren.</span><span class="sxs-lookup"><span data-stu-id="c0cba-172">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="c0cba-173">Wenn *index.html* zurückgegeben wird, übernimmt der clientseitige Router der App und antwortet mit der richtigen Ressource.</span><span class="sxs-lookup"><span data-stu-id="c0cba-173">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

### <a name="app-base-path"></a><span data-ttu-id="c0cba-174">Basispfad einer App</span><span class="sxs-lookup"><span data-stu-id="c0cba-174">App base path</span></span>

<span data-ttu-id="c0cba-175">Beim Basispfad einer App handelt es sich um den virtuellen Stammpfad der App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="c0cba-175">The app base path is the virtual app root path on the server.</span></span> <span data-ttu-id="c0cba-176">Eine App, die sich beispielsweise auf dem Contoso-Server in einem virtuellen Ordner unter `/CoolApp/` befindet, wird unter `https://www.contoso.com/CoolApp` erreicht, wobei der virtuelle Basispfad `/CoolApp/` lautet.</span><span class="sxs-lookup"><span data-stu-id="c0cba-176">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="c0cba-177">Indem der Basispfad der App auf `CoolApp/` festgelegt wird, erkennt die App, wo sie sich virtuell auf dem Server befindet.</span><span class="sxs-lookup"><span data-stu-id="c0cba-177">By setting the app base path to `CoolApp/`, the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="c0cba-178">Die App kann den Basispfad der App verwenden, um URLs relativ zum Stammverzeichnis der App über eine Komponente zu erstellen, die sich nicht im Stammverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="c0cba-178">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="c0cba-179">Dadurch ist es möglich, dass Komponenten, die sich in unterschiedlichen Ebenen der Verzeichnisstruktur befinden, Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen können.</span><span class="sxs-lookup"><span data-stu-id="c0cba-179">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="c0cba-180">Ferner wird der Basispfad der App verwendet, um Klicks auf Links abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet – um die interne Navigation kümmert sich der Blazor-Router.</span><span class="sxs-lookup"><span data-stu-id="c0cba-180">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="c0cba-181">Bei vielen Hostingszenarios ist der virtuelle Pfad des Servers zur App das Stammverzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="c0cba-181">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="c0cba-182">In diesen Fällen ist der Basispfad der App ein Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für eine App.</span><span class="sxs-lookup"><span data-stu-id="c0cba-182">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="c0cba-183">Bei anderen Hostingszenarios wie etwa bei GitHub-Seiten und virtuellen IIS-Verzeichnissen oder untergeordneten Anwendungen muss der Basispfad der App auf den virtuellen Pfad des Servers zur App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-183">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="c0cba-184">Zum Festlegen des Basispfads der App fügen Sie das `<base>`-Tag in *index.html* innerhalb der `<head>`-Tagelemente hinzu bzw. aktualisieren es.</span><span class="sxs-lookup"><span data-stu-id="c0cba-184">To set the app's base path, add or update the `<base>` tag in *index.html* found within the `<head>` tag elements.</span></span> <span data-ttu-id="c0cba-185">Legen Sie den `href`-Attributwert auf `<virtual-path>/` (der nachgestellte Schrägstrich ist erforderlich) fest, wobei `<virtual-path>/` der vollständige virtuelle Stammpfad der App auf dem Server ist.</span><span class="sxs-lookup"><span data-stu-id="c0cba-185">Set the `href` attribute value to `<virtual-path>/` (the trailing slash is required), where `<virtual-path>/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="c0cba-186">Im vorherigen Beispiel wurde der virtuelle Pfad auf `CoolApp/` festgelegt: `<base href="CoolApp/" />`.</span><span class="sxs-lookup"><span data-stu-id="c0cba-186">In the preceding example, the virtual path is set to `CoolApp/`: `<base href="CoolApp/" />`.</span></span>

<span data-ttu-id="c0cba-187">Bei einer App, für die ein virtueller Pfad konfiguriert wurde, der sich nicht im Stammverzeichnis befindet (z. B. `<base href="CoolApp/" />`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*.</span><span class="sxs-lookup"><span data-stu-id="c0cba-187">For an app with a non-root virtual path configured (for example, `<base href="CoolApp/" />`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="c0cba-188">Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.</span><span class="sxs-lookup"><span data-stu-id="c0cba-188">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="c0cba-189">Damit das Pfadbasis-Argument bei der lokalen Ausführung der App mit dem Stammpfad (`/`) übergeben wird, führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="c0cba-189">To pass the path base argument with the root path (`/`) when running the app locally, execute the following command from the app's directory:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="c0cba-190">Die App antwortet lokal unter `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="c0cba-190">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="c0cba-191">Weitere Informationen finden Sie im Abschnitt [Pfadbasis](#path-base).</span><span class="sxs-lookup"><span data-stu-id="c0cba-191">For more information, see the [path base host configuration value](#path-base) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="c0cba-192">Wenn für eine App das [clientseitige Hostingmodell](xref:razor-components/hosting-models#client-side-hosting-model) (basierend auf der **Blazor**-Projektvorlage) verwendet wird und die App als untergeordnete IIS-Anwendung in einer ASP.NET Core-App gehostet wird, muss der geerbte ASP.NET Core Module-Handler deaktiviert werden, oder es muss sichergestellt werden, dass der `<handlers>`-Abschnitt der Stammanwendung (also der übergeordneten Anwendung) in der Datei *web.config* von der untergeordneten Anwendung nicht geerbt wird.</span><span class="sxs-lookup"><span data-stu-id="c0cba-192">If an app uses the [client-side hosting model](xref:razor-components/hosting-models#client-side-hosting-model) (based on the **Blazor** project template) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>
>
> <span data-ttu-id="c0cba-193">Entfernen Sie den Handler in der veröffentlichen *web.config*-Datei der App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="c0cba-193">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> <span data-ttu-id="c0cba-194">Alternativ können Sie auch die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung) deaktivieren, indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="c0cba-194">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> <span data-ttu-id="c0cba-195">Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zu der in diesem Abschnitt beschriebenen Konfiguration des Basispfads der App durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-195">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="c0cba-196">Legen Sie den Basispfad der App in der *index.html*-Datei der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="c0cba-196">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a><span data-ttu-id="c0cba-197">Clientseitige, von Blazor gehostete Bereitstellung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c0cba-197">Client-side Blazor hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="c0cba-198">Mit einer *gehosteten Bereitstellung* wird die clientseitige Blazor-App über eine auf einem Server ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-198">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="c0cba-199">Die Blazor-App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-199">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="c0cba-200">Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c0cba-200">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c0cba-201">Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor-Projektvorlage (in ASP.NET Core gehostet)** (`blazorhosted`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="c0cba-201">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="c0cba-202">Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="c0cba-202">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c0cba-203">Informationen zum Bereitstellen für Azure App Service finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="c0cba-203">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="c0cba-204">Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="c0cba-204">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>

### <a name="client-side-blazor-standalone-deployment"></a><span data-ttu-id="c0cba-205">Clientseitige, eigenständige Blazor-Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="c0cba-205">Client-side Blazor standalone deployment</span></span>

<span data-ttu-id="c0cba-206">Mit einer *eigenständigen Bereitstellung* wird die clientseitige Blazor-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-206">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="c0cba-207">Für die Bereitstellung der Blazor-App wird kein Webserver verwendet.</span><span class="sxs-lookup"><span data-stu-id="c0cba-207">A web server isn't used to serve the Blazor app.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a><span data-ttu-id="c0cba-208">Clientseitige, eigenständige Blazor-Bereitstellung mit IIS</span><span class="sxs-lookup"><span data-stu-id="c0cba-208">Client-side Blazor standalone hosting with IIS</span></span>

<span data-ttu-id="c0cba-209">IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="c0cba-209">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="c0cba-210">Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS (Erstellen einer statischen Website unter IIS)](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="c0cba-210">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="c0cba-211">Veröffentlichte Objekte werden im Ordner *\\bin\\Release\\&lt;target-framework&gt;\\publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-211">Published assets are created in the *\\bin\\Release\\&lt;target-framework&gt;\\publish* folder.</span></span> <span data-ttu-id="c0cba-212">Die Inhalte des Ordners *publish* werden auf dem Webserver oder über den Hostingdienst gehostet.</span><span class="sxs-lookup"><span data-stu-id="c0cba-212">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

<span data-ttu-id="c0cba-213">**web.config**</span><span class="sxs-lookup"><span data-stu-id="c0cba-213">**web.config**</span></span>

<span data-ttu-id="c0cba-214">Beim Veröffentlichen eines Blazor-Projekts wird eine *web.config*-Datei mit der folgenden IIS-Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="c0cba-214">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="c0cba-215">Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="c0cba-215">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="c0cba-216">*\*DLL*: `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="c0cba-216">*\*.dll*: `application/octet-stream`</span></span>
  * <span data-ttu-id="c0cba-217">*\*JSON*: `application/json`</span><span class="sxs-lookup"><span data-stu-id="c0cba-217">*\*.json*: `application/json`</span></span>
  * <span data-ttu-id="c0cba-218">*\*WASM*: `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="c0cba-218">*\*.wasm*: `application/wasm`</span></span>
  * <span data-ttu-id="c0cba-219">*\*WOFF*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c0cba-219">*\*.woff*: `application/font-woff`</span></span>
  * <span data-ttu-id="c0cba-220">*\*WOFF2*: `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="c0cba-220">*\*.woff2*: `application/font-woff`</span></span>
* <span data-ttu-id="c0cba-221">Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:</span><span class="sxs-lookup"><span data-stu-id="c0cba-221">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="c0cba-222">URL-Rewrite-Modul-Regeln werden eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="c0cba-222">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="c0cba-223">Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).</span><span class="sxs-lookup"><span data-stu-id="c0cba-223">Serve the sub-directory where the app's static assets reside (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).</span></span>
  * <span data-ttu-id="c0cba-224">Erstellen Sie SPA-Fallbackrouting, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte (*&lt;assembly_name&gt;\\dist\\index.html*) umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-224">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*&lt;assembly_name&gt;\\dist\\index.html*).</span></span>

<span data-ttu-id="c0cba-225">**Installieren des URL-Rewrite-Moduls**</span><span class="sxs-lookup"><span data-stu-id="c0cba-225">**Install the URL Rewrite Module**</span></span>

<span data-ttu-id="c0cba-226">Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-226">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="c0cba-227">Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="c0cba-227">The module isn't installed by default, and it isn't available for install as an Web Server (IIS) role service feature.</span></span> <span data-ttu-id="c0cba-228">Das Modul muss von der IIS-Website heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-228">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="c0cba-229">Verwenden Sie den Webplattform-Installer zur Installation des Moduls:</span><span class="sxs-lookup"><span data-stu-id="c0cba-229">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="c0cba-230">Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="c0cba-230">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="c0cba-231">Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus.</span><span class="sxs-lookup"><span data-stu-id="c0cba-231">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="c0cba-232">Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.</span><span class="sxs-lookup"><span data-stu-id="c0cba-232">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="c0cba-233">Kopieren Sie den Installer auf den Server.</span><span class="sxs-lookup"><span data-stu-id="c0cba-233">Copy the installer to the server.</span></span> <span data-ttu-id="c0cba-234">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="c0cba-234">Run the installer.</span></span> <span data-ttu-id="c0cba-235">Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu.</span><span class="sxs-lookup"><span data-stu-id="c0cba-235">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="c0cba-236">Der Server muss nach Abschluss der Installation nicht neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-236">A server restart isn't required after the install completes.</span></span>

<span data-ttu-id="c0cba-237">**Konfigurieren der Website**</span><span class="sxs-lookup"><span data-stu-id="c0cba-237">**Configure the website**</span></span>

<span data-ttu-id="c0cba-238">Legen Sie für den **physischen Pfad** der Website den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="c0cba-238">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="c0cba-239">Der Ordner enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="c0cba-239">The folder contains:</span></span>

* <span data-ttu-id="c0cba-240">Die Datei *web.config*, die von IIS zum Konfigurieren der Website verwendet wird, mit den erforderlichen Umleitungsregeln und Dateiinhaltstypen</span><span class="sxs-lookup"><span data-stu-id="c0cba-240">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="c0cba-241">Den Ordner der App für statische Objekte</span><span class="sxs-lookup"><span data-stu-id="c0cba-241">The app's static asset folder.</span></span>

<span data-ttu-id="c0cba-242">**Problembehandlung**</span><span class="sxs-lookup"><span data-stu-id="c0cba-242">**Troubleshooting**</span></span>

<span data-ttu-id="c0cba-243">Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist.</span><span class="sxs-lookup"><span data-stu-id="c0cba-243">If a *500 Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="c0cba-244">Wenn das Modul nicht installiert ist, kann die Datei *web.config* von IIS nicht analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-244">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="c0cba-245">Dadurch wird verhindert, dass die Konfiguration der Website von IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-245">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="c0cba-246">Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="c0cba-246">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a><span data-ttu-id="c0cba-247">Clientseitige, eigenständige Blazor-Bereitstellung mit Nginx</span><span class="sxs-lookup"><span data-stu-id="c0cba-247">Client-side Blazor standalone hosting with Nginx</span></span>

<span data-ttu-id="c0cba-248">Die folgende *nginx.conf*-Datei wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei *Index.html* gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="c0cba-248">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *Index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

<span data-ttu-id="c0cba-249">Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="c0cba-249">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a><span data-ttu-id="c0cba-250">Clientseitige, eigenständige Blazor-Bereitstellung mit Docker</span><span class="sxs-lookup"><span data-stu-id="c0cba-250">Client-side Blazor standalone hosting with Nginx in Docker</span></span>

<span data-ttu-id="c0cba-251">Richten Sie zum Hosten von Blazor in Docker mithilfe von Nginx die Dockerfile für die Verwendung des auf Alpine basierenden Nginx-Images ein.</span><span class="sxs-lookup"><span data-stu-id="c0cba-251">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="c0cba-252">Aktualisieren Sie die Dockerfile zum Kopieren der Datei *nginx.config* in den Container.</span><span class="sxs-lookup"><span data-stu-id="c0cba-252">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="c0cba-253">Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="c0cba-253">Add one line to the Dockerfile, as shown in the following example:</span></span>

```
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a><span data-ttu-id="c0cba-254">Clientseitige, eigenständige Blazor-Bereitstellung mit GitHub Pages</span><span class="sxs-lookup"><span data-stu-id="c0cba-254">Client-side Blazor standalone hosting with GitHub Pages</span></span>

<span data-ttu-id="c0cba-255">Fügen Sie zum Verarbeiten von URL-Umschreibungen eine *404.html*-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite *index.html* verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="c0cba-255">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="c0cba-256">Ein Beispiel für eine Implementierung, das von der Community bereitgestellt wurde, finden Sie unter [Single Page Apps for GitHub Pages (Single-Page-Apps für GitHub Pages)](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages auf GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="c0cba-256">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="c0cba-257">Ein Beispiel für die Verwendung des Community-Ansatzes finden Sie unter [blazor-demo/blazor-demo.github.io auf GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([Livewebsite](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="c0cba-257">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="c0cba-258">Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, fügen Sie der Datei *index.html* das `<base>`-Tag hinzu, oder aktualisieren Sie das Tag in der Datei.</span><span class="sxs-lookup"><span data-stu-id="c0cba-258">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="c0cba-259">Legen Sie den Wert des Attributs `href` auf `<repository-name>/` fest, wobei `<repository-name>/` der Name des GitHub-Repositorys ist.</span><span class="sxs-lookup"><span data-stu-id="c0cba-259">Set the `href` attribute value to `<repository-name>/`, where `<repository-name>/` is the GitHub repository name.</span></span>

## <a name="deploy-a-server-side-razor-components-app"></a><span data-ttu-id="c0cba-260">Bereitstellen einer serverseitigen Razor Components-App</span><span class="sxs-lookup"><span data-stu-id="c0cba-260">Deploy a server-side Razor Components app</span></span>

<span data-ttu-id="c0cba-261">Mit dem [serverseitigen Hostingmodell](xref:razor-components/hosting-models#server-side-hosting-model) wird Razor Components über eine ASP.NET Core-App auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="c0cba-261">With the [server-side hosting model](xref:razor-components/hosting-models#server-side-hosting-model), Razor Components is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="c0cba-262">Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine SignalR-Verbindung verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="c0cba-262">UI updates, event handling, and JavaScript calls are handled over a SignalR connection.</span></span>

<span data-ttu-id="c0cba-263">Die App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="c0cba-263">The app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="c0cba-264">Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="c0cba-264">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="c0cba-265">Bei einer serverseitigen Bereitstellung enthält Visual Studio die **Blazor-Projektvorlage (serverseitig in ASP.NET Core)** (`blazorserver`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="c0cba-265">For a server-side deployment, Visual Studio includes the **Blazor (Server-side in ASP.NET Core)** project template (`blazorserver` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

<span data-ttu-id="c0cba-266">Wenn die ASP.NET Core-App veröffentlicht wird, ist die Razor Components-App in der veröffentlichten Ausgabe enthalten, sodass die ASP.NET Core-App und die Razor Components-App zusammen bereitgestellt werden können.</span><span class="sxs-lookup"><span data-stu-id="c0cba-266">When the ASP.NET Core app is published, the Razor Components app is included in the published output so that the ASP.NET Core app and the Razor Components app can be deployed together.</span></span> <span data-ttu-id="c0cba-267">Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="c0cba-267">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="c0cba-268">Informationen zum Bereitstellen für Azure App Service finden Sie in den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="c0cba-268">For information on deploying to Azure App Service, see the following topics:</span></span>

<xref:tutorials/publish-to-azure-webapp-using-vs>  
<span data-ttu-id="c0cba-269">Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="c0cba-269">Learn how to publish an ASP.NET Core app to Azure App Service using Visual Studio.</span></span>
