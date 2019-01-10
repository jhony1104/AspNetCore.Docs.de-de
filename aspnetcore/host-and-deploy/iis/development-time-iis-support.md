---
title: IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core
author: shirhatti
description: Informationen zur Unterstützung für das Debuggen von ASP.NET Core-Apps, wenn diese hinter IIS unter Windows Server ausgeführt werden.
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2018
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 44570bb28451ce4c5fde12ec77e3856fb5bd3062
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637663"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="50c25-103">IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="50c25-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="50c25-104">Von [Sourabh Shirhatti](https://twitter.com/sshirhatti) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="50c25-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="50c25-105">In diesem Artikel wird die Unterstützung für [Visual Studio](https://www.visualstudio.com/vs/) für das Debuggen von ASP.NET Core-Apps beschrieben, die hinter IIS unter Windows Server ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="50c25-105">This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core apps running behind IIS on Windows Server.</span></span> <span data-ttu-id="50c25-106">Dieses Thema führt Sie durch die Aktivierung dieses Features und die Einrichtung eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="50c25-106">This topic walks through enabling this feature and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="50c25-107">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="50c25-107">Prerequisites</span></span>

* [<span data-ttu-id="50c25-108">Visual Studio für Windows</span><span class="sxs-lookup"><span data-stu-id="50c25-108">Visual Studio for Windows</span></span>](https://www.microsoft.com/net/download/windows)
* <span data-ttu-id="50c25-109">Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="50c25-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="50c25-110">Workload **Plattformübergreifende .NET Core-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="50c25-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="50c25-111">X.509-Sicherheitszertifikat</span><span class="sxs-lookup"><span data-stu-id="50c25-111">X.509 security certificate</span></span>

## <a name="enable-iis"></a><span data-ttu-id="50c25-112">Aktivieren von IIS</span><span class="sxs-lookup"><span data-stu-id="50c25-112">Enable IIS</span></span>

1. <span data-ttu-id="50c25-113">Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="50c25-113">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="50c25-114">Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.</span><span class="sxs-lookup"><span data-stu-id="50c25-114">Select the **Internet Information Services** check box.</span></span>

![Windows-Features, die das aktivierte Kontrollkästchen „Internetinformationsdienste“ als schwarzes Quadrat anzeigen (ohne Häkchen); dies deutet darauf hin, dass einige der IIS-Features aktiviert sind](development-time-iis-support/_static/enable_iis.png)

<span data-ttu-id="50c25-116">Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.</span><span class="sxs-lookup"><span data-stu-id="50c25-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="50c25-117">Konfigurieren von IIS</span><span class="sxs-lookup"><span data-stu-id="50c25-117">Configure IIS</span></span>

<span data-ttu-id="50c25-118">Für IIS muss eine Website mit Folgendem konfiguriert sein:</span><span class="sxs-lookup"><span data-stu-id="50c25-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="50c25-119">Einem Hostnamen, der dem URL-Hostnamen des Startprofils der App entspricht.</span><span class="sxs-lookup"><span data-stu-id="50c25-119">A host name that matches the app's launch profile URL host name.</span></span>
* <span data-ttu-id="50c25-120">Einer Bindung für Port 443 mit zugewiesenem Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="50c25-120">Binding for port 443 with an assigned certificate.</span></span>

<span data-ttu-id="50c25-121">Beispiel: Der **Hostname** für eine hinzugefügte Website ist auf „localhost“ festgelegt (im Startprofil wird später in diesem Abschnitt auch „localhost“ verwendet).</span><span class="sxs-lookup"><span data-stu-id="50c25-121">For example, the **Host name** for an added website is set to "localhost" (the launch profile will also use "localhost" later in this topic).</span></span> <span data-ttu-id="50c25-122">Der Port ist auf „443“ (HTTPS) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="50c25-122">The port is set to "443" (HTTPS).</span></span> <span data-ttu-id="50c25-123">Das **IIS Express-Entwicklungszertifikat** wird der Website zugewiesen, es kann jedoch jedes gültige Zertifikat eingesetzt werden:</span><span class="sxs-lookup"><span data-stu-id="50c25-123">The **IIS Express Development Certificate** is assigned to the website, but any valid certificate works:</span></span>

![Fenster „Website hinzufügen“ in IIS, in dem der Bindungssatz für „localhost“ an Port 443 mit einem zugewiesenen Zertifikat dargestellt wird.](development-time-iis-support/_static/add-website-window.png)

<span data-ttu-id="50c25-125">Wenn die IIS-Installation bereits über eine **Standardwebsite** mit einem Hostnamen verfügt, der dem URL-Hostnamen des Startprofils der App entspricht:</span><span class="sxs-lookup"><span data-stu-id="50c25-125">If the IIS installation already has a **Default Web Site** with a host name that matches the app's launch profile URL host name:</span></span>

* <span data-ttu-id="50c25-126">Fügen Sie eine Portbindung für Port 443 (HTTPS) hinzu.</span><span class="sxs-lookup"><span data-stu-id="50c25-126">Add a port binding for port 443 (HTTPS).</span></span>
* <span data-ttu-id="50c25-127">Weisen Sie der Website ein gültiges Zertifikat zu.</span><span class="sxs-lookup"><span data-stu-id="50c25-127">Assign a valid certificate to the website.</span></span>

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="50c25-128">Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit</span><span class="sxs-lookup"><span data-stu-id="50c25-128">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="50c25-129">Starten Sie den Visual Studio-Installer.</span><span class="sxs-lookup"><span data-stu-id="50c25-129">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="50c25-130">Wählen Sie die Komponente **IIS-Unterstützung zur Entwicklungszeit** aus.</span><span class="sxs-lookup"><span data-stu-id="50c25-130">Select the **Development time IIS support** component.</span></span> <span data-ttu-id="50c25-131">Die Komponente wird im Bereich **Zusammenfassung** für die Workload **ASP.NET und Webentwicklung** als optional aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="50c25-131">The component is listed as optional in the **Summary** panel for the **ASP.NET and web development** workload.</span></span> <span data-ttu-id="50c25-132">Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="50c25-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

![Ändern von Visual Studio-Features: Die Registerkarte „Workloads“ wird ausgewählt.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a><span data-ttu-id="50c25-136">Konfigurieren des Projekts</span><span class="sxs-lookup"><span data-stu-id="50c25-136">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="50c25-137">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="50c25-137">HTTPS redirection</span></span>

<span data-ttu-id="50c25-138">Aktivieren Sie für ein neues Projekt das Kontrollkästchen bei **Configure for HTTPS** (Für HTTPS konfigurieren) im Fenster **Neue ASP.NET Core-Webanwendung**:</span><span class="sxs-lookup"><span data-stu-id="50c25-138">For a new project, select the check box to **Configure for HTTPS** in the **New ASP.NET Core Web Application** window:</span></span>

![Fenster „Neue ASP.NET Core-Webanwendung“ mit aktiviertem Kontrollkästchen bei „Configure for HTTPS“ (Für HTTPS konfigurieren).](development-time-iis-support/_static/new-app.png)

<span data-ttu-id="50c25-140">Verwenden Sie in einem vorhandenen Projekt in `Startup.Configure` Middleware für HTTPS-Umleitung, indem Sie die Erweiterungsmethode [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) aufrufen:</span><span class="sxs-lookup"><span data-stu-id="50c25-140">In an existing project, use HTTPS Redirection Middleware in `Startup.Configure` by calling the [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) extension method:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();

    app.UseMvc();
}
```

### <a name="iis-launch-profile"></a><span data-ttu-id="50c25-141">IIS-Startprofil</span><span class="sxs-lookup"><span data-stu-id="50c25-141">IIS launch profile</span></span>

<span data-ttu-id="50c25-142">So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="50c25-142">Create a new launch profile to add development-time IIS support:</span></span>

1. <span data-ttu-id="50c25-143">Klicken Sie für **Profil** auf die Schaltfläche **Neu**.</span><span class="sxs-lookup"><span data-stu-id="50c25-143">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="50c25-144">Geben Sie dem Profil im Popupfenster den Namen „IIS“.</span><span class="sxs-lookup"><span data-stu-id="50c25-144">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="50c25-145">Klicken Sie auf **OK**, um das Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="50c25-145">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="50c25-146">Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="50c25-146">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="50c25-147">Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.</span><span class="sxs-lookup"><span data-stu-id="50c25-147">Select the check box for **Launch browser** and provide the endpoint URL.</span></span> <span data-ttu-id="50c25-148">Verwenden Sie das HTTPS-Protokoll.</span><span class="sxs-lookup"><span data-stu-id="50c25-148">Use the HTTPS protocol.</span></span> <span data-ttu-id="50c25-149">In diesem Beispiel wird `https://localhost/WebApplication1` verwendet.</span><span class="sxs-lookup"><span data-stu-id="50c25-149">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="50c25-150">Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="50c25-150">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="50c25-151">Geben Sie eine Umgebungsvariable mit einem `ASPNETCORE_ENVIRONMENT`-Schlüssel und einem Wert von `Development` an.</span><span class="sxs-lookup"><span data-stu-id="50c25-151">Provide an environment variable with a key of `ASPNETCORE_ENVIRONMENT` and a value of `Development`.</span></span>
1. <span data-ttu-id="50c25-152">Legen Sie im Bereich **Webservereinstellungen** die **App-URL** fest.</span><span class="sxs-lookup"><span data-stu-id="50c25-152">In the **Web Server Settings** area, set the **App URL**.</span></span> <span data-ttu-id="50c25-153">In diesem Beispiel wird `https://localhost/WebApplication1` verwendet.</span><span class="sxs-lookup"><span data-stu-id="50c25-153">This example uses `https://localhost/WebApplication1`.</span></span>
1. <span data-ttu-id="50c25-154">Speichern Sie den Profilnamen.</span><span class="sxs-lookup"><span data-stu-id="50c25-154">Save the profile.</span></span>

![Fenster „Projekteigenschaften“, in dem die Registerkarte „Debuggen“ ausgewählt ist](development-time-iis-support/_static/project_properties.png)

<span data-ttu-id="50c25-159">Alternativ können Sie in der App manuell ein Startprofil zur Datei [launchSettings.json](http://json.schemastore.org/launchsettings) hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="50c25-159">Alternatively, manually add a launch profile to the [launchSettings.json](http://json.schemastore.org/launchsettings) file in the app:</span></span>

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

## <a name="run-the-project"></a><span data-ttu-id="50c25-160">Ausführen des Projekts</span><span class="sxs-lookup"><span data-stu-id="50c25-160">Run the project</span></span>

<span data-ttu-id="50c25-161">In Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="50c25-161">In Visual Studio:</span></span>

* <span data-ttu-id="50c25-162">Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="50c25-162">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="50c25-163">Legen Sie die Schaltfläche „Ausführen“ auf das Profil **IIS** fest, und klicken Sie zum Starten der App auf die Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="50c25-163">Set the Run button to the **IIS** profile and select the button to start the app.</span></span>

![Die Schaltfläche „Ausführen“ in der VS-Symbolleiste ist auf das IIS-Profil eingestellt, während die Dropdownliste für die Buildkonfiguration auf „Release“ gesetzt ist.](development-time-iis-support/_static/toolbar.png)

<span data-ttu-id="50c25-165">Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="50c25-165">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="50c25-166">Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="50c25-166">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="50c25-167">Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.</span><span class="sxs-lookup"><span data-stu-id="50c25-167">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="50c25-168">Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung.</span><span class="sxs-lookup"><span data-stu-id="50c25-168">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="50c25-169">Beispielsweise werden Haltepunkte nicht erreicht.</span><span class="sxs-lookup"><span data-stu-id="50c25-169">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="50c25-170">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="50c25-170">Additional resources</span></span>

* [<span data-ttu-id="50c25-171">Hosten von ASP.NET Core unter Windows mit IIS</span><span class="sxs-lookup"><span data-stu-id="50c25-171">Host ASP.NET Core on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="50c25-172">Einführung in das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="50c25-172">Introduction to ASP.NET Core Module</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="50c25-173">Konfigurationsreferenz für das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="50c25-173">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="50c25-174">Erzwingen von HTTPS</span><span class="sxs-lookup"><span data-stu-id="50c25-174">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
