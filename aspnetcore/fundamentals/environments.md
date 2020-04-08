---
title: Verwenden von mehreren Umgebungen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie in ASP.NET Core-Apps das App-Verhalten umgebungsübergreifend steuern können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/17/2019
uid: fundamentals/environments
ms.openlocfilehash: b0218b2c77c283c0849dca9491046534b88c5a77
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2020
ms.locfileid: "78645355"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="478f4-103">Verwenden von mehreren Umgebungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="478f4-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="478f4-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="478f4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="478f4-105">ASP.NET Core konfiguriert das App-Verhalten basierend auf der Laufzeitumgebung mit einer Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="478f4-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="478f4-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="478f4-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="478f4-107">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="478f4-107">Environments</span></span>

<span data-ttu-id="478f4-108">ASP.NET Core liest die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` beim Start der App und speichert diesen Wert unter [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="478f4-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="478f4-109">`ASPNETCORE_ENVIRONMENT` kann auf einen beliebigen Wert festgelegt werden, das Framework stellt allerdings nur drei Werte bereit:</span><span class="sxs-lookup"><span data-stu-id="478f4-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="478f4-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="478f4-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="478f4-111">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="478f4-111">The preceding code:</span></span>

* <span data-ttu-id="478f4-112">Ruft [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) auf, wenn `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="478f4-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="478f4-113">Ruft [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) auf, wenn der Wert von `ASPNETCORE_ENVIRONMENT` auf einen der folgenden Werte festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="478f4-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="478f4-114">Das [Umgebungstaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) verwendet den Wert von `IHostingEnvironment.EnvironmentName` zum Einschließen oder Ausschließen von Markup im Element:</span><span class="sxs-lookup"><span data-stu-id="478f4-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="478f4-115">Unter Windows und macOS wird bei Umgebungsvariablen und Werten die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="478f4-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="478f4-116">Bei Linux-Umgebungsvariablen und -Werten **wird die Groß-/Kleinschreibung standardmäßig beachtet**.</span><span class="sxs-lookup"><span data-stu-id="478f4-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="478f4-117">Entwicklung</span><span class="sxs-lookup"><span data-stu-id="478f4-117">Development</span></span>

<span data-ttu-id="478f4-118">In der Entwicklungsumgebung können Features aktiviert werden, die in der Produktion nicht verfügbar gemacht werden sollten.</span><span class="sxs-lookup"><span data-stu-id="478f4-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="478f4-119">Mit den ASP.NET Core-Vorlagen wird beispielsweise die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#developer-exception-page) in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="478f4-120">Die Umgebung für die Entwicklung lokaler Computer kann in der Datei *Properties\launchSettings.json* des Projekts festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="478f4-121">Mit in der Datei *launchSettings.json* festgelegten Umgebungsvariablen werden in der Systemumgebung festgelegte Werte überschrieben.</span><span class="sxs-lookup"><span data-stu-id="478f4-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="478f4-122">Die folgende JSON zeigt drei Profile aus der Datei *launchSettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="478f4-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="478f4-123">Die Eigenschaft `applicationUrl` in *launchSettings.json* kann eine Liste von Server-URLs angeben.</span><span class="sxs-lookup"><span data-stu-id="478f4-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="478f4-124">Verwenden Sie ein Semikolon zwischen den URLs in der Liste:</span><span class="sxs-lookup"><span data-stu-id="478f4-124">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="478f4-125">Wenn die Anwendung mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird, wird das erste Profil mit `"commandName": "Project"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="478f4-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="478f4-126">Der Wert von `commandName` gibt den zu startenden Webserver an.</span><span class="sxs-lookup"><span data-stu-id="478f4-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="478f4-127">`commandName` kann einer der folgenden sein:</span><span class="sxs-lookup"><span data-stu-id="478f4-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="478f4-128">`Project` (über das Kestrel gestartet wird)</span><span class="sxs-lookup"><span data-stu-id="478f4-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="478f4-129">Beim Start einer App mit [dotnet run](/dotnet/core/tools/dotnet-run) tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="478f4-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="478f4-130">Die Datei *launchSettings.json* wird, sofern verfügbar, gelesen.</span><span class="sxs-lookup"><span data-stu-id="478f4-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="478f4-131">Durch `environmentVariables`-Einstellungen in der Datei *launchSettings.json* werden Umgebungsvariablen überschrieben.</span><span class="sxs-lookup"><span data-stu-id="478f4-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="478f4-132">Die Hostingumgebung wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="478f4-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="478f4-133">Die folgende Ausgabe zeigt eine App, die mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wurde:</span><span class="sxs-lookup"><span data-stu-id="478f4-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="478f4-134">Auf der Registerkarte **Debuggen** der Visual Studio-Projekteigenschaften wird eine grafische Benutzeroberfläche für die Bearbeitung der Datei *launchSettings.json* bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="478f4-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Projekteigenschaften zum Festlegen von Umgebungsvariablen](environments/_static/project-properties-debug.png)

<span data-ttu-id="478f4-136">An Projektprofilen vorgenommene Änderungen werden möglicherweise erst nach einem Neustart des Webservers wirksam.</span><span class="sxs-lookup"><span data-stu-id="478f4-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="478f4-137">Kestrel muss neu gestartet werden, bevor es an der Umgebung vorgenommene Änderungen erkennen kann.</span><span class="sxs-lookup"><span data-stu-id="478f4-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="478f4-138">In der Datei *launchSettings.json* sollten keine geheimen Schlüssel gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="478f4-139">Mit dem [Secret Manager-Tool](xref:security/app-secrets) können geheime Schlüssel für die lokale Umgebung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="478f4-140">Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) verwenden, können Umgebungsvariablen in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="478f4-141">Im folgenden Beispiel wird die Umgebung auf `Development` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="478f4-141">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="478f4-142">Die Datei *.vscode/launch.json* im Projekt wird nicht gelesen, wenn die App mit `dotnet run` genauso wie *Properties/launchSettings.json* gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="478f4-143">Wenn Sie eine App in der Entwicklung starten, die keine *launchSettings.json*-Datei enthält, legen Sie die Umgebung mit einer Umgebungsvariablen oder einem Befehlszeilenargument auf den `dotnet run`-Befehl fest.</span><span class="sxs-lookup"><span data-stu-id="478f4-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="478f4-144">Produktion</span><span class="sxs-lookup"><span data-stu-id="478f4-144">Production</span></span>

<span data-ttu-id="478f4-145">Die Produktionsumgebung sollte so konfiguriert werden, dass Sicherheit, Leistung und Stabilität der App maximiert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="478f4-146">Allgemeine Einstellungen, die sich von der Entwicklung unterscheiden, sind zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="478f4-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="478f4-147">Zwischenspeicherung.</span><span class="sxs-lookup"><span data-stu-id="478f4-147">Caching.</span></span>
* <span data-ttu-id="478f4-148">Clientseitige Ressourcen werden gebündelt, verkleinert und potenziell von einem CDN bedient.</span><span class="sxs-lookup"><span data-stu-id="478f4-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="478f4-149">Seiten zur Fehlerdiagnose sind deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="478f4-150">Angezeigte Fehlerseiten sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="478f4-151">Die Produktionsprotokollierung und -überwachung ist aktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="478f4-152">Beispiel: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="478f4-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="478f4-153">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="478f4-153">Set the environment</span></span>

<span data-ttu-id="478f4-154">Häufig ist es sinnvoll, mit einer Umgebungsvariablen oder Plattformeinstellung eine bestimmte Umgebung für Tests festzulegen.</span><span class="sxs-lookup"><span data-stu-id="478f4-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="478f4-155">Wenn die Umgebung nicht festgelegt ist, wird `Production` als Standardwert verwendet, wodurch die meisten Debugfeatures deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="478f4-156">Welche Methode zum Festlegen der Umgebung verwendet wird, hängt vom Betriebssystem ab.</span><span class="sxs-lookup"><span data-stu-id="478f4-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="478f4-157">Wenn der Host erstellt wird, bestimmt die letzte von der App gelesene Umgebungseinstellung die Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="478f4-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="478f4-158">Die Umgebung der App kann nicht geändert werden, während die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="478f4-159">Umgebungsvariable oder Plattformeinstellung</span><span class="sxs-lookup"><span data-stu-id="478f4-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="478f4-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="478f4-160">Azure App Service</span></span>

<span data-ttu-id="478f4-161">Führen Sie die folgenden Schritte durch, um die Umgebung in [Azure App Service](https://azure.microsoft.com/services/app-service/) festzulegen:</span><span class="sxs-lookup"><span data-stu-id="478f4-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="478f4-162">Wählen Sie die App auf dem Blatt **App Services** aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="478f4-163">Wählen Sie in der Gruppe **Einstellungen** das Blatt **Konfiguration** aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-163">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="478f4-164">Wählen Sie auf der Registerkarte **Anwendungseinstellungen** **Neue Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-164">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="478f4-165">Geben Sie im Fenster **Anwendungseinstellung hinzufügen/bearbeiten** `ASPNETCORE_ENVIRONMENT` als **Namen** an.</span><span class="sxs-lookup"><span data-stu-id="478f4-165">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="478f4-166">Geben Sie als **Wert** die Umgebung ein (beispielsweise `Staging`).</span><span class="sxs-lookup"><span data-stu-id="478f4-166">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="478f4-167">Aktivieren Sie das Kontrollkästchen **Bereitstellungssloteinstellung**, wenn Sie möchten, dass die Umgebungseinstellung im aktuellen Slot bleibt, wenn Bereitstellungsslots getauscht werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-167">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="478f4-168">Weitere Informationen finden Sie unter [Einrichten von Stagingumgebungen in Azure App Service](/azure/app-service/web-sites-staged-publishing) in der Azure-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="478f4-168">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="478f4-169">Wählen Sie **OK** aus, um das Fenster **Anwendungseinstellung hinzufügen/bearbeiten** zu schließen.</span><span class="sxs-lookup"><span data-stu-id="478f4-169">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="478f4-170">Klicken Sie oben auf dem Blatt **Konfiguration** auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="478f4-170">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="478f4-171">Azure App Service startet die App automatisch neu, nachdem eine App-Einstellung (Umgebungsvariable) im Azure-Portal hinzugefügt, geändert oder gelöscht wurde.</span><span class="sxs-lookup"><span data-stu-id="478f4-171">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="478f4-172">Windows</span><span class="sxs-lookup"><span data-stu-id="478f4-172">Windows</span></span>

<span data-ttu-id="478f4-173">Zum Festlegen der `ASPNETCORE_ENVIRONMENT` für die aktuelle Sitzung werden folgende Befehle verwendet, wenn die App mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird:</span><span class="sxs-lookup"><span data-stu-id="478f4-173">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="478f4-174">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="478f4-174">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="478f4-175">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="478f4-175">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="478f4-176">Diese Befehle sind nur für das aktuelle Fenster wirksam.</span><span class="sxs-lookup"><span data-stu-id="478f4-176">These commands only take effect for the current window.</span></span> <span data-ttu-id="478f4-177">Wenn das Fenster geschlossen wird, wird die Einstellung `ASPNETCORE_ENVIRONMENT` auf die Standardeinstellung oder den Computerwert zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="478f4-177">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="478f4-178">Wenn Sie den Wert in Windows global festlegen möchten, nutzen Sie eine der folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="478f4-178">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="478f4-179">Öffnen Sie **Systemsteuerung** > **System** > **Erweiterte Systemeinstellungen**, und fügen Sie den Wert `ASPNETCORE_ENVIRONMENT` hinzu, oder bearbeiten Sie ihn:</span><span class="sxs-lookup"><span data-stu-id="478f4-179">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Erweiterte Systemeigenschaften](environments/_static/systemsetting_environment.png)

  ![ASP.NET Core-Umgebungsvariable](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="478f4-182">Führen Sie die Eingabeaufforderung als Administrator aus, und verwenden Sie den Befehl `setx`. Alternativ können Sie auch die PowerShell-Eingabeaufforderung als Administrator ausführen und `[Environment]::SetEnvironmentVariable` nutzen:</span><span class="sxs-lookup"><span data-stu-id="478f4-182">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="478f4-183">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="478f4-183">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="478f4-184">Mit dem Parameter `/M` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-184">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="478f4-185">Ohne den Parameter `/M` wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="478f4-185">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="478f4-186">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="478f4-186">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="478f4-187">Mit dem Optionswert `Machine` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-187">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="478f4-188">Verwenden Sie stattdessen den Optionswert `User`, wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="478f4-188">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="478f4-189">Sobald die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` global festgelegt wird, gilt sie in jedem geöffneten Befehlsfenster für `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="478f4-189">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="478f4-190">**web.config**</span><span class="sxs-lookup"><span data-stu-id="478f4-190">**web.config**</span></span>

<span data-ttu-id="478f4-191">Eine Anleitung zum Festlegen der Umgebungsvariable `ASPNETCORE_ENVIRONMENT` mit *web.config* finden Sie im Abschnitt *Festlegen von Umgebungsvariablen* der im Artikel zu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="478f4-191">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="478f4-192">**Projektdatei oder Veröffentlichungsprofil**</span><span class="sxs-lookup"><span data-stu-id="478f4-192">**Project file or publish profile**</span></span>

<span data-ttu-id="478f4-193">**Für Windows IIS-Bereitstellungen**: Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder die Projektdatei ein.</span><span class="sxs-lookup"><span data-stu-id="478f4-193">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="478f4-194">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="478f4-194">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="478f4-195">**Pro IIS-Anwendungspool**</span><span class="sxs-lookup"><span data-stu-id="478f4-195">**Per IIS Application Pool**</span></span>

<span data-ttu-id="478f4-196">Wenn Sie für eine App, die in einem isolierten Anwendungspool ausgeführt wird, die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen möchten (unterstützt in IIS 10.0 oder höher), lesen Sie den Abschnitt *AppCmd.exe command (Befehl „AppCmd.exe“)* im Artikel zu [Umgebungsvariablen&lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="478f4-196">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="478f4-197">Wurde die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` für einen Anwendungspool festgelegt, überschreibt der Wert die Einstellung auf Systemebene.</span><span class="sxs-lookup"><span data-stu-id="478f4-197">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="478f4-198">Wenn Sie eine APP in den IIS hosten und die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` hinzufügen oder ändern, sorgen Sie auf eine der folgenden Arten und Weisen dafür, dass der neue Wert in den Apps hinterlegt ist:</span><span class="sxs-lookup"><span data-stu-id="478f4-198">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="478f4-199">Führen Sie in einer Eingabeaufforderung `net stop was /y` gefolgt von `net start w3svc` aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-199">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="478f4-200">Starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="478f4-200">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="478f4-201">macOS</span><span class="sxs-lookup"><span data-stu-id="478f4-201">macOS</span></span>

<span data-ttu-id="478f4-202">Das Festlegen der aktuellen Umgebung für macOS kann inline während der Ausführung der App erfolgen:</span><span class="sxs-lookup"><span data-stu-id="478f4-202">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="478f4-203">Legen Sie die Umgebung alternativ mit `export` vor der Ausführung der App fest:</span><span class="sxs-lookup"><span data-stu-id="478f4-203">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="478f4-204">Umgebungsvariablen auf Computerebene werden in der *BASHRC*- oder *BASH_PROFILE*-Datei festgelegt.</span><span class="sxs-lookup"><span data-stu-id="478f4-204">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="478f4-205">Bearbeiten Sie die Datei mit einem beliebigen Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="478f4-205">Edit the file using any text editor.</span></span> <span data-ttu-id="478f4-206">Fügen Sie die folgende Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="478f4-206">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="478f4-207">Linux</span><span class="sxs-lookup"><span data-stu-id="478f4-207">Linux</span></span>

<span data-ttu-id="478f4-208">Verwenden Sie bei Linux-Distributionen für sitzungsbasierte Variableneinstellungen den Befehl `export` in der Befehlszeile und die *BASH-PROFILE*-Datei für Umgebungseinstellungen auf Computerebene.</span><span class="sxs-lookup"><span data-stu-id="478f4-208">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="478f4-209">Festlegen der Umgebung im Code</span><span class="sxs-lookup"><span data-stu-id="478f4-209">Set the environment in code</span></span>

<span data-ttu-id="478f4-210">Rufen Sie beim Erstellen des Hosts <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> auf.</span><span class="sxs-lookup"><span data-stu-id="478f4-210">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="478f4-211">Siehe <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="478f4-211">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="478f4-212">Konfiguration nach Umgebung</span><span class="sxs-lookup"><span data-stu-id="478f4-212">Configuration by environment</span></span>

<span data-ttu-id="478f4-213">Zum Laden von „Konfiguration nach Umgebung“ empfehlen wir:</span><span class="sxs-lookup"><span data-stu-id="478f4-213">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="478f4-214">*appsettings*-Dateien (*appsettings.{Umgebung}.json*).</span><span class="sxs-lookup"><span data-stu-id="478f4-214">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="478f4-215">Siehe <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="478f4-215">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="478f4-216">Umgebungsvariablen (in jedem System festgelegt, in dem die App gehostet wird).</span><span class="sxs-lookup"><span data-stu-id="478f4-216">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="478f4-217">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#environmentname> und <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="478f4-217">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="478f4-218">Secret Manager (nur in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="478f4-218">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="478f4-219">Siehe <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="478f4-219">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="478f4-220">Umgebungsbasierte Startklasse und Methoden</span><span class="sxs-lookup"><span data-stu-id="478f4-220">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="478f4-221">Einfügen von IWebHostEnvironment in Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="478f4-221">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="478f4-222">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup.Configure` ein.</span><span class="sxs-lookup"><span data-stu-id="478f4-222">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="478f4-223">Diese Vorgehensweise ist nützlich, wenn die App `Startup.Configure` nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung anpassen muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-223">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="478f4-224">Einfügen von IWebHostEnvironment in die Startklasse</span><span class="sxs-lookup"><span data-stu-id="478f4-224">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="478f4-225">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in den `Startup`-Konstruktor ein.</span><span class="sxs-lookup"><span data-stu-id="478f4-225">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="478f4-226">Diese Vorgehensweise ist nützlich, wenn die App `Startup` nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-226">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="478f4-227">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="478f4-227">In the following example:</span></span>

* <span data-ttu-id="478f4-228">Die Umgebung wird im Feld `_env` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="478f4-228">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="478f4-229">`_env` wird in `ConfigureServices` und `Configure` verwendet, um die Startkonfiguration basierend auf der Umgebung der App anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="478f4-229">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IWebHostEnvironment _env;

    public Startup(IWebHostEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```
### <a name="startup-class-conventions"></a><span data-ttu-id="478f4-230">Konventionen der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="478f4-230">Startup class conventions</span></span>

<span data-ttu-id="478f4-231">Nach dem Start einer ASP.NET Core-App lädt die [Startklasse](xref:fundamentals/startup) die App.</span><span class="sxs-lookup"><span data-stu-id="478f4-231">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="478f4-232">Die App kann je nach Umgebung unterschiedliche `Startup`-Klassen definieren (z. B. `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="478f4-232">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="478f4-233">Die entsprechende `Startup`-Klasse wird zur Laufzeit ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="478f4-233">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="478f4-234">Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert.</span><span class="sxs-lookup"><span data-stu-id="478f4-234">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="478f4-235">Ist keine übereinstimmende `Startup{EnvironmentName}`-Klasse vorhanden, wird die Klasse `Startup` verwendet.</span><span class="sxs-lookup"><span data-stu-id="478f4-235">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="478f4-236">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-236">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="478f4-237">Wenn Sie umgebungsbasierte `Startup`-Klassen implementieren möchten, erstellen Sie für jedes verwendete Element eine `Startup{EnvironmentName}`-Klasse und eine Fallback-Klasse des Typs `Startup`:</span><span class="sxs-lookup"><span data-stu-id="478f4-237">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="478f4-238">Verwenden Sie die Überladung [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), die einen Assemblynamen akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="478f4-238">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="478f4-239">Konventionen der Methode „Start“</span><span class="sxs-lookup"><span data-stu-id="478f4-239">Startup method conventions</span></span>

<span data-ttu-id="478f4-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) unterstützen umgebungsspezifische Versionen der Form `Configure<EnvironmentName>` und `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="478f4-240">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="478f4-241">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-241">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="478f4-242">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="478f4-242">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="478f4-243">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="478f4-243">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="478f4-244">ASP.NET Core konfiguriert das App-Verhalten basierend auf der Laufzeitumgebung mit einer Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="478f4-244">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="478f4-245">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="478f4-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="478f4-246">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="478f4-246">Environments</span></span>

<span data-ttu-id="478f4-247">ASP.NET Core liest die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` beim Start der App und speichert diesen Wert unter [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="478f4-247">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="478f4-248">`ASPNETCORE_ENVIRONMENT` kann auf einen beliebigen Wert festgelegt werden, das Framework stellt allerdings nur drei Werte bereit:</span><span class="sxs-lookup"><span data-stu-id="478f4-248">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="478f4-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (Standardwert)</span><span class="sxs-lookup"><span data-stu-id="478f4-249"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="478f4-250">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="478f4-250">The preceding code:</span></span>

* <span data-ttu-id="478f4-251">Ruft [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) auf, wenn `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="478f4-251">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="478f4-252">Ruft [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) auf, wenn der Wert von `ASPNETCORE_ENVIRONMENT` auf einen der folgenden Werte festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="478f4-252">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="478f4-253">Das [Umgebungstaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) verwendet den Wert von `IHostingEnvironment.EnvironmentName` zum Einschließen oder Ausschließen von Markup im Element:</span><span class="sxs-lookup"><span data-stu-id="478f4-253">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="478f4-254">Unter Windows und macOS wird bei Umgebungsvariablen und Werten die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="478f4-254">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="478f4-255">Bei Linux-Umgebungsvariablen und -Werten **wird die Groß-/Kleinschreibung standardmäßig beachtet**.</span><span class="sxs-lookup"><span data-stu-id="478f4-255">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="478f4-256">Entwicklung</span><span class="sxs-lookup"><span data-stu-id="478f4-256">Development</span></span>

<span data-ttu-id="478f4-257">In der Entwicklungsumgebung können Features aktiviert werden, die in der Produktion nicht verfügbar gemacht werden sollten.</span><span class="sxs-lookup"><span data-stu-id="478f4-257">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="478f4-258">Mit den ASP.NET Core-Vorlagen wird beispielsweise die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#developer-exception-page) in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-258">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="478f4-259">Die Umgebung für die Entwicklung lokaler Computer kann in der Datei *Properties\launchSettings.json* des Projekts festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-259">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="478f4-260">Mit in der Datei *launchSettings.json* festgelegten Umgebungsvariablen werden in der Systemumgebung festgelegte Werte überschrieben.</span><span class="sxs-lookup"><span data-stu-id="478f4-260">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="478f4-261">Die folgende JSON zeigt drei Profile aus der Datei *launchSettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="478f4-261">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:54339/",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      }
    },
    "EnvironmentsSample": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:54340/"
    },
    "Kestrel Staging": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_My_Environment": "1",
        "ASPNETCORE_DETAILEDERRORS": "1",
        "ASPNETCORE_ENVIRONMENT": "Staging"
      },
      "applicationUrl": "http://localhost:51997/"
    }
  }
}
```

> [!NOTE]
> <span data-ttu-id="478f4-262">Die Eigenschaft `applicationUrl` in *launchSettings.json* kann eine Liste von Server-URLs angeben.</span><span class="sxs-lookup"><span data-stu-id="478f4-262">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="478f4-263">Verwenden Sie ein Semikolon zwischen den URLs in der Liste:</span><span class="sxs-lookup"><span data-stu-id="478f4-263">Use a semicolon between the URLs in the list:</span></span>
>
> ```json
> "EnvironmentsSample": {
>    "commandName": "Project",
>    "launchBrowser": true,
>    "applicationUrl": "https://localhost:5001;http://localhost:5000",
>    "environmentVariables": {
>      "ASPNETCORE_ENVIRONMENT": "Development"
>    }
> }
> ```

<span data-ttu-id="478f4-264">Wenn die Anwendung mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird, wird das erste Profil mit `"commandName": "Project"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="478f4-264">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="478f4-265">Der Wert von `commandName` gibt den zu startenden Webserver an.</span><span class="sxs-lookup"><span data-stu-id="478f4-265">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="478f4-266">`commandName` kann einer der folgenden sein:</span><span class="sxs-lookup"><span data-stu-id="478f4-266">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="478f4-267">`Project` (über das Kestrel gestartet wird)</span><span class="sxs-lookup"><span data-stu-id="478f4-267">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="478f4-268">Beim Start einer App mit [dotnet run](/dotnet/core/tools/dotnet-run) tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="478f4-268">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="478f4-269">Die Datei *launchSettings.json* wird, sofern verfügbar, gelesen.</span><span class="sxs-lookup"><span data-stu-id="478f4-269">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="478f4-270">Durch `environmentVariables`-Einstellungen in der Datei *launchSettings.json* werden Umgebungsvariablen überschrieben.</span><span class="sxs-lookup"><span data-stu-id="478f4-270">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="478f4-271">Die Hostingumgebung wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="478f4-271">The hosting environment is displayed.</span></span>

<span data-ttu-id="478f4-272">Die folgende Ausgabe zeigt eine App, die mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wurde:</span><span class="sxs-lookup"><span data-stu-id="478f4-272">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="478f4-273">Auf der Registerkarte **Debuggen** der Visual Studio-Projekteigenschaften wird eine grafische Benutzeroberfläche für die Bearbeitung der Datei *launchSettings.json* bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="478f4-273">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Projekteigenschaften zum Festlegen von Umgebungsvariablen](environments/_static/project-properties-debug.png)

<span data-ttu-id="478f4-275">An Projektprofilen vorgenommene Änderungen werden möglicherweise erst nach einem Neustart des Webservers wirksam.</span><span class="sxs-lookup"><span data-stu-id="478f4-275">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="478f4-276">Kestrel muss neu gestartet werden, bevor es an der Umgebung vorgenommene Änderungen erkennen kann.</span><span class="sxs-lookup"><span data-stu-id="478f4-276">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="478f4-277">In der Datei *launchSettings.json* sollten keine geheimen Schlüssel gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-277">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="478f4-278">Mit dem [Secret Manager-Tool](xref:security/app-secrets) können geheime Schlüssel für die lokale Umgebung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-278">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="478f4-279">Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) verwenden, können Umgebungsvariablen in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-279">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="478f4-280">Im folgenden Beispiel wird die Umgebung auf `Development` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="478f4-280">The following example sets the environment to `Development`:</span></span>

```json
{
   "version": "0.2.0",
   "configurations": [
        {
            "name": ".NET Core Launch (web)",

            ... additional VS Code configuration settings ...

            "env": {
                "ASPNETCORE_ENVIRONMENT": "Development"
            }
        }
    ]
}
```

<span data-ttu-id="478f4-281">Die Datei *.vscode/launch.json* im Projekt wird nicht gelesen, wenn die App mit `dotnet run` genauso wie *Properties/launchSettings.json* gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-281">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="478f4-282">Wenn Sie eine App in der Entwicklung starten, die keine *launchSettings.json*-Datei enthält, legen Sie die Umgebung mit einer Umgebungsvariablen oder einem Befehlszeilenargument auf den `dotnet run`-Befehl fest.</span><span class="sxs-lookup"><span data-stu-id="478f4-282">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="478f4-283">Produktion</span><span class="sxs-lookup"><span data-stu-id="478f4-283">Production</span></span>

<span data-ttu-id="478f4-284">Die Produktionsumgebung sollte so konfiguriert werden, dass Sicherheit, Leistung und Stabilität der App maximiert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-284">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="478f4-285">Allgemeine Einstellungen, die sich von der Entwicklung unterscheiden, sind zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="478f4-285">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="478f4-286">Zwischenspeicherung.</span><span class="sxs-lookup"><span data-stu-id="478f4-286">Caching.</span></span>
* <span data-ttu-id="478f4-287">Clientseitige Ressourcen werden gebündelt, verkleinert und potenziell von einem CDN bedient.</span><span class="sxs-lookup"><span data-stu-id="478f4-287">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="478f4-288">Seiten zur Fehlerdiagnose sind deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-288">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="478f4-289">Angezeigte Fehlerseiten sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-289">Friendly error pages enabled.</span></span>
* <span data-ttu-id="478f4-290">Die Produktionsprotokollierung und -überwachung ist aktiviert.</span><span class="sxs-lookup"><span data-stu-id="478f4-290">Production logging and monitoring enabled.</span></span> <span data-ttu-id="478f4-291">Beispiel: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="478f4-291">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="478f4-292">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="478f4-292">Set the environment</span></span>

<span data-ttu-id="478f4-293">Häufig ist es sinnvoll, mit einer Umgebungsvariablen oder Plattformeinstellung eine bestimmte Umgebung für Tests festzulegen.</span><span class="sxs-lookup"><span data-stu-id="478f4-293">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="478f4-294">Wenn die Umgebung nicht festgelegt ist, wird `Production` als Standardwert verwendet, wodurch die meisten Debugfeatures deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-294">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="478f4-295">Welche Methode zum Festlegen der Umgebung verwendet wird, hängt vom Betriebssystem ab.</span><span class="sxs-lookup"><span data-stu-id="478f4-295">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="478f4-296">Wenn der Host erstellt wird, bestimmt die letzte von der App gelesene Umgebungseinstellung die Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="478f4-296">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="478f4-297">Die Umgebung der App kann nicht geändert werden, während die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-297">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="478f4-298">Umgebungsvariable oder Plattformeinstellung</span><span class="sxs-lookup"><span data-stu-id="478f4-298">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="478f4-299">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="478f4-299">Azure App Service</span></span>

<span data-ttu-id="478f4-300">Führen Sie die folgenden Schritte durch, um die Umgebung in [Azure App Service](https://azure.microsoft.com/services/app-service/) festzulegen:</span><span class="sxs-lookup"><span data-stu-id="478f4-300">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="478f4-301">Wählen Sie die App auf dem Blatt **App Services** aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-301">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="478f4-302">Wählen Sie in der Gruppe **Einstellungen** das Blatt **Konfiguration** aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-302">In the **Settings** group, select the **Configuration** blade.</span></span>
1. <span data-ttu-id="478f4-303">Wählen Sie auf der Registerkarte **Anwendungseinstellungen** **Neue Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-303">In the **Application settings** tab, select **New application setting**.</span></span>
1. <span data-ttu-id="478f4-304">Geben Sie im Fenster **Anwendungseinstellung hinzufügen/bearbeiten** `ASPNETCORE_ENVIRONMENT` als **Namen** an.</span><span class="sxs-lookup"><span data-stu-id="478f4-304">In the **Add/Edit application setting** window, provide `ASPNETCORE_ENVIRONMENT` for the **Name**.</span></span> <span data-ttu-id="478f4-305">Geben Sie als **Wert** die Umgebung ein (beispielsweise `Staging`).</span><span class="sxs-lookup"><span data-stu-id="478f4-305">For **Value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="478f4-306">Aktivieren Sie das Kontrollkästchen **Bereitstellungssloteinstellung**, wenn Sie möchten, dass die Umgebungseinstellung im aktuellen Slot bleibt, wenn Bereitstellungsslots getauscht werden.</span><span class="sxs-lookup"><span data-stu-id="478f4-306">Select the **Deployment slot setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="478f4-307">Weitere Informationen finden Sie unter [Einrichten von Stagingumgebungen in Azure App Service](/azure/app-service/web-sites-staged-publishing) in der Azure-Dokumentation.</span><span class="sxs-lookup"><span data-stu-id="478f4-307">For more information, see [Set up staging environments in Azure App Service](/azure/app-service/web-sites-staged-publishing) in the Azure documentation.</span></span>
1. <span data-ttu-id="478f4-308">Wählen Sie **OK** aus, um das Fenster **Anwendungseinstellung hinzufügen/bearbeiten** zu schließen.</span><span class="sxs-lookup"><span data-stu-id="478f4-308">Select **OK** to close the **Add/Edit application setting** window.</span></span>
1. <span data-ttu-id="478f4-309">Klicken Sie oben auf dem Blatt **Konfiguration** auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="478f4-309">Select **Save** at the top of the **Configuration** blade.</span></span>

<span data-ttu-id="478f4-310">Azure App Service startet die App automatisch neu, nachdem eine App-Einstellung (Umgebungsvariable) im Azure-Portal hinzugefügt, geändert oder gelöscht wurde.</span><span class="sxs-lookup"><span data-stu-id="478f4-310">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="478f4-311">Windows</span><span class="sxs-lookup"><span data-stu-id="478f4-311">Windows</span></span>

<span data-ttu-id="478f4-312">Zum Festlegen der `ASPNETCORE_ENVIRONMENT` für die aktuelle Sitzung werden folgende Befehle verwendet, wenn die App mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird:</span><span class="sxs-lookup"><span data-stu-id="478f4-312">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="478f4-313">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="478f4-313">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="478f4-314">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="478f4-314">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="478f4-315">Diese Befehle sind nur für das aktuelle Fenster wirksam.</span><span class="sxs-lookup"><span data-stu-id="478f4-315">These commands only take effect for the current window.</span></span> <span data-ttu-id="478f4-316">Wenn das Fenster geschlossen wird, wird die Einstellung `ASPNETCORE_ENVIRONMENT` auf die Standardeinstellung oder den Computerwert zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="478f4-316">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="478f4-317">Wenn Sie den Wert in Windows global festlegen möchten, nutzen Sie eine der folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="478f4-317">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="478f4-318">Öffnen Sie **Systemsteuerung** > **System** > **Erweiterte Systemeinstellungen**, und fügen Sie den Wert `ASPNETCORE_ENVIRONMENT` hinzu, oder bearbeiten Sie ihn:</span><span class="sxs-lookup"><span data-stu-id="478f4-318">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Erweiterte Systemeigenschaften](environments/_static/systemsetting_environment.png)

  ![ASP.NET Core-Umgebungsvariable](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="478f4-321">Führen Sie die Eingabeaufforderung als Administrator aus, und verwenden Sie den Befehl `setx`. Alternativ können Sie auch die PowerShell-Eingabeaufforderung als Administrator ausführen und `[Environment]::SetEnvironmentVariable` nutzen:</span><span class="sxs-lookup"><span data-stu-id="478f4-321">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="478f4-322">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="478f4-322">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="478f4-323">Mit dem Parameter `/M` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-323">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="478f4-324">Ohne den Parameter `/M` wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="478f4-324">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="478f4-325">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="478f4-325">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="478f4-326">Mit dem Optionswert `Machine` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="478f4-326">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="478f4-327">Verwenden Sie stattdessen den Optionswert `User`, wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="478f4-327">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="478f4-328">Sobald die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` global festgelegt wird, gilt sie in jedem geöffneten Befehlsfenster für `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="478f4-328">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="478f4-329">**web.config**</span><span class="sxs-lookup"><span data-stu-id="478f4-329">**web.config**</span></span>

<span data-ttu-id="478f4-330">Eine Anleitung zum Festlegen der Umgebungsvariable `ASPNETCORE_ENVIRONMENT` mit *web.config* finden Sie im Abschnitt *Festlegen von Umgebungsvariablen* der im Artikel zu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="478f4-330">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="478f4-331">**Projektdatei oder Veröffentlichungsprofil**</span><span class="sxs-lookup"><span data-stu-id="478f4-331">**Project file or publish profile**</span></span>

<span data-ttu-id="478f4-332">**Für Windows IIS-Bereitstellungen**: Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder die Projektdatei ein.</span><span class="sxs-lookup"><span data-stu-id="478f4-332">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="478f4-333">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="478f4-333">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="478f4-334">**Pro IIS-Anwendungspool**</span><span class="sxs-lookup"><span data-stu-id="478f4-334">**Per IIS Application Pool**</span></span>

<span data-ttu-id="478f4-335">Wenn Sie für eine App, die in einem isolierten Anwendungspool ausgeführt wird, die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen möchten (unterstützt in IIS 10.0 oder höher), lesen Sie den Abschnitt *AppCmd.exe command (Befehl „AppCmd.exe“)* im Artikel zu [Umgebungsvariablen&lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="478f4-335">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="478f4-336">Wurde die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` für einen Anwendungspool festgelegt, überschreibt der Wert die Einstellung auf Systemebene.</span><span class="sxs-lookup"><span data-stu-id="478f4-336">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="478f4-337">Wenn Sie eine APP in den IIS hosten und die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` hinzufügen oder ändern, sorgen Sie auf eine der folgenden Arten und Weisen dafür, dass der neue Wert in den Apps hinterlegt ist:</span><span class="sxs-lookup"><span data-stu-id="478f4-337">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="478f4-338">Führen Sie in einer Eingabeaufforderung `net stop was /y` gefolgt von `net start w3svc` aus.</span><span class="sxs-lookup"><span data-stu-id="478f4-338">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="478f4-339">Starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="478f4-339">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="478f4-340">macOS</span><span class="sxs-lookup"><span data-stu-id="478f4-340">macOS</span></span>

<span data-ttu-id="478f4-341">Das Festlegen der aktuellen Umgebung für macOS kann inline während der Ausführung der App erfolgen:</span><span class="sxs-lookup"><span data-stu-id="478f4-341">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="478f4-342">Legen Sie die Umgebung alternativ mit `export` vor der Ausführung der App fest:</span><span class="sxs-lookup"><span data-stu-id="478f4-342">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="478f4-343">Umgebungsvariablen auf Computerebene werden in der *BASHRC*- oder *BASH_PROFILE*-Datei festgelegt.</span><span class="sxs-lookup"><span data-stu-id="478f4-343">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="478f4-344">Bearbeiten Sie die Datei mit einem beliebigen Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="478f4-344">Edit the file using any text editor.</span></span> <span data-ttu-id="478f4-345">Fügen Sie die folgende Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="478f4-345">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="478f4-346">Linux</span><span class="sxs-lookup"><span data-stu-id="478f4-346">Linux</span></span>

<span data-ttu-id="478f4-347">Verwenden Sie bei Linux-Distributionen für sitzungsbasierte Variableneinstellungen den Befehl `export` in der Befehlszeile und die *BASH-PROFILE*-Datei für Umgebungseinstellungen auf Computerebene.</span><span class="sxs-lookup"><span data-stu-id="478f4-347">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="478f4-348">Festlegen der Umgebung im Code</span><span class="sxs-lookup"><span data-stu-id="478f4-348">Set the environment in code</span></span>

<span data-ttu-id="478f4-349">Rufen Sie beim Erstellen des Hosts <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> auf.</span><span class="sxs-lookup"><span data-stu-id="478f4-349">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="478f4-350">Siehe <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="478f4-350">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="478f4-351">Konfiguration nach Umgebung</span><span class="sxs-lookup"><span data-stu-id="478f4-351">Configuration by environment</span></span>

<span data-ttu-id="478f4-352">Zum Laden von „Konfiguration nach Umgebung“ empfehlen wir:</span><span class="sxs-lookup"><span data-stu-id="478f4-352">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="478f4-353">*appsettings*-Dateien (*appsettings.{Umgebung}.json*).</span><span class="sxs-lookup"><span data-stu-id="478f4-353">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="478f4-354">Siehe <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="478f4-354">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="478f4-355">Umgebungsvariablen (in jedem System festgelegt, in dem die App gehostet wird).</span><span class="sxs-lookup"><span data-stu-id="478f4-355">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="478f4-356">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#environment> und <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="478f4-356">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="478f4-357">Secret Manager (nur in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="478f4-357">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="478f4-358">Siehe <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="478f4-358">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="478f4-359">Umgebungsbasierte Startklasse und Methoden</span><span class="sxs-lookup"><span data-stu-id="478f4-359">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="478f4-360">Einfügen von IHostingEnvironment in Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="478f4-360">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="478f4-361">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in `Startup.Configure` ein.</span><span class="sxs-lookup"><span data-stu-id="478f4-361">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="478f4-362">Diese Vorgehensweise ist nützlich, wenn die App `Startup.Configure` nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-362">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        // Development environment code
    }
    else
    {
        // Code for all other environments
    }
}
```

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="478f4-363">Einfügen von IHostingEnvironment in die Startklasse</span><span class="sxs-lookup"><span data-stu-id="478f4-363">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="478f4-364">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in den `Startup`-Konstruktor ein, und weisen Sie den Dienst einem Feld zu, das in der gesamten `Startup`-Klasse verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="478f4-364">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="478f4-365">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-365">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="478f4-366">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="478f4-366">In the following example:</span></span>

* <span data-ttu-id="478f4-367">Die Umgebung wird im Feld `_env` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="478f4-367">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="478f4-368">`_env` wird in `ConfigureServices` und `Configure` verwendet, um die Startkonfiguration basierend auf der Umgebung der App anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="478f4-368">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

```csharp
public class Startup
{
    private readonly IHostingEnvironment _env;

    public Startup(IHostingEnvironment env)
    {
        _env = env;
    }

    public void ConfigureServices(IServiceCollection services)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else if (_env.IsStaging())
        {
            // Staging environment code
        }
        else
        {
            // Code for all other environments
        }
    }

    public void Configure(IApplicationBuilder app)
    {
        if (_env.IsDevelopment())
        {
            // Development environment code
        }
        else
        {
            // Code for all other environments
        }
    }
}
```

### <a name="startup-class-conventions"></a><span data-ttu-id="478f4-369">Konventionen der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="478f4-369">Startup class conventions</span></span>

<span data-ttu-id="478f4-370">Nach dem Start einer ASP.NET Core-App lädt die [Startklasse](xref:fundamentals/startup) die App.</span><span class="sxs-lookup"><span data-stu-id="478f4-370">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="478f4-371">Die App kann je nach Umgebung unterschiedliche `Startup`-Klassen definieren (z. B. `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="478f4-371">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="478f4-372">Die entsprechende `Startup`-Klasse wird zur Laufzeit ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="478f4-372">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="478f4-373">Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert.</span><span class="sxs-lookup"><span data-stu-id="478f4-373">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="478f4-374">Ist keine übereinstimmende `Startup{EnvironmentName}`-Klasse vorhanden, wird die Klasse `Startup` verwendet.</span><span class="sxs-lookup"><span data-stu-id="478f4-374">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="478f4-375">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-375">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="478f4-376">Wenn Sie umgebungsbasierte `Startup`-Klassen implementieren möchten, erstellen Sie für jedes verwendete Element eine `Startup{EnvironmentName}`-Klasse und eine Fallback-Klasse des Typs `Startup`:</span><span class="sxs-lookup"><span data-stu-id="478f4-376">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
    }
}
```

<span data-ttu-id="478f4-377">Verwenden Sie die Überladung [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), die einen Assemblynamen akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="478f4-377">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName);
}
```

### <a name="startup-method-conventions"></a><span data-ttu-id="478f4-378">Konventionen der Methode „Start“</span><span class="sxs-lookup"><span data-stu-id="478f4-378">Startup method conventions</span></span>

<span data-ttu-id="478f4-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) unterstützen umgebungsspezifische Versionen der Form `Configure<EnvironmentName>` und `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="478f4-379">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="478f4-380">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="478f4-380">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="478f4-381">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="478f4-381">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end
