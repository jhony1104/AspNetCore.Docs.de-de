---
title: Hosten und Bereitstellen von Blazor
author: guardrex
description: Erfahren Sie, wie Sie Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2019
uid: host-and-deploy/blazor/index
ms.openlocfilehash: a7739e2b240d7fd6c85e68105892c802228ebeb5
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614567"
---
# <a name="host-and-deploy-blazor"></a><span data-ttu-id="06301-103">Hosten und Bereitstellen von Blazor</span><span class="sxs-lookup"><span data-stu-id="06301-103">Host and deploy Blazor</span></span>

<span data-ttu-id="06301-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="06301-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="06301-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="06301-105">Publish the app</span></span>

<span data-ttu-id="06301-106">Apps werden für die Bereitstellung in Releasekonfigurationen mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="06301-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="06301-107">In einer integrierten Entwicklungsumgebung (IDE) kann der Befehl `dotnet publish` mithilfe der integrierten Veröffentlichungsfunktionen automatisch ausgeführt werden, sodass der Befehl je nach verwendetem Entwicklungstool möglicherweise nicht manuell über eine Befehlszeile ausgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="06301-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="06301-108">Mit `dotnet publish` wird eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts ausgelöst und das Projekt [erstellt](/dotnet/core/tools/dotnet-build), bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="06301-108">`dotnet publish` triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="06301-109">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="06301-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="06301-110">Die Bereitstellung wird im Ordner */bin/Release/{Zielframework}/publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="06301-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="06301-111">Die Objekte im Ordner *publish* werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="06301-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="06301-112">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="06301-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="06301-113">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="06301-113">Deployment</span></span>

<span data-ttu-id="06301-114">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="06301-114">For deployment guidance, see the following topics:</span></span>

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
