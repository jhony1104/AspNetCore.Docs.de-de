---
title: Beginnen Sie mit ASP.net Core blazor
author: guardrex
description: Beginnen Sie mit blazor, indem Sie eine blazor-App mit den Tools Ihrer Wahl entwickeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/21/2019
uid: blazor/get-started
ms.openlocfilehash: 48d7ff4bf23273daf43128831aa46cfab3d982fe
ms.sourcegitcommit: 897d4abff58505dae86b2947c5fe3d1b80d927f3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73634029"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="71b39-103">Beginnen Sie mit ASP.net Core blazor</span><span class="sxs-lookup"><span data-stu-id="71b39-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="71b39-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="71b39-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="71b39-105">Beginnen Sie mit blazor:</span><span class="sxs-lookup"><span data-stu-id="71b39-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="71b39-106">Installieren Sie das [.net Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="71b39-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="71b39-107">Installieren Sie die Vorlage [blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) , indem Sie den folgenden Befehl in einer Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="71b39-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="71b39-108">Das Paket " [Microsoft. aspnetcore. blazor. Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) " verfügt über eine Vorschauversion, während sich die blazor-Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="71b39-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. <span data-ttu-id="71b39-109">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="71b39-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71b39-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71b39-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="71b39-111">1 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-111">1\.</span></span> <span data-ttu-id="71b39-112">Installieren Sie [Visual Studio 16,4 Preview 2 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="71b39-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="71b39-113">2 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-113">2\.</span></span> <span data-ttu-id="71b39-114">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="71b39-114">Create a new project.</span></span>

   <span data-ttu-id="71b39-115">3 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-115">3\.</span></span> <span data-ttu-id="71b39-116">Wählen Sie **blazor-App**aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-116">Select **Blazor App**.</span></span> <span data-ttu-id="71b39-117">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="71b39-117">Select **Next**.</span></span>

   <span data-ttu-id="71b39-118">4 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-118">4\.</span></span> <span data-ttu-id="71b39-119">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="71b39-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="71b39-120">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="71b39-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="71b39-121">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-121">Select **Create**.</span></span>

   <span data-ttu-id="71b39-122">5 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-122">5\.</span></span> <span data-ttu-id="71b39-123">Wählen Sie für eine blazor-Webassembly die Vorlage " **blazor Webassembly-App** " aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="71b39-124">Wählen Sie für eine blazor-Server-APP die Vorlage für den **blazor-Server** aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="71b39-125">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-125">Select **Create**.</span></span> <span data-ttu-id="71b39-126">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="71b39-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="71b39-127">6 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-127">6\.</span></span> <span data-ttu-id="71b39-128">Drücken Sie **STRG**+**F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="71b39-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="71b39-129">Wenn Sie die Erweiterung blazor Visual Studio für eine vorherige Vorschauversion von ASP.net Core blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="71b39-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="71b39-130">Die Installation der blazor-Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="71b39-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71b39-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71b39-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="71b39-132">1 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-132">1\.</span></span> <span data-ttu-id="71b39-133">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="71b39-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="71b39-134">2 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-134">2\.</span></span> <span data-ttu-id="71b39-135">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="71b39-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="71b39-136">3 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-136">3\.</span></span> <span data-ttu-id="71b39-137">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine blazor-Webassembly zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="71b39-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="71b39-138">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um einen blazor-Server zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="71b39-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="71b39-139">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="71b39-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="71b39-140">4 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-140">4\.</span></span> <span data-ttu-id="71b39-141">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="71b39-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="71b39-142">5 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-142">5\.</span></span> <span data-ttu-id="71b39-143">Für ein blazor-Server Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="71b39-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="71b39-144">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="71b39-144">Select **Yes**.</span></span>

   <span data-ttu-id="71b39-145">6 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-145">6\.</span></span> <span data-ttu-id="71b39-146">Wenn Sie eine blazor-Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="71b39-147">Wenn Sie eine blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="71b39-148">7 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-148">7\.</span></span> <span data-ttu-id="71b39-149">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="71b39-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="71b39-150">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="71b39-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="71b39-151">Führen Sie für eine blazor-Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="71b39-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="71b39-152">Führen Sie für eine blazor-Server Darstellung die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="71b39-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="71b39-153">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="71b39-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="71b39-154">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="71b39-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="71b39-155">Installieren Sie die neueste Version des [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="71b39-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="71b39-156">Installieren Sie optional die Vorlage [blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) , indem Sie das [.net Core 3,1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) installieren und dann den folgenden Befehl in einer Befehlsshell ausführen:</span><span class="sxs-lookup"><span data-stu-id="71b39-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview1.19508.20
   ```

1. <span data-ttu-id="71b39-157">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="71b39-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="71b39-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="71b39-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="71b39-159">1 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-159">1\.</span></span> <span data-ttu-id="71b39-160">Installieren Sie die neueste Version von [Visual Studio](https://visualstudio.com/vs/) mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="71b39-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="71b39-161">2 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-161">2\.</span></span> <span data-ttu-id="71b39-162">Optional können Sie [Visual Studio 16,4 Preview 2 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** für die blazor Webassembly-App-Entwicklung installieren.</span><span class="sxs-lookup"><span data-stu-id="71b39-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="71b39-163">3 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-163">3\.</span></span> <span data-ttu-id="71b39-164">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="71b39-164">Create a new project.</span></span>

   <span data-ttu-id="71b39-165">4 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-165">4\.</span></span> <span data-ttu-id="71b39-166">Wählen Sie **blazor-App**aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-166">Select **Blazor App**.</span></span> <span data-ttu-id="71b39-167">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="71b39-167">Select **Next**.</span></span>

   <span data-ttu-id="71b39-168">5 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-168">5\.</span></span> <span data-ttu-id="71b39-169">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="71b39-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="71b39-170">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="71b39-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="71b39-171">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-171">Select **Create**.</span></span>

   <span data-ttu-id="71b39-172">6 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-172">6\.</span></span> <span data-ttu-id="71b39-173">Wählen Sie für eine blazor-Webassembly die Vorlage " **blazor Webassembly-App** " aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="71b39-174">Wählen Sie für eine blazor-Server-APP die Vorlage für den **blazor-Server** aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="71b39-175">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-175">Select **Create**.</span></span> <span data-ttu-id="71b39-176">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="71b39-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="71b39-177">7 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-177">7\.</span></span> <span data-ttu-id="71b39-178">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="71b39-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="71b39-179">Wenn Sie die Erweiterung blazor Visual Studio für eine vorherige Vorschauversion von ASP.net Core blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="71b39-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="71b39-180">Die Installation der blazor-Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="71b39-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="71b39-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="71b39-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="71b39-182">1 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-182">1\.</span></span> <span data-ttu-id="71b39-183">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="71b39-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="71b39-184">2 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-184">2\.</span></span> <span data-ttu-id="71b39-185">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="71b39-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="71b39-186">3 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-186">3\.</span></span> <span data-ttu-id="71b39-187">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine blazor-Webassembly zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="71b39-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="71b39-188">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um einen blazor-Server zu nutzen:</span><span class="sxs-lookup"><span data-stu-id="71b39-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="71b39-189">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="71b39-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="71b39-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-190">4\.</span></span> <span data-ttu-id="71b39-191">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="71b39-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="71b39-192">5 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-192">5\.</span></span> <span data-ttu-id="71b39-193">Für ein blazor-Server Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="71b39-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="71b39-194">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="71b39-194">Select **Yes**.</span></span>

   <span data-ttu-id="71b39-195">6 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-195">6\.</span></span> <span data-ttu-id="71b39-196">Wenn Sie eine blazor-Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="71b39-197">Wenn Sie eine blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="71b39-198">7 \.</span><span class="sxs-lookup"><span data-stu-id="71b39-198">7\.</span></span> <span data-ttu-id="71b39-199">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="71b39-199">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="71b39-200">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="71b39-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="71b39-201">Führen Sie für eine blazor-Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="71b39-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="71b39-202">Führen Sie für eine blazor-Server Darstellung die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="71b39-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="71b39-203">Informationen zu den beiden blazor-Hostingmodellen, *blazor Server* und *blazor Webassembly*, finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="71b39-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="71b39-204">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="71b39-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="71b39-205">Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:</span><span class="sxs-lookup"><span data-stu-id="71b39-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="71b39-206">POS1</span><span class="sxs-lookup"><span data-stu-id="71b39-206">Home</span></span>
* <span data-ttu-id="71b39-207">Zähler</span><span class="sxs-lookup"><span data-stu-id="71b39-207">Counter</span></span>
* <span data-ttu-id="71b39-208">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="71b39-208">Fetch data</span></span>

<span data-ttu-id="71b39-209">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="71b39-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="71b39-210">Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript, C#aber mit blazor können Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="71b39-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="71b39-211">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="71b39-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="71b39-212">Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter`-Komponente ihren Inhalt wieder gibt.</span><span class="sxs-lookup"><span data-stu-id="71b39-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="71b39-213">Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="71b39-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="71b39-214">Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="71b39-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="71b39-215">Das `onclick`-Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="71b39-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="71b39-216">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="71b39-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="71b39-217">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="71b39-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="71b39-218">Die Komponente wird wieder gerendert.</span><span class="sxs-lookup"><span data-stu-id="71b39-218">The component is rendered again.</span></span>

<span data-ttu-id="71b39-219">Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="71b39-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="71b39-220">Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="71b39-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="71b39-221">Fügen Sie beispielsweise der Startseite der APP die Komponente `Counter` hinzu, indem Sie der Komponente `Index` ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="71b39-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="71b39-222">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="71b39-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="71b39-223">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-223">Run the app.</span></span> <span data-ttu-id="71b39-224">Die Startseite verfügt über einen eigenen Wert, der von der Komponente `Counter` bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="71b39-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="71b39-225">Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können.</span><span class="sxs-lookup"><span data-stu-id="71b39-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="71b39-226">Aktualisieren Sie den Block "`@code`" der Komponente, um der Komponente "`Counter`" einen Parameter hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="71b39-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="71b39-227">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="71b39-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="71b39-228">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="71b39-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="71b39-229">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="71b39-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="71b39-230">Geben Sie den `IncrementAmount` im `<Counter>`-Element der `Index`-Komponente mithilfe eines-Attributs an.</span><span class="sxs-lookup"><span data-stu-id="71b39-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="71b39-231">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="71b39-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="71b39-232">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="71b39-232">Run the app.</span></span> <span data-ttu-id="71b39-233">Die Komponente "`Index`" hat einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="71b39-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="71b39-234">Die Komponente "`Counter`" (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.</span><span class="sxs-lookup"><span data-stu-id="71b39-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="71b39-235">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="71b39-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="71b39-236">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="71b39-236">Additional resources</span></span>

* <xref:signalr/introduction>
