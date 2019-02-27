---
title: Referenz zu häufigen Fehlern bei Azure App Service und IIS mit ASP.NET Core
author: guardrex
description: Hier erfahren Sie, wie Sie häufige Fehler beim Hosten von ASP.NET Core-Apps in Azure Apps Service und IIS beheben können.
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2019
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 976f7e3fbeab9e81ba99e2dd7d09a892b854651b
ms.sourcegitcommit: 3c2ba9a0d833d2a096d9d800ba67a1a7f9491af0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55854460"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a><span data-ttu-id="359c2-103">Referenz zu häufigen Fehlern bei Azure App Service und IIS mit ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="359c2-103">Common errors reference for Azure App Service and IIS with ASP.NET Core</span></span>

<span data-ttu-id="359c2-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="359c2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="359c2-105">In diesem Artikel erfahren Sie, wie Sie häufige Fehler beim Hosten von ASP.NET Core-Apps in Azure Apps Service und IIS beheben können.</span><span class="sxs-lookup"><span data-stu-id="359c2-105">This topic offers troubleshooting advice for common errors when hosting ASP.NET Core apps on Azure Apps Service and IIS.</span></span>

<span data-ttu-id="359c2-106">Sammeln Sie folgende Informationen:</span><span class="sxs-lookup"><span data-stu-id="359c2-106">Collect the following information:</span></span>

* <span data-ttu-id="359c2-107">Browserverhalten (Statuscode und Fehlermeldung)</span><span class="sxs-lookup"><span data-stu-id="359c2-107">Browser behavior (status code and error message)</span></span>
* <span data-ttu-id="359c2-108">Einträge im Anwendungsereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="359c2-108">Application Event Log entries</span></span>
  * <span data-ttu-id="359c2-109">Azure App Service: <xref:host-and-deploy/azure-apps/troubleshoot></span><span class="sxs-lookup"><span data-stu-id="359c2-109">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="359c2-110">IIS</span><span class="sxs-lookup"><span data-stu-id="359c2-110">IIS</span></span>
    1. <span data-ttu-id="359c2-111">Klicken Sie im **Windows**-Menü auf **Start**, geben Sie *Ereignisanzeige* ein, und drücken Sie die **EINGABETASTE**.</span><span class="sxs-lookup"><span data-stu-id="359c2-111">Select **Start** on the **Windows** menu, type *Event Viewer*, and press **Enter**.</span></span>
    1. <span data-ttu-id="359c2-112">Erweitern Sie in der **Ereignisanzeige** die Option **Windows-Protokolle** > **Anwendung** auf der Randleiste.</span><span class="sxs-lookup"><span data-stu-id="359c2-112">After the **Event Viewer** opens, expand **Windows Logs** > **Application** in the sidebar.</span></span>
* <span data-ttu-id="359c2-113">stdout- und Debugprotokolleinträge im ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="359c2-113">ASP.NET Core Module stdout and debug log entries</span></span>
  * <span data-ttu-id="359c2-114">Azure App Service: <xref:host-and-deploy/azure-apps/troubleshoot></span><span class="sxs-lookup"><span data-stu-id="359c2-114">Azure App Service &ndash; See <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
  * <span data-ttu-id="359c2-115">IIS: Befolgen Sie die Anweisungen in den Abschnitten [Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) und [Erweiterte Diagnoseprotokolle](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) des Artikels zum ASP.NET Core-Modul.</span><span class="sxs-lookup"><span data-stu-id="359c2-115">IIS &ndash; Follow the instructions in the [Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) and [Enhanced diagnostic logs](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) sections of the ASP.NET Core Module topic.</span></span>

<span data-ttu-id="359c2-116">Vergleichen Sie die Fehlerinformationen mit folgenden häufigen Fehlern.</span><span class="sxs-lookup"><span data-stu-id="359c2-116">Compare error information to the following common errors.</span></span> <span data-ttu-id="359c2-117">Befolgen Sie die Hinweise zur Fehlerbehebung, wenn eine Übereinstimmung gefunden wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-117">If a match is found, follow the troubleshooting advice.</span></span>

<span data-ttu-id="359c2-118">Die Fehlerliste in diesem Artikel ist nicht vollständig.</span><span class="sxs-lookup"><span data-stu-id="359c2-118">The list of errors in this topic isn't exhaustive.</span></span> <span data-ttu-id="359c2-119">Wenn bei Ihnen ein Fehler auftritt, der hier nicht aufgeführt wird, öffnen Sie über die Schaltfläche **Feedback zum Inhalt** am Ende des Artikels ein neues Issue, in dem Sie den Fehler ausführlich beschreiben sollten, damit er reproduziert werden kann.</span><span class="sxs-lookup"><span data-stu-id="359c2-119">If you encounter an error not listed here, open a new issue using the **Content feedback** button at the bottom of this topic with detailed instructions on how to reproduce the error.</span></span>

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="installer-unable-to-obtain-vc-redistributable"></a><span data-ttu-id="359c2-120">Installationsprogramm konnte VC++ Redistributable nicht abrufen</span><span class="sxs-lookup"><span data-stu-id="359c2-120">Installer unable to obtain VC++ Redistributable</span></span>

* <span data-ttu-id="359c2-121">**Ausnahme des Installationsprogramms:** „0x80072efd“ **oder** „0x80072f76: Unbekannter Fehler“</span><span class="sxs-lookup"><span data-stu-id="359c2-121">**Installer Exception:** 0x80072efd **--OR--** 0x80072f76 - Unspecified error</span></span>

* <span data-ttu-id="359c2-122">**Protokollausnahme des Installationsprogramms&#8224;:** „Error 0x80072efd“ **oder** „0x80072f76: Fehler beim Ausführen des EXE-Pakets</span><span class="sxs-lookup"><span data-stu-id="359c2-122">**Installer Log Exception&#8224;:** Error 0x80072efd **--OR--** 0x80072f76: Failed to execute EXE package</span></span>

  <span data-ttu-id="359c2-123">&#8224;Das Protokoll befindet sich unter *C:\Benutzer\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span><span class="sxs-lookup"><span data-stu-id="359c2-123">&#8224;The log is located at *C:\Users\{USER}\AppData\Local\Temp\dd_DotNetCoreWinSvrHosting__{TIMESTAMP}.log*.</span></span>

<span data-ttu-id="359c2-124">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-124">Troubleshooting:</span></span>

<span data-ttu-id="359c2-125">Wenn das System während der [Installation des .NET Core-Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) keinen Zugriff auf das Internet hat, tritt diese Ausnahme auf, wenn der Installer *Microsoft Visual C++ 2015 Redistributable* nicht abrufen kann.</span><span class="sxs-lookup"><span data-stu-id="359c2-125">If the system doesn't have Internet access while [installing the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle), this exception occurs when the installer is prevented from obtaining the *Microsoft Visual C++ 2015 Redistributable*.</span></span> <span data-ttu-id="359c2-126">Rufen Sie einen Installer aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840) ab.</span><span class="sxs-lookup"><span data-stu-id="359c2-126">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span> <span data-ttu-id="359c2-127">Wenn der Installer fehlschlägt, erhält der Server möglicherweise nicht die .NET Core-Runtime, die zum Hosten einer [Framework-abhängigen Bereitstellung (Framework-Dependent Deployment, FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd) erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="359c2-127">If the installer fails, the server may not receive the .NET Core runtime required to host a [framework-dependent deployment (FDD)](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span> <span data-ttu-id="359c2-128">Wenn Sie eine FDD hosten, vergewissern Sie sich, dass die Runtime unter **Programme und Features** oder unter **Apps und Features** installiert ist.</span><span class="sxs-lookup"><span data-stu-id="359c2-128">If hosting an FDD, confirm that the runtime is installed in **Programs & Features** or **Apps & features**.</span></span> <span data-ttu-id="359c2-129">Wenn eine bestimmte Runtime erforderlich ist, laden Sie diese von der Seite [.NET Download Archives (.NET-Downloadarchive)](https://dotnet.microsoft.com/download/archives) herunter, und installieren Sie sie auf dem System.</span><span class="sxs-lookup"><span data-stu-id="359c2-129">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="359c2-130">Starten Sie nach dem Installieren der Runtime das System neu, oder starten Sie IIS neu, indem Sie **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="359c2-130">After installing the runtime, restart the system or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a><span data-ttu-id="359c2-131">Durch ein Upgrade des Betriebssystems wird das ASP.NET Core-Modul (32-Bit) entfernt</span><span class="sxs-lookup"><span data-stu-id="359c2-131">OS upgrade removed the 32-bit ASP.NET Core Module</span></span>

<span data-ttu-id="359c2-132">**Anwendungsprotokoll:** Fehler beim Laden der Modul-DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll**.</span><span class="sxs-lookup"><span data-stu-id="359c2-132">**Application Log:** The Module DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** failed to load.</span></span> <span data-ttu-id="359c2-133">Die Daten sind der Fehler.</span><span class="sxs-lookup"><span data-stu-id="359c2-133">The data is the error.</span></span>

<span data-ttu-id="359c2-134">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-134">Troubleshooting:</span></span>

<span data-ttu-id="359c2-135">Nicht zum Betriebssystem gehörende Dateien im Verzeichnis **C:\Windows\SysWOW64\inetsrv** werden während eines Betriebssystemupgrades nicht beibehalten.</span><span class="sxs-lookup"><span data-stu-id="359c2-135">Non-OS files in the **C:\Windows\SysWOW64\inetsrv** directory aren't preserved during an OS upgrade.</span></span> <span data-ttu-id="359c2-136">Dieses Problem tritt auf, wenn vor der Durchführung eines Betriebssystemupgrades das ASP.NET Core-Modul installiert wurde und nach einem Betriebssystemupgrade ein App-Pool im 32-Bit-Modus ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-136">If the ASP.NET Core Module is installed prior to an OS upgrade and then any app pool is run in 32-bit mode after an OS upgrade, this issue is encountered.</span></span> <span data-ttu-id="359c2-137">Reparieren Sie nach einem Betriebssystemupgrade das ASP.NET Core-Modul.</span><span class="sxs-lookup"><span data-stu-id="359c2-137">After an OS upgrade, repair the ASP.NET Core Module.</span></span> <span data-ttu-id="359c2-138">Siehe [Installieren des .NET Core-Hostingpakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="359c2-138">See [Install the .NET Core Hosting bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span> <span data-ttu-id="359c2-139">Wählen Sie **Reparatur** aus, wenn der Installer ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-139">Select **Repair** when the installer is run.</span></span>

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a><span data-ttu-id="359c2-140">Bereitstellung einer x86-App, obwohl der App-Pool nicht für 32-Bit-Apps aktiviert ist</span><span class="sxs-lookup"><span data-stu-id="359c2-140">An x86 app is deployed but the app pool isn't enabled for 32-bit apps</span></span>

* <span data-ttu-id="359c2-141">**Browser:** HTTP-Fehler 500.30: In-Process-Startfehler beim ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="359c2-141">**Browser:** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="359c2-142">**Anwendungsprotokoll:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span><span class="sxs-lookup"><span data-stu-id="359c2-142">**Application Log:** Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' hit unexpected managed exception, exception code = '0xe0434352'.</span></span> <span data-ttu-id="359c2-143">Überprüfen Sie die stderr-Protokolle, um weitere Informationen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="359c2-143">Please check the stderr logs for more information.</span></span> <span data-ttu-id="359c2-144">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span><span class="sxs-lookup"><span data-stu-id="359c2-144">Application '/LM/W3SVC/5/ROOT' with physical root '{PATH}' failed to load clr and managed application.</span></span> <span data-ttu-id="359c2-145">Der CLR-Arbeitsthread wurde vorzeitig beendet</span><span class="sxs-lookup"><span data-stu-id="359c2-145">CLR worker thread exited prematurely</span></span>

* <span data-ttu-id="359c2-146">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.</span><span class="sxs-lookup"><span data-stu-id="359c2-146">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-147">**Debugprotokoll des ASP.NET Core-Moduls:** Failed HRESULT returned: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="359c2-147">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

<span data-ttu-id="359c2-148">Dieses Szenario wird vom SDK ausgelöst, wenn eine eigenständige App veröffentlicht wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-148">This scenario is trapped by the SDK when publishing a self-contained app.</span></span> <span data-ttu-id="359c2-149">Das SDK generiert einen Fehler, wenn die relative ID (RID) nicht mit der Zielplattform übereinstimmt (z. B. bei der RID `win10-x64` für eine Projektdatei mit `<PlatformTarget>x86</PlatformTarget>`).</span><span class="sxs-lookup"><span data-stu-id="359c2-149">The SDK produces an error if the RID doesn't match the platform target (for example, `win10-x64` RID with `<PlatformTarget>x86</PlatformTarget>` in the project file).</span></span>

<span data-ttu-id="359c2-150">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-150">Troubleshooting:</span></span>

<span data-ttu-id="359c2-151">Aktivieren Sie den IIS-App-Pool für 32-Bit-Apps, wenn Sie von x86-Frameworks abhängige Bereitstellungen (`<PlatformTarget>x86</PlatformTarget>`) durchführen möchten.</span><span class="sxs-lookup"><span data-stu-id="359c2-151">For an x86 framework-dependent deployment (`<PlatformTarget>x86</PlatformTarget>`), enable the IIS app pool for 32-bit apps.</span></span> <span data-ttu-id="359c2-152">Öffnen Sie im IIS-Manager die Option **Erweiterte Einstellungen** des App-Pools, und legen Sie **32-Bit-Anwendungen aktivieren** auf **Wahr** fest.</span><span class="sxs-lookup"><span data-stu-id="359c2-152">In IIS Manager, open the app pool's **Advanced Settings** and set **Enable 32-Bit Applications** to **True**.</span></span>

## <a name="platform-conflicts-with-rid"></a><span data-ttu-id="359c2-153">Plattformkonflikte mit RID</span><span class="sxs-lookup"><span data-stu-id="359c2-153">Platform conflicts with RID</span></span>

* <span data-ttu-id="359c2-154">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="359c2-154">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="359c2-155">**Anwendungsprotokoll:** Die Anwendung „MACHINE/WEBROOT/APPHOST/{ASSEMBLY}“ mit dem physischen Stamm „C:\{PATH}\' konnte den Prozess mit der Befehlszeile „C:\{PATH}{assembly}.{exe|dll}“ nicht starten. Fehlercode = 0x80004005 : ff.</span><span class="sxs-lookup"><span data-stu-id="359c2-155">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"C:\{PATH}{ASSEMBLY}.{exe|dll}" ', ErrorCode = '0x80004005 : ff.</span></span>

* <span data-ttu-id="359c2-156">**stdout-Protokoll des ASP.NET Core-Moduls:** Ausnahmefehler: System.BadImageFormatException: Die Datei oder Assembly „{ASSEMBLY}.dll“ wurde nicht gefunden.</span><span class="sxs-lookup"><span data-stu-id="359c2-156">**ASP.NET Core Module stdout Log:** Unhandled Exception: System.BadImageFormatException: Could not load file or assembly '{ASSEMBLY}.dll'.</span></span> <span data-ttu-id="359c2-157">Es wurde versucht, ein Programm mit einem falschen Format zu laden.</span><span class="sxs-lookup"><span data-stu-id="359c2-157">An attempt was made to load a program with an incorrect format.</span></span>

<span data-ttu-id="359c2-158">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-158">Troubleshooting:</span></span>

* <span data-ttu-id="359c2-159">Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-159">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="359c2-160">Ein Prozessfehler könnte die Folge eines Problems in der App sein.</span><span class="sxs-lookup"><span data-stu-id="359c2-160">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="359c2-161">Weitere Informationen finden Sie unter [Problembehandlung bei ASP.NET Core in IIS](xref:host-and-deploy/iis/troubleshoot) oder [Problembehandlung bei ASP.NET Core in Azure App Service](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="359c2-161">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="359c2-162">Wenn diese Ausnahme für eine Azure-App-Bereitstellung beim Aktualisieren einer App und beim Bereitstellen neuerer Assemblys auftritt, löschen Sie alle Dateien aus der vorherigen Bereitstellung manuell.</span><span class="sxs-lookup"><span data-stu-id="359c2-162">If this exception occurs for an Azure Apps deployment when upgrading an app and deploying newer assemblies, manually delete all files from the prior deployment.</span></span> <span data-ttu-id="359c2-163">Veraltete inkompatible Assemblys können zu einer `System.BadImageFormatException`-Ausnahme führen, wenn Sie eine aktualisierte App bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="359c2-163">Lingering incompatible assemblies can result in a `System.BadImageFormatException` exception when deploying an upgraded app.</span></span>

## <a name="uri-endpoint-wrong-or-stopped-website"></a><span data-ttu-id="359c2-164">URI-Endpunkt falsch oder beendete Website</span><span class="sxs-lookup"><span data-stu-id="359c2-164">URI endpoint wrong or stopped website</span></span>

* <span data-ttu-id="359c2-165">**Browser:** „ERR_CONNECTION_REFUSED“ **oder** „Es kann keine Verbindung hergestellt werden.“</span><span class="sxs-lookup"><span data-stu-id="359c2-165">**Browser:** ERR_CONNECTION_REFUSED **--OR--** Unable to connect</span></span>

* <span data-ttu-id="359c2-166">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="359c2-166">**Application Log:** No entry</span></span>

* <span data-ttu-id="359c2-167">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-167">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-168">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-168">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="359c2-169">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-169">Troubleshooting:</span></span>

* <span data-ttu-id="359c2-170">Vergewissern Sie sich, dass der richtige URI-Endpunkt für die App verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-170">Confirm the correct URI endpoint for the app is in use.</span></span> <span data-ttu-id="359c2-171">Überprüfen Sie die Bindungen.</span><span class="sxs-lookup"><span data-stu-id="359c2-171">Check the bindings.</span></span>

* <span data-ttu-id="359c2-172">Vergewissern Sie sich, dass die IIS-Website nicht den Status *Beendet* aufweist.</span><span class="sxs-lookup"><span data-stu-id="359c2-172">Confirm that the IIS website isn't in the *Stopped* state.</span></span>

## <a name="corewebengine-or-w3svc-server-features-disabled"></a><span data-ttu-id="359c2-173">CoreWebEngine oder W3SVC-Serverfeatures deaktiviert</span><span class="sxs-lookup"><span data-stu-id="359c2-173">CoreWebEngine or W3SVC server features disabled</span></span>

<span data-ttu-id="359c2-174">**Betriebssystemausnahme:** Die Features „CoreWebEngine“ und „W3SVC“ von IIS 7.0 müssen installiert sein, um das ASP.NET Core-Modul zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="359c2-174">**OS Exception:** The IIS 7.0 CoreWebEngine and W3SVC features must be installed to use the ASP.NET Core Module.</span></span>

<span data-ttu-id="359c2-175">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-175">Troubleshooting:</span></span>

<span data-ttu-id="359c2-176">Vergewissern Sie sich, dass die richtigen Rollen und Features aktiviert wurden.</span><span class="sxs-lookup"><span data-stu-id="359c2-176">Confirm that the proper role and features are enabled.</span></span> <span data-ttu-id="359c2-177">Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="359c2-177">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

## <a name="incorrect-website-physical-path-or-app-missing"></a><span data-ttu-id="359c2-178">Falscher physischer Pfad der Website oder App fehlt</span><span class="sxs-lookup"><span data-stu-id="359c2-178">Incorrect website physical path or app missing</span></span>

* <span data-ttu-id="359c2-179">**Browser:** „403 – Verboten: Zugriff verweigert.“ **oder** „403.14 Verboten: Der Webserver wurde dafür konfiguriert, die Inhalte dieses Verzeichnisses nicht aufzulisten.</span><span class="sxs-lookup"><span data-stu-id="359c2-179">**Browser:** 403 Forbidden - Access is denied **--OR--** 403.14 Forbidden - The Web server is configured to not list the contents of this directory.</span></span>

* <span data-ttu-id="359c2-180">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="359c2-180">**Application Log:** No entry</span></span>

* <span data-ttu-id="359c2-181">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-181">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-182">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-182">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="359c2-183">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-183">Troubleshooting:</span></span>

<span data-ttu-id="359c2-184">Überprüfen Sie die **Grundeinstellungen** der IIS-Website und den physischen App-Ordner.</span><span class="sxs-lookup"><span data-stu-id="359c2-184">Check the IIS website **Basic Settings** and the physical app folder.</span></span> <span data-ttu-id="359c2-185">Vergewissern Sie sich, dass sich die App im Ordner im **physischen Pfad** der IIS-Website befindet.</span><span class="sxs-lookup"><span data-stu-id="359c2-185">Confirm that the app is in the folder at the IIS website **Physical path**.</span></span>

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a><span data-ttu-id="359c2-186">„Falsche Rolle“, „ASP.NET Core-Modul nicht installiert“ oder „Falsche Berechtigungen“</span><span class="sxs-lookup"><span data-stu-id="359c2-186">Incorrect role, ASP.NET Core Module not installed, or incorrect permissions</span></span>

* <span data-ttu-id="359c2-187">**Browser:** 500.19 – Interner Serverfehler: Auf die angeforderte Seite kann nicht zugegriffen werden, da die zugehörigen Konfigurationsdaten für die Seite ungültig sind.</span><span class="sxs-lookup"><span data-stu-id="359c2-187">**Browser:** 500.19 Internal Server Error - The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span> <span data-ttu-id="359c2-188">**oder** „Diese Seite kann nicht angezeigt werden.“</span><span class="sxs-lookup"><span data-stu-id="359c2-188">**--OR--** This page can't be displayed</span></span>

* <span data-ttu-id="359c2-189">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="359c2-189">**Application Log:** No entry</span></span>

* <span data-ttu-id="359c2-190">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-190">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-191">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-191">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="359c2-192">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-192">Troubleshooting:</span></span>

* <span data-ttu-id="359c2-193">Vergewissern Sie sich, dass die richtige Rolle aktiviert wurde.</span><span class="sxs-lookup"><span data-stu-id="359c2-193">Confirm that the proper role is enabled.</span></span> <span data-ttu-id="359c2-194">Siehe [IIS-Konfiguration](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="359c2-194">See [IIS Configuration](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

* <span data-ttu-id="359c2-195">Öffnen Sie **Programme und Features** oder **Apps und Features**, und vergewissern Sie sich, dass **Windows Server Hosting** installiert ist.</span><span class="sxs-lookup"><span data-stu-id="359c2-195">Open **Programs & Features** or **Apps & features** and confirm that **Windows Server Hosting** is installed.</span></span> <span data-ttu-id="359c2-196">Wenn **Windows Server Hosting** nicht in der Liste der installierten Programme vorhanden ist, laden Sie das .NET Core Hosting-Paket herunter, und installieren Sie es.</span><span class="sxs-lookup"><span data-stu-id="359c2-196">If **Windows Server Hosting** isn't present in the list of installed programs, download and install the .NET Core Hosting Bundle.</span></span>

  [<span data-ttu-id="359c2-197">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="359c2-197">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="359c2-198">Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="359c2-198">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

* <span data-ttu-id="359c2-199">Stellen Sie sicher, dass **Anwendungspool** > **Prozessmodell** > **Identität** auf **ApplicationPoolIdentity** festgelegt ist, oder dass die benutzerdefinierte Identität über die erforderlichen Berechtigungen für den Zugriff auf den Bereitstellungsordner der App verfügt.</span><span class="sxs-lookup"><span data-stu-id="359c2-199">Make sure that the **Application Pool** > **Process Model** > **Identity** is set to **ApplicationPoolIdentity** or the custom identity has the correct permissions to access the app's deployment folder.</span></span>

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a><span data-ttu-id="359c2-200">Falscher processPath-Wert, fehlende PATH-Variable, Hostingpaket nicht installiert, System/IIS wird nicht neu gestartet, VC++ Redistributable nicht installiert oder dotnet.exe-Zugriffsverletzung</span><span class="sxs-lookup"><span data-stu-id="359c2-200">Incorrect processPath, missing PATH variable, Hosting Bundle not installed, system/IIS not restarted, VC++ Redistributable not installed, or dotnet.exe access violation</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-201">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="359c2-201">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="359c2-202">**Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span><span class="sxs-lookup"><span data-stu-id="359c2-202">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="359c2-203">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="359c2-203">', ErrorCode = '0x80070002 : 0.</span></span> <span data-ttu-id="359c2-204">Application '{PATH}' wasn't able to start.</span><span class="sxs-lookup"><span data-stu-id="359c2-204">Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="359c2-205">Es wurde keine ausführbare Datei unter „{PATH}“ gefunden.</span><span class="sxs-lookup"><span data-stu-id="359c2-205">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="359c2-206">Die Anwendung „/LM/W3SVC/2/ROOT“ konnte nicht gestartet werden. Fehlercode: 0x8007023e.</span><span class="sxs-lookup"><span data-stu-id="359c2-206">Failed to start application '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.</span></span>

* <span data-ttu-id="359c2-207">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-207">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="359c2-208">**Debugprotokoll des ASP.NET Core-Moduls:** Ereignisprotokoll: 'Application '{PATH}' wasn't able to start.</span><span class="sxs-lookup"><span data-stu-id="359c2-208">**ASP.NET Core Module Debug Log:** Event Log: 'Application '{PATH}' wasn't able to start.</span></span> <span data-ttu-id="359c2-209">Es wurde keine ausführbare Datei unter „{PATH}“ gefunden.</span><span class="sxs-lookup"><span data-stu-id="359c2-209">Executable was not found at '{PATH}'.</span></span> <span data-ttu-id="359c2-210">Failed HRESULT returned: 0x8007023e</span><span class="sxs-lookup"><span data-stu-id="359c2-210">Failed HRESULT returned: 0x8007023e</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="359c2-211">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="359c2-211">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="359c2-212">**Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span><span class="sxs-lookup"><span data-stu-id="359c2-212">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"{...}"</span></span> <span data-ttu-id="359c2-213">', ErrorCode = '0x80070002 : 0.</span><span class="sxs-lookup"><span data-stu-id="359c2-213">', ErrorCode = '0x80070002 : 0.</span></span>

* <span data-ttu-id="359c2-214">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.</span><span class="sxs-lookup"><span data-stu-id="359c2-214">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="359c2-215">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-215">Troubleshooting:</span></span>

* <span data-ttu-id="359c2-216">Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-216">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="359c2-217">Ein Prozessfehler könnte die Folge eines Problems in der App sein.</span><span class="sxs-lookup"><span data-stu-id="359c2-217">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="359c2-218">Weitere Informationen finden Sie unter [Problembehandlung bei ASP.NET Core in IIS](xref:host-and-deploy/iis/troubleshoot) oder [Problembehandlung bei ASP.NET Core in Azure App Service](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="359c2-218">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="359c2-219">Überprüfen Sie das Attribut *processPath* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass es den Wert `dotnet` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) oder `.\{ASSEMBLY}.exe` für eine [eigenständige Bereitstellung (Self-Contained Deployment, SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) enthält.</span><span class="sxs-lookup"><span data-stu-id="359c2-219">Check the *processPath* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's `dotnet` for a framework-dependent deployment (FDD) or `.\{ASSEMBLY}.exe` for a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd).</span></span>

* <span data-ttu-id="359c2-220">Für eine Framework-abhängige Bereitstellung ist *dotnet.exe* möglicherweise über die PATH-Einstellungen nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="359c2-220">For an FDD, *dotnet.exe* might not be accessible via the PATH settings.</span></span> <span data-ttu-id="359c2-221">Überprüfen Sie, ob *C:\Programme\dotnet\\* in den PATH-Einstellungen des Systems vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="359c2-221">Confirm that *C:\Program Files\dotnet\\* exists in the System PATH settings.</span></span>

* <span data-ttu-id="359c2-222">Für eine frameworkabhängige Bereitstellung ist *dotnet.exe* möglicherweise für die Benutzeridentität des App-Pools nicht verfügbar.</span><span class="sxs-lookup"><span data-stu-id="359c2-222">For an FDD, *dotnet.exe* might not be accessible for the user identity of the app pool.</span></span> <span data-ttu-id="359c2-223">Vergewissern Sie sich, dass die Benutzeridentität des App-Pools Zugriff auf das Verzeichnis *C:\Programme\dotnet* hat.</span><span class="sxs-lookup"><span data-stu-id="359c2-223">Confirm that the app pool user identity has access to the *C:\Program Files\dotnet* directory.</span></span> <span data-ttu-id="359c2-224">Vergewissern Sie sich, dass keine Ablehnungsregeln für die Benutzeridentität des App-Pools im Verzeichnis *C:\Programme\dotnet* und in den App-Verzeichnissen konfiguriert sind.</span><span class="sxs-lookup"><span data-stu-id="359c2-224">Confirm that there are no deny rules configured for the app pool user identity on the *C:\Program Files\dotnet* and app directories.</span></span>

* <span data-ttu-id="359c2-225">Möglicherweise wurde eine FDD bereitgestellt und .NET Core installiert, ohne IIS neu zu starten.</span><span class="sxs-lookup"><span data-stu-id="359c2-225">An FDD may have been deployed and .NET Core installed without restarting IIS.</span></span> <span data-ttu-id="359c2-226">Starten Sie den Server neu, oder starten Sie IIS neu, indem Sie **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="359c2-226">Either restart the server or restart IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="359c2-227">Möglicherweise wurde eine FDD bereitgestellt, ohne die .NET Core-Runtime auf dem Hostsystem zu installieren.</span><span class="sxs-lookup"><span data-stu-id="359c2-227">An FDD may have been deployed without installing the .NET Core runtime on the hosting system.</span></span> <span data-ttu-id="359c2-228">Führen Sie den **Installer für das .NET Core-Hostingpaket** auf dem System aus, wenn die .NET Core-Runtime nicht installiert wurde.</span><span class="sxs-lookup"><span data-stu-id="359c2-228">If the .NET Core runtime hasn't been installed, run the **.NET Core Hosting Bundle installer** on the system.</span></span>

  [<span data-ttu-id="359c2-229">Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)</span><span class="sxs-lookup"><span data-stu-id="359c2-229">Current .NET Core Hosting Bundle installer (direct download)</span></span>](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  <span data-ttu-id="359c2-230">Weitere Informationen finden Sie unter [Installieren des .NET Core Hosting-Pakets](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span><span class="sxs-lookup"><span data-stu-id="359c2-230">For more information, see [Install the .NET Core Hosting Bundle](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle).</span></span>

  <span data-ttu-id="359c2-231">Wenn eine bestimmte Runtime erforderlich ist, laden Sie diese von der Seite [.NET Download Archives (.NET-Downloadarchive)](https://dotnet.microsoft.com/download/archives) herunter, und installieren Sie sie auf dem System.</span><span class="sxs-lookup"><span data-stu-id="359c2-231">If a specific runtime is required, download the runtime from the [.NET Download Archives](https://dotnet.microsoft.com/download/archives) and install it on the system.</span></span> <span data-ttu-id="359c2-232">Schließen Sie die Installation ab, indem Sie das System oder IIS neu starten. Führen Sie dazu **net stop was /y** gefolgt von **net start w3svc** über eine Eingabeaufforderung aus.</span><span class="sxs-lookup"><span data-stu-id="359c2-232">Complete the installation by restarting the system or restarting IIS by executing **net stop was /y** followed by **net start w3svc** from a command prompt.</span></span>

* <span data-ttu-id="359c2-233">Möglicherweise wurde eine FDD bereitgestellt, und *Microsoft Visual C++ 2015 Redistributable (x64)* ist auf dem System nicht installiert.</span><span class="sxs-lookup"><span data-stu-id="359c2-233">An FDD may have been deployed and the *Microsoft Visual C++ 2015 Redistributable (x64)* isn't installed on the system.</span></span> <span data-ttu-id="359c2-234">Rufen Sie einen Installer aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840) ab.</span><span class="sxs-lookup"><span data-stu-id="359c2-234">Obtain an installer from the [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53840).</span></span>

## <a name="incorrect-arguments-of-aspnetcore-element"></a><span data-ttu-id="359c2-235">Falsche Argumente des Elements \<aspNetCore></span><span class="sxs-lookup"><span data-stu-id="359c2-235">Incorrect arguments of \<aspNetCore> element</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-236">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="359c2-236">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="359c2-237">**Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="359c2-237">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="359c2-238">Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen.</span><span class="sxs-lookup"><span data-stu-id="359c2-238">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="359c2-239">Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="359c2-239">Could not find inprocess request handler.</span></span> <span data-ttu-id="359c2-240">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span><span class="sxs-lookup"><span data-stu-id="359c2-240">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="359c2-241">Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Fehler beim Starten der Anwendung "/ LM/W3SVC/3/ROOT", ErrorCode "0x8000ffff".</span><span class="sxs-lookup"><span data-stu-id="359c2-241">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed to start application '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="359c2-242">**stdout-Protokoll des ASP.NET Core-Moduls:** Did you mean to run dotnet SDK commands?</span><span class="sxs-lookup"><span data-stu-id="359c2-242">**ASP.NET Core Module stdout Log:** Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="359c2-243">Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span><span class="sxs-lookup"><span data-stu-id="359c2-243">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409</span></span>

* <span data-ttu-id="359c2-244">**Debugprotokoll des ASP.NET Core-Moduls:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="359c2-244">**ASP.NET Core Module Debug Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="359c2-245">Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen.</span><span class="sxs-lookup"><span data-stu-id="359c2-245">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="359c2-246">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="359c2-246">Failed HRESULT returned: 0x8000ffff Could not find inprocess request handler.</span></span> <span data-ttu-id="359c2-247">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span><span class="sxs-lookup"><span data-stu-id="359c2-247">Captured output from invoking hostfxr: Did you mean to run dotnet SDK commands?</span></span> <span data-ttu-id="359c2-248">Installieren Sie das .NET SDK über: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 HRESULT-Fehler zurückgegeben: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="359c2-248">Please install dotnet SDK from: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="359c2-249">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="359c2-249">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="359c2-250">**Anwendungsprotokoll:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span><span class="sxs-lookup"><span data-stu-id="359c2-250">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' failed to start process with commandline '"dotnet" .\{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.</span></span>

* <span data-ttu-id="359c2-251">**stdout-Protokoll des ASP.NET Core-Moduls:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span><span class="sxs-lookup"><span data-stu-id="359c2-251">**ASP.NET Core Module stdout Log:** The application to execute does not exist: 'PATH\{ASSEMBLY}.dll'</span></span>

::: moniker-end

<span data-ttu-id="359c2-252">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-252">Troubleshooting:</span></span>

* <span data-ttu-id="359c2-253">Vergewissern Sie sich, dass die App lokal auf Kestrel ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="359c2-253">Confirm that the app runs locally on Kestrel.</span></span> <span data-ttu-id="359c2-254">Ein Prozessfehler könnte die Folge eines Problems in der App sein.</span><span class="sxs-lookup"><span data-stu-id="359c2-254">A process failure might be the result of a problem within the app.</span></span> <span data-ttu-id="359c2-255">Weitere Informationen finden Sie unter [Problembehandlung bei ASP.NET Core in IIS](xref:host-and-deploy/iis/troubleshoot) oder [Problembehandlung bei ASP.NET Core in Azure App Service](xref:host-and-deploy/azure-apps/troubleshoot).</span><span class="sxs-lookup"><span data-stu-id="359c2-255">For more information, see [Troubleshoot (IIS)](xref:host-and-deploy/iis/troubleshoot) or [Troubleshoot (Azure App Service)](xref:host-and-deploy/azure-apps/troubleshoot).</span></span>

* <span data-ttu-id="359c2-256">Überprüfen Sie das Attribut *arguments* im Element `<aspNetCore>` in der Datei *web.config*, um sicherzustellen, dass der Wert entweder `.\{ASSEMBLY}.dll` für eine frameworkabhängige Bereitstellung (Framework-Dependent Deployment, FDD) entspricht oder nicht vorhanden, eine leere Zeichenfolge (`arguments=""`) oder eine Liste von Argumenten der App (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) für eine eigenständige Bereitstellung (Self-Contained Deployment, SCD) ist.</span><span class="sxs-lookup"><span data-stu-id="359c2-256">Examine the *arguments* attribute on the `<aspNetCore>` element in *web.config* to confirm that it's either (a) `.\{ASSEMBLY}.dll` for a framework-dependent deployment (FDD); or (b) not present, an empty string (`arguments=""`), or a list of the app's arguments (`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`) for a self-contained deployment (SCD).</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="missing-net-core-shared-framework"></a><span data-ttu-id="359c2-257">Fehlendes freigegebenes .NET Core-Framework</span><span class="sxs-lookup"><span data-stu-id="359c2-257">Missing .NET Core shared framework</span></span>

* <span data-ttu-id="359c2-258">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="359c2-258">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure</span></span>

* <span data-ttu-id="359c2-259">**Anwendungsprotokoll:** Der Aufruf von „hostfxr“ zum Ermitteln des In-Process-Anforderungshandlers ist fehlgeschlagen, ohne native Abhängigkeiten zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="359c2-259">**Application Log:** Invoking hostfxr to find the inprocess request handler failed without finding any native dependencies.</span></span> <span data-ttu-id="359c2-260">Das bedeutet wahrscheinlich, dass die App falsch konfiguriert wurde. Vergleichen Sie die Versionen von „Microsoft.NetCore.App“ und „Microsoft.AspNetCore.App“, die von der Anwendung angezielt werden, mit den auf dem Computer installierten Versionen.</span><span class="sxs-lookup"><span data-stu-id="359c2-260">This most likely means the app is misconfigured, please check the versions of Microsoft.NetCore.App and Microsoft.AspNetCore.App that are targeted by the application and are installed on the machine.</span></span> <span data-ttu-id="359c2-261">Could not find inprocess request handler.</span><span class="sxs-lookup"><span data-stu-id="359c2-261">Could not find inprocess request handler.</span></span> <span data-ttu-id="359c2-262">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span><span class="sxs-lookup"><span data-stu-id="359c2-262">Captured output from invoking hostfxr: It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="359c2-263">Das angegebene Framework „Microsoft.AspNetCore.App“ (Version {VERSION}) konnte nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-263">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

<span data-ttu-id="359c2-264">Die Anwendung „/LM/W3SVC/5/ROOT“ konnte nicht gestartet werden. Fehlercode: 0x8000ffff.</span><span class="sxs-lookup"><span data-stu-id="359c2-264">Failed to start application '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.</span></span>

* <span data-ttu-id="359c2-265">**stdout-Protokoll des ASP.NET Core-Moduls:** Es konnte keine kompatible Frameworkversion gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-265">**ASP.NET Core Module stdout Log:** It was not possible to find any compatible framework version.</span></span> <span data-ttu-id="359c2-266">Das angegebene Framework „Microsoft.AspNetCore.App“ (Version {VERSION}) konnte nicht gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-266">The specified framework 'Microsoft.AspNetCore.App', version '{VERSION}' was not found.</span></span>

* <span data-ttu-id="359c2-267">**Debugprotokoll des ASP.NET Core-Moduls:** Failed HRESULT returned: 0x8000ffff</span><span class="sxs-lookup"><span data-stu-id="359c2-267">**ASP.NET Core Module Debug Log:** Failed HRESULT returned: 0x8000ffff</span></span>

::: moniker-end

<span data-ttu-id="359c2-268">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-268">Troubleshooting:</span></span>

<span data-ttu-id="359c2-269">Stellen Sie für eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) sicher, dass die richtige Runtime im System installiert ist.</span><span class="sxs-lookup"><span data-stu-id="359c2-269">For a framework-dependent deployment (FDD), confirm that the correct runtime installed on the system.</span></span>

## <a name="stopped-application-pool"></a><span data-ttu-id="359c2-270">Beendeter Anwendungspool</span><span class="sxs-lookup"><span data-stu-id="359c2-270">Stopped Application Pool</span></span>

* <span data-ttu-id="359c2-271">**Browser:** 503 – Dienst nicht verfügbar</span><span class="sxs-lookup"><span data-stu-id="359c2-271">**Browser:** 503 Service Unavailable</span></span>

* <span data-ttu-id="359c2-272">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="359c2-272">**Application Log:** No entry</span></span>

* <span data-ttu-id="359c2-273">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-273">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-274">**Debugprotokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-274">**ASP.NET Core Module Debug Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="359c2-275">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-275">Troubleshooting:</span></span>

<span data-ttu-id="359c2-276">Vergewissern Sie sich, dass der Anwendungspool nicht den Status *Beendet* aufweist.</span><span class="sxs-lookup"><span data-stu-id="359c2-276">Confirm that the Application Pool isn't in the *Stopped* state.</span></span>

## <a name="sub-application-includes-a-handlers-section"></a><span data-ttu-id="359c2-277">Untergeordnete Anwendung mit \<handlers>-Abschnitt</span><span class="sxs-lookup"><span data-stu-id="359c2-277">Sub-application includes a \<handlers> section</span></span>

* <span data-ttu-id="359c2-278">**Browser:** HTTP-Fehler 500.19: Interner Serverfehler</span><span class="sxs-lookup"><span data-stu-id="359c2-278">**Browser:** HTTP Error 500.19 - Internal Server Error</span></span>

* <span data-ttu-id="359c2-279">**Anwendungsprotokoll:** Kein Eintrag</span><span class="sxs-lookup"><span data-stu-id="359c2-279">**Application Log:** No entry</span></span>

* <span data-ttu-id="359c2-280">**stdout-Protokoll des ASP.NET Core-Moduls:** The root app's log file is created and shows normal operation.</span><span class="sxs-lookup"><span data-stu-id="359c2-280">**ASP.NET Core Module stdout Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="359c2-281">Die Protokolldatei der untergeordneten App wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-281">The sub-app's log file isn't created.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-282">**Debugprotokoll des ASP.NET Core-Moduls:** The root app's log file is created and shows normal operation.</span><span class="sxs-lookup"><span data-stu-id="359c2-282">**ASP.NET Core Module Debug Log:** The root app's log file is created and shows normal operation.</span></span> <span data-ttu-id="359c2-283">Die Protokolldatei der untergeordneten App wurde nicht erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-283">The sub-app's log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="359c2-284">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-284">Troubleshooting:</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="359c2-285">Überprüfen Sie, ob der Abschnitt `<handlers>` in der *web.config*-Datei der untergeordneten App fehlt oder ob die untergeordnete App die Handler der übergeordneten App nicht erbt.</span><span class="sxs-lookup"><span data-stu-id="359c2-285">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section or that the sub-app doesn't inherit the parent app's handlers.</span></span>

<span data-ttu-id="359c2-286">Der Abschnitt `<system.webServer>` in der *web.config*-Datei der übergeordneten App befindet sich in einem `<location>`-Element.</span><span class="sxs-lookup"><span data-stu-id="359c2-286">The parent app's `<system.webServer>` section of *web.config* is placed inside of a `<location>` element.</span></span> <span data-ttu-id="359c2-287">Die <xref:System.Configuration.SectionInformation.InheritInChildApplications*>-Eigenschaft wird auf `false` festgelegt, um anzugeben, dass die im [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location)-Element angegebenen Einstellungen nicht von Apps geerbt werden, die in einem Unterverzeichnis der übergeordneten App gespeichert sind.</span><span class="sxs-lookup"><span data-stu-id="359c2-287">The <xref:System.Configuration.SectionInformation.InheritInChildApplications*> property is set to `false` to indicate that the settings specified within the [\<location>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) element aren't inherited by apps that reside in a subdirectory of the parent app.</span></span> <span data-ttu-id="359c2-288">Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="359c2-288">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="359c2-289">Überprüfen Sie, ob die Datei *web.config* der untergeordneten App einen `<handlers>`-Abschnitt enthält.</span><span class="sxs-lookup"><span data-stu-id="359c2-289">Confirm that the sub-app's *web.config* file doesn't include a `<handlers>` section.</span></span>

::: moniker-end

## <a name="stdout-log-path-incorrect"></a><span data-ttu-id="359c2-290">Der stdout-Protokollpfad ist falsch</span><span class="sxs-lookup"><span data-stu-id="359c2-290">stdout log path incorrect</span></span>

* <span data-ttu-id="359c2-291">**Browser:** Die App reagiert normal.</span><span class="sxs-lookup"><span data-stu-id="359c2-291">**Browser:** The app responds normally.</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-292">**Anwendungsprotokoll:** Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-292">**Application Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="359c2-293">Ausnahmemeldung: „HRESULT: 0x80070005“ wurde für {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="359c2-293">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="359c2-294">Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht beendet werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-294">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="359c2-295">Ausnahmemeldung: HRESULT 0x80070002 returned at {PATH}.</span><span class="sxs-lookup"><span data-stu-id="359c2-295">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="359c2-296">Das stdout-Verzeichnis in {PATH}\aspnetcorev2_inprocess.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-296">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

* <span data-ttu-id="359c2-297">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-297">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

* <span data-ttu-id="359c2-298">**Debugprotokoll des ASP.NET Core-Moduls:** Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-298">**ASP.NET Core Module debug Log:** Could not start stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="359c2-299">Ausnahmemeldung: „HRESULT: 0x80070005“ wurde für {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="359c2-299">Exception message: HRESULT 0x80070005 returned at {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84.</span></span> <span data-ttu-id="359c2-300">Das stdout-Verzeichnis in C:\Programme\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll konnte nicht beendet werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-300">Could not stop stdout redirection in C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll.</span></span> <span data-ttu-id="359c2-301">Ausnahmemeldung: HRESULT 0x80070002 returned at {PATH}.</span><span class="sxs-lookup"><span data-stu-id="359c2-301">Exception message: HRESULT 0x80070002 returned at {PATH}.</span></span> <span data-ttu-id="359c2-302">Das stdout-Verzeichnis in {PATH}\aspnetcorev2_inprocess.dll konnte nicht gestartet werden.</span><span class="sxs-lookup"><span data-stu-id="359c2-302">Could not start stdout redirection in {PATH}\aspnetcorev2_inprocess.dll.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="359c2-303">**Anwendungsprotokoll:** Warnung: Die stdout-Protokolldatei „\\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log“ konnte nicht erstellt werden. Fehlercode: -2147024893.)</span><span class="sxs-lookup"><span data-stu-id="359c2-303">**Application Log:** Warning: Could not create stdoutLogFile \\?\{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.</span></span>

* <span data-ttu-id="359c2-304">**stdout-Protokoll des ASP.NET Core-Moduls:** Es wurde keine Protokolldatei erstellt.</span><span class="sxs-lookup"><span data-stu-id="359c2-304">**ASP.NET Core Module stdout Log:** The log file isn't created.</span></span>

::: moniker-end

<span data-ttu-id="359c2-305">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-305">Troubleshooting:</span></span>

* <span data-ttu-id="359c2-306">Der im Element `<aspNetCore>` angegebene Pfad `stdoutLogFile` in der Datei *web.config* ist nicht vorhanden.</span><span class="sxs-lookup"><span data-stu-id="359c2-306">The `stdoutLogFile` path specified in the `<aspNetCore>` element of *web.config* doesn't exist.</span></span> <span data-ttu-id="359c2-307">Weitere Informationen finden Sie im Artikel [ASP.NET Core-Modul: Protokollerstellung und Weiterleitung](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span><span class="sxs-lookup"><span data-stu-id="359c2-307">For more information, see [ASP.NET Core Module: Log creation and redirection](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection).</span></span>

* <span data-ttu-id="359c2-308">Der Benutzer des App-Pools hat keinen Schreibzugriff für den Pfad des stdout-Protokolls.</span><span class="sxs-lookup"><span data-stu-id="359c2-308">The app pool user doesn't have write access to the stdout log path.</span></span>

## <a name="application-configuration-general-issue"></a><span data-ttu-id="359c2-309">Allgemeines Problem mit der Anwendungskonfiguration</span><span class="sxs-lookup"><span data-stu-id="359c2-309">Application configuration general issue</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="359c2-310">**Browser:** HTTP-Fehler 500.0: In-Process-Fehler beim Laden des Handlers für das ASP.NET Core-Modul (ANCM) **oder** HTTP-Fehler 500.30: In-Process-Startfehler beim ASP.NET Core-Modul (ANCM)</span><span class="sxs-lookup"><span data-stu-id="359c2-310">**Browser:** HTTP Error 500.0 - ANCM In-Process Handler Load Failure **--OR--** HTTP Error 500.30 - ANCM In-Process Start Failure</span></span>

* <span data-ttu-id="359c2-311">**Anwendungsprotokoll:** Variable</span><span class="sxs-lookup"><span data-stu-id="359c2-311">**Application Log:** Variable</span></span>

* <span data-ttu-id="359c2-312">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer oder wurde mit gewöhnlichen Einträgen erstellt, bis die App fehlgeschlagen ist.</span><span class="sxs-lookup"><span data-stu-id="359c2-312">**ASP.NET Core Module stdout Log:** The log file is created but empty or created with normal entries until the point of the app failing.</span></span>

* <span data-ttu-id="359c2-313">**Debugprotokoll des ASP.NET Core-Moduls:** Variable</span><span class="sxs-lookup"><span data-stu-id="359c2-313">**ASP.NET Core Module Debug Log:** Variable</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="359c2-314">**Browser:** HTTP-Fehler 502.5: Prozessfehler</span><span class="sxs-lookup"><span data-stu-id="359c2-314">**Browser:** HTTP Error 502.5 - Process Failure</span></span>

* <span data-ttu-id="359c2-315">**Anwendungsprotokoll:** Die Anwendung „MACHINE/WEBROOT/APPHOST/{ASSEMBLY}“ mit dem physischen Stamm ‚C:\{PATH}\' hat den Prozess mit der Befehlszeile „C:\{PATH}\{ASSEMBLY}.{exe|dll}“ erstellt, ist aber abgestürzt, reagiert nicht oder lauscht dem angegebenen Port „{PORT}“ nicht. Fehlercode: {ERROR CODE}.)</span><span class="sxs-lookup"><span data-stu-id="359c2-315">**Application Log:** Application 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' with physical root 'C:\{PATH}\' created process with commandline '"C:\{PATH}\{ASSEMBLY}.{exe|dll}" ' but either crashed or did not respond or did not listen on the given port '{PORT}', ErrorCode = '{ERROR CODE}'</span></span>

* <span data-ttu-id="359c2-316">**stdout-Protokoll des ASP.NET Core-Moduls:** Die erstellte Protokolldatei ist leer.</span><span class="sxs-lookup"><span data-stu-id="359c2-316">**ASP.NET Core Module stdout Log:** The log file is created but empty.</span></span>

::: moniker-end

<span data-ttu-id="359c2-317">Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="359c2-317">Troubleshooting:</span></span>

<span data-ttu-id="359c2-318">Der Prozess konnte nicht gestartet werden, weil wahrscheinlich ein Problem mit der Konfiguration oder Programmierung der App vorliegt.</span><span class="sxs-lookup"><span data-stu-id="359c2-318">The process failed to start, most likely due to an app configuration or programming issue.</span></span>

<span data-ttu-id="359c2-319">Weitere Informationen finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="359c2-319">For more information, see the following topics:</span></span>

* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:test/troubleshoot>
