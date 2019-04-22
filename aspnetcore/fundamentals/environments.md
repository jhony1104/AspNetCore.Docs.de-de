---
title: Verwenden von mehreren Umgebungen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie in ASP.NET Core-Apps das App-Verhalten umgebungsübergreifend steuern können.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/10/2019
uid: fundamentals/environments
ms.openlocfilehash: f14c29030d96b530ea4af054cfa195d44db43679
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516351"
---
# <a name="use-multiple-environments-in-aspnet-core"></a>Verwenden von mehreren Umgebungen in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core konfiguriert das App-Verhalten basierend auf der Laufzeitumgebung mit einer Umgebungsvariablen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/environments/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="environments"></a>Umgebungen

ASP.NET Core liest die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` beim Start der App und speichert diesen Wert unter [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname). Sie können `ASPNETCORE_ENVIRONMENT` auf einen beliebigen Wert festlegen, das Framework unterstützt allerdings nur [drei Werte](/dotnet/api/microsoft.aspnetcore.hosting.environmentname): [Entwicklung](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.development), [Staging](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.staging) und [Produktion](/dotnet/api/microsoft.aspnetcore.hosting.environmentname.production). Wenn `ASPNETCORE_ENVIRONMENT` nicht festgelegt ist, ist der Standardwert `Production`.

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet)]

Der vorangehende Code:

* Ruft [UseDeveloperExceptionPage](/dotnet/api/microsoft.aspnetcore.builder.developerexceptionpageextensions.usedeveloperexceptionpage) auf, wenn `ASPNETCORE_ENVIRONMENT` auf `Development` festgelegt ist.
* Ruft [UseExceptionHandler](/dotnet/api/microsoft.aspnetcore.builder.exceptionhandlerextensions.useexceptionhandler) auf, wenn der Wert von `ASPNETCORE_ENVIRONMENT` auf einen der folgenden Werte festgelegt ist:

  * `Staging`
  * `Production`
  * `Staging_2`

Das [Umgebungstaghilfsprogramm](xref:mvc/views/tag-helpers/builtin-th/environment-tag-helper) verwendet den Wert von `IHostingEnvironment.EnvironmentName` zum Einschließen oder Ausschließen von Markup im Element:

[!code-cshtml[](environments/sample-snapshot/EnvironmentsSample/Pages/About.cshtml)]

Unter Windows und macOS wird bei Umgebungsvariablen und Werten die Groß-/Kleinschreibung nicht beachtet. Bei Linux-Umgebungsvariablen und -Werten **wird die Groß-/Kleinschreibung standardmäßig beachtet**.

### <a name="development"></a>Entwicklung

In der Entwicklungsumgebung können Features aktiviert werden, die in der Produktion nicht verfügbar gemacht werden sollten. Mit den ASP.NET Core-Vorlagen wird beispielsweise die [Seite mit Ausnahmen für Entwickler](xref:fundamentals/error-handling#developer-exception-page) in der Entwicklungsumgebung aktiviert.

Die Umgebung für die Entwicklung lokaler Computer kann in der Datei *Properties\launchSettings.json* des Projekts festgelegt werden. Mit in der Datei *launchSettings.json* festgelegten Umgebungsvariablen werden in der Systemumgebung festgelegte Werte überschrieben.

Die folgende JSON zeigt drei Profile aus der Datei *launchSettings.json* an:

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
> Die Eigenschaft `applicationUrl` in *launchSettings.json* kann eine Liste von Server-URLs angeben. Verwenden Sie ein Semikolon zwischen den URLs in der Liste:
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

Wenn die Anwendung mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird, wird das erste Profil mit `"commandName": "Project"` verwendet. Der Wert von `commandName` gibt den zu startenden Webserver an. `commandName` kann einer der folgenden sein:

* `IISExpress`
* `IIS`
* `Project` (über das Kestrel gestartet wird)

Beim Start einer App mit [dotnet run](/dotnet/core/tools/dotnet-run) tritt Folgendes ein:

* Die Datei *launchSettings.json* wird, sofern verfügbar, gelesen. Durch `environmentVariables`-Einstellungen in der Datei *launchSettings.json* werden Umgebungsvariablen überschrieben.
* Die Hostingumgebung wird angezeigt.

Die folgende Ausgabe zeigt eine App, die mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wurde:

```bash
PS C:\Websites\EnvironmentsSample> dotnet run
Using launch settings from C:\Websites\EnvironmentsSample\Properties\launchSettings.json...
Hosting environment: Staging
Content root path: C:\Websites\EnvironmentsSample
Now listening on: http://localhost:54340
Application started. Press Ctrl+C to shut down.
```

Auf der Registerkarte **Debuggen** der Visual Studio-Projekteigenschaften wird eine grafische Benutzeroberfläche für die Bearbeitung der Datei *launchSettings.json* bereitgestellt:

![Projekteigenschaften zum Festlegen von Umgebungsvariablen](environments/_static/project-properties-debug.png)

An Projektprofilen vorgenommene Änderungen werden möglicherweise erst nach einem Neustart des Webservers wirksam. Kestrel muss neu gestartet werden, bevor es an der Umgebung vorgenommene Änderungen erkennen kann.

> [!WARNING]
> In der Datei *launchSettings.json* sollten keine geheimen Schlüssel gespeichert werden. Mit dem [Secret Manager-Tool](xref:security/app-secrets) können geheime Schlüssel für die lokale Umgebung gespeichert werden.

Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) verwenden, können Umgebungsvariablen in der Datei *.vscode/launch.json* festgelegt werden. Im folgenden Beispiel wird die Umgebung auf `Development` festgelegt:

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

Die Datei *.vscode/launch.json* im Projekt wird nicht gelesen, wenn die App mit `dotnet run` genauso wie *Properties/launchSettings.json* gestartet wird. Wenn Sie eine App in der Entwicklung starten, die keine *launchSettings.json*-Datei enthält, legen Sie die Umgebung mit einer Umgebungsvariablen oder einem Befehlszeilenargument auf den `dotnet run`-Befehl fest.

### <a name="production"></a>Produktion

Die Produktionsumgebung sollte so konfiguriert werden, dass Sicherheit, Leistung und Stabilität der App maximiert werden. Allgemeine Einstellungen, die sich von der Entwicklung unterscheiden, sind zum Beispiel:

* Zwischenspeicherung.
* Clientseitige Ressourcen werden gebündelt, verkleinert und potenziell von einem CDN bedient.
* Seiten zur Fehlerdiagnose sind deaktiviert.
* Angezeigte Fehlerseiten sind aktiviert.
* Die Produktionsprotokollierung und -überwachung ist aktiviert. Beispiel: [Application Insights](/azure/application-insights/app-insights-asp-net-core).

## <a name="set-the-environment"></a>Festlegen der Umgebung

Es ist oft nützlich, zu Testzwecken eine bestimmte Umgebung festzulegen. Wenn die Umgebung nicht festgelegt ist, wird `Production` als Standardwert verwendet, wodurch die meisten Debugfeatures deaktiviert werden. Welche Methode zum Festlegen der Umgebung verwendet wird, hängt vom Betriebssystem ab.

### <a name="azure-app-service"></a>Azure App Service

Führen Sie die folgenden Schritte durch, um die Umgebung in [Azure App Service](https://azure.microsoft.com/services/app-service/) festzulegen:

1. Wählen Sie die App auf dem Blatt **App Services** aus.
1. Wählen Sie in der Gruppe **EINSTELLUNGEN** das Blatt **Anwendungseinstellung** aus.
1. Wählen Sie im Bereich **Anwendungseinstellung** **Neue Einstellung hinzufügen** aus.
1. Für **Geben Sie einen Namen**, bieten `ASPNETCORE_ENVIRONMENT`. Für **Geben Sie einen Wert**, geben Sie die Umgebung (z. B. `Staging`).
1. Aktivieren Sie das Kontrollkästchen **Sloteinstellung**, wenn Sie möchten, dass die Umgebungseinstellung im aktuellen Slot bleibt, wenn Bereitstellungsslots getauscht werden. Weitere Informationen finden Sie in der [Azure-Dokumentation: Welche Einstellungen werden ausgetauscht?](/azure/app-service/web-sites-staged-publishing).
1. Klicken Sie oben auf dem Blatt auf **Speichern**.

Azure App Service startet die App automatisch neu, nachdem eine App-Einstellung (Umgebungsvariable) im Azure-Portal hinzugefügt, geändert oder gelöscht wurde.

### <a name="windows"></a>Windows

Zum Festlegen der `ASPNETCORE_ENVIRONMENT` für die aktuelle Sitzung werden folgende Befehle verwendet, wenn die App mit [dotnet run](/dotnet/core/tools/dotnet-run) gestartet wird:

**Eingabeaufforderung**

```console
set ASPNETCORE_ENVIRONMENT=Development
```

**PowerShell**

```powershell
$Env:ASPNETCORE_ENVIRONMENT = "Development"
```

Diese Befehle sind nur für das aktuelle Fenster wirksam. Wenn das Fenster geschlossen wird, wird die Einstellung `ASPNETCORE_ENVIRONMENT` auf die Standardeinstellung oder den Computerwert zurückgesetzt.

Wenn Sie den Wert in Windows global festlegen möchten, nutzen Sie eine der folgenden Möglichkeiten:

* Öffnen Sie **Systemsteuerung** > **System** > **Erweiterte Systemeinstellungen**, und fügen Sie den Wert `ASPNETCORE_ENVIRONMENT` hinzu, oder bearbeiten Sie ihn:

  ![Erweiterte Systemeigenschaften](environments/_static/systemsetting_environment.png)

  ![ASP.NET Core-Umgebungsvariable](environments/_static/windows_aspnetcore_environment.png)

* Führen Sie die Eingabeaufforderung als Administrator aus, und verwenden Sie den Befehl `setx`. Alternativ können Sie auch die PowerShell-Eingabeaufforderung als Administrator ausführen und `[Environment]::SetEnvironmentVariable` nutzen:

  **Eingabeaufforderung**

  ```console
  setx ASPNETCORE_ENVIRONMENT Development /M
  ```

  Mit dem Parameter `/M` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird. Ohne den Parameter `/M` wird die Umgebungsvariable für das Nutzerkonto festgelegt.

  **PowerShell**

  ```powershell
  [Environment]::SetEnvironmentVariable("ASPNETCORE_ENVIRONMENT", "Development", "Machine")
  ```

  Mit dem Optionswert `Machine` wird angegeben, dass die Umgebungsvariable auf Systemebene festgelegt wird. Verwenden Sie stattdessen den Optionswert `User`, wird die Umgebungsvariable für das Nutzerkonto festgelegt.

Sobald die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` global festgelegt wird, gilt sie in jedem geöffneten Befehlsfenster für `dotnet run`.

**web.config**

Eine Anleitung zum Festlegen der Umgebungsvariable `ASPNETCORE_ENVIRONMENT` mit *web.config* finden Sie im Abschnitt *Festlegen von Umgebungsvariablen* der im Artikel zu <xref:host-and-deploy/aspnet-core-module#setting-environment-variables>.

::: moniker range=">= aspnetcore-2.2"

**Projektdatei oder Veröffentlichungsprofil**

**Für Windows IIS-Bereitstellungen**: Beziehen Sie die `<EnvironmentName>`-Eigenschaft in das Veröffentlichungsprofil (*PUBXML*) oder die Projektdatei ein. Dieser Ansatz legt die Umgebung in *web.config* fest, wenn das Projekt veröffentlicht wird:

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

::: moniker-end

**Pro IIS-Anwendungspool**

Wenn Sie für eine App, die in einem isolierten Anwendungspool ausgeführt wird, die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` festlegen möchten (unterstützt in IIS 10.0 oder höher), lesen Sie den Abschnitt *AppCmd.exe command (Befehl „AppCmd.exe“)* im Artikel zu [Umgebungsvariablen&lt;environmentVariables&gt;](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe). Wurde die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` für einen Anwendungspool festgelegt, überschreibt der Wert die Einstellung auf Systemebene.

> [!IMPORTANT]
> Wenn Sie eine APP in den IIS hosten und die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` hinzufügen oder ändern, sorgen Sie auf eine der folgenden Arten und Weisen dafür, dass der neue Wert in den Apps hinterlegt ist:
>
> * Führen Sie in einer Eingabeaufforderung `net stop was /y` gefolgt von `net start w3svc` aus.
> * Starten Sie den Server neu.

### <a name="macos"></a>macOS

Das Festlegen der aktuellen Umgebung für macOS kann inline während der Ausführung der App erfolgen:

```bash
ASPNETCORE_ENVIRONMENT=Development dotnet run
```

Legen Sie die Umgebung alternativ mit `export` vor der Ausführung der App fest:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

Umgebungsvariablen auf Computerebene werden in der *BASHRC*- oder *BASH_PROFILE*-Datei festgelegt. Bearbeiten Sie die Datei mit einem beliebigen Text-Editor. Fügen Sie die folgende Anweisung hinzu:

```bash
export ASPNETCORE_ENVIRONMENT=Development
```

### <a name="linux"></a>Linux

Verwenden Sie bei Linux-Distributionen für sitzungsbasierte Variableneinstellungen den Befehl `export` in der Befehlszeile und die *BASH-PROFILE*-Datei für Umgebungseinstellungen auf Computerebene.

### <a name="configuration-by-environment"></a>Konfiguration nach Umgebung

Zum Laden von „Konfiguration nach Umgebung“ empfehlen wir:

* *appsettings*-Dateien (*appsettings.\<Umgebung>.json*). Weitere Informationen finden Sie unter [Konfiguration: Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider).
* Umgebungsvariablen (in jedem System festgelegt, in dem die App gehostet wird) Weitere Informationen finden Sie unter [Konfiguration: Dateikonfigurationsanbieter](xref:fundamentals/configuration/index#file-configuration-provider) und [Sicheres Speichern geheimer App-Schlüssel in der Entwicklung: Umgebungsvariablen](xref:security/app-secrets#environment-variables).
* Secret Manager (nur in der Entwicklungsumgebung) Siehe <xref:security/app-secrets>.

## <a name="environment-based-startup-class-and-methods"></a>Umgebungsbasierte Startklasse und Methoden

### <a name="startup-class-conventions"></a>Konventionen der Startup-Klasse

Nach dem Start einer ASP.NET Core-App lädt die [Startklasse](xref:fundamentals/startup) die App. Die App kann je nach Umgebung unterschiedliche `Startup`-Klassen definieren (z.B. `StartupDevelopment`). Zur Laufzeit wird dann die passende `Startup`-Klasse ausgewählt. Die Klasse, deren Namenssuffix mit der aktuellen Umgebung übereinstimmt, wird priorisiert. Ist keine übereinstimmende `Startup{EnvironmentName}`-Klasse vorhanden, wird die Klasse `Startup` verwendet.

Wenn Sie umgebungsbasierte `Startup`-Klassen implementieren möchten, erstellen Sie für jedes verwendete Element eine `Startup{EnvironmentName}`-Klasse und eine Fallback-Klasse des Typs `Startup`:

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

Verwenden Sie die Überladung [UseStartup(IWebHostBuilder, String)](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.usestartup), die einen Assemblynamen akzeptiert:

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

### <a name="startup-method-conventions"></a>Konventionen der Methode „Start“

[Configure](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configure) und [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) unterstützen umgebungsspezifische Versionen der Form `Configure<EnvironmentName>` und `Configure<EnvironmentName>Services`:

[!code-csharp[](environments/sample/EnvironmentsSample/Startup.cs?name=snippet_all&highlight=15,42)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/startup>
* <xref:fundamentals/configuration/index>
* [IHostingEnvironment.EnvironmentName](/dotnet/api/microsoft.aspnetcore.hosting.ihostingenvironment.environmentname)
