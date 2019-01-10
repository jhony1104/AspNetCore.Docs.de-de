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
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core

Von [Sourabh Shirhatti](https://twitter.com/sshirhatti) und [Luke Latham](https://github.com/guardrex)

In diesem Artikel wird die Unterstützung für [Visual Studio](https://www.visualstudio.com/vs/) für das Debuggen von ASP.NET Core-Apps beschrieben, die hinter IIS unter Windows Server ausgeführt werden. Dieses Thema führt Sie durch die Aktivierung dieses Features und die Einrichtung eines Projekts.

## <a name="prerequisites"></a>Erforderliche Komponenten

* [Visual Studio für Windows](https://www.microsoft.com/net/download/windows)
* Workload **ASP.NET und Webentwicklung**
* Workload **Plattformübergreifende .NET Core-Entwicklung**
* X.509-Sicherheitszertifikat

## <a name="enable-iis"></a>Aktivieren von IIS

1. Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).
1. Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.

![Windows-Features, die das aktivierte Kontrollkästchen „Internetinformationsdienste“ als schwarzes Quadrat anzeigen (ohne Häkchen); dies deutet darauf hin, dass einige der IIS-Features aktiviert sind](development-time-iis-support/_static/enable_iis.png)

Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.

## <a name="configure-iis"></a>Konfigurieren von IIS

Für IIS muss eine Website mit Folgendem konfiguriert sein:

* Einem Hostnamen, der dem URL-Hostnamen des Startprofils der App entspricht.
* Einer Bindung für Port 443 mit zugewiesenem Zertifikat.

Beispiel: Der **Hostname** für eine hinzugefügte Website ist auf „localhost“ festgelegt (im Startprofil wird später in diesem Abschnitt auch „localhost“ verwendet). Der Port ist auf „443“ (HTTPS) festgelegt. Das **IIS Express-Entwicklungszertifikat** wird der Website zugewiesen, es kann jedoch jedes gültige Zertifikat eingesetzt werden:

![Fenster „Website hinzufügen“ in IIS, in dem der Bindungssatz für „localhost“ an Port 443 mit einem zugewiesenen Zertifikat dargestellt wird.](development-time-iis-support/_static/add-website-window.png)

Wenn die IIS-Installation bereits über eine **Standardwebsite** mit einem Hostnamen verfügt, der dem URL-Hostnamen des Startprofils der App entspricht:

* Fügen Sie eine Portbindung für Port 443 (HTTPS) hinzu.
* Weisen Sie der Website ein gültiges Zertifikat zu.

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit

1. Starten Sie den Visual Studio-Installer.
1. Wählen Sie die Komponente **IIS-Unterstützung zur Entwicklungszeit** aus. Die Komponente wird im Bereich **Zusammenfassung** für die Workload **ASP.NET und Webentwicklung** als optional aufgeführt. Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.

![Ändern von Visual Studio-Features: Die Registerkarte „Workloads“ wird ausgewählt. Im Bereich „Web und Cloud“ ist der Bereich „ASP.NET und Webentwicklung“ ausgewählt. Auf der rechten Seite des Bereichs „Optional“ im Bereich „Zusammenfassung“ befindet sich ein Kontrollkästchen für „IIS-Unterstützung zur Entwicklungszeit“.](development-time-iis-support/_static/development_time_support.png)

## <a name="configure-the-project"></a>Konfigurieren des Projekts

### <a name="https-redirection"></a>HTTPS-Umleitung

Aktivieren Sie für ein neues Projekt das Kontrollkästchen bei **Configure for HTTPS** (Für HTTPS konfigurieren) im Fenster **Neue ASP.NET Core-Webanwendung**:

![Fenster „Neue ASP.NET Core-Webanwendung“ mit aktiviertem Kontrollkästchen bei „Configure for HTTPS“ (Für HTTPS konfigurieren).](development-time-iis-support/_static/new-app.png)

Verwenden Sie in einem vorhandenen Projekt in `Startup.Configure` Middleware für HTTPS-Umleitung, indem Sie die Erweiterungsmethode [UseHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpspolicybuilderextensions.usehttpsredirection) aufrufen:

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

### <a name="iis-launch-profile"></a>IIS-Startprofil

So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen:

1. Klicken Sie für **Profil** auf die Schaltfläche **Neu**. Geben Sie dem Profil im Popupfenster den Namen „IIS“. Klicken Sie auf **OK**, um das Profil zu erstellen.
1. Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.
1. Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an. Verwenden Sie das HTTPS-Protokoll. In diesem Beispiel wird `https://localhost/WebApplication1` verwendet.
1. Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**. Geben Sie eine Umgebungsvariable mit einem `ASPNETCORE_ENVIRONMENT`-Schlüssel und einem Wert von `Development` an.
1. Legen Sie im Bereich **Webservereinstellungen** die **App-URL** fest. In diesem Beispiel wird `https://localhost/WebApplication1` verwendet.
1. Speichern Sie den Profilnamen.

![Fenster „Projekteigenschaften“, in dem die Registerkarte „Debuggen“ ausgewählt ist Die Profil- und Starteinstellungen werden auf IIS festgelegt. Das Feature „Browser starten“ wird mit der Adresse https://localhost/WebApplication1 aktiviert. Die gleiche Adresse wird auch im Feld „App-URL“ im Bereich „Webservereinstellungen“ angegeben.](development-time-iis-support/_static/project_properties.png)

Alternativ können Sie in der App manuell ein Startprofil zur Datei [launchSettings.json](http://json.schemastore.org/launchsettings) hinzufügen:

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

## <a name="run-the-project"></a>Ausführen des Projekts

In Visual Studio:

* Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.
* Legen Sie die Schaltfläche „Ausführen“ auf das Profil **IIS** fest, und klicken Sie zum Starten der App auf die Schaltfläche.

![Die Schaltfläche „Ausführen“ in der VS-Symbolleiste ist auf das IIS-Profil eingestellt, während die Dropdownliste für die Buildkonfiguration auf „Release“ gesetzt ist.](development-time-iis-support/_static/toolbar.png)

Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird. Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.

Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.

> [!NOTE]
> Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung. Beispielsweise werden Haltepunkte nicht erreicht.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Hosten von ASP.NET Core unter Windows mit IIS](xref:host-and-deploy/iis/index)
* [Einführung in das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module)
* [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module)
* [Erzwingen von HTTPS](xref:security/enforcing-ssl)
