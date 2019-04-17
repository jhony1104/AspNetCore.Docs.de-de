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
# <a name="host-and-deploy-blazor"></a>Hosten und Bereitstellen von Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="publish-the-app"></a>Veröffentlichen der App

Apps werden für die Bereitstellung in Releasekonfigurationen mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) veröffentlicht. In einer integrierten Entwicklungsumgebung (IDE) kann der Befehl `dotnet publish` mithilfe der integrierten Veröffentlichungsfunktionen automatisch ausgeführt werden, sodass der Befehl je nach verwendetem Entwicklungstool möglicherweise nicht manuell über eine Befehlszeile ausgeführt werden muss.

```console
dotnet publish -c Release
```

Mit `dotnet publish` wird eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts ausgelöst und das Projekt [erstellt](/dotnet/core/tools/dotnet-build), bevor die Objekte für die Bereitstellung erstellt werden. Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren. Die Bereitstellung wird im Ordner */bin/Release/{Zielframework}/publish* erstellt.

Die Objekte im Ordner *publish* werden auf dem Webserver bereitgestellt. Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.

## <a name="deployment"></a>Bereitstellung

Anleitungen für die Bereitstellung finden Sie in folgenden Themen:

* <xref:host-and-deploy/blazor/client-side>
* <xref:host-and-deploy/blazor/server-side>
