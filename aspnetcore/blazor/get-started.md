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
ms.openlocfilehash: 2135c2a090d60ec7a46fa4f899f0f14987b6b4e0
ms.sourcegitcommit: 2388c2a7334ce66b6be3ffbab06dd7923df18f60
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75951723"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="17884-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="17884-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="17884-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="17884-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="17884-105">Beginnen Sie mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="17884-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="17884-106">Installieren Sie das [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="17884-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="17884-107">Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="17884-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="17884-108">Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="17884-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="17884-109">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="17884-109">Run the following command in a command shell.</span></span> <span data-ttu-id="17884-110">[Microsoft.aspnetcore.Blazor.Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="17884-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="17884-111">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="17884-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="17884-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17884-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="17884-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="17884-113">1\.</span></span> <span data-ttu-id="17884-114">Installieren Sie [Visual Studio 16,4 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="17884-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="17884-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="17884-115">2\.</span></span> <span data-ttu-id="17884-116">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="17884-116">Create a new project.</span></span>

   <span data-ttu-id="17884-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="17884-117">3\.</span></span> <span data-ttu-id="17884-118">Wählen Sie **Blazor App**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-118">Select **Blazor App**.</span></span> <span data-ttu-id="17884-119">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="17884-119">Select **Next**.</span></span>

   <span data-ttu-id="17884-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="17884-120">4\.</span></span> <span data-ttu-id="17884-121">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="17884-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="17884-122">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="17884-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="17884-123">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-123">Select **Create**.</span></span>

   <span data-ttu-id="17884-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="17884-124">5\.</span></span> <span data-ttu-id="17884-125">Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="17884-126">Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="17884-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="17884-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-127">Select **Create**.</span></span> <span data-ttu-id="17884-128">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="17884-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="17884-129">6\.</span></span> <span data-ttu-id="17884-130">Drücken Sie **STRG**+**F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="17884-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="17884-131">Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="17884-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="17884-132">Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="17884-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="17884-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17884-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="17884-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="17884-134">1\.</span></span> <span data-ttu-id="17884-135">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="17884-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="17884-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="17884-136">2\.</span></span> <span data-ttu-id="17884-137">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="17884-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="17884-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="17884-138">3\.</span></span> <span data-ttu-id="17884-139">Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="17884-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="17884-140">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="17884-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="17884-141">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="17884-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="17884-142">4\.</span></span> <span data-ttu-id="17884-143">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="17884-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="17884-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="17884-144">5\.</span></span> <span data-ttu-id="17884-145">Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="17884-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="17884-146">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="17884-146">Select **Yes**.</span></span>

   <span data-ttu-id="17884-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="17884-147">6\.</span></span> <span data-ttu-id="17884-148">Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="17884-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="17884-149">Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="17884-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="17884-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="17884-150">7\.</span></span> <span data-ttu-id="17884-151">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="17884-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="17884-152">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="17884-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="17884-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="17884-153">1\.</span></span> <span data-ttu-id="17884-154">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="17884-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="17884-155">2 \.</span><span class="sxs-lookup"><span data-stu-id="17884-155">2\.</span></span> <span data-ttu-id="17884-156">Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="17884-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="17884-157">3 \.</span><span class="sxs-lookup"><span data-stu-id="17884-157">3\.</span></span> <span data-ttu-id="17884-158">Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="17884-159">4 \.</span><span class="sxs-lookup"><span data-stu-id="17884-159">4\.</span></span> <span data-ttu-id="17884-160">Wählen Sie die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="17884-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="17884-161">Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="17884-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="17884-162">Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="17884-163">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="17884-164">5 \.</span><span class="sxs-lookup"><span data-stu-id="17884-164">5\.</span></span> <span data-ttu-id="17884-165">Legen Sie das **Ziel Framework** auf **.net Core 3,1** fest, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="17884-166">6 \.</span><span class="sxs-lookup"><span data-stu-id="17884-166">6\.</span></span> <span data-ttu-id="17884-167">Benennen Sie die APP im Feld **Projektname** `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="17884-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="17884-168">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-168">Select **Create**.</span></span>

   <span data-ttu-id="17884-169">7 \.</span><span class="sxs-lookup"><span data-stu-id="17884-169">7\.</span></span> <span data-ttu-id="17884-170">Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="17884-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="17884-171">Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="17884-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="17884-172">Wenn eine Eingabeaufforderung zum vertrauenswürdigen Entwicklungs Zertifikat angezeigt wird, Vertrauen Sie dem Zertifikat, und fahren Sie fort.</span><span class="sxs-lookup"><span data-stu-id="17884-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="17884-173">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="17884-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="17884-174">Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="17884-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="17884-175">Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="17884-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="17884-176">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="17884-177">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="17884-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="17884-178">Installieren Sie das neueste [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span><span class="sxs-lookup"><span data-stu-id="17884-178">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="17884-179">Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="17884-179">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="17884-180">Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="17884-180">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="17884-181">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="17884-181">Run the following command in a command shell.</span></span> <span data-ttu-id="17884-182">[Microsoft.aspnetcore.Blazor.Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="17884-182">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="17884-183">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="17884-183">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="17884-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="17884-184">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="17884-185">1 \.</span><span class="sxs-lookup"><span data-stu-id="17884-185">1\.</span></span> <span data-ttu-id="17884-186">Installieren Sie die neueste Version von [Visual Studio](https://visualstudio.com/vs/) mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="17884-186">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="17884-187">2 \.</span><span class="sxs-lookup"><span data-stu-id="17884-187">2\.</span></span> <span data-ttu-id="17884-188">Optional können Sie [Visual Studio 16,4 Preview 2 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** für Blazor Webassembly-App-Entwicklung installieren.</span><span class="sxs-lookup"><span data-stu-id="17884-188">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="17884-189">3 \.</span><span class="sxs-lookup"><span data-stu-id="17884-189">3\.</span></span> <span data-ttu-id="17884-190">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="17884-190">Create a new project.</span></span>

   <span data-ttu-id="17884-191">4 \.</span><span class="sxs-lookup"><span data-stu-id="17884-191">4\.</span></span> <span data-ttu-id="17884-192">Wählen Sie **Blazor App**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-192">Select **Blazor App**.</span></span> <span data-ttu-id="17884-193">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="17884-193">Select **Next**.</span></span>

   <span data-ttu-id="17884-194">5 \.</span><span class="sxs-lookup"><span data-stu-id="17884-194">5\.</span></span> <span data-ttu-id="17884-195">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="17884-195">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="17884-196">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="17884-196">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="17884-197">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-197">Select **Create**.</span></span>

   <span data-ttu-id="17884-198">6 \.</span><span class="sxs-lookup"><span data-stu-id="17884-198">6\.</span></span> <span data-ttu-id="17884-199">Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-199">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="17884-200">Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="17884-200">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="17884-201">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-201">Select **Create**.</span></span> <span data-ttu-id="17884-202">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-202">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="17884-203">7 \.</span><span class="sxs-lookup"><span data-stu-id="17884-203">7\.</span></span> <span data-ttu-id="17884-204">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="17884-204">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="17884-205">Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="17884-205">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="17884-206">Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="17884-206">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="17884-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="17884-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="17884-208">1 \.</span><span class="sxs-lookup"><span data-stu-id="17884-208">1\.</span></span> <span data-ttu-id="17884-209">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="17884-209">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="17884-210">2 \.</span><span class="sxs-lookup"><span data-stu-id="17884-210">2\.</span></span> <span data-ttu-id="17884-211">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="17884-211">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="17884-212">3 \.</span><span class="sxs-lookup"><span data-stu-id="17884-212">3\.</span></span> <span data-ttu-id="17884-213">Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="17884-213">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="17884-214">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="17884-214">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="17884-215">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-215">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="17884-216">4 \.</span><span class="sxs-lookup"><span data-stu-id="17884-216">4\.</span></span> <span data-ttu-id="17884-217">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="17884-217">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="17884-218">5 \.</span><span class="sxs-lookup"><span data-stu-id="17884-218">5\.</span></span> <span data-ttu-id="17884-219">Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="17884-219">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="17884-220">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="17884-220">Select **Yes**.</span></span>

   <span data-ttu-id="17884-221">6 \.</span><span class="sxs-lookup"><span data-stu-id="17884-221">6\.</span></span> <span data-ttu-id="17884-222">Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="17884-222">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="17884-223">Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="17884-223">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="17884-224">7 \.</span><span class="sxs-lookup"><span data-stu-id="17884-224">7\.</span></span> <span data-ttu-id="17884-225">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="17884-225">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="17884-226">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="17884-226">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="17884-227">1 \.</span><span class="sxs-lookup"><span data-stu-id="17884-227">1\.</span></span> <span data-ttu-id="17884-228">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="17884-228">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="17884-229">Wechseln [Sie zum Aktualisierungs Kanal, um die Vorschau anzuzeigen](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="17884-229">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="17884-230">2 \.</span><span class="sxs-lookup"><span data-stu-id="17884-230">2\.</span></span> <span data-ttu-id="17884-231">Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="17884-231">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="17884-232">3 \.</span><span class="sxs-lookup"><span data-stu-id="17884-232">3\.</span></span> <span data-ttu-id="17884-233">Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-233">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="17884-234">4 \.</span><span class="sxs-lookup"><span data-stu-id="17884-234">4\.</span></span> <span data-ttu-id="17884-235">Wählen Sie die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="17884-235">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="17884-236">Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="17884-236">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="17884-237">Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-237">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="17884-238">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-238">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="17884-239">5 \.</span><span class="sxs-lookup"><span data-stu-id="17884-239">5\.</span></span> <span data-ttu-id="17884-240">Legen Sie das **Ziel Framework** auf **.net Core 3,0** fest, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="17884-240">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="17884-241">6 \.</span><span class="sxs-lookup"><span data-stu-id="17884-241">6\.</span></span> <span data-ttu-id="17884-242">Benennen Sie die APP im Feld **Projektname** `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="17884-242">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="17884-243">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="17884-243">Select **Create**.</span></span>

   <span data-ttu-id="17884-244">7 \.</span><span class="sxs-lookup"><span data-stu-id="17884-244">7\.</span></span> <span data-ttu-id="17884-245">Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="17884-245">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="17884-246">Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="17884-246">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="17884-247">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="17884-247">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="17884-248">Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="17884-248">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="17884-249">Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="17884-249">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="17884-250">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="17884-250">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="17884-251">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="17884-251">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="17884-252">Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:</span><span class="sxs-lookup"><span data-stu-id="17884-252">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="17884-253">-Startseite</span><span class="sxs-lookup"><span data-stu-id="17884-253">Home</span></span>
* <span data-ttu-id="17884-254">Zähler</span><span class="sxs-lookup"><span data-stu-id="17884-254">Counter</span></span>
* <span data-ttu-id="17884-255">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="17884-255">Fetch data</span></span>

<span data-ttu-id="17884-256">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="17884-256">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="17884-257">Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript C#, aber mit Blazor können Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="17884-257">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="17884-258">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="17884-258">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="17884-259">Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter` Komponente ihren Inhalt wieder gibt.</span><span class="sxs-lookup"><span data-stu-id="17884-259">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="17884-260">Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="17884-260">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="17884-261">Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="17884-261">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="17884-262">Das `onclick` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="17884-262">The `onclick` event is fired.</span></span>
* <span data-ttu-id="17884-263">Die `IncrementCount`-Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="17884-263">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="17884-264">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="17884-264">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="17884-265">Die Komponente wird wieder gerendert.</span><span class="sxs-lookup"><span data-stu-id="17884-265">The component is rendered again.</span></span>

<span data-ttu-id="17884-266">Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="17884-266">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="17884-267">Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="17884-267">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="17884-268">Fügen Sie z. b. der Startseite der APP die `Counter` Komponente hinzu, indem Sie der `Index` Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="17884-268">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="17884-269">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="17884-269">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="17884-270">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="17884-270">Run the app.</span></span> <span data-ttu-id="17884-271">Die Startseite verfügt über einen eigenen Counter, der von der `Counter` Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="17884-271">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="17884-272">Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können.</span><span class="sxs-lookup"><span data-stu-id="17884-272">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="17884-273">Um der `Counter` Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code` Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="17884-273">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="17884-274">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="17884-274">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="17884-275">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="17884-275">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="17884-276">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="17884-276">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="17884-277">Geben Sie die `IncrementAmount` im `<Counter>`-Element der `Index` Komponente mithilfe eines-Attributs an.</span><span class="sxs-lookup"><span data-stu-id="17884-277">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="17884-278">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="17884-278">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="17884-279">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="17884-279">Run the app.</span></span> <span data-ttu-id="17884-280">Die `Index` Komponente verfügt über einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="17884-280">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="17884-281">Die `Counter` Komponente (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.</span><span class="sxs-lookup"><span data-stu-id="17884-281">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="17884-282">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="17884-282">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="17884-283">Weitere Ressourcen</span><span class="sxs-lookup"><span data-stu-id="17884-283">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
