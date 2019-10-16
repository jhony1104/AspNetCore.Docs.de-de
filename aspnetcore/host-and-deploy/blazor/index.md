---
title: Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 271135a0ebe67d31fd8e2bcf672e723814727147
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391338"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="a059a-103">Hosten und Bereitstellen von ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="a059a-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="a059a-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="a059a-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="a059a-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="a059a-105">Publish the app</span></span>

<span data-ttu-id="a059a-106">Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="a059a-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a059a-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a059a-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="a059a-108">Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.</span><span class="sxs-lookup"><span data-stu-id="a059a-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="a059a-109">Wählen Sie das *Veröffentlichungsziel* aus.</span><span class="sxs-lookup"><span data-stu-id="a059a-109">Select the *publish target*.</span></span> <span data-ttu-id="a059a-110">Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="a059a-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="a059a-111">Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an.</span><span class="sxs-lookup"><span data-stu-id="a059a-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="a059a-112">Wählen Sie die Schaltfläche **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="a059a-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a059a-113">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="a059a-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a059a-114">Verwenden Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um die App mit einer Releasekonfiguration zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="a059a-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="a059a-115">Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="a059a-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="a059a-116">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="a059a-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="a059a-117">Eine Blazor WebAssembly-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="a059a-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="a059a-118">Eine Blazor Server-App wird im Ordner */bin/Release/{TARGET FRAMEWORK}/publish* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="a059a-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="a059a-119">Die Objekte im Ordner werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a059a-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="a059a-120">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="a059a-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="a059a-121">Basispfad einer App</span><span class="sxs-lookup"><span data-stu-id="a059a-121">App base path</span></span>

<span data-ttu-id="a059a-122">Der *Basispfad einer App* beschreibt den URL-Stammpfad der App.</span><span class="sxs-lookup"><span data-stu-id="a059a-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="a059a-123">Stellen Sie sich die folgenden Haupt- und Blazor-Apps vor:</span><span class="sxs-lookup"><span data-stu-id="a059a-123">Consider the following main app and Blazor app:</span></span>

* <span data-ttu-id="a059a-124">Die Haupt-App heißt `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="a059a-124">The main app is called `MyApp`:</span></span>
  * <span data-ttu-id="a059a-125">Die App befindet sich physisch unter *d:\\MyApp*.</span><span class="sxs-lookup"><span data-stu-id="a059a-125">The app physically resides at *d:\\MyApp*.</span></span>
  * <span data-ttu-id="a059a-126">Anforderungen werden über `https://www.contoso.com/{MYAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="a059a-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="a059a-127">Eine Blazor-App namens `CoolApp` fungiert als untergeordnete App von `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="a059a-127">A Blazor app called `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="a059a-128">Die untergeordnete App befindet sich physisch unter *d:\\MyApp\\CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="a059a-128">The sub-app physically resides at *d:\\MyApp\\CoolApp*.</span></span>
  * <span data-ttu-id="a059a-129">Anforderungen werden über `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="a059a-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="a059a-130">Wenn keine zusätzlichen Konfiguration für `CoolApp` festgelegt wird, weiß die untergeordnete App in diesem Szenario nicht, wo sie sich auf dem Server befindet.</span><span class="sxs-lookup"><span data-stu-id="a059a-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="a059a-131">Beispielsweise kann die App keine richtigen relativen URLs zu ihren Ressourcen erstellen, ohne zu wissen, dass sie sich unter dem relativen URL-Pfad `/CoolApp/` befindet.</span><span class="sxs-lookup"><span data-stu-id="a059a-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="a059a-132">Das `href`-Attribut des Tags `<base>` wird auf den relativen Stammpfad in der Datei *wwwroot/index.html* festgelegt, um die Konfiguration für den Basispfad `https://www.contoso.com/CoolApp/` der Blazor-App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="a059a-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *wwwroot/index.html* file:</span></span>

```html
<base href="/CoolApp/">
```

<span data-ttu-id="a059a-133">Indem der relative URL-Pfad angegeben wird, kann eine Komponente, die sich nicht im Stammverzeichnis befindet, URLs relativ zum Stammpfad der App erstellen.</span><span class="sxs-lookup"><span data-stu-id="a059a-133">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="a059a-134">Komponenten auf verschiedenen Verzeichnisstrukturebenen können Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen.</span><span class="sxs-lookup"><span data-stu-id="a059a-134">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="a059a-135">Ferner wird der Basispfad der App verwendet, um Klicks auf Links abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet – um die interne Navigation kümmert sich der Blazor-Router.</span><span class="sxs-lookup"><span data-stu-id="a059a-135">The app base path is also used to intercept hyperlink clicks where the `href` target of the link is within the app base path URI space&mdash;the Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="a059a-136">Bei vielen Hostingszenarios ist der relative URL-Pfad des Servers zur App das Stammverzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="a059a-136">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="a059a-137">In diesen Fällen handelt es sich beim relativen URL-Basispfad um einen Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="a059a-137">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="a059a-138">Bei anderen Hostingszenarios, z. B. bei GitHub-Seiten und untergeordneten IIS-Apps, muss der Basispfad der App auf den relativen URL-Pfad des Servers zur App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="a059a-138">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path to the app.</span></span>

<span data-ttu-id="a059a-139">Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei *wwwroot/index.html*, um den Basispfad der App festzulegen.</span><span class="sxs-lookup"><span data-stu-id="a059a-139">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *wwwroot/index.html* file.</span></span> <span data-ttu-id="a059a-140">Legen Sie das `href`-Attribut auf `/{RELATIVE URL PATH}/` fest (der nachstehende Schrägstrich ist erforderlich). `{RELATIVE URL PATH}` entspricht hier dem vollständigen relativen URL-Pfad der App.</span><span class="sxs-lookup"><span data-stu-id="a059a-140">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="a059a-141">Bei einer App mit einem relativen URL-Pfad, der sich nicht im Stammverzeichnis befindet (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*.</span><span class="sxs-lookup"><span data-stu-id="a059a-141">For an app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="a059a-142">Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.</span><span class="sxs-lookup"><span data-stu-id="a059a-142">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="a059a-143">Führen Sie den Befehl `dotnet run` aus dem Verzeichnis der App mit der `--pathbase`-Option aus, um das Basispfadargument beim lokalen Ausführen der App zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="a059a-143">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="a059a-144">Für eine App mit einem relativen URL-Pfad von `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:</span><span class="sxs-lookup"><span data-stu-id="a059a-144">For an app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="a059a-145">Die App antwortet lokal unter `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="a059a-145">The app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="a059a-146">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="a059a-146">Deployment</span></span>

<span data-ttu-id="a059a-147">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="a059a-147">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
