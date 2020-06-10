---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: 06c215156cdfa9bc1ae6ac2eb21a3f739a309bed
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106779"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>'Identity'

'Let's Encrypt'

::: moniker range=">= aspnetcore-3.0"

'Razor' 'SignalR' uid:

## <a name="prerequisites"></a>IIS-Support zum Zeitpunkt der Entwicklung in Visual Studio für ASP.NET Core

* Von [Sourabh Shirhatti](https://twitter.com/sshirhatti)
* In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden.
* Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.
* Voraussetzungen

## <a name="enable-iis"></a>[Visual Studio für Windows](https://visualstudio.microsoft.com/downloads/)

1. Workload **ASP.NET und Webentwicklung**
1. Workload **Plattformübergreifende .NET Core-Entwicklung** X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)

Aktivieren von IIS

## <a name="configure-iis"></a>Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).

Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.

* Klicken Sie auf **OK**. Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.
* Konfigurieren von IIS
  * Für IIS muss eine Website mit Folgendem konfiguriert sein: **Hostname:** In der Regel wird die **Standardwebsite** mit dem **Hostnamen** `localhost` verwendet.
  * Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.
  * **Sitebindung** Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443.

## <a name="enable-development-time-iis-support-in-visual-studio"></a>In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.

1. Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.
1. Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS.
1. **Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**

   Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit Starten Sie den Visual Studio-Installer.

## <a name="configure-the-project"></a>Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.

### <a name="https-redirection"></a>Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.

Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads. Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.

Konfigurieren des Projekts HTTPS-Umleitung

Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren). Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.

### <a name="iis-launch-profile"></a>Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`.

Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.

1. Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt. Es ist keine App-Konfiguration erforderlich. IIS-Startprofil
1. So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen: Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Eigenschaften**.
1. Öffnen Sie die Registerkarte **Debuggen**.
1. Klicken Sie für **Profil** auf die Schaltfläche **Neu**.

   Geben Sie dem Profil im Popupfenster den Namen „IIS“. Klicken Sie auf **OK**, um das Profil zu erstellen.

   Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.

   Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.

   Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).
1. Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`). Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.
1. Geben Sie den Namen der App am Ende der URL an.
1. `https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs. Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**. Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an. Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.
1. Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.

Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet. Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (In-Process) verwendet.

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

Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.

## <a name="run-the-project"></a>Speichern Sie den Profilnamen.

Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen.

* Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:
* Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.

Ausführen des Projekts Führen Sie Visual Studio als Administrator aus:

Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.

> [!NOTE]
> Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus. Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.

## <a name="additional-resources"></a>Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.

* Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Das Debuggen einer Releasebuildkonfiguration mit [Nur mein Code](/visualstudio/debugger/just-my-code) und Compileroptimierungen führt zu einer beeinträchtigten Leistung. Beispielsweise werden Haltepunkte nicht erreicht.

## <a name="prerequisites"></a>Zusätzliche Ressourcen

* [Erste Schritte mit IIS-Manager in IIS](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* In diesem Artikel wird die [Visual Studio](https://visualstudio.microsoft.com)-Unterstützung des Debuggens von ASP.NET Core-Apps beschrieben, die mit IIS unter Windows Server ausgeführt werden.
* Dieses Thema führt Sie durch die Aktivierung dieses Szenarios und Einrichtung eines Projekts.
* Voraussetzungen

## <a name="enable-iis"></a>[Visual Studio für Windows](https://visualstudio.microsoft.com/downloads/)

1. Workload **ASP.NET und Webentwicklung**
1. Workload **Plattformübergreifende .NET Core-Entwicklung** X.509-Sicherheitszertifikat (für HTTPS-Unterstützung)

Aktivieren von IIS

## <a name="configure-iis"></a>Navigieren Sie in Windows zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).

Aktivieren Sie das Kontrollkästchen **Internetinformationsdienste**.

* Klicken Sie auf **OK**. Für die IIS-Installation ist möglicherweise ein Neustart des Systems erforderlich.
* Konfigurieren von IIS
  * Für IIS muss eine Website mit Folgendem konfiguriert sein: **Hostname:** In der Regel wird die **Standardwebsite** mit dem **Hostnamen** `localhost` verwendet.
  * Allerdings funktioniert jede gültige IIS-Website mit einem eindeutigen Hostnamen.
  * **Sitebindung** Für Anwendungen, die HTTPS benötigen, erstellen Sie mithilfe eines Zertifikats eine Bindung an Port 443.

## <a name="enable-development-time-iis-support-in-visual-studio"></a>In der Regel wird das **IIS Express-Entwicklungszertifikat** verwendet, doch es funktioniert auch jedes andere gültige Zertifikat.

1. Bei Apps, die HTTP verwenden, bestätigen Sie das Vorhandensein einer Bindung an Port 80, oder erstellen Sie für eine neue Website eine Bindung an Port 80.
1. Verwenden Sie eine einzelne Bindung für entweder HTTP oder HTTPS.
1. **Die gleichzeitige Bindung sowohl an HTTP- als auch an HTTPS-Ports wird nicht unterstützt.**

   Aktivieren von IIS-Unterstützung in Visual Studio zur Entwicklungszeit Starten Sie den Visual Studio-Installer.

## <a name="configure-the-project"></a>Klicken Sie für die Visual Studio-Installation, die Sie für die Unterstützung von IIS-Entwicklungszeit verwenden möchten, auf **Ändern**.

### <a name="https-redirection"></a>Wechseln Sie für die Workload **ASP.NET- und Webentwicklung** zur Komponente **Entwicklungszeit-IIS-Unterstützung** , und installieren Sie sie.

Die Komponente befindet sich im Abschnitt **Optional** unter **Entwicklungszeit-IIS-Unterstützung** im Bereich **Installationsdetails** rechts neben den Workloads. Sie installiert das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) – ein natives IIS-Modul, das zur Ausführung von ASP.NET Core-Anwendungen mit IIS erforderlich ist.

Konfigurieren des Projekts HTTPS-Umleitung

Aktivieren Sie für ein neues Projekt, das HTTPS erfordert, im Fenster **Neue ASP.NET Core-Webanwendung** das Kontrollkästchen neben **Configure for HTTPS** (Für HTTPS konfigurieren). Bei Aktivieren des Kontrollkästchens werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) der App bei ihrer Erstellung hinzugefügt.

### <a name="iis-launch-profile"></a>Verwenden Sie für ein vorhandenes Projekt, das HTTPS erfordert, die HTTPS-Umleitung und HSTS-Middleware in `Startup.Configure`.

Weitere Informationen finden Sie unter <xref:security/enforcing-ssl>.

1. Für ein Projekt, das HTTP verwendet, werden die [HTTPS-Umleitung und HSTS-Middleware](xref:security/enforcing-ssl) nicht der App hinzugefügt. Es ist keine App-Konfiguration erforderlich. IIS-Startprofil
1. So erstellen Sie ein neues Startprofil, um IIS-Unterstützung zur Entwicklungszeit hinzuzufügen: Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt. Klicken Sie auf **Eigenschaften**.
1. Öffnen Sie die Registerkarte **Debuggen**.
1. Klicken Sie für **Profil** auf die Schaltfläche **Neu**.

   Geben Sie dem Profil im Popupfenster den Namen „IIS“. Klicken Sie auf **OK**, um das Profil zu erstellen.

   Wählen Sie bei der Einstellung **Start** den Eintrag **IIS** aus der Liste aus.

   Aktivieren Sie das Kontrollkästchen bei **Browser starten**, und geben Sie die Endpunkt-URL an.

   Wenn die App HTTPS erfordert, verwenden Sie einen HTTPS-Endpunkt (`https://`).
1. Verwenden Sie für HTTP einen HTTP-Endpunkt (`http://`). Geben Sie den gleichen Hostnamen und Port wie bei der [zuvor angegebenen IIS-Konfiguration](#configure-iis) an, typischerweise `localhost`.
1. Geben Sie den Namen der App am Ende der URL an.
1. `https://localhost/WebApplication1` (HTTPS) oder `http://localhost/WebApplication1` (HTTP) sind beispielsweise gültige Endpunkt-URLs. Klicken Sie im Abschnitt **Umgebungsvariablen** auf die Schaltfläche **Hinzufügen**. Geben Sie eine Umgebungsvariable mit dem **Namen**`ASPNETCORE_ENVIRONMENT` und dem **Wert**`Development` an. Legen Sie im Bereich **Webservereinstellungen** die **App-URL** auf den gleichen Wert fest, der für die Endpunkt-URL für **Browser starten** verwendet wird.
1. Wählen Sie für die Einstellung **Hostingmodel** in Visual Studio 2019 oder höher **Standard**, um das vom Projekt verwendete Hostingmodell zu nutzen.

Wenn das Projekt die `<AspNetCoreHostingModel>`-Eigenschaft in seiner Projektdatei festlegt, wird der Wert der Eigenschaft (`InProcess` oder `OutOfProcess`) verwendet. Wenn die Eigenschaft nicht vorhanden ist, wird das standardmäßige Hostingmodell der App (Out-of-Process) verwendet.

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

Wenn die App eine explizite Einstellung des Hostingmodells erfordert, die sich vom normalen Hostingmodell der App unterscheidet, legen Sie das **Hostingmodell** nach Bedarf entweder auf `In Process` oder `Out Of Process` fest.

## <a name="run-the-project"></a>Speichern Sie den Profilnamen.

Wenn Sie nicht Visual Studio verwenden, können Sie in der App im Ordner *Properties* manuell ein Startprofil zur Datei [launchSettings.json](https://json.schemastore.org/launchsettings) hinzufügen.

* Im folgenden Beispiel wird das Profil für die Verwendung des HTTPS-Protokolls konfiguriert:
* Vergewissern Sie sich, dass die Endpunkte `applicationUrl` und `launchUrl` übereinstimmen, und verwenden Sie das gleiche Protokoll wie die IIS-Bindungskonfiguration, entweder HTTP oder HTTPS.

Ausführen des Projekts Führen Sie Visual Studio als Administrator aus:

Vergewissern Sie sich, dass die Dropdownliste der Buildkonfiguration auf **Debuggen** festgelegt ist.

> [!NOTE]
> Legen Sie die [Schaltfläche „Debugging starten“](/visualstudio/debugger/debugger-feature-tour) auf das Profil **IIS** fest, und wählen Sie zum Starten der App die Schaltfläche aus. Visual Studio fordert möglicherweise zu einem Neustart auf, wenn das Profil nicht als Administrator ausgeführt wird.

## <a name="additional-resources"></a>Wenn Sie dazu aufgefordert werden, starten Sie Visual Studio neu.

* Bei Verwendung eines nicht vertrauenswürdigen Entwicklungszertifikats müssen Sie für den Browser möglicherweise eine Ausnahme für das nicht vertrauenswürdige Zertifikat erstellen.
* <xref:security/enforcing-ssl>

::: moniker-end
