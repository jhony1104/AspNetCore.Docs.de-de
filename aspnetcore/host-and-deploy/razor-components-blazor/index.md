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
# <a name="host-and-deploy-razor-components-and-blazor"></a>Hosten und Bereitstellen von Razor-Komponenten und Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Veröffentlichen der App

Apps werden für die Bereitstellung in Releasekonfigurationen mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) veröffentlicht. In einer integrierten Entwicklungsumgebung (IDE) kann der Befehl `dotnet publish` mithilfe der integrierten Veröffentlichungsfunktionen automatisch ausgeführt werden, sodass der Befehl je nach verwendetem Entwicklungstool möglicherweise nicht manuell über eine Befehlszeile ausgeführt werden muss.

```console
dotnet publish -c Release
```

`dotnet publish` Löst eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts aus und [erstellt](/dotnet/core/tools/dotnet-build) das Projekt, bevor die Objekte für die Bereitstellung erstellt werden. Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren. Die Bereitstellung wird im Ordner */bin/Release/{Zielframework}/publish* erstellt.

Die Objekte im Ordner *publish* werden auf dem Webserver bereitgestellt. Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.

## <a name="deployment"></a>Bereitstellung

Anleitungen für die Bereitstellung finden Sie in folgenden Themen:

* [Clientseitiges Blazor](xref:host-and-deploy/razor-components-blazor/blazor)
* [Serverseitige .NET Core-Razor-Komponenten](xref:host-and-deploy/razor-components-blazor/razor-components)
