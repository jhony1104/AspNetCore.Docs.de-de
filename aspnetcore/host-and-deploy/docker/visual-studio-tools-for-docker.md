---
title: Visual Studio-Containertools mit ASP.NET Core
author: spboyer
description: Informationen zum Verwenden der Visual Studio-Tools und Docker für Windows, um Ihre ASP.NET Core-App in Container zu packen.
ms.author: scaddie
ms.custom: mvc
ms.date: 09/12/2018
uid: host-and-deploy/docker/visual-studio-tools-for-docker
ms.openlocfilehash: cecec09d3d58a84a7c15630e41d23facdb9b236e
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75356150"
---
# <a name="visual-studio-container-tools-with-aspnet-core"></a><span data-ttu-id="43826-103">Visual Studio-Containertools mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="43826-103">Visual Studio Container Tools with ASP.NET Core</span></span>

<span data-ttu-id="43826-104">Visual Studio 2017 und höhere Versionen unterstützen das Erstellen, Debuggen und Ausführen von ASP.NET Core-Container-Apps, die für .NET Core entwickelt werden.</span><span class="sxs-lookup"><span data-stu-id="43826-104">Visual Studio 2017 and later versions support building, debugging, and running containerized ASP.NET Core apps targeting .NET Core.</span></span> <span data-ttu-id="43826-105">Sowohl Windows- als auch Linux-Container werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="43826-105">Both Windows and Linux containers are supported.</span></span>

<span data-ttu-id="43826-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="43826-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/docker/visual-studio-tools-for-docker/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="43826-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="43826-107">Prerequisites</span></span>

* [<span data-ttu-id="43826-108">Docker für Windows</span><span class="sxs-lookup"><span data-stu-id="43826-108">Docker for Windows</span></span>](https://docs.docker.com/docker-for-windows/install/)
* <span data-ttu-id="43826-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload für die **plattformübergreifende .NET Core-Entwicklung**.</span><span class="sxs-lookup"><span data-stu-id="43826-109">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **.NET Core cross-platform development** workload</span></span>

## <a name="installation-and-setup"></a><span data-ttu-id="43826-110">Installation und Einrichtung</span><span class="sxs-lookup"><span data-stu-id="43826-110">Installation and setup</span></span>

<span data-ttu-id="43826-111">Lesen Sie vor der Installation von Docker zunächst [Docker for Windows: What to know before you install (Docker Desktop für Windows: Was vor der Installation zu beachten ist)](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span><span class="sxs-lookup"><span data-stu-id="43826-111">For Docker installation, first review the information at [Docker for Windows: What to know before you install](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).</span></span> <span data-ttu-id="43826-112">Installieren Sie anschließend [Docker für Windows](https://docs.docker.com/docker-for-windows/install/).</span><span class="sxs-lookup"><span data-stu-id="43826-112">Next, install [Docker For Windows](https://docs.docker.com/docker-for-windows/install/).</span></span>

<span data-ttu-id="43826-113">**[Freigegebene Laufwerke](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker für Windows müssen so konfiguriert sein, dass sie die Volumezuordnung und das Debuggen unterstützen.</span><span class="sxs-lookup"><span data-stu-id="43826-113">**[Shared Drives](https://docs.docker.com/docker-for-windows/#shared-drives)** in Docker for Windows must be configured to support volume mapping and debugging.</span></span> <span data-ttu-id="43826-114">Klicken Sie mit der rechten Maustaste in der Taskleiste auf das Docker-Symbol, und rufen Sie anschließend **Einstellungen** > **Freigegebene Laufwerke** aus.</span><span class="sxs-lookup"><span data-stu-id="43826-114">Right-click the System Tray's Docker icon, select **Settings**, and select **Shared Drives**.</span></span> <span data-ttu-id="43826-115">Wählen Sie das Laufwerk aus, auf dem Docker Dateien speichert.</span><span class="sxs-lookup"><span data-stu-id="43826-115">Select the drive where Docker stores files.</span></span> <span data-ttu-id="43826-116">Klicken Sie auf **Übernehmen**.</span><span class="sxs-lookup"><span data-stu-id="43826-116">Click **Apply**.</span></span>

![Dialogfeld zur Auswahl des lokalen Laufwerks C, das für Container freigegeben werden soll](visual-studio-tools-for-docker/_static/settings-shared-drives-win.png)

> [!TIP]
> <span data-ttu-id="43826-118">Visual Studio 2017 Version 15.6 und höher zeigt später eine Eingabeaufforderung an, wenn die **Freigegebenen Laufwerke** nicht konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="43826-118">Visual Studio 2017 versions 15.6 and later prompt when **Shared Drives** aren't configured.</span></span>

## <a name="add-a-project-to-a-docker-container"></a><span data-ttu-id="43826-119">Hinzufügen eines Projekts zu einem Docker-Container</span><span class="sxs-lookup"><span data-stu-id="43826-119">Add a project to a Docker container</span></span>

<span data-ttu-id="43826-120">Zur Containerisierung eines ASP.NET Core-Projekts muss das Projekt .NET Core als Zielplattform verwenden.</span><span class="sxs-lookup"><span data-stu-id="43826-120">To containerize an ASP.NET Core project, the project must target .NET Core.</span></span> <span data-ttu-id="43826-121">Sowohl Linux- als auch Windows-Container werden unterstützt.</span><span class="sxs-lookup"><span data-stu-id="43826-121">Both Linux and Windows containers are supported.</span></span>

<span data-ttu-id="43826-122">Wenn Sie Docker-Unterstützung zu einem Projekt hinzufügen, können Sie zwischen einem Windows- oder einem Linux-Container auswählen.</span><span class="sxs-lookup"><span data-stu-id="43826-122">When adding Docker support to a project, choose either a Windows or a Linux container.</span></span> <span data-ttu-id="43826-123">Der Docker-Host muss den gleichen Containertyp ausführen.</span><span class="sxs-lookup"><span data-stu-id="43826-123">The Docker host must be running the same container type.</span></span> <span data-ttu-id="43826-124">Wenn Sie den Containertyp in der ausgeführten Docker-Instanz ändern möchten, klicken Sie mit der rechten Maustaste auf der Taskleiste auf das Docker-Symbol, und wählen Sie **Switch to Windows containers** (Zu Windows-Containern wechseln) oder **Switch to Linux container** (Zu Linux-Containern wechseln) aus.</span><span class="sxs-lookup"><span data-stu-id="43826-124">To change the container type in the running Docker instance, right-click the System Tray's Docker icon and choose **Switch to Windows containers...** or **Switch to Linux containers...**.</span></span>

### <a name="new-app"></a><span data-ttu-id="43826-125">Neue App</span><span class="sxs-lookup"><span data-stu-id="43826-125">New app</span></span>

<span data-ttu-id="43826-126">Wenn Sie mithilfe der Projektvorlage **ASP.NET Core-Webanwendung** eine neue App erstellen, aktivieren Sie das Kontrollkästchen **Enable Docker Support** (Docker-Unterstützung aktivieren):</span><span class="sxs-lookup"><span data-stu-id="43826-126">When creating a new app with the **ASP.NET Core Web Application** project templates, select the **Enable Docker Support** check box:</span></span>

![Kontrollkästchen „Enable Docker Support“ (Docker-Unterstützung aktivieren)](visual-studio-tools-for-docker/_static/enable-docker-support-check-box.png)

<span data-ttu-id="43826-128">Wenn .NET Core das Zielframework ist, können Sie in der Dropdownliste **OS** (Betriebssystem) einen Containertyp auswählen.</span><span class="sxs-lookup"><span data-stu-id="43826-128">If the target framework is .NET Core, the **OS** drop-down allows for the selection of a container type.</span></span>

### <a name="existing-app"></a><span data-ttu-id="43826-129">Vorhandene App</span><span class="sxs-lookup"><span data-stu-id="43826-129">Existing app</span></span>

<span data-ttu-id="43826-130">Es gibt für ASP.NET Core-Projekte, die auf .NET Core abzielen, zwei Möglichkeiten, Docker-Unterstützung über Tools hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="43826-130">For ASP.NET Core projects targeting .NET Core, there are two options for adding Docker support via the tooling.</span></span> <span data-ttu-id="43826-131">Öffnen Sie das Projekt in Visual Studio, und wählen Sie eine der folgenden Optionen aus:</span><span class="sxs-lookup"><span data-stu-id="43826-131">Open the project in Visual Studio, and choose one of the following options:</span></span>

* <span data-ttu-id="43826-132">Wählen Sie aus dem **Projektmenü** **Docker-Unterstützung** aus.</span><span class="sxs-lookup"><span data-stu-id="43826-132">Select **Docker Support** from the **Project** menu.</span></span>
* <span data-ttu-id="43826-133">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und rufen Sie anschließend **Hinzufügen** > **Docker-Unterstützung** auf.</span><span class="sxs-lookup"><span data-stu-id="43826-133">Right-click the project in **Solution Explorer** and select **Add** > **Docker Support**.</span></span>

<span data-ttu-id="43826-134">Mit den Containertools von Visual Studio können Sie Docker nicht zu einem vorhandenen ASP.NET Core-Projekt hinzufügen, das auf .NET Framework abzielt.</span><span class="sxs-lookup"><span data-stu-id="43826-134">The Visual Studio Container Tools don't support adding Docker to an existing ASP.NET Core project targeting .NET Framework.</span></span>

## <a name="dockerfile-overview"></a><span data-ttu-id="43826-135">Übersicht über die Dockerfile-Datei</span><span class="sxs-lookup"><span data-stu-id="43826-135">Dockerfile overview</span></span>

<span data-ttu-id="43826-136">Eine *Dockerfile*-Datei, der wichtigste Bestandteil beim Erstellen eines endgültigen Docker-Images, wird dem Projektstamm hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="43826-136">A *Dockerfile*, the recipe for creating a final Docker image, is added to the project root.</span></span> <span data-ttu-id="43826-137">Verweisen Sie auf einen [Dockerfile-Verweis](https://docs.docker.com/engine/reference/builder/), damit Sie einen Überblick über die darin enthaltenen Befehle erlangen.</span><span class="sxs-lookup"><span data-stu-id="43826-137">Refer to [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) for an understanding of the commands within it.</span></span> <span data-ttu-id="43826-138">Diese spezielle *Dockerfile*-Datei verwendet einen [mehrstufigen Build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/), der vier unterschiedlich benannte Buildschritte enthält:</span><span class="sxs-lookup"><span data-stu-id="43826-138">This particular *Dockerfile* uses a [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) with four distinct, named build stages:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile.original?highlight=1,6,14,17)]

<span data-ttu-id="43826-139">Die obige *Dockerfile*-Datei basiert auf dem [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/)-Image.</span><span class="sxs-lookup"><span data-stu-id="43826-139">The preceding *Dockerfile* is based on the [microsoft/dotnet](https://hub.docker.com/r/microsoft/dotnet/) image.</span></span> <span data-ttu-id="43826-140">Dieses Basisimage enthält die ASP.NET Core-Runtime und NuGet-Pakete.</span><span class="sxs-lookup"><span data-stu-id="43826-140">This base image includes the ASP.NET Core runtime and NuGet packages.</span></span> <span data-ttu-id="43826-141">Zur Verbesserung der Startleistung wurden die Pakete mit einem JIT-Compiler (Just-In-Time) übersetzt.</span><span class="sxs-lookup"><span data-stu-id="43826-141">The packages are just-in-time (JIT) compiled to improve startup performance.</span></span>

<span data-ttu-id="43826-142">Wenn im neuen Projektdialogfeld das Kontrollkästchen **Configure for HTTPS** (Für HTTPS konfigurieren) aktiviert ist, werden durch die *Dockerfile*-Datei zwei Ports verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="43826-142">When the new project dialog's **Configure for HTTPS** check box is checked, the *Dockerfile* exposes two ports.</span></span> <span data-ttu-id="43826-143">Ein Port wird für den HTTP-Datenverkehr, der andere für HTTPS verwendet.</span><span class="sxs-lookup"><span data-stu-id="43826-143">One port is used for HTTP traffic; the other port is used for HTTPS.</span></span> <span data-ttu-id="43826-144">Wenn dieses Kontrollkästchen nicht aktiviert ist, wird nur der Port 80 für den HTTP-Datenverkehr verfügbar gemacht.</span><span class="sxs-lookup"><span data-stu-id="43826-144">If the check box isn't checked, a single port (80) is exposed for HTTP traffic.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-dockerfile[](visual-studio-tools-for-docker/samples/2.0/HelloDockerTools/Dockerfile?highlight=1,5,13,16)]

<span data-ttu-id="43826-145">Die obige *Dockerfile*-Datei basiert auf dem [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/)-Image.</span><span class="sxs-lookup"><span data-stu-id="43826-145">The preceding *Dockerfile* is based on the [microsoft/aspnetcore](https://hub.docker.com/r/microsoft/aspnetcore/) image.</span></span> <span data-ttu-id="43826-146">Dieses Basisimage enthält die ASP.NET Core-NuGet-Pakete, die mit einem JIT-Compiler übersetzt wurden und dadurch die Startleistung verbessern.</span><span class="sxs-lookup"><span data-stu-id="43826-146">This base image includes the ASP.NET Core NuGet packages, which are just-in-time (JIT) compiled to improve startup performance.</span></span>

::: moniker-end

## <a name="add-container-orchestrator-support-to-an-app"></a><span data-ttu-id="43826-147">Hinzufügen eines Containerorchestrators zu einer App</span><span class="sxs-lookup"><span data-stu-id="43826-147">Add container orchestrator support to an app</span></span>

<span data-ttu-id="43826-148">In Version 15.7 oder in früheren Versionen von Visual Studio 2017 wird als Containerorchestrierungslösung ausschließlich [Docker Compose](https://docs.docker.com/compose/overview/) unterstützt.</span><span class="sxs-lookup"><span data-stu-id="43826-148">Visual Studio 2017 versions 15.7 or earlier support [Docker Compose](https://docs.docker.com/compose/overview/) as the sole container orchestration solution.</span></span> <span data-ttu-id="43826-149">Die Docker Compose-Artefakte werden über **Hinzufügen** > **Docker-Unterstützung** hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="43826-149">The Docker Compose artifacts are added via **Add** > **Docker Support**.</span></span>

<span data-ttu-id="43826-150">Ab Version 15.8 von Visual Studio 2017 wird eine Orchestrierungslösung erst bei Aufforderung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="43826-150">Visual Studio 2017 versions 15.8 or later add an orchestration solution only when instructed.</span></span> <span data-ttu-id="43826-151">Klicken Sie mit der rechten Maustaste im **Projektmappen-Explorer** auf das Projekt, und rufen Sie **Hinzufügen** > **Container Orchestrator Support** (Unterstützung für Containerorchestrator) auf.</span><span class="sxs-lookup"><span data-stu-id="43826-151">Right-click the project in **Solution Explorer** and select **Add** > **Container Orchestrator Support**.</span></span> <span data-ttu-id="43826-152">Es werden zwei verschiedene Auswahlmöglichkeiten angeboten: [Docker Compose](#docker-compose) und [Service Fabric](#service-fabric).</span><span class="sxs-lookup"><span data-stu-id="43826-152">Two different choices are offered: [Docker Compose](#docker-compose) and [Service Fabric](#service-fabric).</span></span>

### <a name="docker-compose"></a><span data-ttu-id="43826-153">Docker Compose</span><span class="sxs-lookup"><span data-stu-id="43826-153">Docker Compose</span></span>

<span data-ttu-id="43826-154">Die Containertools von Visual Studio fügen der Projektmappe ein *docker-compose*-Projekt mit den folgenden Dateien hinzu:</span><span class="sxs-lookup"><span data-stu-id="43826-154">The Visual Studio Container Tools add a *docker-compose* project to the solution with the following files:</span></span>

* <span data-ttu-id="43826-155">*docker-compose.dcproj* &ndash; die Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="43826-155">*docker-compose.dcproj* &ndash; The file representing the project.</span></span> <span data-ttu-id="43826-156">Diese enthält ein `<DockerTargetOS>`-Element, mit dem das zu verwendende Betriebssystem angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="43826-156">Includes a `<DockerTargetOS>` element specifying the OS to be used.</span></span>
* <span data-ttu-id="43826-157">*.dockerignore* &ndash; Diese Datei enthält eine Liste mit Mustern für Dateien und Verzeichnisse, die beim Generieren eines Buildkontexts ausgeschlossen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="43826-157">*.dockerignore* &ndash; Lists the file and directory patterns to exclude when generating a build context.</span></span>
* <span data-ttu-id="43826-158">*docker-compose.yml* &ndash; die [Docker Compose](https://docs.docker.com/compose/overview/)-Basisdatei zum Definieren der Sammlung von Images, die mit `docker-compose build` und `docker-compose run` erstellt bzw. ausgeführt werden soll.</span><span class="sxs-lookup"><span data-stu-id="43826-158">*docker-compose.yml* &ndash; The base [Docker Compose](https://docs.docker.com/compose/overview/) file used to define the collection of images built and run with `docker-compose build` and `docker-compose run`, respectively.</span></span>
* <span data-ttu-id="43826-159">*docker-compose.override.yml* &ndash; eine optionale Datei, die von Docker Compose gelesen wird und Einstellungen zur Überschreibung von Konfigurationen für Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="43826-159">*docker-compose.override.yml* &ndash; An optional file, read by Docker Compose, with configuration overrides for services.</span></span> <span data-ttu-id="43826-160">Visual Studio führt `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` aus, um die Dateien zusammenzuführen.</span><span class="sxs-lookup"><span data-stu-id="43826-160">Visual Studio executes `docker-compose -f "docker-compose.yml" -f "docker-compose.override.yml"` to merge these files.</span></span>

<span data-ttu-id="43826-161">Die Datei *docker-compose.yml* verweist auf den Namen des Images, das beim Ausführen des Projekts erstellt wird:</span><span class="sxs-lookup"><span data-stu-id="43826-161">The *docker-compose.yml* file references the name of the image that's created when the project runs:</span></span>

[!code-yaml[](visual-studio-tools-for-docker/samples/2.0/docker-compose.yml?highlight=5)]

<span data-ttu-id="43826-162">In dem zuvor genannten Beispiel generiert `image: hellodockertools` das Image `hellodockertools:dev`, wenn die App im Modus **Debuggen** ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="43826-162">In the preceding example, `image: hellodockertools` generates the image `hellodockertools:dev` when the app runs in **Debug** mode.</span></span> <span data-ttu-id="43826-163">Das `hellodockertools:latest`-Image wird generiert, wenn die App im Modus **Release** ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="43826-163">The `hellodockertools:latest` image is generated when the app runs in **Release** mode.</span></span>

<span data-ttu-id="43826-164">Stellen Sie dem Imagenamen den Benutzernamen für [Docker Hub](https://hub.docker.com/) voran (z.B. `dockerhubusername/hellodockertools`), wenn das Image per Push in die Registrierung übertragen werden soll.</span><span class="sxs-lookup"><span data-stu-id="43826-164">Prefix the image name with the [Docker Hub](https://hub.docker.com/) username (for example, `dockerhubusername/hellodockertools`) if the image is pushed to the registry.</span></span> <span data-ttu-id="43826-165">Stattdessen können Sie auch den Imagenamen ändern, sodass er die private Registrierungs-URL, die von der Konfiguration abhängig ist (z.B. `privateregistry.domain.com/hellodockertools`) enthält.</span><span class="sxs-lookup"><span data-stu-id="43826-165">Alternatively, change the image name to include the private registry URL (for example, `privateregistry.domain.com/hellodockertools`) depending on the configuration.</span></span>

<span data-ttu-id="43826-166">Wenn Sie ein anderes Verhalten basierend auf der Buildkonfiguration wünschen (z.B. Debug oder Release), fügen Sie konfigurationsspezifische *docker-compose* Dateien hinzu.</span><span class="sxs-lookup"><span data-stu-id="43826-166">If you want different behavior based on the build configuration (for example, Debug or Release), add configuration-specific *docker-compose* files.</span></span> <span data-ttu-id="43826-167">Die Dateien sollten entsprechend der Buildkonfiguration benannt werden (z.B. *docker-compose.vs.debug.yml* und *docker-compose.vs.release.yml*) und am gleichen Speicherort wie die Datei *docker-compose-override.yml* gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="43826-167">The files should be named according to the build configuration (for example, *docker-compose.vs.debug.yml* and *docker-compose.vs.release.yml*) and placed in the same location as the *docker-compose-override.yml* file.</span></span> 

<span data-ttu-id="43826-168">Mithilfe der konfigurationsspezifischen Außerkraftsetzungsdateien können Sie verschiedene Konfigurationseinstellungen (z.B. Umgebungsvariablen oder Einstiegspunkte) für Debug- und Releasebuildkonfigurationen angeben.</span><span class="sxs-lookup"><span data-stu-id="43826-168">Using the configuration-specific override files, you can specify different configuration settings (such as environment variables or entry points) for Debug and Release build configurations.</span></span>

<span data-ttu-id="43826-169">Damit Docker Compose eine Option für die Ausführung in Visual Studio anzeigen kann, muss das Docker-Projekt als Startprojekt festgelegt sein.</span><span class="sxs-lookup"><span data-stu-id="43826-169">For Docker Compose to display an option to run in Visual Studio, the docker project must be the startup project.</span></span>

### <a name="service-fabric"></a><span data-ttu-id="43826-170">Service Fabric</span><span class="sxs-lookup"><span data-stu-id="43826-170">Service Fabric</span></span>

<span data-ttu-id="43826-171">Zusätzlich zu den grundlegenden [Voraussetzungen](#prerequisites) ist für [Service Fabric](/azure/service-fabric/) als Orchestrierungslösung Folgendes erforderlich:</span><span class="sxs-lookup"><span data-stu-id="43826-171">In addition to the base [Prerequisites](#prerequisites), the [Service Fabric](/azure/service-fabric/) orchestration solution demands the following prerequisites:</span></span>

* <span data-ttu-id="43826-172">Version 2.6 oder eine höher des [Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)</span><span class="sxs-lookup"><span data-stu-id="43826-172">[Microsoft Azure Service Fabric SDK](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK) version 2.6 or later</span></span>
* <span data-ttu-id="43826-173">die Visual Studio-Workload **Azure-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="43826-173">Visual Studio's **Azure Development** workload</span></span>

<span data-ttu-id="43826-174">Die Ausführung von Linux-Containern auf dem lokalen Entwicklungscluster unter Windows wird von Service Fabric nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="43826-174">Service Fabric doesn't support running Linux containers in the local development cluster on Windows.</span></span> <span data-ttu-id="43826-175">Wenn für das Projekt bereits ein Linux-Container verwendet wird, fordert Visual Studio Sie dazu auf, stattdessen einen Windows-Container zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="43826-175">If the project is already using a Linux container, Visual Studio prompts to switch to Windows containers.</span></span>

<span data-ttu-id="43826-176">Die Containertools von Visual Studio führen die folgenden Aufgaben aus:</span><span class="sxs-lookup"><span data-stu-id="43826-176">The Visual Studio Container Tools do the following tasks:</span></span>

* <span data-ttu-id="43826-177">Ein **Service Fabric-Anwendungsprojekt** mit dem Namen *&lt;Projektname&gt;Application* wird der Projektmappe hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="43826-177">Adds a *&lt;project_name&gt;Application* **Service Fabric Application** project to the solution.</span></span>
* <span data-ttu-id="43826-178">Eine *Dockerfile*-Datei und eine *DOCKERIGNORE*-Datei werden dem ASP.NET Core-Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="43826-178">Adds a *Dockerfile* and a *.dockerignore* file to the ASP.NET Core project.</span></span> <span data-ttu-id="43826-179">Wenn im ASP.NET Core-Projekt bereits eine *Dockerfile*-Datei vorhanden ist, wird diese in *Dockerfile.original* umbenannt.</span><span class="sxs-lookup"><span data-stu-id="43826-179">If a *Dockerfile* already exists in the ASP.NET Core project, it's renamed to *Dockerfile.original*.</span></span> <span data-ttu-id="43826-180">Erstellt wird eine neue *Dockerfile*-Datei, die der folgenden ähnelt:</span><span class="sxs-lookup"><span data-stu-id="43826-180">A new *Dockerfile*, similar to the following, is created:</span></span>

    [!code-dockerfile[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/Dockerfile)]

* <span data-ttu-id="43826-181">Ein `<IsServiceFabricServiceProject>`-Element wird der *CSPROJ*-Datei des ASP.NET Core-Projekts hinzugefügt:</span><span class="sxs-lookup"><span data-stu-id="43826-181">Adds an `<IsServiceFabricServiceProject>` element to the ASP.NET Core project's *.csproj* file:</span></span>

    [!code-xml[](visual-studio-tools-for-docker/samples/2.1/HelloDockerTools/HelloDockerTools.csproj?name=snippet_IsServiceFabricServiceProject)]

* <span data-ttu-id="43826-182">Ein *PackageRoot*-Ordner wird dem ASP.NET Core-Projekt hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="43826-182">Adds a *PackageRoot* folder to the ASP.NET Core project.</span></span> <span data-ttu-id="43826-183">Der Ordner enthält das Dienstmanifest und die Einstellungen für den neuen Dienst.</span><span class="sxs-lookup"><span data-stu-id="43826-183">The folder includes the service manifest and settings for the new service.</span></span>

<span data-ttu-id="43826-184">Weitere Informationen finden Sie unter [Bereitstellen einer .NET-App in einem Windows-Container in Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span><span class="sxs-lookup"><span data-stu-id="43826-184">For more information, see [Deploy a .NET app in a Windows container to Azure Service Fabric](/azure/service-fabric/service-fabric-host-app-in-a-container).</span></span>

## <a name="debug"></a><span data-ttu-id="43826-185">Debug</span><span class="sxs-lookup"><span data-stu-id="43826-185">Debug</span></span>

<span data-ttu-id="43826-186">Wählen Sie in der Symbolleiste im Dropdownmenü „Debuggen“ die Option **Docker** aus, und starten Sie das Debuggen der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="43826-186">Select **Docker** from the debug drop-down in the toolbar, and start debugging the app.</span></span> <span data-ttu-id="43826-187">In der Ansicht **Docker** im Fenster **Ausgabe** werden die folgenden Aktionen angezeigt:</span><span class="sxs-lookup"><span data-stu-id="43826-187">The **Docker** view of the **Output** window shows the following actions taking place:</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="43826-188">Das Tag *2.1-aspnetcore-runtime* des *microsoft/dotnet*-Runtimeimages wird abgerufen (falls das Image noch nicht im Cache vorhanden ist).</span><span class="sxs-lookup"><span data-stu-id="43826-188">The *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* runtime image is acquired (if not already in the cache).</span></span> <span data-ttu-id="43826-189">Das Image installiert die Runtimes für ASP.NET Core und .NET Core sowie die zugehörigen Bibliotheken.</span><span class="sxs-lookup"><span data-stu-id="43826-189">The image installs the ASP.NET Core and .NET Core runtimes and associated libraries.</span></span> <span data-ttu-id="43826-190">Es ist für die Ausführung von ASP.NET Core-Apps in einer Produktionsumgebung optimiert.</span><span class="sxs-lookup"><span data-stu-id="43826-190">It's optimized for running ASP.NET Core apps in production.</span></span>
* <span data-ttu-id="43826-191">Die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` wird innerhalb des Containers auf `Development` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="43826-191">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="43826-192">Zwei dynamisch zugewiesene Ports werden verfügbar gemacht, wobei einer für HTTP und der andere für HTTPS verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="43826-192">Two dynamically assigned ports are exposed: one for HTTP and one for HTTPS.</span></span> <span data-ttu-id="43826-193">Der Port, der „localhost“ zugewiesen ist, kann mit dem Befehl `docker ps` abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="43826-193">The port assigned to localhost can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="43826-194">Die App wird in den Container kopiert.</span><span class="sxs-lookup"><span data-stu-id="43826-194">The app is copied to the container.</span></span>
* <span data-ttu-id="43826-195">Der Standardbrowser wird mit dem dynamisch zugewiesenen Port gestartet, wobei der Debugger an den Container angehängt wird.</span><span class="sxs-lookup"><span data-stu-id="43826-195">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="43826-196">Das resultierende Docker-Image der App wird mit dem Tag *dev* versehen.</span><span class="sxs-lookup"><span data-stu-id="43826-196">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="43826-197">Das Image basiert auf dem Tag *2.1-aspnetcore-runtime* des Basisimages *microsoft/dotnet*.</span><span class="sxs-lookup"><span data-stu-id="43826-197">The image is based on the *2.1-aspnetcore-runtime* tag of the *microsoft/dotnet* base image.</span></span> <span data-ttu-id="43826-198">Führen Sie im Fenster **Paket-Manager-Konsole** den Befehl `docker images` aus.</span><span class="sxs-lookup"><span data-stu-id="43826-198">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="43826-199">Die Images auf dem Computer werden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="43826-199">The images on the machine are displayed:</span></span>

```console
REPOSITORY        TAG                     IMAGE ID      CREATED         SIZE
hellodockertools  dev                     d72ce0f1dfe7  30 seconds ago  255MB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago      255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* <span data-ttu-id="43826-200">Das Runtime-Image *microsoft/aspnetcore* wird geladen (falls es sich nicht bereits im Cache befindet).</span><span class="sxs-lookup"><span data-stu-id="43826-200">The *microsoft/aspnetcore* runtime image is acquired (if not already in the cache).</span></span>
* <span data-ttu-id="43826-201">Die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` wird innerhalb des Containers auf `Development` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="43826-201">The `ASPNETCORE_ENVIRONMENT` environment variable is set to `Development` within the container.</span></span>
* <span data-ttu-id="43826-202">Port 80 wird verfügbar gemacht und einem dynamisch zugewiesenen Port für Localhost zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="43826-202">Port 80 is exposed and mapped to a dynamically assigned port for localhost.</span></span> <span data-ttu-id="43826-203">Der Port wird vom Docker-Host bestimmt und kann mit dem Befehl `docker ps` abgefragt werden.</span><span class="sxs-lookup"><span data-stu-id="43826-203">The port is determined by the Docker host and can be queried with the `docker ps` command.</span></span>
* <span data-ttu-id="43826-204">Die App wird in den Container kopiert.</span><span class="sxs-lookup"><span data-stu-id="43826-204">The app is copied to the container.</span></span>
* <span data-ttu-id="43826-205">Der Standardbrowser wird mit dem dynamisch zugewiesenen Port gestartet, wobei der Debugger an den Container angehängt wird.</span><span class="sxs-lookup"><span data-stu-id="43826-205">The default browser is launched with the debugger attached to the container using the dynamically assigned port.</span></span>

<span data-ttu-id="43826-206">Das resultierende Docker-Image der App wird mit dem Tag *dev* versehen.</span><span class="sxs-lookup"><span data-stu-id="43826-206">The resulting Docker image of the app is tagged as *dev*.</span></span> <span data-ttu-id="43826-207">Das Image basiert auf dem Basisimage *microsoft/aspnetcore*.</span><span class="sxs-lookup"><span data-stu-id="43826-207">The image is based on the *microsoft/aspnetcore* base image.</span></span> <span data-ttu-id="43826-208">Führen Sie im Fenster **Paket-Manager-Konsole** den Befehl `docker images` aus.</span><span class="sxs-lookup"><span data-stu-id="43826-208">Run the `docker images` command in the **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="43826-209">Die Images auf dem Computer werden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="43826-209">The images on the machine are displayed:</span></span>

```console
REPOSITORY            TAG  IMAGE ID      CREATED        SIZE
hellodockertools      dev  5fafe5d1ad5b  4 minutes ago  347MB
microsoft/aspnetcore  2.0  c69d39472da9  13 days ago    347MB
```

::: moniker-end

> [!NOTE]
> <span data-ttu-id="43826-210">Das *dev*-Image enthält keine App-Inhalte, da **Debug**-Konfigurationen durch das Einbinden von Volumes für die iterative Funktionalität sorgen.</span><span class="sxs-lookup"><span data-stu-id="43826-210">The *dev* image lacks the app contents, as **Debug** configurations use volume mounting to provide the iterative experience.</span></span> <span data-ttu-id="43826-211">Verwenden Sie zum Verschieben eines Images die Konfiguration **Release**.</span><span class="sxs-lookup"><span data-stu-id="43826-211">To push an image, use the **Release** configuration.</span></span>

<span data-ttu-id="43826-212">Führen Sie in der Paket-Manager-Konsole den Befehl `docker ps` aus.</span><span class="sxs-lookup"><span data-stu-id="43826-212">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="43826-213">Beachten Sie, dass die App mithilfe des Containers ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="43826-213">Notice the app is running using the container:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   21 seconds ago      Up 19 seconds       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="edit-and-continue"></a><span data-ttu-id="43826-214">Bearbeiten und Fortfahren</span><span class="sxs-lookup"><span data-stu-id="43826-214">Edit and continue</span></span>

<span data-ttu-id="43826-215">Änderungen an statischen Dateien und Razor-Ansichten werden automatisch aktualisiert, wobei kein Kompilierungsschritt erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="43826-215">Changes to static files and Razor views are automatically updated without the need for a compilation step.</span></span> <span data-ttu-id="43826-216">Nehmen Sie die Änderung vor, speichern Sie die Datei, und aktualisieren Sie die Browseransicht, um die Aktualisierung anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="43826-216">Make the change, save, and refresh the browser to view the update.</span></span>

<span data-ttu-id="43826-217">Änderungen an Codedateien erfordern eine Kompilierung und einen Neustart von Kestrel im Container.</span><span class="sxs-lookup"><span data-stu-id="43826-217">Code file modifications require compilation and a restart of Kestrel within the container.</span></span> <span data-ttu-id="43826-218">Nachdem Sie die Änderung vorgenommen haben, drücken Sie `CTRL+F5`, um den Prozess durchzuführen und die App im Container zu starten.</span><span class="sxs-lookup"><span data-stu-id="43826-218">After making the change, use `CTRL+F5` to perform the process and start the app within the container.</span></span> <span data-ttu-id="43826-219">Der Docker-Container wird nicht erneut erstellt oder angehalten.</span><span class="sxs-lookup"><span data-stu-id="43826-219">The Docker container isn't rebuilt or stopped.</span></span> <span data-ttu-id="43826-220">Führen Sie in der Paket-Manager-Konsole den Befehl `docker ps` aus.</span><span class="sxs-lookup"><span data-stu-id="43826-220">Run the `docker ps` command in PMC.</span></span> <span data-ttu-id="43826-221">Beachten Sie, dass der ursprüngliche Container immer noch genau so ausgeführt wird wie vor zehn Minuten:</span><span class="sxs-lookup"><span data-stu-id="43826-221">Notice the original container is still running as of 10 minutes ago:</span></span>

```console
CONTAINER ID        IMAGE                  COMMAND                   CREATED             STATUS              PORTS                   NAMES
baf9a678c88d        hellodockertools:dev   "C:\\remote_debugge..."   10 minutes ago      Up 10 minutes       0.0.0.0:37630->80/tcp   dockercompose4642749010770307127_hellodockertools_1
```

## <a name="publish-docker-images"></a><span data-ttu-id="43826-222">Veröffentlichen von Docker-Images</span><span class="sxs-lookup"><span data-stu-id="43826-222">Publish Docker images</span></span>

<span data-ttu-id="43826-223">Nachdem der Entwicklungs- und Debugzyklus für die App abgeschlossen wurde, kann mit den Containertools von Visual Studio das Produktionsimage der App erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="43826-223">Once the develop and debug cycle of the app is completed, the Visual Studio Container Tools assist in creating the production image of the app.</span></span> <span data-ttu-id="43826-224">Wählen Sie im Dropdownmenü „Konfiguration“ die Option **Release** aus, und erstellen Sie die App.</span><span class="sxs-lookup"><span data-stu-id="43826-224">Change the configuration drop-down to **Release** and build the app.</span></span> <span data-ttu-id="43826-225">Die Tools rufen das Kompilierungs-/Veröffentlichungsimage von Docker Hub ab (falls es nicht bereits im Cache vorhanden ist).</span><span class="sxs-lookup"><span data-stu-id="43826-225">The tooling acquires the compile/publish image from Docker Hub (if not already in the cache).</span></span> <span data-ttu-id="43826-226">Ein Image mit dem Tag *latest* wird erstellt und kann mittels Push an die private Registrierung oder an den Docker-Hub übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="43826-226">An image is produced with the *latest* tag, which can be pushed to the private registry or Docker Hub.</span></span>

<span data-ttu-id="43826-227">Mit dem Befehl `docker images` können Sie die Liste der Images in der Paket-Manager-Konsole anzeigen.</span><span class="sxs-lookup"><span data-stu-id="43826-227">Run the `docker images` command in PMC to see the list of images.</span></span> <span data-ttu-id="43826-228">Dadurch werden Informationen angezeigt, die mit denen der folgenden Ausgabe vergleichbar sind:</span><span class="sxs-lookup"><span data-stu-id="43826-228">Output similar to the following is displayed:</span></span>

::: moniker range=">= aspnetcore-2.1"

```console
REPOSITORY        TAG                     IMAGE ID      CREATED             SIZE
hellodockertools  latest                  e3984a64230c  About a minute ago  258MB
hellodockertools  dev                     d72ce0f1dfe7  4 minutes ago       255MB
microsoft/dotnet  2.1-sdk                 9e243db15f91  6 days ago          1.7GB
microsoft/dotnet  2.1-aspnetcore-runtime  fcc3887985bb  6 days ago          255MB
```

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

```console
REPOSITORY                  TAG     IMAGE ID      CREATED         SIZE
hellodockertools            latest  cd28f0d4abbd  12 seconds ago  349MB
hellodockertools            dev     5fafe5d1ad5b  23 minutes ago  347MB
microsoft/aspnetcore-build  2.0     7fed40fbb647  13 days ago     2.02GB
microsoft/aspnetcore        2.0     c69d39472da9  13 days ago     347MB
```

<span data-ttu-id="43826-229">Die `microsoft/aspnetcore-build`- und `microsoft/aspnetcore`-Images, die in der obigen Ausgabe zu sehen sind, werden ab .NET Core 2.1 durch `microsoft/dotnet`-Images ersetzt.</span><span class="sxs-lookup"><span data-stu-id="43826-229">The `microsoft/aspnetcore-build` and `microsoft/aspnetcore` images listed in the preceding output are replaced with `microsoft/dotnet` images as of .NET Core 2.1.</span></span> <span data-ttu-id="43826-230">Weitere Informationen finden Sie in der [Ankündigung zur Migration der Docker-Repositorys](https://github.com/aspnet/Announcements/issues/298).</span><span class="sxs-lookup"><span data-stu-id="43826-230">For more information, see [the Docker repositories migration announcement](https://github.com/aspnet/Announcements/issues/298).</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="43826-231">Der Befehl `docker images` gibt Vermittlerimages mit Repositorynamen und -tags zurück, die als *\<none>* gekennzeichnet sind (obenstehend nicht aufgeführt).</span><span class="sxs-lookup"><span data-stu-id="43826-231">The `docker images` command returns intermediary images with repository names and tags identified as *\<none>* (not listed above).</span></span> <span data-ttu-id="43826-232">Diese unbenannten Images werden von der [mehrstufig erstellten](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*-Datei erstellt.</span><span class="sxs-lookup"><span data-stu-id="43826-232">These unnamed images are produced by the [multi-stage build](https://docs.docker.com/engine/userguide/eng-image/multistage-build/) *Dockerfile*.</span></span> <span data-ttu-id="43826-233">Sie verbessern die Effizienz der Erstellung des endgültigen Images. D.h., nur die notwendigen Ebenen werden erneut erstellt, wenn Änderungen auftreten.</span><span class="sxs-lookup"><span data-stu-id="43826-233">They improve the efficiency of building the final image&mdash;only the necessary layers are rebuilt when changes occur.</span></span> <span data-ttu-id="43826-234">Wenn die Vermittlerimages nicht mehr benötigt werden, löschen Sie sie über den Befehl [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).</span><span class="sxs-lookup"><span data-stu-id="43826-234">When the intermediary images are no longer needed, delete them using the [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/) command.</span></span>

<span data-ttu-id="43826-235">Möglicherweise wird angenommen, dass das Produktions- oder Releaseimage im Vergleich zum *dev*-Image kleiner ist.</span><span class="sxs-lookup"><span data-stu-id="43826-235">There may be an expectation for the production or release image to be smaller in size by comparison to the *dev* image.</span></span> <span data-ttu-id="43826-236">Aufgrund der Volumezuordnung wurden der Debugger und die App über den lokalen Computer und nicht innerhalb des Containers ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="43826-236">Because of the volume mapping, the debugger and app were running from the local machine and not within the container.</span></span> <span data-ttu-id="43826-237">Im *neusten* Image wurde der benötigte App-Code gepackt, um die App auf einem Hostcomputer auszuführen.</span><span class="sxs-lookup"><span data-stu-id="43826-237">The *latest* image has packaged the necessary app code to run the app on a host machine.</span></span> <span data-ttu-id="43826-238">Aus diesem Grund ist das Delta die Größe des App-Codes.</span><span class="sxs-lookup"><span data-stu-id="43826-238">Therefore, the delta is the size of the app code.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="43826-239">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="43826-239">Additional resources</span></span>

* [<span data-ttu-id="43826-240">Containerentwicklung mit Visual Studio</span><span class="sxs-lookup"><span data-stu-id="43826-240">Container development with Visual Studio</span></span>](/visualstudio/containers)
* [<span data-ttu-id="43826-241">Azure Service Fabric: Vorbereiten der Entwicklungsumgebung</span><span class="sxs-lookup"><span data-stu-id="43826-241">Azure Service Fabric: Prepare your development environment</span></span>](/azure/service-fabric/service-fabric-get-started)
* [<span data-ttu-id="43826-242">Bereitstellen einer .NET-App in einem Windows-Container in Azure Service Fabric</span><span class="sxs-lookup"><span data-stu-id="43826-242">Deploy a .NET app in a Windows container to Azure Service Fabric</span></span>](/azure/service-fabric/service-fabric-host-app-in-a-container)
* [<span data-ttu-id="43826-243">Problembehandlung bei der Visual Studio-Entwicklung mit Docker</span><span class="sxs-lookup"><span data-stu-id="43826-243">Troubleshoot Visual Studio development with Docker</span></span>](/azure/vs-azure-tools-docker-troubleshooting-docker-errors)
* [<span data-ttu-id="43826-244">GitHub-Repository mit Visual Studio-Containertools</span><span class="sxs-lookup"><span data-stu-id="43826-244">Visual Studio Container Tools GitHub repository</span></span>](https://github.com/Microsoft/DockerTools)
