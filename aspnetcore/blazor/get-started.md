---
title: Erste Schritte mit ASP.NET Core Blazor
author: guardrex
description: Beginnen Sie mit der Blazor, indem Sie eine Blazor-App mit den Tools Ihrer Wahl entwickeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 554f4daff92a0839ee7679287a4618e9b51e0fe5
ms.sourcegitcommit: 925cdbd94613243f33bc7613a62ea34006219931
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921304"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="85e9f-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="85e9f-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="85e9f-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="85e9f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="85e9f-105">Beginnen Sie mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="85e9f-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="85e9f-106">Installieren Sie das [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="85e9f-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="85e9f-107">Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="85e9f-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="85e9f-108">Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="85e9f-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="85e9f-109">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-109">Run the following command in a command shell.</span></span> <span data-ttu-id="85e9f-110">[Microsoft.aspnetcore.Blazor.Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="85e9f-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="85e9f-111">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="85e9f-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85e9f-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85e9f-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="85e9f-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-113">1\.</span></span> <span data-ttu-id="85e9f-114">Installieren Sie [Visual Studio 16,4 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="85e9f-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="85e9f-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-115">2\.</span></span> <span data-ttu-id="85e9f-116">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="85e9f-116">Create a new project.</span></span>

   <span data-ttu-id="85e9f-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-117">3\.</span></span> <span data-ttu-id="85e9f-118">Wählen Sie **Blazor App**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-118">Select **Blazor App**.</span></span> <span data-ttu-id="85e9f-119">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="85e9f-119">Select **Next**.</span></span>

   <span data-ttu-id="85e9f-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-120">4\.</span></span> <span data-ttu-id="85e9f-121">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="85e9f-122">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="85e9f-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="85e9f-123">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-123">Select **Create**.</span></span>

   <span data-ttu-id="85e9f-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-124">5\.</span></span> <span data-ttu-id="85e9f-125">Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="85e9f-126">Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="85e9f-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-127">Select **Create**.</span></span> <span data-ttu-id="85e9f-128">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85e9f-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-129">6\.</span></span> <span data-ttu-id="85e9f-130">Drücken Sie **STRG**+**F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="85e9f-131">Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="85e9f-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="85e9f-132">Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85e9f-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85e9f-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="85e9f-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-134">1\.</span></span> <span data-ttu-id="85e9f-135">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="85e9f-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="85e9f-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-136">2\.</span></span> <span data-ttu-id="85e9f-137">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="85e9f-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="85e9f-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-138">3\.</span></span> <span data-ttu-id="85e9f-139">Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="85e9f-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="85e9f-140">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="85e9f-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="85e9f-141">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85e9f-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-142">4\.</span></span> <span data-ttu-id="85e9f-143">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="85e9f-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="85e9f-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-144">5\.</span></span> <span data-ttu-id="85e9f-145">Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="85e9f-146">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="85e9f-146">Select **Yes**.</span></span>

   <span data-ttu-id="85e9f-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-147">6\.</span></span> <span data-ttu-id="85e9f-148">Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="85e9f-149">Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="85e9f-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-150">7\.</span></span> <span data-ttu-id="85e9f-151">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="85e9f-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85e9f-152">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85e9f-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="85e9f-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-153">1\.</span></span> <span data-ttu-id="85e9f-154">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="85e9f-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="85e9f-155">2 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-155">2\.</span></span> <span data-ttu-id="85e9f-156">Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="85e9f-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="85e9f-157">3 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-157">3\.</span></span> <span data-ttu-id="85e9f-158">Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="85e9f-159">4 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-159">4\.</span></span> <span data-ttu-id="85e9f-160">Wählen Sie die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="85e9f-161">Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="85e9f-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="85e9f-162">Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="85e9f-163">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="85e9f-164">5 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-164">5\.</span></span> <span data-ttu-id="85e9f-165">Legen Sie das **Ziel Framework** auf **.net Core 3,1** fest, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="85e9f-166">6 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-166">6\.</span></span> <span data-ttu-id="85e9f-167">Benennen Sie die APP im Feld **Projektname** `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="85e9f-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="85e9f-168">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-168">Select **Create**.</span></span>

   <span data-ttu-id="85e9f-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-169">7\.</span></span> <span data-ttu-id="85e9f-170">Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="85e9f-171">Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="85e9f-172">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="85e9f-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="85e9f-173">Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="85e9f-173">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="85e9f-174">Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="85e9f-174">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="85e9f-175">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-175">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85e9f-176">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="85e9f-176">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="85e9f-177">Installieren Sie das neueste [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span><span class="sxs-lookup"><span data-stu-id="85e9f-177">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="85e9f-178">Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="85e9f-178">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="85e9f-179">Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="85e9f-179">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="85e9f-180">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-180">Run the following command in a command shell.</span></span> <span data-ttu-id="85e9f-181">[Microsoft.aspnetcore.Blazor.Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="85e9f-181">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="85e9f-182">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="85e9f-182">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="85e9f-183">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="85e9f-183">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="85e9f-184">1 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-184">1\.</span></span> <span data-ttu-id="85e9f-185">Installieren Sie die neueste Version von [Visual Studio](https://visualstudio.com/vs/) mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="85e9f-185">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="85e9f-186">2 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-186">2\.</span></span> <span data-ttu-id="85e9f-187">Optional können Sie [Visual Studio 16,4 Preview 2 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** für Blazor Webassembly-App-Entwicklung installieren.</span><span class="sxs-lookup"><span data-stu-id="85e9f-187">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="85e9f-188">3 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-188">3\.</span></span> <span data-ttu-id="85e9f-189">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="85e9f-189">Create a new project.</span></span>

   <span data-ttu-id="85e9f-190">4 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-190">4\.</span></span> <span data-ttu-id="85e9f-191">Wählen Sie **Blazor App**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-191">Select **Blazor App**.</span></span> <span data-ttu-id="85e9f-192">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="85e9f-192">Select **Next**.</span></span>

   <span data-ttu-id="85e9f-193">5 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-193">5\.</span></span> <span data-ttu-id="85e9f-194">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-194">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="85e9f-195">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="85e9f-195">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="85e9f-196">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-196">Select **Create**.</span></span>

   <span data-ttu-id="85e9f-197">6 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-197">6\.</span></span> <span data-ttu-id="85e9f-198">Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-198">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="85e9f-199">Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-199">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="85e9f-200">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-200">Select **Create**.</span></span> <span data-ttu-id="85e9f-201">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-201">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85e9f-202">7 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-202">7\.</span></span> <span data-ttu-id="85e9f-203">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-203">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="85e9f-204">Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="85e9f-204">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="85e9f-205">Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-205">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="85e9f-206">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="85e9f-206">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="85e9f-207">1 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-207">1\.</span></span> <span data-ttu-id="85e9f-208">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="85e9f-208">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="85e9f-209">2 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-209">2\.</span></span> <span data-ttu-id="85e9f-210">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="85e9f-210">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="85e9f-211">3 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-211">3\.</span></span> <span data-ttu-id="85e9f-212">Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="85e9f-212">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="85e9f-213">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="85e9f-213">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="85e9f-214">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-214">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85e9f-215">4 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-215">4\.</span></span> <span data-ttu-id="85e9f-216">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="85e9f-216">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="85e9f-217">5 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-217">5\.</span></span> <span data-ttu-id="85e9f-218">Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-218">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="85e9f-219">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="85e9f-219">Select **Yes**.</span></span>

   <span data-ttu-id="85e9f-220">6 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-220">6\.</span></span> <span data-ttu-id="85e9f-221">Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-221">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="85e9f-222">Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-222">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="85e9f-223">7 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-223">7\.</span></span> <span data-ttu-id="85e9f-224">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="85e9f-224">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="85e9f-225">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="85e9f-225">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="85e9f-226">1 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-226">1\.</span></span> <span data-ttu-id="85e9f-227">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="85e9f-227">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="85e9f-228">Wechseln [Sie zum Aktualisierungs Kanal, um die Vorschau anzuzeigen](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="85e9f-228">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="85e9f-229">2 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-229">2\.</span></span> <span data-ttu-id="85e9f-230">Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="85e9f-230">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="85e9f-231">3 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-231">3\.</span></span> <span data-ttu-id="85e9f-232">Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-232">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="85e9f-233">4 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-233">4\.</span></span> <span data-ttu-id="85e9f-234">Wählen Sie die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-234">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="85e9f-235">Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="85e9f-235">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="85e9f-236">Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-236">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="85e9f-237">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-237">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="85e9f-238">5 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-238">5\.</span></span> <span data-ttu-id="85e9f-239">Legen Sie das **Ziel Framework** auf **.net Core 3,0** fest, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-239">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="85e9f-240">6 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-240">6\.</span></span> <span data-ttu-id="85e9f-241">Benennen Sie die APP im Feld **Projektname** `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="85e9f-241">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="85e9f-242">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-242">Select **Create**.</span></span>

   <span data-ttu-id="85e9f-243">7 \.</span><span class="sxs-lookup"><span data-stu-id="85e9f-243">7\.</span></span> <span data-ttu-id="85e9f-244">Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-244">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="85e9f-245">Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-245">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="85e9f-246">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="85e9f-246">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="85e9f-247">Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="85e9f-247">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="85e9f-248">Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="85e9f-248">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="85e9f-249">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="85e9f-249">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="85e9f-250">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="85e9f-250">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="85e9f-251">Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:</span><span class="sxs-lookup"><span data-stu-id="85e9f-251">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="85e9f-252">-Startseite</span><span class="sxs-lookup"><span data-stu-id="85e9f-252">Home</span></span>
* <span data-ttu-id="85e9f-253">Zähler</span><span class="sxs-lookup"><span data-stu-id="85e9f-253">Counter</span></span>
* <span data-ttu-id="85e9f-254">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="85e9f-254">Fetch data</span></span>

<span data-ttu-id="85e9f-255">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-255">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="85e9f-256">Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript C#, aber mit Blazor können Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="85e9f-256">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="85e9f-257">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="85e9f-257">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="85e9f-258">Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter` Komponente ihren Inhalt wieder gibt.</span><span class="sxs-lookup"><span data-stu-id="85e9f-258">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="85e9f-259">Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="85e9f-259">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="85e9f-260">Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="85e9f-260">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="85e9f-261">Das `onclick` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="85e9f-261">The `onclick` event is fired.</span></span>
* <span data-ttu-id="85e9f-262">Die `IncrementCount`-Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-262">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="85e9f-263">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="85e9f-263">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="85e9f-264">Die Komponente wird wieder gerendert.</span><span class="sxs-lookup"><span data-stu-id="85e9f-264">The component is rendered again.</span></span>

<span data-ttu-id="85e9f-265">Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="85e9f-265">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="85e9f-266">Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="85e9f-266">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="85e9f-267">Fügen Sie z. b. der Startseite der APP die `Counter` Komponente hinzu, indem Sie der `Index` Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="85e9f-267">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="85e9f-268">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="85e9f-268">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="85e9f-269">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-269">Run the app.</span></span> <span data-ttu-id="85e9f-270">Die Startseite verfügt über einen eigenen Counter, der von der `Counter` Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="85e9f-270">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="85e9f-271">Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können.</span><span class="sxs-lookup"><span data-stu-id="85e9f-271">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="85e9f-272">Um der `Counter` Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code` Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="85e9f-272">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="85e9f-273">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="85e9f-273">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="85e9f-274">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="85e9f-274">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="85e9f-275">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="85e9f-275">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="85e9f-276">Geben Sie die `IncrementAmount` im `<Counter>`-Element der `Index` Komponente mithilfe eines-Attributs an.</span><span class="sxs-lookup"><span data-stu-id="85e9f-276">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="85e9f-277">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="85e9f-277">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="85e9f-278">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="85e9f-278">Run the app.</span></span> <span data-ttu-id="85e9f-279">Die `Index` Komponente verfügt über einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="85e9f-279">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="85e9f-280">Die `Counter` Komponente (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.</span><span class="sxs-lookup"><span data-stu-id="85e9f-280">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="85e9f-281">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="85e9f-281">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="85e9f-282">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="85e9f-282">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
