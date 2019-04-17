---
title: IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core
author: guardrex
description: Informationen zur Unterstützung des Debuggens von ASP.NET Core-Apps, wenn diese mit IIS unter Windows Server ausgeführt werden.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 6f555858239b4432d252f8b3ac7add5c3e8bfe62
ms.sourcegitcommit: 258a97159da206f9009f23fdf6f8fa32f178e50b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425100"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a><span data-ttu-id="63f0b-103">IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="63f0b-103">Development-time IIS support in Visual Studio for ASP.NET Core</span></span>

<span data-ttu-id="63f0b-104">Von [Sourabh Shirhatti](https://twitter.com/sshirhatti) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="63f0b-104">By [Sourabh Shirhatti](https://twitter.com/sshirhatti) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="63f0b-105">In diesem Artikel wird die [Visual Studio](https://www.visualstudio.com/vs/)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="63f0b-105">This article describes [Visual Studio](https://www.visualstudio.com/vs/) support for debugging ASP.NET Core apps running with IIS on Windows Server.</span></span> <span data-ttu-id="63f0b-106">Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.</span><span class="sxs-lookup"><span data-stu-id="63f0b-106">This topic walks through enabling this scenario and setting up a project.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="63f0b-107">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="63f0b-107">Prerequisites</span></span>

* [<span data-ttu-id="63f0b-108">Visual Studio für Windows</span><span class="sxs-lookup"><span data-stu-id="63f0b-108">Visual Studio for Windows</span></span>](https://visualstudio.microsoft.com/downloads/)
* <span data-ttu-id="63f0b-109">Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="63f0b-109">**ASP.NET and web development** workload</span></span>
* <span data-ttu-id="63f0b-110">Workload **Plattformübergreifende .NET Core-Entwicklung**</span><span class="sxs-lookup"><span data-stu-id="63f0b-110">**.NET Core cross-platform development** workload</span></span>
* <span data-ttu-id="63f0b-111">X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)</span><span class="sxs-lookup"><span data-stu-id="63f0b-111">X.509 security certificate (for HTTPS support)</span></span>

## <a name="enable-iis"></a><span data-ttu-id="63f0b-112">Aktivieren von IIS</span><span class="sxs-lookup"><span data-stu-id="63f0b-112">Enable IIS</span></span>

1. <span data-ttu-id="63f0b-113">Navigieren Sie unter Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).</span><span class="sxs-lookup"><span data-stu-id="63f0b-113">In Windows, navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="63f0b-114">Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-114">Select the **Internet Information Services** check box.</span></span> <span data-ttu-id="63f0b-115">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-115">Select **OK**.</span></span>

<span data-ttu-id="63f0b-116">Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.</span><span class="sxs-lookup"><span data-stu-id="63f0b-116">The IIS installation may require a system restart.</span></span>

## <a name="configure-iis"></a><span data-ttu-id="63f0b-117">Konfigurieren von IIS</span><span class="sxs-lookup"><span data-stu-id="63f0b-117">Configure IIS</span></span>

<span data-ttu-id="63f0b-118">Für IIS muss eine Website mit Folgendem konfiguriert sein:</span><span class="sxs-lookup"><span data-stu-id="63f0b-118">IIS must have a website configured with the following:</span></span>

* <span data-ttu-id="63f0b-119">**Hostname** &ndash; In der Regel wird die **Standardwebsite** mit einem **Host** namens `localhost` verwendet.</span><span class="sxs-lookup"><span data-stu-id="63f0b-119">**Host name** &ndash; Typically, the **Default Web Site** is used with a **Host name** of `localhost`.</span></span> <span data-ttu-id="63f0b-120">Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-120">However, any valid IIS website with a unique host name works.</span></span>
* **<span data-ttu-id="63f0b-121">Sitebindung</span><span class="sxs-lookup"><span data-stu-id="63f0b-121">Site Binding</span></span>**
  * <span data-ttu-id="63f0b-122">Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443.</span><span class="sxs-lookup"><span data-stu-id="63f0b-122">For apps that require HTTPS, create a binding to port 443 with a certificate.</span></span> <span data-ttu-id="63f0b-123">In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.</span><span class="sxs-lookup"><span data-stu-id="63f0b-123">Typically, the **IIS Express Development Certificate** is used, but any valid certificate works.</span></span>
  * <span data-ttu-id="63f0b-124">Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.</span><span class="sxs-lookup"><span data-stu-id="63f0b-124">For apps that use HTTP, confirm the existence of a binding to post 80 or create a binding to port 80 for a new site.</span></span>
  * <span data-ttu-id="63f0b-125">Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="63f0b-125">Use a single binding for either HTTP or HTTPS.</span></span> **<span data-ttu-id="63f0b-126">Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="63f0b-126">Binding to both HTTP and HTTPS ports simultaneously isn't supported.</span></span>**

## <a name="enable-development-time-iis-support-in-visual-studio"></a><span data-ttu-id="63f0b-127">Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit</span><span class="sxs-lookup"><span data-stu-id="63f0b-127">Enable development-time IIS support in Visual Studio</span></span>

1. <span data-ttu-id="63f0b-128">Starten Sie den Visual Studio-Installer.</span><span class="sxs-lookup"><span data-stu-id="63f0b-128">Launch the Visual Studio installer.</span></span>
1. <span data-ttu-id="63f0b-129">Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-129">Select **Modify** for the Visual Studio installation that you plan to use for IIS development-time support.</span></span>
1. <span data-ttu-id="63f0b-130">Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.</span><span class="sxs-lookup"><span data-stu-id="63f0b-130">For the **ASP.NET and web development** workload, locate and install the **Development time IIS support** component.</span></span>

   <span data-ttu-id="63f0b-131">Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads.</span><span class="sxs-lookup"><span data-stu-id="63f0b-131">The component is listed in the **Optional** section under **Development time IIS support** in the **Installation details** panel to the right of the workloads.</span></span> <span data-ttu-id="63f0b-132">Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="63f0b-132">The component installs the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), which is a native IIS module required to run ASP.NET Core apps with IIS.</span></span>

## <a name="configure-the-project"></a><span data-ttu-id="63f0b-133">Konfigurieren des Projekts</span><span class="sxs-lookup"><span data-stu-id="63f0b-133">Configure the project</span></span>

### <a name="https-redirection"></a><span data-ttu-id="63f0b-134">HTTPS-Umleitung</span><span class="sxs-lookup"><span data-stu-id="63f0b-134">HTTPS redirection</span></span>

<span data-ttu-id="63f0b-135">Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren).</span><span class="sxs-lookup"><span data-stu-id="63f0b-135">For a new project that requires HTTPS, select the check box to **Configure for HTTPS** in the **Create a new ASP.NET Core Web Application** window.</span></span> <span data-ttu-id="63f0b-136">Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63f0b-136">Selecting the check box adds [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) to the app when it's created.</span></span>

<span data-ttu-id="63f0b-137">Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="63f0b-137">For an existing project that requires HTTPS, use HTTPS Redirection and HSTS Middleware in `Startup.Configure`.</span></span> <span data-ttu-id="63f0b-138">Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.</span><span class="sxs-lookup"><span data-stu-id="63f0b-138">For more information, see <xref:security/enforcing-ssl>.</span></span>

<span data-ttu-id="63f0b-139">Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="63f0b-139">For a project that uses HTTP, [HTTPS Redirection and HSTS Middleware](xref:security/enforcing-ssl) aren't added to the app.</span></span> <span data-ttu-id="63f0b-140">Es ist keine App-Konfiguration erforderlich.</span><span class="sxs-lookup"><span data-stu-id="63f0b-140">No app configuration is required.</span></span>

### <a name="iis-launch-profile"></a><span data-ttu-id="63f0b-141">IIS-Startprofil</span><span class="sxs-lookup"><span data-stu-id="63f0b-141">IIS launch profile</span></span>

<span data-ttu-id="63f0b-142">So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:</span><span class="sxs-lookup"><span data-stu-id="63f0b-142">Create a new launch profile to add development-time IIS support:</span></span>

::: moniker range=">= aspnetcore-3.0"

1. <span data-ttu-id="63f0b-143">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="63f0b-143">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="63f0b-144">Klicken Sie auf **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-144">Select **Properties**.</span></span> <span data-ttu-id="63f0b-145">Öffnen Sie die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-145">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="63f0b-146">Klicken Sie für **Profil** auf die Schaltfläche **Neu**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-146">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="63f0b-147">Geben Sie dem Profil im Popupfenster den Namen „IIS“.</span><span class="sxs-lookup"><span data-stu-id="63f0b-147">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="63f0b-148">Klicken Sie auf **OK**, um das Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-148">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="63f0b-149">Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="63f0b-149">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="63f0b-150">Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.</span><span class="sxs-lookup"><span data-stu-id="63f0b-150">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="63f0b-151">Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).</span><span class="sxs-lookup"><span data-stu-id="63f0b-151">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="63f0b-152">Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).</span><span class="sxs-lookup"><span data-stu-id="63f0b-152">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="63f0b-153">Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.</span><span class="sxs-lookup"><span data-stu-id="63f0b-153">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="63f0b-154">Geben Sie den Namen der App am Ende der URL an.</span><span class="sxs-lookup"><span data-stu-id="63f0b-154">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="63f0b-155">`https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.</span><span class="sxs-lookup"><span data-stu-id="63f0b-155">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="63f0b-156">Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-156">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="63f0b-157">Geben Sie eine Umgebungsvariable mit dem **Namen** `ASPNETCORE_ENVIRONMENT` und dem **Wert** `Development` an.</span><span class="sxs-lookup"><span data-stu-id="63f0b-157">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="63f0b-158">Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63f0b-158">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="63f0b-159">Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-159">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="63f0b-160">Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="63f0b-160">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="63f0b-161">Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (In-Process) verwendet.</span><span class="sxs-lookup"><span data-stu-id="63f0b-161">If the property isn't present, the default hosting model of the app is used, which is in-process.</span></span> <span data-ttu-id="63f0b-162">Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.</span><span class="sxs-lookup"><span data-stu-id="63f0b-162">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="63f0b-163">Speichern Sie den Profilnamen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-163">Save the profile.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. <span data-ttu-id="63f0b-164">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt.</span><span class="sxs-lookup"><span data-stu-id="63f0b-164">Right-click the project in **Solution Explorer**.</span></span> <span data-ttu-id="63f0b-165">Klicken Sie auf **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-165">Select **Properties**.</span></span> <span data-ttu-id="63f0b-166">Öffnen Sie die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-166">Open the **Debug** tab.</span></span>
1. <span data-ttu-id="63f0b-167">Klicken Sie für **Profil** auf die Schaltfläche **Neu**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-167">For **Profile**, select the **New** button.</span></span> <span data-ttu-id="63f0b-168">Geben Sie dem Profil im Popupfenster den Namen „IIS“.</span><span class="sxs-lookup"><span data-stu-id="63f0b-168">Name the profile "IIS" in the popup window.</span></span> <span data-ttu-id="63f0b-169">Klicken Sie auf **OK**, um das Profil zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-169">Select **OK** to create the profile.</span></span>
1. <span data-ttu-id="63f0b-170">Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="63f0b-170">For the **Launch** setting, select **IIS** from the list.</span></span>
1. <span data-ttu-id="63f0b-171">Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.</span><span class="sxs-lookup"><span data-stu-id="63f0b-171">Select the check box for **Launch browser** and provide the endpoint URL.</span></span>

   <span data-ttu-id="63f0b-172">Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).</span><span class="sxs-lookup"><span data-stu-id="63f0b-172">When the app requires HTTPS, use an HTTPS endpoint (`https://`).</span></span> <span data-ttu-id="63f0b-173">Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`).</span><span class="sxs-lookup"><span data-stu-id="63f0b-173">For HTTP, use an HTTP (`http://`) endpoint.</span></span>

   <span data-ttu-id="63f0b-174">Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.</span><span class="sxs-lookup"><span data-stu-id="63f0b-174">Provide the same host name and port as the [IIS configuration specified earlier uses](#configure-iis), typically `localhost`.</span></span>

   <span data-ttu-id="63f0b-175">Geben Sie den Namen der App am Ende der URL an.</span><span class="sxs-lookup"><span data-stu-id="63f0b-175">Provide the name of the app at the end of the URL.</span></span>

   <span data-ttu-id="63f0b-176">`https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs.</span><span class="sxs-lookup"><span data-stu-id="63f0b-176">For example, `https://localhost/WebApplication1` (HTTPS) or `http://localhost/WebApplication1` (HTTP) are valid endpoint URLs.</span></span>
1. <span data-ttu-id="63f0b-177">Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="63f0b-177">In the **Environment variables** section, select the **Add** button.</span></span> <span data-ttu-id="63f0b-178">Geben Sie eine Umgebungsvariable mit dem **Namen** `ASPNETCORE_ENVIRONMENT` und dem **Wert** `Development` an.</span><span class="sxs-lookup"><span data-stu-id="63f0b-178">Provide an environment variable with a **Name** of `ASPNETCORE_ENVIRONMENT` and a **Value** of `Development`.</span></span>
1. <span data-ttu-id="63f0b-179">Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="63f0b-179">In the **Web Server Settings** area, set the **App URL** to the same value used for the **Launch browser** endpoint URL.</span></span>
1. <span data-ttu-id="63f0b-180">Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-180">For the **Hosting Model** setting in Visual Studio 2019 or later, select **Default** to use the hosting model used by the project.</span></span> <span data-ttu-id="63f0b-181">Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet.</span><span class="sxs-lookup"><span data-stu-id="63f0b-181">If the project sets the `<AspNetCoreHostingModel>` property in its project file, the value of the property (`InProcess` or `OutOfProcess`) is used.</span></span> <span data-ttu-id="63f0b-182">Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (Out-of-Process) verwendet.</span><span class="sxs-lookup"><span data-stu-id="63f0b-182">If the property isn't present, the default hosting model of the app is used, which is out-of-process.</span></span> <span data-ttu-id="63f0b-183">Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.</span><span class="sxs-lookup"><span data-stu-id="63f0b-183">If the app requires an explicit hosting model setting different from the app's normal hosting model, set the **Hosting Model** to either `In Process` or `Out Of Process` as needed.</span></span>
1. <span data-ttu-id="63f0b-184">Speichern Sie den Profilnamen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-184">Save the profile.</span></span>

::: moniker-end

<span data-ttu-id="63f0b-185">Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](http://json.schemastore.org/launchsettings) hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-185">When not using Visual Studio, manually add a launch profile to the [launchSettings.json](http://json.schemastore.org/launchsettings) file in the *Properties* folder.</span></span> <span data-ttu-id="63f0b-186">Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:</span><span class="sxs-lookup"><span data-stu-id="63f0b-186">The following example configures the profile to use the HTTPS protocol:</span></span>

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

<span data-ttu-id="63f0b-187">Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.</span><span class="sxs-lookup"><span data-stu-id="63f0b-187">Confirm that the `applicationUrl` and `launchUrl` endpoints match and use the same protocol as the IIS binding configuration, either HTTP or HTTPS.</span></span>

## <a name="run-the-project"></a><span data-ttu-id="63f0b-188">Ausführen des Projekts</span><span class="sxs-lookup"><span data-stu-id="63f0b-188">Run the project</span></span>

<span data-ttu-id="63f0b-189">Führen Sie Visual Studio als Administrator aus:</span><span class="sxs-lookup"><span data-stu-id="63f0b-189">Run Visual Studio as an administrator:</span></span>

* <span data-ttu-id="63f0b-190">Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="63f0b-190">Confirm that the build configuration drop-down list is set to **Debug**.</span></span>
* <span data-ttu-id="63f0b-191">Legen Sie die Schaltfläche „Ausführen“ auf das Profil **IIS** fest, und klicken Sie zum Starten der App auf die Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="63f0b-191">Set the Run button to the **IIS** profile and select the button to start the app.</span></span>

<span data-ttu-id="63f0b-192">Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="63f0b-192">Visual Studio may prompt a restart if not running as an administrator.</span></span> <span data-ttu-id="63f0b-193">Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.</span><span class="sxs-lookup"><span data-stu-id="63f0b-193">If prompted, restart Visual Studio.</span></span>

<span data-ttu-id="63f0b-194">Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.</span><span class="sxs-lookup"><span data-stu-id="63f0b-194">If an untrusted development certificate is used, the browser may require you to create an exception for the untrusted certificate.</span></span>

> [!NOTE]
> <span data-ttu-id="63f0b-195">Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung.</span><span class="sxs-lookup"><span data-stu-id="63f0b-195">Debugging a Release build configuration with [Just My Code](/visualstudio/debugger/just-my-code) and compiler optimizations results in a degraded experience.</span></span> <span data-ttu-id="63f0b-196">Beispielsweise werden Haltepunkte nicht erreicht.</span><span class="sxs-lookup"><span data-stu-id="63f0b-196">For example, break points aren't hit.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="63f0b-197">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="63f0b-197">Additional resources</span></span>

* [<span data-ttu-id="63f0b-198">Erste Schritte mit IIS-Manager in IIS</span><span class="sxs-lookup"><span data-stu-id="63f0b-198">Getting Started with the IIS Manager in IIS</span></span>](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* [<span data-ttu-id="63f0b-199">Hosten von ASP.NET Core unter Windows mit IIS</span><span class="sxs-lookup"><span data-stu-id="63f0b-199">Host ASP.NET Core on Windows with IIS</span></span>](xref:host-and-deploy/iis/index)
* [<span data-ttu-id="63f0b-200">Einführung in das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="63f0b-200">Introduction to ASP.NET Core Module</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="63f0b-201">Konfigurationsreferenz für das ASP.NET Core-Modul</span><span class="sxs-lookup"><span data-stu-id="63f0b-201">ASP.NET Core Module configuration reference</span></span>](xref:host-and-deploy/aspnet-core-module)
* [<span data-ttu-id="63f0b-202">Erzwingen von HTTPS</span><span class="sxs-lookup"><span data-stu-id="63f0b-202">Enforce HTTPS</span></span>](xref:security/enforcing-ssl)
