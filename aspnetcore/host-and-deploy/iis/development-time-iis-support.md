---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 06c215156cdfa9bc1ae6ac2eb21a3f739a309bed
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106779"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="81384-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81384-103">'Identity'</span></span>

<span data-ttu-id="81384-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81384-104">'Let's Encrypt'</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="81384-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81384-105">'Razor'</span></span> <span data-ttu-id="81384-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="81384-106">'SignalR' uid:</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81384-107">IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="81384-107">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

* <span data-ttu-id="81384-108">Von [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="81384-108">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>
* <span data-ttu-id="81384-109">In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="81384-109">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span>
* <span data-ttu-id="81384-110">Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="81384-110">This topic walks through enabling this scenario and setting up a project.</span></span>
* <span data-ttu-id="81384-111">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="81384-111">Prerequisites</span></span>

## <a name="enable-iis"></a>[<span data-ttu-id="81384-112">Visual Studio für Windows</span><span class="sxs-lookup"><span data-stu-id="81384-112">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)

1. <span data-ttu-id="81384-113">Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="81384-113">**ASP.NET and web development** workload</span></span>
1. <span data-ttu-id="81384-114">Workload **Plattformübergreifende .NET Core-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="81384-114">**.NET Core cross-platform development** workload</span></span> <span data-ttu-id="81384-115">X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)</span><span class="sxs-lookup"><span data-stu-id="81384-115">X.509 security certificate (for HTTPS support)</span></span>

<span data-ttu-id="81384-116">Aktivieren von IIS</span><span class="sxs-lookup"><span data-stu-id="81384-116">Enable IIS</span></span>

## <a name="configure-iis"></a><span data-ttu-id="81384-117">Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="81384-117">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

<span data-ttu-id="81384-118">Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.</span><span class="sxs-lookup"><span data-stu-id="81384-118">Select the **Internet Information Services** check box.</span></span>

* <span data-ttu-id="81384-119">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="81384-119">Select **OK**.</span></span> <span data-ttu-id="81384-120">Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.</span><span class="sxs-lookup"><span data-stu-id="81384-120">The IIS installation may require a system restart.</span></span>
* <span data-ttu-id="81384-121">Konfigurieren von IIS</span><span class="sxs-lookup"><span data-stu-id="81384-121">Configure IIS</span></span>
  * <span data-ttu-id="81384-122">Für IIS muss eine Website mit Folgendem konfiguriert sein:</span><span class="sxs-lookup"><span data-stu-id="81384-122">IIS must have a website configured with the following:</span></span> <span data-ttu-id="81384-123">**Hostname:** In der Regel wird die **Standardwebsite** mit dem **Hostnamen** `localhost` verwendet.</span><span class="sxs-lookup"><span data-stu-id="81384-123">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span>
  * <span data-ttu-id="81384-124">Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.</span><span class="sxs-lookup"><span data-stu-id="81384-124">However, any valid IIS website with a unique host name works.</span></span>
  * <span data-ttu-id="81384-125">**Sitebindung**</span><span class="sxs-lookup"><span data-stu-id="81384-125">**Site Binding**</span></span> <span data-ttu-id="81384-126">Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443.</span><span class="sxs-lookup"><span data-stu-id="81384-126">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="81384-127">In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="81384-127">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>

1. <span data-ttu-id="81384-128">Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.</span><span class="sxs-lookup"><span data-stu-id="81384-128">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
1. <span data-ttu-id="81384-129">Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="81384-129">Use a single binding for either HTTP or HTTPS.</span></span>
1. <span data-ttu-id="81384-130">**Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**</span><span class="sxs-lookup"><span data-stu-id="81384-130">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

   <span data-ttu-id="81384-131">Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit</span><span class="sxs-lookup"><span data-stu-id="81384-131">Enable development-time IIS support in Visual Studio</span></span> <span data-ttu-id="81384-132">Starten Sie den Visual Studio-Installer.</span><span class="sxs-lookup"><span data-stu-id="81384-132">Launch the Visual Studio installer.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="81384-133">Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="81384-133">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>

### <a name="https-redirection"></a><span data-ttu-id="81384-134">Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="81384-134">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

<span data-ttu-id="81384-135">Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads.</span><span class="sxs-lookup"><span data-stu-id="81384-135">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="81384-136">Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="81384-136">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

<span data-ttu-id="81384-137">Konfigurieren des Projekts</span><span class="sxs-lookup"><span data-stu-id="81384-137">Configure the project</span></span> <span data-ttu-id="81384-138">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="81384-138">HTTPS redirection</span></span>

<span data-ttu-id="81384-139">Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren).</span><span class="sxs-lookup"><span data-stu-id="81384-139">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="81384-140">Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="81384-140">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="81384-141">Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="81384-141">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span>

<span data-ttu-id="81384-142">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="81384-142">For more information, see <xref:security/enforcing-ssl>.</span></span>

1. <span data-ttu-id="81384-143">Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="81384-143">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="81384-144">Es ist keine App-Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="81384-144">No app configuration is required.</span></span> <span data-ttu-id="81384-145">IIS-Startprofil</span><span class="sxs-lookup"><span data-stu-id="81384-145">IIS launch profile</span></span>
1. <span data-ttu-id="81384-146">So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="81384-146">Create a new launch profile to add development-time IIS support:</span></span> <span data-ttu-id="81384-147">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="81384-147">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="81384-148">Klicken Sie auf **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="81384-148">Select **Properties**.</span></span>
1. <span data-ttu-id="81384-149">Öffnen Sie die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="81384-149">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="81384-150">Klicken Sie für **Profil** auf die Schaltfläche **Neu**.</span><span class="sxs-lookup"><span data-stu-id="81384-150">For **Profile**, select the **New** button.</span></span>

   <span data-ttu-id="81384-151">Geben Sie dem Profil im Popupfenster den Namen „IIS“.</span><span class="sxs-lookup"><span data-stu-id="81384-151">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="81384-152">Klicken Sie auf **OK**, um das Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="81384-152">Select **OK** to create the profile.</span></span>

   <span data-ttu-id="81384-153">Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="81384-153">For the **Launch** setting, select **IIS** from the list.</span></span>

   <span data-ttu-id="81384-154">Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.</span><span class="sxs-lookup"><span data-stu-id="81384-154">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="81384-155">Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).</span><span class="sxs-lookup"><span data-stu-id="81384-155">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span>
1. <span data-ttu-id="81384-156">Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).</span><span class="sxs-lookup"><span data-stu-id="81384-156">For HTTP, use an HTTP (`http://`) endpoint.</span></span> <span data-ttu-id="81384-157">Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.</span><span class="sxs-lookup"><span data-stu-id="81384-157">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>
1. <span data-ttu-id="81384-158">Geben Sie den Namen der App am Ende der URL an.</span><span class="sxs-lookup"><span data-stu-id="81384-158">Provide the name of the app at the end of the URL.</span></span>
1. <span data-ttu-id="81384-159">`https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.</span><span class="sxs-lookup"><span data-stu-id="81384-159">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span> <span data-ttu-id="81384-160">Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="81384-160">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="81384-161">Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an.</span><span class="sxs-lookup"><span data-stu-id="81384-161">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span> <span data-ttu-id="81384-162">Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="81384-162">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="81384-163">Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="81384-163">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span>

<span data-ttu-id="81384-164">Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="81384-164">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="81384-165">Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (In-Process) verwendet.</span><span class="sxs-lookup"><span data-stu-id="81384-165">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="81384-166">Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.</span><span class="sxs-lookup"><span data-stu-id="81384-166">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="81384-167">Speichern Sie den Profilnamen.</span><span class="sxs-lookup"><span data-stu-id="81384-167">Save the profile.</span></span>

<span data-ttu-id="81384-168">Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="81384-168">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span>

* <span data-ttu-id="81384-169">Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="81384-169">The following example configures the profile to use the HTTPS protocol:</span></span>
* <span data-ttu-id="81384-170">Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="81384-170">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

<span data-ttu-id="81384-171">Ausführen des Projekts</span><span class="sxs-lookup"><span data-stu-id="81384-171">Run the project</span></span> <span data-ttu-id="81384-172">Führen Sie Visual Studio als Administrator aus:</span><span class="sxs-lookup"><span data-stu-id="81384-172">Run Visual Studio as an administrator:</span></span>

<span data-ttu-id="81384-173">Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="81384-173">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>

> [!NOTE]
> <span data-ttu-id="81384-174">Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus.</span><span class="sxs-lookup"><span data-stu-id="81384-174">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span> <span data-ttu-id="81384-175">Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="81384-175">Visual Studio may prompt a restart if not running as an administrator.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81384-176">Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="81384-176">If prompted, restart Visual Studio.</span></span>

* <span data-ttu-id="81384-177">Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.</span><span class="sxs-lookup"><span data-stu-id="81384-177">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="81384-178">Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung.</span><span class="sxs-lookup"><span data-stu-id="81384-178">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="81384-179">Beispielsweise werden Haltepunkte nicht erreicht.</span><span class="sxs-lookup"><span data-stu-id="81384-179">For example, break points aren't hit.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81384-180">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="81384-180">Additional resources</span></span>

* [<span data-ttu-id="81384-181">Erste Schritte mit IIS-Manager in IIS</span><span class="sxs-lookup"><span data-stu-id="81384-181">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <span data-ttu-id="81384-182">In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="81384-182">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span>
* <span data-ttu-id="81384-183">Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="81384-183">This topic walks through enabling this scenario and setting up a project.</span></span>
* <span data-ttu-id="81384-184">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="81384-184">Prerequisites</span></span>

## <a name="enable-iis"></a>[<span data-ttu-id="81384-185">Visual Studio für Windows</span><span class="sxs-lookup"><span data-stu-id="81384-185">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)

1. <span data-ttu-id="81384-186">Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="81384-186">**ASP.NET and web development** workload</span></span>
1. <span data-ttu-id="81384-187">Workload **Plattformübergreifende .NET Core-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="81384-187">**.NET Core cross-platform development** workload</span></span> <span data-ttu-id="81384-188">X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)</span><span class="sxs-lookup"><span data-stu-id="81384-188">X.509 security certificate (for HTTPS support)</span></span>

<span data-ttu-id="81384-189">Aktivieren von IIS</span><span class="sxs-lookup"><span data-stu-id="81384-189">Enable IIS</span></span>

## <a name="configure-iis"></a><span data-ttu-id="81384-190">Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="81384-190">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>

<span data-ttu-id="81384-191">Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.</span><span class="sxs-lookup"><span data-stu-id="81384-191">Select the **Internet Information Services** check box.</span></span>

* <span data-ttu-id="81384-192">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="81384-192">Select **OK**.</span></span> <span data-ttu-id="81384-193">Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.</span><span class="sxs-lookup"><span data-stu-id="81384-193">The IIS installation may require a system restart.</span></span>
* <span data-ttu-id="81384-194">Konfigurieren von IIS</span><span class="sxs-lookup"><span data-stu-id="81384-194">Configure IIS</span></span>
  * <span data-ttu-id="81384-195">Für IIS muss eine Website mit Folgendem konfiguriert sein:</span><span class="sxs-lookup"><span data-stu-id="81384-195">IIS must have a website configured with the following:</span></span> <span data-ttu-id="81384-196">**Hostname:** In der Regel wird die **Standardwebsite** mit dem **Hostnamen** `localhost` verwendet.</span><span class="sxs-lookup"><span data-stu-id="81384-196">**Host name**: Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span>
  * <span data-ttu-id="81384-197">Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.</span><span class="sxs-lookup"><span data-stu-id="81384-197">However, any valid IIS website with a unique host name works.</span></span>
  * <span data-ttu-id="81384-198">**Sitebindung**</span><span class="sxs-lookup"><span data-stu-id="81384-198">**Site Binding**</span></span> <span data-ttu-id="81384-199">Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443.</span><span class="sxs-lookup"><span data-stu-id="81384-199">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="81384-200">In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="81384-200">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>

1. <span data-ttu-id="81384-201">Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.</span><span class="sxs-lookup"><span data-stu-id="81384-201">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
1. <span data-ttu-id="81384-202">Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="81384-202">Use a single binding for either HTTP or HTTPS.</span></span>
1. <span data-ttu-id="81384-203">**Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**</span><span class="sxs-lookup"><span data-stu-id="81384-203">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

   <span data-ttu-id="81384-204">Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit</span><span class="sxs-lookup"><span data-stu-id="81384-204">Enable development-time IIS support in Visual Studio</span></span> <span data-ttu-id="81384-205">Starten Sie den Visual Studio-Installer.</span><span class="sxs-lookup"><span data-stu-id="81384-205">Launch the Visual Studio installer.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="81384-206">Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="81384-206">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>

### <a name="https-redirection"></a><span data-ttu-id="81384-207">Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="81384-207">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

<span data-ttu-id="81384-208">Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads.</span><span class="sxs-lookup"><span data-stu-id="81384-208">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="81384-209">Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="81384-209">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

<span data-ttu-id="81384-210">Konfigurieren des Projekts</span><span class="sxs-lookup"><span data-stu-id="81384-210">Configure the project</span></span> <span data-ttu-id="81384-211">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="81384-211">HTTPS redirection</span></span>

<span data-ttu-id="81384-212">Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren).</span><span class="sxs-lookup"><span data-stu-id="81384-212">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="81384-213">Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="81384-213">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="81384-214">Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="81384-214">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span>

<span data-ttu-id="81384-215">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="81384-215">For more information, see <xref:security/enforcing-ssl>.</span></span>

1. <span data-ttu-id="81384-216">Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="81384-216">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="81384-217">Es ist keine App-Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="81384-217">No app configuration is required.</span></span> <span data-ttu-id="81384-218">IIS-Startprofil</span><span class="sxs-lookup"><span data-stu-id="81384-218">IIS launch profile</span></span>
1. <span data-ttu-id="81384-219">So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="81384-219">Create a new launch profile to add development-time IIS support:</span></span> <span data-ttu-id="81384-220">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="81384-220">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="81384-221">Klicken Sie auf **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="81384-221">Select **Properties**.</span></span>
1. <span data-ttu-id="81384-222">Öffnen Sie die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="81384-222">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="81384-223">Klicken Sie für **Profil** auf die Schaltfläche **Neu**.</span><span class="sxs-lookup"><span data-stu-id="81384-223">For **Profile**, select the **New** button.</span></span>

   <span data-ttu-id="81384-224">Geben Sie dem Profil im Popupfenster den Namen „IIS“.</span><span class="sxs-lookup"><span data-stu-id="81384-224">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="81384-225">Klicken Sie auf **OK**, um das Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="81384-225">Select **OK** to create the profile.</span></span>

   <span data-ttu-id="81384-226">Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="81384-226">For the **Launch** setting, select **IIS** from the list.</span></span>

   <span data-ttu-id="81384-227">Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.</span><span class="sxs-lookup"><span data-stu-id="81384-227">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="81384-228">Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).</span><span class="sxs-lookup"><span data-stu-id="81384-228">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span>
1. <span data-ttu-id="81384-229">Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).</span><span class="sxs-lookup"><span data-stu-id="81384-229">For HTTP, use an HTTP (`http://`) endpoint.</span></span> <span data-ttu-id="81384-230">Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.</span><span class="sxs-lookup"><span data-stu-id="81384-230">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>
1. <span data-ttu-id="81384-231">Geben Sie den Namen der App am Ende der URL an.</span><span class="sxs-lookup"><span data-stu-id="81384-231">Provide the name of the app at the end of the URL.</span></span>
1. <span data-ttu-id="81384-232">`https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.</span><span class="sxs-lookup"><span data-stu-id="81384-232">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span> <span data-ttu-id="81384-233">Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="81384-233">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="81384-234">Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an.</span><span class="sxs-lookup"><span data-stu-id="81384-234">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span> <span data-ttu-id="81384-235">Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="81384-235">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="81384-236">Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="81384-236">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span>

<span data-ttu-id="81384-237">Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="81384-237">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="81384-238">Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (Out-of-Process) verwendet.</span><span class="sxs-lookup"><span data-stu-id="81384-238">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

<span data-ttu-id="81384-239">Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.</span><span class="sxs-lookup"><span data-stu-id="81384-239">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="81384-240">Speichern Sie den Profilnamen.</span><span class="sxs-lookup"><span data-stu-id="81384-240">Save the profile.</span></span>

<span data-ttu-id="81384-241">Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="81384-241">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span>

* <span data-ttu-id="81384-242">Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="81384-242">The following example configures the profile to use the HTTPS protocol:</span></span>
* <span data-ttu-id="81384-243">Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="81384-243">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

<span data-ttu-id="81384-244">Ausführen des Projekts</span><span class="sxs-lookup"><span data-stu-id="81384-244">Run the project</span></span> <span data-ttu-id="81384-245">Führen Sie Visual Studio als Administrator aus:</span><span class="sxs-lookup"><span data-stu-id="81384-245">Run Visual Studio as an administrator:</span></span>

<span data-ttu-id="81384-246">Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="81384-246">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>

> [!NOTE]
> <span data-ttu-id="81384-247">Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus.</span><span class="sxs-lookup"><span data-stu-id="81384-247">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span> <span data-ttu-id="81384-248">Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="81384-248">Visual Studio may prompt a restart if not running as an administrator.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="81384-249">Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="81384-249">If prompted, restart Visual Studio.</span></span>

* <span data-ttu-id="81384-250">Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.</span><span class="sxs-lookup"><span data-stu-id="81384-250">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>
* <xref:security/enforcing-ssl>

::: moniker-end
