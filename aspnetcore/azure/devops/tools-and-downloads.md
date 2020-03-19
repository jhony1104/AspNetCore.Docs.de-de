---
title: 'Tools und Downloads: DevOps mit ASP.NET Core und Azure'
author: CamSoper
description: Für DevOps mit ASP.NET Core und Azure erforderliche Tools und Downloads.
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/tools-and-downloads
ms.openlocfilehash: 9c1042dd48b9167209b46e97a09e011b80e2609c
ms.sourcegitcommit: d64ef143c64ee4fdade8f9ea0b753b16752c5998
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511144"
---
# <a name="tools-and-downloads"></a><span data-ttu-id="cf079-103">Tools und Downloads</span><span class="sxs-lookup"><span data-stu-id="cf079-103">Tools and downloads</span></span>

<span data-ttu-id="cf079-104">Azure verfügt über mehrere Schnittstellen für die Bereitstellung und Verwaltung von Ressourcen wie das [Azure-Portal](https://portal.azure.com), die [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash) und Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cf079-104">Azure has several interfaces for provisioning and managing resources, such as the [Azure portal](https://portal.azure.com), [Azure CLI](/cli/azure/), [Azure PowerShell](/powershell/azure/overview), [Azure Cloud Shell](https://shell.azure.com/bash), and Visual Studio.</span></span> <span data-ttu-id="cf079-105">In diesem Leitfaden wird ein minimalistischer Ansatz verwendet, bei dem möglichst häufig Azure Cloud Shell zum Einsatz kommt, um die Anzahl der erforderlichen Schritte zu verringern.</span><span class="sxs-lookup"><span data-stu-id="cf079-105">This guide takes a minimalist approach and uses the Azure Cloud Shell whenever possible to reduce the steps required.</span></span> <span data-ttu-id="cf079-106">Für manche Schritte muss jedoch das Azure-Portal verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="cf079-106">However, the Azure portal must be used for some portions.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="cf079-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="cf079-107">Prerequisites</span></span>

<span data-ttu-id="cf079-108">Folgende Abonnements sind erforderlich:</span><span class="sxs-lookup"><span data-stu-id="cf079-108">The following subscriptions are required:</span></span>

* <span data-ttu-id="cf079-109">Azure &mdash; Wenn Sie über kein Konto verfügen, [laden Sie eine kostenlose Testversion herunter](https://azure.microsoft.com/free/).</span><span class="sxs-lookup"><span data-stu-id="cf079-109">Azure &mdash; If you don't have an account, [get a free trial](https://azure.microsoft.com/free/).</span></span>
* <span data-ttu-id="cf079-110">Azure DevOps Services &mdash; Ihr Azure DevOps-Abonnement und Ihre Organisation werden in Kapitel 4 erstellt.</span><span class="sxs-lookup"><span data-stu-id="cf079-110">Azure DevOps Services &mdash; your Azure DevOps subscription and organization is created in Chapter 4.</span></span>
* <span data-ttu-id="cf079-111">GitHub &mdash; Falls Sie noch kein Konto besitzen, können Sie sich [kostenlos dafür registrieren](https://github.com/join).</span><span class="sxs-lookup"><span data-stu-id="cf079-111">GitHub &mdash; If you don't have an account, [sign up for free](https://github.com/join).</span></span>

<span data-ttu-id="cf079-112">Die folgenden Tools werden benötigt:</span><span class="sxs-lookup"><span data-stu-id="cf079-112">The following tools are required:</span></span>

* <span data-ttu-id="cf079-113">[Git](https://git-scm.com/downloads) &mdash; Grundlegende Kenntnisse von Git werden für diesen Leitfaden empfohlen.</span><span class="sxs-lookup"><span data-stu-id="cf079-113">[Git](https://git-scm.com/downloads) &mdash; A fundamental understanding of Git is recommended for this guide.</span></span> <span data-ttu-id="cf079-114">Lesen Sie die [Git-Dokumentation](https://git-scm.com/doc), insbesondere [git remote](https://git-scm.com/docs/git-remote) und [git push](https://git-scm.com/docs/git-push).</span><span class="sxs-lookup"><span data-stu-id="cf079-114">Review the [Git documentation](https://git-scm.com/doc), specifically [git remote](https://git-scm.com/docs/git-remote) and [git push](https://git-scm.com/docs/git-push).</span></span>
* <span data-ttu-id="cf079-115">[.Net Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 oder höher ist erforderlich, um die Beispiel-App zu erstellen und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="cf079-115">[.NET Core SDK](https://dotnet.microsoft.com/download/) &mdash; Version 2.1.300 or later is required to build and run the sample app.</span></span> <span data-ttu-id="cf079-116">Wenn Visual Studio mit der **Plattformübergreifenden .NET Core-Entwicklung** installiert wird, ist das .NET Core SDK bereits installiert.</span><span class="sxs-lookup"><span data-stu-id="cf079-116">If Visual Studio is installed with the **.NET Core cross-platform development** workload, the .NET Core SDK is already installed.</span></span>

    <span data-ttu-id="cf079-117">Überprüfen Sie Ihre .NET Core SDK-Installation.</span><span class="sxs-lookup"><span data-stu-id="cf079-117">Verify your .NET Core SDK installation.</span></span> <span data-ttu-id="cf079-118">Öffnen Sie eine Befehlsshell, und führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="cf079-118">Open a command shell, and run the following command:</span></span>

    ```dotnetcli
    dotnet --version
    ```

## <a name="recommended-tools-windows-only"></a><span data-ttu-id="cf079-119">Empfohlene Tools (nur Windows)</span><span class="sxs-lookup"><span data-stu-id="cf079-119">Recommended tools (Windows only)</span></span>

* <span data-ttu-id="cf079-120">Die stabilen Azure-Tools von [Visual Studio](https://visualstudio.microsoft.com) bieten eine GUI für die meisten der in diesem Handbuch beschriebenen Funktionen.</span><span class="sxs-lookup"><span data-stu-id="cf079-120">[Visual Studio](https://visualstudio.microsoft.com)'s robust Azure tools provide a GUI for most of the functionality described in this guide.</span></span> <span data-ttu-id="cf079-121">Sie können jede beliebige Edition von Visual Studio verwenden, einschließlich der kostenlosen Visual Studio Community Edition.</span><span class="sxs-lookup"><span data-stu-id="cf079-121">Any edition of Visual Studio will work, including the free Visual Studio Community Edition.</span></span> <span data-ttu-id="cf079-122">Die Tutorials dienen der Veranschaulichung von Entwicklung, Bereitstellung und DevOps sowohl mit als auch ohne Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cf079-122">The tutorials are written to demonstrate development, deployment, and DevOps both with and without Visual Studio.</span></span>

  <span data-ttu-id="cf079-123">Vergewissern Sie sich, dass für Visual Studio die folgenden [Workloads](/visualstudio/install/modify-visual-studio) installiert sind:</span><span class="sxs-lookup"><span data-stu-id="cf079-123">Confirm that Visual Studio has the following [workloads](/visualstudio/install/modify-visual-studio) installed:</span></span>

  * <span data-ttu-id="cf079-124">ASP.NET und Webentwicklung</span><span class="sxs-lookup"><span data-stu-id="cf079-124">ASP.NET and web development</span></span>
  * <span data-ttu-id="cf079-125">Azure-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="cf079-125">Azure development</span></span>
  * <span data-ttu-id="cf079-126">Plattformübergreifende .NET Core-Entwicklung</span><span class="sxs-lookup"><span data-stu-id="cf079-126">.NET Core cross-platform development</span></span>
