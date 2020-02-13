---
title: Hosten von ASP.NET Core in einem Windows-Dienst
author: guardrex
description: Erfahren Sie, wie eine ASP.NET Core-App in einem Windows-Dienst gehostet wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 829c282606e60a80682233555e1268acb706090e
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172328"
---
# <a name="host-aspnet-core-in-a-windows-service"></a><span data-ttu-id="a9750-103">Hosten von ASP.NET Core in einem Windows-Dienst</span><span class="sxs-lookup"><span data-stu-id="a9750-103">Host ASP.NET Core in a Windows Service</span></span>

<span data-ttu-id="a9750-104">Von [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a9750-104">By [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9750-105">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-105">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="a9750-106">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="a9750-106">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="a9750-107">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9750-107">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a9750-108">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="a9750-108">Prerequisites</span></span>

* [<span data-ttu-id="a9750-109">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="a9750-109">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="a9750-110">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="a9750-110">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a><span data-ttu-id="a9750-111">Workerdienstvorlage</span><span class="sxs-lookup"><span data-stu-id="a9750-111">Worker Service template</span></span>

<span data-ttu-id="a9750-112">Die ASP.NET Core-Workerdienstvorlage dient als Ausgangspunkt für das Programmieren von zeitintensiven Dienstanwendungen.</span><span class="sxs-lookup"><span data-stu-id="a9750-112">The ASP.NET Core Worker Service template provides a starting point for writing long running service apps.</span></span> <span data-ttu-id="a9750-113">So können Sie diese Vorlage als Grundlage für eine Windows-Dienstanwendung verwenden:</span><span class="sxs-lookup"><span data-stu-id="a9750-113">To use the template as a basis for a Windows Service app:</span></span>

1. <span data-ttu-id="a9750-114">Erstellen Sie eine Workerdienstanwendung über die .NET Core-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="a9750-114">Create a Worker Service app from the .NET Core template.</span></span>
1. <span data-ttu-id="a9750-115">Befolgen Sie die Anweisungen im Abschnitt [App-Konfiguration](#app-configuration), um die Workerdienstanwendung so zu aktualisieren, dass sie als Windows-Dienst ausgeführt werden kann.</span><span class="sxs-lookup"><span data-stu-id="a9750-115">Follow the guidance in the [App configuration](#app-configuration) section to update the Worker Service app so that it can run as a Windows Service.</span></span>

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a><span data-ttu-id="a9750-116">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a9750-116">App configuration</span></span>

<span data-ttu-id="a9750-117">Die App erfordert einen Paketverweis auf [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span><span class="sxs-lookup"><span data-stu-id="a9750-117">The app requires a package reference for [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices).</span></span>

<span data-ttu-id="a9750-118">Beim Erstellen des Hosts wird `IHostBuilder.UseWindowsService` aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="a9750-118">`IHostBuilder.UseWindowsService` is called when building the host.</span></span> <span data-ttu-id="a9750-119">Wenn die App als Windows-Dienst ausgeführt wird, sorgt die Methode für Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a9750-119">If the app is running as a Windows Service, the method:</span></span>

* <span data-ttu-id="a9750-120">Sie legt die Lebensdauer des Hosts auf `WindowsServiceLifetime` fest.</span><span class="sxs-lookup"><span data-stu-id="a9750-120">Sets the host lifetime to `WindowsServiceLifetime`.</span></span>
* <span data-ttu-id="a9750-121">[AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) wird als [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a9750-121">Sets the [content root](xref:fundamentals/index#content-root) to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span> <span data-ttu-id="a9750-122">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="a9750-122">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span>
* <span data-ttu-id="a9750-123">Ermöglicht die Protokollierung zum Ereignisprotokoll:</span><span class="sxs-lookup"><span data-stu-id="a9750-123">Enables logging to the event log:</span></span>
  * <span data-ttu-id="a9750-124">Der Anwendungsname wird als Standardname für die Quelle verwendet.</span><span class="sxs-lookup"><span data-stu-id="a9750-124">The application name is used as the default source name.</span></span>
  * <span data-ttu-id="a9750-125">Für eine App basierend auf einer ASP.NET Core-Vorlage, die `CreateDefaultBuilder` zur Erstellung des Hosts aufruft, lautet die Standardprotokollebene *Warnung* oder höher.</span><span class="sxs-lookup"><span data-stu-id="a9750-125">The default log level is *Warning* or higher for an app based on an ASP.NET Core template that calls `CreateDefaultBuilder` to build the host.</span></span>
  * <span data-ttu-id="a9750-126">Setzen Sie die Standardprotokollebene mit dem `Logging:EventLog:LogLevel:Default`-Schlüssel in *appsettings.json*/*appsettings.{Environment}.json* oder einem anderen Konfigurationsanbieter außer Kraft.</span><span class="sxs-lookup"><span data-stu-id="a9750-126">Override the default log level with the `Logging:EventLog:LogLevel:Default` key in *appsettings.json*/*appsettings.{Environment}.json* or other configuration provider.</span></span>
  * <span data-ttu-id="a9750-127">Nur Administratoren können neue Ereignisquellen erstellen.</span><span class="sxs-lookup"><span data-stu-id="a9750-127">Only administrators can create new event sources.</span></span> <span data-ttu-id="a9750-128">Wenn keine Ereignisquellen mithilfe des Anwendungsnamens erstellt werden können, wird in der *Anwendungsquelle* eine Warnung protokolliert, und die Ereignisprotokolle werden deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="a9750-128">When an event source can't be created using the application name, a warning is logged to the *Application* source and event logs are disabled.</span></span>

<span data-ttu-id="a9750-129">In `CreateHostBuilder` von *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a9750-129">In `CreateHostBuilder` of *Program.cs*:</span></span>

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

<span data-ttu-id="a9750-130">In diesem Thema werden die folgenden Beispiel-Apps behandelt:</span><span class="sxs-lookup"><span data-stu-id="a9750-130">The following sample apps accompany this topic:</span></span>

* <span data-ttu-id="a9750-131">Beispiel eines Hintergrundworkerdiensts &ndash; Ein Beispiel einer nicht webbasierten App, die auf der [Workerdienstvorlage](#worker-service-template) basiert, die [gehostete Dienste](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben verwendet.</span><span class="sxs-lookup"><span data-stu-id="a9750-131">Background Worker Service Sample &ndash; A non-web app sample based on the [Worker Service template](#worker-service-template) that uses [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>
* <span data-ttu-id="a9750-132">Beispiel eines Web-App-Diensts &ndash; Ein Razor Pages-Web-App-Beispiel, das als Windows-Dienst mit [gehosteten Diensten](xref:fundamentals/host/hosted-services) für Hintergrundaufgaben ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-132">Web App Service Sample &ndash; A Razor Pages web app sample that runs as a Windows Service with [hosted services](xref:fundamentals/host/hosted-services) for background tasks.</span></span>

<span data-ttu-id="a9750-133">MVC-Anleitungen finden Sie in den Artikeln unter <xref:mvc/overview> und <xref:migration/22-to-30>.</span><span class="sxs-lookup"><span data-stu-id="a9750-133">For MVC guidance, see the articles under <xref:mvc/overview> and <xref:migration/22-to-30>.</span></span>

## <a name="deployment-type"></a><span data-ttu-id="a9750-134">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="a9750-134">Deployment type</span></span>

<span data-ttu-id="a9750-135">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="a9750-135">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="a9750-136">SDK</span><span class="sxs-lookup"><span data-stu-id="a9750-136">SDK</span></span>

<span data-ttu-id="a9750-137">Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="a9750-137">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a9750-138">Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)):</span><span class="sxs-lookup"><span data-stu-id="a9750-138">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="a9750-139">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="a9750-139">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="a9750-140">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="a9750-140">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="a9750-141">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="a9750-141">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="a9750-142">Bei Verwendung des [Web-SDK](#sdk) ist eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a9750-142">If using the [Web SDK](#sdk), a *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="a9750-143">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-143">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="a9750-144">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="a9750-144">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="a9750-145">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="a9750-145">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="a9750-146">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a9750-146">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="a9750-147">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="a9750-147">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="a9750-148">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-148">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="a9750-149">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="a9750-149">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="a9750-150">Verwenden Sie den Eigenschaftsnamen [\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="a9750-150">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="a9750-151">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="a9750-151">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

## <a name="service-user-account"></a><span data-ttu-id="a9750-152">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="a9750-152">Service user account</span></span>

<span data-ttu-id="a9750-153">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="a9750-153">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="a9750-154">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="a9750-154">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-155">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="a9750-155">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="a9750-156">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="a9750-156">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="a9750-157">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="a9750-157">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="a9750-158">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="a9750-158">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="a9750-159">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="a9750-159">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="a9750-160">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="a9750-160">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="a9750-161">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="a9750-161">Log on as a service rights</span></span>

<span data-ttu-id="a9750-162">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="a9750-162">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="a9750-163">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-163">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="a9750-164">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="a9750-164">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="a9750-165">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="a9750-165">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="a9750-166">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-166">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="a9750-167">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="a9750-167">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="a9750-168">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a9750-168">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="a9750-169">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-169">Select **Advanced**.</span></span> <span data-ttu-id="a9750-170">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="a9750-170">Select **Find Now**.</span></span> <span data-ttu-id="a9750-171">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-171">Select the user account from the list.</span></span> <span data-ttu-id="a9750-172">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="a9750-172">Select **OK**.</span></span> <span data-ttu-id="a9750-173">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a9750-173">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="a9750-174">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="a9750-174">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="a9750-175">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-175">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="a9750-176">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-176">Create a service</span></span>

<span data-ttu-id="a9750-177">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="a9750-177">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="a9750-178">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-178">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="a9750-179">`{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="a9750-179">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="a9750-180">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="a9750-180">Don't include the app's executable in the path.</span></span> <span data-ttu-id="a9750-181">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="a9750-181">A trailing slash isn't required.</span></span>
* <span data-ttu-id="a9750-182">`{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="a9750-182">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="a9750-183">`{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).</span><span class="sxs-lookup"><span data-stu-id="a9750-183">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="a9750-184">`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="a9750-184">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="a9750-185">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-185">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="a9750-186">`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="a9750-186">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="a9750-187">`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).</span><span class="sxs-lookup"><span data-stu-id="a9750-187">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="a9750-188">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-188">Start a service</span></span>

<span data-ttu-id="a9750-189">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="a9750-189">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-190">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="a9750-190">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="a9750-191">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-191">Determine a service's status</span></span>

<span data-ttu-id="a9750-192">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="a9750-192">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-193">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="a9750-193">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="a9750-194">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-194">Stop a service</span></span>

<span data-ttu-id="a9750-195">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="a9750-195">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="a9750-196">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-196">Remove a service</span></span>

<span data-ttu-id="a9750-197">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="a9750-197">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a9750-198">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="a9750-198">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a9750-199">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="a9750-199">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="a9750-200">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="a9750-200">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="a9750-201">Konfigurieren von Endpunkten</span><span class="sxs-lookup"><span data-stu-id="a9750-201">Configure endpoints</span></span>

<span data-ttu-id="a9750-202">Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.</span><span class="sxs-lookup"><span data-stu-id="a9750-202">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a9750-203">Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.</span><span class="sxs-lookup"><span data-stu-id="a9750-203">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="a9750-204">Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:</span><span class="sxs-lookup"><span data-stu-id="a9750-204">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="a9750-205">Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="a9750-205">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="a9750-206">Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-206">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="a9750-207">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a9750-207">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="a9750-208">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="a9750-208">Current directory and content root</span></span>

<span data-ttu-id="a9750-209">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="a9750-209">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="a9750-210">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="a9750-210">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="a9750-211">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="a9750-211">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="use-contentrootpath-or-contentrootfileprovider"></a><span data-ttu-id="a9750-212">Verwenden von ContentRootPath oder ContentRootFileProvider</span><span class="sxs-lookup"><span data-stu-id="a9750-212">Use ContentRootPath or ContentRootFileProvider</span></span>

<span data-ttu-id="a9750-213">Verwenden Sie [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) oder <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider>, um die Ressourcen einer App zu finden.</span><span class="sxs-lookup"><span data-stu-id="a9750-213">Use [IHostEnvironment.ContentRootPath](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) or <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> to locate an app's resources.</span></span>

<span data-ttu-id="a9750-214">Wenn die App als Dienst ausgeführt wird, [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory) durch <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> für <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a9750-214">When the app runs as a service, <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> sets the <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> to [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory).</span></span>

<span data-ttu-id="a9750-215">Die Standardeinstellungsdateien *appsettings.json* und *appsettings.{Environment}.json* der App, werden über das Inhaltsstammverzeichnis der App geladen, indem [CreateDefaultBuilder](xref:fundamentals/host/generic-host#set-up-a-host) während der Hosterstellung aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-215">The app's default settings files, *appsettings.json* and *appsettings.{Environment}.json*, are loaded from the app's content root by calling [CreateDefaultBuilder during host construction](xref:fundamentals/host/generic-host#set-up-a-host).</span></span>

<span data-ttu-id="a9750-216">Für andere Einstellungsdateien, die in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*> durch Entwicklercode geladen werden, muss <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> nicht aufgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-216">For other settings files loaded by developer code in <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>, there's no need to call <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>.</span></span> <span data-ttu-id="a9750-217">Im folgenden Beispiel ist die Datei *custom_settings.json* im Inhaltsstammverzeichnis der App enthalten und wird geladen, ohne das ein Basispfad explizit festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="a9750-217">In the following example, the *custom_settings.json* file exists in the app's content root and is loaded without explicitly setting a base path:</span></span>

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

<span data-ttu-id="a9750-218">Versuchen Sie nicht, <xref:System.IO.Directory.GetCurrentDirectory*> zum Abrufen eines Ressourcenpfads zu verwenden, da eine Windows-Dienstanwendung den Ordner *C:\\WINDOWS\\system32* als aktuelles Verzeichnis zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="a9750-218">Don't attempt to use <xref:System.IO.Directory.GetCurrentDirectory*> to obtain a resource path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder as its current directory.</span></span>

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="a9750-219">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="a9750-219">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="a9750-220">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="a9750-220">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a9750-221">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="a9750-221">Troubleshoot</span></span>

<span data-ttu-id="a9750-222">Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="a9750-222">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="a9750-223">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="a9750-223">Common errors</span></span>

* <span data-ttu-id="a9750-224">Eine alte oder eine Vorabversion von PowerShell wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="a9750-224">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="a9750-225">Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="a9750-225">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="a9750-226">Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a9750-226">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="a9750-227">Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden:</span><span class="sxs-lookup"><span data-stu-id="a9750-227">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="a9750-228">*bin/Release/{ZIELFRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="a9750-228">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="a9750-229">*bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="a9750-229">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="a9750-230">Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.</span><span class="sxs-lookup"><span data-stu-id="a9750-230">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="a9750-231">Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="a9750-231">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="a9750-232">Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="a9750-232">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="a9750-233">Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.</span><span class="sxs-lookup"><span data-stu-id="a9750-233">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="a9750-234">Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-234">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="a9750-235">Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a9750-235">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="a9750-236">Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a9750-236">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="a9750-237">System- und Anwendungsereignisprotokolle</span><span class="sxs-lookup"><span data-stu-id="a9750-237">System and Application Event Logs</span></span>

<span data-ttu-id="a9750-238">So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:</span><span class="sxs-lookup"><span data-stu-id="a9750-238">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="a9750-239">Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-239">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="a9750-240">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="a9750-240">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="a9750-241">Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="a9750-241">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="a9750-242">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="a9750-242">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="a9750-243">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="a9750-243">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="a9750-244">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="a9750-244">Run the app at a command prompt</span></span>

<span data-ttu-id="a9750-245">Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen.</span><span class="sxs-lookup"><span data-stu-id="a9750-245">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="a9750-246">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-246">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="a9750-247">Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a9750-247">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="a9750-248">Bereinigen der Paketcaches</span><span class="sxs-lookup"><span data-stu-id="a9750-248">Clear package caches</span></span>

<span data-ttu-id="a9750-249">Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="a9750-249">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="a9750-250">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-250">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a9750-251">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="a9750-251">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a9750-252">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="a9750-252">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="a9750-253">Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-253">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="a9750-254">Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.</span><span class="sxs-lookup"><span data-stu-id="a9750-254">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="a9750-255">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-255">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="a9750-256">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="a9750-256">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a9750-257">Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a9750-257">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="a9750-258">Langsame oder hängende App</span><span class="sxs-lookup"><span data-stu-id="a9750-258">Slow or hanging app</span></span>

<span data-ttu-id="a9750-259">Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="a9750-259">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="a9750-260">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="a9750-260">App crashes or encounters an exception</span></span>

<span data-ttu-id="a9750-261">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="a9750-261">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="a9750-262">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="a9750-262">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="a9750-263">Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-263">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="a9750-264">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="a9750-264">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="a9750-265">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-265">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="a9750-266">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-266">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="a9750-267">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="a9750-267">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="a9750-268">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="a9750-268">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="a9750-269">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="a9750-269">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="a9750-270">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="a9750-270">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="a9750-271">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="a9750-271">Analyze the dump</span></span>

<span data-ttu-id="a9750-272">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-272">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="a9750-273">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="a9750-273">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9750-274">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a9750-274">Additional resources</span></span>

* <span data-ttu-id="a9750-275">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="a9750-275">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a9750-276">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-276">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="a9750-277">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="a9750-277">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="a9750-278">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9750-278">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a9750-279">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="a9750-279">Prerequisites</span></span>

* [<span data-ttu-id="a9750-280">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="a9750-280">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="a9750-281">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="a9750-281">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="a9750-282">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a9750-282">App configuration</span></span>

<span data-ttu-id="a9750-283">Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="a9750-283">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="a9750-284">Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-284">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="a9750-285">Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt.</span><span class="sxs-lookup"><span data-stu-id="a9750-285">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="a9750-286">Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf.</span><span class="sxs-lookup"><span data-stu-id="a9750-286">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="a9750-287">Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):</span><span class="sxs-lookup"><span data-stu-id="a9750-287">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="a9750-288">Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.</span><span class="sxs-lookup"><span data-stu-id="a9750-288">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="a9750-289">Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-289">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="a9750-290">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="a9750-290">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="a9750-291">Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-291">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="a9750-292">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-292">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="a9750-293">Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.</span><span class="sxs-lookup"><span data-stu-id="a9750-293">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="a9750-294">Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-294">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="a9750-295">Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.</span><span class="sxs-lookup"><span data-stu-id="a9750-295">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="a9750-296">Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a9750-296">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="a9750-297">Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="a9750-297">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="a9750-298">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="a9750-298">Deployment type</span></span>

<span data-ttu-id="a9750-299">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="a9750-299">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="a9750-300">SDK</span><span class="sxs-lookup"><span data-stu-id="a9750-300">SDK</span></span>

<span data-ttu-id="a9750-301">Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="a9750-301">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a9750-302">Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)):</span><span class="sxs-lookup"><span data-stu-id="a9750-302">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="a9750-303">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="a9750-303">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="a9750-304">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="a9750-304">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="a9750-305">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="a9750-305">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="a9750-306">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="a9750-306">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="a9750-307">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a9750-307">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="a9750-308">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a9750-308">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="a9750-309">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="a9750-309">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="a9750-310">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a9750-310">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="a9750-311">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-311">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="a9750-312">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="a9750-312">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="a9750-313">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="a9750-313">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="a9750-314">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a9750-314">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="a9750-315">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="a9750-315">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="a9750-316">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-316">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="a9750-317">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="a9750-317">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="a9750-318">Verwenden Sie den Eigenschaftsnamen [\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="a9750-318">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="a9750-319">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="a9750-319">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="a9750-320">Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a9750-320">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="a9750-321">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="a9750-321">Service user account</span></span>

<span data-ttu-id="a9750-322">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="a9750-322">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="a9750-323">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="a9750-323">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-324">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="a9750-324">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="a9750-325">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="a9750-325">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="a9750-326">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="a9750-326">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="a9750-327">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="a9750-327">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="a9750-328">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="a9750-328">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="a9750-329">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="a9750-329">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="a9750-330">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="a9750-330">Log on as a service rights</span></span>

<span data-ttu-id="a9750-331">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="a9750-331">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="a9750-332">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-332">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="a9750-333">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="a9750-333">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="a9750-334">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="a9750-334">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="a9750-335">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-335">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="a9750-336">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="a9750-336">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="a9750-337">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a9750-337">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="a9750-338">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-338">Select **Advanced**.</span></span> <span data-ttu-id="a9750-339">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="a9750-339">Select **Find Now**.</span></span> <span data-ttu-id="a9750-340">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-340">Select the user account from the list.</span></span> <span data-ttu-id="a9750-341">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="a9750-341">Select **OK**.</span></span> <span data-ttu-id="a9750-342">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a9750-342">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="a9750-343">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="a9750-343">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="a9750-344">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-344">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="a9750-345">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-345">Create a service</span></span>

<span data-ttu-id="a9750-346">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="a9750-346">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="a9750-347">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-347">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="a9750-348">`{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="a9750-348">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="a9750-349">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="a9750-349">Don't include the app's executable in the path.</span></span> <span data-ttu-id="a9750-350">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="a9750-350">A trailing slash isn't required.</span></span>
* <span data-ttu-id="a9750-351">`{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="a9750-351">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="a9750-352">`{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).</span><span class="sxs-lookup"><span data-stu-id="a9750-352">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="a9750-353">`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="a9750-353">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="a9750-354">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-354">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="a9750-355">`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="a9750-355">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="a9750-356">`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).</span><span class="sxs-lookup"><span data-stu-id="a9750-356">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="a9750-357">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-357">Start a service</span></span>

<span data-ttu-id="a9750-358">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="a9750-358">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-359">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="a9750-359">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="a9750-360">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-360">Determine a service's status</span></span>

<span data-ttu-id="a9750-361">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="a9750-361">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-362">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="a9750-362">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="a9750-363">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-363">Stop a service</span></span>

<span data-ttu-id="a9750-364">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="a9750-364">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="a9750-365">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-365">Remove a service</span></span>

<span data-ttu-id="a9750-366">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="a9750-366">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="a9750-367">Behandeln von Start- und Stopereignissen</span><span class="sxs-lookup"><span data-stu-id="a9750-367">Handle starting and stopping events</span></span>

<span data-ttu-id="a9750-368">Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:</span><span class="sxs-lookup"><span data-stu-id="a9750-368">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="a9750-369">Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:</span><span class="sxs-lookup"><span data-stu-id="a9750-369">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="a9750-370">Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:</span><span class="sxs-lookup"><span data-stu-id="a9750-370">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="a9750-371">Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:</span><span class="sxs-lookup"><span data-stu-id="a9750-371">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="a9750-372">Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.</span><span class="sxs-lookup"><span data-stu-id="a9750-372">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a9750-373">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="a9750-373">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a9750-374">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="a9750-374">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="a9750-375">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="a9750-375">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="a9750-376">Konfigurieren von Endpunkten</span><span class="sxs-lookup"><span data-stu-id="a9750-376">Configure endpoints</span></span>

<span data-ttu-id="a9750-377">Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.</span><span class="sxs-lookup"><span data-stu-id="a9750-377">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a9750-378">Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.</span><span class="sxs-lookup"><span data-stu-id="a9750-378">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="a9750-379">Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:</span><span class="sxs-lookup"><span data-stu-id="a9750-379">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="a9750-380">Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="a9750-380">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="a9750-381">Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-381">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="a9750-382">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a9750-382">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="a9750-383">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="a9750-383">Current directory and content root</span></span>

<span data-ttu-id="a9750-384">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="a9750-384">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="a9750-385">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="a9750-385">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="a9750-386">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="a9750-386">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="a9750-387">Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="a9750-387">Set the content root path to the app's folder</span></span>

<span data-ttu-id="a9750-388"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-388">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="a9750-389">Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App auf.</span><span class="sxs-lookup"><span data-stu-id="a9750-389">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="a9750-390">Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="a9750-390">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="a9750-391">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="a9750-391">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="a9750-392">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="a9750-392">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a9750-393">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="a9750-393">Troubleshoot</span></span>

<span data-ttu-id="a9750-394">Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="a9750-394">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="a9750-395">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="a9750-395">Common errors</span></span>

* <span data-ttu-id="a9750-396">Eine alte oder eine Vorabversion von PowerShell wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="a9750-396">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="a9750-397">Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="a9750-397">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="a9750-398">Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a9750-398">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="a9750-399">Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden:</span><span class="sxs-lookup"><span data-stu-id="a9750-399">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="a9750-400">*bin/Release/{ZIELFRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="a9750-400">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="a9750-401">*bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="a9750-401">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="a9750-402">Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.</span><span class="sxs-lookup"><span data-stu-id="a9750-402">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="a9750-403">Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="a9750-403">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="a9750-404">Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="a9750-404">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="a9750-405">Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.</span><span class="sxs-lookup"><span data-stu-id="a9750-405">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="a9750-406">Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-406">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="a9750-407">Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a9750-407">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="a9750-408">Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a9750-408">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="a9750-409">System- und Anwendungsereignisprotokolle</span><span class="sxs-lookup"><span data-stu-id="a9750-409">System and Application Event Logs</span></span>

<span data-ttu-id="a9750-410">So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:</span><span class="sxs-lookup"><span data-stu-id="a9750-410">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="a9750-411">Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-411">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="a9750-412">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="a9750-412">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="a9750-413">Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="a9750-413">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="a9750-414">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="a9750-414">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="a9750-415">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="a9750-415">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="a9750-416">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="a9750-416">Run the app at a command prompt</span></span>

<span data-ttu-id="a9750-417">Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen.</span><span class="sxs-lookup"><span data-stu-id="a9750-417">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="a9750-418">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-418">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="a9750-419">Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a9750-419">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="a9750-420">Bereinigen der Paketcaches</span><span class="sxs-lookup"><span data-stu-id="a9750-420">Clear package caches</span></span>

<span data-ttu-id="a9750-421">Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="a9750-421">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="a9750-422">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-422">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a9750-423">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="a9750-423">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a9750-424">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="a9750-424">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="a9750-425">Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-425">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="a9750-426">Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.</span><span class="sxs-lookup"><span data-stu-id="a9750-426">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="a9750-427">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-427">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="a9750-428">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="a9750-428">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a9750-429">Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a9750-429">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="a9750-430">Langsame oder hängende App</span><span class="sxs-lookup"><span data-stu-id="a9750-430">Slow or hanging app</span></span>

<span data-ttu-id="a9750-431">Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="a9750-431">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="a9750-432">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="a9750-432">App crashes or encounters an exception</span></span>

<span data-ttu-id="a9750-433">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="a9750-433">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="a9750-434">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="a9750-434">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="a9750-435">Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-435">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="a9750-436">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="a9750-436">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="a9750-437">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-437">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="a9750-438">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-438">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="a9750-439">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="a9750-439">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="a9750-440">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="a9750-440">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="a9750-441">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="a9750-441">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="a9750-442">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="a9750-442">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="a9750-443">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="a9750-443">Analyze the dump</span></span>

<span data-ttu-id="a9750-444">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-444">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="a9750-445">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="a9750-445">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9750-446">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a9750-446">Additional resources</span></span>

* <span data-ttu-id="a9750-447">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="a9750-447">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="a9750-448">Eine ASP.NET Core-App kann unter Windows als [Windows-Dienst](/dotnet/framework/windows-services/introduction-to-windows-service-applications) ohne die Verwendung von IIS gehostet werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-448">An ASP.NET Core app can be hosted on Windows as a [Windows Service](/dotnet/framework/windows-services/introduction-to-windows-service-applications) without using IIS.</span></span> <span data-ttu-id="a9750-449">Wenn die App als Windows-Dienst gehostet und der Server neu gestartet wird, startet diese automatisch.</span><span class="sxs-lookup"><span data-stu-id="a9750-449">When hosted as a Windows Service, the app automatically starts after server reboots.</span></span>

<span data-ttu-id="a9750-450">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a9750-450">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a9750-451">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="a9750-451">Prerequisites</span></span>

* [<span data-ttu-id="a9750-452">ASP.NET Core SDK 2.1 oder höher</span><span class="sxs-lookup"><span data-stu-id="a9750-452">ASP.NET Core SDK 2.1 or later</span></span>](https://dotnet.microsoft.com/download)
* [<span data-ttu-id="a9750-453">PowerShell 6.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="a9750-453">PowerShell 6.2 or later</span></span>](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a><span data-ttu-id="a9750-454">App-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a9750-454">App configuration</span></span>

<span data-ttu-id="a9750-455">Die App benötigt Paketverweise für [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) und [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span><span class="sxs-lookup"><span data-stu-id="a9750-455">The app requires package references for [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) and [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog).</span></span>

<span data-ttu-id="a9750-456">Um bei der Ausführung außerhalb eines Dienstes zu testen und zu debuggen, fügen Sie Code hinzu, um festzustellen, ob die Anwendung als Dienst oder als Konsolenanwendung ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-456">To test and debug when running outside of a service, add code to determine if the app is running as a service or a console app.</span></span> <span data-ttu-id="a9750-457">Überprüfen Sie, ob der Debugger angefügt ist oder ob es einen `--console`-Parameter gibt.</span><span class="sxs-lookup"><span data-stu-id="a9750-457">Inspect if the debugger is attached or a `--console` switch is present.</span></span> <span data-ttu-id="a9750-458">Wenn eine dieser Bedingungen erfüllt ist (die App wird nicht als Dienst ausgeführt), rufen Sie <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*> auf.</span><span class="sxs-lookup"><span data-stu-id="a9750-458">If either condition is true (the app isn't run as a service), call <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>.</span></span> <span data-ttu-id="a9750-459">Wenn die Bedingungen nicht zutreffen (die App als Dienst ausgeführt wird):</span><span class="sxs-lookup"><span data-stu-id="a9750-459">If the conditions are false (the app is run as a service):</span></span>

* <span data-ttu-id="a9750-460">Rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> auf, und verwenden Sie einen Pfad zum veröffentlichten Speicherort der App.</span><span class="sxs-lookup"><span data-stu-id="a9750-460">Call <xref:System.IO.Directory.SetCurrentDirectory*> and use a path to the app's published location.</span></span> <span data-ttu-id="a9750-461">Rufen Sie nicht <xref:System.IO.Directory.GetCurrentDirectory*> auf, um den Pfad abzurufen, da eine Windows-Dienst-App den Ordner *C:\\WINDOWS\\system32* zurückgibt, wenn <xref:System.IO.Directory.GetCurrentDirectory*> aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-461">Don't call <xref:System.IO.Directory.GetCurrentDirectory*> to obtain the path because a Windows Service app returns the *C:\\WINDOWS\\system32* folder when <xref:System.IO.Directory.GetCurrentDirectory*> is called.</span></span> <span data-ttu-id="a9750-462">Weitere Informationen finden Sie im Abschnitt [Aktuelles Verzeichnis und Inhaltsstammverzeichnis](#current-directory-and-content-root).</span><span class="sxs-lookup"><span data-stu-id="a9750-462">For more information, see the [Current directory and content root](#current-directory-and-content-root) section.</span></span> <span data-ttu-id="a9750-463">Dieser Schritt wird ausgeführt, bevor die App in `CreateWebHostBuilder` konfiguriert wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-463">This step is performed before the app is configured in `CreateWebHostBuilder`.</span></span>
* <span data-ttu-id="a9750-464">Rufen Sie <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> auf, um die App als Dienst auszuführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-464">Call <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> to run the app as a service.</span></span>

<span data-ttu-id="a9750-465">Da der [Anbieter der Befehlszeilenkonfiguration](xref:fundamentals/configuration/index#command-line-configuration-provider) Name/Wert-Paare für Befehlszeilenargumente benötigt, wird der Parameter `--console` aus den Argumenten entfernt, bevor <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> diese empfängt.</span><span class="sxs-lookup"><span data-stu-id="a9750-465">Because the [Command-line Configuration Provider](xref:fundamentals/configuration/index#command-line-configuration-provider) requires name-value pairs for command-line arguments, the `--console` switch is removed from the arguments before <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> receives the arguments.</span></span>

<span data-ttu-id="a9750-466">Um das Windows-Ereignisprotokoll zu schreiben, fügen Sie den EventLog-Anbieter zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*> hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-466">To write to the Windows Event Log, add the EventLog provider to <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>.</span></span> <span data-ttu-id="a9750-467">Legen Sie den Protokollierungsgrad mit dem `Logging:LogLevel:Default`-Schlüssel in der *appsettings.Production.json*-Datei fest.</span><span class="sxs-lookup"><span data-stu-id="a9750-467">Set the logging level with the `Logging:LogLevel:Default` key in the *appsettings.Production.json* file.</span></span>

<span data-ttu-id="a9750-468">Im folgenden Beispiel aus der Beispiel-App wird `RunAsCustomService` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> aufgerufen, um Ereignisse in Bezug auf die Lebensdauer innerhalb der App zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="a9750-468">In the following example from the sample app, `RunAsCustomService` is called instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in order to handle lifetime events within the app.</span></span> <span data-ttu-id="a9750-469">Weitere Informationen finden Sie im Abschnitt [Behandeln von Start- und Stopereignissen](#handle-starting-and-stopping-events).</span><span class="sxs-lookup"><span data-stu-id="a9750-469">For more information, see the [Handle starting and stopping events](#handle-starting-and-stopping-events) section.</span></span>

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a><span data-ttu-id="a9750-470">Bereitstellungstyp</span><span class="sxs-lookup"><span data-stu-id="a9750-470">Deployment type</span></span>

<span data-ttu-id="a9750-471">Weitere Informationen und Tipps zu Bereitstellungsszenarien finden Sie unter [.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/).</span><span class="sxs-lookup"><span data-stu-id="a9750-471">For information and advice on deployment scenarios, see [.NET Core application deployment](/dotnet/core/deploying/).</span></span>

### <a name="sdk"></a><span data-ttu-id="a9750-472">SDK</span><span class="sxs-lookup"><span data-stu-id="a9750-472">SDK</span></span>

<span data-ttu-id="a9750-473">Geben Sie für einen Web-App-basierten Dienst, der die Razor Pages- oder MVC-Frameworks verwendet, das Web-SDK in der Projektdatei an:</span><span class="sxs-lookup"><span data-stu-id="a9750-473">For a web app-based service that uses the Razor Pages or MVC frameworks, specify the Web SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="a9750-474">Geben Sie das Worker-SDK in der Projektdatei an, wenn der Dienst nur Hintergrundaufgaben ausführt (z. B. [gehostete Dienste](xref:fundamentals/host/hosted-services)):</span><span class="sxs-lookup"><span data-stu-id="a9750-474">If the service only executes background tasks (for example, [hosted services](xref:fundamentals/host/hosted-services)), specify the Worker SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a><span data-ttu-id="a9750-475">Frameworkabhängige Bereitstellung (Framework-dependent deployment, FDD)</span><span class="sxs-lookup"><span data-stu-id="a9750-475">Framework-dependent deployment (FDD)</span></span>

<span data-ttu-id="a9750-476">Eine Framework-abhängige Bereitstellung (Framework-Dependent Deployment, FDD) benötigt eine gemeinsame systemweite Version von .NET Core auf dem Zielsystem.</span><span class="sxs-lookup"><span data-stu-id="a9750-476">Framework-dependent deployment (FDD) relies on the presence of a shared system-wide version of .NET Core on the target system.</span></span> <span data-ttu-id="a9750-477">Wenn das FDD-Szenario gemäß der Anleitung in diesem Artikel übernommen wird, erzeugt das SDK eine ausführbare Datei ( *.exe*). Diese wird als *frameworkabhängige ausführbare Datei* bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="a9750-477">When the FDD scenario is adopted following the guidance in this article, the SDK produces an executable (*.exe*), called a *framework-dependent executable*.</span></span>

<span data-ttu-id="a9750-478">Der [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ([\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifier)) enthält das Zielframework.</span><span class="sxs-lookup"><span data-stu-id="a9750-478">The Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>](/dotnet/core/tools/csproj#runtimeidentifier)) contains the target framework.</span></span> <span data-ttu-id="a9750-479">Im folgenden Beispiel wird die RID auf `win7-x64` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a9750-479">In the following example, the RID is set to `win7-x64`.</span></span> <span data-ttu-id="a9750-480">Die `<SelfContained>`-Eigenschaft ist auf `false` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a9750-480">The `<SelfContained>` property is set to `false`.</span></span> <span data-ttu-id="a9750-481">Diese Eigenschaften geben dem SDK die Anweisung, eine ausführbare Datei ( *.exe*) für Windows sowie eine App zu generieren, die vom freigegebenen .NET Core-Framework abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="a9750-481">These properties instruct the SDK to generate an executable (*.exe*) file for Windows and an app that depends on the shared .NET Core framework.</span></span>

<span data-ttu-id="a9750-482">Die `<UseAppHost>`-Eigenschaft ist auf `true` festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a9750-482">The `<UseAppHost>` property is set to `true`.</span></span> <span data-ttu-id="a9750-483">Diese Eigenschaft stellt für den Dienst einen Aktivierungspfad (eine ausführbare Datei, *EXE*) für eine frameworkabhängige Bereitstellung bereit.</span><span class="sxs-lookup"><span data-stu-id="a9750-483">This property provides the service with an activation path (an executable, *.exe*) for an FDD.</span></span>

<span data-ttu-id="a9750-484">Eine *web.config*-Datei, die normalerweise erstellt wird, wenn Sie eine ASP.NET Core-App veröffentlichen, ist für eine Windows Services-App nicht erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a9750-484">A *web.config* file, which is normally produced when publishing an ASP.NET Core app, is unnecessary for a Windows Services app.</span></span> <span data-ttu-id="a9750-485">Um die Erstellung der *web.config*-Datei zu deaktivieren, fügen Sie die auf `true` festgelegte `<IsTransformWebConfigDisabled>`-Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-485">To disable the creation of the *web.config* file, add the `<IsTransformWebConfigDisabled>` property set to `true`.</span></span>

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a><span data-ttu-id="a9750-486">Eigenständige Bereitstellung</span><span class="sxs-lookup"><span data-stu-id="a9750-486">Self-contained deployment (SCD)</span></span>

<span data-ttu-id="a9750-487">Eigenständige Bereitstellungen (Self-Contained Deployment, SCD) benötigen kein gemeinsames Framework auf dem Hostsystem.</span><span class="sxs-lookup"><span data-stu-id="a9750-487">Self-contained deployment (SCD) doesn't rely on the presence of a shared framework on the host system.</span></span> <span data-ttu-id="a9750-488">Die Runtime und die App-Abhängigkeiten werden mit der App bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a9750-488">The runtime and the app's dependencies are deployed with the app.</span></span>

<span data-ttu-id="a9750-489">Ein [Runtimebezeichner](/dotnet/core/rid-catalog) (Runtime Identifier, RID) für Windows ist im `<PropertyGroup>`-Objekt enthalten, das wiederum das Zielframework enthält:</span><span class="sxs-lookup"><span data-stu-id="a9750-489">A Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) is included in the `<PropertyGroup>` that contains the target framework:</span></span>

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

<span data-ttu-id="a9750-490">So führen Sie die Veröffentlichung für mehrere RIDs aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-490">To publish for multiple RIDs:</span></span>

* <span data-ttu-id="a9750-491">Geben Sie die RIDs in einer durch Semikolons getrennten Liste an.</span><span class="sxs-lookup"><span data-stu-id="a9750-491">Provide the RIDs in a semicolon-delimited list.</span></span>
* <span data-ttu-id="a9750-492">Verwenden Sie den Eigenschaftsnamen [\<Runtimebezeichner>](/dotnet/core/tools/csproj#runtimeidentifiers) (Plural).</span><span class="sxs-lookup"><span data-stu-id="a9750-492">Use the property name [\<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (plural).</span></span>

<span data-ttu-id="a9750-493">Weitere Informationen finden Sie im [.NET Core RID-Katalog](/dotnet/core/rid-catalog).</span><span class="sxs-lookup"><span data-stu-id="a9750-493">For more information, see [.NET Core RID Catalog](/dotnet/core/rid-catalog).</span></span>

<span data-ttu-id="a9750-494">Eine `<SelfContained>`-Eigenschaft wird auf `true` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="a9750-494">A `<SelfContained>` property is set to `true`:</span></span>

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a><span data-ttu-id="a9750-495">Benutzerkonten für Dienste</span><span class="sxs-lookup"><span data-stu-id="a9750-495">Service user account</span></span>

<span data-ttu-id="a9750-496">Wenn Sie ein Benutzerkonto für einen Dienst erstellen möchten, verwenden Sie das Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) in einer PowerShell 6-Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="a9750-496">To create a user account for a service, use the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet from an administrative PowerShell 6 command shell.</span></span>

<span data-ttu-id="a9750-497">Unter Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763) oder höher:</span><span class="sxs-lookup"><span data-stu-id="a9750-497">On Windows 10 October 2018 Update (version 1809/build 10.0.17763) or later:</span></span>

```powershell
New-LocalUser -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-498">Unter Windows mit einer früheren Betriebssystemversion als Windows 10 mit dem Update von Oktober 2018 (Version 1809/Build 10.0.17763):</span><span class="sxs-lookup"><span data-stu-id="a9750-498">On Windows OS earlier than the Windows 10 October 2018 Update (version 1809/build 10.0.17763):</span></span>

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

<span data-ttu-id="a9750-499">Geben Sie bei Aufforderung ein [sicheres Kennwort](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) ein.</span><span class="sxs-lookup"><span data-stu-id="a9750-499">Provide a [strong password](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) when prompted.</span></span>

<span data-ttu-id="a9750-500">Solange der Parameter `-AccountExpires` dem Cmdlet [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) nicht mit einem Ablaufdatum <xref:System.DateTime> angegeben wird, läuft das Konto nicht ab.</span><span class="sxs-lookup"><span data-stu-id="a9750-500">Unless the `-AccountExpires` parameter is supplied to the [New-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet with an expiration <xref:System.DateTime>, the account doesn't expire.</span></span>

<span data-ttu-id="a9750-501">Weitere Informationen finden Sie unter [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) und [Dienstbenutzerkonten](/windows/desktop/services/service-user-accounts).</span><span class="sxs-lookup"><span data-stu-id="a9750-501">For more information, see [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) and [Service User Accounts](/windows/desktop/services/service-user-accounts).</span></span>

<span data-ttu-id="a9750-502">Eine alternative Methode zum Verwalten von Benutzern bei Verwendung von Active Directory ist die Verwendung von verwalteten Dienstkonten.</span><span class="sxs-lookup"><span data-stu-id="a9750-502">An alternative approach to managing users when using Active Directory is to use Managed Service Accounts.</span></span> <span data-ttu-id="a9750-503">Weitere Informationen finden Sie unter [Gruppenverwaltete Dienstkonten: Übersicht](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span><span class="sxs-lookup"><span data-stu-id="a9750-503">For more information, see [Group Managed Service Accounts Overview](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview).</span></span>

## <a name="log-on-as-a-service-rights"></a><span data-ttu-id="a9750-504">Rechte zum Anmelden als Dienst</span><span class="sxs-lookup"><span data-stu-id="a9750-504">Log on as a service rights</span></span>

<span data-ttu-id="a9750-505">Gehen Sie wie folgt vor, um Rechte zum *Anmelden als Dienst* für das Benutzerkonto eines Diensts einzurichten:</span><span class="sxs-lookup"><span data-stu-id="a9750-505">To establish *Log on as a service* rights for a service user account:</span></span>

1. <span data-ttu-id="a9750-506">Öffnen Sie den Editor für lokale Sicherheitsrichtlinien, indem Sie die Datei *secpol.msc* ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-506">Open the Local Security Policy editor by running *secpol.msc*.</span></span>
1. <span data-ttu-id="a9750-507">Erweitern Sie den Knoten **Lokale Richtlinien**, und klicken Sie auf **Zuweisen von Benutzerrechten**.</span><span class="sxs-lookup"><span data-stu-id="a9750-507">Expand the **Local Policies** node and select **User Rights Assignment**.</span></span>
1. <span data-ttu-id="a9750-508">Öffnen Sie die Richtlinie **Anmelden als Dienst**.</span><span class="sxs-lookup"><span data-stu-id="a9750-508">Open the **Log on as a service** policy.</span></span>
1. <span data-ttu-id="a9750-509">Wählen Sie **Benutzer oder Gruppe hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-509">Select **Add User or Group**.</span></span>
1. <span data-ttu-id="a9750-510">Geben Sie den Objektnamen (das Benutzerkonto) an. Dafür gibt es zwei Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="a9750-510">Provide the object name (user account) using either of the following approaches:</span></span>
   1. <span data-ttu-id="a9750-511">Geben Sie die Bezeichnung des Benutzerkontos (`{DOMAIN OR COMPUTER NAME\USER}`) in das Feld für den Objektnamen ein, und klicken Sie auf **OK**, um den Benutzer der Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a9750-511">Type the user account (`{DOMAIN OR COMPUTER NAME\USER}`) in the object name field and select **OK** to add the user to the policy.</span></span>
   1. <span data-ttu-id="a9750-512">Wählen Sie **Erweitert** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-512">Select **Advanced**.</span></span> <span data-ttu-id="a9750-513">Klicken Sie auf **Find Now** (Suche starten).</span><span class="sxs-lookup"><span data-stu-id="a9750-513">Select **Find Now**.</span></span> <span data-ttu-id="a9750-514">Wählen Sie das betreffende Benutzerkonto aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-514">Select the user account from the list.</span></span> <span data-ttu-id="a9750-515">Klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="a9750-515">Select **OK**.</span></span> <span data-ttu-id="a9750-516">Klicken Sie erneut auf **OK**, um den Benutzer zur Richtlinie hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a9750-516">Select **OK** again to add the user to the policy.</span></span>
1. <span data-ttu-id="a9750-517">Klicken Sie auf **OK** oder auf **Anwenden**, um die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="a9750-517">Select **OK** or **Apply** to accept the changes.</span></span>

## <a name="create-and-manage-the-windows-service"></a><span data-ttu-id="a9750-518">Erstellen und Verwalten des Windows-Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-518">Create and manage the Windows Service</span></span>

### <a name="create-a-service"></a><span data-ttu-id="a9750-519">Erstellen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-519">Create a service</span></span>

<span data-ttu-id="a9750-520">Verwenden Sie PowerShell-Befehle, um einen Dienst zu registrieren.</span><span class="sxs-lookup"><span data-stu-id="a9750-520">Use PowerShell commands to register a service.</span></span> <span data-ttu-id="a9750-521">Führen Sie über eine administrative PowerShell 6-Befehlsshell die folgenden Befehle aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-521">From an administrative PowerShell 6 command shell, execute the following commands:</span></span>

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* <span data-ttu-id="a9750-522">`{EXE PATH}`: entspricht dem Pfad zum Ordner der App auf dem Host (z. B. `d:\myservice`).</span><span class="sxs-lookup"><span data-stu-id="a9750-522">`{EXE PATH}` &ndash; Path to the app's folder on the host (for example, `d:\myservice`).</span></span> <span data-ttu-id="a9750-523">Fügen Sie nicht die ausführbare Datei der App in den Pfad ein.</span><span class="sxs-lookup"><span data-stu-id="a9750-523">Don't include the app's executable in the path.</span></span> <span data-ttu-id="a9750-524">Außerdem benötigen Sie keinen nachstehenden Schrägstrich.</span><span class="sxs-lookup"><span data-stu-id="a9750-524">A trailing slash isn't required.</span></span>
* <span data-ttu-id="a9750-525">`{DOMAIN OR COMPUTER NAME\USER}`: entspricht dem Benutzerkonto für den Dienst (z. B. `Contoso\ServiceUser`).</span><span class="sxs-lookup"><span data-stu-id="a9750-525">`{DOMAIN OR COMPUTER NAME\USER}` &ndash; Service user account (for example, `Contoso\ServiceUser`).</span></span>
* <span data-ttu-id="a9750-526">`{SERVICE NAME}`: entspricht dem Dienstnamen (z. B. `MyService`).</span><span class="sxs-lookup"><span data-stu-id="a9750-526">`{SERVICE NAME}` &ndash; Service name (for example, `MyService`).</span></span>
* <span data-ttu-id="a9750-527">`{EXE FILE PATH}`: entspricht dem Pfad zur ausführbaren Datei der App (z. B. `d:\myservice\myservice.exe`).</span><span class="sxs-lookup"><span data-stu-id="a9750-527">`{EXE FILE PATH}` &ndash; The app's executable path (for example, `d:\myservice\myservice.exe`).</span></span> <span data-ttu-id="a9750-528">Fügen Sie den Namen der ausführbaren Datei einschließlich ihrer Erweiterung hinzu.</span><span class="sxs-lookup"><span data-stu-id="a9750-528">Include the executable's file name with extension.</span></span>
* <span data-ttu-id="a9750-529">`{DESCRIPTION}`: ist eine Beschreibung des Diensts (z. B. `My sample service`).</span><span class="sxs-lookup"><span data-stu-id="a9750-529">`{DESCRIPTION}` &ndash; Service description (for example, `My sample service`).</span></span>
* <span data-ttu-id="a9750-530">`{DISPLAY NAME}`: entspricht dem Anzeigenamen des Diensts (z. B. `My Service`).</span><span class="sxs-lookup"><span data-stu-id="a9750-530">`{DISPLAY NAME}` &ndash; Service display name (for example, `My Service`).</span></span>

### <a name="start-a-service"></a><span data-ttu-id="a9750-531">Starten eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-531">Start a service</span></span>

<span data-ttu-id="a9750-532">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls starten:</span><span class="sxs-lookup"><span data-stu-id="a9750-532">Start a service with the following PowerShell 6 command:</span></span>

```powershell
Start-Service -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-533">Das Starten des Diensts dauert ein paar Sekunden.</span><span class="sxs-lookup"><span data-stu-id="a9750-533">The command takes a few seconds to start the service.</span></span>

### <a name="determine-a-services-status"></a><span data-ttu-id="a9750-534">Ermitteln des Status eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-534">Determine a service's status</span></span>

<span data-ttu-id="a9750-535">Sie können den Status von Diensten mithilfe des folgenden PowerShell 6-Befehls überprüfen:</span><span class="sxs-lookup"><span data-stu-id="a9750-535">To check the status of a service, use the following PowerShell 6 command:</span></span>

```powershell
Get-Service -Name {SERVICE NAME}
```

<span data-ttu-id="a9750-536">Der Status wird als einer der folgenden Werte gemeldet:</span><span class="sxs-lookup"><span data-stu-id="a9750-536">The status is reported as one of the following values:</span></span>

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a><span data-ttu-id="a9750-537">Beenden eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-537">Stop a service</span></span>

<span data-ttu-id="a9750-538">Sie können Dienste mithilfe des folgenden PowerShell 6-Befehls beenden:</span><span class="sxs-lookup"><span data-stu-id="a9750-538">Stop a service with the following Powershell 6 command:</span></span>

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a><span data-ttu-id="a9750-539">Entfernen eines Diensts</span><span class="sxs-lookup"><span data-stu-id="a9750-539">Remove a service</span></span>

<span data-ttu-id="a9750-540">Nach einer kurzen Verzögerung durch das Beenden des Diensts können Sie diesen mithilfe des folgenden Powershell 6-Befehls entfernen:</span><span class="sxs-lookup"><span data-stu-id="a9750-540">After a short delay to stop a service, remove a service with the following Powershell 6 command:</span></span>

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a><span data-ttu-id="a9750-541">Behandeln von Start- und Stopereignissen</span><span class="sxs-lookup"><span data-stu-id="a9750-541">Handle starting and stopping events</span></span>

<span data-ttu-id="a9750-542">Gehen Sie wie folgt vor, um <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>-, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>- und <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*>-Ereignisse zu bearbeiten:</span><span class="sxs-lookup"><span data-stu-id="a9750-542">To handle <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*>, <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>, and <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> events:</span></span>

1. <span data-ttu-id="a9750-543">Erstellen Sie eine von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> abgeleitete Klasse mit den `OnStarting`-, `OnStarted`- und `OnStopping`-Methoden:</span><span class="sxs-lookup"><span data-stu-id="a9750-543">Create a class that derives from <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> with the `OnStarting`, `OnStarted`, and `OnStopping` methods:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. <span data-ttu-id="a9750-544">Erstellen Sie eine Erweiterungsmethode für <xref:Microsoft.AspNetCore.Hosting.IWebHost>, die den `CustomWebHostService` an <xref:System.ServiceProcess.ServiceBase.Run*> übergibt:</span><span class="sxs-lookup"><span data-stu-id="a9750-544">Create an extension method for <xref:Microsoft.AspNetCore.Hosting.IWebHost> that passes the `CustomWebHostService` to <xref:System.ServiceProcess.ServiceBase.Run*>:</span></span>

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. <span data-ttu-id="a9750-545">Rufen Sie in `Program.Main` anstelle von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> die Erweiterungsmethode `RunAsCustomService` auf:</span><span class="sxs-lookup"><span data-stu-id="a9750-545">In `Program.Main`, call the `RunAsCustomService` extension method instead of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>:</span></span>

   ```csharp
   host.RunAsCustomService();
   ```

   <span data-ttu-id="a9750-546">Wenn Sie Informationen dazu benötigen, wie Sie den Speicherort von <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main` abrufen können, sehen Sie sich das Codebeispiel im Abschnitt [Bereitstellungstyp](#deployment-type) an.</span><span class="sxs-lookup"><span data-stu-id="a9750-546">To see the location of <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> in `Program.Main`, refer to the code sample shown in the [Deployment type](#deployment-type) section.</span></span>

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="a9750-547">Proxyserver und Lastenausgleichsszenarien</span><span class="sxs-lookup"><span data-stu-id="a9750-547">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="a9750-548">Dienste, die mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren und hinter einem Proxy oder Lastenausgleich ausgeführt werden, erfordern möglicherweise zusätzliche Konfigurationen.</span><span class="sxs-lookup"><span data-stu-id="a9750-548">Services that interact with requests from the Internet or a corporate network and are behind a proxy or load balancer might require additional configuration.</span></span> <span data-ttu-id="a9750-549">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="a9750-549">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="configure-endpoints"></a><span data-ttu-id="a9750-550">Konfigurieren von Endpunkten</span><span class="sxs-lookup"><span data-stu-id="a9750-550">Configure endpoints</span></span>

<span data-ttu-id="a9750-551">Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.</span><span class="sxs-lookup"><span data-stu-id="a9750-551">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="a9750-552">Konfigurieren Sie URL und Port, indem Sie die `ASPNETCORE_URLS`-Umgebungsvariable festlegen.</span><span class="sxs-lookup"><span data-stu-id="a9750-552">Configure the URL and port by setting the `ASPNETCORE_URLS` environment variable.</span></span>

<span data-ttu-id="a9750-553">Weitere Informationen zu den Konfigurationsansätzen für URLs und Ports finden Sie im relevanten Serverartikel:</span><span class="sxs-lookup"><span data-stu-id="a9750-553">For additional URL and port configuration approaches, see the relevant server article:</span></span>

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

<span data-ttu-id="a9750-554">Die vorangehende Anleitung behandelt die Unterstützung für HTTPS-Endpunkte.</span><span class="sxs-lookup"><span data-stu-id="a9750-554">The preceding guidance covers support for HTTPS endpoints.</span></span> <span data-ttu-id="a9750-555">Konfigurieren Sie z. B. die APP für HTTPS, wenn die Authentifizierung mit einem Windows-Dienst verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-555">For example, configure the app for HTTPS when authentication is used with a Windows Service.</span></span>

> [!NOTE]
> <span data-ttu-id="a9750-556">Die Verwendung des ASP.NET Core-HTTPS-Entwicklerzertifikats zum Schützen eines Dienstendpunkts wird nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a9750-556">Use of the ASP.NET Core HTTPS development certificate to secure a service endpoint isn't supported.</span></span>

## <a name="current-directory-and-content-root"></a><span data-ttu-id="a9750-557">Aktuelles Verzeichnis und Inhaltsstammverzeichnis</span><span class="sxs-lookup"><span data-stu-id="a9750-557">Current directory and content root</span></span>

<span data-ttu-id="a9750-558">Das aktuelle Arbeitsverzeichnis, das durch Aufrufen von <xref:System.IO.Directory.GetCurrentDirectory*> für einen Windows-Dienst zurückgegeben wird, ist der Ordner *C:\\WINDOWS\\system32*.</span><span class="sxs-lookup"><span data-stu-id="a9750-558">The current working directory returned by calling <xref:System.IO.Directory.GetCurrentDirectory*> for a Windows Service is the *C:\\WINDOWS\\system32* folder.</span></span> <span data-ttu-id="a9750-559">Der Ordner *system32* ist kein geeigneter Speicherort für die Dateien eines Diensts (z.B. Einstellungsdateien).</span><span class="sxs-lookup"><span data-stu-id="a9750-559">The *system32* folder isn't a suitable location to store a service's files (for example, settings files).</span></span> <span data-ttu-id="a9750-560">Verwenden Sie einen der folgenden Ansätze, um die Objekt- und Einstellungsdateien eines Dienstes beizubehalten und darauf zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="a9750-560">Use one of the following approaches to maintain and access a service's assets and settings files.</span></span>

### <a name="set-the-content-root-path-to-the-apps-folder"></a><span data-ttu-id="a9750-561">Legen Sie den Inhaltsstammpfad auf den Ordner der App fest.</span><span class="sxs-lookup"><span data-stu-id="a9750-561">Set the content root path to the app's folder</span></span>

<span data-ttu-id="a9750-562"><xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> entspricht dem Pfad, der für das Argument `binPath` bereitgestellt wird, wenn ein Dienst erstellt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-562">The <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> is the same path provided to the `binPath` argument when a service is created.</span></span> <span data-ttu-id="a9750-563">Anstatt `GetCurrentDirectory` aufzurufen, um Pfade zu Einstellungsdateien zu erstellen, rufen Sie <xref:System.IO.Directory.SetCurrentDirectory*> mit dem Pfad zum [Inhaltsstammverzeichnis](xref:fundamentals/index#content-root) der App auf.</span><span class="sxs-lookup"><span data-stu-id="a9750-563">Instead of calling `GetCurrentDirectory` to create paths to settings files, call <xref:System.IO.Directory.SetCurrentDirectory*> with the path to the app's [content root](xref:fundamentals/index#content-root).</span></span>

<span data-ttu-id="a9750-564">Bestimmen Sie unter `Program.Main` den Pfad zum Ordner der ausführbaren Datei des Dienstes und verwenden Sie den Pfad, um das Inhaltsverzeichnis der App festzulegen:</span><span class="sxs-lookup"><span data-stu-id="a9750-564">In `Program.Main`, determine the path to the folder of the service's executable and use the path to establish the app's content root:</span></span>

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a><span data-ttu-id="a9750-565">Speichern der Dateien eines Diensts an einem geeigneten Ort auf dem Datenträger</span><span class="sxs-lookup"><span data-stu-id="a9750-565">Store a service's files in a suitable location on disk</span></span>

<span data-ttu-id="a9750-566">Geben Sie mit <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> beim Verwenden von <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> einen absoluten Pfad zu dem Ordner an, der die Dateien enthält.</span><span class="sxs-lookup"><span data-stu-id="a9750-566">Specify an absolute path with <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> when using an <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> to the folder containing the files.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="a9750-567">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="a9750-567">Troubleshoot</span></span>

<span data-ttu-id="a9750-568">Informationen zur Problembehandlung für Windows-Dienstanwendungen finden Sie unter <xref:test/troubleshoot>.</span><span class="sxs-lookup"><span data-stu-id="a9750-568">To troubleshoot a Windows Service app, see <xref:test/troubleshoot>.</span></span>

### <a name="common-errors"></a><span data-ttu-id="a9750-569">Häufige Fehler</span><span class="sxs-lookup"><span data-stu-id="a9750-569">Common errors</span></span>

* <span data-ttu-id="a9750-570">Eine alte oder eine Vorabversion von PowerShell wird verwendet.</span><span class="sxs-lookup"><span data-stu-id="a9750-570">An old or pre-release version of PowerShell is in use.</span></span>
* <span data-ttu-id="a9750-571">Der registrierte Dienst verwendet nicht die **veröffentlichte** Ausgabe des Befehls [dotnet publish](/dotnet/core/tools/dotnet-publish) der Anwendung.</span><span class="sxs-lookup"><span data-stu-id="a9750-571">The registered service doesn't use the app's **published** output from the [dotnet publish](/dotnet/core/tools/dotnet-publish) command.</span></span> <span data-ttu-id="a9750-572">Die Ausgabe des Befehls [dotnet build](/dotnet/core/tools/dotnet-build) wird nicht für die Anwendungsbereitstellung unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a9750-572">Output of the [dotnet build](/dotnet/core/tools/dotnet-build) command isn't supported for app deployment.</span></span> <span data-ttu-id="a9750-573">Veröffentlichte Ressourcen sind je nach Bereitstellungstyp in einem der folgenden Ordner vorzufinden:</span><span class="sxs-lookup"><span data-stu-id="a9750-573">Published assets are found in either of the following folders depending on the deployment type:</span></span>
  * <span data-ttu-id="a9750-574">*bin/Release/{ZIELFRAMEWORK}/publish* (FDD)</span><span class="sxs-lookup"><span data-stu-id="a9750-574">*bin/Release/{TARGET FRAMEWORK}/publish* (FDD)</span></span>
  * <span data-ttu-id="a9750-575">*bin/Release/{ZIELFRAMEWORK}/{RUNTIME-ID}/publish* (SCD)</span><span class="sxs-lookup"><span data-stu-id="a9750-575">*bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)</span></span>
* <span data-ttu-id="a9750-576">Der Dienst weist nicht den Status „WIRD AUSGEFÜHRT“ auf.</span><span class="sxs-lookup"><span data-stu-id="a9750-576">The service isn't in the RUNNING state.</span></span>
* <span data-ttu-id="a9750-577">Die App enthält falsche Pfade zu Ressourcen (z. B. Zertifikate).</span><span class="sxs-lookup"><span data-stu-id="a9750-577">The paths to resources that the app uses (for example, certificates) are incorrect.</span></span> <span data-ttu-id="a9750-578">Der Basispfad eines Windows-Diensts lautet: *C:\\Windows\\System32*.</span><span class="sxs-lookup"><span data-stu-id="a9750-578">The base path of a Windows Service is *c:\\Windows\\System32*.</span></span>
* <span data-ttu-id="a9750-579">Der Benutzer verfügt nicht über die Berechtigung *Als Dienst anmelden*.</span><span class="sxs-lookup"><span data-stu-id="a9750-579">The user doesn't have *Log on as a service* rights.</span></span>
* <span data-ttu-id="a9750-580">Das Kennwort des Benutzers ist abgelaufen oder wird nicht ordnungsgemäß übermittelt, wenn der PowerShell-Befehl `New-Service` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a9750-580">The user's password is expired or incorrectly passed when executing the `New-Service` PowerShell command.</span></span>
* <span data-ttu-id="a9750-581">Die App erfordert ASP.NET Core-Authentifizierung, aber sie wurde nicht für sichere (HTTPS-)Verbindungen konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a9750-581">The app requires ASP.NET Core authentication but isn't configured for secure connections (HTTPS).</span></span>
* <span data-ttu-id="a9750-582">Der Port für die Anforderungs-URL in der App ist fehlerhaft oder nicht ordnungsgemäß konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="a9750-582">The request URL port is incorrect or not configured correctly in the app.</span></span>

### <a name="system-and-application-event-logs"></a><span data-ttu-id="a9750-583">System- und Anwendungsereignisprotokolle</span><span class="sxs-lookup"><span data-stu-id="a9750-583">System and Application Event Logs</span></span>

<span data-ttu-id="a9750-584">So greifen Sie auf die System- und Anwendungsereignisprotokolle zu:</span><span class="sxs-lookup"><span data-stu-id="a9750-584">Access the System and Application Event Logs:</span></span>

1. <span data-ttu-id="a9750-585">Öffnen Sie das Startmenü, suchen Sie nach der *Ereignisanzeige*, und wählen Sie dann die App **Ereignisanzeige** aus.</span><span class="sxs-lookup"><span data-stu-id="a9750-585">Open the Start menu, search for *Event Viewer*, and select the **Event Viewer** app.</span></span>
1. <span data-ttu-id="a9750-586">Öffnen Sie unter **Ereignisanzeige** den Knoten **Windows-Protokolle**.</span><span class="sxs-lookup"><span data-stu-id="a9750-586">In **Event Viewer**, open the **Windows Logs** node.</span></span>
1. <span data-ttu-id="a9750-587">Klicken Sie auf **System**, um das Systemereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="a9750-587">Select **System** to open the System Event Log.</span></span> <span data-ttu-id="a9750-588">Wählen Sie **Anwendung** aus, um das Anwendungsereignisprotokoll zu öffnen.</span><span class="sxs-lookup"><span data-stu-id="a9750-588">Select **Application** to open the Application Event Log.</span></span>
1. <span data-ttu-id="a9750-589">Suchen Sie nach Fehlern, die mit der fehlerhaften App im Zusammenhang stehen.</span><span class="sxs-lookup"><span data-stu-id="a9750-589">Search for errors associated with the failing app.</span></span>

### <a name="run-the-app-at-a-command-prompt"></a><span data-ttu-id="a9750-590">Ausführen der App in einer Eingabeaufforderung</span><span class="sxs-lookup"><span data-stu-id="a9750-590">Run the app at a command prompt</span></span>

<span data-ttu-id="a9750-591">Viele Startfehler erzeugen keine nützlichen Informationen in den Ereignisprotokollen.</span><span class="sxs-lookup"><span data-stu-id="a9750-591">Many startup errors don't produce useful information in the event logs.</span></span> <span data-ttu-id="a9750-592">Sie können die Ursache für einige Fehler ermitteln, indem Sie die App in einer Eingabeaufforderung auf dem Hostsystem ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-592">You can find the cause of some errors by running the app at a command prompt on the hosting system.</span></span> <span data-ttu-id="a9750-593">Senken Sie den [Protokolliergrad](xref:fundamentals/logging/index#log-level), oder führen Sie die App in der [Entwicklungsumgebung](xref:fundamentals/environments) aus, um zusätzliche Informationen von der App zu protokollieren.</span><span class="sxs-lookup"><span data-stu-id="a9750-593">To log additional detail from the app, lower the [log level](xref:fundamentals/logging/index#log-level) or run the app in the [Development environment](xref:fundamentals/environments).</span></span>

### <a name="clear-package-caches"></a><span data-ttu-id="a9750-594">Bereinigen der Paketcaches</span><span class="sxs-lookup"><span data-stu-id="a9750-594">Clear package caches</span></span>

<span data-ttu-id="a9750-595">Eine funktionsfähige App kann direkt nach der Durchführung eines Upgrades für das .NET Core SDK auf dem Entwicklungscomputer oder einer Änderung der Paketversionen in der App fehlschlagen.</span><span class="sxs-lookup"><span data-stu-id="a9750-595">A functioning app may fail immediately after upgrading either the .NET Core SDK on the development machine or changing package versions within the app.</span></span> <span data-ttu-id="a9750-596">In einigen Fällen können inkohärente Pakete eine App beschädigen, wenn größere Upgrades durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-596">In some cases, incoherent packages may break an app when performing major upgrades.</span></span> <span data-ttu-id="a9750-597">Die meisten dieser Probleme können durch Befolgung der folgenden Anweisungen behoben werden:</span><span class="sxs-lookup"><span data-stu-id="a9750-597">Most of these issues can be fixed by following these instructions:</span></span>

1. <span data-ttu-id="a9750-598">Löschen Sie die Ordner *bin* und *obj*.</span><span class="sxs-lookup"><span data-stu-id="a9750-598">Delete the *bin* and *obj* folders.</span></span>
1. <span data-ttu-id="a9750-599">Bereinigen Sie die Paketcaches, indem Sie [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) über eine Befehlsshell ausführen.</span><span class="sxs-lookup"><span data-stu-id="a9750-599">Clear the package caches by executing [dotnet nuget locals all --clear](/dotnet/core/tools/dotnet-nuget-locals) from a command shell.</span></span>

   <span data-ttu-id="a9750-600">Sie können die Paketcaches auch mit dem Tool [nuget.exe](https://www.nuget.org/downloads) und dem Befehl `nuget locals all -clear` bereinigen.</span><span class="sxs-lookup"><span data-stu-id="a9750-600">Clearing package caches can also be accomplished with the [nuget.exe](https://www.nuget.org/downloads) tool and executing the command `nuget locals all -clear`.</span></span> <span data-ttu-id="a9750-601">*nuget.exe* ist wird unter dem Windows Desktop-Betriebssystem nicht gebündelt installiert und muss separat von der [NuGet-Website](https://www.nuget.org/downloads) abgerufen werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-601">*nuget.exe* isn't a bundled install with the Windows desktop operating system and must be obtained separately from the [NuGet website](https://www.nuget.org/downloads).</span></span>

1. <span data-ttu-id="a9750-602">Stellen Sie das Projekt wieder her und erstellen Sie es neu.</span><span class="sxs-lookup"><span data-stu-id="a9750-602">Restore and rebuild the project.</span></span>
1. <span data-ttu-id="a9750-603">Löschen Sie alle Dateien im Bereitstellungsordner auf dem Server, bevor Sie die App noch mal bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="a9750-603">Delete all of the files in the deployment folder on the server prior to redeploying the app.</span></span>

### <a name="slow-or-hanging-app"></a><span data-ttu-id="a9750-604">Langsame oder hängende App</span><span class="sxs-lookup"><span data-stu-id="a9750-604">Slow or hanging app</span></span>

<span data-ttu-id="a9750-605">Ein *Absturzabbild* ist eine Momentaufnahme des Systemarbeitsspeichers, die Ihnen dabei behilflich sein kann, die Ursache eines App-Absturzes, eines Startfehlers oder einer langsamen App zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="a9750-605">A *crash dump* is a snapshot of the system's memory and can help determine the cause of an app crash, startup failure, or slow app.</span></span>

#### <a name="app-crashes-or-encounters-an-exception"></a><span data-ttu-id="a9750-606">App stürzt ab, oder es tritt eine Ausnahme auf</span><span class="sxs-lookup"><span data-stu-id="a9750-606">App crashes or encounters an exception</span></span>

<span data-ttu-id="a9750-607">Abrufen und Analysieren eines Speicherabbilds aus der [Windows-Fehlerberichterstattung (WER)](/windows/desktop/wer/windows-error-reporting):</span><span class="sxs-lookup"><span data-stu-id="a9750-607">Obtain and analyze a dump from [Windows Error Reporting (WER)](/windows/desktop/wer/windows-error-reporting):</span></span>

1. <span data-ttu-id="a9750-608">Erstellen Sie einen Ordner zum Speichern von Absturzabbilddateien unter `c:\dumps`.</span><span class="sxs-lookup"><span data-stu-id="a9750-608">Create a folder to hold crash dump files at `c:\dumps`.</span></span>
1. <span data-ttu-id="a9750-609">Führen Sie das PowerShell-Skript [EnableDumps](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) mit dem Namen des ausführbaren Anwendung aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-609">Run the [EnableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) with the application executable name:</span></span>

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. <span data-ttu-id="a9750-610">Führen Sie die App unter den Bedingungen aus, die dazu führen, dass der Absturz auftritt.</span><span class="sxs-lookup"><span data-stu-id="a9750-610">Run the app under the conditions that cause the crash to occur.</span></span>
1. <span data-ttu-id="a9750-611">Nachdem der Absturz stattgefunden hat, führen Sie das [PowerShell-Skript „DisableDumps“](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1) aus:</span><span class="sxs-lookup"><span data-stu-id="a9750-611">After the crash has occurred, run the [DisableDumps PowerShell script](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1):</span></span>

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

<span data-ttu-id="a9750-612">Nachdem eine Anwendung abgestürzt ist und die Absturzabbildsammlung abgeschlossen ist, kann die App ordnungsgemäß beendet werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-612">After an app crashes and dump collection is complete, the app is allowed to terminate normally.</span></span> <span data-ttu-id="a9750-613">Das PowerShell-Skript konfiguriert WER für die Erfassung von bis zu fünf Absturzabbildern pro App.</span><span class="sxs-lookup"><span data-stu-id="a9750-613">The PowerShell script configures WER to collect up to five dumps per app.</span></span>

> [!WARNING]
> <span data-ttu-id="a9750-614">Absturzabbilder können eine große Menge Speicherplatz in Anspruch nehmen (jeweils bis zu mehreren GB).</span><span class="sxs-lookup"><span data-stu-id="a9750-614">Crash dumps might take up a large amount of disk space (up to several gigabytes each).</span></span>

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a><span data-ttu-id="a9750-615">App hat sich aufgehängt, es tritt ein Fehler während des Starts auf, oder sie wird normal ausgeführt</span><span class="sxs-lookup"><span data-stu-id="a9750-615">App hangs, fails during startup, or runs normally</span></span>

<span data-ttu-id="a9750-616">Wenn sich eine App *aufgehängt* hat (nicht mehr reagiert, aber nicht abstürzt), ein Fehler während des Starts auftritt bzw. die App normal ausgeführt wird, finden Sie weitere Informationen unter [Benutzermodus-Absturzabbilddateien: Auswählen des besten Tools](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool), um ein geeignetes Tool zum Generieren des Speicherabbilds auszuwählen.</span><span class="sxs-lookup"><span data-stu-id="a9750-616">When an app *hangs* (stops responding but doesn't crash), fails during startup, or runs normally, see [User-Mode Dump Files: Choosing the Best Tool](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool) to select an appropriate tool to produce the dump.</span></span>

#### <a name="analyze-the-dump"></a><span data-ttu-id="a9750-617">Analysieren des Speicherabbilds</span><span class="sxs-lookup"><span data-stu-id="a9750-617">Analyze the dump</span></span>

<span data-ttu-id="a9750-618">Ein Speicherabbild kann mithilfe mehrerer Ansätze analysiert werden.</span><span class="sxs-lookup"><span data-stu-id="a9750-618">A dump can be analyzed using several approaches.</span></span> <span data-ttu-id="a9750-619">Weitere Informationen finden Sie unter [Analysieren einer Benutzermodus-Speicherabbilddatei](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span><span class="sxs-lookup"><span data-stu-id="a9750-619">For more information, see [Analyzing a User-Mode Dump File](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a9750-620">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a9750-620">Additional resources</span></span>

* <span data-ttu-id="a9750-621">[Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) (einschließlich HTTPS-Konfiguration und Unterstützung für SNI)</span><span class="sxs-lookup"><span data-stu-id="a9750-621">[Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) (includes HTTPS configuration and SNI support)</span></span>
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
