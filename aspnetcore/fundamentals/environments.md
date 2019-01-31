---
title: Verwenden von mehreren Umgebungen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie in ASP.NET Core-Apps das App-Verhalten umgebungsübergreifend steuern können.
ms.author: riande
ms.date: 01/22/2019
uid: fundamentals/environments
ms.openlocfilehash: 39e1b48481832a6d76de605b37410fe2e16dcd88
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836739"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="b13d8-103">Verwenden von mehreren Umgebungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b13d8-103">Use multiple environments in ASP.NET Core</span></span>

<span data-ttu-id="b13d8-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b13d8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b13d8-105">ASP.NET Core konfiguriert das App-Verhalten basierend auf der Laufzeitumgebung mit einer Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="b13d8-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="b13d8-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b13d8-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="b13d8-107">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="b13d8-107">Environments</span></span>

<span data-ttu-id="b13d8-108">ASP.NET Core liest die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` beim Start der App und speichert diesen Wert unter [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span><span class="sxs-lookup"><span data-stu-id="b13d8-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname).</span></span> <span data-ttu-id="b13d8-109">Sie können `ASPNETCORE_ENVIRONMENT` auf einen beliebigen Wert festlegen, das Framework unterstützt allerdings nur [drei Werte](/dotnet/api/microsoft.aspnetcore.hosting.environmentname): [Entwicklung](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) und [Produktion](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span><span class="sxs-lookup"><span data-stu-id="b13d8-109">You can set `ASPNETCORE_ENVIRONMENT` to any value, but [three values](/dotnet/api/microsoft.aspnetcore.hosting.environmentname) are supported by the framework: [Development](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging), and [Production](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production).</span></span> <span data-ttu-id="b13d8-110">Wenn `ASPNETCORE_ENVIRONMENT` nicht festgelegt ist, ist der Standardwert `Production`.</span><span class="sxs-lookup"><span data-stu-id="b13d8-110">If `ASPNETCORE_ENVIRONMENT` isn't set, it defaults to `Production`.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="b13d8-111">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="b13d8-111">The preceding code:</span></span>

* <span data-ttu-id="b13d8-112">Ruft [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) auf, wenn `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="b13d8-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="b13d8-113">Ruft [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) auf, wenn der Wert von `ASPNETCORE_ENVIRONMENT` auf einen der folgenden Werte festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="b13d8-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

    * `Staging`
    * `Production`
    * `Staging_2`

<span data-ttu-id="b13d8-114">Das [Umgebungstaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) verwendet den Wert von `IHostingEnvironment.EnvironmentName` zum Einschließen oder Ausschließen von Markup im Element:</span><span class="sxs-lookup"><span data-stu-id="b13d8-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="b13d8-115">Unter Windows und macOS wird bei Umgebungsvariablen und Werten die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="b13d8-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="b13d8-116">Bei Linux-Umgebungsvariablen und -Werten **wird die Groß-/Kleinschreibung standardmäßig beachtet**.</span><span class="sxs-lookup"><span data-stu-id="b13d8-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="b13d8-117">Entwicklung</span><span class="sxs-lookup"><span data-stu-id="b13d8-117">Development</span></span>

<span data-ttu-id="b13d8-118">In der Entwicklungsumgebung können Features aktiviert werden, die in der Produktion nicht verfügbar gemacht werden sollten.</span><span class="sxs-lookup"><span data-stu-id="b13d8-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="b13d8-119">Mit den ASP.NET Core-Vorlagen wird beispielsweise die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#the-developer-exception-page) in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b13d8-119">For example, the ASP.NET Core templates enable the [developer exception page](xref:fundamentals/error-handling#the-developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="b13d8-120">Die Umgebung für die Entwicklung lokaler Computer kann in der Datei *Properties\launchSettings.json* des Projekts festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b13d8-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="b13d8-121">Mit in der Datei *launchSettings.json* festgelegten Umgebungsvariablen werden in der Systemumgebung festgelegte Werte überschrieben.</span><span class="sxs-lookup"><span data-stu-id="b13d8-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="b13d8-122">Die folgende JSON zeigt drei Profile aus der Datei *launchSettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="b13d8-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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

::: moniker range=">= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="b13d8-123">Die Eigenschaft `applicationUrl` in *launchSettings.json* kann eine Liste von Server-URLs angeben.</span><span class="sxs-lookup"><span data-stu-id="b13d8-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="b13d8-124">Verwenden Sie ein Semikolon zwischen den URLs in der Liste:</span><span class="sxs-lookup"><span data-stu-id="b13d8-124">Use a semicolon between the URLs in the list:</span></span>
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

::: moniker-end

<span data-ttu-id="b13d8-125">Wenn die Anwendung mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird, wird das erste Profil mit `"commandName": "Project"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b13d8-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="b13d8-126">Der Wert von `commandName` gibt den zu startenden Webserver an.</span><span class="sxs-lookup"><span data-stu-id="b13d8-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="b13d8-127">`commandName` kann einer der folgenden sein:</span><span class="sxs-lookup"><span data-stu-id="b13d8-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="b13d8-128">`Project` (über das Kestrel gestartet wird)</span><span class="sxs-lookup"><span data-stu-id="b13d8-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="b13d8-129">Beim Start einer App mit [dotnet run](/dotnet/core/tools/dotnet-run) tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="b13d8-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="b13d8-130">Die Datei *launchSettings.json* wird, sofern verfügbar, gelesen.</span><span class="sxs-lookup"><span data-stu-id="b13d8-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="b13d8-131">Durch `environmentVariables`-Einstellungen in der Datei *launchSettings.json* werden Umgebungsvariablen überschrieben.</span><span class="sxs-lookup"><span data-stu-id="b13d8-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="b13d8-132">Die Hostingumgebung wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="b13d8-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="b13d8-133">Die folgende Ausgabe zeigt eine App, die mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wurde:</span><span class="sxs-lookup"><span data-stu-id="b13d8-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="b13d8-134">Auf der Registerkarte **Debuggen** der Visual Studio-Projekteigenschaften wird eine grafische Benutzeroberfläche für die Bearbeitung der Datei *launchSettings.json* bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="b13d8-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Projekteigenschaften zum Festlegen von Umgebungsvariablen](environments/_static/project-properties-debug.png)

<span data-ttu-id="b13d8-136">An Projektprofilen vorgenommene Änderungen werden möglicherweise erst nach einem Neustart des Webservers wirksam.</span><span class="sxs-lookup"><span data-stu-id="b13d8-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="b13d8-137">Kestrel muss neu gestartet werden, bevor es an der Umgebung vorgenommene Änderungen erkennen kann.</span><span class="sxs-lookup"><span data-stu-id="b13d8-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="b13d8-138">In der Datei *launchSettings.json* sollten keine geheimen Schlüssel gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b13d8-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="b13d8-139">Mit dem [Secret Manager-Tool](xref:security/app-secrets) können geheime Schlüssel für die lokale Umgebung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="b13d8-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="b13d8-140">Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) verwenden, können Umgebungsvariablen in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="b13d8-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="b13d8-141">Im folgenden Beispiel wird die Umgebung auf `Development` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="b13d8-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="b13d8-142">Die Datei *.vscode/launch.json* im Projekt wird nicht gelesen, wenn die App mit `dotnet run` genauso wie *Properties/launchSettings.json* gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="b13d8-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="b13d8-143">Wenn Sie eine App in der Entwicklung starten, die keine *launchSettings.json*-Datei enthält, legen Sie die Umgebung mit einer Umgebungsvariablen oder einem Befehlszeilenargument auf den `dotnet run`-Befehl fest.</span><span class="sxs-lookup"><span data-stu-id="b13d8-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="b13d8-144">Produktion</span><span class="sxs-lookup"><span data-stu-id="b13d8-144">Production</span></span>

<span data-ttu-id="b13d8-145">Die Produktionsumgebung sollte so konfiguriert werden, dass Sicherheit, Leistung und Stabilität der App maximiert werden.</span><span class="sxs-lookup"><span data-stu-id="b13d8-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="b13d8-146">Allgemeine Einstellungen, die sich von der Entwicklung unterscheiden, sind zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="b13d8-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="b13d8-147">Zwischenspeicherung.</span><span class="sxs-lookup"><span data-stu-id="b13d8-147">Caching.</span></span>
* <span data-ttu-id="b13d8-148">Clientseitige Ressourcen werden gebündelt, verkleinert und potenziell von einem CDN bedient.</span><span class="sxs-lookup"><span data-stu-id="b13d8-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="b13d8-149">Seiten zur Fehlerdiagnose sind deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="b13d8-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="b13d8-150">Angezeigte Fehlerseiten sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b13d8-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="b13d8-151">Die Produktionsprotokollierung und -überwachung ist aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b13d8-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="b13d8-152">Beispiel: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="b13d8-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="b13d8-153">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="b13d8-153">Set the environment</span></span>

<span data-ttu-id="b13d8-154">Es ist oft nützlich, zu Testzwecken eine bestimmte Umgebung festzulegen.</span><span class="sxs-lookup"><span data-stu-id="b13d8-154">It's often useful to set a specific environment for testing.</span></span> <span data-ttu-id="b13d8-155">Wenn die Umgebung nicht festgelegt ist, wird `Production` als Standardwert verwendet, wodurch die meisten Debugfeatures deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="b13d8-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="b13d8-156">Welche Methode zum Festlegen der Umgebung verwendet wird, hängt vom Betriebssystem ab.</span><span class="sxs-lookup"><span data-stu-id="b13d8-156">The method for setting the environment depends on the operating system.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="b13d8-157">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="b13d8-157">Azure App Service</span></span>

<span data-ttu-id="b13d8-158">Führen Sie die folgenden Schritte durch, um die Umgebung in [Azure App Service](https://azure.microsoft.com/services/app-service/) festzulegen:</span><span class="sxs-lookup"><span data-stu-id="b13d8-158">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="b13d8-159">Wählen Sie die App auf dem Blatt **App Services** aus.</span><span class="sxs-lookup"><span data-stu-id="b13d8-159">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="b13d8-160">Wählen Sie in der Gruppe **EINSTELLUNGEN** das Blatt **Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="b13d8-160">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="b13d8-161">Wählen Sie im Bereich **Anwendungseinstellung** **Neue Einstellung hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b13d8-161">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="b13d8-162">Für **Geben Sie einen Namen**, bieten `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="b13d8-162">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="b13d8-163">Für **Geben Sie einen Wert**, geben Sie die Umgebung (z. B. `Staging`).</span><span class="sxs-lookup"><span data-stu-id="b13d8-163">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="b13d8-164">Aktivieren Sie das Kontrollkästchen **Sloteinstellung**, wenn Sie möchten, dass die Umgebungseinstellung im aktuellen Slot bleibt, wenn Bereitstellungsslots getauscht werden.</span><span class="sxs-lookup"><span data-stu-id="b13d8-164">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="b13d8-165">Weitere Informationen finden Sie in der [Azure-Dokumentation: Welche Einstellungen werden ausgetauscht?](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="b13d8-165">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="b13d8-166">Klicken Sie oben auf dem Blatt auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="b13d8-166">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="b13d8-167">Azure App Service startet die App automatisch neu, nachdem eine App-Einstellung (Umgebungsvariable) im Azure-Portal hinzugefügt, geändert oder gelöscht wurde.</span><span class="sxs-lookup"><span data-stu-id="b13d8-167">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

### <a name="windows"></a><span data-ttu-id="b13d8-168">Windows</span><span class="sxs-lookup"><span data-stu-id="b13d8-168">Windows</span></span>

<span data-ttu-id="b13d8-169">Zum Festlegen der `ASPNETCORE_ENVIRONMENT` für die aktuelle Sitzung werden folgende Befehle verwendet, wenn die App mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird:</span><span class="sxs-lookup"><span data-stu-id="b13d8-169">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="b13d8-170">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="b13d8-170">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="b13d8-171">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="b13d8-171">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="b13d8-172">Diese Befehle sind nur für das aktuelle Fenster wirksam.</span><span class="sxs-lookup"><span data-stu-id="b13d8-172">These commands only take effect for the current window.</span></span> <span data-ttu-id="b13d8-173">Wenn das Fenster geschlossen wird, wird die Einstellung `ASPNETCORE_ENVIRONMENT` auf die Standardeinstellung oder den Computerwert zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="b13d8-173">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="b13d8-174">Wenn Sie den Wert in Windows global festlegen möchten, nutzen Sie eine der folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="b13d8-174">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="b13d8-175">Öffnen Sie **Systemsteuerung** > **System** > **Erweiterte Systemeinstellungen**, und fügen Sie den Wert `ASPNETCORE_ENVIRONMENT` hinzu, oder bearbeiten Sie ihn:</span><span class="sxs-lookup"><span data-stu-id="b13d8-175">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Erweiterte Systemeigenschaften](environments/_static/systemsetting_environment.png)

  ![ASP.NET Core-Umgebungsvariable](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="b13d8-178">Führen Sie die Eingabeaufforderung als Administrator aus, und verwenden Sie den Befehl `setx`. Alternativ können Sie auch die PowerShell-Eingabeaufforderung als Administrator ausführen und `[Environment]::SetEnvironmentVariable` nutzen:</span><span class="sxs-lookup"><span data-stu-id="b13d8-178">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="b13d8-179">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="b13d8-179">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="b13d8-180">Mit dem Parameter `/M` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="b13d8-180">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="b13d8-181">Ohne den Parameter `/M` wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b13d8-181">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="b13d8-182">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="b13d8-182">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="b13d8-183">Mit dem Optionswert `Machine` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="b13d8-183">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="b13d8-184">Verwenden Sie stattdessen den Optionswert `User`, wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b13d8-184">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="b13d8-185">Sobald die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` global festgelegt wird, gilt sie in jedem geöffneten Befehlsfenster für `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="b13d8-185">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="b13d8-186">**web.config**</span><span class="sxs-lookup"><span data-stu-id="b13d8-186">**web.config**</span></span>

<span data-ttu-id="b13d8-187">Eine Anleitung zum Festlegen der Umgebungsvariable `ASPNETCORE_ENVIRONMENT` mit *web.config* finden Sie im Abschnitt *Festlegen von Umgebungsvariablen* der im Artikel zu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="b13d8-187">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span> <span data-ttu-id="b13d8-188">Wurde die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` mit *web.config* festgelegt, überschreibt der Wert die Einstellung auf Systemebene.</span><span class="sxs-lookup"><span data-stu-id="b13d8-188">When the `ASPNETCORE_ENVIRONMENT` environment variable is set with *web.config*, its value overrides a setting at the system level.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="b13d8-189">**Projektdatei oder Veröffentlichungsprofil**</span><span class="sxs-lookup"><span data-stu-id="b13d8-189">**Project file or publish profile**</span></span>

<span data-ttu-id="b13d8-190">**Für Windows IIS-Bereitstellungen**: Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das Veröffentlichungsprofil (*PUBXML*) oder die Projektdatei ein.</span><span class="sxs-lookup"><span data-stu-id="b13d8-190">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file.</span></span> <span data-ttu-id="b13d8-191">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="b13d8-191">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

<span data-ttu-id="b13d8-192">**Pro IIS-Anwendungspool**</span><span class="sxs-lookup"><span data-stu-id="b13d8-192">**Per IIS Application Pool**</span></span>

<span data-ttu-id="b13d8-193">Wenn Sie für eine App, die in einem isolierten Anwendungspool ausgeführt wird, die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen möchten (unterstützt in IIS 10.0 oder höher), lesen Sie den Abschnitt *AppCmd.exe command (Befehl „AppCmd.exe“)* im Artikel zu [Umgebungsvariablen&lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="b13d8-193">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="b13d8-194">Wurde die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` für einen Anwendungspool festgelegt, überschreibt der Wert die Einstellung auf Systemebene.</span><span class="sxs-lookup"><span data-stu-id="b13d8-194">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b13d8-195">Wenn Sie eine APP in den IIS hosten und die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` hinzufügen oder ändern, sorgen Sie auf eine der folgenden Arten und Weisen dafür, dass der neue Wert in den Apps hinterlegt ist:</span><span class="sxs-lookup"><span data-stu-id="b13d8-195">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="b13d8-196">Führen Sie in einer Eingabeaufforderung `net stop was /y` gefolgt von `net start w3svc` aus.</span><span class="sxs-lookup"><span data-stu-id="b13d8-196">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="b13d8-197">Starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="b13d8-197">Restart the server.</span></span>

### <a name="macos"></a><span data-ttu-id="b13d8-198">macOS</span><span class="sxs-lookup"><span data-stu-id="b13d8-198">macOS</span></span>

<span data-ttu-id="b13d8-199">Das Festlegen der aktuellen Umgebung für macOS kann inline während der Ausführung der App erfolgen:</span><span class="sxs-lookup"><span data-stu-id="b13d8-199">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="b13d8-200">Legen Sie die Umgebung alternativ mit `export` vor der Ausführung der App fest:</span><span class="sxs-lookup"><span data-stu-id="b13d8-200">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="b13d8-201">Umgebungsvariablen auf Computerebene werden in der *BASHRC*- oder *BASH_PROFILE*-Datei festgelegt.</span><span class="sxs-lookup"><span data-stu-id="b13d8-201">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="b13d8-202">Bearbeiten Sie die Datei mit einem beliebigen Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="b13d8-202">Edit the file using any text editor.</span></span> <span data-ttu-id="b13d8-203">Fügen Sie die folgende Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="b13d8-203">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a><span data-ttu-id="b13d8-204">Linux</span><span class="sxs-lookup"><span data-stu-id="b13d8-204">Linux</span></span>

<span data-ttu-id="b13d8-205">Verwenden Sie bei Linux-Distributionen für sitzungsbasierte Variableneinstellungen den Befehl `export` in der Befehlszeile und die *BASH-PROFILE*-Datei für Umgebungseinstellungen auf Computerebene.</span><span class="sxs-lookup"><span data-stu-id="b13d8-205">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="b13d8-206">Konfiguration nach Umgebung</span><span class="sxs-lookup"><span data-stu-id="b13d8-206">Configuration by environment</span></span>

<span data-ttu-id="b13d8-207">Zum Laden von „Konfiguration nach Umgebung“ empfehlen wir:</span><span class="sxs-lookup"><span data-stu-id="b13d8-207">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="b13d8-208">*appsettings*-Dateien (\*appsettings.&lt;<Environment>&gt;.json)</span><span class="sxs-lookup"><span data-stu-id="b13d8-208">*appsettings* files (\*appsettings.&lt;<Environment>&gt;.json).</span></span> <span data-ttu-id="b13d8-209">Weitere Informationen finden Sie unter [Konfiguration: Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider).</span><span class="sxs-lookup"><span data-stu-id="b13d8-209">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider).</span></span>
* <span data-ttu-id="b13d8-210">Umgebungsvariablen (in jedem System festgelegt, in dem die App gehostet wird)</span><span class="sxs-lookup"><span data-stu-id="b13d8-210">environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="b13d8-211">Weitere Informationen finden Sie unter [Konfiguration: Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) und [Sicheres Speichern geheimer App-Schlüssel in der Entwicklung: Umgebungsvariablen](xref:security/app-secrets#environment-variables).</span><span class="sxs-lookup"><span data-stu-id="b13d8-211">See [Configuration: File configuration provider](xref:fundamentals/configuration/index#file-configuration-provider) and [Safe storage of app secrets in development: Environment variables](xref:security/app-secrets#environment-variables).</span></span>
* <span data-ttu-id="b13d8-212">Secret Manager (nur in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="b13d8-212">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="b13d8-213">Siehe <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="b13d8-213">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="b13d8-214">Umgebungsbasierte Startklasse und Methoden</span><span class="sxs-lookup"><span data-stu-id="b13d8-214">Environment-based Startup class and methods</span></span>

### <a name="startup-class-conventions"></a><span data-ttu-id="b13d8-215">Konventionen der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="b13d8-215">Startup class conventions</span></span>

<span data-ttu-id="b13d8-216">Nach dem Start einer ASP.NET Core-App lädt die [Startklasse](xref:fundamentals/startup) die App.</span><span class="sxs-lookup"><span data-stu-id="b13d8-216">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="b13d8-217">Die App kann je nach Umgebung unterschiedliche `Startup`-Klassen definieren (z.B. `StartupDevelopment`). Zur Laufzeit wird dann die passende `Startup`-Klasse ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="b13d8-217">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`), and the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="b13d8-218">Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert.</span><span class="sxs-lookup"><span data-stu-id="b13d8-218">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="b13d8-219">Ist keine übereinstimmende `Startup{EnvironmentName}`-Klasse vorhanden, wird die Klasse `Startup` verwendet.</span><span class="sxs-lookup"><span data-stu-id="b13d8-219">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span>

<span data-ttu-id="b13d8-220">Wenn Sie umgebungsbasierte `Startup`-Klassen implementieren möchten, erstellen Sie für jedes verwendete Element eine `Startup{EnvironmentName}`-Klasse und eine Fallback-Klasse des Typs `Startup`:</span><span class="sxs-lookup"><span data-stu-id="b13d8-220">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

```csharp
// Startup class to use in the Development environment
public class StartupDevelopment
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Startup class to use in the Production environment
public class StartupProduction
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}

// Fallback Startup class
// Selected if the environment doesn't match a Startup{EnvironmentName} class
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        ...
    }
}
```

<span data-ttu-id="b13d8-221">Verwenden Sie die Überladung [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), die einen Assemblynamen akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="b13d8-221">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

::: moniker range=">= aspnetcore-2.1"

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

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
public static void Main(string[] args)
{
    CreateWebHost(args).Run();
}

public static IWebHost CreateWebHost(string[] args)
{
    var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

    return WebHost.CreateDefaultBuilder(args)
        .UseStartup(assemblyName)
        .Build();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var assemblyName = typeof(Startup).GetTypeInfo().Assembly.FullName;

        var host = new WebHostBuilder()
            .UseStartup(assemblyName)
            .Build();

        host.Run();
    }
}
```

::: moniker-end

### <a name="startup-method-conventions"></a><span data-ttu-id="b13d8-222">Konventionen der Methode „Start“</span><span class="sxs-lookup"><span data-stu-id="b13d8-222">Startup method conventions</span></span>

<span data-ttu-id="b13d8-223">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) unterstützen umgebungsspezifische Versionen der Form `Configure<EnvironmentName>` und `Configure<EnvironmentName>Services`:</span><span class="sxs-lookup"><span data-stu-id="b13d8-223">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`:</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,51)]

## <a name="additional-resources"></a><span data-ttu-id="b13d8-224">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="b13d8-224">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [<span data-ttu-id="b13d8-225">IHostingEnvironment.EnvironmentName</span><span class="sxs-lookup"><span data-stu-id="b13d8-225">IHostingEnvironment.EnvironmentName</span></span>](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
