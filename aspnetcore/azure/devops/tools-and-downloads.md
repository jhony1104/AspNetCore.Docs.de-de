---
title: Tools und Downloads - DevOps mit ASP.NET Core und Azure
author: CamSoper
description: Tools und Downloads, die für DevOps mit ASP.NET Core und Azure erforderlich sind.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 25ce311373b0aaddfa3bc2728c39e503acbca69d
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71080639"
---
# <a name="tools-and-downloads"></a>Tools und downloads

Azure verfügt über mehrere Schnittstellen für die Bereitstellung und Verwaltung von Ressourcen, z. B. die [Azure-Portal](https://portal.azure.com), [Azure-Befehlszeilenschnittstelle](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure-Cloud Shell](https://shell.azure.com/bash), und Visual Studio. Dieses Handbuch verwendet einen minimalistischen Ansatz und die Azure Cloud Shell nach Möglichkeit, um die erforderlichen Schritte zu reduzieren. Allerdings muss das Azure-Portal für einige Teile verwendet werden.

## <a name="prerequisites"></a>Vorraussetzungen

Die folgenden Abonnements sind erforderlich:

* Azure &mdash; , wenn Sie nicht über ein Konto verfügen [erhalten eine kostenlose Testversion](https://azure.microsoft.com/free/).
* Azure DevOps-Dienste &mdash; wird Ihr Azure DevOps-Abonnement und Ihre Organisation erstellt, in Kapitel 4.
* GitHub &mdash; , wenn Sie nicht über ein Konto verfügen [melden Sie sich jetzt](https://github.com/join).

Die folgenden Tools sind erforderlich:

* [Git](https://git-scm.com/downloads) &mdash; ein grundlegender Überblick über Git wird in diesem Handbuch empfohlen. Überprüfen Sie die [Git-Dokumentation](https://git-scm.com/doc), insbesondere [Git-Remoterepository](https://git-scm.com/docs/git-remote) und [Git-Push](https://git-scm.com/docs/git-push).
* [.NET Core SDK](https://www.microsoft.com/net/download/) &mdash; 2.1.300-Version oder höher erstellen und Ausführen der Beispiel-app erforderlich ist. Wenn die Installation von Visual Studio mit der **plattformübergreifende Entwicklung mit .NET Core** Workload, die .NET Core SDK ist bereits installiert.

    Überprüfen Sie die .NET Core SDK-Installation. Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl:

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a>Empfohlene Tools (nur Windows)

* [Visual Studio](https://visualstudio.microsoft.com)des robuste Azure-Tools bieten einer grafische Benutzeroberfläche für den Großteil der Funktionalität, die in diesem Handbuch beschrieben. Eine beliebige Edition von Visual Studio funktionieren, einschließlich der kostenlosen Visual Studio Community Edition. In den Tutorials werden geschrieben, um Entwicklung und Bereitstellung von DevOps mit und ohne Visual Studio veranschaulichen.

  Vergewissern Sie sich, dass Visual Studio die folgenden verfügt [Workloads](/visualstudio/install/modify-visual-studio) installiert:

  * ASP.NET und Webentwicklung
  * Azure-Entwicklung
  * Plattformübergreifende .NET Core-Entwicklung
