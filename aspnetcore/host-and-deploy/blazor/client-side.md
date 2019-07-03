---
title: Clientseitiges Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: 7567473ae8acd9e1072954907f0fe9c7beea29ad
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67153184"
---
# <a name="host-and-deploy-aspnet-core-blazor-client-side"></a><span data-ttu-id="1d01d-103">Clientseitiges Hosten und Bereitstellen von ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1d01d-103">Host and deploy ASP.NET Core Blazor client-side</span></span>

<span data-ttu-id="1d01d-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1d01d-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="host-configuration-values"></a><span data-ttu-id="1d01d-105">Hostkonfigurationswerte</span><span class="sxs-lookup"><span data-stu-id="1d01d-105">Host configuration values</span></span>

<span data-ttu-id="1d01d-106">Bei Blazor-Apps, für die das [clientseitige Hostingmodell](xref:blazor/hosting-models#client-side) verwendet wird, können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Laufzeit in der Entwicklungsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-106">Blazor apps that use the [client-side hosting model](xref:blazor/hosting-models#client-side) can accept the following host configuration values as command-line arguments at runtime in the development environment.</span></span>

### <a name="content-root"></a><span data-ttu-id="1d01d-107">Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="1d01d-107">Content Root</span></span>

<span data-ttu-id="1d01d-108">Mit dem Argument `--contentroot` wird der absolute Pfad zu dem Verzeichnis festgelegt, das die Inhaltsdateien der App enthält.</span><span class="sxs-lookup"><span data-stu-id="1d01d-108">The `--contentroot` argument sets the absolute path to the directory that contains the app's content files.</span></span> <span data-ttu-id="1d01d-109">In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.</span><span class="sxs-lookup"><span data-stu-id="1d01d-109">In the following examples, `/content-root-path` is the app's content root path.</span></span>

* <span data-ttu-id="1d01d-110">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="1d01d-110">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1d01d-111">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="1d01d-111">From the app's directory, execute:</span></span>

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* <span data-ttu-id="1d01d-112">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d01d-112">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="1d01d-113">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-113">This setting is used when the app is run with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* <span data-ttu-id="1d01d-114">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="1d01d-114">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1d01d-115">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-115">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a><span data-ttu-id="1d01d-116">Basispfad</span><span class="sxs-lookup"><span data-stu-id="1d01d-116">Path base</span></span>

<span data-ttu-id="1d01d-117">Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem virtuellen Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt).</span><span class="sxs-lookup"><span data-stu-id="1d01d-117">The `--pathbase` argument sets the app base path for an app run locally with a non-root virtual path (the `<base>` tag `href` is set to a path other than `/` for staging and production).</span></span> <span data-ttu-id="1d01d-118">In den folgenden Beispielen ist `/virtual-path` die Pfadbasis der App.</span><span class="sxs-lookup"><span data-stu-id="1d01d-118">In the following examples, `/virtual-path` is the app's path base.</span></span> <span data-ttu-id="1d01d-119">Weitere Informationen finden Sie im Abschnitt [Basispfad einer App](#app-base-path).</span><span class="sxs-lookup"><span data-stu-id="1d01d-119">For more information, see the [App base path](#app-base-path) section.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1d01d-120">Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-120">Unlike the path provided to `href` of the `<base>` tag, don't include a trailing slash (`/`) when passing the `--pathbase` argument value.</span></span> <span data-ttu-id="1d01d-121">Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.</span><span class="sxs-lookup"><span data-stu-id="1d01d-121">If the app base path is provided in the `<base>` tag as `<base href="/CoolApp/">` (includes a trailing slash), pass the command-line argument value as `--pathbase=/CoolApp` (no trailing slash).</span></span>

* <span data-ttu-id="1d01d-122">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="1d01d-122">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1d01d-123">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="1d01d-123">From the app's directory, execute:</span></span>

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* <span data-ttu-id="1d01d-124">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d01d-124">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="1d01d-125">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-125">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* <span data-ttu-id="1d01d-126">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="1d01d-126">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1d01d-127">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-127">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a><span data-ttu-id="1d01d-128">URLs</span><span class="sxs-lookup"><span data-stu-id="1d01d-128">URLs</span></span>

<span data-ttu-id="1d01d-129">Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.</span><span class="sxs-lookup"><span data-stu-id="1d01d-129">The `--urls` argument sets the IP addresses or host addresses with ports and protocols to listen on for requests.</span></span>

* <span data-ttu-id="1d01d-130">Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben.</span><span class="sxs-lookup"><span data-stu-id="1d01d-130">Pass the argument when running the app locally at a command prompt.</span></span> <span data-ttu-id="1d01d-131">Führen Sie den folgenden Befehl über das Verzeichnis der App aus:</span><span class="sxs-lookup"><span data-stu-id="1d01d-131">From the app's directory, execute:</span></span>

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* <span data-ttu-id="1d01d-132">Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d01d-132">Add an entry to the app's *launchSettings.json* file in the **IIS Express** profile.</span></span> <span data-ttu-id="1d01d-133">Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-133">This setting is used when running the app with the Visual Studio Debugger and from a command prompt with `dotnet run`.</span></span>

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* <span data-ttu-id="1d01d-134">Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein.</span><span class="sxs-lookup"><span data-stu-id="1d01d-134">In Visual Studio, specify the argument in **Properties** > **Debug** > **Application arguments**.</span></span> <span data-ttu-id="1d01d-135">Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-135">Setting the argument in the Visual Studio property page adds the argument to the *launchSettings.json* file.</span></span>

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a><span data-ttu-id="1d01d-136">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="1d01d-136">Deployment</span></span>

<span data-ttu-id="1d01d-137">Mit dem [clientseitigen Hostingmodell](xref:blazor/hosting-models#client-side) ist Folgendes möglich:</span><span class="sxs-lookup"><span data-stu-id="1d01d-137">With the [client-side hosting model](xref:blazor/hosting-models#client-side):</span></span>

* <span data-ttu-id="1d01d-138">Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen.</span><span class="sxs-lookup"><span data-stu-id="1d01d-138">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="1d01d-139">Die App wird direkt im UI-Thread des Browsers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-139">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="1d01d-140">Dabei wird eine der folgenden beiden Strategien unterstützt:</span><span class="sxs-lookup"><span data-stu-id="1d01d-140">Either of the following strategies is supported:</span></span>
  * <span data-ttu-id="1d01d-141">Die Blazor-App wird von einer ASP.NET Core-App unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-141">The Blazor app is served by an ASP.NET Core app.</span></span> <span data-ttu-id="1d01d-142">Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-142">This strategy is covered in the [Hosted deployment with ASP.NET Core](#hosted-deployment-with-aspnet-core) section.</span></span>
  * <span data-ttu-id="1d01d-143">Die Blazor-App wird auf einem statischen Hosting-Webserver oder -Webservice abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-143">The Blazor app is placed on a static hosting web server or service, where .NET isn't used to serve the Blazor app.</span></span> <span data-ttu-id="1d01d-144">Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-144">This strategy is covered in the [Standalone deployment](#standalone-deployment) section.</span></span>

## <a name="configure-the-linker"></a><span data-ttu-id="1d01d-145">Konfigurieren des Linkers</span><span class="sxs-lookup"><span data-stu-id="1d01d-145">Configure the Linker</span></span>

<span data-ttu-id="1d01d-146">Blazor führt auf jedem Build eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte IL aus den Ausgabeassemblys zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="1d01d-146">Blazor performs Intermediate Language (IL) linking on each build to remove unnecessary IL from the output assemblies.</span></span> <span data-ttu-id="1d01d-147">Verknüpfungen mit Assemblys können beim Erstellen des Builds gesteuert werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-147">Assembly linking can be controlled on build.</span></span> <span data-ttu-id="1d01d-148">Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/configure-linker>.</span><span class="sxs-lookup"><span data-stu-id="1d01d-148">For more information, see <xref:host-and-deploy/blazor/configure-linker>.</span></span>

## <a name="rewrite-urls-for-correct-routing"></a><span data-ttu-id="1d01d-149">Erneutes Generieren von URLs für ein ordnungsgemäßes Routing</span><span class="sxs-lookup"><span data-stu-id="1d01d-149">Rewrite URLs for correct routing</span></span>

<span data-ttu-id="1d01d-150">Routinganforderungen für Seitenkomponenten in einer clientseitigen App sind etwas komplexer als Routinganforderungen an eine serverseitige, gehostete App.</span><span class="sxs-lookup"><span data-stu-id="1d01d-150">Routing requests for page components in a client-side app isn't as simple as routing requests to a server-side, hosted app.</span></span> <span data-ttu-id="1d01d-151">Betrachten wir eine clientseitige App mit zwei Seiten:</span><span class="sxs-lookup"><span data-stu-id="1d01d-151">Consider a client-side app with two pages:</span></span>

* <span data-ttu-id="1d01d-152">**_Main.razor**: Wird im Stammverzeichnis der App geladen und enthält einen Link zur Infoseite (`href="About"`).</span><span class="sxs-lookup"><span data-stu-id="1d01d-152">**_Main.razor** &ndash; Loads at the root of the app and contains a link to the About page (`href="About"`).</span></span>
* <span data-ttu-id="1d01d-153">**_About.razor**: Infoseite.</span><span class="sxs-lookup"><span data-stu-id="1d01d-153">**_About.razor** &ndash; About page.</span></span>

<span data-ttu-id="1d01d-154">Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1d01d-154">When the app's default document is requested using the browser's address bar (for example, `https://www.contoso.com/`):</span></span>

1. <span data-ttu-id="1d01d-155">Der Browser sendet eine Anforderung.</span><span class="sxs-lookup"><span data-stu-id="1d01d-155">The browser makes a request.</span></span>
1. <span data-ttu-id="1d01d-156">Die Standardseite wird zurückgegeben, in der Regel *index.html*.</span><span class="sxs-lookup"><span data-stu-id="1d01d-156">The default page is returned, which is usually *index.html*.</span></span>
1. <span data-ttu-id="1d01d-157">*index.html* startet die App.</span><span class="sxs-lookup"><span data-stu-id="1d01d-157">*index.html* bootstraps the app.</span></span>
1. <span data-ttu-id="1d01d-158">Der Router von Blazor wird geladen, und die Razor-Hauptseite (*Main.razor*) wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-158">Blazor's router loads, and the Razor Main page (*Main.razor*) is displayed.</span></span>

<span data-ttu-id="1d01d-159">Wenn auf der Hauptseite der Link zur Infoseite ausgewählt wird, wird die Infoseite geladen.</span><span class="sxs-lookup"><span data-stu-id="1d01d-159">On the Main page, selecting the link to the About page loads the About page.</span></span> <span data-ttu-id="1d01d-160">Der Link zur Infoseite kann auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die Infoseite selbst bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-160">Selecting the link to the About page works on the client because the Blazor router stops the browser from making a request on the Internet to `www.contoso.com` for `About` and serves the About page itself.</span></span> <span data-ttu-id="1d01d-161">Alle Anforderungen von internen Seiten *innerhalb der clientseitigen App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1d01d-161">All of the requests for internal pages *within the client-side app* work the same way: Requests don't trigger browser-based requests to server-hosted resources on the Internet.</span></span> <span data-ttu-id="1d01d-162">Der Router verarbeitet Anforderungen intern.</span><span class="sxs-lookup"><span data-stu-id="1d01d-162">The router handles the requests internally.</span></span>

<span data-ttu-id="1d01d-163">Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf.</span><span class="sxs-lookup"><span data-stu-id="1d01d-163">If a request is made using the browser's address bar for `www.contoso.com/About`, the request fails.</span></span> <span data-ttu-id="1d01d-164">Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="1d01d-164">No such resource exists on the app's Internet host, so a *404 - Not Found* response is returned.</span></span>

<span data-ttu-id="1d01d-165">Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite *index.html* erneut generieren.</span><span class="sxs-lookup"><span data-stu-id="1d01d-165">Because browsers make requests to Internet-based hosts for client-side pages, web servers and hosting services must rewrite all requests for resources not physically on the server to the *index.html* page.</span></span> <span data-ttu-id="1d01d-166">Wenn *index.html* zurückgegeben wird, übernimmt der clientseitige Router der App und antwortet mit der richtigen Ressource.</span><span class="sxs-lookup"><span data-stu-id="1d01d-166">When *index.html* is returned, the app's client-side router takes over and responds with the correct resource.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="1d01d-167">Basispfad einer App</span><span class="sxs-lookup"><span data-stu-id="1d01d-167">App base path</span></span>

<span data-ttu-id="1d01d-168">Beim *Basispfad einer App* handelt es sich um den virtuellen Stammpfad der App auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="1d01d-168">The *app base path* is the virtual app root path on the server.</span></span> <span data-ttu-id="1d01d-169">Eine App, die sich beispielsweise auf dem Contoso-Server in einem virtuellen Ordner unter `/CoolApp/` befindet, wird unter `https://www.contoso.com/CoolApp` erreicht, wobei der virtuelle Basispfad `/CoolApp/` lautet.</span><span class="sxs-lookup"><span data-stu-id="1d01d-169">For example, an app that resides on the Contoso server in a virtual folder at `/CoolApp/` is reached at `https://www.contoso.com/CoolApp` and has a virtual base path of `/CoolApp/`.</span></span> <span data-ttu-id="1d01d-170">Indem Sie den Basispfad der App auf den virtuellen Pfad (`<base href="/CoolApp/">`) festlegen, wird der App gezeigt, wo sie sich virtuell auf dem Server befindet.</span><span class="sxs-lookup"><span data-stu-id="1d01d-170">By setting the app base path to the virtual path (`<base href="/CoolApp/">`), the app is made aware of where it virtually resides on the server.</span></span> <span data-ttu-id="1d01d-171">Die App kann den Basispfad der App verwenden, um URLs relativ zum Stammverzeichnis der App über eine Komponente zu erstellen, die sich nicht im Stammverzeichnis befindet.</span><span class="sxs-lookup"><span data-stu-id="1d01d-171">The app can use the app base path to construct URLs relative to the app root from a component that isn't in the root directory.</span></span> <span data-ttu-id="1d01d-172">Dadurch ist es möglich, dass Komponenten, die sich in unterschiedlichen Ebenen der Verzeichnisstruktur befinden, Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen können.</span><span class="sxs-lookup"><span data-stu-id="1d01d-172">This allows components that exist at different levels of the directory structure to build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="1d01d-173">Ferner wird der Basispfad der App verwendet, um Klicks auf Links abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet – um die interne Navigation kümmert sich der Blazor-Router.</span><span class="sxs-lookup"><span data-stu-id="1d01d-173">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="1d01d-174">Bei vielen Hostingszenarios ist der virtuelle Pfad des Servers zur App das Stammverzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="1d01d-174">In many hosting scenarios, the server's virtual path to the app is the root of the app.</span></span> <span data-ttu-id="1d01d-175">In diesen Fällen ist der Basispfad der App ein Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für eine App.</span><span class="sxs-lookup"><span data-stu-id="1d01d-175">In these cases, the app base path is a forward slash (`<base href="/" />`), which is the default configuration for an app.</span></span> <span data-ttu-id="1d01d-176">Bei anderen Hostingszenarios wie etwa bei GitHub-Seiten und virtuellen IIS-Verzeichnissen oder untergeordneten Anwendungen muss der Basispfad der App auf den virtuellen Pfad des Servers zur App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-176">In other hosting scenarios, such as GitHub Pages and IIS virtual directories or sub-applications, the app base path must be set to the server's virtual path to the app.</span></span> <span data-ttu-id="1d01d-177">Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei *wwwroot/index.html*, um den Basispfad der App festzulegen.</span><span class="sxs-lookup"><span data-stu-id="1d01d-177">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="1d01d-178">Legen Sie den `href`-Attributwert auf `/virtual-path/` (der nachgestellte Schrägstrich ist erforderlich) fest, wobei `/virtual-path/` der vollständige virtuelle Stammpfad der App auf dem Server ist.</span><span class="sxs-lookup"><span data-stu-id="1d01d-178">Set the `href` attribute value to `/virtual-path/` (the trailing slash is required), where `/virtual-path/` is the full virtual app root path on the server for the app.</span></span> <span data-ttu-id="1d01d-179">Im vorherigen Beispiel wurde der virtuelle Pfad auf `/CoolApp/` festgelegt: `<base href="/CoolApp/">`.</span><span class="sxs-lookup"><span data-stu-id="1d01d-179">In the preceding example, the virtual path is set to `/CoolApp/`: `<base href="/CoolApp/">`.</span></span>

<span data-ttu-id="1d01d-180">Bei einer App, für die ein virtueller Pfad konfiguriert wurde, der sich nicht im Stammverzeichnis befindet (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*.</span><span class="sxs-lookup"><span data-stu-id="1d01d-180">For an app with a non-root virtual path configured (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="1d01d-181">Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.</span><span class="sxs-lookup"><span data-stu-id="1d01d-181">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span>

<span data-ttu-id="1d01d-182">Führen Sie mithilfe der Option `--pathbase` den Befehl `dotnet run` über das Verzeichnis der App aus, um das Basispfadargument mit dem Stammverzeichnis (`/`) zu übergeben, wenn Sie die App lokal ausführen:</span><span class="sxs-lookup"><span data-stu-id="1d01d-182">To pass the path base argument with the root path (`/`) when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

<span data-ttu-id="1d01d-183">Für eine App mit dem virtuellen Basispfad `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:</span><span class="sxs-lookup"><span data-stu-id="1d01d-183">For an app with a virtual base path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```console
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="1d01d-184">Die App antwortet lokal unter `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="1d01d-184">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

<span data-ttu-id="1d01d-185">Weitere Informationen finden Sie im Abschnitt zum [Hostkonfigurationswert für die Pfadbasis](#path-base).</span><span class="sxs-lookup"><span data-stu-id="1d01d-185">For more information, see the section on the [path base host configuration value](#path-base).</span></span>

<span data-ttu-id="1d01d-186">Wenn eine App das [clientseitige Hostingmodell](xref:blazor/hosting-models#client-side) verwendet (basierend auf der **Blazor**-Projektvorlage; die `blazor`-Vorlage bei Verwendung des [dotnet.new](/dotnet/core/tools/dotnet-new)-Befehls) und als untergeordnete IIS-Anwendung in einer ASP.NET Core-App gehostet wird, muss der geerbte ASP.NET Core-Modulhandler deaktiviert werden, um sicherzustellen, dass der `<handlers>`-Abschnitt der Stammanwendung (bzw. der übergeordneten App) in der *web.config*-Datei von der untergeordneten App nicht geerbt wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-186">If an app uses the [client-side hosting model](xref:blazor/hosting-models#client-side) (based on the **Blazor** project template; the `blazor` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command) and is hosted as an IIS sub-application in an ASP.NET Core app, it's important to disable the inherited ASP.NET Core Module handler or make sure the root (parent) app's `<handlers>` section in the *web.config* file isn't inherited by the sub-app.</span></span>

<span data-ttu-id="1d01d-187">Entfernen Sie den Handler in der veröffentlichen *web.config*-Datei der App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="1d01d-187">Remove the handler in the app's published *web.config* file by adding a `<handlers>` section to the file:</span></span>

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

<span data-ttu-id="1d01d-188">Alternativ können Sie auch die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung) deaktivieren, indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="1d01d-188">Alternatively, disable inheritance of the root (parent) app's `<system.webServer>` section using a `<location>` element with `inheritInChildApplications` set to `false`:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" ... />
      </handlers>
      <aspNetCore ... />
    </system.webServer>
  </location>
</configuration>
```

<span data-ttu-id="1d01d-189">Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zu der in diesem Abschnitt beschriebenen Konfiguration des Basispfads der App durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-189">Removing the handler or disabling inheritance is performed in addition to configuring the app's base path as described in this section.</span></span> <span data-ttu-id="1d01d-190">Legen Sie den Basispfad der App in der *index.html*-Datei der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-190">Set the app base path in the app's *index.html* file to the IIS alias used when configuring the sub-app in IIS.</span></span>

## <a name="hosted-deployment-with-aspnet-core"></a><span data-ttu-id="1d01d-191">Gehostete Bereitstellung mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="1d01d-191">Hosted deployment with ASP.NET Core</span></span>

<span data-ttu-id="1d01d-192">Mit einer *gehosteten Bereitstellung* wird die clientseitige Blazor-App über eine auf einem Server ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-192">A *hosted deployment* serves the client-side Blazor app to browsers from an [ASP.NET Core app](xref:index) that runs on a server.</span></span>

<span data-ttu-id="1d01d-193">Die Blazor-App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-193">The Blazor app is included with the ASP.NET Core app in the published output so that the two apps are deployed together.</span></span> <span data-ttu-id="1d01d-194">Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1d01d-194">A web server that is capable of hosting an ASP.NET Core app is required.</span></span> <span data-ttu-id="1d01d-195">Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor-Projektvorlage (in ASP.NET Core gehostet)** (`blazorhosted`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).</span><span class="sxs-lookup"><span data-stu-id="1d01d-195">For a hosted deployment, Visual Studio includes the **Blazor (ASP.NET Core hosted)** project template (`blazorhosted` template when using the [dotnet new](/dotnet/core/tools/dotnet-new) command).</span></span>

<span data-ttu-id="1d01d-196">Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.</span><span class="sxs-lookup"><span data-stu-id="1d01d-196">For more information on ASP.NET Core app hosting and deployment, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="1d01d-197">Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.</span><span class="sxs-lookup"><span data-stu-id="1d01d-197">For information on deploying to Azure App Service, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

## <a name="standalone-deployment"></a><span data-ttu-id="1d01d-198">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="1d01d-198">Standalone deployment</span></span>

<span data-ttu-id="1d01d-199">Mit einer *eigenständigen Bereitstellung* wird die clientseitige Blazor-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-199">A *standalone deployment* serves the client-side Blazor app as a set of static files that are requested directly by clients.</span></span> <span data-ttu-id="1d01d-200">Jeder statische Dateiserver kann die Blazor-App bedienen.</span><span class="sxs-lookup"><span data-stu-id="1d01d-200">Any static file server is able to serve the Blazor app.</span></span>

<span data-ttu-id="1d01d-201">Eigenständige Bereitstellungsobjekte werden im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="1d01d-201">Standalone deployment assets are published to the *bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span>

### <a name="iis"></a><span data-ttu-id="1d01d-202">IIS</span><span class="sxs-lookup"><span data-stu-id="1d01d-202">IIS</span></span>

<span data-ttu-id="1d01d-203">IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="1d01d-203">IIS is a capable static file server for Blazor apps.</span></span> <span data-ttu-id="1d01d-204">Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS (Erstellen einer statischen Website unter IIS)](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span><span class="sxs-lookup"><span data-stu-id="1d01d-204">To configure IIS to host Blazor, see [Build a Static Website on IIS](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).</span></span>

<span data-ttu-id="1d01d-205">Veröffentlichte Objekte werden im Ordner */bin/Release/{Zielframework}/publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-205">Published assets are created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span> <span data-ttu-id="1d01d-206">Die Inhalte des Ordners *publish* werden auf dem Webserver oder über den Hostingdienst gehostet.</span><span class="sxs-lookup"><span data-stu-id="1d01d-206">Host the contents of the *publish* folder on the web server or hosting service.</span></span>

#### <a name="webconfig"></a><span data-ttu-id="1d01d-207">web.config</span><span class="sxs-lookup"><span data-stu-id="1d01d-207">web.config</span></span>

<span data-ttu-id="1d01d-208">Beim Veröffentlichen eines Blazor-Projekts wird eine *web.config*-Datei mit der folgenden IIS-Konfiguration erstellt:</span><span class="sxs-lookup"><span data-stu-id="1d01d-208">When a Blazor project is published, a *web.config* file is created with the following IIS configuration:</span></span>

* <span data-ttu-id="1d01d-209">Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:</span><span class="sxs-lookup"><span data-stu-id="1d01d-209">MIME types are set for the following file extensions:</span></span>
  * <span data-ttu-id="1d01d-210">*.dll* &ndash; `application/octet-stream`</span><span class="sxs-lookup"><span data-stu-id="1d01d-210">*.dll* &ndash; `application/octet-stream`</span></span>
  * <span data-ttu-id="1d01d-211">*.json* &ndash; `application/json`</span><span class="sxs-lookup"><span data-stu-id="1d01d-211">*.json* &ndash; `application/json`</span></span>
  * <span data-ttu-id="1d01d-212">*.wasm* &ndash; `application/wasm`</span><span class="sxs-lookup"><span data-stu-id="1d01d-212">*.wasm* &ndash; `application/wasm`</span></span>
  * <span data-ttu-id="1d01d-213">*.woff* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1d01d-213">*.woff* &ndash; `application/font-woff`</span></span>
  * <span data-ttu-id="1d01d-214">*.woff2* &ndash; `application/font-woff`</span><span class="sxs-lookup"><span data-stu-id="1d01d-214">*.woff2* &ndash; `application/font-woff`</span></span>
* <span data-ttu-id="1d01d-215">Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:</span><span class="sxs-lookup"><span data-stu-id="1d01d-215">HTTP compression is enabled for the following MIME types:</span></span>
  * `application/octet-stream`
  * `application/wasm`
* <span data-ttu-id="1d01d-216">URL-Rewrite-Modul-Regeln werden eingerichtet:</span><span class="sxs-lookup"><span data-stu-id="1d01d-216">URL Rewrite Module rules are established:</span></span>
  * <span data-ttu-id="1d01d-217">Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden ( *{ASSEMBLYNAME}/dist/{ANGEFORDERTER PFAD}* ).</span><span class="sxs-lookup"><span data-stu-id="1d01d-217">Serve the sub-directory where the app's static assets reside (*{ASSEMBLY NAME}/dist/{PATH REQUESTED}*).</span></span>
  * <span data-ttu-id="1d01d-218">Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte ( *{ASSEMBLYNAME}/dist/index.html*) umgeleitet werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-218">Create SPA fallback routing so that requests for non-file assets are redirected to the app's default document in its static assets folder (*{ASSEMBLY NAME}/dist/index.html*).</span></span>

#### <a name="install-the-url-rewrite-module"></a><span data-ttu-id="1d01d-219">Installieren des URL-Rewrite-Moduls</span><span class="sxs-lookup"><span data-stu-id="1d01d-219">Install the URL Rewrite Module</span></span>

<span data-ttu-id="1d01d-220">Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-220">The [URL Rewrite Module](https://www.iis.net/downloads/microsoft/url-rewrite) is required to rewrite URLs.</span></span> <span data-ttu-id="1d01d-221">Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1d01d-221">The module isn't installed by default, and it isn't available for install as a Web Server (IIS) role service feature.</span></span> <span data-ttu-id="1d01d-222">Das Modul muss von der IIS-Website heruntergeladen werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-222">The module must be downloaded from the IIS website.</span></span> <span data-ttu-id="1d01d-223">Verwenden Sie den Webplattform-Installer zur Installation des Moduls:</span><span class="sxs-lookup"><span data-stu-id="1d01d-223">Use the Web Platform Installer to install the module:</span></span>

1. <span data-ttu-id="1d01d-224">Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span><span class="sxs-lookup"><span data-stu-id="1d01d-224">Locally, navigate to the [URL Rewrite Module downloads page](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads).</span></span> <span data-ttu-id="1d01d-225">Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus.</span><span class="sxs-lookup"><span data-stu-id="1d01d-225">For the English version, select **WebPI** to download the WebPI installer.</span></span> <span data-ttu-id="1d01d-226">Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.</span><span class="sxs-lookup"><span data-stu-id="1d01d-226">For other languages, select the appropriate architecture for the server (x86/x64) to download the installer.</span></span>
1. <span data-ttu-id="1d01d-227">Kopieren Sie den Installer auf den Server.</span><span class="sxs-lookup"><span data-stu-id="1d01d-227">Copy the installer to the server.</span></span> <span data-ttu-id="1d01d-228">Führen Sie den Installer aus.</span><span class="sxs-lookup"><span data-stu-id="1d01d-228">Run the installer.</span></span> <span data-ttu-id="1d01d-229">Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu.</span><span class="sxs-lookup"><span data-stu-id="1d01d-229">Select the **Install** button and accept the license terms.</span></span> <span data-ttu-id="1d01d-230">Der Server muss nach Abschluss der Installation nicht neu gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-230">A server restart isn't required after the install completes.</span></span>

#### <a name="configure-the-website"></a><span data-ttu-id="1d01d-231">Konfigurieren der Website</span><span class="sxs-lookup"><span data-stu-id="1d01d-231">Configure the website</span></span>

<span data-ttu-id="1d01d-232">Legen Sie für den **physischen Pfad** der Website den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="1d01d-232">Set the website's **Physical path** to the app's folder.</span></span> <span data-ttu-id="1d01d-233">Der Ordner enthält Folgendes:</span><span class="sxs-lookup"><span data-stu-id="1d01d-233">The folder contains:</span></span>

* <span data-ttu-id="1d01d-234">Die Datei *web.config*, die von IIS zum Konfigurieren der Website verwendet wird, mit den erforderlichen Umleitungsregeln und Dateiinhaltstypen</span><span class="sxs-lookup"><span data-stu-id="1d01d-234">The *web.config* file that IIS uses to configure the website, including the required redirect rules and file content types.</span></span>
* <span data-ttu-id="1d01d-235">Den Ordner der App für statische Objekte</span><span class="sxs-lookup"><span data-stu-id="1d01d-235">The app's static asset folder.</span></span>

#### <a name="troubleshooting"></a><span data-ttu-id="1d01d-236">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="1d01d-236">Troubleshooting</span></span>

<span data-ttu-id="1d01d-237">Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist.</span><span class="sxs-lookup"><span data-stu-id="1d01d-237">If a *500 - Internal Server Error* is received and IIS Manager throws errors when attempting to access the website's configuration, confirm that the URL Rewrite Module is installed.</span></span> <span data-ttu-id="1d01d-238">Wenn das Modul nicht installiert ist, kann die Datei *web.config* von IIS nicht analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-238">When the module isn't installed, the *web.config* file can't be parsed by IIS.</span></span> <span data-ttu-id="1d01d-239">Dadurch wird verhindert, dass die Konfiguration der Website von IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.</span><span class="sxs-lookup"><span data-stu-id="1d01d-239">This prevents the IIS Manager from loading the website's configuration and the website from serving Blazor's static files.</span></span>

<span data-ttu-id="1d01d-240">Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="1d01d-240">For more information on troubleshooting deployments to IIS, see <xref:host-and-deploy/iis/troubleshoot>.</span></span>

### <a name="azure-storage"></a><span data-ttu-id="1d01d-241">Azure Storage</span><span class="sxs-lookup"><span data-stu-id="1d01d-241">Azure Storage</span></span>

<span data-ttu-id="1d01d-242">Die Hosting statischer Dateien von Azure Storage ermöglicht das serverlose Blazor-App-Hosting.</span><span class="sxs-lookup"><span data-stu-id="1d01d-242">Azure Storage static file hosting allows serverless Blazor app hosting.</span></span> <span data-ttu-id="1d01d-243">Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="1d01d-243">Custom domain names, the Azure Content Delivery Network (CDN), and HTTPS are supported.</span></span>

<span data-ttu-id="1d01d-244">Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:</span><span class="sxs-lookup"><span data-stu-id="1d01d-244">When the blob service is enabled for static website hosting on a storage account:</span></span>

* <span data-ttu-id="1d01d-245">Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="1d01d-245">Set the **Index document name** to `index.html`.</span></span>
* <span data-ttu-id="1d01d-246">Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest.</span><span class="sxs-lookup"><span data-stu-id="1d01d-246">Set the **Error document path** to `index.html`.</span></span> <span data-ttu-id="1d01d-247">Razor-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-247">Razor Components and other non-file endpoints don't reside at physical paths in the static content stored by the blob service.</span></span> <span data-ttu-id="1d01d-248">Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blobdienst generierte Fehler *404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter.</span><span class="sxs-lookup"><span data-stu-id="1d01d-248">When a request for one of these resources is received that the Blazor router should handle, the *404 - Not Found* error generated by the blob service routes the request to the **Error document path**.</span></span> <span data-ttu-id="1d01d-249">Das Blob *index.html* wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.</span><span class="sxs-lookup"><span data-stu-id="1d01d-249">The *index.html* blob is returned, and the Blazor router loads and processes the path.</span></span>

<span data-ttu-id="1d01d-250">Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span><span class="sxs-lookup"><span data-stu-id="1d01d-250">For more information, see [Static website hosting in Azure Storage](/azure/storage/blobs/storage-blob-static-website).</span></span>

### <a name="nginx"></a><span data-ttu-id="1d01d-251">Nginx</span><span class="sxs-lookup"><span data-stu-id="1d01d-251">Nginx</span></span>

<span data-ttu-id="1d01d-252">Die folgende *nginx.conf*-Datei wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei *Index.html* gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-252">The following *nginx.conf* file is simplified to show how to configure Nginx to send the *index.html* file whenever it can't find a corresponding file on disk.</span></span>

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

<span data-ttu-id="1d01d-253">Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span><span class="sxs-lookup"><span data-stu-id="1d01d-253">For more information on production Nginx web server configuration, see [Creating NGINX Plus and NGINX Configuration Files](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).</span></span>

### <a name="nginx-in-docker"></a><span data-ttu-id="1d01d-254">Nginx in Docker</span><span class="sxs-lookup"><span data-stu-id="1d01d-254">Nginx in Docker</span></span>

<span data-ttu-id="1d01d-255">Richten Sie zum Hosten von Blazor in Docker mithilfe von Nginx die Dockerfile für die Verwendung des auf Alpine basierenden Nginx-Images ein.</span><span class="sxs-lookup"><span data-stu-id="1d01d-255">To host Blazor in Docker using Nginx, setup the Dockerfile to use the Alpine-based Nginx image.</span></span> <span data-ttu-id="1d01d-256">Aktualisieren Sie die Dockerfile zum Kopieren der Datei *nginx.config* in den Container.</span><span class="sxs-lookup"><span data-stu-id="1d01d-256">Update the Dockerfile to copy the *nginx.config* file into the container.</span></span>

<span data-ttu-id="1d01d-257">Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:</span><span class="sxs-lookup"><span data-stu-id="1d01d-257">Add one line to the Dockerfile, as shown in the following example:</span></span>

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a><span data-ttu-id="1d01d-258">GitHub-Seiten</span><span class="sxs-lookup"><span data-stu-id="1d01d-258">GitHub Pages</span></span>

<span data-ttu-id="1d01d-259">Fügen Sie zum Verarbeiten von URL-Umschreibungen eine *404.html*-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite *index.html* verarbeitet wird.</span><span class="sxs-lookup"><span data-stu-id="1d01d-259">To handle URL rewrites, add a *404.html* file with a script that handles redirecting the request to the *index.html* page.</span></span> <span data-ttu-id="1d01d-260">Ein Beispiel für eine Implementierung, das von der Community bereitgestellt wurde, finden Sie unter [Single Page Apps for GitHub Pages (Single-Page-Apps für GitHub Pages)](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages auf GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span><span class="sxs-lookup"><span data-stu-id="1d01d-260">For an example implementation provided by the community, see [Single Page Apps for GitHub Pages](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages on GitHub](https://github.com/rafrex/spa-github-pages#readme)).</span></span> <span data-ttu-id="1d01d-261">Ein Beispiel für die Verwendung des Community-Ansatzes finden Sie unter [blazor-demo/blazor-demo.github.io auf GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([Livewebsite](https://blazor-demo.github.io/)).</span><span class="sxs-lookup"><span data-stu-id="1d01d-261">An example using the community approach can be seen at [blazor-demo/blazor-demo.github.io on GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([live site](https://blazor-demo.github.io/)).</span></span>

<span data-ttu-id="1d01d-262">Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, fügen Sie der Datei *index.html* das `<base>`-Tag hinzu, oder aktualisieren Sie das Tag in der Datei.</span><span class="sxs-lookup"><span data-stu-id="1d01d-262">When using a project site instead of an organization site, add or update the `<base>` tag in *index.html*.</span></span> <span data-ttu-id="1d01d-263">Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Slash fest (z.B. `my-repository/`).</span><span class="sxs-lookup"><span data-stu-id="1d01d-263">Set the `href` attribute value to the GitHub repository name with a trailing slash (for example, `my-repository/`.</span></span>
