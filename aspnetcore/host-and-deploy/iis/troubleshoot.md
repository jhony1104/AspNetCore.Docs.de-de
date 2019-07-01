---
title: Problembehandlung bei ASP.NET Core in IIS
author: guardrex
description: Erfahren Sie, wie Sie Probleme mit IIS-Bereitstellungen (IIS = Internet Information Services) von ASP.NET Core-Apps diagnostizieren können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313660"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a><span data-ttu-id="6b3f8-103">Problembehandlung bei ASP.NET Core in IIS</span><span class="sxs-lookup"><span data-stu-id="6b3f8-103">Troubleshoot ASP.NET Core on IIS</span></span>

<span data-ttu-id="6b3f8-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="6b3f8-104">By [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6b3f8-105">Dieser Artikel enthält Anweisungen zum Diagnostizieren eines Startproblems der ASP.NET Core-App beim Hosten mithilfe von [Internet Information Services (IIS)](/iis).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-105">This article provides instructions on how to diagnose an ASP.NET Core app startup issue when hosting with [Internet Information Services (IIS)](/iis).</span></span> <span data-ttu-id="6b3f8-106">Die in diesem Artikel enthaltenen Informationen gelten für das Hosting in IIS unter Windows Server und Windows Desktop.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-106">The information in this article applies to hosting in IIS on Windows Server and Windows Desktop.</span></span>

<span data-ttu-id="6b3f8-107">Weitere Themen zur Problembehandlung:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-107">Additional troubleshooting topics:</span></span>

* <span data-ttu-id="6b3f8-108">Azure App Service verwendet auch das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) und IIS zum Hosten von Apps.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-108">Azure App Service also uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and IIS to host apps.</span></span> <span data-ttu-id="6b3f8-109">Spezielle Empfehlungen zur Problembehandlung von Azure App Service finden Sie unter <xref:host-and-deploy/azure-apps/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-109">For troubleshooting advice that pertains specifically to Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>
* <span data-ttu-id="6b3f8-110">In <xref:fundamentals/error-handling> erläutert, wie Sie während der Entwicklungsphase auf einem lokalen System Fehler in ASP.NET Core-Apps behandeln.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-110"><xref:fundamentals/error-handling> covers how to handle errors in ASP.NET Core apps during development on a local system.</span></span>
* <span data-ttu-id="6b3f8-111">[Debuggen mit Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger): In diesem Artikel werden die Features des Visual Studio-Debuggers vorgestellt.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-111">[Learn to debug using Visual Studio](/visualstudio/debugger/getting-started-with-the-debugger) introduces the features of the Visual Studio debugger.</span></span>
* <span data-ttu-id="6b3f8-112">[Debuggen mit Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging): In diesem Artikel wird beschrieben, wie Sie mit dem in Visual Studio Code integrierten Debugger arbeiten.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-112">[Debugging with Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging) describes the debugging support built into Visual Studio Code.</span></span>

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a><span data-ttu-id="6b3f8-113">Problembehandlung bei App-Startfehlern</span><span class="sxs-lookup"><span data-stu-id="6b3f8-113">Troubleshoot app startup errors</span></span>

### <a name="enable-the-aspnet-core-module-debug-log"></a><span data-ttu-id="6b3f8-114">Aktivieren des Debugprotokolls des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="6b3f8-114">Enable the ASP.NET Core Module debug log</span></span>

<span data-ttu-id="6b3f8-115">Fügen Sie der *web.config*-Datei der App die folgenden Handlereinstellungen hinzu, um Debugprotokolle des ASP.NET Core-Moduls zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-115">Add the following handler settings to the app's *web.config* file to enable ASP.NET Core Module debug logs:</span></span>

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="6b3f8-116">Überprüfen Sie, ob der für das Protokoll angegebene Pfad vorhanden ist, und ob die Identität des Anwendungspools Schreibberechtigungen für den Speicherort hat.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-116">Confirm that the path specified for the log exists and that the app pool's identity has write permissions to the location.</span></span>

### <a name="application-event-log"></a><span data-ttu-id="6b3f8-117">Anwendungsereignisprotokoll</span><span class="sxs-lookup"><span data-stu-id="6b3f8-117">Application Event Log</span></span>

<span data-ttu-id="6b3f8-118">Greifen Sie auf das Anwendungsereignisprotokoll zu:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-118">Access the Application Event Log:</span></span>

1. <span data-ttu-id="6b3f8-119">Öffnen Sie das Menü „Start“, suchen Sie nach der **Ereignisanzeige**, und wählen Sie anschließend die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-119">Open the Start menu, search for **Event Viewer**, and then select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="6b3f8-120">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-120">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="6b3f8-121">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-121">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="6b3f8-122">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-122">Search for errors associated with the failing app.</span></span> <span data-ttu-id="6b3f8-123">Fehler weisen in der Spalte *Quelle* den Wert *IIS AspNetCore-Modul* oder *IIS Express AspNetCore-Modul* auf.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-123">Errors have a value of *IIS AspNetCore Module* or *IIS Express AspNetCore Module* in the *Source* column.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="6b3f8-124">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="6b3f8-124">Run the app at a command prompt</span></span>

<span data-ttu-id="6b3f8-125">Viele Startfehler erzeugen keine nützlichen Informationen im Anwendungsereignisprotokoll.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-125">Many startup errors don't produce useful information in the Application Event Log.</span></span> <span data-ttu-id="6b3f8-126">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-126">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span>

#### <a name="framework-dependent-deployment"></a><span data-ttu-id="6b3f8-127">Framework-abhängige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="6b3f8-127">Framework-dependent deployment</span></span>

<span data-ttu-id="6b3f8-128">Wenn es sich bei der App um eine [Framework-abhängige Bereitstellung handelt](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span><span class="sxs-lookup"><span data-stu-id="6b3f8-128">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd):</span></span>

1. <span data-ttu-id="6b3f8-129">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die App aus, indem Sie die Assembly der App mit *dotnet.exe* ausführen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-129">At a command prompt, navigate to the deployment folder and run the app by executing the app's assembly with *dotnet.exe*.</span></span> <span data-ttu-id="6b3f8-130">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-130">In the following command, substitute the name of the app's assembly for \<assembly_name>: `dotnet .\<assembly_name>.dll`.</span></span>
1. <span data-ttu-id="6b3f8-131">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-131">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="6b3f8-132">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-132">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="6b3f8-133">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-133">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="6b3f8-134">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-134">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

#### <a name="self-contained-deployment"></a><span data-ttu-id="6b3f8-135">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="6b3f8-135">Self-contained deployment</span></span>

<span data-ttu-id="6b3f8-136">Wenn es sich bei der App um eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) handelt:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-136">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd):</span></span>

1. <span data-ttu-id="6b3f8-137">Navigieren Sie in einer Eingabeaufforderung zum Bereitstellungsordner und führen Sie die ausführbaren Dateien der App aus.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-137">At a command prompt, navigate to the deployment folder and run the app's executable.</span></span> <span data-ttu-id="6b3f8-138">Ersetzen Sie in folgendem Befehl den Namen der Assembly der App durch \<assembly_name>: `<assembly_name>.exe`.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-138">In the following command, substitute the name of the app's assembly for \<assembly_name>: `<assembly_name>.exe`.</span></span>
1. <span data-ttu-id="6b3f8-139">Die Konsolenausgabe der App, die Fehler anzeigt, wird in das Konsolenfenster geschrieben.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-139">The console output from the app, showing any errors, is written to the console window.</span></span>
1. <span data-ttu-id="6b3f8-140">Wenn der Fehler während einer Anforderung an die App auftritt, führen Sie eine Anforderung an den Host und Port aus, auf dem Kestrel empfangsbereit ist.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-140">If the errors occur when making a request to the app, make a request to the host and port where Kestrel listens.</span></span> <span data-ttu-id="6b3f8-141">Führen Sie unter Verwendung der Standardhosts und -ports eine Anforderung für `http://localhost:5000/` aus.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-141">Using the default host and post, make a request to `http://localhost:5000/`.</span></span> <span data-ttu-id="6b3f8-142">Wenn die App normalerweise auf die Kestrel-Endpunktadresse reagiert, ist die Problemursache wahrscheinlich die Hostingkonfiguration (anstelle der App).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-142">If the app responds normally at the Kestrel endpoint address, the problem is more likely related to the hosting configuration and less likely within the app.</span></span>

### <a name="aspnet-core-module-stdout-log"></a><span data-ttu-id="6b3f8-143">stdout-Protokoll des ASP.NET Core-Moduls</span><span class="sxs-lookup"><span data-stu-id="6b3f8-143">ASP.NET Core Module stdout log</span></span>

<span data-ttu-id="6b3f8-144">So aktivieren Sie stdout-Protokolle und zeigen diese an:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-144">To enable and view stdout logs:</span></span>

1. <span data-ttu-id="6b3f8-145">Navigieren Sie zum Bereitstellungsordner der Website auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-145">Navigate to the site's deployment folder on the hosting system.</span></span>
1. <span data-ttu-id="6b3f8-146">Erstellen Sie den Ordner *logs*, wenn dieser nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-146">If the *logs* folder isn't present, create the folder.</span></span> <span data-ttu-id="6b3f8-147">Anweisungen zum Aktivieren von MSBuild für die automatische Erstellung des Ordners *logs* in der Bereitstellung finden Sie im Thema [Verzeichnisstruktur](xref:host-and-deploy/directory-structure).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-147">For instructions on how to enable MSBuild to create the *logs* folder in the deployment automatically, see the [Directory structure](xref:host-and-deploy/directory-structure) topic.</span></span>
1. <span data-ttu-id="6b3f8-148">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-148">Edit the *web.config* file.</span></span> <span data-ttu-id="6b3f8-149">Legen Sie **stdoutLogEnabled** auf `true` fest, und ändern Sie den Pfad von **stdoutLogFile** so, dass auf den Ordner *logs* verwiesen wird (Beispiel: `.\logs\stdout`).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-149">Set **stdoutLogEnabled** to `true` and change the **stdoutLogFile** path to point to the *logs* folder (for example, `.\logs\stdout`).</span></span> <span data-ttu-id="6b3f8-150">`stdout` im Pfad ist das Präfix des Protokolldateinamens.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-150">`stdout` in the path is the log file name prefix.</span></span> <span data-ttu-id="6b3f8-151">Ein Zeitstempel, eine Prozess-ID und eine Dateierweiterung werden bei der Protokollerstellung automatisch hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-151">A timestamp, process id, and file extension are added automatically when the log is created.</span></span> <span data-ttu-id="6b3f8-152">Mit `stdout` als Präfix des Dateinamens wird eine typische Protokolldatei als *stdout_20180205184032_5412.log* benannt.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-152">Using `stdout` as the file name prefix, a typical log file is named *stdout_20180205184032_5412.log*.</span></span>
1. <span data-ttu-id="6b3f8-153">Stellen Sie sicher, dass die Identität des Anwendungspools über Schreibberechtigungen für den Ordner *Protokolle* verfügt.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-153">Ensure your application pool's identity has write permissions to the *logs* folder.</span></span>
1. <span data-ttu-id="6b3f8-154">Speichern Sie die aktualisierte Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-154">Save the updated *web.config* file.</span></span>
1. <span data-ttu-id="6b3f8-155">Führen Sie eine Anforderung an die App aus.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-155">Make a request to the app.</span></span>
1. <span data-ttu-id="6b3f8-156">Navigieren Sie zu dem Ordner *logs*.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-156">Navigate to the *logs* folder.</span></span> <span data-ttu-id="6b3f8-157">Suchen und öffnen Sie das aktuelle stdout-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-157">Find and open the most recent stdout log.</span></span>
1. <span data-ttu-id="6b3f8-158">Untersuchen Sie das Protokoll auf Fehler.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-158">Study the log for errors.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6b3f8-159">Deaktivieren Sie die stdout-Protokollierung, wenn die Problembehandlung abgeschlossen ist.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-159">Disable stdout logging when troubleshooting is complete.</span></span>

1. <span data-ttu-id="6b3f8-160">Bearbeiten Sie die Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-160">Edit the *web.config* file.</span></span>
1. <span data-ttu-id="6b3f8-161">Legen Sie **stdoutLogEnabled** auf `false` fest.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-161">Set **stdoutLogEnabled** to `false`.</span></span>
1. <span data-ttu-id="6b3f8-162">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-162">Save the file.</span></span>

> [!WARNING]
> <span data-ttu-id="6b3f8-163">Wenn das stdout-Protokoll nicht deaktiviert wird, können App- oder Serverfehler auftreten.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-163">Failure to disable the stdout log can lead to app or server failure.</span></span> <span data-ttu-id="6b3f8-164">Für die Protokollgröße oder die Anzahl von erstellten Protokolldateien ist kein Grenzwert festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-164">There's no limit on log file size or the number of log files created.</span></span>
>
> <span data-ttu-id="6b3f8-165">Verwenden Sie für die routinemäßige Protokollierung in einer ASP.NET Core-App eine Protokollierungsbibliothek, die die Protokolldateigröße beschränkt und die Protokolle rotiert.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-165">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="6b3f8-166">Weitere Informationen finden Sie im Artikel zur [Protokollierung von Drittanbietern](xref:fundamentals/logging/index#third-party-logging-providers).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-166">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

## <a name="enable-the-developer-exception-page"></a><span data-ttu-id="6b3f8-167">Aktivieren der Seite mit Ausnahmen für Entwickler</span><span class="sxs-lookup"><span data-stu-id="6b3f8-167">Enable the Developer Exception Page</span></span>

<span data-ttu-id="6b3f8-168">Die `ASPNETCORE_ENVIRONMENT` [Umgebungsvariable kann zur Datei web.config hinzugefügt werden](xref:host-and-deploy/aspnet-core-module#setting-environment-variables), um die App in der Entwicklungsumgebung auszuführen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-168">The `ASPNETCORE_ENVIRONMENT` [environment variable can be added to web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) to run the app in the Development environment.</span></span> <span data-ttu-id="6b3f8-169">Solange die Umgebung nicht beim Starten der App von `UseEnvironment` im Host-Builder außer Kraft gesetzt wird, kann die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling) durch Festlegen der Umgebungsvariable angezeigt werden, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-169">As long as the environment isn't overridden in app startup by `UseEnvironment` on the host builder, setting the environment variable allows the [Developer Exception Page](xref:fundamentals/error-handling) to appear when the app is run.</span></span>

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

<span data-ttu-id="6b3f8-170">Das Festlegen der Umgebungsvariablen für `ASPNETCORE_ENVIRONMENT` wird nur bei der Verwendung von Staging- und Testservern empfohlen, die nicht für das Internet verfügbar gemacht werden.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-170">Setting the environment variable for `ASPNETCORE_ENVIRONMENT` is only recommended for use on staging and testing servers that aren't exposed to the Internet.</span></span> <span data-ttu-id="6b3f8-171">Entfernen Sie nach der Fehlerbehebung die Umgebungsvariable aus der Datei *web.config*.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-171">Remove the environment variable from the *web.config* file after troubleshooting.</span></span> <span data-ttu-id="6b3f8-172">Informationen zum Festlegen von Umgebungsvariablen in der Datei *web.config* finden Sie unter [Untergeordnetes environmentVariables-Element von aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-172">For information on setting environment variables in *web.config*, see [environmentVariables child element of aspNetCore](xref:host-and-deploy/aspnet-core-module#setting-environment-variables).</span></span>

## <a name="common-startup-errors"></a><span data-ttu-id="6b3f8-173">Häufige Startfehler</span><span class="sxs-lookup"><span data-stu-id="6b3f8-173">Common startup errors</span></span>

<span data-ttu-id="6b3f8-174">Siehe <xref:host-and-deploy/azure-iis-errors-reference>.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-174">See <xref:host-and-deploy/azure-iis-errors-reference>.</span></span> <span data-ttu-id="6b3f8-175">Die meisten der häufig auftretenden Probleme, die den Start von Apps verhindern, werden im Referenzartikel behandelt.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-175">Most of the common problems that prevent app startup are covered in the reference topic.</span></span>

## <a name="obtain-data-from-an-app"></a><span data-ttu-id="6b3f8-176">Abrufen von Daten aus einer App</span><span class="sxs-lookup"><span data-stu-id="6b3f8-176">Obtain data from an app</span></span>

<span data-ttu-id="6b3f8-177">Wenn eine App in der Lage sind, auf Anforderungen zu reagieren, erhalten Sie Anforderungen, Verbindungen und zusätzliche Daten von den Apps über die Inlinemiddleware des Terminals.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-177">If an app is capable of responding to requests, obtain request, connection, and additional data from the app using terminal inline middleware.</span></span> <span data-ttu-id="6b3f8-178">Weitere Informationen und Beispielcode finden Sie unter <xref:test/troubleshoot#obtain-data-from-an-app>.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-178">For more information and sample code, see <xref:test/troubleshoot#obtain-data-from-an-app>.</span></span>

## <a name="create-a-dump"></a><span data-ttu-id="6b3f8-179">Erstellen eines Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="6b3f8-179">Create a dump</span></span>

<span data-ttu-id="6b3f8-180">Ein *Speicherabbild* ist eine Momentaufnahme des Systemarbeitsspeichers. Ein Speicherabbild kann hilfreich sein, um die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-180">A *dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="6b3f8-181">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="6b3f8-181">App crashes or encounters an exception</span></span>

<span data-ttu-id="6b3f8-182">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="6b3f8-182">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="6b3f8-183">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-183">Create a folder to hold crash dump files at `c:\dumps`.</span></span> <span data-ttu-id="6b3f8-184">Der App-Pool muss über Schreibzugriff auf den Ordner verfügen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-184">The app pool must have write access to the folder.</span></span>
1. <span data-ttu-id="6b3f8-185">Führen Sie das [PowerShell-Skript „EnableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-185">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1):</span></span>
   * <span data-ttu-id="6b3f8-186">Wenn die App das [In-Process-Hostingmodell](xref:host-and-deploy/iis/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-186">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * <span data-ttu-id="6b3f8-187">Wenn die App das [Out-of-Process-Hostingmodell](xref:host-and-deploy/iis/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-187">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. <span data-ttu-id="6b3f8-188">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-188">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="6b3f8-189">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-189">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1):</span></span>
   * <span data-ttu-id="6b3f8-190">Wenn die App das [In-Process-Hostingmodell](xref:host-and-deploy/iis/index#in-process-hosting-model) verwendet, führen Sie das Skript für *w3wp.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-190">If the app uses the [in-process hosting model](xref:host-and-deploy/iis/index#in-process-hosting-model), run the script for *w3wp.exe*:</span></span>

     ```console
     .\DisableDumps w3wp.exe
     ```

   * <span data-ttu-id="6b3f8-191">Wenn die App das [Out-of-Process-Hostingmodell](xref:host-and-deploy/iis/index#out-of-process-hosting-model) verwendet, führen Sie das Skript für *dotnet.exe* aus:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-191">If the app uses the [out-of-process hosting model](xref:host-and-deploy/iis/index#out-of-process-hosting-model), run the script for *dotnet.exe*:</span></span>

     ```console
     .\DisableDumps dotnet.exe
     ```

<span data-ttu-id="6b3f8-192">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-192">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="6b3f8-193">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-193">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="6b3f8-194">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-194">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="6b3f8-195">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="6b3f8-195">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="6b3f8-196">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-196">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

### <a name="analyze-the-dump"></a><span data-ttu-id="6b3f8-197">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="6b3f8-197">Analyze the dump</span></span>

<span data-ttu-id="6b3f8-198">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-198">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="6b3f8-199">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-199">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="remote-debugging"></a><span data-ttu-id="6b3f8-200">Remotedebuggen</span><span class="sxs-lookup"><span data-stu-id="6b3f8-200">Remote debugging</span></span>

<span data-ttu-id="6b3f8-201">Siehe [Remotedebuggen von ASP.NET Core auf einem IIS-Remotecomputer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in der Dokumentation zu Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-201">See [Remote Debug ASP.NET Core on a Remote IIS Computer in Visual Studio 2017](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer) in the Visual Studio documentation.</span></span>

## <a name="application-insights"></a><span data-ttu-id="6b3f8-202">Application Insights</span><span class="sxs-lookup"><span data-stu-id="6b3f8-202">Application Insights</span></span>

<span data-ttu-id="6b3f8-203">[Application Insights](/azure/application-insights/) stellt Telemetriedaten von Apps bereit, die von IIS gehostet werden, einschließlich Features für die Fehlerprotokollierung und die Berichterstellung.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-203">[Application Insights](/azure/application-insights/) provides telemetry from apps hosted by IIS, including error logging and reporting features.</span></span> <span data-ttu-id="6b3f8-204">Application Insights kann nur Fehler melden, die nach dem Start der App auftreten, wenn die Protokollierungsfeatures der App verfügbar werden.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-204">Application Insights can only report on errors that occur after the app starts when the app's logging features become available.</span></span> <span data-ttu-id="6b3f8-205">Weitere Informationen finden Sie unter [Application Insights for ASP.NET Core (Application Insights für ASP.NET Core)](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="6b3f8-205">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="additional-advice"></a><span data-ttu-id="6b3f8-206">Zusätzliche Hinweise</span><span class="sxs-lookup"><span data-stu-id="6b3f8-206">Additional advice</span></span>

<span data-ttu-id="6b3f8-207">Manchmal schlägt eine funktionsfähige App direkt nach der Durchführung eines Upgrades des .NET Core SDK auf dem Entwicklungscomputer oder der Paketversionen in der App fehl.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-207">Sometimes a functioning app fails immediately after upgrading either the .NET Core SDK on the development machine or package versions within the app.</span></span> <span data-ttu-id="6b3f8-208">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-208">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="6b3f8-209">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="6b3f8-209">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="6b3f8-210">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-210">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="6b3f8-211">Löschen Sie die Paketcaches unter *%UserProfile%\\.nuget\\packages* und *%LocalAppData%\\Nuget\\v3-cache*.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-211">Clear the package caches at *%UserProfile%\\.nuget\\packages* and *%LocalAppData%\\Nuget\\v3-cache*.</span></span>
1. <span data-ttu-id="6b3f8-212">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-212">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="6b3f8-213">Überprüfen Sie, ob die vorherige Bereitstellung auf dem Server vollständig gelöscht wurde, bevor Sie die App erneut bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-213">Confirm that the prior deployment on the server has been completely deleted prior to redeploying the app.</span></span>

> [!TIP]
> <span data-ttu-id="6b3f8-214">Sie können Paketcaches ganz einfach löschen, indem Sie `dotnet nuget locals all --clear` über eine Eingabeaufforderung ausführen.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-214">A convenient way to clear package caches is to execute `dotnet nuget locals all --clear` from a command prompt.</span></span>
>
> <span data-ttu-id="6b3f8-215">Darüber hinaus können Paketcaches mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und durch Ausführung des Befehls `nuget locals all -clear` gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-215">Clearing package caches can also be accomplished by using the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="6b3f8-216">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="6b3f8-216">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6b3f8-217">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6b3f8-217">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
