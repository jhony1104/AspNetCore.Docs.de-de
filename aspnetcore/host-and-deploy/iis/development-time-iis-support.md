---
title: IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core
author: rick-anderson
description: Informationen zur Unterstützung des Debuggens von ASP.NET Core-Apps, wenn diese mit IIS unter Windows Server ausgeführt werden.
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
ms.openlocfilehash: a6719b4f84b1bc60c7c2aea2aa3a97ef79f43e2e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777019"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="40fec-103">IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40fec-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="40fec-104">Von [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span><span class="sxs-lookup"><span data-stu-id="40fec-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40fec-105">In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40fec-105">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="40fec-106">Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="40fec-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40fec-107">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="40fec-107">Prerequisites</span></span>

* [<span data-ttu-id="40fec-108">Visual Studio für Windows</span><span class="sxs-lookup"><span data-stu-id="40fec-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="40fec-109">Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="40fec-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="40fec-110">Workload **Plattformübergreifende .NET Core-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="40fec-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="40fec-111">X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)</span><span class="sxs-lookup"><span data-stu-id="40fec-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="40fec-112">Aktivieren von IIS</span><span class="sxs-lookup"><span data-stu-id="40fec-112">Enable IIS</span></span>

1. <span data-ttu-id="40fec-113">Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="40fec-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="40fec-114">Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.</span><span class="sxs-lookup"><span data-stu-id="40fec-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="40fec-115">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="40fec-115">Select **OK**.</span></span>

<span data-ttu-id="40fec-116">Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.</span><span class="sxs-lookup"><span data-stu-id="40fec-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="40fec-117">Konfigurieren von IIS</span><span class="sxs-lookup"><span data-stu-id="40fec-117">Configure IIS</span></span>

<span data-ttu-id="40fec-118">Für IIS muss eine Website mit Folgendem konfiguriert sein:</span><span class="sxs-lookup"><span data-stu-id="40fec-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="40fec-119">**Hostname:** In der Regel wird die **Standardwebsite** mit einem **Host** namens `localhost` verwendet.</span><span class="sxs-lookup"><span data-stu-id="40fec-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="40fec-120">Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.</span><span class="sxs-lookup"><span data-stu-id="40fec-120">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="40fec-121">**Sitebindung**</span><span class="sxs-lookup"><span data-stu-id="40fec-121">**Site Binding**</span></span>
  * <span data-ttu-id="40fec-122">Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443.</span><span class="sxs-lookup"><span data-stu-id="40fec-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="40fec-123">In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="40fec-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="40fec-124">Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.</span><span class="sxs-lookup"><span data-stu-id="40fec-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="40fec-125">Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="40fec-125">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="40fec-126">**Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**</span><span class="sxs-lookup"><span data-stu-id="40fec-126">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="40fec-127">Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit</span><span class="sxs-lookup"><span data-stu-id="40fec-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="40fec-128">Starten Sie den Visual Studio-Installer.</span><span class="sxs-lookup"><span data-stu-id="40fec-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="40fec-129">Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="40fec-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="40fec-130">Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="40fec-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="40fec-131">Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads.</span><span class="sxs-lookup"><span data-stu-id="40fec-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="40fec-132">Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="40fec-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="40fec-133">Konfigurieren des Projekts</span><span class="sxs-lookup"><span data-stu-id="40fec-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="40fec-134">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="40fec-134">HTTPS redirection</span></span>

<span data-ttu-id="40fec-135">Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren).</span><span class="sxs-lookup"><span data-stu-id="40fec-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="40fec-136">Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="40fec-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="40fec-137">Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="40fec-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="40fec-138">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="40fec-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="40fec-139">Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="40fec-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="40fec-140">Es ist keine App-Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="40fec-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="40fec-141">IIS-Startprofil</span><span class="sxs-lookup"><span data-stu-id="40fec-141">IIS launch profile</span></span>

<span data-ttu-id="40fec-142">So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40fec-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="40fec-143">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="40fec-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="40fec-144">Klicken Sie auf **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="40fec-144">Select **Properties**.</span></span> <span data-ttu-id="40fec-145">Öffnen Sie die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="40fec-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="40fec-146">Klicken Sie für **Profil** auf die Schaltfläche **Neu**.</span><span class="sxs-lookup"><span data-stu-id="40fec-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="40fec-147">Geben Sie dem Profil im Popupfenster den Namen „IIS“.</span><span class="sxs-lookup"><span data-stu-id="40fec-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="40fec-148">Klicken Sie auf **OK**, um das Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="40fec-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="40fec-149">Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="40fec-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="40fec-150">Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.</span><span class="sxs-lookup"><span data-stu-id="40fec-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="40fec-151">Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).</span><span class="sxs-lookup"><span data-stu-id="40fec-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="40fec-152">Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).</span><span class="sxs-lookup"><span data-stu-id="40fec-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="40fec-153">Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.</span><span class="sxs-lookup"><span data-stu-id="40fec-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="40fec-154">Geben Sie den Namen der App am Ende der URL an.</span><span class="sxs-lookup"><span data-stu-id="40fec-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="40fec-155">`https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.</span><span class="sxs-lookup"><span data-stu-id="40fec-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="40fec-156">Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="40fec-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="40fec-157">Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an.</span><span class="sxs-lookup"><span data-stu-id="40fec-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="40fec-158">Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="40fec-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="40fec-159">Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="40fec-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="40fec-160">Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="40fec-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="40fec-161">Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (In-Process) verwendet.</span><span class="sxs-lookup"><span data-stu-id="40fec-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="40fec-162">Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.</span><span class="sxs-lookup"><span data-stu-id="40fec-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="40fec-163">Speichern Sie den Profilnamen.</span><span class="sxs-lookup"><span data-stu-id="40fec-163">Save the profile.</span></span>

<span data-ttu-id="40fec-164">Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="40fec-164">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="40fec-165">Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="40fec-165">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="40fec-166">Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="40fec-166">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="40fec-167">Ausführen des Projekts</span><span class="sxs-lookup"><span data-stu-id="40fec-167">Run the project</span></span>

<span data-ttu-id="40fec-168">Führen Sie Visual Studio als Administrator aus:</span><span class="sxs-lookup"><span data-stu-id="40fec-168">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="40fec-169">Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="40fec-169">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="40fec-170">Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus.</span><span class="sxs-lookup"><span data-stu-id="40fec-170">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="40fec-171">Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="40fec-171">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="40fec-172">Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="40fec-172">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="40fec-173">Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.</span><span class="sxs-lookup"><span data-stu-id="40fec-173">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="40fec-174">Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung.</span><span class="sxs-lookup"><span data-stu-id="40fec-174">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="40fec-175">Beispielsweise werden Haltepunkte nicht erreicht.</span><span class="sxs-lookup"><span data-stu-id="40fec-175">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40fec-176">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="40fec-176">Additional resources</span></span>

* [<span data-ttu-id="40fec-177">Erste Schritte mit IIS-Manager in IIS</span><span class="sxs-lookup"><span data-stu-id="40fec-177">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="40fec-178">In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40fec-178">This article describes [Visual Studio](https://visualstudio.microsoft.com) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="40fec-179">Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="40fec-179">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40fec-180">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="40fec-180">Prerequisites</span></span>

* [<span data-ttu-id="40fec-181">Visual Studio für Windows</span><span class="sxs-lookup"><span data-stu-id="40fec-181">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="40fec-182">Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="40fec-182">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="40fec-183">Workload **Plattformübergreifende .NET Core-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="40fec-183">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="40fec-184">X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)</span><span class="sxs-lookup"><span data-stu-id="40fec-184">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="40fec-185">Aktivieren von IIS</span><span class="sxs-lookup"><span data-stu-id="40fec-185">Enable IIS</span></span>

1. <span data-ttu-id="40fec-186">Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="40fec-186">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="40fec-187">Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.</span><span class="sxs-lookup"><span data-stu-id="40fec-187">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="40fec-188">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="40fec-188">Select **OK**.</span></span>

<span data-ttu-id="40fec-189">Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.</span><span class="sxs-lookup"><span data-stu-id="40fec-189">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="40fec-190">Konfigurieren von IIS</span><span class="sxs-lookup"><span data-stu-id="40fec-190">Configure IIS</span></span>

<span data-ttu-id="40fec-191">Für IIS muss eine Website mit Folgendem konfiguriert sein:</span><span class="sxs-lookup"><span data-stu-id="40fec-191">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="40fec-192">**Hostname:** In der Regel wird die **Standardwebsite** mit einem **Host** namens `localhost` verwendet.</span><span class="sxs-lookup"><span data-stu-id="40fec-192">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="40fec-193">Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.</span><span class="sxs-lookup"><span data-stu-id="40fec-193">However, any valid IIS website with a unique host name works.</span></span>
* <span data-ttu-id="40fec-194">**Sitebindung**</span><span class="sxs-lookup"><span data-stu-id="40fec-194">**Site Binding**</span></span>
  * <span data-ttu-id="40fec-195">Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443.</span><span class="sxs-lookup"><span data-stu-id="40fec-195">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="40fec-196">In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="40fec-196">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="40fec-197">Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.</span><span class="sxs-lookup"><span data-stu-id="40fec-197">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="40fec-198">Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="40fec-198">Use a single binding for either HTTP or HTTPS.</span></span> <span data-ttu-id="40fec-199">**Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**</span><span class="sxs-lookup"><span data-stu-id="40fec-199">**Binding to both HTTP and HTTPS ports simultaneously isn't supported.**</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="40fec-200">Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit</span><span class="sxs-lookup"><span data-stu-id="40fec-200">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="40fec-201">Starten Sie den Visual Studio-Installer.</span><span class="sxs-lookup"><span data-stu-id="40fec-201">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="40fec-202">Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="40fec-202">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="40fec-203">Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="40fec-203">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="40fec-204">Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads.</span><span class="sxs-lookup"><span data-stu-id="40fec-204">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="40fec-205">Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="40fec-205">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="40fec-206">Konfigurieren des Projekts</span><span class="sxs-lookup"><span data-stu-id="40fec-206">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="40fec-207">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="40fec-207">HTTPS redirection</span></span>

<span data-ttu-id="40fec-208">Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren).</span><span class="sxs-lookup"><span data-stu-id="40fec-208">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="40fec-209">Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="40fec-209">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="40fec-210">Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="40fec-210">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="40fec-211">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="40fec-211">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="40fec-212">Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="40fec-212">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="40fec-213">Es ist keine App-Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="40fec-213">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="40fec-214">IIS-Startprofil</span><span class="sxs-lookup"><span data-stu-id="40fec-214">IIS launch profile</span></span>

<span data-ttu-id="40fec-215">So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="40fec-215">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="40fec-216">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="40fec-216">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="40fec-217">Klicken Sie auf **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="40fec-217">Select **Properties**.</span></span> <span data-ttu-id="40fec-218">Öffnen Sie die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="40fec-218">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="40fec-219">Klicken Sie für **Profil** auf die Schaltfläche **Neu**.</span><span class="sxs-lookup"><span data-stu-id="40fec-219">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="40fec-220">Geben Sie dem Profil im Popupfenster den Namen „IIS“.</span><span class="sxs-lookup"><span data-stu-id="40fec-220">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="40fec-221">Klicken Sie auf **OK**, um das Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="40fec-221">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="40fec-222">Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="40fec-222">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="40fec-223">Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.</span><span class="sxs-lookup"><span data-stu-id="40fec-223">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="40fec-224">Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).</span><span class="sxs-lookup"><span data-stu-id="40fec-224">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="40fec-225">Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).</span><span class="sxs-lookup"><span data-stu-id="40fec-225">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="40fec-226">Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.</span><span class="sxs-lookup"><span data-stu-id="40fec-226">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="40fec-227">Geben Sie den Namen der App am Ende der URL an.</span><span class="sxs-lookup"><span data-stu-id="40fec-227">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="40fec-228">`https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.</span><span class="sxs-lookup"><span data-stu-id="40fec-228">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="40fec-229">Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="40fec-229">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="40fec-230">Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an.</span><span class="sxs-lookup"><span data-stu-id="40fec-230">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="40fec-231">Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="40fec-231">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="40fec-232">Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="40fec-232">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="40fec-233">Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="40fec-233">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="40fec-234">Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (Out-of-Process) verwendet.</span><span class="sxs-lookup"><span data-stu-id="40fec-234">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="40fec-235">Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.</span><span class="sxs-lookup"><span data-stu-id="40fec-235">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="40fec-236">Speichern Sie den Profilnamen.</span><span class="sxs-lookup"><span data-stu-id="40fec-236">Save the profile.</span></span>

<span data-ttu-id="40fec-237">Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="40fec-237">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](https://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="40fec-238">Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="40fec-238">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="40fec-239">Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="40fec-239">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="40fec-240">Ausführen des Projekts</span><span class="sxs-lookup"><span data-stu-id="40fec-240">Run the project</span></span>

<span data-ttu-id="40fec-241">Führen Sie Visual Studio als Administrator aus:</span><span class="sxs-lookup"><span data-stu-id="40fec-241">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="40fec-242">Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="40fec-242">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="40fec-243">Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus.</span><span class="sxs-lookup"><span data-stu-id="40fec-243">Set the [Start Debugging button](/visualstudio/debugger/debugger-feature-tour) to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="40fec-244">Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="40fec-244">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="40fec-245">Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="40fec-245">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="40fec-246">Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.</span><span class="sxs-lookup"><span data-stu-id="40fec-246">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="40fec-247">Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung.</span><span class="sxs-lookup"><span data-stu-id="40fec-247">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="40fec-248">Beispielsweise werden Haltepunkte nicht erreicht.</span><span class="sxs-lookup"><span data-stu-id="40fec-248">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="40fec-249">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="40fec-249">Additional resources</span></span>

* [<span data-ttu-id="40fec-250">Erste Schritte mit IIS-Manager in IIS</span><span class="sxs-lookup"><span data-stu-id="40fec-250">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
