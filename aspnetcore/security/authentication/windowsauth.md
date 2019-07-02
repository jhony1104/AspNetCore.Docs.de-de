---
title: Konfigurieren der Windows-Authentifizierung in ASP.NET Core
author: scottaddie
description: Erfahren Sie, wie Windows-Authentifizierung in ASP.NET Core für IIS und HTTP.sys zu konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 07/01/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: 30f1f554a29412ed6b84115d457d2da1aba91c17
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500498"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a><span data-ttu-id="40f17-103">Konfigurieren der Windows-Authentifizierung in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40f17-103">Configure Windows Authentication in ASP.NET Core</span></span>

<span data-ttu-id="40f17-104">Durch [Scott Addie](https://twitter.com/Scott_Addie) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="40f17-104">By [Scott Addie](https://twitter.com/Scott_Addie) and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40f17-105">Windows-Authentifizierung (auch bekannt als Negotiate, Kerberos oder NTLM-Authentifizierung) kann konfiguriert werden, für ASP.NET Core-apps mit gehosteten [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), oder [HTTP.sys](xref:fundamentals/servers/httpsys) .</span><span class="sxs-lookup"><span data-stu-id="40f17-105">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="40f17-106">Windows-Authentifizierung (auch bekannt als Negotiate, Kerberos oder NTLM-Authentifizierung) kann konfiguriert werden, für ASP.NET Core-apps mit gehosteten [IIS](xref:host-and-deploy/iis/index) oder [HTTP.sys](xref:fundamentals/servers/httpsys).</span><span class="sxs-lookup"><span data-stu-id="40f17-106">Windows Authentication (also known as Negotiate, Kerberos, or NTLM authentication) can be configured for ASP.NET Core apps hosted with [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys).</span></span>

::: moniker-end

<span data-ttu-id="40f17-107">Windows-Authentifizierung basiert auf dem Betriebssystem zur Authentifizierung von Benutzern von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="40f17-107">Windows Authentication relies on the operating system to authenticate users of ASP.NET Core apps.</span></span> <span data-ttu-id="40f17-108">Sie können Windows-Authentifizierung verwenden, wenn Ihre Server in einem Unternehmensnetzwerk, die mithilfe von Active Directory-domänenidentitäten oder Windows-Konten zur Identifizierung von Benutzern ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="40f17-108">You can use Windows Authentication when your server runs on a corporate network using Active Directory domain identities or Windows accounts to identify users.</span></span> <span data-ttu-id="40f17-109">Windows-Authentifizierung ist am besten für Intranetumgebungen, in dem Benutzer, Client-apps und Webserver mit der gleichen Windows-Domäne gehören.</span><span class="sxs-lookup"><span data-stu-id="40f17-109">Windows Authentication is best suited to intranet environments where users, client apps, and web servers belong to the same Windows domain.</span></span>

> [!NOTE]
> <span data-ttu-id="40f17-110">Windows-Authentifizierung mit HTTP/2 wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="40f17-110">Windows Authentication isn't supported with HTTP/2.</span></span> <span data-ttu-id="40f17-111">-Authentifizierungsaufforderungen gesendet werden können, über HTTP/2-Antworten, aber der Client muss auf HTTP/1.1 Führen Sie vor dem authentifizieren.</span><span class="sxs-lookup"><span data-stu-id="40f17-111">Authentication challenges can be sent on HTTP/2 responses, but the client must downgrade to HTTP/1.1 before authenticating.</span></span>

## <a name="iisiis-express"></a><span data-ttu-id="40f17-112">IIS/IIS Express</span><span class="sxs-lookup"><span data-stu-id="40f17-112">IIS/IIS Express</span></span>

<span data-ttu-id="40f17-113">Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> Namespace) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="40f17-113">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a><span data-ttu-id="40f17-114">Starteinstellungen Sie (Debugger)</span><span class="sxs-lookup"><span data-stu-id="40f17-114">Launch settings (debugger)</span></span>

<span data-ttu-id="40f17-115">Konfiguration für die starteinstellungen für den wirkt sich nur auf die *Properties/launchSettings.json* für IIS Express-Datei, und Konfigurieren von IIS für Windows-Authentifizierung nicht.</span><span class="sxs-lookup"><span data-stu-id="40f17-115">Configuration for launch settings only affects the *Properties/launchSettings.json* file for IIS Express and doesn't configure IIS for Windows Authentication.</span></span> <span data-ttu-id="40f17-116">Server-Konfiguration wird erläutert, der [IIS](#iis) Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="40f17-116">Server configuration is explained in the [IIS](#iis) section.</span></span>

<span data-ttu-id="40f17-117">Die **Webanwendung** Vorlage zur Verfügung, über Visual Studio oder .NET Core-CLI kann konfiguriert werden, zur Unterstützung von Windows-Authentifizierung, aktualisiert der *Properties/launchSettings.json* Datei automatisch.</span><span class="sxs-lookup"><span data-stu-id="40f17-117">The **Web Application** template available via Visual Studio or the .NET Core CLI can be configured to support Windows Authentication, which updates the *Properties/launchSettings.json* file automatically.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="40f17-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="40f17-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="40f17-119">**Neues Projekt**</span><span class="sxs-lookup"><span data-stu-id="40f17-119">**New project**</span></span>

1. <span data-ttu-id="40f17-120">Erstellen Sie ein neues Projekt.</span><span class="sxs-lookup"><span data-stu-id="40f17-120">Create a new project.</span></span>
1. <span data-ttu-id="40f17-121">Wählen Sie **ASP.NET Core-Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="40f17-121">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="40f17-122">Klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="40f17-122">Select **Next**.</span></span>
1. <span data-ttu-id="40f17-123">Geben Sie einen Namen in der **Projektname** Feld.</span><span class="sxs-lookup"><span data-stu-id="40f17-123">Provide a name in the **Project name** field.</span></span> <span data-ttu-id="40f17-124">Vergewissern Sie sich die **Speicherort** Eintrag richtig ist, oder geben Sie einen Speicherort für das Projekt.</span><span class="sxs-lookup"><span data-stu-id="40f17-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="40f17-125">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="40f17-125">Select **Create**.</span></span>
1. <span data-ttu-id="40f17-126">Wählen Sie **Änderung** unter **Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="40f17-126">Select **Change** under **Authentication**.</span></span>
1. <span data-ttu-id="40f17-127">In der **Authentifizierung ändern** wählen Sie im Fenster **Windows-Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="40f17-127">In the **Change Authentication** window, select **Windows Authentication**.</span></span> <span data-ttu-id="40f17-128">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="40f17-128">Select **OK**.</span></span>
1. <span data-ttu-id="40f17-129">Wählen Sie **Webanwendung** aus.</span><span class="sxs-lookup"><span data-stu-id="40f17-129">Select **Web Application**.</span></span>
1. <span data-ttu-id="40f17-130">Wählen Sie **Erstellen** aus.</span><span class="sxs-lookup"><span data-stu-id="40f17-130">Select **Create**.</span></span>

<span data-ttu-id="40f17-131">Führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="40f17-131">Run the app.</span></span> <span data-ttu-id="40f17-132">Der Benutzername wird in der gerenderten app-Benutzeroberfläche angezeigt.</span><span class="sxs-lookup"><span data-stu-id="40f17-132">The username appears in the rendered app's user interface.</span></span>

<span data-ttu-id="40f17-133">**Vorhandenes Projekt**</span><span class="sxs-lookup"><span data-stu-id="40f17-133">**Existing project**</span></span>

<span data-ttu-id="40f17-134">Die Eigenschaften des Projekts Windows-Authentifizierung aktivieren und deaktivieren Sie die anonyme Authentifizierung:</span><span class="sxs-lookup"><span data-stu-id="40f17-134">The project's properties enable Windows Authentication and disable Anonymous Authentication:</span></span>

1. <span data-ttu-id="40f17-135">Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.</span><span class="sxs-lookup"><span data-stu-id="40f17-135">Right-click the project in **Solution Explorer** and select **Properties**.</span></span>
1. <span data-ttu-id="40f17-136">Klicken Sie auf die Registerkarte **Debuggen**.</span><span class="sxs-lookup"><span data-stu-id="40f17-136">Select the **Debug** tab.</span></span>
1. <span data-ttu-id="40f17-137">Deaktivieren Sie das Kontrollkästchen für **Aktivieren der anonymen Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="40f17-137">Clear the check box for **Enable Anonymous Authentication**.</span></span>
1. <span data-ttu-id="40f17-138">Wählen Sie das Kontrollkästchen für **Windows-Authentifizierung aktivieren**.</span><span class="sxs-lookup"><span data-stu-id="40f17-138">Select the check box for **Enable Windows Authentication**.</span></span>
1. <span data-ttu-id="40f17-139">Speichern Sie und schließen Sie die Eigenschaftenseite.</span><span class="sxs-lookup"><span data-stu-id="40f17-139">Save and close the property page.</span></span>

<span data-ttu-id="40f17-140">Alternativ können die Eigenschaften konfiguriert werden, der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="40f17-140">Alternatively, the properties can be configured in the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[<span data-ttu-id="40f17-141">Visual Studio Code / .NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="40f17-141">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="40f17-142">**Neues Projekt**</span><span class="sxs-lookup"><span data-stu-id="40f17-142">**New project**</span></span>

<span data-ttu-id="40f17-143">Führen Sie die [Dotnet neue](/dotnet/core/tools/dotnet-new) -Befehl mit der `webapp` Argument (ASP.NET Core-Web-App) und `--auth Windows` wechseln:</span><span class="sxs-lookup"><span data-stu-id="40f17-143">Execute the [dotnet new](/dotnet/core/tools/dotnet-new) command with the `webapp` argument (ASP.NET Core Web App) and `--auth Windows` switch:</span></span>

```console
dotnet new webapp --auth Windows
```

<span data-ttu-id="40f17-144">**Vorhandenes Projekt**</span><span class="sxs-lookup"><span data-stu-id="40f17-144">**Existing project**</span></span>

<span data-ttu-id="40f17-145">Update der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:</span><span class="sxs-lookup"><span data-stu-id="40f17-145">Update the `iisSettings` node of the *launchSettings.json* file:</span></span>

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

<span data-ttu-id="40f17-146">Wenn Sie ein vorhandenes Projekt ändern möchten, vergewissern Sie sich, dass die Projektdatei für die Paketverweise enthält die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) **oder** der [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="40f17-146">When modifying an existing project, confirm that the project file includes a package reference for the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) **or** the [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet package.</span></span>

### <a name="iis"></a><span data-ttu-id="40f17-147">IIS</span><span class="sxs-lookup"><span data-stu-id="40f17-147">IIS</span></span>

<span data-ttu-id="40f17-148">IIS verwendet den [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zum Hosten von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="40f17-148">IIS uses the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) to host ASP.NET Core apps.</span></span> <span data-ttu-id="40f17-149">Windows-Authentifizierung konfiguriert ist, für IIS über die *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="40f17-149">Windows Authentication is configured for IIS via the *web.config* file.</span></span> <span data-ttu-id="40f17-150">Die folgenden Abschnitte zeigen Gewusst wie:</span><span class="sxs-lookup"><span data-stu-id="40f17-150">The following sections show how to:</span></span>

* <span data-ttu-id="40f17-151">Geben Sie eine lokale *"Web.config"* -Datei, die Windows-Authentifizierung auf dem Server aktiviert, wenn die app bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="40f17-151">Provide a local *web.config* file that activates Windows Authentication on the server when the app is deployed.</span></span>
* <span data-ttu-id="40f17-152">Verwenden Sie den IIS-Manager so konfigurieren Sie die *"Web.config"* Datei von einer ASP.NET Core-app, die bereits auf dem Server bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="40f17-152">Use the IIS Manager to configure the *web.config* file of an ASP.NET Core app that has already been deployed to the server.</span></span>

<span data-ttu-id="40f17-153">Wenn Sie nicht bereits geschehen, aktivieren Sie IIS zum Hosten von ASP.NET Core-apps.</span><span class="sxs-lookup"><span data-stu-id="40f17-153">If you haven't already done so, enable IIS to host ASP.NET Core apps.</span></span> <span data-ttu-id="40f17-154">Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index>.</span><span class="sxs-lookup"><span data-stu-id="40f17-154">For more information, see <xref:host-and-deploy/iis/index>.</span></span>

<span data-ttu-id="40f17-155">Den Rollendienst "IIS" für Windows-Authentifizierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="40f17-155">Enable the IIS Role Service for Windows Authentication.</span></span> <span data-ttu-id="40f17-156">Weitere Informationen finden Sie unter [Aktivieren der Windows-Authentifizierung in IIS-Rollendienste (Siehe Schritt2)](xref:host-and-deploy/iis/index#iis-configuration).</span><span class="sxs-lookup"><span data-stu-id="40f17-156">For more information, see [Enable Windows Authentication in IIS Role Services (see Step 2)](xref:host-and-deploy/iis/index#iis-configuration).</span></span>

<span data-ttu-id="40f17-157">[Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird standardmäßig zum Authentifizieren von Anforderungen automatisch konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="40f17-157">[IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) is configured to automatically authenticate requests by default.</span></span> <span data-ttu-id="40f17-158">Weitere Informationen finden Sie unter [Hosten von ASP.NET Core unter Windows mit IIS: IIS-Optionen (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span><span class="sxs-lookup"><span data-stu-id="40f17-158">For more information, see [Host ASP.NET Core on Windows with IIS: IIS options (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).</span></span>

<span data-ttu-id="40f17-159">ASP.NET Core-Modul ist zum Weiterleiten von der Windows-Authentifizierung-Token für die app wird standardmäßig konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="40f17-159">The ASP.NET Core Module is configured to forward the Windows Authentication token to the app by default.</span></span> <span data-ttu-id="40f17-160">Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul: Attribute des-Elements AspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span><span class="sxs-lookup"><span data-stu-id="40f17-160">For more information, see [ASP.NET Core Module configuration reference: Attributes of the aspNetCore element](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).</span></span>

<span data-ttu-id="40f17-161">Verwendung **entweder** der folgenden Methoden:</span><span class="sxs-lookup"><span data-stu-id="40f17-161">Use **either** of the following approaches:</span></span>

* <span data-ttu-id="40f17-162">**Vor der Veröffentlichung und Bereitstellung des Projekts** fügen Sie die folgenden *"Web.config"* Datei in das Stammverzeichnis des Projekts:</span><span class="sxs-lookup"><span data-stu-id="40f17-162">**Before publishing and deploying the project,** add the following *web.config* file to the project root:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  <span data-ttu-id="40f17-163">Wenn das Projekt veröffentlicht wird, durch das .NET Core SDK (ohne die `<IsTransformWebConfigDisabled>` -Eigenschaft auf festgelegt `true` in der Projektdatei), die veröffentlichte *"Web.config"* -Datei enthält die `<location><system.webServer><security><authentication>` Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="40f17-163">When the project is published by the .NET Core SDK (without the `<IsTransformWebConfigDisabled>` property set to `true` in the project file), the published *web.config* file includes the `<location><system.webServer><security><authentication>` section.</span></span> <span data-ttu-id="40f17-164">Weitere Informationen zu den `<IsTransformWebConfigDisabled>` -Eigenschaft finden Sie unter <xref:host-and-deploy/iis/index#webconfig-file>.</span><span class="sxs-lookup"><span data-stu-id="40f17-164">For more information on the `<IsTransformWebConfigDisabled>` property, see <xref:host-and-deploy/iis/index#webconfig-file>.</span></span>

* <span data-ttu-id="40f17-165">**Nach der Veröffentlichung und Bereitstellung des Projekts** : serverseitige Konfiguration mit dem IIS-Manager ausführen:</span><span class="sxs-lookup"><span data-stu-id="40f17-165">**After publishing and deploying the project,** perform server-side configuration with the IIS Manager:</span></span>

  1. <span data-ttu-id="40f17-166">Im IIS-Manager, wählen Sie die IIS-Website unter der **Websites** Knoten die **Verbindungen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="40f17-166">In IIS Manager, select the IIS site under the **Sites** node of the **Connections** sidebar.</span></span>
  1. <span data-ttu-id="40f17-167">Doppelklicken Sie auf **Authentifizierung** in die **IIS** Bereich.</span><span class="sxs-lookup"><span data-stu-id="40f17-167">Double-click **Authentication** in the **IIS** area.</span></span>
  1. <span data-ttu-id="40f17-168">Wählen Sie **anonyme Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="40f17-168">Select **Anonymous Authentication**.</span></span> <span data-ttu-id="40f17-169">Wählen Sie **deaktivieren** in die **Aktionen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="40f17-169">Select **Disable** in the **Actions** sidebar.</span></span>
  1. <span data-ttu-id="40f17-170">Wählen Sie **Windows-Authentifizierung**.</span><span class="sxs-lookup"><span data-stu-id="40f17-170">Select **Windows Authentication**.</span></span> <span data-ttu-id="40f17-171">Wählen Sie **aktivieren** in die **Aktionen** Randleiste.</span><span class="sxs-lookup"><span data-stu-id="40f17-171">Select **Enable** in the **Actions** sidebar.</span></span>

  <span data-ttu-id="40f17-172">Wenn diese Aktionen ausgeführt werden, ändert der IIS-Manager der app *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="40f17-172">When these actions are taken, IIS Manager modifies the app's *web.config* file.</span></span> <span data-ttu-id="40f17-173">Ein `<system.webServer><security><authentication>` Knoten wird hinzugefügt, mit aktualisierten Einstellungen für `anonymousAuthentication` und `windowsAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="40f17-173">A `<system.webServer><security><authentication>` node is added with updated settings for `anonymousAuthentication` and `windowsAuthentication`:</span></span>

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  <span data-ttu-id="40f17-174">Die `<system.webServer>` Abschnitts in der *"Web.config"* Datei vom IIS-Manager befindet sich außerhalb der app `<location>` Abschnitt durch die .NET Core SDK hinzugefügt wird, wenn die Anwendung veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="40f17-174">The `<system.webServer>` section added to the *web.config* file by IIS Manager is outside of the app's `<location>` section added by the .NET Core SDK when the app is published.</span></span> <span data-ttu-id="40f17-175">Da der Abschnitt, außerhalb von hinzugefügt wird den `<location>` Knoten werden die Einstellungen von einem geerbt [untergeordnete apps, die](xref:host-and-deploy/iis/index#sub-applications) der aktuellen App.</span><span class="sxs-lookup"><span data-stu-id="40f17-175">Because the section is added outside of the `<location>` node, the settings are inherited by any [sub-apps](xref:host-and-deploy/iis/index#sub-applications) to the current app.</span></span> <span data-ttu-id="40f17-176">Um Vererbung zu verhindern, verschieben Sie die hinzugefügte `<security>` Abschnitt in der die `<location><system.webServer>` -Abschnitt, der das .NET Core SDK bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="40f17-176">To prevent inheritance, move the added `<security>` section inside of the `<location><system.webServer>` section that the .NET Core SDK provided.</span></span>

  <span data-ttu-id="40f17-177">Wenn IIS-Manager verwendet wird, um die IIS-Konfiguration hinzuzufügen, es wirkt sich nur auf der app *"Web.config"* Datei auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="40f17-177">When IIS Manager is used to add the IIS configuration, it only affects the app's *web.config* file on the server.</span></span> <span data-ttu-id="40f17-178">Eine anschließende Bereitstellung der app möglicherweise die Einstellungen auf dem Server überschreiben, wenn die Kopie des Servers der *"Web.config"* durch des Projekts ersetzt *"Web.config"* Datei.</span><span class="sxs-lookup"><span data-stu-id="40f17-178">A subsequent deployment of the app may overwrite the settings on the server if the server's copy of *web.config* is replaced by the project's *web.config* file.</span></span> <span data-ttu-id="40f17-179">Verwendung **entweder** der folgenden Methoden zum Verwalten der Einstellungen:</span><span class="sxs-lookup"><span data-stu-id="40f17-179">Use **either** of the following approaches to manage the settings:</span></span>

  * <span data-ttu-id="40f17-180">Verwenden Sie IIS-Manager zum Zurücksetzen der Einstellungen in der *"Web.config"* -Datei nach der Bereitstellung die Datei überschrieben wird.</span><span class="sxs-lookup"><span data-stu-id="40f17-180">Use IIS Manager to reset the settings in the *web.config* file after the file is overwritten on deployment.</span></span>
  * <span data-ttu-id="40f17-181">Hinzufügen einer *Datei "Web.config"* an die app lokal mit den Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="40f17-181">Add a *web.config file* to the app locally with the settings.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a><span data-ttu-id="40f17-182">Kestrel</span><span class="sxs-lookup"><span data-stu-id="40f17-182">Kestrel</span></span>

 <span data-ttu-id="40f17-183">Die [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet-Paket verwendet werden kann, mit [Kestrel](xref:fundamentals/servers/kestrel) zur Unterstützung der Windows-Authentifizierung mithilfe des Negotiate, Kerberos und NTLM für Windows, Linux und MacOS.</span><span class="sxs-lookup"><span data-stu-id="40f17-183">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet package can be used with [Kestrel](xref:fundamentals/servers/kestrel) to support Windows Authentication using Negotiate, Kerberos, and NTLM on Windows, Linux, and macOS.</span></span>

> [!WARNING]
> <span data-ttu-id="40f17-184">Anmeldeinformationen können für die Anforderungen für eine Verbindung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="40f17-184">Credentials can be persisted across requests on a connection.</span></span> <span data-ttu-id="40f17-185">*Aushandeln Authentifizierung darf nicht mit Proxys verwendet werden, es sei denn, der Proxy eine 1:1-verbindungsaffinität (eine permanente Verbindung) mit Kestrel führt.*</span><span class="sxs-lookup"><span data-stu-id="40f17-185">*Negotiate authentication must not be used with proxies unless the proxy maintains a 1:1 connection affinity (a persistent connection) with Kestrel.*</span></span>

> [!NOTE]
> <span data-ttu-id="40f17-186">Der Negotiate-Handler erkennt, wenn der zugrunde liegenden Server Windows-Authentifizierung nativ unterstützt, und wenn es aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="40f17-186">The Negotiate handler detects if the underlying server supports Windows Authentication natively and if it's enabled.</span></span> <span data-ttu-id="40f17-187">Wenn der Server unterstützt die Windows-Authentifizierung, aber er deaktiviert ist, wird ein Fehler ausgelöst, werden Sie aufgefordert, die Server-Implementierung zu aktivieren.</span><span class="sxs-lookup"><span data-stu-id="40f17-187">If the server supports Windows Authentication but it's disabled, an error is thrown asking you to enable the server implementation.</span></span> <span data-ttu-id="40f17-188">Bei der Windows-Authentifizierung auf dem Server aktiviert ist, werden die Negotiate-Handler transparent an sie weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="40f17-188">When Windows Authentication is enabled in the server, the Negotiate handler transparently forwards to it.</span></span>

 <span data-ttu-id="40f17-189">Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` Namespace) und `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` Namespace) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="40f17-189">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) and `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` namespace) in `Startup.ConfigureServices`:</span></span>

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

<span data-ttu-id="40f17-190">Fügen Sie Middleware für die Authentifizierung durch den Aufruf <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span><span class="sxs-lookup"><span data-stu-id="40f17-190">Add Authentication Middleware by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:</span></span>

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

<span data-ttu-id="40f17-191">Weitere Informationen zu Middleware, finden Sie unter <xref:fundamentals/middleware/index>.</span><span class="sxs-lookup"><span data-stu-id="40f17-191">For more information on middleware, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="40f17-192">Anonyme Anforderungen sind zulässig.</span><span class="sxs-lookup"><span data-stu-id="40f17-192">Anonymous requests are allowed.</span></span> <span data-ttu-id="40f17-193">Verwendung [ASP.NET Core-Autorisierung](xref:security/authorization/introduction) sollen anonyme Anforderungen für die Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="40f17-193">Use [ASP.NET Core Authorization](xref:security/authorization/introduction) to challenge anonymous requests for authentication.</span></span>

### <a name="windows-environment-configuration"></a><span data-ttu-id="40f17-194">Konfiguration der Windows-Umgebung</span><span class="sxs-lookup"><span data-stu-id="40f17-194">Windows environment configuration</span></span>

<span data-ttu-id="40f17-195">Die [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Komponente führt eine Benutzermodus-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="40f17-195">The [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) component performs User Mode authentication.</span></span> <span data-ttu-id="40f17-196">Dienstprinzipalnamen (SPN) muss das Benutzerkonto, das Ausführen des Diensts, nicht das Computerkonto hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="40f17-196">Service Principal Names (SPNs) must be added to the user account running the service, not the machine account.</span></span> <span data-ttu-id="40f17-197">Führen Sie `setspn -S HTTP/mysrevername.mydomain.com myuser` in einer administrativen Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="40f17-197">Execute `setspn -S HTTP/mysrevername.mydomain.com myuser` in an administrative command shell.</span></span>

### <a name="linux-and-macos-environment-configuration"></a><span data-ttu-id="40f17-198">Konfiguration für Linux und MacOS-Umgebung</span><span class="sxs-lookup"><span data-stu-id="40f17-198">Linux and macOS environment configuration</span></span>

<span data-ttu-id="40f17-199">Anweisungen zum Verknüpfen von eines Linux- oder MacOS-Computers mit einer Windows-Domäne finden Sie in der [Verbinden von Azure Data Studio zu Ihrem SQL Server mithilfe der Windows-Authentifizierung – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) Artikel.</span><span class="sxs-lookup"><span data-stu-id="40f17-199">Instructions for joining a Linux or macOS machine to a Windows domain are available in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article.</span></span> <span data-ttu-id="40f17-200">Die Anweisungen erstellen Sie ein Computerkonto für den Linux-Computer in der Domäne.</span><span class="sxs-lookup"><span data-stu-id="40f17-200">The instructions create a machine account for the Linux machine on the domain.</span></span> <span data-ttu-id="40f17-201">SPNs müssen auf diese Computerkonten hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="40f17-201">SPNs must be added to that machine account.</span></span>

> [!NOTE]
> <span data-ttu-id="40f17-202">Befolgen Sie die Anweisungen in der [Verbinden von Azure Data Studio zu Ihrem SQL Server mithilfe der Windows-Authentifizierung – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) Artikel, ersetzen Sie `python-software-properties` mit `python3-software-properties` bei Bedarf.</span><span class="sxs-lookup"><span data-stu-id="40f17-202">When following the guidance in the [Connect Azure Data Studio to your SQL Server using Windows authentication - Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) article, replace `python-software-properties` with `python3-software-properties` if needed.</span></span>

<span data-ttu-id="40f17-203">Nach der Linux oder MacOS-Computer der Domäne angehört, sind zusätzliche Schritte erforderlich, zu einem [Keytab-Datei](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) mit der SPNs:</span><span class="sxs-lookup"><span data-stu-id="40f17-203">Once the Linux or macOS machine is joined to the domain, additional steps are required to provide a [keytab file](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) with the SPNs:</span></span>

* <span data-ttu-id="40f17-204">Fügen Sie auf dem Domänencontroller auf das Computerkonto neuen Webdienst SPNs hinzu:</span><span class="sxs-lookup"><span data-stu-id="40f17-204">On the domain controller, add new web service SPNs to the machine account:</span></span>
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* <span data-ttu-id="40f17-205">Verwendung [Ktpass](/windows-server/administration/windows-commands/ktpass) generiert eine Keytab-Datei:</span><span class="sxs-lookup"><span data-stu-id="40f17-205">Use [ktpass](/windows-server/administration/windows-commands/ktpass) to generate a keytab file:</span></span>
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * <span data-ttu-id="40f17-206">Einige Felder müssen angegeben werden Großbuchstaben angegeben.</span><span class="sxs-lookup"><span data-stu-id="40f17-206">Some fields must be specified in uppercase as indicated.</span></span>
* <span data-ttu-id="40f17-207">Kopieren Sie die Keytab-Datei mit dem Linux oder MacOS-Computer.</span><span class="sxs-lookup"><span data-stu-id="40f17-207">Copy the keytab file to the Linux or macOS machine.</span></span>
* <span data-ttu-id="40f17-208">Wählen Sie die Keytab-Datei über eine Umgebungsvariable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span><span class="sxs-lookup"><span data-stu-id="40f17-208">Select the keytab file via an environment variable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`</span></span>
* <span data-ttu-id="40f17-209">Rufen Sie `klist` , die derzeit nur SPNs angezeigt.</span><span class="sxs-lookup"><span data-stu-id="40f17-209">Invoke `klist` to show the SPNs currently available for use.</span></span>

> [!NOTE]
> <span data-ttu-id="40f17-210">Eine Keytab-Datei enthält die Anmeldeinformationen für die Domäne zugreifen und muss entsprechend geschützt werden.</span><span class="sxs-lookup"><span data-stu-id="40f17-210">A keytab file contains domain access credentials and must be protected accordingly.</span></span>

::: moniker-end

## <a name="httpsys"></a><span data-ttu-id="40f17-211">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="40f17-211">HTTP.sys</span></span>

<span data-ttu-id="40f17-212">[HTTP.sys](xref:fundamentals/servers/httpsys) Windows Kernelmodusauthentifizierung mit Negotiate, NTLM oder Standardauthentifizierung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="40f17-212">[HTTP.sys](xref:fundamentals/servers/httpsys) supports Kernel Mode Windows Authentication using Negotiate, NTLM, or Basic authentication.</span></span>

<span data-ttu-id="40f17-213">Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> Namespace) in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="40f17-213">Add authentication services by invoking <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace) in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

<span data-ttu-id="40f17-214">Konfigurieren der app-Web-Host zur Verwendung von HTTP.sys mit Windows-Authentifizierung ( *"Program.cs"* ).</span><span class="sxs-lookup"><span data-stu-id="40f17-214">Configure the app's web host to use HTTP.sys with Windows Authentication (*Program.cs*).</span></span> <span data-ttu-id="40f17-215"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> befindet sich in der <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> Namespace.</span><span class="sxs-lookup"><span data-stu-id="40f17-215"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> is in the <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> namespace.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="40f17-216">HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll.</span><span class="sxs-lookup"><span data-stu-id="40f17-216">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="40f17-217">Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt.</span><span class="sxs-lookup"><span data-stu-id="40f17-217">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="40f17-218">Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird.</span><span class="sxs-lookup"><span data-stu-id="40f17-218">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="40f17-219">Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App.</span><span class="sxs-lookup"><span data-stu-id="40f17-219">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

> [!NOTE]
> <span data-ttu-id="40f17-220">HTTP.sys wird nicht auf Nano Server-Version 1709 oder höher unterstützt.</span><span class="sxs-lookup"><span data-stu-id="40f17-220">HTTP.sys isn't supported on Nano Server version 1709 or later.</span></span> <span data-ttu-id="40f17-221">Um Windows-Authentifizierung und HTTP.sys mit Nano Server verwenden möchten, verwenden eine [Server Core (Microsoft/Windowsservercore) Container](https://hub.docker.com/r/microsoft/windowsservercore/).</span><span class="sxs-lookup"><span data-stu-id="40f17-221">To use Windows Authentication and HTTP.sys with Nano Server, use a [Server Core (microsoft/windowsservercore) container](https://hub.docker.com/r/microsoft/windowsservercore/).</span></span> <span data-ttu-id="40f17-222">Weitere Informationen zur Server Core finden Sie unter [Was ist, dass der Server Core-Installationsoption in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span><span class="sxs-lookup"><span data-stu-id="40f17-222">For more information on Server Core, see [What is the Server Core installation option in Windows Server?](/windows-server/administration/server-core/what-is-server-core).</span></span>

## <a name="authorize-users"></a><span data-ttu-id="40f17-223">Autorisieren von Benutzern</span><span class="sxs-lookup"><span data-stu-id="40f17-223">Authorize users</span></span>

<span data-ttu-id="40f17-224">Der Konfigurationsstatus des anonymen Zugriffs bestimmt die Art, wie die `[Authorize]` und `[AllowAnonymous]` Attribute werden in der app verwendet.</span><span class="sxs-lookup"><span data-stu-id="40f17-224">The configuration state of anonymous access determines the way in which the `[Authorize]` and `[AllowAnonymous]` attributes are used in the app.</span></span> <span data-ttu-id="40f17-225">In den folgenden zwei Abschnitten wird erläutert, wie die nicht zulässigen und die zulässigen Zustände des anonymen Zugriffs zu behandeln.</span><span class="sxs-lookup"><span data-stu-id="40f17-225">The following two sections explain how to handle the disallowed and allowed configuration states of anonymous access.</span></span>

### <a name="disallow-anonymous-access"></a><span data-ttu-id="40f17-226">Anonyme Zugriffe nicht zulassen</span><span class="sxs-lookup"><span data-stu-id="40f17-226">Disallow anonymous access</span></span>

<span data-ttu-id="40f17-227">Wenn Windows-Authentifizierung aktiviert ist und der anonyme Zugriff deaktiviert ist, die `[Authorize]` und `[AllowAnonymous]` Attribute haben keine Auswirkung.</span><span class="sxs-lookup"><span data-stu-id="40f17-227">When Windows Authentication is enabled and anonymous access is disabled, the `[Authorize]` and `[AllowAnonymous]` attributes have no effect.</span></span> <span data-ttu-id="40f17-228">Wenn eine IIS-Website konfiguriert ist, um den anonymen Zugriff verweigern, erreicht die Anforderung nie die app.</span><span class="sxs-lookup"><span data-stu-id="40f17-228">If an IIS site is configured to disallow anonymous access, the request never reaches the app.</span></span> <span data-ttu-id="40f17-229">Aus diesem Grund die `[AllowAnonymous]` Attribut ist nicht anwendbar.</span><span class="sxs-lookup"><span data-stu-id="40f17-229">For this reason, the `[AllowAnonymous]` attribute isn't applicable.</span></span>

### <a name="allow-anonymous-access"></a><span data-ttu-id="40f17-230">Anonymen Zugriff zulassen</span><span class="sxs-lookup"><span data-stu-id="40f17-230">Allow anonymous access</span></span>

<span data-ttu-id="40f17-231">Wenn sowohl für Windows-Authentifizierung als auch für anonymen Zugriff aktiviert sind, verwenden die `[Authorize]` und `[AllowAnonymous]` Attribute.</span><span class="sxs-lookup"><span data-stu-id="40f17-231">When both Windows Authentication and anonymous access are enabled, use the `[Authorize]` and `[AllowAnonymous]` attributes.</span></span> <span data-ttu-id="40f17-232">Die `[Authorize]` Attribut können Sie die Endpunkte der app schützen, das Authentifizierung erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="40f17-232">The `[Authorize]` attribute allows you to secure endpoints of the app which require authentication.</span></span> <span data-ttu-id="40f17-233">Die `[AllowAnonymous]` -Attribut überschreibt die `[Authorize]` -Attribut in apps, die den anonymen Zugriff zulassen.</span><span class="sxs-lookup"><span data-stu-id="40f17-233">The `[AllowAnonymous]` attribute overrides the `[Authorize]` attribute in apps that allow anonymous access.</span></span> <span data-ttu-id="40f17-234">Attribut Verwendungsdetails finden Sie unter <xref:security/authorization/simple>.</span><span class="sxs-lookup"><span data-stu-id="40f17-234">For attribute usage details, see <xref:security/authorization/simple>.</span></span>

> [!NOTE]
> <span data-ttu-id="40f17-235">Standardmäßig werden Benutzer, die Autorisierung zum Zugriff auf eine Seite nicht über eine leere HTTP 403-Antwort angezeigt.</span><span class="sxs-lookup"><span data-stu-id="40f17-235">By default, users who lack authorization to access a page are presented with an empty HTTP 403 response.</span></span> <span data-ttu-id="40f17-236">Die [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) kann konfiguriert werden, um Benutzern mit "Zugriff verweigert" Benutzerfreundlicher zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="40f17-236">The [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) can be configured to provide users with a better "Access Denied" experience.</span></span>

## <a name="impersonation"></a><span data-ttu-id="40f17-237">Identitätswechsel</span><span class="sxs-lookup"><span data-stu-id="40f17-237">Impersonation</span></span>

<span data-ttu-id="40f17-238">ASP.NET Core implementiert keine Identitätswechsel.</span><span class="sxs-lookup"><span data-stu-id="40f17-238">ASP.NET Core doesn't implement impersonation.</span></span> <span data-ttu-id="40f17-239">Apps, die mit der app Identität für alle Anforderungen, die mithilfe von app-Pool oder Prozess Identität ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="40f17-239">Apps run with the app's identity for all requests, using app pool or process identity.</span></span> <span data-ttu-id="40f17-240">Wenn die app eine Aktion im Auftrag eines Benutzers ausführen soll, verwenden Sie [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in einem [terminal Inline-Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="40f17-240">If the app should perform an action on behalf of a user, use [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in a [terminal inline middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`.</span></span> <span data-ttu-id="40f17-241">Eine einzelne Aktion in diesem Kontext ausgeführt, und schließen Sie den Kontext.</span><span class="sxs-lookup"><span data-stu-id="40f17-241">Run a single action in this context and then close the context.</span></span>

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

<span data-ttu-id="40f17-242">`RunImpersonated` unterstützt keine asynchronen Vorgänge und sollte nicht für komplexe Szenarien verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="40f17-242">`RunImpersonated` doesn't support asynchronous operations and shouldn't be used for complex scenarios.</span></span> <span data-ttu-id="40f17-243">Z. B. wird nicht wrapping gesamte Anforderungen oder Middleware verkettet unterstützt oder empfohlen.</span><span class="sxs-lookup"><span data-stu-id="40f17-243">For example, wrapping entire requests or middleware chains isn't supported or recommended.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40f17-244">Während der [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Paket ermöglicht die Authentifizierung auf Windows, Linux und MacOS, Identitätswechsel wird nur unter Windows unterstützt.</span><span class="sxs-lookup"><span data-stu-id="40f17-244">While the [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) package enables authentication on Windows, Linux, and macOS, impersonation is only supported on Windows.</span></span>

::: moniker-end

## <a name="claims-transformations"></a><span data-ttu-id="40f17-245">Transformationen von Ansprüchen</span><span class="sxs-lookup"><span data-stu-id="40f17-245">Claims transformations</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40f17-246">Wenn Sie mit IIS hosten <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> wird nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="40f17-246">When hosting with IIS, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="40f17-247">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="40f17-247">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="40f17-248">Weitere Informationen und ein Codebeispiel, das Transformationen von Ansprüchen aktiviert wird, finden Sie unter <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="40f17-248">For more information and a code example that activates claims transformations, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="40f17-249">Wenn Sie mit in-Process-Modus von IIS hosten <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> wird nicht intern aufgerufen, um einen Benutzer zu initialisieren.</span><span class="sxs-lookup"><span data-stu-id="40f17-249">When hosting with IIS in-process mode, <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> isn't called internally to initialize a user.</span></span> <span data-ttu-id="40f17-250">Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="40f17-250">Therefore, an <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation> implementation used to transform claims after every authentication isn't activated by default.</span></span> <span data-ttu-id="40f17-251">Weitere Informationen und ein Codebeispiel, das Transformationen von Ansprüchen, das aktiviert wird, wenn prozessinternes hosting, finden Sie unter <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span><span class="sxs-lookup"><span data-stu-id="40f17-251">For more information and a code example that activates claims transformations when hosting in-process, see <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="40f17-252">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="40f17-252">Additional resources</span></span>

* [<span data-ttu-id="40f17-253">dotnet publish</span><span class="sxs-lookup"><span data-stu-id="40f17-253">dotnet publish</span></span>](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
