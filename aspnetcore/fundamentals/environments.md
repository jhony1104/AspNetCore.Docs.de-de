---
title: Verwenden von mehreren Umgebungen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie in ASP.NET Core-Apps das App-Verhalten umgebungsübergreifend steuern können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/environments
ms.openlocfilehash: affbb95273c91fe5bf452e0e1ebefa669297304c
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74944320"
---
# <a name="use-multiple-environments-in-aspnet-core"></a><span data-ttu-id="d6f6c-103">Verwenden von mehreren Umgebungen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d6f6c-103">Use multiple environments in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d6f6c-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d6f6c-105">ASP.NET Core konfiguriert das App-Verhalten basierend auf der Laufzeitumgebung mit einer Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-105">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="d6f6c-106">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6f6c-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="d6f6c-107">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="d6f6c-107">Environments</span></span>

<span data-ttu-id="d6f6c-108">ASP.NET Core liest die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` beim Start der App und speichert diesen Wert unter [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-108">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IWebHostEnvironment.EnvironmentName](xref:Microsoft.Extensions.Hosting.IHostEnvironment.EnvironmentName).</span></span> <span data-ttu-id="d6f6c-109">`ASPNETCORE_ENVIRONMENT` kann auf einen beliebigen Wert festgelegt werden, das Framework stellt allerdings nur drei Werte bereit:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-109">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.Extensions.Hosting.Environments.Development>
* <xref:Microsoft.Extensions.Hosting.Environments.Staging>
* <span data-ttu-id="d6f6c-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (Standard)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-110"><xref:Microsoft.Extensions.Hosting.Environments.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="d6f6c-111">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-111">The preceding code:</span></span>

* <span data-ttu-id="d6f6c-112">Ruft [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) auf, wenn `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-112">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="d6f6c-113">Ruft [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) auf, wenn der Wert von `ASPNETCORE_ENVIRONMENT` auf einen der folgenden Werte festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-113">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="d6f6c-114">Das [Umgebungstaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) verwendet den Wert von `IHostingEnvironment.EnvironmentName` zum Einschließen oder Ausschließen von Markup im Element:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-114">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="d6f6c-115">Unter Windows und macOS wird bei Umgebungsvariablen und Werten die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-115">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="d6f6c-116">Bei Linux-Umgebungsvariablen und -Werten **wird die Groß-/Kleinschreibung standardmäßig beachtet**.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-116">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="d6f6c-117">Entwicklung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-117">Development</span></span>

<span data-ttu-id="d6f6c-118">In der Entwicklungsumgebung können Features aktiviert werden, die in der Produktion nicht verfügbar gemacht werden sollten.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-118">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="d6f6c-119">Mit den ASP.NET Core-Vorlagen wird beispielsweise die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#developer-exception-page) in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-119">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="d6f6c-120">Die Umgebung für die Entwicklung lokaler Computer kann in der Datei *Properties\launchSettings.json* des Projekts festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-120">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="d6f6c-121">Mit in der Datei *launchSettings.json* festgelegten Umgebungsvariablen werden in der Systemumgebung festgelegte Werte überschrieben.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-121">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="d6f6c-122">Die folgende JSON zeigt drei Profile aus der Datei *launchSettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-122">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="d6f6c-123">Die Eigenschaft `applicationUrl` in *launchSettings.json* kann eine Liste von Server-URLs angeben.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-123">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="d6f6c-124">Verwenden Sie ein Semikolon zwischen den URLs in der Liste:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-124">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="d6f6c-125">Wenn die Anwendung mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird, wird das erste Profil mit `"commandName": "Project"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-125">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="d6f6c-126">Der Wert von `commandName` gibt den zu startenden Webserver an.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-126">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="d6f6c-127">`commandName` kann einer der folgenden sein:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-127">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="d6f6c-128">`Project` (über das Kestrel gestartet wird)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-128">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="d6f6c-129">Beim Start einer App mit [dotnet run](/dotnet/core/tools/dotnet-run) tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-129">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="d6f6c-130">Die Datei *launchSettings.json* wird, sofern verfügbar, gelesen.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-130">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="d6f6c-131">Durch `environmentVariables`-Einstellungen in der Datei *launchSettings.json* werden Umgebungsvariablen überschrieben.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-131">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="d6f6c-132">Die Hostingumgebung wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-132">The hosting environment is displayed.</span></span>

<span data-ttu-id="d6f6c-133">Die folgende Ausgabe zeigt eine App, die mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wurde:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-133">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="d6f6c-134">Auf der Registerkarte **Debuggen** der Visual Studio-Projekteigenschaften wird eine grafische Benutzeroberfläche für die Bearbeitung der Datei *launchSettings.json* bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-134">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Projekteigenschaften zum Festlegen von Umgebungsvariablen](environments/_static/project-properties-debug.png)

<span data-ttu-id="d6f6c-136">An Projektprofilen vorgenommene Änderungen werden möglicherweise erst nach einem Neustart des Webservers wirksam.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-136">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="d6f6c-137">Kestrel muss neu gestartet werden, bevor es an der Umgebung vorgenommene Änderungen erkennen kann.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-137">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="d6f6c-138">In der Datei *launchSettings.json* sollten keine geheimen Schlüssel gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-138">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="d6f6c-139">Mit dem [Secret Manager-Tool](xref:security/app-secrets) können geheime Schlüssel für die lokale Umgebung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-139">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="d6f6c-140">Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) verwenden, können Umgebungsvariablen in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-140">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="d6f6c-141">Im folgenden Beispiel wird die Umgebung auf `Development` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-141">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="d6f6c-142">Die Datei *.vscode/launch.json* im Projekt wird nicht gelesen, wenn die App mit `dotnet run` genauso wie *Properties/launchSettings.json* gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-142">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="d6f6c-143">Wenn Sie eine App in der Entwicklung starten, die keine *launchSettings.json*-Datei enthält, legen Sie die Umgebung mit einer Umgebungsvariablen oder einem Befehlszeilenargument auf den `dotnet run`-Befehl fest.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-143">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="d6f6c-144">Produktion</span><span class="sxs-lookup"><span data-stu-id="d6f6c-144">Production</span></span>

<span data-ttu-id="d6f6c-145">Die Produktionsumgebung sollte so konfiguriert werden, dass Sicherheit, Leistung und Stabilität der App maximiert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-145">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="d6f6c-146">Allgemeine Einstellungen, die sich von der Entwicklung unterscheiden, sind zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-146">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="d6f6c-147">Zwischenspeicherung.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-147">Caching.</span></span>
* <span data-ttu-id="d6f6c-148">Clientseitige Ressourcen werden gebündelt, verkleinert und potenziell von einem CDN bedient.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-148">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="d6f6c-149">Seiten zur Fehlerdiagnose sind deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-149">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="d6f6c-150">Angezeigte Fehlerseiten sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-150">Friendly error pages enabled.</span></span>
* <span data-ttu-id="d6f6c-151">Die Produktionsprotokollierung und -überwachung ist aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-151">Production logging and monitoring enabled.</span></span> <span data-ttu-id="d6f6c-152">Beispiel: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-152">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="d6f6c-153">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-153">Set the environment</span></span>

<span data-ttu-id="d6f6c-154">Häufig ist es sinnvoll, mit einer Umgebungsvariablen oder Plattformeinstellung eine bestimmte Umgebung für Tests festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-154">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="d6f6c-155">Wenn die Umgebung nicht festgelegt ist, wird `Production` als Standardwert verwendet, wodurch die meisten Debugfeatures deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-155">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="d6f6c-156">Welche Methode zum Festlegen der Umgebung verwendet wird, hängt vom Betriebssystem ab.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-156">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="d6f6c-157">Wenn der Host erstellt wird, bestimmt die letzte von der App gelesene Umgebungseinstellung die Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-157">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="d6f6c-158">Die Umgebung der App kann nicht geändert werden, während die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-158">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="d6f6c-159">Umgebungsvariable oder Plattformeinstellung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-159">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="d6f6c-160">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6f6c-160">Azure App Service</span></span>

<span data-ttu-id="d6f6c-161">Führen Sie die folgenden Schritte durch, um die Umgebung in [Azure App Service](https://azure.microsoft.com/services/app-service/) festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-161">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="d6f6c-162">Wählen Sie die App auf dem Blatt **App Services** aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-162">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="d6f6c-163">Wählen Sie in der Gruppe **EINSTELLUNGEN** das Blatt **Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-163">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="d6f6c-164">Wählen Sie im Bereich **Anwendungseinstellung** **Neue Einstellung hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-164">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="d6f6c-165">Für **Geben Sie einen Namen**, bieten `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-165">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="d6f6c-166">Für **Geben Sie einen Wert**, geben Sie die Umgebung (z. B. `Staging`).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-166">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="d6f6c-167">Aktivieren Sie das Kontrollkästchen **Sloteinstellung**, wenn Sie möchten, dass die Umgebungseinstellung im aktuellen Slot bleibt, wenn Bereitstellungsslots getauscht werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-167">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="d6f6c-168">Weitere Informationen finden Sie in der [Azure-Dokumentation: Welche Einstellungen werden ausgetauscht?](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-168">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="d6f6c-169">Klicken Sie oben auf dem Blatt auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-169">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="d6f6c-170">Azure App Service startet die App automatisch neu, nachdem eine App-Einstellung (Umgebungsvariable) im Azure-Portal hinzugefügt, geändert oder gelöscht wurde.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-170">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="d6f6c-171">Windows</span><span class="sxs-lookup"><span data-stu-id="d6f6c-171">Windows</span></span>

<span data-ttu-id="d6f6c-172">Zum Festlegen der `ASPNETCORE_ENVIRONMENT` für die aktuelle Sitzung werden folgende Befehle verwendet, wenn die App mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-172">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="d6f6c-173">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-173">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="d6f6c-174">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-174">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="d6f6c-175">Diese Befehle sind nur für das aktuelle Fenster wirksam.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-175">These commands only take effect for the current window.</span></span> <span data-ttu-id="d6f6c-176">Wenn das Fenster geschlossen wird, wird die Einstellung `ASPNETCORE_ENVIRONMENT` auf die Standardeinstellung oder den Computerwert zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-176">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="d6f6c-177">Wenn Sie den Wert in Windows global festlegen möchten, nutzen Sie eine der folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-177">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="d6f6c-178">Öffnen Sie **Systemsteuerung** > **System** > **Erweiterte Systemeinstellungen**, und fügen Sie den Wert `ASPNETCORE_ENVIRONMENT` hinzu, oder bearbeiten Sie ihn:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-178">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Erweiterte Systemeigenschaften](environments/_static/systemsetting_environment.png)

  ![ASP.NET Core-Umgebungsvariable](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="d6f6c-181">Führen Sie die Eingabeaufforderung als Administrator aus, und verwenden Sie den Befehl `setx`. Alternativ können Sie auch die PowerShell-Eingabeaufforderung als Administrator ausführen und `[Environment]::SetEnvironmentVariable` nutzen:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-181">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="d6f6c-182">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-182">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="d6f6c-183">Mit dem Parameter `/M` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-183">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="d6f6c-184">Ohne den Parameter `/M` wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-184">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="d6f6c-185">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-185">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="d6f6c-186">Mit dem Optionswert `Machine` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-186">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="d6f6c-187">Verwenden Sie stattdessen den Optionswert `User`, wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-187">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="d6f6c-188">Sobald die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` global festgelegt wird, gilt sie in jedem geöffneten Befehlsfenster für `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-188">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="d6f6c-189">**web.config**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-189">**web.config**</span></span>

<span data-ttu-id="d6f6c-190">Eine Anleitung zum Festlegen der Umgebungsvariable `ASPNETCORE_ENVIRONMENT` mit *web.config* finden Sie im Abschnitt *Festlegen von Umgebungsvariablen* der im Artikel zu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-190">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="d6f6c-191">**Projektdatei oder Veröffentlichungsprofil**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-191">**Project file or publish profile**</span></span>

<span data-ttu-id="d6f6c-192">**Für Windows IIS-Bereitstellungen**: Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder die Projektdatei ein.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-192">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="d6f6c-193">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-193">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="d6f6c-194">**Pro IIS-Anwendungspool**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-194">**Per IIS Application Pool**</span></span>

<span data-ttu-id="d6f6c-195">Wenn Sie für eine App, die in einem isolierten Anwendungspool ausgeführt wird, die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen möchten (unterstützt in IIS 10.0 oder höher), lesen Sie den Abschnitt *AppCmd.exe command (Befehl „AppCmd.exe“)* im Artikel zu [Umgebungsvariablen&lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-195">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="d6f6c-196">Wurde die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` für einen Anwendungspool festgelegt, überschreibt der Wert die Einstellung auf Systemebene.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-196">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d6f6c-197">Wenn Sie eine APP in den IIS hosten und die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` hinzufügen oder ändern, sorgen Sie auf eine der folgenden Arten und Weisen dafür, dass der neue Wert in den Apps hinterlegt ist:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-197">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="d6f6c-198">Führen Sie in einer Eingabeaufforderung `net stop was /y` gefolgt von `net start w3svc` aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-198">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="d6f6c-199">Starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-199">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="d6f6c-200">macOS</span><span class="sxs-lookup"><span data-stu-id="d6f6c-200">macOS</span></span>

<span data-ttu-id="d6f6c-201">Das Festlegen der aktuellen Umgebung für macOS kann inline während der Ausführung der App erfolgen:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-201">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="d6f6c-202">Legen Sie die Umgebung alternativ mit `export` vor der Ausführung der App fest:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-202">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="d6f6c-203">Umgebungsvariablen auf Computerebene werden in der *BASHRC*- oder *BASH_PROFILE*-Datei festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-203">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="d6f6c-204">Bearbeiten Sie die Datei mit einem beliebigen Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-204">Edit the file using any text editor.</span></span> <span data-ttu-id="d6f6c-205">Fügen Sie die folgende Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-205">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="d6f6c-206">Linux</span><span class="sxs-lookup"><span data-stu-id="d6f6c-206">Linux</span></span>

<span data-ttu-id="d6f6c-207">Verwenden Sie bei Linux-Distributionen für sitzungsbasierte Variableneinstellungen den Befehl `export` in der Befehlszeile und die *BASH-PROFILE*-Datei für Umgebungseinstellungen auf Computerebene.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-207">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="d6f6c-208">Festlegen der Umgebung im Code</span><span class="sxs-lookup"><span data-stu-id="d6f6c-208">Set the environment in code</span></span>

<span data-ttu-id="d6f6c-209">Rufen Sie beim Erstellen des Hosts <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> auf.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-209">Call <xref:Microsoft.Extensions.Hosting.HostingHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="d6f6c-210">Siehe <xref:fundamentals/host/generic-host#environmentname>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-210">See <xref:fundamentals/host/generic-host#environmentname>.</span></span>


### <a name="configuration-by-environment"></a><span data-ttu-id="d6f6c-211">Konfiguration nach Umgebung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-211">Configuration by environment</span></span>

<span data-ttu-id="d6f6c-212">Zum Laden von „Konfiguration nach Umgebung“ empfehlen wir:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-212">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="d6f6c-213">*appsettings*-Dateien (*appsettings.{Umgebung}.json*).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-213">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="d6f6c-214">Siehe <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-214">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="d6f6c-215">Umgebungsvariablen (in jedem System festgelegt, in dem die App gehostet wird).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-215">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="d6f6c-216">Weitere Informationen finden Sie unter <xref:fundamentals/host/generic-host#environmentname> und <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-216">See <xref:fundamentals/host/generic-host#environmentname> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="d6f6c-217">Secret Manager (nur in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-217">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="d6f6c-218">Siehe <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-218">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="d6f6c-219">Umgebungsbasierte Startklasse und Methoden</span><span class="sxs-lookup"><span data-stu-id="d6f6c-219">Environment-based Startup class and methods</span></span>

### <a name="inject-iwebhostenvironment-into-startupconfigure"></a><span data-ttu-id="d6f6c-220">Einfügen von IWebHostEnvironment in Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="d6f6c-220">Inject IWebHostEnvironment into Startup.Configure</span></span>

<span data-ttu-id="d6f6c-221">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in `Startup.Configure` ein.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-221">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="d6f6c-222">Diese Vorgehensweise ist nützlich, wenn die App `Startup.Configure` nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung anpassen muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-222">This approach is useful when the app only requires adjusting `Startup.Configure` for a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-iwebhostenvironment-into-the-startup-class"></a><span data-ttu-id="d6f6c-223">Einfügen von IWebHostEnvironment in die Startklasse</span><span class="sxs-lookup"><span data-stu-id="d6f6c-223">Inject IWebHostEnvironment into the Startup class</span></span>

<span data-ttu-id="d6f6c-224">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> in den `Startup`-Konstruktor ein.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-224">Inject <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> into the `Startup` constructor.</span></span> <span data-ttu-id="d6f6c-225">Diese Vorgehensweise ist nützlich, wenn die App `Startup` nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-225">This approach is useful when the app requires configuring `Startup` for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="d6f6c-226">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-226">In the following example:</span></span>

* <span data-ttu-id="d6f6c-227">Die Umgebung wird im Feld `_env` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-227">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="d6f6c-228">`_env` wird in `ConfigureServices` und `Configure` verwendet, um die Startkonfiguration basierend auf der Umgebung der App anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-228">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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
### <a name="startup-class-conventions"></a><span data-ttu-id="d6f6c-229">Konventionen der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="d6f6c-229">Startup class conventions</span></span>

<span data-ttu-id="d6f6c-230">Nach dem Start einer ASP.NET Core-App lädt die [Startklasse](xref:fundamentals/startup) die App.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-230">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="d6f6c-231">Die App kann je nach Umgebung unterschiedliche `Startup`-Klassen definieren (z. B. `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-231">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="d6f6c-232">Die entsprechende `Startup`-Klasse wird zur Laufzeit ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-232">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="d6f6c-233">Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-233">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="d6f6c-234">Ist keine übereinstimmende `Startup{EnvironmentName}`-Klasse vorhanden, wird die Klasse `Startup` verwendet.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-234">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="d6f6c-235">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-235">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="d6f6c-236">Wenn Sie umgebungsbasierte `Startup`-Klassen implementieren möchten, erstellen Sie für jedes verwendete Element eine `Startup{EnvironmentName}`-Klasse und eine Fallback-Klasse des Typs `Startup`:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-236">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="d6f6c-237">Verwenden Sie die Überladung [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), die einen Assemblynamen akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-237">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="d6f6c-238">Konventionen der Methode „Start“</span><span class="sxs-lookup"><span data-stu-id="d6f6c-238">Startup method conventions</span></span>

<span data-ttu-id="d6f6c-239">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) unterstützen umgebungsspezifische Versionen der Form `Configure<EnvironmentName>` und `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-239">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="d6f6c-240">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-240">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="d6f6c-241">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d6f6c-241">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d6f6c-242">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-242">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d6f6c-243">ASP.NET Core konfiguriert das App-Verhalten basierend auf der Laufzeitumgebung mit einer Umgebungsvariablen.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-243">ASP.NET Core configures app behavior based on the runtime environment using an environment variable.</span></span>

<span data-ttu-id="d6f6c-244">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d6f6c-244">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="environments"></a><span data-ttu-id="d6f6c-245">Umgebungen</span><span class="sxs-lookup"><span data-stu-id="d6f6c-245">Environments</span></span>

<span data-ttu-id="d6f6c-246">ASP.NET Core liest die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` beim Start der App und speichert diesen Wert unter [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-246">ASP.NET Core reads the environment variable `ASPNETCORE_ENVIRONMENT` at app startup and stores the value in [IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName).</span></span> <span data-ttu-id="d6f6c-247">`ASPNETCORE_ENVIRONMENT` kann auf einen beliebigen Wert festgelegt werden, das Framework stellt allerdings nur drei Werte bereit:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-247">`ASPNETCORE_ENVIRONMENT` can be set to any value, but three values are provided by the framework:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Development>
* <xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Staging>
* <span data-ttu-id="d6f6c-248"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (Standard)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-248"><xref:Microsoft.AspNetCore.Hosting.EnvironmentName.Production> (default)</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

<span data-ttu-id="d6f6c-249">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-249">The preceding code:</span></span>

* <span data-ttu-id="d6f6c-250">Ruft [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) auf, wenn `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-250">Calls [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) when `ASPNETCORE_ENVIRONMENT` is set to `Development`.</span></span>
* <span data-ttu-id="d6f6c-251">Ruft [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) auf, wenn der Wert von `ASPNETCORE_ENVIRONMENT` auf einen der folgenden Werte festgelegt ist:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-251">Calls [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) when the value of `ASPNETCORE_ENVIRONMENT` is set one of the following:</span></span>

  * `Staging`
  * `Production`
  * `Staging_2`

<span data-ttu-id="d6f6c-252">Das [Umgebungstaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) verwendet den Wert von `IHostingEnvironment.EnvironmentName` zum Einschließen oder Ausschließen von Markup im Element:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-252">The [Environment Tag Helper](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) uses the value of `IHostingEnvironment.EnvironmentName` to include or exclude markup in the element:</span></span>

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

<span data-ttu-id="d6f6c-253">Unter Windows und macOS wird bei Umgebungsvariablen und Werten die Groß-/Kleinschreibung nicht beachtet.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-253">On Windows and macOS, environment variables and values aren't case sensitive.</span></span> <span data-ttu-id="d6f6c-254">Bei Linux-Umgebungsvariablen und -Werten **wird die Groß-/Kleinschreibung standardmäßig beachtet**.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-254">Linux environment variables and values are **case sensitive** by default.</span></span>

### <a name="development"></a><span data-ttu-id="d6f6c-255">Entwicklung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-255">Development</span></span>

<span data-ttu-id="d6f6c-256">In der Entwicklungsumgebung können Features aktiviert werden, die in der Produktion nicht verfügbar gemacht werden sollten.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-256">The development environment can enable features that shouldn't be exposed in production.</span></span> <span data-ttu-id="d6f6c-257">Mit den ASP.NET Core-Vorlagen wird beispielsweise die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#developer-exception-page) in der Entwicklungsumgebung aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-257">For example, the ASP.NET Core templates enable the [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page) in the development environment.</span></span>

<span data-ttu-id="d6f6c-258">Die Umgebung für die Entwicklung lokaler Computer kann in der Datei *Properties\launchSettings.json* des Projekts festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-258">The environment for local machine development can be set in the *Properties\launchSettings.json* file of the project.</span></span> <span data-ttu-id="d6f6c-259">Mit in der Datei *launchSettings.json* festgelegten Umgebungsvariablen werden in der Systemumgebung festgelegte Werte überschrieben.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-259">Environment values set in *launchSettings.json* override values set in the system environment.</span></span>

<span data-ttu-id="d6f6c-260">Die folgende JSON zeigt drei Profile aus der Datei *launchSettings.json* an:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-260">The following JSON shows three profiles from a *launchSettings.json* file:</span></span>

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
> <span data-ttu-id="d6f6c-261">Die Eigenschaft `applicationUrl` in *launchSettings.json* kann eine Liste von Server-URLs angeben.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-261">The `applicationUrl` property in *launchSettings.json* can specify a list of server URLs.</span></span> <span data-ttu-id="d6f6c-262">Verwenden Sie ein Semikolon zwischen den URLs in der Liste:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-262">Use a semicolon between the URLs in the list:</span></span>
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

<span data-ttu-id="d6f6c-263">Wenn die Anwendung mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird, wird das erste Profil mit `"commandName": "Project"` verwendet.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-263">When the app is launched with [dotnet run](/dotnet/core/tools/dotnet-run), the first profile with `"commandName": "Project"` is used.</span></span> <span data-ttu-id="d6f6c-264">Der Wert von `commandName` gibt den zu startenden Webserver an.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-264">The value of `commandName` specifies the web server to launch.</span></span> <span data-ttu-id="d6f6c-265">`commandName` kann einer der folgenden sein:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-265">`commandName` can be any one of the following:</span></span>

* `IISExpress`
* `IIS`
* <span data-ttu-id="d6f6c-266">`Project` (über das Kestrel gestartet wird)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-266">`Project` (which launches Kestrel)</span></span>

<span data-ttu-id="d6f6c-267">Beim Start einer App mit [dotnet run](/dotnet/core/tools/dotnet-run) tritt Folgendes ein:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-267">When an app is launched with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

* <span data-ttu-id="d6f6c-268">Die Datei *launchSettings.json* wird, sofern verfügbar, gelesen.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-268">*launchSettings.json* is read if available.</span></span> <span data-ttu-id="d6f6c-269">Durch `environmentVariables`-Einstellungen in der Datei *launchSettings.json* werden Umgebungsvariablen überschrieben.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-269">`environmentVariables` settings in *launchSettings.json* override environment variables.</span></span>
* <span data-ttu-id="d6f6c-270">Die Hostingumgebung wird angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-270">The hosting environment is displayed.</span></span>

<span data-ttu-id="d6f6c-271">Die folgende Ausgabe zeigt eine App, die mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wurde:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-271">The following output shows an app started with [dotnet run](/dotnet/core/tools/dotnet-run):</span></span>

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="d6f6c-272">Auf der Registerkarte **Debuggen** der Visual Studio-Projekteigenschaften wird eine grafische Benutzeroberfläche für die Bearbeitung der Datei *launchSettings.json* bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-272">The Visual Studio project properties **Debug** tab provides a GUI to edit the *launchSettings.json* file:</span></span>

![Projekteigenschaften zum Festlegen von Umgebungsvariablen](environments/_static/project-properties-debug.png)

<span data-ttu-id="d6f6c-274">An Projektprofilen vorgenommene Änderungen werden möglicherweise erst nach einem Neustart des Webservers wirksam.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-274">Changes made to project profiles may not take effect until the web server is restarted.</span></span> <span data-ttu-id="d6f6c-275">Kestrel muss neu gestartet werden, bevor es an der Umgebung vorgenommene Änderungen erkennen kann.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-275">Kestrel must be restarted before it can detect changes made to its environment.</span></span>

> [!WARNING]
> <span data-ttu-id="d6f6c-276">In der Datei *launchSettings.json* sollten keine geheimen Schlüssel gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-276">*launchSettings.json* shouldn't store secrets.</span></span> <span data-ttu-id="d6f6c-277">Mit dem [Secret Manager-Tool](xref:security/app-secrets) können geheime Schlüssel für die lokale Umgebung gespeichert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-277">The [Secret Manager tool](xref:security/app-secrets) can be used to store secrets for local development.</span></span>

<span data-ttu-id="d6f6c-278">Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) verwenden, können Umgebungsvariablen in der Datei *.vscode/launch.json* festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-278">When using [Visual Studio Code](https://code.visualstudio.com/), environment variables can be set in the *.vscode/launch.json* file.</span></span> <span data-ttu-id="d6f6c-279">Im folgenden Beispiel wird die Umgebung auf `Development` festgelegt:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-279">The following example sets the environment to `Development`:</span></span>

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

<span data-ttu-id="d6f6c-280">Die Datei *.vscode/launch.json* im Projekt wird nicht gelesen, wenn die App mit `dotnet run` genauso wie *Properties/launchSettings.json* gestartet wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-280">A *.vscode/launch.json* file in the project isn't read when starting the app with `dotnet run` in the same way as *Properties/launchSettings.json*.</span></span> <span data-ttu-id="d6f6c-281">Wenn Sie eine App in der Entwicklung starten, die keine *launchSettings.json*-Datei enthält, legen Sie die Umgebung mit einer Umgebungsvariablen oder einem Befehlszeilenargument auf den `dotnet run`-Befehl fest.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-281">When launching an app in development that doesn't have a *launchSettings.json* file, either set the environment with an environment variable or a command-line argument to the `dotnet run` command.</span></span>

### <a name="production"></a><span data-ttu-id="d6f6c-282">Produktion</span><span class="sxs-lookup"><span data-stu-id="d6f6c-282">Production</span></span>

<span data-ttu-id="d6f6c-283">Die Produktionsumgebung sollte so konfiguriert werden, dass Sicherheit, Leistung und Stabilität der App maximiert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-283">The production environment should be configured to maximize security, performance, and app robustness.</span></span> <span data-ttu-id="d6f6c-284">Allgemeine Einstellungen, die sich von der Entwicklung unterscheiden, sind zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-284">Some common settings that differ from development include:</span></span>

* <span data-ttu-id="d6f6c-285">Zwischenspeicherung.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-285">Caching.</span></span>
* <span data-ttu-id="d6f6c-286">Clientseitige Ressourcen werden gebündelt, verkleinert und potenziell von einem CDN bedient.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-286">Client-side resources are bundled, minified, and potentially served from a CDN.</span></span>
* <span data-ttu-id="d6f6c-287">Seiten zur Fehlerdiagnose sind deaktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-287">Diagnostic error pages disabled.</span></span>
* <span data-ttu-id="d6f6c-288">Angezeigte Fehlerseiten sind aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-288">Friendly error pages enabled.</span></span>
* <span data-ttu-id="d6f6c-289">Die Produktionsprotokollierung und -überwachung ist aktiviert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-289">Production logging and monitoring enabled.</span></span> <span data-ttu-id="d6f6c-290">Beispiel: [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-290">For example, [Application Insights](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="d6f6c-291">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-291">Set the environment</span></span>

<span data-ttu-id="d6f6c-292">Häufig ist es sinnvoll, mit einer Umgebungsvariablen oder Plattformeinstellung eine bestimmte Umgebung für Tests festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-292">It's often useful to set a specific environment for testing with an environment variable or platform setting.</span></span> <span data-ttu-id="d6f6c-293">Wenn die Umgebung nicht festgelegt ist, wird `Production` als Standardwert verwendet, wodurch die meisten Debugfeatures deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-293">If the environment isn't set, it defaults to `Production`, which disables most debugging features.</span></span> <span data-ttu-id="d6f6c-294">Welche Methode zum Festlegen der Umgebung verwendet wird, hängt vom Betriebssystem ab.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-294">The method for setting the environment depends on the operating system.</span></span>

<span data-ttu-id="d6f6c-295">Wenn der Host erstellt wird, bestimmt die letzte von der App gelesene Umgebungseinstellung die Umgebung der App.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-295">When the host is built, the last environment setting read by the app determines the app's environment.</span></span> <span data-ttu-id="d6f6c-296">Die Umgebung der App kann nicht geändert werden, während die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-296">The app's environment can't be changed while the app is running.</span></span>

### <a name="environment-variable-or-platform-setting"></a><span data-ttu-id="d6f6c-297">Umgebungsvariable oder Plattformeinstellung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-297">Environment variable or platform setting</span></span>

#### <a name="azure-app-service"></a><span data-ttu-id="d6f6c-298">Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d6f6c-298">Azure App Service</span></span>

<span data-ttu-id="d6f6c-299">Führen Sie die folgenden Schritte durch, um die Umgebung in [Azure App Service](https://azure.microsoft.com/services/app-service/) festzulegen:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-299">To set the environment in [Azure App Service](https://azure.microsoft.com/services/app-service/), perform the following steps:</span></span>

1. <span data-ttu-id="d6f6c-300">Wählen Sie die App auf dem Blatt **App Services** aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-300">Select the app from the **App Services** blade.</span></span>
1. <span data-ttu-id="d6f6c-301">Wählen Sie in der Gruppe **EINSTELLUNGEN** das Blatt **Anwendungseinstellung** aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-301">In the **SETTINGS** group, select the **Application settings** blade.</span></span>
1. <span data-ttu-id="d6f6c-302">Wählen Sie im Bereich **Anwendungseinstellung** **Neue Einstellung hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-302">In the **Application settings** area, select **Add new setting**.</span></span>
1. <span data-ttu-id="d6f6c-303">Für **Geben Sie einen Namen**, bieten `ASPNETCORE_ENVIRONMENT`.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-303">For **Enter a name**, provide `ASPNETCORE_ENVIRONMENT`.</span></span> <span data-ttu-id="d6f6c-304">Für **Geben Sie einen Wert**, geben Sie die Umgebung (z. B. `Staging`).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-304">For **Enter a value**, provide the environment (for example, `Staging`).</span></span>
1. <span data-ttu-id="d6f6c-305">Aktivieren Sie das Kontrollkästchen **Sloteinstellung**, wenn Sie möchten, dass die Umgebungseinstellung im aktuellen Slot bleibt, wenn Bereitstellungsslots getauscht werden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-305">Select the **Slot Setting** check box if you wish the environment setting to remain with the current slot when deployment slots are swapped.</span></span> <span data-ttu-id="d6f6c-306">Weitere Informationen finden Sie in der [Azure-Dokumentation: Welche Einstellungen werden ausgetauscht?](/azure/app-service/web-sites-staged-publishing).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-306">For more information, see [Azure Documentation: Which settings are swapped?](/azure/app-service/web-sites-staged-publishing).</span></span>
1. <span data-ttu-id="d6f6c-307">Klicken Sie oben auf dem Blatt auf **Speichern**.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-307">Select **Save** at the top of the blade.</span></span>

<span data-ttu-id="d6f6c-308">Azure App Service startet die App automatisch neu, nachdem eine App-Einstellung (Umgebungsvariable) im Azure-Portal hinzugefügt, geändert oder gelöscht wurde.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-308">Azure App Service automatically restarts the app after an app setting (environment variable) is added, changed, or deleted in the Azure portal.</span></span>

#### <a name="windows"></a><span data-ttu-id="d6f6c-309">Windows</span><span class="sxs-lookup"><span data-stu-id="d6f6c-309">Windows</span></span>

<span data-ttu-id="d6f6c-310">Zum Festlegen der `ASPNETCORE_ENVIRONMENT` für die aktuelle Sitzung werden folgende Befehle verwendet, wenn die App mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-310">To set the `ASPNETCORE_ENVIRONMENT` for the current session when the app is started using [dotnet run](/dotnet/core/tools/dotnet-run), the following commands are used:</span></span>

<span data-ttu-id="d6f6c-311">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-311">**Command prompt**</span></span>

```console
set ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="d6f6c-312">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-312">**PowerShell**</span></span>

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

<span data-ttu-id="d6f6c-313">Diese Befehle sind nur für das aktuelle Fenster wirksam.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-313">These commands only take effect for the current window.</span></span> <span data-ttu-id="d6f6c-314">Wenn das Fenster geschlossen wird, wird die Einstellung `ASPNETCORE_ENVIRONMENT` auf die Standardeinstellung oder den Computerwert zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-314">When the window is closed, the `ASPNETCORE_ENVIRONMENT` setting reverts to the default setting or machine value.</span></span>

<span data-ttu-id="d6f6c-315">Wenn Sie den Wert in Windows global festlegen möchten, nutzen Sie eine der folgenden Möglichkeiten:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-315">To set the value globally in Windows, use either of the following approaches:</span></span>

* <span data-ttu-id="d6f6c-316">Öffnen Sie **Systemsteuerung** > **System** > **Erweiterte Systemeinstellungen**, und fügen Sie den Wert `ASPNETCORE_ENVIRONMENT` hinzu, oder bearbeiten Sie ihn:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-316">Open the **Control Panel** > **System** > **Advanced system settings** and add or edit the `ASPNETCORE_ENVIRONMENT` value:</span></span>

  ![Erweiterte Systemeigenschaften](environments/_static/systemsetting_environment.png)

  ![ASP.NET Core-Umgebungsvariable](environments/_static/windows_aspnetcore_environment.png)

* <span data-ttu-id="d6f6c-319">Führen Sie die Eingabeaufforderung als Administrator aus, und verwenden Sie den Befehl `setx`. Alternativ können Sie auch die PowerShell-Eingabeaufforderung als Administrator ausführen und `[Environment]::SetEnvironmentVariable` nutzen:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-319">Open an administrative command prompt and use the `setx` command or open an administrative PowerShell command prompt and use `[Environment]::SetEnvironmentVariable`:</span></span>

  <span data-ttu-id="d6f6c-320">**Eingabeaufforderung**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-320">**Command prompt**</span></span>

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  <span data-ttu-id="d6f6c-321">Mit dem Parameter `/M` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-321">The `/M` switch indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="d6f6c-322">Ohne den Parameter `/M` wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-322">If the `/M` switch isn't used, the environment variable is set for the user account.</span></span>

  <span data-ttu-id="d6f6c-323">**PowerShell**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-323">**PowerShell**</span></span>

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  <span data-ttu-id="d6f6c-324">Mit dem Optionswert `Machine` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-324">The `Machine` option value indicates to set the environment variable at the system level.</span></span> <span data-ttu-id="d6f6c-325">Verwenden Sie stattdessen den Optionswert `User`, wird die Umgebungsvariable für das Nutzerkonto festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-325">If the option value is changed to `User`, the environment variable is set for the user account.</span></span>

<span data-ttu-id="d6f6c-326">Sobald die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` global festgelegt wird, gilt sie in jedem geöffneten Befehlsfenster für `dotnet run`.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-326">When the `ASPNETCORE_ENVIRONMENT` environment variable is set globally, it takes effect for `dotnet run` in any command window opened after the value is set.</span></span>

<span data-ttu-id="d6f6c-327">**web.config**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-327">**web.config**</span></span>

<span data-ttu-id="d6f6c-328">Eine Anleitung zum Festlegen der Umgebungsvariable `ASPNETCORE_ENVIRONMENT` mit *web.config* finden Sie im Abschnitt *Festlegen von Umgebungsvariablen* der im Artikel zu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-328">To set the `ASPNETCORE_ENVIRONMENT` environment variable with *web.config*, see the *Setting environment variables* section of <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.</span></span>

<span data-ttu-id="d6f6c-329">**Projektdatei oder Veröffentlichungsprofil**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-329">**Project file or publish profile**</span></span>

<span data-ttu-id="d6f6c-330">**Für Windows IIS-Bereitstellungen**: Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das [Veröffentlichungsprofil (PUBXML)](xref:host-and-deploy/visual-studio-publish-profiles) oder die Projektdatei ein.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-330">**For Windows IIS deployments:** Include the `<EnvironmentName>` property in the [publish profile (.pubxml)](xref:host-and-deploy/visual-studio-publish-profiles) or project file.</span></span> <span data-ttu-id="d6f6c-331">Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-331">This approach sets the environment in *web.config* when the project is published:</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="d6f6c-332">**Pro IIS-Anwendungspool**</span><span class="sxs-lookup"><span data-stu-id="d6f6c-332">**Per IIS Application Pool**</span></span>

<span data-ttu-id="d6f6c-333">Wenn Sie für eine App, die in einem isolierten Anwendungspool ausgeführt wird, die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen möchten (unterstützt in IIS 10.0 oder höher), lesen Sie den Abschnitt *AppCmd.exe command (Befehl „AppCmd.exe“)* im Artikel zu [Umgebungsvariablen&lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-333">To set the `ASPNETCORE_ENVIRONMENT` environment variable for an app running in an isolated Application Pool (supported on IIS 10.0 or later), see the *AppCmd.exe command* section of the [Environment Variables &lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) topic.</span></span> <span data-ttu-id="d6f6c-334">Wurde die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` für einen Anwendungspool festgelegt, überschreibt der Wert die Einstellung auf Systemebene.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-334">When the `ASPNETCORE_ENVIRONMENT` environment variable is set for an app pool, its value overrides a setting at the system level.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d6f6c-335">Wenn Sie eine APP in den IIS hosten und die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` hinzufügen oder ändern, sorgen Sie auf eine der folgenden Arten und Weisen dafür, dass der neue Wert in den Apps hinterlegt ist:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-335">When hosting an app in IIS and adding or changing the `ASPNETCORE_ENVIRONMENT` environment variable, use any one of the following approaches to have the new value picked up by apps:</span></span>
>
> * <span data-ttu-id="d6f6c-336">Führen Sie in einer Eingabeaufforderung `net stop was /y` gefolgt von `net start w3svc` aus.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-336">Execute `net stop was /y` followed by `net start w3svc` from a command prompt.</span></span>
> * <span data-ttu-id="d6f6c-337">Starten Sie den Server neu.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-337">Restart the server.</span></span>

#### <a name="macos"></a><span data-ttu-id="d6f6c-338">macOS</span><span class="sxs-lookup"><span data-stu-id="d6f6c-338">macOS</span></span>

<span data-ttu-id="d6f6c-339">Das Festlegen der aktuellen Umgebung für macOS kann inline während der Ausführung der App erfolgen:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-339">Setting the current environment for macOS can be performed in-line when running the app:</span></span>

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

<span data-ttu-id="d6f6c-340">Legen Sie die Umgebung alternativ mit `export` vor der Ausführung der App fest:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-340">Alternatively, set the environment with `export` prior to running the app:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

<span data-ttu-id="d6f6c-341">Umgebungsvariablen auf Computerebene werden in der *BASHRC*- oder *BASH_PROFILE*-Datei festgelegt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-341">Machine-level environment variables are set in the *.bashrc* or *.bash_profile* file.</span></span> <span data-ttu-id="d6f6c-342">Bearbeiten Sie die Datei mit einem beliebigen Text-Editor.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-342">Edit the file using any text editor.</span></span> <span data-ttu-id="d6f6c-343">Fügen Sie die folgende Anweisung hinzu:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-343">Add the following statement:</span></span>

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

#### <a name="linux"></a><span data-ttu-id="d6f6c-344">Linux</span><span class="sxs-lookup"><span data-stu-id="d6f6c-344">Linux</span></span>

<span data-ttu-id="d6f6c-345">Verwenden Sie bei Linux-Distributionen für sitzungsbasierte Variableneinstellungen den Befehl `export` in der Befehlszeile und die *BASH-PROFILE*-Datei für Umgebungseinstellungen auf Computerebene.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-345">For Linux distros, use the `export` command at a command prompt for session-based variable settings and *bash_profile* file for machine-level environment settings.</span></span>

### <a name="set-the-environment-in-code"></a><span data-ttu-id="d6f6c-346">Festlegen der Umgebung im Code</span><span class="sxs-lookup"><span data-stu-id="d6f6c-346">Set the environment in code</span></span>

<span data-ttu-id="d6f6c-347">Rufen Sie beim Erstellen des Hosts <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> auf.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-347">Call <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseEnvironment*> when building the host.</span></span> <span data-ttu-id="d6f6c-348">Siehe <xref:fundamentals/host/web-host#environment>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-348">See <xref:fundamentals/host/web-host#environment>.</span></span>

### <a name="configuration-by-environment"></a><span data-ttu-id="d6f6c-349">Konfiguration nach Umgebung</span><span class="sxs-lookup"><span data-stu-id="d6f6c-349">Configuration by environment</span></span>

<span data-ttu-id="d6f6c-350">Zum Laden von „Konfiguration nach Umgebung“ empfehlen wir:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-350">To load configuration by environment, we recommend:</span></span>

* <span data-ttu-id="d6f6c-351">*appsettings*-Dateien (*appsettings.{Umgebung}.json*).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-351">*appsettings* files (*appsettings.{Environment}.json*).</span></span> <span data-ttu-id="d6f6c-352">Siehe <xref:fundamentals/configuration/index#json-configuration-provider>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-352">See <xref:fundamentals/configuration/index#json-configuration-provider>.</span></span>
* <span data-ttu-id="d6f6c-353">Umgebungsvariablen (in jedem System festgelegt, in dem die App gehostet wird).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-353">Environment variables (set on each system where the app is hosted).</span></span> <span data-ttu-id="d6f6c-354">Weitere Informationen finden Sie unter <xref:fundamentals/host/web-host#environment> und <xref:security/app-secrets#environment-variables>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-354">See <xref:fundamentals/host/web-host#environment> and <xref:security/app-secrets#environment-variables>.</span></span>
* <span data-ttu-id="d6f6c-355">Secret Manager (nur in der Entwicklungsumgebung)</span><span class="sxs-lookup"><span data-stu-id="d6f6c-355">Secret Manager (in the Development environment only).</span></span> <span data-ttu-id="d6f6c-356">Siehe <xref:security/app-secrets>.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-356">See <xref:security/app-secrets>.</span></span>

## <a name="environment-based-startup-class-and-methods"></a><span data-ttu-id="d6f6c-357">Umgebungsbasierte Startklasse und Methoden</span><span class="sxs-lookup"><span data-stu-id="d6f6c-357">Environment-based Startup class and methods</span></span>

### <a name="inject-ihostingenvironment-into-startupconfigure"></a><span data-ttu-id="d6f6c-358">Einfügen von IHostingEnvironment in Startup.Configure</span><span class="sxs-lookup"><span data-stu-id="d6f6c-358">Inject IHostingEnvironment into Startup.Configure</span></span>

<span data-ttu-id="d6f6c-359">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in `Startup.Configure` ein.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-359">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into `Startup.Configure`.</span></span> <span data-ttu-id="d6f6c-360">Diese Vorgehensweise ist nützlich, wenn die App `Startup.Configure` nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-360">This approach is useful when the app only requires configuring `Startup.Configure` for only a few environments with minimal code differences per environment.</span></span>

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

### <a name="inject-ihostingenvironment-into-the-startup-class"></a><span data-ttu-id="d6f6c-361">Einfügen von IHostingEnvironment in die Startklasse</span><span class="sxs-lookup"><span data-stu-id="d6f6c-361">Inject IHostingEnvironment into the Startup class</span></span>

<span data-ttu-id="d6f6c-362">Fügen Sie <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> in den `Startup`-Konstruktor ein, und weisen Sie den Dienst einem Feld zu, das in der gesamten `Startup`-Klasse verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-362">Inject <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> into the `Startup` constructor and assign the service to a field for use throughout the `Startup` class.</span></span> <span data-ttu-id="d6f6c-363">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ nur für einige Umgebungen mit minimalen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-363">This approach is useful when the app requires configuring startup for only a few environments with minimal code differences per environment.</span></span>

<span data-ttu-id="d6f6c-364">Im folgenden Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-364">In the following example:</span></span>

* <span data-ttu-id="d6f6c-365">Die Umgebung wird im Feld `_env` gespeichert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-365">The environment is held in the `_env` field.</span></span>
* <span data-ttu-id="d6f6c-366">`_env` wird in `ConfigureServices` und `Configure` verwendet, um die Startkonfiguration basierend auf der Umgebung der App anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-366">`_env` is used in `ConfigureServices` and `Configure` to apply startup configuration based on the app's environment.</span></span>

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

### <a name="startup-class-conventions"></a><span data-ttu-id="d6f6c-367">Konventionen der Startup-Klasse</span><span class="sxs-lookup"><span data-stu-id="d6f6c-367">Startup class conventions</span></span>

<span data-ttu-id="d6f6c-368">Nach dem Start einer ASP.NET Core-App lädt die [Startklasse](xref:fundamentals/startup) die App.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-368">When an ASP.NET Core app starts, the [Startup class](xref:fundamentals/startup) bootstraps the app.</span></span> <span data-ttu-id="d6f6c-369">Die App kann je nach Umgebung unterschiedliche `Startup`-Klassen definieren (z. B. `StartupDevelopment`).</span><span class="sxs-lookup"><span data-stu-id="d6f6c-369">The app can define separate `Startup` classes for different environments (for example, `StartupDevelopment`).</span></span> <span data-ttu-id="d6f6c-370">Die entsprechende `Startup`-Klasse wird zur Laufzeit ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-370">The appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="d6f6c-371">Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-371">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="d6f6c-372">Ist keine übereinstimmende `Startup{EnvironmentName}`-Klasse vorhanden, wird die Klasse `Startup` verwendet.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-372">If a matching `Startup{EnvironmentName}` class isn't found, the `Startup` class is used.</span></span> <span data-ttu-id="d6f6c-373">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-373">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

<span data-ttu-id="d6f6c-374">Wenn Sie umgebungsbasierte `Startup`-Klassen implementieren möchten, erstellen Sie für jedes verwendete Element eine `Startup{EnvironmentName}`-Klasse und eine Fallback-Klasse des Typs `Startup`:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-374">To implement environment-based `Startup` classes, create a `Startup{EnvironmentName}` class for each environment in use and a fallback `Startup` class:</span></span>

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

<span data-ttu-id="d6f6c-375">Verwenden Sie die Überladung [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), die einen Assemblynamen akzeptiert:</span><span class="sxs-lookup"><span data-stu-id="d6f6c-375">Use the [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup) overload that accepts an assembly name:</span></span>

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

### <a name="startup-method-conventions"></a><span data-ttu-id="d6f6c-376">Konventionen der Methode „Start“</span><span class="sxs-lookup"><span data-stu-id="d6f6c-376">Startup method conventions</span></span>

<span data-ttu-id="d6f6c-377">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) unterstützen umgebungsspezifische Versionen der Form `Configure<EnvironmentName>` und `Configure<EnvironmentName>Services`.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-377">[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) and [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) support environment-specific versions of the form `Configure<EnvironmentName>` and `Configure<EnvironmentName>Services`.</span></span> <span data-ttu-id="d6f6c-378">Diese Vorgehensweise ist nützlich, wenn die App „Startup“ für mehrere Umgebungen mit vielen Codeunterschieden pro Umgebung konfigurieren muss.</span><span class="sxs-lookup"><span data-stu-id="d6f6c-378">This approach is useful when the app requires configuring startup for several environments with many code differences per environment.</span></span>

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a><span data-ttu-id="d6f6c-379">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d6f6c-379">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>

::: moniker-end