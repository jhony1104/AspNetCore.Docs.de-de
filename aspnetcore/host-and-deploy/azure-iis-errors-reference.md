---
title: Referenz zu häufigen Fehlern bei Azure App Service und IIS mit ASP.NET Core
author: guardrex
description: Hier erfahren Sie, wie Sie häufige Fehler beim Hosten von ASP.NET Core-Apps in Azure Apps Service und IIS beheben können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/12/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 0191460f8c3dab98e6f977a29eacf0396b6789d8
ms.sourcegitcommit: b4ef2b00f3e1eb287138f8b43c811cb35a100d3e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65970072"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="7acbe-103">Referenz zu häufigen Fehlern bei Azure App Service und IIS mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7acbe-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="7acbe-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="7acbe-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="7acbe-105">In diesem Artikel erfahren Sie, wie Sie häufige Fehler beim Hosten von ASP.NET Core-Apps in Azure Apps Service und IIS beheben können.</span><span class="sxs-lookup"><span data-stu-id="7acbe-105">This topic offers troubleshooting advice for common errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="7acbe-106">Sammeln Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="7acbe-106">Collect the following information:</span></span>

* <span data-ttu-id="7acbe-107">Browserverhalten (Statuscode und Fehlermeldung)</span><span class="sxs-lookup"><span data-stu-id="7acbe-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="7acbe-108">Einträge im Anwendungsereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="7acbe-108">Application Event Log entries</span></span>
  * <span data-ttu-id="7acbe-109">Azure App Service: <xref:host-and-deploy/azure-apps/troubleshoot></span><span class="sxs-lookup"><span data-stu-id="7acbe-109">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="7acbe-110">IIS</span><span class="sxs-lookup"><span data-stu-id="7acbe-110">IIS</span></span>
    1. <span data-ttu-id="7acbe-111">Klicken Sie im **Windows**-Menü auf **Start**, geben Sie *Ereignisanzeige* ein, und drücken Sie die **EINGABETASTE**.</span><span class="sxs-lookup"><span data-stu-id="7acbe-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="7acbe-112">Erweitern Sie in der **Ereignisanzeige** die Option **Windows-Protokolle** > **Anwendung** auf der Randleiste.</span><span class="sxs-lookup"><span data-stu-id="7acbe-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="7acbe-113">stdout- und Debugprotokolleinträge im ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="7acbe-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="7acbe-114">Azure App Service: <xref:host-and-deploy/azure-apps/troubleshoot></span><span class="sxs-lookup"><span data-stu-id="7acbe-114">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="7acbe-115">IIS: Befolgen Sie die Anweisungen in den Abschnitten [Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) und [Erweiterte Diagnoseprotokolle](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) des Artikels zum ASP.NET Core-Modul.</span><span class="sxs-lookup"><span data-stu-id="7acbe-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="7acbe-116">Vergleichen Sie die Fehlerinformationen mit folgenden häufigen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="7acbe-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="7acbe-117">Befolgen Sie die Hinweise zur Fehlerbehebung, wenn eine Übereinstimmung gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="7acbe-118">Die Fehlerliste in diesem Artikel ist nicht vollständig.</span><span class="sxs-lookup"><span data-stu-id="7acbe-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="7acbe-119">Wenn bei Ihnen ein Fehler auftritt, der hier nicht aufgeführt wird, öffnen Sie über die Schaltfläche **Feedback zum Inhalt** am Ende des Artikels ein neues Issue, in dem Sie den Fehler ausführlich beschreiben sollten, damit er reproduziert werden kann.</span><span class="sxs-lookup"><span data-stu-id="7acbe-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="7acbe-120">Installationsprogramm konnte VC++ Redistributable nicht abrufen</span><span class="sxs-lookup"><span data-stu-id="7acbe-120">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="7acbe-121">**Ausnahme des Installationsprogramms:** „0x80072efd“ **oder** „0x80072f76: Unbekannter Fehler“</span><span class="sxs-lookup"><span data-stu-id="7acbe-121">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="7acbe-122">**Protokollausnahme des Installationsprogramms&#8224;:** „Error 0x80072efd“ **oder** „0x80072f76: Fehler beim Ausführen des EXE-Pakets</span><span class="sxs-lookup"><span data-stu-id="7acbe-122">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="7acbe-123">&#8224;Das Protokoll befindet sich unter *C:\Benutzer\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span><span class="sxs-lookup"><span data-stu-id="7acbe-123">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="7acbe-124">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-124">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-125">Wenn das System während der [Installation des .NET Core-Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) keinen Zugriff auf das Internet hat, tritt diese Ausnahme auf, wenn der Installer *Microsoft Visual C++ 2015 Redistributable* nicht abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="7acbe-125">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="7acbe-126">Rufen Sie einen Installer aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840) ab.</span><span class="sxs-lookup"><span data-stu-id="7acbe-126">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="7acbe-127">Wenn der Installer fehlschlägt, erhält der Server möglicherweise nicht die .NET Core-Runtime, die zum Hosten einer [Framework-abhängigen Bereitstellung (Framework-Dependent Deployment, FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd) erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-127">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="7acbe-128">Wenn Sie eine FDD hosten, vergewissern Sie sich, dass die Runtime unter **Programme und Features** oder unter **Apps und Features** installiert ist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-128">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="7acbe-129">Wenn eine bestimmte Runtime erforderlich ist, laden Sie diese von der Seite [.NET Download Archives (.NET-Downloadarchive)](https://dotnet.microsoft.com/download/archives) herunter, und installieren Sie sie auf dem System.</span><span class="sxs-lookup"><span data-stu-id="7acbe-129">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="7acbe-130">Starten Sie nach dem Installieren der Runtime das System neu, oder starten Sie IIS neu, indem Sie **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-130">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="7acbe-131">Durch ein Upgrade des Betriebssystems wird das ASP.NET Core-Modul (32-Bit) entfernt</span><span class="sxs-lookup"><span data-stu-id="7acbe-131">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="7acbe-132">**Anwendungsprotokoll:** Fehler beim Laden der Modul-DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**.</span><span class="sxs-lookup"><span data-stu-id="7acbe-132">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="7acbe-133">Die Daten sind der Fehler.</span><span class="sxs-lookup"><span data-stu-id="7acbe-133">The data is the error.</span></span>

<span data-ttu-id="7acbe-134">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-134">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-135">Nicht zum Betriebssystem gehörende Dateien im Verzeichnis **C:\Windows\SysWOW64\inetsrv** werden während eines Betriebssystemupgrades nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="7acbe-135">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="7acbe-136">Dieses Problem tritt auf, wenn vor der Durchführung eines Betriebssystemupgrades das ASP.NET Core-Modul installiert wurde und nach einem Betriebssystemupgrade ein App-Pool im 32-Bit-Modus ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-136">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="7acbe-137">Reparieren Sie nach einem Betriebssystemupgrade das ASP.NET Core-Modul.</span><span class="sxs-lookup"><span data-stu-id="7acbe-137">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="7acbe-138">Siehe [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="7acbe-138">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="7acbe-139">Wählen Sie **Reparatur** aus, wenn der Installer ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-139">Select **Repair** when the installer is run.</span></span>

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a><span data-ttu-id="7acbe-140">Websiteerweiterung fehlt, 32-Bit- und 64-Bit-Websiteerweiterungen (x86 und x64) installiert oder falsche Prozessbitanzahl festgelegt</span><span class="sxs-lookup"><span data-stu-id="7acbe-140">Missing site extension, 32-bit (x86) and 64-bit (x64) site extensions installed, or wrong process bitness set</span></span>

<span data-ttu-id="7acbe-141">*Gilt für Apps, die von Azure App Services gehostet werden.*</span><span class="sxs-lookup"><span data-stu-id="7acbe-141">*Applies to apps hosted by Azure App Services.*</span></span>

* <span data-ttu-id="7acbe-142">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="7acbe-142">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="7acbe-143">**Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="7acbe-143">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="7acbe-144">Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="7acbe-144">Could not find inprocess request handler.</span></span> <span data-ttu-id="7acbe-145">Captured output from invoking hostfxr: Es konnte keine kompatible Frameworkversion gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-145">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="7acbe-146">Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-146">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span> <span data-ttu-id="7acbe-147">Die Anwendung „/LM/W3SVC/1416782824/ROOT“ konnte nicht gestartet werden. Fehlercode: „0x8000ffff“.</span><span class="sxs-lookup"><span data-stu-id="7acbe-147">Failed to start application '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="7acbe-148">**stdout-Protokoll des ASP.NET Core-Moduls:** Es konnte keine kompatible Frameworkversion gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-148">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="7acbe-149">Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-149">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-150">**Debugprotokoll des ASP.NET Core-Moduls:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="7acbe-150">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="7acbe-151">Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-151">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="7acbe-152">Failed HRESULT returned: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="7acbe-152">Failed HRESULT returned: 0x8000ffff.</span></span> <span data-ttu-id="7acbe-153">Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="7acbe-153">Could not find inprocess request handler.</span></span> <span data-ttu-id="7acbe-154">Es konnte keine kompatible Frameworkversion gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-154">It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="7acbe-155">Das angegebene Framework „Microsoft.AspNetCore.App“, Version „{VERSION}-preview-\*“ konnte nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-155">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}-preview-\*' was not found.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-156">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-156">Troubleshooting:</span></span>

* <span data-ttu-id="7acbe-157">Wenn die App in einer Vorschauruntime ausgeführt wird, installieren Sie entweder die 32-Bit- **oder** 64-Bit-Websiteerweiterung (x86 oder x64), die der Bitanzahl und der Runtimeversion der App entspricht.</span><span class="sxs-lookup"><span data-stu-id="7acbe-157">If running the app on a preview runtime, install either the 32-bit (x86) **or** 64-bit (x64) site extension that matches the bitness of the app and the app's runtime version.</span></span> <span data-ttu-id="7acbe-158">**Installieren Sie nicht sowohl Erweiterungen als auch mehrere Runtimeversionen der Erweiterung.**</span><span class="sxs-lookup"><span data-stu-id="7acbe-158">**Don't install both extensions or multiple runtime versions of the extension.**</span></span>

  * <span data-ttu-id="7acbe-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span><span class="sxs-lookup"><span data-stu-id="7acbe-159">ASP.NET Core {RUNTIME VERSION} (x86) Runtime</span></span>
  * <span data-ttu-id="7acbe-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span><span class="sxs-lookup"><span data-stu-id="7acbe-160">ASP.NET Core {RUNTIME VERSION} (x64) Runtime</span></span>

  <span data-ttu-id="7acbe-161">Starten Sie die App neu.</span><span class="sxs-lookup"><span data-stu-id="7acbe-161">Restart the app.</span></span> <span data-ttu-id="7acbe-162">Warten Sie einige Sekunden, bis die App neu startet.</span><span class="sxs-lookup"><span data-stu-id="7acbe-162">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="7acbe-163">Wenn Sie die App auf einer Vorschauruntime ausführen und sowohl die 32-Bit- als auch die 64-Bit-[Websiteerweiterung](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) (x86 und x64) installiert sind, deinstallieren Sie die Websiteerweiterung, die nicht der Bitanzahl der App entspricht.</span><span class="sxs-lookup"><span data-stu-id="7acbe-163">If running the app on a preview runtime and both the 32-bit (x86) and 64-bit (x64) [site extensions](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) are installed, uninstall the site extension that doesn't match the bitness of the app.</span></span> <span data-ttu-id="7acbe-164">Starten Sie die App nach dem Entfernen der Websiteerweiterung neu.</span><span class="sxs-lookup"><span data-stu-id="7acbe-164">After removing the site extension, restart the app.</span></span> <span data-ttu-id="7acbe-165">Warten Sie einige Sekunden, bis die App neu startet.</span><span class="sxs-lookup"><span data-stu-id="7acbe-165">Wait several seconds for the app to restart.</span></span>

* <span data-ttu-id="7acbe-166">Wenn Sie die App auf einer Vorschauruntime ausführen und die Bitanzahl der Websiteerweiterung mit der Bitanzahl der App übereinstimmt, bestätigen Sie, dass die *Runtimeversion* der Websiteerweiterung der Vorschau der Runtimeversion der App entspricht.</span><span class="sxs-lookup"><span data-stu-id="7acbe-166">If running the app on a preview runtime and the site extension's bitness matches that of the app, confirm that the preview site extension's *runtime version* matches the app's runtime version.</span></span>

* <span data-ttu-id="7acbe-167">Überprüfen Sie, ob die **Plattform** der App in **Anwendungseinstellungen** der Bitanzahl der App entspricht.</span><span class="sxs-lookup"><span data-stu-id="7acbe-167">Confirm that the app's **Platform** in **Application Settings** matches the bitness of the app.</span></span>

<span data-ttu-id="7acbe-168">Weitere Informationen finden Sie unter <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span><span class="sxs-lookup"><span data-stu-id="7acbe-168">For more information, see <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="7acbe-169">Bereitstellung einer x86-App, obwohl der App-Pool nicht für 32-Bit-Apps aktiviert ist</span><span class="sxs-lookup"><span data-stu-id="7acbe-169">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="7acbe-170">**Browser:** HTTP-Fehler 500.30: In-Process-Startfehler beim ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="7acbe-170">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="7acbe-171">**Anwendungsprotokoll:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span><span class="sxs-lookup"><span data-stu-id="7acbe-171">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="7acbe-172">Überprüfen Sie die stderr-Protokolle, um weitere Informationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="7acbe-172">Please check the stderr logs for more information.</span></span> <span data-ttu-id="7acbe-173">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span><span class="sxs-lookup"><span data-stu-id="7acbe-173">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="7acbe-174">Der CLR-Arbeitsthread wurde vorzeitig beendet</span><span class="sxs-lookup"><span data-stu-id="7acbe-174">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="7acbe-175">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.</span><span class="sxs-lookup"><span data-stu-id="7acbe-175">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-176">**Debugprotokoll des ASP.NET Core-Moduls:** Failed HRESULT returned: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="7acbe-176">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="7acbe-177">Dieses Szenario wird vom SDK ausgelöst, wenn eine eigenständige App veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-177">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="7acbe-178">Das SDK generiert einen Fehler, wenn die relative ID (RID) nicht mit der Zielplattform übereinstimmt (z. B. bei der RID `win10-x64` für eine Projektdatei mit `<PlatformTarget>x86</PlatformTarget>`).</span><span class="sxs-lookup"><span data-stu-id="7acbe-178">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="7acbe-179">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-179">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-180">Aktivieren Sie den IIS-App-Pool für 32-Bit-Apps, wenn Sie von x86-Frameworks abhängige Bereitstellungen (`<PlatformTarget>x86</PlatformTarget>`) durchführen möchten.</span><span class="sxs-lookup"><span data-stu-id="7acbe-180">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="7acbe-181">Öffnen Sie im IIS-Manager die Option **Erweiterte Einstellungen** des App-Pools, und legen Sie **32-Bit-Anwendungen aktivieren** auf **Wahr** fest.</span><span class="sxs-lookup"><span data-stu-id="7acbe-181">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="7acbe-182">Plattformkonflikte mit RID</span><span class="sxs-lookup"><span data-stu-id="7acbe-182">Platform conflicts with RID</span></span>

* <span data-ttu-id="7acbe-183">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="7acbe-183">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="7acbe-184">**Anwendungsprotokoll:** Die Anwendung „MACHINE/WEBROOT/APPHOST/{ASSEMBLY}“ mit dem physischen Stamm „C:\{PATH}\' konnte den Prozess mit der Befehlszeile „C:\{PATH}{assembly}.{exe|dll}“ nicht starten. Fehlercode = 0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="7acbe-184">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="7acbe-185">**stdout-Protokoll des ASP.NET Core-Moduls:** Ausnahmefehler: System.BadImageFormatException: Die Datei oder Assembly „{ASSEMBLY}.dll“ wurde nicht gefunden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-185">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="7acbe-186">Es wurde versucht, ein Programm mit einem falschen Format zu laden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-186">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="7acbe-187">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-187">Troubleshooting:</span></span>

* <span data-ttu-id="7acbe-188">Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-188">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="7acbe-189">Ein Prozessfehler könnte die Folge eines Problems in der App sein.</span><span class="sxs-lookup"><span data-stu-id="7acbe-189">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="7acbe-190">Weitere Informationen finden Sie unter [Problembehandlung bei ASP.NET Core in IIS](xref:host-and-deploy/iis/troubleshoot) oder [Problembehandlung bei ASP.NET Core in Azure App Service](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="7acbe-190">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="7acbe-191">Wenn diese Ausnahme für eine Azure-App-Bereitstellung beim Aktualisieren einer App und beim Bereitstellen neuerer Assemblys auftritt, löschen Sie alle Dateien aus der vorherigen Bereitstellung manuell.</span><span class="sxs-lookup"><span data-stu-id="7acbe-191">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="7acbe-192">Veraltete inkompatible Assemblys können zu einer `System.BadImageFormatException`-Ausnahme führen, wenn Sie eine aktualisierte App bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-192">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="7acbe-193">URI-Endpunkt falsch oder beendete Website</span><span class="sxs-lookup"><span data-stu-id="7acbe-193">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="7acbe-194">**Browser:** „ERR_CONNECTION_REFUSED“ **oder** „Es kann keine Verbindung hergestellt werden.“</span><span class="sxs-lookup"><span data-stu-id="7acbe-194">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="7acbe-195">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="7acbe-195">**Application Log:** No entry</span></span>

* <span data-ttu-id="7acbe-196">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-196">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-197">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-197">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-198">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-198">Troubleshooting:</span></span>

* <span data-ttu-id="7acbe-199">Vergewissern Sie sich, dass der richtige URI-Endpunkt für die App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-199">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="7acbe-200">Überprüfen Sie die Bindungen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-200">Check the bindings.</span></span>

* <span data-ttu-id="7acbe-201">Vergewissern Sie sich, dass die IIS-Website nicht den Status *Beendet* aufweist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-201">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="7acbe-202">CoreWebEngine oder W3SVC-Serverfeatures deaktiviert</span><span class="sxs-lookup"><span data-stu-id="7acbe-202">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="7acbe-203">**Betriebssystemausnahme:** Die Features „CoreWebEngine“ und „W3SVC“ von IIS 7.0 müssen installiert sein, um das ASP.NET Core-Modul zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-203">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="7acbe-204">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-204">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-205">Vergewissern Sie sich, dass die richtigen Rollen und Features aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-205">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="7acbe-206">Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="7acbe-206">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="7acbe-207">Falscher physischer Pfad der Website oder App fehlt</span><span class="sxs-lookup"><span data-stu-id="7acbe-207">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="7acbe-208">**Browser:** „403 – Verboten: Zugriff verweigert.“ **oder** „403.14 Verboten: Der Webserver wurde dafür konfiguriert, die Inhalte dieses Verzeichnisses nicht aufzulisten.</span><span class="sxs-lookup"><span data-stu-id="7acbe-208">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="7acbe-209">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="7acbe-209">**Application Log:** No entry</span></span>

* <span data-ttu-id="7acbe-210">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-210">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-211">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-211">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-212">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-212">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-213">Überprüfen Sie die **Grundeinstellungen** der IIS-Website und den physischen App-Ordner.</span><span class="sxs-lookup"><span data-stu-id="7acbe-213">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="7acbe-214">Vergewissern Sie sich, dass sich die App im Ordner im **physischen Pfad** der IIS-Website befindet.</span><span class="sxs-lookup"><span data-stu-id="7acbe-214">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="7acbe-215">„Falsche Rolle“, „ASP.NET Core-Modul nicht installiert“ oder „Falsche Berechtigungen“</span><span class="sxs-lookup"><span data-stu-id="7acbe-215">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="7acbe-216">**Browser:** 500.19 – Interner Serverfehler: Auf die angeforderte Seite kann nicht zugegriffen werden, da die zugehörigen Konfigurationsdaten für die Seite ungültig sind.</span><span class="sxs-lookup"><span data-stu-id="7acbe-216">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="7acbe-217">**oder** „Diese Seite kann nicht angezeigt werden.“</span><span class="sxs-lookup"><span data-stu-id="7acbe-217">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="7acbe-218">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="7acbe-218">**Application Log:** No entry</span></span>

* <span data-ttu-id="7acbe-219">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-219">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-220">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-220">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-221">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-221">Troubleshooting:</span></span>

* <span data-ttu-id="7acbe-222">Vergewissern Sie sich, dass die richtige Rolle aktiviert wurde.</span><span class="sxs-lookup"><span data-stu-id="7acbe-222">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="7acbe-223">Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="7acbe-223">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="7acbe-224">Öffnen Sie **Programme und Features** oder **Apps und Features**, und vergewissern Sie sich, dass **Windows Server Hosting** installiert ist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-224">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="7acbe-225">Wenn **Windows Server Hosting** nicht in der Liste der installierten Programme vorhanden ist, laden Sie das .NET Core Hosting-Paket herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="7acbe-225">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="7acbe-226">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="7acbe-226">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="7acbe-227">Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="7acbe-227">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="7acbe-228">Stellen Sie sicher, dass **Anwendungspool** > **Prozessmodell** > **Identität** auf **ApplicationPoolIdentity** festgelegt ist, oder dass die benutzerdefinierte Identität über die erforderlichen Berechtigungen für den Zugriff auf den Bereitstellungsordner der App verfügt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-228">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

* <span data-ttu-id="7acbe-229">Wenn Sie das ASP.NET Core-Hostingpaket deinstallieren und eine frühere Version des Hostingpakets installieren, ist in der *applicationHost.config*-Datei kein Abschnitt für das ASP.NET Core-Modul enthalten.</span><span class="sxs-lookup"><span data-stu-id="7acbe-229">If you uninstalled the ASP.NET Core Hosting Bundle and installed an earlier version of the hosting bundle, the *applicationHost.config* file doesn't include a section for the ASP.NET Core Module.</span></span> <span data-ttu-id="7acbe-230">Öffnen Sie die *applicationHost.config*-Datei unter *%windir%/System32/inetsrv/config*, und suchen Sie nach der Abschnittsgruppe `<configuration><configSections><sectionGroup name="system.webServer">`.</span><span class="sxs-lookup"><span data-stu-id="7acbe-230">Open *applicationHost.config* at *%windir%/System32/inetsrv/config* and find the `<configuration><configSections><sectionGroup name="system.webServer">` section group.</span></span> <span data-ttu-id="7acbe-231">Wenn der Abschnitt für das ASP.NET Core-Modul in der Abschnittsgruppe nicht vorhanden ist, fügen Sie dieses Abschnittselement hinzu:</span><span class="sxs-lookup"><span data-stu-id="7acbe-231">If the section for the ASP.NET Core Module is missing from the section group, add the section element:</span></span>

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  <span data-ttu-id="7acbe-232">Installieren Sie alternativ die neueste Version des ASP.NET Core-Hostingbundles.</span><span class="sxs-lookup"><span data-stu-id="7acbe-232">Alternatively, install the latest version of the ASP.NET Core Hosting Bundle.</span></span> <span data-ttu-id="7acbe-233">Die neueste Version ist mit den unterstützten ASP.NET Core-Apps abwärtskompatibel.</span><span class="sxs-lookup"><span data-stu-id="7acbe-233">The latest version is backwards-compatible with supported ASP.NET Core apps.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="7acbe-234">Falscher processPath-Wert, fehlende PATH-Variable, Hostingpaket nicht installiert, System/IIS wird nicht neu gestartet, VC++ Redistributable nicht installiert oder dotnet.exe-Zugriffsverletzung</span><span class="sxs-lookup"><span data-stu-id="7acbe-234">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-235">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="7acbe-235">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="7acbe-236">**Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span><span class="sxs-lookup"><span data-stu-id="7acbe-236">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="7acbe-237">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="7acbe-237">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="7acbe-238">Application '{PATH}' wasn't able to start.</span><span class="sxs-lookup"><span data-stu-id="7acbe-238">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="7acbe-239">Es wurde keine ausführbare Datei unter „{PATH}“ gefunden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-239">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="7acbe-240">Die Anwendung „/LM/W3SVC/2/ROOT“ konnte nicht gestartet werden. Fehlercode: 0x8007023e.</span><span class="sxs-lookup"><span data-stu-id="7acbe-240">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="7acbe-241">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-241">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="7acbe-242">**Debugprotokoll des ASP.NET Core-Moduls:** Ereignisprotokoll: 'Application '{PATH}' wasn't able to start.</span><span class="sxs-lookup"><span data-stu-id="7acbe-242">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="7acbe-243">Es wurde keine ausführbare Datei unter „{PATH}“ gefunden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-243">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="7acbe-244">Failed HRESULT returned: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="7acbe-244">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="7acbe-245">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="7acbe-245">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="7acbe-246">**Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span><span class="sxs-lookup"><span data-stu-id="7acbe-246">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="7acbe-247">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="7acbe-247">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="7acbe-248">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.</span><span class="sxs-lookup"><span data-stu-id="7acbe-248">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-249">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-249">Troubleshooting:</span></span>

* <span data-ttu-id="7acbe-250">Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-250">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="7acbe-251">Ein Prozessfehler könnte die Folge eines Problems in der App sein.</span><span class="sxs-lookup"><span data-stu-id="7acbe-251">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="7acbe-252">Weitere Informationen finden Sie unter [Problembehandlung bei ASP.NET Core in IIS](xref:host-and-deploy/iis/troubleshoot) oder [Problembehandlung bei ASP.NET Core in Azure App Service](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="7acbe-252">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="7acbe-253">Überprüfen Sie das Attribut *processPath* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass es den Wert `dotnet` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) oder `.\{ASSEMBLY}.exe` für eine [eigenständige Bereitstellung (Self-Contained Deployment, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) enthält.</span><span class="sxs-lookup"><span data-stu-id="7acbe-253">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="7acbe-254">Für eine Framework-abhängige Bereitstellung ist *dotnet.exe* möglicherweise über die PATH-Einstellungen nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7acbe-254">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="7acbe-255">Überprüfen Sie, ob *C:\Programme\dotnet\\* in den PATH-Einstellungen des Systems vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-255">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="7acbe-256">Für eine frameworkabhängige Bereitstellung ist *dotnet.exe* möglicherweise für die Benutzeridentität des App-Pools nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="7acbe-256">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="7acbe-257">Vergewissern Sie sich, dass die Benutzeridentität des App-Pools Zugriff auf das Verzeichnis *C:\Programme\dotnet* hat.</span><span class="sxs-lookup"><span data-stu-id="7acbe-257">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="7acbe-258">Vergewissern Sie sich, dass keine Ablehnungsregeln für die Benutzeridentität des App-Pools im Verzeichnis *C:\Programme\dotnet* und in den App-Verzeichnissen konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="7acbe-258">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="7acbe-259">Möglicherweise wurde eine FDD bereitgestellt und .NET Core installiert, ohne IIS neu zu starten.</span><span class="sxs-lookup"><span data-stu-id="7acbe-259">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="7acbe-260">Starten Sie den Server neu, oder starten Sie IIS neu, indem Sie **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-260">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="7acbe-261">Möglicherweise wurde eine FDD bereitgestellt, ohne die .NET Core-Runtime auf dem Hostsystem zu installieren.</span><span class="sxs-lookup"><span data-stu-id="7acbe-261">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="7acbe-262">Führen Sie den **Installer für das .NET Core-Hostingpaket** auf dem System aus, wenn die .NET Core-Runtime nicht installiert wurde.</span><span class="sxs-lookup"><span data-stu-id="7acbe-262">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="7acbe-263">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="7acbe-263">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="7acbe-264">Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="7acbe-264">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="7acbe-265">Wenn eine bestimmte Runtime erforderlich ist, laden Sie diese von der Seite [.NET Download Archives (.NET-Downloadarchive)](https://dotnet.microsoft.com/download/archives) herunter, und installieren Sie sie auf dem System.</span><span class="sxs-lookup"><span data-stu-id="7acbe-265">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="7acbe-266">Schließen Sie die Installation ab, indem Sie das System oder IIS neu starten. Führen Sie dazu **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="7acbe-266">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="7acbe-267">Möglicherweise wurde eine FDD bereitgestellt, und *Microsoft Visual C++ 2015 Redistributable (x64)* ist auf dem System nicht installiert.</span><span class="sxs-lookup"><span data-stu-id="7acbe-267">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="7acbe-268">Rufen Sie einen Installer aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840) ab.</span><span class="sxs-lookup"><span data-stu-id="7acbe-268">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="7acbe-269">Falsche Argumente des Elements \<aspNetCore></span><span class="sxs-lookup"><span data-stu-id="7acbe-269">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-270">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="7acbe-270">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="7acbe-271">**Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="7acbe-271">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="7acbe-272">Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-272">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="7acbe-273">Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="7acbe-273">Could not find inprocess request handler.</span></span> <span data-ttu-id="7acbe-274">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span><span class="sxs-lookup"><span data-stu-id="7acbe-274">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="7acbe-275">Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Fehler beim Starten der Anwendung "/ LM/W3SVC/3/ROOT", ErrorCode "0x8000ffff".</span><span class="sxs-lookup"><span data-stu-id="7acbe-275">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="7acbe-276">**stdout-Protokoll des ASP.NET Core-Moduls:** Did you mean to run dotnet SDK commands?</span><span class="sxs-lookup"><span data-stu-id="7acbe-276">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="7acbe-277">Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="7acbe-277">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="7acbe-278">**Debugprotokoll des ASP.NET Core-Moduls:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="7acbe-278">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="7acbe-279">Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-279">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="7acbe-280">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="7acbe-280">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="7acbe-281">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span><span class="sxs-lookup"><span data-stu-id="7acbe-281">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="7acbe-282">Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT-Fehler zurückgegeben: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="7acbe-282">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="7acbe-283">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="7acbe-283">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="7acbe-284">**Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span><span class="sxs-lookup"><span data-stu-id="7acbe-284">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="7acbe-285">**stdout-Protokoll des ASP.NET Core-Moduls:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="7acbe-285">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="7acbe-286">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-286">Troubleshooting:</span></span>

* <span data-ttu-id="7acbe-287">Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="7acbe-287">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="7acbe-288">Ein Prozessfehler könnte die Folge eines Problems in der App sein.</span><span class="sxs-lookup"><span data-stu-id="7acbe-288">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="7acbe-289">Weitere Informationen finden Sie unter [Problembehandlung bei ASP.NET Core in IIS](xref:host-and-deploy/iis/troubleshoot) oder [Problembehandlung bei ASP.NET Core in Azure App Service](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="7acbe-289">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="7acbe-290">Überprüfen Sie das Attribut *arguments* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass der Wert entweder `.\{ASSEMBLY}.dll` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) entspricht oder nicht vorhanden, eine leere Zeichenfolge (`arguments=""`) oder eine Liste von Argumenten der App (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) für eine eigenständige Bereitstellung (Self-Contained Deployment, SCD) ist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-290">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="7acbe-291">Fehlendes freigegebenes .NET Core-Framework</span><span class="sxs-lookup"><span data-stu-id="7acbe-291">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="7acbe-292">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="7acbe-292">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="7acbe-293">**Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="7acbe-293">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="7acbe-294">Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen.</span><span class="sxs-lookup"><span data-stu-id="7acbe-294">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="7acbe-295">Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="7acbe-295">Could not find inprocess request handler.</span></span> <span data-ttu-id="7acbe-296">Captured output from invoking hostfxr: Es konnte keine kompatible Frameworkversion gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-296">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="7acbe-297">Das angegebene Framework „Microsoft.AspNetCore.App“ (Version {VERSION}) konnte nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-297">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="7acbe-298">Die Anwendung „/LM/W3SVC/5/ROOT“ konnte nicht gestartet werden. Fehlercode: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="7acbe-298">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="7acbe-299">**stdout-Protokoll des ASP.NET Core-Moduls:** Es konnte keine kompatible Frameworkversion gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-299">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="7acbe-300">Das angegebene Framework „Microsoft.AspNetCore.App“ (Version {VERSION}) konnte nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-300">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="7acbe-301">**Debugprotokoll des ASP.NET Core-Moduls:** Failed HRESULT returned: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="7acbe-301">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="7acbe-302">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-302">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-303">Stellen Sie für eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) sicher, dass die richtige Runtime im System installiert ist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-303">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="7acbe-304">Beendeter Anwendungspool</span><span class="sxs-lookup"><span data-stu-id="7acbe-304">Stopped Application Pool</span></span>

* <span data-ttu-id="7acbe-305">**Browser:** 503 – Dienst nicht verfügbar</span><span class="sxs-lookup"><span data-stu-id="7acbe-305">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="7acbe-306">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="7acbe-306">**Application Log:** No entry</span></span>

* <span data-ttu-id="7acbe-307">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-307">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-308">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-308">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-309">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-309">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-310">Vergewissern Sie sich, dass der Anwendungspool nicht den Status *Beendet* aufweist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-310">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="7acbe-311">Untergeordnete Anwendung mit \<handlers>-Abschnitt</span><span class="sxs-lookup"><span data-stu-id="7acbe-311">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="7acbe-312">**Browser:** HTTP-Fehler 500.19: Interner Serverfehler</span><span class="sxs-lookup"><span data-stu-id="7acbe-312">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="7acbe-313">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="7acbe-313">**Application Log:** No entry</span></span>

* <span data-ttu-id="7acbe-314">**stdout-Protokoll des ASP.NET Core-Moduls:** The root app's log file is created and shows normal operation.</span><span class="sxs-lookup"><span data-stu-id="7acbe-314">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="7acbe-315">Die Protokolldatei der untergeordneten App wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-315">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-316">**Debugprotokoll des ASP.NET Core-Moduls:** The root app's log file is created and shows normal operation.</span><span class="sxs-lookup"><span data-stu-id="7acbe-316">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="7acbe-317">Die Protokolldatei der untergeordneten App wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-317">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-318">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-318">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="7acbe-319">Überprüfen Sie, ob der Abschnitt `<handlers>` in der *web.config*-Datei der untergeordneten App fehlt oder ob die untergeordnete App die Handler der übergeordneten App nicht erbt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-319">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="7acbe-320">Der Abschnitt `<system.webServer>` in der *web.config*-Datei der übergeordneten App befindet sich in einem `<location>`-Element.</span><span class="sxs-lookup"><span data-stu-id="7acbe-320">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="7acbe-321">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der übergeordneten App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="7acbe-321">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="7acbe-322">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="7acbe-322">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="7acbe-323">Überprüfen Sie, ob die Datei *web.config* der untergeordneten App einen `<handlers>`-Abschnitt enthält.</span><span class="sxs-lookup"><span data-stu-id="7acbe-323">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="7acbe-324">Der stdout-Protokollpfad ist falsch</span><span class="sxs-lookup"><span data-stu-id="7acbe-324">stdout log path incorrect</span></span>

* <span data-ttu-id="7acbe-325">**Browser:** Die App reagiert normal.</span><span class="sxs-lookup"><span data-stu-id="7acbe-325">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-326">**Anwendungsprotokoll:** Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-326">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="7acbe-327">Ausnahmemeldung: „HRESULT: 0x80070005“ wurde für {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7acbe-327">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="7acbe-328">Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht beendet werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-328">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="7acbe-329">Ausnahmemeldung: HRESULT 0x80070002 returned at {PATH}.</span><span class="sxs-lookup"><span data-stu-id="7acbe-329">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="7acbe-330">Das stdout-Verzeichnis in {PATH}\aspnetcorev2_inprocess.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-330">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="7acbe-331">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-331">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="7acbe-332">**Debugprotokoll des ASP.NET Core-Moduls:** Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-332">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="7acbe-333">Ausnahmemeldung: „HRESULT: 0x80070005“ wurde für {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="7acbe-333">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="7acbe-334">Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht beendet werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-334">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="7acbe-335">Ausnahmemeldung: HRESULT 0x80070002 returned at {PATH}.</span><span class="sxs-lookup"><span data-stu-id="7acbe-335">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="7acbe-336">Das stdout-Verzeichnis in {PATH}\aspnetcorev2_inprocess.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-336">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="7acbe-337">**Anwendungsprotokoll:** Warnung: Die stdout-Protokolldatei „ \\?\{ PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log“ konnte nicht erstellt werden. Fehlercode: -2147024893.)</span><span class="sxs-lookup"><span data-stu-id="7acbe-337">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="7acbe-338">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-338">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-339">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-339">Troubleshooting:</span></span>

* <span data-ttu-id="7acbe-340">Der im Element `<aspNetCore>` angegebene Pfad `stdoutLogFile` in der Datei *web.config* ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="7acbe-340">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="7acbe-341">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul: Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="7acbe-341">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="7acbe-342">Der Benutzer des App-Pools hat keinen Schreibzugriff für den Pfad des stdout-Protokolls.</span><span class="sxs-lookup"><span data-stu-id="7acbe-342">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="7acbe-343">Allgemeines Problem mit der Anwendungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="7acbe-343">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="7acbe-344">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)  **oder**  HTTP-Fehler 500.30: In-Process-Startfehler beim ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="7acbe-344">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="7acbe-345">**Anwendungsprotokoll:** Variable</span><span class="sxs-lookup"><span data-stu-id="7acbe-345">**Application Log:** Variable</span></span>

* <span data-ttu-id="7acbe-346">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer oder wurde mit gewöhnlichen Einträgen erstellt, bis die App fehlgeschlagen ist.</span><span class="sxs-lookup"><span data-stu-id="7acbe-346">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="7acbe-347">**Debugprotokoll des ASP.NET Core-Moduls:** Variable</span><span class="sxs-lookup"><span data-stu-id="7acbe-347">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="7acbe-348">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="7acbe-348">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="7acbe-349">**Anwendungsprotokoll:** Die Anwendung „MACHINE/WEBROOT/APPHOST/{ASSEMBLY}“ mit dem physischen Stamm ‚C:\{PATH}\' hat den Prozess mit der Befehlszeile „C:\{PATH}\{ASSEMBLY}.{exe|dll}“ erstellt, ist aber abgestürzt, reagiert nicht oder lauscht dem angegebenen Port „{PORT}“ nicht. Fehlercode: {ERROR CODE}.)</span><span class="sxs-lookup"><span data-stu-id="7acbe-349">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="7acbe-350">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.</span><span class="sxs-lookup"><span data-stu-id="7acbe-350">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="7acbe-351">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="7acbe-351">Troubleshooting:</span></span>

<span data-ttu-id="7acbe-352">Der Prozess konnte nicht gestartet werden, weil wahrscheinlich ein Problem mit der Konfiguration oder Programmierung der App vorliegt.</span><span class="sxs-lookup"><span data-stu-id="7acbe-352">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="7acbe-353">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="7acbe-353">For more information, see the following topics:</span></span>

* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:test/troubleshoot>
