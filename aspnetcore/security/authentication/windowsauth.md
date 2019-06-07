---
title: Konfigurieren der Windows-Authentifizierung in ASP.NET Core
author: scottaddie
description: Erfahren Sie, wie Windows-Authentifizierung in ASP.NET Core für IIS und HTTP.sys zu konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/05/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 900bbf5f14b1876ad537b2b77e4ba07d7aa168f2
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750164"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="e61fd-103">Konfigurieren der Windows-Authentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="e61fd-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="e61fd-104">Durch [Scott Addie](https://twitter.com/Scott_Addie) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e61fd-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e61fd-105">[Windows-Authentifizierung](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) kann konfiguriert werden, für ASP.NET Core-apps mit gehosteten [IIS](xref:host-and-deploy/iis/index) oder [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="e61fd-105">[Windows Authentication](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="e61fd-106">Windows-Authentifizierung basiert auf dem Betriebssystem zur Authentifizierung von Benutzern von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="e61fd-106">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="e61fd-107">Sie können Windows-Authentifizierung verwenden, wenn Ihre Server in einem Unternehmensnetzwerk, die mithilfe von Active Directory-domänenidentitäten oder Windows-Konten zur Identifizierung von Benutzern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="e61fd-107">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="e61fd-108">Windows-Authentifizierung ist am besten für Intranetumgebungen, in dem Benutzer, Client-apps und Webserver mit der gleichen Windows-Domäne gehören.</span><span class="sxs-lookup"><span data-stu-id="e61fd-108">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="e61fd-109">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="e61fd-109">IIS/IIS Express</span></span>

<span data-ttu-id="e61fd-110">Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> Namespace) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e61fd-110">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="e61fd-111">Starteinstellungen Sie (Debugger)</span><span class="sxs-lookup"><span data-stu-id="e61fd-111">Launch settings (debugger)</span></span>

<span data-ttu-id="e61fd-112">Konfiguration für die starteinstellungen für den wirkt sich nur auf die *Properties/launchSettings.json* für IIS Express-Datei, und Konfigurieren von IIS für Windows-Authentifizierung nicht.</span><span class="sxs-lookup"><span data-stu-id="e61fd-112">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="e61fd-113">Server-Konfiguration wird erläutert, der [IIS](#iis) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-113">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="e61fd-114">Die **Webanwendung** Vorlage zur Verfügung, über Visual Studio oder .NET Core-CLI kann konfiguriert werden, zur Unterstützung von Windows-Authentifizierung, aktualisiert der *Properties/launchSettings.json* Datei automatisch.</span><span class="sxs-lookup"><span data-stu-id="e61fd-114">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="e61fd-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e61fd-115">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e61fd-116">**Neues Projekt**</span><span class="sxs-lookup"><span data-stu-id="e61fd-116">**New project**</span></span>

1. <span data-ttu-id="e61fd-117">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-117">Create a new project.</span></span>
1. <span data-ttu-id="e61fd-118">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="e61fd-118">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="e61fd-119">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-119">Select **Next**.</span></span>
1. <span data-ttu-id="e61fd-120">Geben Sie einen Namen in der **Projektname** Feld.</span><span class="sxs-lookup"><span data-stu-id="e61fd-120">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="e61fd-121">Vergewissern Sie sich die **Speicherort** Eintrag richtig ist, oder geben Sie einen Speicherort für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-121">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="e61fd-122">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="e61fd-122">Select **Create**.</span></span>
1. <span data-ttu-id="e61fd-123">Wählen Sie **Änderung** unter **Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-123">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="e61fd-124">In der **Authentifizierung ändern** wählen Sie im Fenster **Windows-Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-124">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="e61fd-125">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-125">Select **OK**.</span></span>
1. <span data-ttu-id="e61fd-126">Wählen Sie **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="e61fd-126">Select **Web Application**.</span></span>
1. <span data-ttu-id="e61fd-127">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="e61fd-127">Select **Create**.</span></span>

<span data-ttu-id="e61fd-128">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="e61fd-128">Run the app.</span></span> <span data-ttu-id="e61fd-129">Der Benutzername wird in der gerenderten app-Benutzeroberfläche angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-129">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="e61fd-130">**Vorhandenes Projekt**</span><span class="sxs-lookup"><span data-stu-id="e61fd-130">**Existing project**</span></span>

<span data-ttu-id="e61fd-131">Die Eigenschaften des Projekts Windows-Authentifizierung aktivieren und deaktivieren Sie die anonyme Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="e61fd-131">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="e61fd-132">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="e61fd-132">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="e61fd-133">Klicken Sie auf die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-133">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="e61fd-134">Deaktivieren Sie das Kontrollkästchen für **Aktivieren der anonymen Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-134">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="e61fd-135">Wählen Sie das Kontrollkästchen für **Windows-Authentifizierung aktivieren**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-135">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="e61fd-136">Speichern Sie und schließen Sie die Eigenschaftenseite.</span><span class="sxs-lookup"><span data-stu-id="e61fd-136">Save and close the property page.</span></span>

<span data-ttu-id="e61fd-137">Alternativ können die Eigenschaften konfiguriert werden, der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="e61fd-137">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="e61fd-138">Visual Studio Code / .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="e61fd-138">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="e61fd-139">**Neues Projekt**</span><span class="sxs-lookup"><span data-stu-id="e61fd-139">**New project**</span></span>

<span data-ttu-id="e61fd-140">Führen Sie die [Dotnet neue](/dotnet/core/tools/dotnet-new) -Befehl mit der `webapp` Argument (ASP.NET Core-Web-App) und `--auth Windows` wechseln:</span><span class="sxs-lookup"><span data-stu-id="e61fd-140">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="e61fd-141">**Vorhandenes Projekt**</span><span class="sxs-lookup"><span data-stu-id="e61fd-141">**Existing project**</span></span>

<span data-ttu-id="e61fd-142">Update der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="e61fd-142">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="e61fd-143">Wenn Sie ein vorhandenes Projekt ändern möchten, vergewissern Sie sich, dass die Projektdatei für die Paketverweise enthält die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) **oder** der [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="e61fd-143">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="e61fd-144">IIS</span><span class="sxs-lookup"><span data-stu-id="e61fd-144">IIS</span></span>

<span data-ttu-id="e61fd-145">IIS verwendet den [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zum Hosten von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="e61fd-145">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="e61fd-146">Windows-Authentifizierung konfiguriert ist, für IIS über die *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="e61fd-146">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="e61fd-147">Die folgenden Abschnitte zeigen Gewusst wie:</span><span class="sxs-lookup"><span data-stu-id="e61fd-147">The following sections show how to:</span></span>

* <span data-ttu-id="e61fd-148">Geben Sie eine lokale *"Web.config"* -Datei, die Windows-Authentifizierung auf dem Server aktiviert, wenn die app bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="e61fd-148">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="e61fd-149">Verwenden Sie den IIS-Manager so konfigurieren Sie die *"Web.config"* Datei von einer ASP.NET Core-app, die bereits auf dem Server bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="e61fd-149">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="e61fd-150">Wenn Sie nicht bereits geschehen, aktivieren Sie IIS zum Hosten von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="e61fd-150">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="e61fd-151">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="e61fd-151">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="e61fd-152">Den Rollendienst "IIS" für Windows-Authentifizierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="e61fd-152">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="e61fd-153">Weitere Informationen finden Sie unter [Aktivieren der Windows-Authentifizierung in IIS-Rollendienste (Siehe Schritt2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="e61fd-153">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="e61fd-154">[Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird standardmäßig zum Authentifizieren von Anforderungen automatisch konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e61fd-154">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="e61fd-155">Weitere Informationen finden Sie unter [Hosten von ASP.NET Core unter Windows mit IIS: IIS-Optionen (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="e61fd-155">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="e61fd-156">ASP.NET Core-Modul ist zum Weiterleiten von der Windows-Authentifizierung-Token für die app wird standardmäßig konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="e61fd-156">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="e61fd-157">Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul: Attribute des-Elements AspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="e61fd-157">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="e61fd-158">Verwendung **entweder** der folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="e61fd-158">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="e61fd-159">**Vor der Veröffentlichung und Bereitstellung des Projekts** fügen Sie die folgenden *"Web.config"* Datei in das Stammverzeichnis des Projekts:</span><span class="sxs-lookup"><span data-stu-id="e61fd-159">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="e61fd-160">Wenn das Projekt veröffentlicht wird, durch das .NET Core SDK (ohne die `<IsTransformWebConfigDisabled>` -Eigenschaft auf festgelegt `true` in der Projektdatei), die veröffentlichte *"Web.config"* -Datei enthält die `<location><system.webServer><security><authentication>` Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-160">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="e61fd-161">Weitere Informationen zu den `<IsTransformWebConfigDisabled>` -Eigenschaft finden Sie unter <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="e61fd-161">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="e61fd-162">**Nach der Veröffentlichung und Bereitstellung des Projekts** : serverseitige Konfiguration mit dem IIS-Manager ausführen:</span><span class="sxs-lookup"><span data-stu-id="e61fd-162">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="e61fd-163">Im IIS-Manager, wählen Sie die IIS-Website unter der **Websites** Knoten die **Verbindungen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="e61fd-163">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="e61fd-164">Doppelklicken Sie auf **Authentifizierung** in die **IIS** Bereich.</span><span class="sxs-lookup"><span data-stu-id="e61fd-164">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="e61fd-165">Wählen Sie **anonyme Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-165">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="e61fd-166">Wählen Sie **deaktivieren** in die **Aktionen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="e61fd-166">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="e61fd-167">Wählen Sie **Windows-Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="e61fd-167">Select **Windows Authentication**.</span></span> <span data-ttu-id="e61fd-168">Wählen Sie **aktivieren** in die **Aktionen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="e61fd-168">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="e61fd-169">Wenn diese Aktionen ausgeführt werden, ändert der IIS-Manager der app *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="e61fd-169">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="e61fd-170">Ein `<system.webServer><security><authentication>` Knoten wird hinzugefügt, mit aktualisierten Einstellungen für `anonymousAuthentication` und `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="e61fd-170">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="e61fd-171">Die `<system.webServer>` Abschnitts in der *"Web.config"* Datei vom IIS-Manager befindet sich außerhalb der app `<location>` Abschnitt durch die .NET Core SDK hinzugefügt wird, wenn die Anwendung veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="e61fd-171">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="e61fd-172">Da der Abschnitt, außerhalb von hinzugefügt wird den `<location>` Knoten werden die Einstellungen von einem geerbt [untergeordnete apps, die](xref:host-and-deploy/iis/index#sub-applications) der aktuellen App.</span><span class="sxs-lookup"><span data-stu-id="e61fd-172">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="e61fd-173">Um Vererbung zu verhindern, verschieben Sie die hinzugefügte `<security>` Abschnitt in der die `<location><system.webServer>` -Abschnitt, der das .NET Core SDK bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-173">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="e61fd-174">Wenn IIS-Manager verwendet wird, um die IIS-Konfiguration hinzuzufügen, es wirkt sich nur auf der app *"Web.config"* Datei auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="e61fd-174">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="e61fd-175">Eine anschließende Bereitstellung der app möglicherweise die Einstellungen auf dem Server überschreiben, wenn die Kopie des Servers der *"Web.config"* durch des Projekts ersetzt *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="e61fd-175">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="e61fd-176">Verwendung **entweder** der folgenden Methoden zum Verwalten der Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="e61fd-176">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="e61fd-177">Verwenden Sie IIS-Manager zum Zurücksetzen der Einstellungen in der *"Web.config"* -Datei nach der Bereitstellung die Datei überschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="e61fd-177">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="e61fd-178">Hinzufügen einer *Datei "Web.config"* an die app lokal mit den Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="e61fd-178">Add a *web.config file* to the app locally with the settings.</span></span>

## <a name="httpsys"></a><span data-ttu-id="e61fd-179">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="e61fd-179">HTTP.sys</span></span>

<span data-ttu-id="e61fd-180">In selbstgehosteten Szenarien [Kestrel](xref:fundamentals/servers/kestrel) keine Unterstützung von Windows-Authentifizierung, aber Sie können [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="e61fd-180">In self-hosted scenarios, [Kestrel](xref:fundamentals/servers/kestrel) doesn't support Windows Authentication, but you can use [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

<span data-ttu-id="e61fd-181">Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> Namespace) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="e61fd-181">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="e61fd-182">Konfigurieren der app-Web-Host zur Verwendung von HTTP.sys mit Windows-Authentifizierung ( *"Program.cs"* ).</span><span class="sxs-lookup"><span data-stu-id="e61fd-182">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="e61fd-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> befindet sich in der <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> Namespace.</span><span class="sxs-lookup"><span data-stu-id="e61fd-183"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="e61fd-184">HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll.</span><span class="sxs-lookup"><span data-stu-id="e61fd-184">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="e61fd-185">Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-185">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="e61fd-186">Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="e61fd-186">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="e61fd-187">Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App.</span><span class="sxs-lookup"><span data-stu-id="e61fd-187">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="e61fd-188">HTTP.sys wird nicht auf Nano Server-Version 1709 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-188">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="e61fd-189">Um Windows-Authentifizierung und HTTP.sys mit Nano Server verwenden möchten, verwenden eine [Server Core (Microsoft/Windowsservercore) Container](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="e61fd-189">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="e61fd-190">Weitere Informationen zur Server Core finden Sie unter [Was ist, dass der Server Core-Installationsoption in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="e61fd-190">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="e61fd-191">Autorisieren von Benutzern</span><span class="sxs-lookup"><span data-stu-id="e61fd-191">Authorize users</span></span>

<span data-ttu-id="e61fd-192">Der Konfigurationsstatus des anonymen Zugriffs bestimmt die Art, wie die `[Authorize]` und `[AllowAnonymous]` Attribute werden in der app verwendet.</span><span class="sxs-lookup"><span data-stu-id="e61fd-192">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="e61fd-193">In den folgenden zwei Abschnitten wird erläutert, wie die nicht zulässigen und die zulässigen Zustände des anonymen Zugriffs zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="e61fd-193">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="e61fd-194">Anonyme Zugriffe nicht zulassen</span><span class="sxs-lookup"><span data-stu-id="e61fd-194">Disallow anonymous access</span></span>

<span data-ttu-id="e61fd-195">Wenn Windows-Authentifizierung aktiviert ist und der anonyme Zugriff deaktiviert ist, die `[Authorize]` und `[AllowAnonymous]` Attribute haben keine Auswirkung.</span><span class="sxs-lookup"><span data-stu-id="e61fd-195">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="e61fd-196">Wenn eine IIS-Website konfiguriert ist, um den anonymen Zugriff verweigern, erreicht die Anforderung nie die app.</span><span class="sxs-lookup"><span data-stu-id="e61fd-196">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="e61fd-197">Aus diesem Grund die `[AllowAnonymous]` Attribut ist nicht anwendbar.</span><span class="sxs-lookup"><span data-stu-id="e61fd-197">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="e61fd-198">Anonymen Zugriff zulassen</span><span class="sxs-lookup"><span data-stu-id="e61fd-198">Allow anonymous access</span></span>

<span data-ttu-id="e61fd-199">Wenn sowohl für Windows-Authentifizierung als auch für anonymen Zugriff aktiviert sind, verwenden die `[Authorize]` und `[AllowAnonymous]` Attribute.</span><span class="sxs-lookup"><span data-stu-id="e61fd-199">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="e61fd-200">Die `[Authorize]` Attribut können Sie die Endpunkte der app schützen, das Authentifizierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="e61fd-200">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="e61fd-201">Die `[AllowAnonymous]` -Attribut überschreibt die `[Authorize]` -Attribut in apps, die den anonymen Zugriff zulassen.</span><span class="sxs-lookup"><span data-stu-id="e61fd-201">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="e61fd-202">Attribut Verwendungsdetails finden Sie unter <xref:security/authorization/simple>.</span><span class="sxs-lookup"><span data-stu-id="e61fd-202">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="e61fd-203">Standardmäßig werden Benutzer, die Autorisierung zum Zugriff auf eine Seite nicht über eine leere HTTP 403-Antwort angezeigt.</span><span class="sxs-lookup"><span data-stu-id="e61fd-203">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="e61fd-204">Die [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) kann konfiguriert werden, um Benutzern mit "Zugriff verweigert" Benutzerfreundlicher zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="e61fd-204">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="e61fd-205">Identitätswechsel</span><span class="sxs-lookup"><span data-stu-id="e61fd-205">Impersonation</span></span>

<span data-ttu-id="e61fd-206">ASP.NET Core implementiert keine Identitätswechsel.</span><span class="sxs-lookup"><span data-stu-id="e61fd-206">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="e61fd-207">Apps, die mit der app Identität für alle Anforderungen, die mithilfe von app-Pool oder Prozess Identität ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="e61fd-207">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="e61fd-208">Wenn die app eine Aktion im Auftrag eines Benutzers ausführen soll, verwenden Sie [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in einem [terminal Inline-Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="e61fd-208">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="e61fd-209">Eine einzelne Aktion in diesem Kontext ausgeführt, und schließen Sie den Kontext.</span><span class="sxs-lookup"><span data-stu-id="e61fd-209">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="e61fd-210">`RunImpersonated` unterstützt keine asynchronen Vorgänge und sollte nicht für komplexe Szenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="e61fd-210">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="e61fd-211">Z. B. wird nicht wrapping gesamte Anforderungen oder Middleware verkettet unterstützt oder empfohlen.</span><span class="sxs-lookup"><span data-stu-id="e61fd-211">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

## <a name="claims-transformations"></a><span data-ttu-id="e61fd-212">Transformationen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="e61fd-212">Claims transformations</span></span>

<span data-ttu-id="e61fd-213">Wenn Sie mit in-Process-Modus von IIS hosten <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> wird nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="e61fd-213">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="e61fd-214">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="e61fd-214">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="e61fd-215">Weitere Informationen und ein Codebeispiel, das Transformationen von Ansprüchen, das aktiviert wird, wenn prozessinternes hosting, finden Sie unter <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="e61fd-215">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e61fd-216">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="e61fd-216">Additional resources</span></span>

* [<span data-ttu-id="e61fd-217">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="e61fd-217">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
