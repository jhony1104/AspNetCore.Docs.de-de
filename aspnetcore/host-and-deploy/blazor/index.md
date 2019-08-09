---
title: Hosten und Bereitstellen von ASP.NET Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: d18abbf33c71dca5130bfc6b503b46c1d5bce537
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68913927"
---
# <a name="host-and-deploy-aspnet-core-blazor"></a><span data-ttu-id="9190c-103">Hosten und Bereitstellen von ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="9190c-103">Host and deploy ASP.NET Core Blazor</span></span>

<span data-ttu-id="9190c-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="9190c-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="9190c-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="9190c-105">Publish the app</span></span>

<span data-ttu-id="9190c-106">Apps werden für die Bereitstellung in Releasekonfigurationen veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="9190c-106">Apps are published for deployment in Release configuration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9190c-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9190c-107">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="9190c-108">Wählen Sie **Build** > **Publish {APPLICATION}** auf der Navigationsleiste aus.</span><span class="sxs-lookup"><span data-stu-id="9190c-108">Select **Build** > **Publish {APPLICATION}** from the navigation bar.</span></span>
1. <span data-ttu-id="9190c-109">Wählen Sie das *Veröffentlichungsziel* aus.</span><span class="sxs-lookup"><span data-stu-id="9190c-109">Select the *publish target*.</span></span> <span data-ttu-id="9190c-110">Um lokal zu veröffentlichen, wählen Sie **Ordner** aus.</span><span class="sxs-lookup"><span data-stu-id="9190c-110">To publish locally, select **Folder**.</span></span>
1. <span data-ttu-id="9190c-111">Übernehmen Sie den Standardspeicherort im Feld **Ordner auswählen**, oder geben Sie einen anderen Speicherort an.</span><span class="sxs-lookup"><span data-stu-id="9190c-111">Accept the default location in the **Choose a folder** field or specify a different location.</span></span> <span data-ttu-id="9190c-112">Wählen Sie die Schaltfläche **Veröffentlichen** aus.</span><span class="sxs-lookup"><span data-stu-id="9190c-112">Select the **Publish** button.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="9190c-113">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="9190c-113">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="9190c-114">Verwenden Sie den Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) aus, um die App mit einer Releasekonfiguration zu veröffentlichen:</span><span class="sxs-lookup"><span data-stu-id="9190c-114">Use the [dotnet publish](/dotnet/core/tools/dotnet-publish) command to publish the app with a Release configuration:</span></span>

```console
dotnet publish -c Release
```

---

<span data-ttu-id="9190c-115">Das Veröffentlichen einer App löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="9190c-115">Publishing the app triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="9190c-116">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="9190c-116">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span>

<span data-ttu-id="9190c-117">Eine clientseitige Blazor-App wird im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="9190c-117">A Blazor client-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish/{ASSEMBLY NAME}/dist* folder.</span></span> <span data-ttu-id="9190c-118">Eine serverseitige Blazor-App wird im Ordner */bin/Release/{TARGET FRAMEWORK}/publish* veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="9190c-118">A Blazor server-side app is published to the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="9190c-119">Die Objekte im Ordner werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="9190c-119">The assets in the folder are deployed to the web server.</span></span> <span data-ttu-id="9190c-120">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="9190c-120">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="9190c-121">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="9190c-121">Deployment</span></span>

<span data-ttu-id="9190c-122">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="9190c-122">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>

## <a name="blazor-serverless-hosting-with-azure-storage"></a><span data-ttu-id="9190c-123">Serverloses Blazor-Hosting mit Azure Storage</span><span class="sxs-lookup"><span data-stu-id="9190c-123">Blazor serverless hosting with Azure Storage</span></span>

<span data-ttu-id="9190c-124">Clientseitige Blazor-Apps können statische Inhalte aus [Azure Storage](https://azure.microsoft.com/services/storage/) direkt aus einem Speichercontainer erhalten.</span><span class="sxs-lookup"><span data-stu-id="9190c-124">Blazor client-side apps can be served from [Azure Storage](https://azure.microsoft.com/services/storage/) as static content directly from a storage container.</span></span>

<span data-ttu-id="9190c-125">Weitere Informationen finden Sie unter [Host and deploy ASP.NET Core Blazor client-side (Standalone deployment) (Hosten und Bereitstellen von clientseitigem ASP.NET Core Blazor (eigenständige Bereitstellung)): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span><span class="sxs-lookup"><span data-stu-id="9190c-125">For more information, see [Host and deploy ASP.NET Core Blazor client-side (Standalone deployment): Azure Storage](xref:host-and-deploy/blazor/client-side#azure-storage).</span></span>
