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
ms.openlocfilehash: e368ecaf931d392de7e52ec2d5a2dfd171c2c86f
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943763"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="1162c-103">Erste Schritte mit ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="1162c-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="1162c-104">Von [Daniel Roth](https://github.com/danroth27) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1162c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="1162c-105">Beginnen Sie mit Blazor:</span><span class="sxs-lookup"><span data-stu-id="1162c-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="1162c-106">Installieren Sie das [.net Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="1162c-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="1162c-107">Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="1162c-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="1162c-108">Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="1162c-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="1162c-109">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-109">Run the following command in a command shell.</span></span> <span data-ttu-id="1162c-110">[Microsoft. aspnetcore.Blazor. Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="1162c-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="1162c-111">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="1162c-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1162c-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1162c-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="1162c-113">1 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-113">1\.</span></span> <span data-ttu-id="1162c-114">Installieren Sie [Visual Studio 16,4 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="1162c-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="1162c-115">2 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-115">2\.</span></span> <span data-ttu-id="1162c-116">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="1162c-116">Create a new project.</span></span>

   <span data-ttu-id="1162c-117">3 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-117">3\.</span></span> <span data-ttu-id="1162c-118">Wählen Sie **Blazor App**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-118">Select **Blazor App**.</span></span> <span data-ttu-id="1162c-119">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="1162c-119">Select **Next**.</span></span>

   <span data-ttu-id="1162c-120">4 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-120">4\.</span></span> <span data-ttu-id="1162c-121">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="1162c-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1162c-122">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="1162c-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1162c-123">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-123">Select **Create**.</span></span>

   <span data-ttu-id="1162c-124">5 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-124">5\.</span></span> <span data-ttu-id="1162c-125">Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1162c-126">Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1162c-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-127">Select **Create**.</span></span> <span data-ttu-id="1162c-128">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="1162c-129">6 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-129">6\.</span></span> <span data-ttu-id="1162c-130">Drücken Sie **STRG**+**F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1162c-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="1162c-131">Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="1162c-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="1162c-132">Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="1162c-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1162c-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1162c-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="1162c-134">1 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-134">1\.</span></span> <span data-ttu-id="1162c-135">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="1162c-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="1162c-136">2 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-136">2\.</span></span> <span data-ttu-id="1162c-137">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="1162c-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="1162c-138">3 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-138">3\.</span></span> <span data-ttu-id="1162c-139">Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="1162c-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="1162c-140">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="1162c-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="1162c-141">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="1162c-142">4 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-142">4\.</span></span> <span data-ttu-id="1162c-143">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1162c-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="1162c-144">5 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-144">5\.</span></span> <span data-ttu-id="1162c-145">Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="1162c-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="1162c-146">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="1162c-146">Select **Yes**.</span></span>

   <span data-ttu-id="1162c-147">6 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-147">6\.</span></span> <span data-ttu-id="1162c-148">Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="1162c-149">Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="1162c-150">7 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-150">7\.</span></span> <span data-ttu-id="1162c-151">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1162c-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1162c-152">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1162c-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="1162c-153">1 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-153">1\.</span></span> <span data-ttu-id="1162c-154">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="1162c-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="1162c-155">Wechseln [Sie zum Aktualisierungs Kanal, um die Vorschau anzuzeigen](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="1162c-155">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="1162c-156">2 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-156">2\.</span></span> <span data-ttu-id="1162c-157">Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1162c-157">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="1162c-158">3 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-158">3\.</span></span> <span data-ttu-id="1162c-159">Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-159">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="1162c-160">4 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-160">4\.</span></span> <span data-ttu-id="1162c-161">Wählen Sie die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-161">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="1162c-162">Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1162c-162">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="1162c-163">Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-163">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="1162c-164">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-164">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="1162c-165">5 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-165">5\.</span></span> <span data-ttu-id="1162c-166">Legen Sie das **Ziel Framework** auf **.net Core 3,1** fest, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-166">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="1162c-167">6 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-167">6\.</span></span> <span data-ttu-id="1162c-168">Benennen Sie die APP im Feld **Projektname** `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="1162c-168">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="1162c-169">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-169">Select **Create**.</span></span>

   <span data-ttu-id="1162c-170">7 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-170">7\.</span></span> <span data-ttu-id="1162c-171">Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1162c-171">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="1162c-172">Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1162c-172">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1162c-173">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="1162c-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="1162c-174">Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="1162c-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="1162c-175">Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="1162c-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="1162c-176">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="1162c-177">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1162c-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="1162c-178">Installieren Sie das neueste [.net Core 3,0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span><span class="sxs-lookup"><span data-stu-id="1162c-178">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="1162c-179">Installieren Sie optional die Vorlage [Blazor Webassembly](xref:blazor/hosting-models#blazor-webassembly) :</span><span class="sxs-lookup"><span data-stu-id="1162c-179">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="1162c-180">Installieren Sie das [.net Core 3,1-oder spätere SDK (Vorschauversion)](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span><span class="sxs-lookup"><span data-stu-id="1162c-180">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="1162c-181">Führen Sie den folgenden Befehl in einer Befehlsshell aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-181">Run the following command in a command shell.</span></span> <span data-ttu-id="1162c-182">[Microsoft. aspnetcore.Blazor. Das Vorlagen](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) Paket verfügt über eine Vorschauversion, während sich Blazor Webassembly in der Vorschau Phase befindet.</span><span class="sxs-lookup"><span data-stu-id="1162c-182">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="1162c-183">Befolgen Sie die Anleitungen für die Auswahl der Tools:</span><span class="sxs-lookup"><span data-stu-id="1162c-183">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1162c-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1162c-184">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="1162c-185">1 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-185">1\.</span></span> <span data-ttu-id="1162c-186">Installieren Sie die neueste Version von [Visual Studio](https://visualstudio.com/vs/) mit der Arbeitsauslastung **ASP.net und Webentwicklung** .</span><span class="sxs-lookup"><span data-stu-id="1162c-186">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="1162c-187">2 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-187">2\.</span></span> <span data-ttu-id="1162c-188">Optional können Sie [Visual Studio 16,4 Preview 2 oder](https://visualstudio.microsoft.com/vs/preview/) höher mit der Arbeitsauslastung **ASP.net und Webentwicklung** für Blazor Webassembly-App-Entwicklung installieren.</span><span class="sxs-lookup"><span data-stu-id="1162c-188">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="1162c-189">3 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-189">3\.</span></span> <span data-ttu-id="1162c-190">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="1162c-190">Create a new project.</span></span>

   <span data-ttu-id="1162c-191">4 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-191">4\.</span></span> <span data-ttu-id="1162c-192">Wählen Sie **Blazor App**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-192">Select **Blazor App**.</span></span> <span data-ttu-id="1162c-193">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="1162c-193">Select **Next**.</span></span>

   <span data-ttu-id="1162c-194">5 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-194">5\.</span></span> <span data-ttu-id="1162c-195">Geben Sie im Feld **Projektname** einen Projektnamen ein, oder übernehmen Sie den Standardnamen.</span><span class="sxs-lookup"><span data-stu-id="1162c-195">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="1162c-196">Vergewissern Sie sich, dass der **Orts** Eintrag korrekt ist, oder geben Sie einen Speicherort für das Projekt</span><span class="sxs-lookup"><span data-stu-id="1162c-196">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="1162c-197">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-197">Select **Create**.</span></span>

   <span data-ttu-id="1162c-198">6 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-198">6\.</span></span> <span data-ttu-id="1162c-199">Wählen Sie für eine Blazor Webassembly die Vorlage **Blazor Webassembly-App** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-199">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="1162c-200">Wählen Sie für eine Blazor Server-Darstellung die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-200">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="1162c-201">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-201">Select **Create**.</span></span> <span data-ttu-id="1162c-202">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-202">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="1162c-203">7 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-203">7\.</span></span> <span data-ttu-id="1162c-204">Drücken Sie **F5**, um die App auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1162c-204">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="1162c-205">Wenn Sie die Blazor Visual Studio-Erweiterung für eine vorherige Vorschauversion von ASP.net Core Blazor (Preview 6 oder früher) installiert haben, können Sie die Erweiterung deinstallieren.</span><span class="sxs-lookup"><span data-stu-id="1162c-205">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="1162c-206">Die Installation der Blazor Vorlagen in einer Befehlsshell reicht nun aus, um die Vorlagen in Visual Studio zu überstehen.</span><span class="sxs-lookup"><span data-stu-id="1162c-206">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1162c-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1162c-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="1162c-208">1 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-208">1\.</span></span> <span data-ttu-id="1162c-209">Installieren Sie [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="1162c-209">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="1162c-210">2 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-210">2\.</span></span> <span data-ttu-id="1162c-211">Installieren Sie die neueste [ C# für Visual Studio Code Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="1162c-211">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="1162c-212">3 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-212">3\.</span></span> <span data-ttu-id="1162c-213">Führen Sie für eine Blazor Webassembly den folgenden Befehl in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="1162c-213">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="1162c-214">Führen Sie den folgenden Befehl in einer Befehlsshell aus, um eine Blazor Server-Funktion zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="1162c-214">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="1162c-215">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-215">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="1162c-216">4 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-216">4\.</span></span> <span data-ttu-id="1162c-217">Öffnen Sie den Ordner *WebApplication1* in Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="1162c-217">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="1162c-218">5 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-218">5\.</span></span> <span data-ttu-id="1162c-219">Bei einem Blazor Server-Projekt fordert die IDE an, dass Sie Assets hinzufügen, um das Projekt zu erstellen und zu debuggen.</span><span class="sxs-lookup"><span data-stu-id="1162c-219">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="1162c-220">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="1162c-220">Select **Yes**.</span></span>

   <span data-ttu-id="1162c-221">6 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-221">6\.</span></span> <span data-ttu-id="1162c-222">Wenn Sie eine Blazor Server-App verwenden, führen Sie die APP mit dem Visual Studio Code Debugger aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-222">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="1162c-223">Wenn Sie eine Blazor Webassembly-App verwenden, führen Sie `dotnet run` aus dem Projektordner der APP aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-223">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="1162c-224">7 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-224">7\.</span></span> <span data-ttu-id="1162c-225">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1162c-225">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1162c-226">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="1162c-226">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="1162c-227">1 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-227">1\.</span></span> <span data-ttu-id="1162c-228">Installieren Sie [Visual Studio für Mac](https://visualstudio.microsoft.com/vs/mac/).</span><span class="sxs-lookup"><span data-stu-id="1162c-228">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="1162c-229">Wechseln [Sie zum Aktualisierungs Kanal, um die Vorschau anzuzeigen](/visualstudio/mac/install-preview).</span><span class="sxs-lookup"><span data-stu-id="1162c-229">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="1162c-230">2 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-230">2\.</span></span> <span data-ttu-id="1162c-231">Wählen Sie Datei > **neue** Projekt **Mappe** aus, oder erstellen Sie ein **Neues Projekt**.</span><span class="sxs-lookup"><span data-stu-id="1162c-231">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="1162c-232">3 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-232">3\.</span></span> <span data-ttu-id="1162c-233">Wählen Sie in der Rand Leiste die Option **.net Core** - > **App**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-233">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="1162c-234">4 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-234">4\.</span></span> <span data-ttu-id="1162c-235">Wählen Sie die **Blazor Server-App** -Vorlage aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-235">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="1162c-236">Zurzeit ist nur die Blazor Server-Vorlage in Visual Studio für Mac verfügbar.</span><span class="sxs-lookup"><span data-stu-id="1162c-236">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="1162c-237">Befolgen Sie für eine Blazor Webassembly die Anweisungen auf der Registerkarte **.net Core-CLI** . Nachdem Sie die Vorlage Blazor Server ausgewählt haben, wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-237">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="1162c-238">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-238">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="1162c-239">5 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-239">5\.</span></span> <span data-ttu-id="1162c-240">Legen Sie das **Ziel Framework** auf **.net Core 3,0** fest, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-240">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="1162c-241">6 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-241">6\.</span></span> <span data-ttu-id="1162c-242">Benennen Sie die APP im Feld **Projektname** `WebApplication1`.</span><span class="sxs-lookup"><span data-stu-id="1162c-242">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="1162c-243">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-243">Select **Create**.</span></span>

   <span data-ttu-id="1162c-244">7 \.</span><span class="sxs-lookup"><span data-stu-id="1162c-244">7\.</span></span> <span data-ttu-id="1162c-245">Wählen **Sie > ausführen** **ohne Debuggen** ausführen aus, um die APP *ohne den Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1162c-245">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="1162c-246">Führen Sie die APP mit **Debuggen starten** aus, um die APP *mit dem Debugger*auszuführen.</span><span class="sxs-lookup"><span data-stu-id="1162c-246">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1162c-247">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="1162c-247">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="1162c-248">Führen Sie für eine Blazor Webassembly die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="1162c-248">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="1162c-249">Führen Sie die folgenden Befehle in einer Befehlsshell aus, um die Blazor Server-Darstellung auszuführen:</span><span class="sxs-lookup"><span data-stu-id="1162c-249">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="1162c-250">Informationen zu den beiden Blazor Hostingmodellen *Blazor Server* und *Blazor Webassembly*finden Sie unter <xref:blazor/hosting-models>.</span><span class="sxs-lookup"><span data-stu-id="1162c-250">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="1162c-251">Navigieren Sie in einem Browser zu `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="1162c-251">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="1162c-252">Auf den Registerkarten in der Rand Leiste sind mehrere Seiten verfügbar:</span><span class="sxs-lookup"><span data-stu-id="1162c-252">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="1162c-253">Startseite</span><span class="sxs-lookup"><span data-stu-id="1162c-253">Home</span></span>
* <span data-ttu-id="1162c-254">Zähler</span><span class="sxs-lookup"><span data-stu-id="1162c-254">Counter</span></span>
* <span data-ttu-id="1162c-255">Abrufen von Daten</span><span class="sxs-lookup"><span data-stu-id="1162c-255">Fetch data</span></span>

<span data-ttu-id="1162c-256">Wählen Sie auf der Seite „Counter“ die Schaltfläche **Hier klicken** aus, um den Zähler ohne Seitenaktualisierung heraufzusetzen.</span><span class="sxs-lookup"><span data-stu-id="1162c-256">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="1162c-257">Das Inkrementieren eines Zählers in einer Webseite erfordert normalerweise das Schreiben von JavaScript C#, aber mit Blazor können Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="1162c-257">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="1162c-258">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="1162c-258">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="1162c-259">Eine Anforderung für `/counter` im Browser, wie von der `@page`-Direktive angegeben, bewirkt, dass die `Counter` Komponente ihren Inhalt wieder gibt.</span><span class="sxs-lookup"><span data-stu-id="1162c-259">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="1162c-260">Komponenten werden in einer Speicher internen Darstellung der Rendering-Struktur dargestellt, die dann zum flexiblen und effizienten Aktualisieren der Benutzeroberfläche verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="1162c-260">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="1162c-261">Jedes Mal, wenn die Schaltfläche " **Click me** " ausgewählt wird:</span><span class="sxs-lookup"><span data-stu-id="1162c-261">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="1162c-262">Das `onclick` Ereignis wird ausgelöst.</span><span class="sxs-lookup"><span data-stu-id="1162c-262">The `onclick` event is fired.</span></span>
* <span data-ttu-id="1162c-263">Die `IncrementCount` -Methode wird aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="1162c-263">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="1162c-264">Der `currentCount` wird inkrementiert.</span><span class="sxs-lookup"><span data-stu-id="1162c-264">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="1162c-265">Die Komponente wird wieder gerendert.</span><span class="sxs-lookup"><span data-stu-id="1162c-265">The component is rendered again.</span></span>

<span data-ttu-id="1162c-266">Die Laufzeit vergleicht den neuen Inhalt mit dem vorherigen Inhalt und wendet nur den geänderten Inhalt auf die Dokumentobjektmodell (DOM) an.</span><span class="sxs-lookup"><span data-stu-id="1162c-266">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="1162c-267">Fügen Sie einer anderen Komponente mithilfe der HTML-Syntax eine Komponente hinzu.</span><span class="sxs-lookup"><span data-stu-id="1162c-267">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="1162c-268">Fügen Sie z. b. der Startseite der APP die `Counter` Komponente hinzu, indem Sie der `Index` Komponente ein `<Counter />`-Element hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="1162c-268">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="1162c-269">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="1162c-269">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="1162c-270">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-270">Run the app.</span></span> <span data-ttu-id="1162c-271">Die Startseite verfügt über einen eigenen Counter, der von der `Counter` Komponente bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="1162c-271">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="1162c-272">Komponenten Parameter werden mithilfe von Attributen oder untergeordnetem [Inhalt](xref:blazor/components#child-content)angegeben, sodass Sie Eigenschaften für die untergeordnete Komponente festlegen können.</span><span class="sxs-lookup"><span data-stu-id="1162c-272">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="1162c-273">Um der `Counter` Komponente einen Parameter hinzuzufügen, aktualisieren Sie den `@code` Block der Komponente:</span><span class="sxs-lookup"><span data-stu-id="1162c-273">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="1162c-274">Fügen Sie eine öffentliche Eigenschaft für `IncrementAmount` mit einem `[Parameter]` Attribut hinzu.</span><span class="sxs-lookup"><span data-stu-id="1162c-274">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="1162c-275">Ändern Sie die `IncrementCount`-Methode, um `IncrementAmount` beim Heraufsetzen des Werts von `currentCount` zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="1162c-275">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="1162c-276">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="1162c-276">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="1162c-277">Geben Sie die `IncrementAmount` im `<Counter>`-Element der `Index` Komponente mithilfe eines-Attributs an.</span><span class="sxs-lookup"><span data-stu-id="1162c-277">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="1162c-278">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="1162c-278">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="1162c-279">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="1162c-279">Run the app.</span></span> <span data-ttu-id="1162c-280">Die `Index` Komponente verfügt über einen eigenen Wert, der jedes Mal um zehn erhöht wird, wenn die Schaltfläche " **Click me** " ausgewählt ist.</span><span class="sxs-lookup"><span data-stu-id="1162c-280">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="1162c-281">Die `Counter` Komponente (*counter. Razor*) bei `/counter` wird weiterhin um 1 erhöht.</span><span class="sxs-lookup"><span data-stu-id="1162c-281">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1162c-282">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="1162c-282">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="1162c-283">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1162c-283">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
