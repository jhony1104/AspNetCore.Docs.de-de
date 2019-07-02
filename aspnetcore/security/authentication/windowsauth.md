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
# <a name="configure-windows-authentication-in-aspnet-core"></a>Konfigurieren der Windows-Authentifizierung in ASP.NET Core

Durch [Scott Addie](https://twitter.com/Scott_Addie) und [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

Windows-Authentifizierung (auch bekannt als Negotiate, Kerberos oder NTLM-Authentifizierung) kann konfiguriert werden, für ASP.NET Core-apps mit gehosteten [IIS](xref:host-and-deploy/iis/index), [Kestrel](xref:fundamentals/servers/kestrel), oder [HTTP.sys](xref:fundamentals/servers/httpsys) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Windows-Authentifizierung (auch bekannt als Negotiate, Kerberos oder NTLM-Authentifizierung) kann konfiguriert werden, für ASP.NET Core-apps mit gehosteten [IIS](xref:host-and-deploy/iis/index) oder [HTTP.sys](xref:fundamentals/servers/httpsys).

::: moniker-end

Windows-Authentifizierung basiert auf dem Betriebssystem zur Authentifizierung von Benutzern von ASP.NET Core-apps. Sie können Windows-Authentifizierung verwenden, wenn Ihre Server in einem Unternehmensnetzwerk, die mithilfe von Active Directory-domänenidentitäten oder Windows-Konten zur Identifizierung von Benutzern ausgeführt wird. Windows-Authentifizierung ist am besten für Intranetumgebungen, in dem Benutzer, Client-apps und Webserver mit der gleichen Windows-Domäne gehören.

> [!NOTE]
> Windows-Authentifizierung mit HTTP/2 wird nicht unterstützt. -Authentifizierungsaufforderungen gesendet werden können, über HTTP/2-Antworten, aber der Client muss auf HTTP/1.1 Führen Sie vor dem authentifizieren.

## <a name="iisiis-express"></a>IIS/IIS Express

Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.IISIntegration?displayProperty=fullName> Namespace) in `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

### <a name="launch-settings-debugger"></a>Starteinstellungen Sie (Debugger)

Konfiguration für die starteinstellungen für den wirkt sich nur auf die *Properties/launchSettings.json* für IIS Express-Datei, und Konfigurieren von IIS für Windows-Authentifizierung nicht. Server-Konfiguration wird erläutert, der [IIS](#iis) Abschnitt.

Die **Webanwendung** Vorlage zur Verfügung, über Visual Studio oder .NET Core-CLI kann konfiguriert werden, zur Unterstützung von Windows-Authentifizierung, aktualisiert der *Properties/launchSettings.json* Datei automatisch.

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

**Neues Projekt**

1. Erstellen Sie ein neues Projekt.
1. Wählen Sie **ASP.NET Core-Webanwendung** aus. Klicken Sie auf **Weiter**.
1. Geben Sie einen Namen in der **Projektname** Feld. Vergewissern Sie sich die **Speicherort** Eintrag richtig ist, oder geben Sie einen Speicherort für das Projekt. Wählen Sie **Erstellen** aus.
1. Wählen Sie **Änderung** unter **Authentifizierung**.
1. In der **Authentifizierung ändern** wählen Sie im Fenster **Windows-Authentifizierung**. Klicken Sie auf **OK**.
1. Wählen Sie **Webanwendung** aus.
1. Wählen Sie **Erstellen** aus.

Führen Sie die App aus. Der Benutzername wird in der gerenderten app-Benutzeroberfläche angezeigt.

**Vorhandenes Projekt**

Die Eigenschaften des Projekts Windows-Authentifizierung aktivieren und deaktivieren Sie die anonyme Authentifizierung:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Eigenschaften** aus.
1. Klicken Sie auf die Registerkarte **Debuggen**.
1. Deaktivieren Sie das Kontrollkästchen für **Aktivieren der anonymen Authentifizierung**.
1. Wählen Sie das Kontrollkästchen für **Windows-Authentifizierung aktivieren**.
1. Speichern Sie und schließen Sie die Eigenschaftenseite.

Alternativ können die Eigenschaften konfiguriert werden, der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

# <a name="visual-studio-code--net-core-clitabvisual-studio-codenetcore-cli"></a>[Visual Studio Code / .NET Core-CLI](#tab/visual-studio-code+netcore-cli)

**Neues Projekt**

Führen Sie die [Dotnet neue](/dotnet/core/tools/dotnet-new) -Befehl mit der `webapp` Argument (ASP.NET Core-Web-App) und `--auth Windows` wechseln:

```console
dotnet new webapp --auth Windows
```

**Vorhandenes Projekt**

Update der `iisSettings` Knoten die *"launchsettings.JSON"* Datei:

[!code-json[](windowsauth/sample_snapshot/launchSettings.json?highlight=2-3)]

---

Wenn Sie ein vorhandenes Projekt ändern möchten, vergewissern Sie sich, dass die Projektdatei für die Paketverweise enthält die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app) **oder** der [ Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) NuGet-Paket.

### <a name="iis"></a>IIS

IIS verwendet den [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) zum Hosten von ASP.NET Core-apps. Windows-Authentifizierung konfiguriert ist, für IIS über die *"Web.config"* Datei. Die folgenden Abschnitte zeigen Gewusst wie:

* Geben Sie eine lokale *"Web.config"* -Datei, die Windows-Authentifizierung auf dem Server aktiviert, wenn die app bereitgestellt wird.
* Verwenden Sie den IIS-Manager so konfigurieren Sie die *"Web.config"* Datei von einer ASP.NET Core-app, die bereits auf dem Server bereitgestellt wurde.

Wenn Sie nicht bereits geschehen, aktivieren Sie IIS zum Hosten von ASP.NET Core-apps. Weitere Informationen finden Sie unter <xref:host-and-deploy/iis/index>.

Den Rollendienst "IIS" für Windows-Authentifizierung zu aktivieren. Weitere Informationen finden Sie unter [Aktivieren der Windows-Authentifizierung in IIS-Rollendienste (Siehe Schritt2)](xref:host-and-deploy/iis/index#iis-configuration).

[Middleware für die Integration von IIS](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) wird standardmäßig zum Authentifizieren von Anforderungen automatisch konfiguriert. Weitere Informationen finden Sie unter [Hosten von ASP.NET Core unter Windows mit IIS: IIS-Optionen (AutomaticAuthentication)](xref:host-and-deploy/iis/index#iis-options).

ASP.NET Core-Modul ist zum Weiterleiten von der Windows-Authentifizierung-Token für die app wird standardmäßig konfiguriert. Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul: Attribute des-Elements AspNetCore](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element).

Verwendung **entweder** der folgenden Methoden:

* **Vor der Veröffentlichung und Bereitstellung des Projekts** fügen Sie die folgenden *"Web.config"* Datei in das Stammverzeichnis des Projekts:

  [!code-xml[](windowsauth/sample_snapshot/web_2.config)]

  Wenn das Projekt veröffentlicht wird, durch das .NET Core SDK (ohne die `<IsTransformWebConfigDisabled>` -Eigenschaft auf festgelegt `true` in der Projektdatei), die veröffentlichte *"Web.config"* -Datei enthält die `<location><system.webServer><security><authentication>` Abschnitt. Weitere Informationen zu den `<IsTransformWebConfigDisabled>` -Eigenschaft finden Sie unter <xref:host-and-deploy/iis/index#webconfig-file>.

* **Nach der Veröffentlichung und Bereitstellung des Projekts** : serverseitige Konfiguration mit dem IIS-Manager ausführen:

  1. Im IIS-Manager, wählen Sie die IIS-Website unter der **Websites** Knoten die **Verbindungen** Randleiste.
  1. Doppelklicken Sie auf **Authentifizierung** in die **IIS** Bereich.
  1. Wählen Sie **anonyme Authentifizierung**. Wählen Sie **deaktivieren** in die **Aktionen** Randleiste.
  1. Wählen Sie **Windows-Authentifizierung**. Wählen Sie **aktivieren** in die **Aktionen** Randleiste.

  Wenn diese Aktionen ausgeführt werden, ändert der IIS-Manager der app *"Web.config"* Datei. Ein `<system.webServer><security><authentication>` Knoten wird hinzugefügt, mit aktualisierten Einstellungen für `anonymousAuthentication` und `windowsAuthentication`:

  [!code-xml[](windowsauth/sample_snapshot/web_1.config?highlight=4-5)]

  Die `<system.webServer>` Abschnitts in der *"Web.config"* Datei vom IIS-Manager befindet sich außerhalb der app `<location>` Abschnitt durch die .NET Core SDK hinzugefügt wird, wenn die Anwendung veröffentlicht wurde. Da der Abschnitt, außerhalb von hinzugefügt wird den `<location>` Knoten werden die Einstellungen von einem geerbt [untergeordnete apps, die](xref:host-and-deploy/iis/index#sub-applications) der aktuellen App. Um Vererbung zu verhindern, verschieben Sie die hinzugefügte `<security>` Abschnitt in der die `<location><system.webServer>` -Abschnitt, der das .NET Core SDK bereitgestellt.

  Wenn IIS-Manager verwendet wird, um die IIS-Konfiguration hinzuzufügen, es wirkt sich nur auf der app *"Web.config"* Datei auf dem Server. Eine anschließende Bereitstellung der app möglicherweise die Einstellungen auf dem Server überschreiben, wenn die Kopie des Servers der *"Web.config"* durch des Projekts ersetzt *"Web.config"* Datei. Verwendung **entweder** der folgenden Methoden zum Verwalten der Einstellungen:

  * Verwenden Sie IIS-Manager zum Zurücksetzen der Einstellungen in der *"Web.config"* -Datei nach der Bereitstellung die Datei überschrieben wird.
  * Hinzufügen einer *Datei "Web.config"* an die app lokal mit den Einstellungen.

::: moniker range=">= aspnetcore-3.0"

## <a name="kestrel"></a>Kestrel

 Die [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) NuGet-Paket verwendet werden kann, mit [Kestrel](xref:fundamentals/servers/kestrel) zur Unterstützung der Windows-Authentifizierung mithilfe des Negotiate, Kerberos und NTLM für Windows, Linux und MacOS.

> [!WARNING]
> Anmeldeinformationen können für die Anforderungen für eine Verbindung gespeichert werden. *Aushandeln Authentifizierung darf nicht mit Proxys verwendet werden, es sei denn, der Proxy eine 1:1-verbindungsaffinität (eine permanente Verbindung) mit Kestrel führt.*

> [!NOTE]
> Der Negotiate-Handler erkennt, wenn der zugrunde liegenden Server Windows-Authentifizierung nativ unterstützt, und wenn es aktiviert ist. Wenn der Server unterstützt die Windows-Authentifizierung, aber er deaktiviert ist, wird ein Fehler ausgelöst, werden Sie aufgefordert, die Server-Implementierung zu aktivieren. Bei der Windows-Authentifizierung auf dem Server aktiviert ist, werden die Negotiate-Handler transparent an sie weitergeleitet.

 Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (`Microsoft.AspNetCore.Authentication.Negotiate` Namespace) und `AddNegotitate` (`Microsoft.AspNetCore.Authentication.Negotiate` Namespace) in `Startup.ConfigureServices`:

 ```csharp
services.AddAuthentication(NegotiateDefaults.AuthenticationScheme)
    .AddNegotiate();
```

Fügen Sie Middleware für die Authentifizierung durch den Aufruf <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> in `Startup.Configure`:

 ```csharp
app.UseAuthentication();

app.UseMvc();
```

Weitere Informationen zu Middleware, finden Sie unter <xref:fundamentals/middleware/index>.

Anonyme Anforderungen sind zulässig. Verwendung [ASP.NET Core-Autorisierung](xref:security/authorization/introduction) sollen anonyme Anforderungen für die Authentifizierung.

### <a name="windows-environment-configuration"></a>Konfiguration der Windows-Umgebung

Die [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Komponente führt eine Benutzermodus-Authentifizierung. Dienstprinzipalnamen (SPN) muss das Benutzerkonto, das Ausführen des Diensts, nicht das Computerkonto hinzugefügt werden. Führen Sie `setspn -S HTTP/mysrevername.mydomain.com myuser` in einer administrativen Befehlsshell.

### <a name="linux-and-macos-environment-configuration"></a>Konfiguration für Linux und MacOS-Umgebung

Anweisungen zum Verknüpfen von eines Linux- oder MacOS-Computers mit einer Windows-Domäne finden Sie in der [Verbinden von Azure Data Studio zu Ihrem SQL Server mithilfe der Windows-Authentifizierung – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) Artikel. Die Anweisungen erstellen Sie ein Computerkonto für den Linux-Computer in der Domäne. SPNs müssen auf diese Computerkonten hinzugefügt werden.

> [!NOTE]
> Befolgen Sie die Anweisungen in der [Verbinden von Azure Data Studio zu Ihrem SQL Server mithilfe der Windows-Authentifizierung – Kerberos](/sql/azure-data-studio/enable-kerberos?view=sql-server-2017#join-your-os-to-the-active-directory-domain-controller) Artikel, ersetzen Sie `python-software-properties` mit `python3-software-properties` bei Bedarf.

Nach der Linux oder MacOS-Computer der Domäne angehört, sind zusätzliche Schritte erforderlich, zu einem [Keytab-Datei](https://blogs.technet.microsoft.com/pie/2018/01/03/all-you-need-to-know-about-keytab-files/) mit der SPNs:

* Fügen Sie auf dem Domänencontroller auf das Computerkonto neuen Webdienst SPNs hinzu:
  * `setspn -S HTTP/mywebservice.mydomain.com mymachine`
  * `setspn -S HTTP/mywebservice@MYDOMAIN.COM mymachine`
* Verwendung [Ktpass](/windows-server/administration/windows-commands/ktpass) generiert eine Keytab-Datei:
  * `ktpass -princ HTTP/mywebservice.mydomain.com@MYDOMAIN.COM -pass myKeyTabFilePassword -mapuser MYDOMAIN\mymachine$ -pType KRB5_NT_PRINCIPAL -out c:\temp\mymachine.HTTP.keytab -crypto AES256-SHA1`
  * Einige Felder müssen angegeben werden Großbuchstaben angegeben.
* Kopieren Sie die Keytab-Datei mit dem Linux oder MacOS-Computer.
* Wählen Sie die Keytab-Datei über eine Umgebungsvariable: `export KRB5_KTNAME=/tmp/mymachine.HTTP.keytab`
* Rufen Sie `klist` , die derzeit nur SPNs angezeigt.

> [!NOTE]
> Eine Keytab-Datei enthält die Anmeldeinformationen für die Domäne zugreifen und muss entsprechend geschützt werden.

::: moniker-end

## <a name="httpsys"></a>HTTP.sys

[HTTP.sys](xref:fundamentals/servers/httpsys) Windows Kernelmodusauthentifizierung mit Negotiate, NTLM oder Standardauthentifizierung unterstützt.

Hinzufügen von Authentifizierungsdienste durch den Aufruf <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication*> (<xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> Namespace) in `Startup.ConfigureServices`:

```csharp
services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
```

Konfigurieren der app-Web-Host zur Verwendung von HTTP.sys mit Windows-Authentifizierung ( *"Program.cs"* ). <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> befindet sich in der <xref:Microsoft.AspNetCore.Server.HttpSys?displayProperty=fullName> Namespace.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_GenericHost.cs?highlight=13-19)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](windowsauth/sample_snapshot/Program_WebHost.cs?highlight=9-15)]

::: moniker-end

> [!NOTE]
> HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll. Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt. Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird. Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App.

> [!NOTE]
> HTTP.sys wird nicht auf Nano Server-Version 1709 oder höher unterstützt. Um Windows-Authentifizierung und HTTP.sys mit Nano Server verwenden möchten, verwenden eine [Server Core (Microsoft/Windowsservercore) Container](https://hub.docker.com/r/microsoft/windowsservercore/). Weitere Informationen zur Server Core finden Sie unter [Was ist, dass der Server Core-Installationsoption in Windows Server?](/windows-server/administration/server-core/what-is-server-core).

## <a name="authorize-users"></a>Autorisieren von Benutzern

Der Konfigurationsstatus des anonymen Zugriffs bestimmt die Art, wie die `[Authorize]` und `[AllowAnonymous]` Attribute werden in der app verwendet. In den folgenden zwei Abschnitten wird erläutert, wie die nicht zulässigen und die zulässigen Zustände des anonymen Zugriffs zu behandeln.

### <a name="disallow-anonymous-access"></a>Anonyme Zugriffe nicht zulassen

Wenn Windows-Authentifizierung aktiviert ist und der anonyme Zugriff deaktiviert ist, die `[Authorize]` und `[AllowAnonymous]` Attribute haben keine Auswirkung. Wenn eine IIS-Website konfiguriert ist, um den anonymen Zugriff verweigern, erreicht die Anforderung nie die app. Aus diesem Grund die `[AllowAnonymous]` Attribut ist nicht anwendbar.

### <a name="allow-anonymous-access"></a>Anonymen Zugriff zulassen

Wenn sowohl für Windows-Authentifizierung als auch für anonymen Zugriff aktiviert sind, verwenden die `[Authorize]` und `[AllowAnonymous]` Attribute. Die `[Authorize]` Attribut können Sie die Endpunkte der app schützen, das Authentifizierung erforderlich ist. Die `[AllowAnonymous]` -Attribut überschreibt die `[Authorize]` -Attribut in apps, die den anonymen Zugriff zulassen. Attribut Verwendungsdetails finden Sie unter <xref:security/authorization/simple>.

> [!NOTE]
> Standardmäßig werden Benutzer, die Autorisierung zum Zugriff auf eine Seite nicht über eine leere HTTP 403-Antwort angezeigt. Die [StatusCodePages Middleware](xref:fundamentals/error-handling#usestatuscodepages) kann konfiguriert werden, um Benutzern mit "Zugriff verweigert" Benutzerfreundlicher zu ermöglichen.

## <a name="impersonation"></a>Identitätswechsel

ASP.NET Core implementiert keine Identitätswechsel. Apps, die mit der app Identität für alle Anforderungen, die mithilfe von app-Pool oder Prozess Identität ausgeführt werden. Wenn die app eine Aktion im Auftrag eines Benutzers ausführen soll, verwenden Sie [WindowsIdentity.RunImpersonated](xref:System.Security.Principal.WindowsIdentity.RunImpersonated*) in einem [terminal Inline-Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) in `Startup.Configure`. Eine einzelne Aktion in diesem Kontext ausgeführt, und schließen Sie den Kontext.

[!code-csharp[](windowsauth/sample_snapshot/Startup.cs?highlight=10-19)]

`RunImpersonated` unterstützt keine asynchronen Vorgänge und sollte nicht für komplexe Szenarien verwendet werden. Z. B. wird nicht wrapping gesamte Anforderungen oder Middleware verkettet unterstützt oder empfohlen.

::: moniker range=">= aspnetcore-3.0"

Während der [Microsoft.AspNetCore.Authentication.Negotiate](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Negotiate) Paket ermöglicht die Authentifizierung auf Windows, Linux und MacOS, Identitätswechsel wird nur unter Windows unterstützt.

::: moniker-end

## <a name="claims-transformations"></a>Transformationen von Ansprüchen

::: moniker range=">= aspnetcore-3.0"

Wenn Sie mit IIS hosten <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> wird nicht intern aufgerufen, um einen Benutzer zu initialisieren. Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert. Weitere Informationen und ein Codebeispiel, das Transformationen von Ansprüchen aktiviert wird, finden Sie unter <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Wenn Sie mit in-Process-Modus von IIS hosten <xref:Microsoft.AspNetCore.Authentication.AuthenticationService.AuthenticateAsync*> wird nicht intern aufgerufen, um einen Benutzer zu initialisieren. Deshalb ist eine <xref:Microsoft.AspNetCore.Authentication.IClaimsTransformation>-Implementierung, die verwendet wird, um Ansprüche nach jeder Authentifizierung zu transformieren, nicht standardmäßig aktiviert. Weitere Informationen und ein Codebeispiel, das Transformationen von Ansprüchen, das aktiviert wird, wenn prozessinternes hosting, finden Sie unter <xref:host-and-deploy/aspnet-core-module#in-process-hosting-model>.

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [dotnet publish](/dotnet/core/tools/dotnet-publish)
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/visual-studio-publish-profiles>
