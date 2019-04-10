---
title: Hosten und Bereitstellen von Razor-Komponenten und Blazor
author: guardrex
description: Erfahren Sie, wie Sie Razor-Komponenten und Blazor-Apps hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: host-and-deploy/razor-components-blazor/index
ms.openlocfilehash: eeaa27bef584523b77d8b47000b310fe0cac7341
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59070307"
---
# <a name="host-and-deploy-razor-components-and-blazor"></a><span data-ttu-id="2edef-103">Hosten und Bereitstellen von Razor-Komponenten und Blazor</span><span class="sxs-lookup"><span data-stu-id="2edef-103">Host and deploy Razor Components and Blazor</span></span>

<span data-ttu-id="2edef-104">Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="2edef-104">By [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com), and [Daniel Roth](https://github.com/danroth27)</span></span>

## <a name="publish-the-app"></a><span data-ttu-id="2edef-105">Veröffentlichen der App</span><span class="sxs-lookup"><span data-stu-id="2edef-105">Publish the app</span></span>

<span data-ttu-id="2edef-106">Apps werden für die Bereitstellung in Releasekonfigurationen mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="2edef-106">Apps are published for deployment in Release configuration with the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="2edef-107">In einer integrierten Entwicklungsumgebung (IDE) kann der Befehl `dotnet publish` mithilfe der integrierten Veröffentlichungsfunktionen automatisch ausgeführt werden, sodass der Befehl je nach verwendetem Entwicklungstool möglicherweise nicht manuell über eine Befehlszeile ausgeführt werden muss.</span><span class="sxs-lookup"><span data-stu-id="2edef-107">An integrated development environment (IDE) may handle executing the `dotnet publish` command automatically using its built-in publishing features, so it might not be necessary to manually execute the command from a command prompt depending on the development tools in use.</span></span>

```console
dotnet publish -c Release
```

`dotnet publish` <span data-ttu-id="2edef-108">Löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="2edef-108">triggers a [restore](/dotnet/core/tools/dotnet-restore) of the project's dependencies and [builds](/dotnet/core/tools/dotnet-build) the project before creating the assets for deployment.</span></span> <span data-ttu-id="2edef-109">Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren.</span><span class="sxs-lookup"><span data-stu-id="2edef-109">As part of the build process, unused methods and assemblies are removed to reduce app download size and load times.</span></span> <span data-ttu-id="2edef-110">Die Bereitstellung wird im Ordner */bin/Release/{Zielframework}/publish* erstellt.</span><span class="sxs-lookup"><span data-stu-id="2edef-110">The deployment is created in the */bin/Release/{TARGET FRAMEWORK}/publish* folder.</span></span>

<span data-ttu-id="2edef-111">Die Objekte im Ordner *publish* werden auf dem Webserver bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="2edef-111">The assets in the *publish* folder are deployed to the web server.</span></span> <span data-ttu-id="2edef-112">Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.</span><span class="sxs-lookup"><span data-stu-id="2edef-112">Deployment might be a manual or automated process depending on the development tools in use.</span></span>

## <a name="deployment"></a><span data-ttu-id="2edef-113">Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="2edef-113">Deployment</span></span>

<span data-ttu-id="2edef-114">Anleitungen für die Bereitstellung finden Sie in folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="2edef-114">For deployment guidance, see the following topics:</span></span>

* [<span data-ttu-id="2edef-115">Clientseitiges Blazor</span><span class="sxs-lookup"><span data-stu-id="2edef-115">Client-side Blazor</span></span>](xref:host-and-deploy/razor-components-blazor/blazor)
* [<span data-ttu-id="2edef-116">Serverseitige .NET Core-Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="2edef-116">Server-side ASP.NET Core Razor Components</span></span>](xref:host-and-deploy/razor-components-blazor/razor-components)
