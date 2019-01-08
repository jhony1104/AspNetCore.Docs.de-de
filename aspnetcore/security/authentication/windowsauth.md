---
title: Konfigurieren der Windows-Authentifizierung in ASP.NET Core
author: scottaddie
description: Informationen Sie zum Konfigurieren der Windows-Authentifizierung in ASP.NET Core mit IIS Express, IIS und HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 12/23/2018
uid: security/authentication/windowsauth
ms.openlocfilehash: 64178c8fce71445fc6a728a236d811484b21e3e0
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54099259"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="a8949-103">Konfigurieren der Windows-Authentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a8949-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="a8949-104">Durch [Scott Addie](https://twitter.com/Scott_Addie) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a8949-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a8949-105">[Windows-Authentifizierung](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) kann konfiguriert werden, für ASP.NET Core-apps mit gehosteten [IIS](xref:host-and-deploy/iis/index) oder [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="a8949-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="a8949-106">Windows-Authentifizierung basiert auf dem Betriebssystem zur Authentifizierung von Benutzern von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="a8949-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="a8949-107">Sie können Windows-Authentifizierung verwenden, wenn Ihre Server in einem Unternehmensnetzwerk, die mithilfe von Active Directory-domänenidentitäten oder Windows-Konten zur Identifizierung von Benutzern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a8949-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="a8949-108">Windows-Authentifizierung ist am besten für Intranetumgebungen, in dem Benutzer, Client-apps und Webserver mit der gleichen Windows-Domäne gehören.</span><span class="sxs-lookup"><span data-stu-id="a8949-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a><span data-ttu-id="a8949-109">Aktivieren der Windows-Authentifizierung in ASP.NET Core-Apps</span><span class="sxs-lookup"><span data-stu-id="a8949-109">Enable Windows Authentication in an ASP.NET Core app</span></span>

<span data-ttu-id="a8949-110">Die **Webanwendung** Vorlage zur Verfügung, über Visual Studio oder .NET Core-CLI kann konfiguriert werden, um die Windows-Authentifizierung unterstützen.</span><span class="sxs-lookup"><span data-stu-id="a8949-110">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a8949-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a8949-111">Visual Studio</span></span>](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a><span data-ttu-id="a8949-112">Verwenden Sie die Windows-Authentifizierung-app-Vorlage für ein neues Projekt</span><span class="sxs-lookup"><span data-stu-id="a8949-112">Use the Windows Authentication app template for a new project</span></span>

<span data-ttu-id="a8949-113">In Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="a8949-113">In Visual Studio:</span></span>

1. <span data-ttu-id="a8949-114">Erstellen Sie ein neues **ASP.NET Core-Webanwendung**.</span><span class="sxs-lookup"><span data-stu-id="a8949-114">Create a new **ASP.NET Core Web Application**.</span></span>
1. <span data-ttu-id="a8949-115">Wählen Sie **Webanwendung** aus der Liste der Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="a8949-115">Select **Web Application** from the list of templates.</span></span>
1. <span data-ttu-id="a8949-116">Wählen Sie die **Authentifizierung ändern** Schaltfläche, und wählen **Windows-Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="a8949-116">Select the **Change Authentication** button and select **Windows Authentication**.</span></span>

<span data-ttu-id="a8949-117">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="a8949-117">Run the app.</span></span> <span data-ttu-id="a8949-118">Der Benutzername wird in der gerenderten app-Benutzeroberfläche angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a8949-118">The username appears in the rendered app's user interface.</span></span>

### <a name="manual-configuration-for-an-existing-project"></a><span data-ttu-id="a8949-119">Manuelle Konfiguration für ein vorhandenes Projekt</span><span class="sxs-lookup"><span data-stu-id="a8949-119">Manual configuration for an existing project</span></span>

<span data-ttu-id="a8949-120">Die Eigenschaften des Projekts können Sie Windows-Authentifizierung aktivieren und deaktivieren Sie die anonyme Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="a8949-120">The project's properties allow you to enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="a8949-121">Mit der rechten Maustaste in des Projekts in Visual Studio **Projektmappen-Explorer** , und wählen Sie **Eigenschaften**.</span><span class="sxs-lookup"><span data-stu-id="a8949-121">Right-click the project in Visual Studio's **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="a8949-122">Klicken Sie auf die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="a8949-122">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="a8949-123">Deaktivieren Sie das Kontrollkästchen für **Aktivieren der anonymen Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="a8949-123">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="a8949-124">Wählen Sie das Kontrollkästchen für **Windows-Authentifizierung aktivieren**.</span><span class="sxs-lookup"><span data-stu-id="a8949-124">Select the check box for **Enable Windows Authentication**.</span></span>

<span data-ttu-id="a8949-125">Alternativ können die Eigenschaften konfiguriert werden, der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="a8949-125">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a8949-126">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="a8949-126">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a8949-127">Verwenden der **Windows-Authentifizierung** -app-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a8949-127">Use the **Windows Authentication** app template.</span></span>

<span data-ttu-id="a8949-128">Führen Sie die [Dotnet neue](/dotnet/core/tools/dotnet-new) -Befehl mit der `webapp` Argument (ASP.NET Core-Web-App) und `--auth Windows` wechseln:</span><span class="sxs-lookup"><span data-stu-id="a8949-128">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

---

## <a name="enable-windows-authentication-with-iis"></a><span data-ttu-id="a8949-129">Aktivieren der Windows-Authentifizierung mit IIS</span><span class="sxs-lookup"><span data-stu-id="a8949-129">Enable Windows Authentication with IIS</span></span>

<span data-ttu-id="a8949-130">IIS verwendet den [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zum Hosten von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="a8949-130">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="a8949-131">Windows-Authentifizierung konfiguriert ist, für IIS über die *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="a8949-131">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="a8949-132">Die folgenden Abschnitte zeigen Gewusst wie:</span><span class="sxs-lookup"><span data-stu-id="a8949-132">The following sections show how to:</span></span>

* <span data-ttu-id="a8949-133">Geben Sie eine lokale *"Web.config"* -Datei, die Windows-Authentifizierung auf dem Server aktiviert, wenn die app bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="a8949-133">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="a8949-134">Verwenden Sie den IIS-Manager so konfigurieren Sie die *"Web.config"* Datei von einer ASP.NET Core-app, die bereits auf dem Server bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a8949-134">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

### <a name="iis-configuration"></a><span data-ttu-id="a8949-135">IIS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a8949-135">IIS configuration</span></span>

<span data-ttu-id="a8949-136">Wenn Sie nicht bereits geschehen, aktivieren Sie IIS zum Hosten von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="a8949-136">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="a8949-137">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="a8949-137">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="a8949-138">Den Rollendienst "IIS" für Windows-Authentifizierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="a8949-138">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="a8949-139">Weitere Informationen finden Sie unter [Aktivieren der Windows-Authentifizierung in IIS-Rollendienste (Siehe Schritt2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="a8949-139">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="a8949-140">Middleware für die Integration von IIS ist standardmäßig konfiguriert, um Anforderungen zu authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="a8949-140">IIS Integration Middleware is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="a8949-141">Weitere Informationen finden Sie unter [Hosten von ASP.NET Core unter Windows mit IIS: IIS-Optionen (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="a8949-141">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="a8949-142">ASP.NET Core-Modul ist zum Weiterleiten von der Windows-Authentifizierung-Token für die app wird standardmäßig konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a8949-142">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="a8949-143">Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul: Attribute des-Elements AspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="a8949-143">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

### <a name="create-a-new-iis-site"></a><span data-ttu-id="a8949-144">Erstellen einer neuen IIS-Website</span><span class="sxs-lookup"><span data-stu-id="a8949-144">Create a new IIS site</span></span>

<span data-ttu-id="a8949-145">Geben Sie einen Namen und den Ordner, und erlauben Sie, dass Sie einen neuen Anwendungspool zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a8949-145">Specify a name and folder and allow it to create a new application pool.</span></span>

### <a name="enable-windows-authentication-for-the-app-in-iis"></a><span data-ttu-id="a8949-146">Aktivieren der Windows-Authentifizierung für die app in IIS</span><span class="sxs-lookup"><span data-stu-id="a8949-146">Enable Windows Authentication for the app in IIS</span></span>

<span data-ttu-id="a8949-147">Verwendung **entweder** der folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="a8949-147">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="a8949-148">[Development-Seite-Konfiguration vor dem Veröffentlichen der Anwendung](#development-side-configuration-with-a-local-webconfig-file) (*empfohlen*)</span><span class="sxs-lookup"><span data-stu-id="a8949-148">[Development-side configuration before publishing the app](#development-side-configuration-with-a-local-webconfig-file) (*Recommended*)</span></span>
* [<span data-ttu-id="a8949-149">Serverseitige Konfiguration nach dem Veröffentlichen der app</span><span class="sxs-lookup"><span data-stu-id="a8949-149">Server-side configuration after publishing the app</span></span>](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a><span data-ttu-id="a8949-150">Development-Seite-Konfiguration mit einer lokalen Datei "Web.config"-Datei</span><span class="sxs-lookup"><span data-stu-id="a8949-150">Development-side configuration with a local web.config file</span></span>

<span data-ttu-id="a8949-151">Die folgenden Schritte aus **vor** Sie [veröffentlichen und Bereitstellen des Projekts](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="a8949-151">Perform the following steps **before** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

<span data-ttu-id="a8949-152">Fügen Sie die folgenden *"Web.config"* Datei in das Stammverzeichnis des Projekts:</span><span class="sxs-lookup"><span data-stu-id="a8949-152">Add the following *web.config* file to the project root:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

<span data-ttu-id="a8949-153">Wenn das Projekt veröffentlicht wird, durch das SDK (ohne die `<IsTransformWebConfigDisabled>` -Eigenschaft auf festgelegt `true` in der Projektdatei), die veröffentlichte *"Web.config"* -Datei enthält die `<location><system.webServer><security><authentication>` Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="a8949-153">When the project is published by the SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="a8949-154">Weitere Informationen zu den `<IsTransformWebConfigDisabled>` -Eigenschaft finden Sie unter <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="a8949-154">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

#### <a name="server-side-configuration-with-the-iis-manager"></a><span data-ttu-id="a8949-155">Serverseitige Konfiguration mit dem IIS-Manager</span><span class="sxs-lookup"><span data-stu-id="a8949-155">Server-side configuration with the IIS Manager</span></span>

<span data-ttu-id="a8949-156">Die folgenden Schritte aus **nach** Sie [veröffentlichen und Bereitstellen des Projekts](#publish-and-deploy-your-project-to-the-iis-site-folder).</span><span class="sxs-lookup"><span data-stu-id="a8949-156">Perform the following steps **after** you [publish and deploy your project](#publish-and-deploy-your-project-to-the-iis-site-folder).</span></span>

1. <span data-ttu-id="a8949-157">Im IIS-Manager, wählen Sie die IIS-Website unter der **Websites** Knoten die **Verbindungen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="a8949-157">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
1. <span data-ttu-id="a8949-158">Doppelklicken Sie auf **Authentifizierung** in die **IIS** Bereich.</span><span class="sxs-lookup"><span data-stu-id="a8949-158">Double-click **Authentication** in the **IIS** area.</span></span>
1. <span data-ttu-id="a8949-159">Wählen Sie **anonyme Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="a8949-159">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="a8949-160">Wählen Sie **deaktivieren** in die **Aktionen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="a8949-160">Select **Disable** in the **Actions** sidebar.</span></span>
1. <span data-ttu-id="a8949-161">Wählen Sie **Windows-Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="a8949-161">Select **Windows Authentication**.</span></span> <span data-ttu-id="a8949-162">Wählen Sie **aktivieren** in die **Aktionen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="a8949-162">Select **Enable** in the **Actions** sidebar.</span></span>

<span data-ttu-id="a8949-163">Wenn diese Aktionen ausgeführt werden, ändert der IIS-Manager der app *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="a8949-163">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="a8949-164">Ein `<system.webServer><security><authentication>` Knoten wird hinzugefügt, mit aktualisierten Einstellungen für `anonymousAuthentication` und `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="a8949-164">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

<span data-ttu-id="a8949-165">Die `<system.webServer>` Abschnitts in der *"Web.config"* Datei vom IIS-Manager befindet sich außerhalb der app `<location>` Abschnitt durch die .NET Core SDK hinzugefügt wird, wenn die Anwendung veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="a8949-165">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="a8949-166">Da der Abschnitt, außerhalb von hinzugefügt wird den `<location>` Knoten werden die Einstellungen von einem geerbt [untergeordnete apps, die](xref:host-and-deploy/iis/index#sub-applications) der aktuellen App.</span><span class="sxs-lookup"><span data-stu-id="a8949-166">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="a8949-167">Um die Vererbung zu verhindern, verschieben Sie die hinzugefügte `<security>` Abschnitt in der die `<location><system.webServer>` Abschnitt, in dem das SDK zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="a8949-167">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the SDK provided.</span></span>

<span data-ttu-id="a8949-168">Wenn IIS-Manager verwendet wird, um die IIS-Konfiguration hinzuzufügen, es wirkt sich nur auf der app *"Web.config"* Datei auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="a8949-168">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="a8949-169">Eine anschließende Bereitstellung der app möglicherweise die Einstellungen auf dem Server überschreiben, wenn die Kopie des Servers der *"Web.config"* durch des Projekts ersetzt *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="a8949-169">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="a8949-170">Verwendung **entweder** der folgenden Methoden zum Verwalten der Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="a8949-170">Use **either** of the following approaches to manage the settings:</span></span>

* <span data-ttu-id="a8949-171">Verwenden Sie IIS-Manager zum Zurücksetzen der Einstellungen in der *"Web.config"* -Datei nach der Bereitstellung die Datei überschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="a8949-171">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
* <span data-ttu-id="a8949-172">Hinzufügen einer *Datei "Web.config"* an die app lokal mit den Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="a8949-172">Add a *web.config file* to the app locally with the settings.</span></span> <span data-ttu-id="a8949-173">Weitere Informationen finden Sie unter den [Development-Seite-Konfiguration](#development-side-configuration-with-a-local-webconfig-file) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="a8949-173">For more information, see the [Development-side configuration](#development-side-configuration-with-a-local-webconfig-file) section.</span></span>

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a><span data-ttu-id="a8949-174">Veröffentlichen Sie und Bereitstellen Sie des Projekts in der IIS-Site-Ordner</span><span class="sxs-lookup"><span data-stu-id="a8949-174">Publish and deploy your project to the IIS site folder</span></span>

<span data-ttu-id="a8949-175">Verwenden Visual Studio oder .NET Core-CLI, veröffentlichen und Bereitstellen der app in den Zielordner.</span><span class="sxs-lookup"><span data-stu-id="a8949-175">Using Visual Studio or the .NET Core CLI, publish and deploy the app to the destination folder.</span></span>

<span data-ttu-id="a8949-176">Weitere Informationen zum hosting mit IIS Veröffentlichung und Bereitstellung, finden Sie unter den folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="a8949-176">For more information on hosting with IIS, publishing, and deployment, see the following topics:</span></span>

* [<span data-ttu-id="a8949-177">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="a8949-177">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

<span data-ttu-id="a8949-178">Starten Sie die app aus, um sicherzustellen, dass die Windows-Authentifizierung funktioniert.</span><span class="sxs-lookup"><span data-stu-id="a8949-178">Launch the app to verify Windows Authentication is working.</span></span>

## <a name="enable-windows-authentication-with-httpsys"></a><span data-ttu-id="a8949-179">Aktivieren der Windows-Authentifizierung mit HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a8949-179">Enable Windows Authentication with HTTP.sys</span></span>

<span data-ttu-id="a8949-180">Obwohl Windows-Authentifizierung von Kestrel nicht unterstützt wird, können Sie [HTTP.sys](xref:fundamentals/servers/httpsys) selbst gehostete Szenarios für Windows zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="a8949-180">Although Kestrel doesn't support Windows Authentication, you can use [HTTP.sys](xref:fundamentals/servers/httpsys) to support self-hosted scenarios on Windows.</span></span> <span data-ttu-id="a8949-181">Im folgenden Beispiel wird der app Web-Host, um die "http.sys" mit der Windows-Authentifizierung zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="a8949-181">The following example configures the app's web host to use HTTP.sys with Windows Authentication:</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> <span data-ttu-id="a8949-182">HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll.</span><span class="sxs-lookup"><span data-stu-id="a8949-182">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="a8949-183">Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a8949-183">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="a8949-184">Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="a8949-184">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="a8949-185">Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App.</span><span class="sxs-lookup"><span data-stu-id="a8949-185">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="a8949-186">HTTP.sys wird nicht auf Nano Server-Version 1709 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a8949-186">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="a8949-187">Um Windows-Authentifizierung und HTTP.sys mit Nano Server verwenden möchten, verwenden eine [Server Core (Microsoft/Windowsservercore) Container](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="a8949-187">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="a8949-188">Weitere Informationen zur Server Core finden Sie unter [Was ist, dass der Server Core-Installationsoption in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="a8949-188">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="work-with-windows-authentication"></a><span data-ttu-id="a8949-189">Arbeiten mit Windows-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="a8949-189">Work with Windows Authentication</span></span>

<span data-ttu-id="a8949-190">Der Konfigurationsstatus des anonymen Zugriffs bestimmt die Art, wie die `[Authorize]` und `[AllowAnonymous]` Attribute werden in der app verwendet.</span><span class="sxs-lookup"><span data-stu-id="a8949-190">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="a8949-191">In den folgenden zwei Abschnitten wird erläutert, wie die nicht zulässigen und die zulässigen Zustände des anonymen Zugriffs zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="a8949-191">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="a8949-192">Anonyme Zugriffe nicht zulassen</span><span class="sxs-lookup"><span data-stu-id="a8949-192">Disallow anonymous access</span></span>

<span data-ttu-id="a8949-193">Wenn Windows-Authentifizierung aktiviert ist und der anonyme Zugriff deaktiviert ist, die `[Authorize]` und `[AllowAnonymous]` Attribute haben keine Auswirkung.</span><span class="sxs-lookup"><span data-stu-id="a8949-193">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="a8949-194">Wenn der IIS-Site (oder HTTP.sys) konfiguriert ist, um den anonymen Zugriff verweigern, erreicht die Anforderung nie Ihrer app.</span><span class="sxs-lookup"><span data-stu-id="a8949-194">If the IIS site (or HTTP.sys) is configured to disallow anonymous access, the request never reaches your app.</span></span> <span data-ttu-id="a8949-195">Aus diesem Grund die `[AllowAnonymous]` Attribut ist nicht anwendbar.</span><span class="sxs-lookup"><span data-stu-id="a8949-195">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="a8949-196">Anonymen Zugriff zulassen</span><span class="sxs-lookup"><span data-stu-id="a8949-196">Allow anonymous access</span></span>

<span data-ttu-id="a8949-197">Wenn sowohl für Windows-Authentifizierung als auch für anonymen Zugriff aktiviert sind, verwenden die `[Authorize]` und `[AllowAnonymous]` Attribute.</span><span class="sxs-lookup"><span data-stu-id="a8949-197">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="a8949-198">Die `[Authorize]` Attribut können Sie Teile der app schützen, das wirklich Windows-Authentifizierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="a8949-198">The `[Authorize]` attribute allows you to secure pieces of the app which truly do require Windows Authentication.</span></span> <span data-ttu-id="a8949-199">Die `[AllowAnonymous]` -Attribut überschreibt `[Authorize]` Attribut Nutzung in apps, die anonymen Zugriff zulassen.</span><span class="sxs-lookup"><span data-stu-id="a8949-199">The `[AllowAnonymous]` attribute overrides `[Authorize]` attribute usage within apps which allow anonymous access.</span></span> <span data-ttu-id="a8949-200">Finden Sie unter [einfache Autorisierung](xref:security/authorization/simple) Nutzungsdetails Attribut.</span><span class="sxs-lookup"><span data-stu-id="a8949-200">See [Simple Authorization](xref:security/authorization/simple) for attribute usage details.</span></span>

<span data-ttu-id="a8949-201">In ASP.NET Core 2.x, die `[Authorize]` Attribut erfordert zusätzliche Konfigurationsschritte in *"Startup.cs"* sollen anonyme Anforderungen für die Windows-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="a8949-201">In ASP.NET Core 2.x, the `[Authorize]` attribute requires additional configuration in *Startup.cs* to challenge anonymous requests for Windows Authentication.</span></span> <span data-ttu-id="a8949-202">Die empfohlene Konfiguration variiert leicht basierend auf dem Webserver verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a8949-202">The recommended configuration varies slightly based on the web server being used.</span></span>

> [!NOTE]
> <span data-ttu-id="a8949-203">Standardmäßig werden Benutzer, die Autorisierung zum Zugriff auf eine Seite nicht über eine leere HTTP 403-Antwort angezeigt.</span><span class="sxs-lookup"><span data-stu-id="a8949-203">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="a8949-204">Die [StatusCodePages Middleware](xref:fundamentals/error-handling#configure-status-code-pages) kann konfiguriert werden, um Benutzern mit "Zugriff verweigert" Benutzerfreundlicher zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="a8949-204">The [StatusCodePages middleware](xref:fundamentals/error-handling#configure-status-code-pages) can be configured to provide users with a better "Access Denied" experience.</span></span>

#### <a name="iis"></a><span data-ttu-id="a8949-205">IIS</span><span class="sxs-lookup"><span data-stu-id="a8949-205">IIS</span></span>

<span data-ttu-id="a8949-206">Wenn Sie IIS verwenden, fügen Sie den folgenden der `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="a8949-206">If using IIS, add the following to the `ConfigureServices` method:</span></span>

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a><span data-ttu-id="a8949-207">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="a8949-207">HTTP.sys</span></span>

<span data-ttu-id="a8949-208">Wenn Sie "http.sys" verwenden, fügen Sie den folgenden der `ConfigureServices` Methode:</span><span class="sxs-lookup"><span data-stu-id="a8949-208">If using HTTP.sys, add the following to the `ConfigureServices` method:</span></span>

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a><span data-ttu-id="a8949-209">Identitätswechsel</span><span class="sxs-lookup"><span data-stu-id="a8949-209">Impersonation</span></span>

<span data-ttu-id="a8949-210">ASP.NET Core implementiert keine Identitätswechsel.</span><span class="sxs-lookup"><span data-stu-id="a8949-210">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="a8949-211">Apps, die mit der app Identität für alle Anforderungen, die mithilfe von app-Pool oder Prozess Identität ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a8949-211">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="a8949-212">Wenn Sie explizit eine Aktion im Auftrag eines Benutzers ausführen müssen, verwenden Sie [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in einem [terminal Inline-Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="a8949-212">If you need to explicitly perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="a8949-213">Eine einzelne Aktion in diesem Kontext ausgeführt, und schließen Sie den Kontext.</span><span class="sxs-lookup"><span data-stu-id="a8949-213">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="a8949-214">`RunImpersonated` unterstützt keine asynchronen Vorgänge und sollte nicht für komplexe Szenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a8949-214">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="a8949-215">Z. B. wird nicht wrapping gesamte Anforderungen oder Middleware verkettet unterstützt oder empfohlen.</span><span class="sxs-lookup"><span data-stu-id="a8949-215">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>
