---
title: Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/23/2019
no-loc:
- Blazor
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 5c37c3d9f424f4c4b814e1955880623fd95179f2
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550372"
---
# <a name="host-and-deploy-aspnet-core-opno-locblazor"></a><span data-ttu-id="582a0-103">Hosten und Bereitstellen von ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="582a0-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="582a0-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="582a0-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="publish-the-app"></a><span data-ttu-id="582a0-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="582a0-105">Publish the app</span></span>

<span data-ttu-id="582a0-106">Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="582a0-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="582a0-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="582a0-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="582a0-108">Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.</span><span class="sxs-lookup"><span data-stu-id="582a0-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="582a0-109">Wählen Sie das *Veröffentlichungsziel* aus.</span><span class="sxs-lookup"><span data-stu-id="582a0-109">Select the *publish target*.</span></span> <span data-ttu-id="582a0-110">Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="582a0-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="582a0-111">Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an.</span><span class="sxs-lookup"><span data-stu-id="582a0-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="582a0-112">Wählen Sie die Schaltfläche **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="582a0-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="582a0-113">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="582a0-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="582a0-114">Verwenden Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um die App mit einer Releasekonfiguration zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="582a0-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```dotnetcli
dotnet publish -c Release
```

---

<span data-ttu-id="582a0-115">Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="582a0-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="582a0-116">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="582a0-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="582a0-117">Eine Blazor WebAssembly-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="582a0-117">A Blazor WebAssembly app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="582a0-118">Eine Blazor Server-App wird im Ordner */bin/Release/{TARGET FRAMEWORK}/publish* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="582a0-118">A Blazor Server app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="582a0-119">Die Objekte im Ordner werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="582a0-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="582a0-120">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="582a0-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="app-base-path"></a><span data-ttu-id="582a0-121">Basispfad einer App</span><span class="sxs-lookup"><span data-stu-id="582a0-121">App base path</span></span>

<span data-ttu-id="582a0-122">Der *Basispfad einer App* beschreibt den URL-Stammpfad der App.</span><span class="sxs-lookup"><span data-stu-id="582a0-122">The *app base path* is the app's root URL path.</span></span> <span data-ttu-id="582a0-123">Betrachten Sie die folgende ASP.NET Core-App und die untergeordnete Blazor-App:</span><span class="sxs-lookup"><span data-stu-id="582a0-123">Consider the following ASP.NET Core app and Blazor sub-app:</span></span>

* <span data-ttu-id="582a0-124">Die ASP.NET Core-App heißt `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="582a0-124">The ASP.NET Core app is named `MyApp`:</span></span>
  * <span data-ttu-id="582a0-125">Die App befindet sich physisch unter *d:/MyApp*.</span><span class="sxs-lookup"><span data-stu-id="582a0-125">The app physically resides at *d:/MyApp*.</span></span>
  * <span data-ttu-id="582a0-126">Anforderungen werden über `https://www.contoso.com/{MYAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="582a0-126">Requests are received at `https://www.contoso.com/{MYAPP RESOURCE}`.</span></span>
* <span data-ttu-id="582a0-127">Eine Blazor-App mit dem Namen `CoolApp` ist die untergeordnete App von `MyApp`:</span><span class="sxs-lookup"><span data-stu-id="582a0-127">A Blazor app named `CoolApp` is a sub-app of `MyApp`:</span></span>
  * <span data-ttu-id="582a0-128">Die untergeordnete App befindet sich physisch unter *d:/MyApp/CoolApp*.</span><span class="sxs-lookup"><span data-stu-id="582a0-128">The sub-app physically resides at *d:/MyApp/CoolApp*.</span></span>
  * <span data-ttu-id="582a0-129">Anforderungen werden über `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}` empfangen.</span><span class="sxs-lookup"><span data-stu-id="582a0-129">Requests are received at `https://www.contoso.com/CoolApp/{COOLAPP RESOURCE}`.</span></span>

<span data-ttu-id="582a0-130">Wenn keine zusätzlichen Konfiguration für `CoolApp` festgelegt wird, weiß die untergeordnete App in diesem Szenario nicht, wo sie sich auf dem Server befindet.</span><span class="sxs-lookup"><span data-stu-id="582a0-130">Without specifying additional configuration for `CoolApp`, the sub-app in this scenario has no knowledge of where it resides on the server.</span></span> <span data-ttu-id="582a0-131">Beispielsweise kann die App keine richtigen relativen URLs zu ihren Ressourcen erstellen, ohne zu wissen, dass sie sich unter dem relativen URL-Pfad `/CoolApp/` befindet.</span><span class="sxs-lookup"><span data-stu-id="582a0-131">For example, the app can't construct correct relative URLs to its resources without knowing that it resides at the relative URL path `/CoolApp/`.</span></span>

<span data-ttu-id="582a0-132">Das `href`-Attribut des Tags `<base>` wird auf den relativen Stammpfad in der Datei *Pages/_Host.cshtml* (Blazor Server) oder *wwwroot/index.html* (Blazor WebAssembly) festgelegt, um die Konfiguration für den Basispfad `https://www.contoso.com/CoolApp/` der Blazor-App anzugeben:</span><span class="sxs-lookup"><span data-stu-id="582a0-132">To provide configuration for the Blazor app's base path of `https://www.contoso.com/CoolApp/`, the `<base>` tag's `href` attribute is set to the relative root path in the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly):</span></span>

```html
<base href="/CoolApp/">
```

Blazor<span data-ttu-id="582a0-133"> Server-Apps legen zusätzlich den serverseitigen Basispfad fest, indem sie <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in der Anforderungspipeline `Startup.Configure` der App aufrufen.</span><span class="sxs-lookup"><span data-stu-id="582a0-133"> Server apps additionally set the server-side base path by calling <xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*> in the app's request pipeline of `Startup.Configure`:</span></span>

```csharp
app.UsePathBase("/CoolApp");
```

<span data-ttu-id="582a0-134">Indem der relative URL-Pfad angegeben wird, kann eine Komponente, die sich nicht im Stammverzeichnis befindet, URLs relativ zum Stammpfad der App erstellen.</span><span class="sxs-lookup"><span data-stu-id="582a0-134">By providing the relative URL path, a component that isn't in the root directory can construct URLs relative to the app's root path.</span></span> <span data-ttu-id="582a0-135">Komponenten auf verschiedenen Verzeichnisstrukturebenen können Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen.</span><span class="sxs-lookup"><span data-stu-id="582a0-135">Components at different levels of the directory structure can build links to other resources at locations throughout the app.</span></span> <span data-ttu-id="582a0-136">Ferner wird der Basispfad der App verwendet, um ausgewählte Hyperlinks abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet.</span><span class="sxs-lookup"><span data-stu-id="582a0-136">The app base path is also used to intercept selected hyperlinks where the `href` target of the link is within the app base path URI space.</span></span> <span data-ttu-id="582a0-137">Der Router Blazor verarbeitet interne Navigation.</span><span class="sxs-lookup"><span data-stu-id="582a0-137">The Blazor router handles the internal navigation.</span></span>

<span data-ttu-id="582a0-138">Bei vielen Hostingszenarios ist der relative URL-Pfad des Servers zur App das Stammverzeichnis der App.</span><span class="sxs-lookup"><span data-stu-id="582a0-138">In many hosting scenarios, the relative URL path to the app is the root of the app.</span></span> <span data-ttu-id="582a0-139">In diesen Fällen handelt es sich beim relativen URL-Basispfad um einen Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für Blazor-Apps.</span><span class="sxs-lookup"><span data-stu-id="582a0-139">In these cases, the app's relative URL base path is a forward slash (`<base href="/" />`), which is the default configuration for a Blazor app.</span></span> <span data-ttu-id="582a0-140">Bei anderen Hostingszenarios, z. B. bei GitHub-Seiten und untergeordneten IIS-Apps, muss der Basispfad der App auf den relativen URL-Pfad des Servers der App festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="582a0-140">In other hosting scenarios, such as GitHub Pages and IIS sub-apps, the app base path must be set to the server's relative URL path of the app.</span></span>

<span data-ttu-id="582a0-141">Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei *Pages/_Host.cshtml* (Blazor Server) oder *wwwroot/index.html* (Blazor WebAssembly), um den Basispfad der App festzulegen.</span><span class="sxs-lookup"><span data-stu-id="582a0-141">To set the app's base path, update the `<base>` tag within the `<head>` tag elements of the *Pages/_Host.cshtml* file (Blazor Server) or *wwwroot/index.html* file (Blazor WebAssembly).</span></span> <span data-ttu-id="582a0-142">Legen Sie das `href`-Attribut auf `/{RELATIVE URL PATH}/` fest (der nachstehende Schrägstrich ist erforderlich). `{RELATIVE URL PATH}` entspricht hier dem vollständigen relativen URL-Pfad der App.</span><span class="sxs-lookup"><span data-stu-id="582a0-142">Set the `href` attribute value to `/{RELATIVE URL PATH}/` (the trailing slash is required), where `{RELATIVE URL PATH}` is the app's full relative URL path.</span></span>

<span data-ttu-id="582a0-143">Bei einer Blazor WebAssembly-App mit einem relativen URL-Pfad, der sich nicht im Stammverzeichnis befindet (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*.</span><span class="sxs-lookup"><span data-stu-id="582a0-143">For an Blazor WebAssembly app with a non-root relative URL path (for example, `<base href="/CoolApp/">`), the app fails to find its resources *when run locally*.</span></span> <span data-ttu-id="582a0-144">Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.</span><span class="sxs-lookup"><span data-stu-id="582a0-144">To overcome this problem during local development and testing, you can supply a *path base* argument that matches the `href` value of the `<base>` tag at runtime.</span></span> <span data-ttu-id="582a0-145">Fügen Sie keinen nachgestellten Schrägstrich ein.</span><span class="sxs-lookup"><span data-stu-id="582a0-145">Don't include a trailing slash.</span></span> <span data-ttu-id="582a0-146">Führen Sie den Befehl `dotnet run` aus dem Verzeichnis der App mit der `--pathbase`-Option aus, um das Basispfadargument beim lokalen Ausführen der App zu übergeben:</span><span class="sxs-lookup"><span data-stu-id="582a0-146">To pass the path base argument when running the app locally, execute the `dotnet run` command from the app's directory with the `--pathbase` option:</span></span>

```dotnetcli
dotnet run --pathbase=/{RELATIVE URL PATH (no trailing slash)}
```

<span data-ttu-id="582a0-147">Für eine Blazor WebAssembly-App mit einem relativen URL-Pfad von `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:</span><span class="sxs-lookup"><span data-stu-id="582a0-147">For a Blazor WebAssembly app with a relative URL path of `/CoolApp/` (`<base href="/CoolApp/">`), the command is:</span></span>

```dotnetcli
dotnet run --pathbase=/CoolApp
```

<span data-ttu-id="582a0-148">Die Blazor WebAssembly-App antwortet lokal unter `http://localhost:port/CoolApp`.</span><span class="sxs-lookup"><span data-stu-id="582a0-148">The Blazor WebAssembly app responds locally at `http://localhost:port/CoolApp`.</span></span>

## <a name="deployment"></a><span data-ttu-id="582a0-149">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="582a0-149">Deployment</span></span>

<span data-ttu-id="582a0-150">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="582a0-150">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/webassembly>
* <xref:host-and-deploy/blazor/server>
