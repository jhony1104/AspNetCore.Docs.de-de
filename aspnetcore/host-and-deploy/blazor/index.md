---
title: Hosten und Bereitstellen von Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/23/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: 0fc7643c65b93a63d7a594d35e4013eab76e9db8
ms.sourcegitcommit: 4d05e30567279072f1b070618afe58ae1bcefd5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376386"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="b8334-103">Hosten und Bereitstellen von Blazor</span><span class="sxs-lookup"><span data-stu-id="b8334-103">Host and deploy Blazor</span></span>

<span data-ttu-id="b8334-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="b8334-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="b8334-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="b8334-105">Publish the app</span></span>

<span data-ttu-id="b8334-106">Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="b8334-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b8334-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b8334-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="b8334-108">Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.</span><span class="sxs-lookup"><span data-stu-id="b8334-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="b8334-109">Wählen Sie das *Veröffentlichungsziel* aus.</span><span class="sxs-lookup"><span data-stu-id="b8334-109">Select the *publish target*.</span></span> <span data-ttu-id="b8334-110">Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="b8334-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="b8334-111">Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an.</span><span class="sxs-lookup"><span data-stu-id="b8334-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="b8334-112">Wählen Sie die Schaltfläche **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="b8334-112">Select the **Publish** button.</span></span>

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="b8334-113">Visual Studio Code / .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b8334-113">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="b8334-114">Verwenden Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um die App mit einer Releasekonfiguration zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="b8334-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="b8334-115">Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="b8334-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="b8334-116">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="b8334-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="b8334-117">Eine clientseitige Blazor-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="b8334-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="b8334-118">Eine serverseitige Blazor-App wird im Ordner */bin/Release/{TARGET FRAMEWORK}/publish* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="b8334-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="b8334-119">Die Objekte im Ordner werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="b8334-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="b8334-120">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="b8334-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="b8334-121">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="b8334-121">Deployment</span></span>

<span data-ttu-id="b8334-122">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="b8334-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a><span data-ttu-id="b8334-123">Serverloses Blazor-Hosting mit Azure Storage</span><span class="sxs-lookup"><span data-stu-id="b8334-123">Blazor serverless hosting with Azure Storage</span></span>

<span data-ttu-id="b8334-124">Clientseitige Blazor-Apps können statische Inhalte aus [Azure Storage](https://azure.microsoft.com/services/storage/) direkt aus einem Speichercontainer erhalten.</span><span class="sxs-lookup"><span data-stu-id="b8334-124">Blazor client-side apps can be served from [Azure Storage](https://azure.microsoft.com/services/storage/) as static content directly from a storage container.</span></span>

<span data-ttu-id="b8334-125">Weitere Informationen finden Sie unter [Hosten und Bereitstellen von clientseitigem Blazor (eigenständige Bereitstellung): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="b8334-125">For more information, see [Host and deploy Blazor client-side (Standalone deployment): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span></span>
