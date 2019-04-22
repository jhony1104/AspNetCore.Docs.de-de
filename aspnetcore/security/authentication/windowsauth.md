---
title: Konfigurieren der Windows-Authentifizierung in ASP.NET Core
author: scottaddie
description: Informationen Sie zum Konfigurieren der Windows-Authentifizierung in ASP.NET Core mit IIS Express, IIS und HTTP.sys.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 04/03/2019
uid: security/authentication/windowsauth
ms.openlocfilehash: bd4ffa79c4d1e0070c820fa9c06b0a84c3aaae74
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59468652"
---
# <a name="configure-windows-authentication-in-aspnet-core"></a>Konfigurieren der Windows-Authentifizierung in ASP.NET Core

Durch [Scott Addie](https://twitter.com/Scott_Addie) und [Luke Latham](https://github.com/guardrex)

[Windows-Authentifizierung](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) kann konfiguriert werden, für ASP.NET Core-apps mit gehosteten [IIS](xref:host-and-deploy/iis/index) oder [HTTP.sys](xref:fundamentals/servers/httpsys).

Windows-Authentifizierung basiert auf dem Betriebssystem zur Authentifizierung von Benutzern von ASP.NET Core-apps. Sie können Windows-Authentifizierung verwenden, wenn Ihre Server in einem Unternehmensnetzwerk, die mithilfe von Active Directory-domänenidentitäten oder Windows-Konten zur Identifizierung von Benutzern ausgeführt wird. Windows-Authentifizierung ist am besten für Intranetumgebungen, in dem Benutzer, Client-apps und Webserver mit der gleichen Windows-Domäne gehören.

## <a name="enable-windows-authentication-in-an-aspnet-core-app"></a>Aktivieren der Windows-Authentifizierung in ASP.NET Core-Apps

Die **Webanwendung** Vorlage zur Verfügung, über Visual Studio oder .NET Core-CLI kann konfiguriert werden, um die Windows-Authentifizierung unterstützen.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="use-the-windows-authentication-app-template-for-a-new-project"></a>Verwenden Sie die Windows-Authentifizierung-app-Vorlage für ein neues Projekt

In Visual Studio:

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **ASP.NET Core-Webanwendung** aus. Klicken Sie auf **Weiter**.
1. Geben Sie einen Namen in der **Projektname** Feld. Vergewissern Sie sich die **Speicherort** Eintrag richtig ist, oder geben Sie einen Speicherort für das Projekt. Wählen Sie **Erstellen** aus.
1. Wählen Sie **Änderung** unter **Authentifizierung**.
1. In der **Authentifizierung ändern** wählen Sie im Fenster **Windows-Authentifizierung**. Klicken Sie auf **OK**.
1. Wählen Sie **Webanwendung** aus.
1. Wählen Sie **Erstellen** aus.

Führen Sie die App aus. Der Benutzername wird in der gerenderten app-Benutzeroberfläche angezeigt.

### <a name="manual-configuration-for-an-existing-project"></a>Manuelle Konfiguration für ein vorhandenes Projekt

Die Eigenschaften des Projekts können Sie Windows-Authentifizierung aktivieren und deaktivieren Sie die anonyme Authentifizierung:

1. Mit der rechten Maustaste in des Projekts in Visual Studio **Projektmappen-Explorer** , und wählen Sie **Eigenschaften**.
1. Klicken Sie auf die Registerkarte **Debuggen**.
1. Deaktivieren Sie das Kontrollkästchen für **Aktivieren der anonymen Authentifizierung**.
1. Wählen Sie das Kontrollkästchen für **Windows-Authentifizierung aktivieren**.

Alternativ können die Eigenschaften konfiguriert werden, der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Verwenden der **Windows-Authentifizierung** -app-Vorlage.

Führen Sie die [Dotnet neue](/dotnet/core/tools/dotnet-new) -Befehl mit der `webapp` Argument (ASP.NET Core-Web-App) und `--auth Windows` wechseln:

```console
dotnet new webapp --auth Windows
```

---

Wenn Sie ein vorhandenes Projekt ändern möchten, vergewissern Sie sich, dass die Projektdatei für die Paketverweise enthält die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) **oder** der [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet-Paket.

## <a name="enable-windows-authentication-with-iis"></a>Aktivieren der Windows-Authentifizierung mit IIS

IIS verwendet den [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zum Hosten von ASP.NET Core-apps. Windows-Authentifizierung konfiguriert ist, für IIS über die *"Web.config"* Datei. Die folgenden Abschnitte zeigen Gewusst wie:

* Geben Sie eine lokale *"Web.config"* -Datei, die Windows-Authentifizierung auf dem Server aktiviert, wenn die app bereitgestellt wird.
* Verwenden Sie den IIS-Manager so konfigurieren Sie die *"Web.config"* Datei von einer ASP.NET Core-app, die bereits auf dem Server bereitgestellt wurde.

### <a name="iis-configuration"></a>IIS-Konfiguration

Wenn Sie nicht bereits geschehen, aktivieren Sie IIS zum Hosten von ASP.NET Core-apps. Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index>.

Den Rollendienst "IIS" für Windows-Authentifizierung zu aktivieren. Weitere Informationen finden Sie unter [Aktivieren der Windows-Authentifizierung in IIS-Rollendienste (Siehe Schritt2)](xref:host-and-deploy/iis/index#iis-configuration).

Middleware für die Integration von IIS ist standardmäßig konfiguriert, um Anforderungen zu authentifizieren. Weitere Informationen finden Sie unter [Hosten von ASP.NET Core unter Windows mit IIS: IIS-Optionen (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

ASP.NET Core-Modul ist zum Weiterleiten von der Windows-Authentifizierung-Token für die app wird standardmäßig konfiguriert. Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul: Attribute des-Elements AspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

### <a name="create-a-new-iis-site"></a>Erstellen einer neuen IIS-Website

Geben Sie einen Namen und den Ordner, und erlauben Sie, dass Sie einen neuen Anwendungspool zu erstellen.

### <a name="enable-windows-authentication-for-the-app-in-iis"></a>Aktivieren der Windows-Authentifizierung für die app in IIS

Verwendung **entweder** der folgenden Methoden:

* [Development-Seite-Konfiguration vor dem Veröffentlichen der Anwendung](#development-side-configuration-with-a-local-webconfig-file) (*empfohlen*)
* [Serverseitige Konfiguration nach dem Veröffentlichen der app](#server-side-configuration-with-the-iis-manager)

#### <a name="development-side-configuration-with-a-local-webconfig-file"></a>Development-Seite-Konfiguration mit einer lokalen Datei "Web.config"-Datei

Die folgenden Schritte aus **vor** Sie [veröffentlichen und Bereitstellen des Projekts](#publish-and-deploy-your-project-to-the-iis-site-folder).

Fügen Sie die folgenden *"Web.config"* Datei in das Stammverzeichnis des Projekts:

[!code-xml[](windowsauth/sample_snapshot/web_2.config)]

Wenn das Projekt veröffentlicht wird, durch das SDK (ohne die `<IsTransformWebConfigDisabled>` -Eigenschaft auf festgelegt `true` in der Projektdatei), die veröffentlichte *"Web.config"* -Datei enthält die `<location><system.webServer><security><authentication>` Abschnitt. Weitere Informationen zu den `<IsTransformWebConfigDisabled>` -Eigenschaft finden Sie unter <xref:host-and-deploy/iis/index#webconfig-file>.

#### <a name="server-side-configuration-with-the-iis-manager"></a>Serverseitige Konfiguration mit dem IIS-Manager

Die folgenden Schritte aus **nach** Sie [veröffentlichen und Bereitstellen des Projekts](#publish-and-deploy-your-project-to-the-iis-site-folder).

1. Im IIS-Manager, wählen Sie die IIS-Website unter der **Websites** Knoten die **Verbindungen** Randleiste.
1. Doppelklicken Sie auf **Authentifizierung** in die **IIS** Bereich.
1. Wählen Sie **anonyme Authentifizierung**. Wählen Sie **deaktivieren** in die **Aktionen** Randleiste.
1. Wählen Sie **Windows-Authentifizierung**. Wählen Sie **aktivieren** in die **Aktionen** Randleiste.

Wenn diese Aktionen ausgeführt werden, ändert der IIS-Manager der app *"Web.config"* Datei. Ein `<system.webServer><security><authentication>` Knoten wird hinzugefügt, mit aktualisierten Einstellungen für `anonymousAuthentication` und `windowsAuthentication`:

[!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

Die `<system.webServer>` Abschnitts in der *"Web.config"* Datei vom IIS-Manager befindet sich außerhalb der app `<location>` Abschnitt durch die .NET Core SDK hinzugefügt wird, wenn die Anwendung veröffentlicht wurde. Da der Abschnitt, außerhalb von hinzugefügt wird den `<location>` Knoten werden die Einstellungen von einem geerbt [untergeordnete apps, die](xref:host-and-deploy/iis/index#sub-applications) der aktuellen App. Um die Vererbung zu verhindern, verschieben Sie die hinzugefügte `<security>` Abschnitt in der die `<location><system.webServer>` Abschnitt, in dem das SDK zur Verfügung.

Wenn IIS-Manager verwendet wird, um die IIS-Konfiguration hinzuzufügen, es wirkt sich nur auf der app *"Web.config"* Datei auf dem Server. Eine anschließende Bereitstellung der app möglicherweise die Einstellungen auf dem Server überschreiben, wenn die Kopie des Servers der *"Web.config"* durch des Projekts ersetzt *"Web.config"* Datei. Verwendung **entweder** der folgenden Methoden zum Verwalten der Einstellungen:

* Verwenden Sie IIS-Manager zum Zurücksetzen der Einstellungen in der *"Web.config"* -Datei nach der Bereitstellung die Datei überschrieben wird.
* Hinzufügen einer *Datei "Web.config"* an die app lokal mit den Einstellungen. Weitere Informationen finden Sie unter den [Development-Seite-Konfiguration](#development-side-configuration-with-a-local-webconfig-file) Abschnitt.

### <a name="publish-and-deploy-your-project-to-the-iis-site-folder"></a>Veröffentlichen Sie und Bereitstellen Sie des Projekts in der IIS-Site-Ordner

Verwenden Visual Studio oder .NET Core-CLI, veröffentlichen und Bereitstellen der app in den Zielordner.

Weitere Informationen zum hosting mit IIS Veröffentlichung und Bereitstellung, finden Sie unter den folgenden Themen:

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>

Starten Sie die app aus, um sicherzustellen, dass die Windows-Authentifizierung funktioniert.

## <a name="enable-windows-authentication-with-httpsys"></a>Aktivieren der Windows-Authentifizierung mit HTTP.sys

Obwohl Windows-Authentifizierung von Kestrel nicht unterstützt wird, können Sie [HTTP.sys](xref:fundamentals/servers/httpsys) selbst gehostete Szenarios für Windows zu unterstützen. Im folgenden Beispiel wird der app Web-Host, um die "http.sys" mit der Windows-Authentifizierung zu verwenden:

[!code-csharp[](windowsauth/sample_snapshot/Program.cs?highlight=9-14)]

> [!NOTE]
> HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll. Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt. Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird. Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App.

> [!NOTE]
> HTTP.sys wird nicht auf Nano Server-Version 1709 oder höher unterstützt. Um Windows-Authentifizierung und HTTP.sys mit Nano Server verwenden möchten, verwenden eine [Server Core (Microsoft/Windowsservercore) Container](https://hub.docker.com/r/microsoft/windowsservercore/). Weitere Informationen zur Server Core finden Sie unter [Was ist, dass der Server Core-Installationsoption in Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="work-with-windows-authentication"></a>Arbeiten mit Windows-Authentifizierung

Der Konfigurationsstatus des anonymen Zugriffs bestimmt die Art, wie die `[Authorize]` und `[AllowAnonymous]` Attribute werden in der app verwendet. In den folgenden zwei Abschnitten wird erläutert, wie die nicht zulässigen und die zulässigen Zustände des anonymen Zugriffs zu behandeln.

### <a name="disallow-anonymous-access"></a>Anonyme Zugriffe nicht zulassen

Wenn Windows-Authentifizierung aktiviert ist und der anonyme Zugriff deaktiviert ist, die `[Authorize]` und `[AllowAnonymous]` Attribute haben keine Auswirkung. Wenn der IIS-Site (oder HTTP.sys) konfiguriert ist, um den anonymen Zugriff verweigern, erreicht die Anforderung nie Ihrer app. Aus diesem Grund die `[AllowAnonymous]` Attribut ist nicht anwendbar.

### <a name="allow-anonymous-access"></a>Anonymen Zugriff zulassen

Wenn sowohl für Windows-Authentifizierung als auch für anonymen Zugriff aktiviert sind, verwenden die `[Authorize]` und `[AllowAnonymous]` Attribute. Die `[Authorize]` Attribut können Sie Teile der app schützen, das wirklich Windows-Authentifizierung erforderlich ist. Die `[AllowAnonymous]` -Attribut überschreibt `[Authorize]` Attribut Nutzung in apps, die anonymen Zugriff zulassen. Finden Sie unter [einfache Autorisierung](xref:security/authorization/simple) Nutzungsdetails Attribut.

In ASP.NET Core 2.x, die `[Authorize]` Attribut erfordert zusätzliche Konfigurationsschritte in *"Startup.cs"* sollen anonyme Anforderungen für die Windows-Authentifizierung. Die empfohlene Konfiguration variiert leicht basierend auf dem Webserver verwendet wird.

> [!NOTE]
> Standardmäßig werden Benutzer, die Autorisierung zum Zugriff auf eine Seite nicht über eine leere HTTP 403-Antwort angezeigt. Die [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) kann konfiguriert werden, um Benutzern mit "Zugriff verweigert" Benutzerfreundlicher zu ermöglichen.

#### <a name="iis"></a>IIS

Wenn Sie IIS verwenden, fügen Sie den folgenden der `ConfigureServices` Methode:

```csharp
// IISDefaults requires the following import:
// using Microsoft.AspNetCore.Server.IISIntegration;
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

#### <a name="httpsys"></a>HTTP.sys

Wenn Sie "http.sys" verwenden, fügen Sie den folgenden der `ConfigureServices` Methode:

```csharp
// HttpSysDefaults requires the following import:
// using Microsoft.AspNetCore.Server.HttpSys;
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

### <a name="impersonation"></a>Identitätswechsel

ASP.NET Core implementiert keine Identitätswechsel. Apps, die mit der app Identität für alle Anforderungen, die mithilfe von app-Pool oder Prozess Identität ausgeführt werden. Wenn Sie explizit eine Aktion im Auftrag eines Benutzers ausführen müssen, verwenden Sie [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in einem [terminal Inline-Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`. Eine einzelne Aktion in diesem Kontext ausgeführt, und schließen Sie den Kontext.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` unterstützt keine asynchronen Vorgänge und sollte nicht für komplexe Szenarien verwendet werden. Z. B. wird nicht wrapping gesamte Anforderungen oder Middleware verkettet unterstützt oder empfohlen.

### <a name="claims-transformations"></a>Transformationen von Ansprüchen

Wenn Sie mit in-Process-Modus von IIS hosten <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> wird nicht intern aufgerufen, um einen Benutzer zu initialisieren. Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert. Weitere Informationen und ein Codebeispiel, das Transformationen von Ansprüchen, das aktiviert wird, wenn prozessinternes hosting, finden Sie unter <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.
