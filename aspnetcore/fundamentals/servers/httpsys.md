---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor' 'Identity'"
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 11c2aa8d8633d1ca165d05ce5ea7b277d6ab7ad2
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84106792"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>'Let's Encrypt'

'Razor'

::: moniker range=">= aspnetcore-3.1"

'SignalR' uid: Implementierung des Http.sys-Webservers in ASP.NET Core

> [!IMPORTANT]
> Von [Tom Dykstra](https://github.com/tdykstra) und [Chris Ross](https://github.com/Tratcher)

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) ist ein [Webserver für ASP.NET Core](xref:fundamentals/servers/index), der nur unter Windows ausgeführt werden kann.

* HTTP.sys stellt eine Alternative zum [Kestrel](xref:fundamentals/servers/kestrel)-Server dar und bietet einige Features, die in Kestrel fehlen.
* HTTP.sys ist nicht mit dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) kompatibel und kann nicht mit IIS oder IIS Express verwendet werden.
* HTTP.sys unterstützt die folgenden Features:
* [Windows-Authentifizierung](xref:security/authentication/windowsauth)
* Portfreigabe
* HTTPS mit SNI
* HTTP/2 über TLS (Windows 10 oder höher)

Direkte Dateiübertragung

* Zwischenspeichern von Antworten
* WebSockets (Windows 8 oder höher)

Unterstützte Windows-Versionen:

## <a name="when-to-use-httpsys"></a>Windows 7 oder höher

Windows Server 2008 R2 oder höher

* [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

  ![Empfohlene Verwendung von HTTP.sys](httpsys/_static/httpsys-to-internet.png)

* HTTP.sys eignet sich für Bereitstellungen, auf die Folgendes zutrifft:

  ![Sie müssen den Server ohne IIS direkt mit dem Internet verbinden.](httpsys/_static/httpsys-to-internal.png)

HTTP.sys kommuniziert direkt mit dem Internet Eine interne Bereitstellung erfordert ein Feature, das in Kestrel nicht verfügbar ist, z.B. die [Windows-Authentifizierung](xref:security/authentication/windowsauth).

## <a name="http2-support"></a>HTTP.sys kommuniziert direkt mit dem internen Netzwerk

Bei HTTP.sys handelt es sich um eine ausgereifte Technologie, die Schutz vor vielen Arten von Angriffen bietet und zudem die Stabilität, Sicherheit und Skalierbarkeit eines Webservers mit vollem Funktionsumfang bereitstellt.

* IIS selbst wird auf HTTP.sys als HTTP-Listener ausgeführt.
* HTTP/2-Unterstützung
* [HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps aktiviert, wenn die folgenden Basisanforderungen erfüllt sind:

Windows Server 2016/Windows 10 oder höher

[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation) TLS 1.2-Verbindung oder höher Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`.

## <a name="kernel-mode-authentication-with-kerberos"></a>HTTP/2 ist standardmäßig aktiviert.

Für die Verbindung wird ein Fallback auf HTTP/1.1 ausgeführt, wenn keine HTTP/2-Verbindung hergestellt wurde. In einer zukünftigen Version von Windows werden HTTP/2-Konfigurationsflags verfügbar sein, einschließlich der Möglichkeit, HTTP/2 mit HTTP.sys zu deaktivieren. Kernelmodusauthentifizierung mit Kerberos HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll.

## <a name="how-to-use-httpsys"></a>Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt.

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird.

Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App. Empfohlene Verwendung von HTTP.sys

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Konfigurieren der ASP.NET Core-App für die Verwendung von HTTP.sys

Rufen Sie die Erweiterungsmethode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> auf, wenn Sie den Host erstellen, und geben Sie dabei alle erforderlichen <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>-Objekte an.

| Im folgenden Beispiel werden Optionen auf ihre Standardwerte festgelegt: | Die weitere Konfiguration von HTTP.sys erfolgt über [Registrierungseinstellungen](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows). | **HTTP.sys-Optionen** |
| -------- | ----------- | :-----: |
| Eigenschaft | Beschreibung | `false` |
| Standard | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | `true` |
| 'Blazor' | 'Identity' 'Let's Encrypt' 'Razor' | `None` |
| 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | 'Let's Encrypt' | 'Razor' |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | 'SignalR' uid: ---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' | `null`<br>'Identity'<br>'Let's Encrypt' |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | 'Razor' | 'SignalR' uid:<br>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | 'Blazor' | 'Identity' |
| `RequestQueueMode` | 'Let's Encrypt'<br>'Razor' | `RequestQueueMode.Create` |
| `RequestQueueName` | 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | 'Blazor' | `false`<br>'Identity' |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 'Let's Encrypt' 'Razor'<ul><li>'SignalR' uid:</li><li>------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Hiermit steuern Sie, ob eine synchrone Eingabe/Ausgabe für `HttpContext.Request.Body` und `HttpContext.Response.Body` zulässig ist.</li><li> | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Hiermit erlauben Sie anonyme Anforderungen.</li><li> | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Hiermit geben Sie die zulässigen Authentifizierungsschemas an.</li><li>Diese Eigenschaft kann jederzeit vor dem Verwerfen des Listeners geändert werden.</li><li>Die Werte werden durch die [AuthenticationSchemes-Enumeration](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) bereitgestellt: `Basic`, `Kerberos`, `Negotiate`, `None` und `NTLM`.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> |  | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Hiermit wird das Zwischenspeichern im [Kernelmodus](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) für Antworten mit geeigneten Headern versucht. Die Antwort enthält möglicherweise keine `Set-Cookie`-, `Vary`- oder `Pragma`-Header. Sie muss einen `Cache-Control`-Header enthalten, der vom Typ `public` ist und entweder ein `shared-max-age`- oder ein `max-age`-Wert oder ein `Expires`-Header ist. |  |

<a name="maxrequestbodysize"></a>

 | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Die maximale Anzahl gleichzeitiger Aufrufe.

| 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Die maximale Anzahl an gleichzeitigen Verbindungen, die akzeptiert werden. Verwenden Sie `-1`, um eine unbegrenzte Anzahl anzugeben. Verwenden Sie `null`, um die computerübergreifende Einstellung der Registrierung zu verwenden.

(computerweite

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Informationen hierzu finden Sie im Abschnitt <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30.000.000 Bytes

(~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Die maximale Anzahl von Anforderungen, die in der Warteschlange gespeichert werden kann.

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

| 1000 | | `RequestQueueMode` | Hiermit wird angegeben, ob der Server für die Erstellung und Konfiguration der Anforderungswarteschlange verantwortlich ist oder ob ein Anfügen an eine bestehende Warteschlange erfolgen soll.

Die meisten vorhandenen Konfigurationsoptionen gelten beim Anfügen an eine vorhandene Warteschlange nicht.  | `RequestQueueMode.Create` | | `RequestQueueName` | Der Name der HTTP.sys-Anforderungswarteschlange.

![ | `null` (Anonyme Warteschlange) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Hiermit geben Sie an, ob Schreibvorgänge für Antworttext, die einen Fehler zurückgeben, weil die Verbindung zum Client getrennt wird, eine Ausnahme auslösen oder normal beendet werden.](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>(complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Hiermit machen Sie die HTTP.sys-Konfiguration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> verfügbar. Diese kann auch in der Registrierung konfiguriert werden.

1. Unter folgenden API-Links finden Sie Informationen zu den einzelnen Einstellungen sowie die Standardwerte: [TimeoutManager.DrainEntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) die zulässige Zeit, in der die HTTP-Server-API den Entitätstext in einer Keep-Alive-Verbindung leeren muss

1. [TimeoutManager.EntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) die zulässige Zeit für die Ankunft des Anforderungsentitätstexts [TimeoutManager.HeaderWait:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) die zulässige Zeit, in der die HTTP-Server-API den Anforderungsheader analysieren muss

1. [TimeoutManager.IdleConnection:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) die zulässige Zeit für eine Verbindung im Leerlauf

   [TimeoutManager.MinSendBytesPerSecond:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) die Mindestsenderate für die Antwort. [TimeoutManager.RequestQueue:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) die zulässige Zeit, die die Anforderung in der Anforderungswarteschlange verbleibt, bevor sie von der App übernommen wird

   |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Geben Sie die <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> an, die bei „HTTP.sys“ registriert werden soll.

1. Besonders nützlich ist die Eigenschaft [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), mit der Sie der Sammlung ein Präfix hinzufügen können.

   * Diese Eigenschaften können jederzeit vor dem Verwerfen des Listeners geändert werden. |  |
   * **MaxRequestBodySize** Die maximal zulässige Größe eines Anforderungstexts in Bytes. Wenn dieser Wert auf `null` festgelegt wird, ist die maximale Größe für Anforderungstexte unbegrenzt.

   Dieser Grenzwert wirkt sich nicht auf Verbindungen aus, für die ein Upgrade durchgeführt wurde – diese sind immer unbegrenzt. Die empfohlene Methode zum Überschreiben des Grenzwerts in einer ASP.NET Core-MVC-App für ein einzelnes `IActionResult` besteht im Verwenden von <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in einer Aktionsmethode:

1. Eine Ausnahme wird ausgelöst, wenn die App versucht, den Grenzwert einer Anforderung zu konfigurieren, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.

   Sie können eine `IsReadOnly`-Eigenschaft verwenden, um darauf hinzuweisen, dass sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, der Grenzwert also nicht mehr konfiguriert werden kann. Wenn die App <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> pro Anforderung außer Kraft setzen sollte, verwenden Sie das <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * Wenn Sie Visual Studio verwenden, stellen Sie sicher, dass die App nicht für die Ausführung von IIS oder IIS Express konfiguriert ist.
   * In Visual Studio ist das Standardstartprofil auf IIS Express ausgerichtet.
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   Wenn das Projekt als Konsolen-App ausgeführt werden soll, ändern Sie das ausgewählte Profil manuell, wie im folgenden Screenshot dargestellt:

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Auswählen des Profils der App-Konsole

   Konfigurieren von Windows Server Bestimmen Sie die Ports, die für die App geöffnet werden sollen, und verwenden Sie die [Windows-Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) oder das PowerShell-Cmdlet [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule), um Firewall-Ports zu öffnen, damit der Datenverkehr HTTP.sys erreichen kann.

   In den folgenden Befehlen und der Appkonfiguration wird Port 443 verwendet.

   > [!WARNING]
   > Öffnen Sie bei der Bereitstellung für eine Azure-VM die Ports in der [Netzwerksicherheitsgruppe](/azure/virtual-machines/windows/nsg-quickstart-portal). In den folgenden Befehlen und der Appkonfiguration wird Port 443 verwendet. Beziehen Sie X.509-Zertifikate und installieren Sie sie, falls erforderlich. Erstellen Sie unter Windows selbstsignierte Zertifikate mit dem [PowerShell-Cmdlet „New-SelfSignedCertificate“](/powershell/module/pkiclient/new-selfsignedcertificate). Ein nicht unterstütztes Beispiel finden Sie unter [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1). Installieren Sie entweder selbstsignierte oder CA-signierte Zertifikate im Speicher **Lokaler Server** > **Persönlich** des Servers.

1. Wenn es sich bei der App um eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) handelt, installieren Sie .NET Core, .NET Framework oder beides (wenn es sich um eine .NET Core-App für das .NET Framework handelt).

   **.NET Core**: Wenn die App .NET Core erfordert, rufen Sie den Installer für die **.NET Core Runtime** über [.NET Core-Downloads](https://dotnet.microsoft.com/download) ab, und führen Sie ihn aus. Installieren Sie nicht das vollständige SDK auf dem Server. **.NET Framework**: Erfordert die App .NET Framework, rufen Sie das [.NET Framework-Installationshandbuch](/dotnet/framework/install/) auf.

   Installieren Sie das erforderliche .NET Framework.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * Der Installer für das neueste .NET Framework steht auf der Seite [.NET Core-Downloads](https://dotnet.microsoft.com/download) zur Verfügung. Wenn die App eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) ist, enthält die App die Runtime in ihrer Bereitstellung. Es ist keine Frameworkinstallation auf dem Server erforderlich. Konfigurieren Sie URLs und Ports in der App.
   * Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.

   Zum Konfigurieren von URL-Präfixen und Ports stehen folgende Optionen zur Verfügung:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Befehlszeilenargument `urls`

   Umgebungsvariable `ASPNETCORE_URLS`

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Das folgende Codebeispiel zeigt, wie Sie <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> mit der lokalen IP-Adresse `10.0.0.4` des Servers auf Port 443 verwenden:

   Ein Vorteil von `UrlPrefixes` besteht darin, dass bei falsch formatierten Präfixen sofort eine Fehlermeldung generiert wird.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * Die Einstellungen in `UrlPrefixes` überschreiben die Einstellungen für `UseUrls`/`urls`/`ASPNETCORE_URLS`. Daher bieten `UseUrls`, `urls` und die Umgebungsvariable `ASPNETCORE_URLS` den Vorteil, dass sie den Wechsel zwischen Kestrel und HTTP.sys vereinfachen. HTTP.sys verwendet die [HTTP Server-API-UrlPrefix-Zeichenfolgenformate](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).
   * Allgemeine Platzhalterbindungen (`http://*:80/` und `http://+:80`) dürfen **nicht** verwendet werden.
   * Platzhalterbindungen auf oberster Ebene gefährden die Sicherheit Ihrer App.
   * Dies gilt für starke und schwache Platzhalter.

   Verwenden Sie statt Platzhaltern explizite Hostnamen oder IP-Adressen.

   * Platzhalterbindungen in untergeordneten Domänen (z.B. `*.mysub.com`) stellen kein Sicherheitsrisiko dar, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist).
     * Weitere Informationen finden Sie unter [RFC 7230: Abschnitt 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).
     * URL-Präfixe können vorab auf dem Server registriert werden.
     * Das integrierte Tool für die Konfiguration von HTTP.sys ist *netsh.exe*.
   * Mithilfe von *netsh.exe* können Sie URL-Präfixe reservieren und X.509-Zertifikate zuweisen.
     * Das Tool erfordert Administratorrechte.
     * Verwenden Sie das Tool *netsh.exe*, um die URLs für die App zu registrieren:

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   `<URL>`: Der vollqualifizierte Uniform Resource Locator (URL).

   * Verwenden Sie keine Platzhalterbindung.
   * Verwenden Sie einen gültigen Hostnamen oder eine gültige lokale IP-Adresse.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   *Die URL muss einen nachgestellten Schrägstrich enthalten.*

   `<USER>`: gibt den Benutzer oder den Benutzergruppennamen an

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Im folgenden Beispiel ist die lokale IP-Adresse des Servers `10.0.0.4`:

   * Wenn eine URL registriert ist, antwortet das Tool mit `URL reservation successfully added`.
   * Verwenden Sie zum Löschen einer registrierten URL den Befehl `delete urlacl`:

1. Registrieren Sie X.509-Zertifikate auf dem Server.

   Verwenden Sie das Tool *netsh.exe*, um Zertifikate für die App zu registrieren: `<IP>`: gibt die lokale IP-Adresse für die Bindung an.

   Verwenden Sie keine Platzhalterbindung. Verwenden Sie eine gültige IP-Adresse.

   `<PORT>`: gibt den Port für die Bindung an `<THUMBPRINT>`: der Zertifikatsfingerabdruck X.509

   ![`<GUID>`: eine vom Entwickler generierte GUID zur Darstellung der App zu Informationszwecken](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Speichern Sie die GUID zu Referenzzwecken in der App als Paket-Tag:

In Visual Studio: Öffnen Sie die Projekteigenschaften der App, indem Sie mit der rechten Maustaste auf die App im **Projektmappen-Explorer** klicken und **Eigenschaften** auswählen.

## <a name="additional-resources"></a>Wählen Sie die Registerkarte **Paket** aus.

* Geben Sie die GUID ein, die Sie im Feld **Tags** erstellt haben.
* Wenn Sie Visual Studio nicht verwenden:
* Öffnen Sie die Projektdatei der App.
* Fügen Sie einer neuen oder vorhandenen `<PropertyGroup>` mit der von Ihnen erstellten GUID eine `<PackageTags>`-Eigenschaft hinzu:
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Im folgenden Beispiel: Die lokale IP-Adresse des Servers lautet `10.0.0.4`.

> [!IMPORTANT]
> Ein zufälliger Online-GUID-Generator stellt den Wert `appid` bereit.

Wenn ein Zertifikat registriert ist, antwortet das Tool mit `SSL Certificate successfully added`.

* Verwenden Sie zum Löschen einer Zertifikatsregistrierung den Befehl `delete sslcert`:
* Referenzdokumentation für *netsh.exe*:
* [Netsh Commands for Hypertext Transfer Protocol (HTTP) (Netsh-Befehle für Hypertext Transfer-Protokolle (HTTP))](https://technet.microsoft.com/library/cc725882.aspx)
* [UrlPrefix Strings (UrlPrefix-Zeichenfolgen)](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)
* Führen Sie die App aus.
* Administratorberechtigungen sind nicht erforderlich, um die App auszuführen, wenn die Verbindung zum lokalen Host über HTTP (nicht HTTPS) mit einer Portnummer größer als 1024 erfolgt.
* Für andere Konfigurationen (z.B. über eine lokale IP-Adresse oder Bindung an Port 443) führen Sie die App mit Administratorberechtigungen aus.

Die App antwortet auf die öffentliche IP-Adresse des Servers.

* In diesem Beispiel wird der Server aus dem Internet mit seiner öffentlichen IP-Adresse `104.214.79.47` erreicht.
* In diesem Beispiel wird ein Entwicklungszertifikat verwendet.

Die Seite wird sicher geladen, nachdem die Warnung vor nicht vertrauenswürdigen Zertifikaten des Browsers umgangen wurde.

## <a name="when-to-use-httpsys"></a>Browserfenster mit Anzeige der geladenen Indexseite der App

Proxyserver und Lastenausgleichsszenarien

* Für Apps, die von HTTP.sys gehostet werden und mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren, ist möglicherweise eine zusätzliche Konfiguration erforderlich, wenn sie hinter Proxyservern und Lastenausgleichsmodulen hosten.

  ![Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).](httpsys/_static/httpsys-to-internet.png)

* Zusätzliche Ressourcen

  ![[Aktivieren der Windows-Authentifizierung mit HTTP.sys](xref:security/authentication/windowsauth#httpsys)](httpsys/_static/httpsys-to-internal.png)

[HTTP-Server-API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx) [aspnet/HttpSysServer – GitHub-Repository (Quellcode)](https://github.com/aspnet/HttpSysServer/)

## <a name="http2-support"></a>[Der Host](xref:fundamentals/index#host)

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) ist ein [Webserver für ASP.NET Core](xref:fundamentals/servers/index), der nur unter Windows ausgeführt werden kann.

* HTTP.sys stellt eine Alternative zum [Kestrel](xref:fundamentals/servers/kestrel)-Server dar und bietet einige Features, die in Kestrel fehlen.
* HTTP.sys ist nicht mit dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) kompatibel und kann nicht mit IIS oder IIS Express verwendet werden.
* HTTP.sys unterstützt die folgenden Features:

[Windows-Authentifizierung](xref:security/authentication/windowsauth)

Portfreigabe HTTPS mit SNI HTTP/2 über TLS (Windows 10 oder höher)

## <a name="kernel-mode-authentication-with-kerberos"></a>Direkte Dateiübertragung

Zwischenspeichern von Antworten WebSockets (Windows 8 oder höher) Unterstützte Windows-Versionen: Windows 7 oder höher

## <a name="how-to-use-httpsys"></a>Windows Server 2008 R2 oder höher

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Empfohlene Verwendung von HTTP.sys HTTP.sys eignet sich für Bereitstellungen, auf die Folgendes zutrifft:

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

Sie müssen den Server ohne IIS direkt mit dem Internet verbinden.

HTTP.sys kommuniziert direkt mit dem Internet

| Eine interne Bereitstellung erfordert ein Feature, das in Kestrel nicht verfügbar ist, z.B. die [Windows-Authentifizierung](xref:security/authentication/windowsauth). | HTTP.sys kommuniziert direkt mit dem internen Netzwerk | Bei HTTP.sys handelt es sich um eine ausgereifte Technologie, die Schutz vor vielen Arten von Angriffen bietet und zudem die Stabilität, Sicherheit und Skalierbarkeit eines Webservers mit vollem Funktionsumfang bereitstellt. |
| -------- | ----------- | :-----: |
| IIS selbst wird auf HTTP.sys als HTTP-Listener ausgeführt. | HTTP/2-Unterstützung | `false` |
| [HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps aktiviert, wenn die folgenden Basisanforderungen erfüllt sind: | Windows Server 2016/Windows 10 oder höher | `true` |
| [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation) | TLS 1.2-Verbindung oder höher Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`. HTTP/2 ist standardmäßig aktiviert. | `None` |
| Für die Verbindung wird ein Fallback auf HTTP/1.1 ausgeführt, wenn keine HTTP/2-Verbindung hergestellt wurde. | In einer zukünftigen Version von Windows werden HTTP/2-Konfigurationsflags verfügbar sein, einschließlich der Möglichkeit, HTTP/2 mit HTTP.sys zu deaktivieren. Kernelmodusauthentifizierung mit Kerberos HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll. | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt. | Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird. |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App. Empfohlene Verwendung von HTTP.sys Konfigurieren der ASP.NET Core-App für die Verwendung von HTTP.sys | `null`<br>Rufen Sie die Erweiterungsmethode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> auf, wenn Sie den Host erstellen, und geben Sie dabei alle erforderlichen <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>-Objekte an.<br>Im folgenden Beispiel werden Optionen auf ihre Standardwerte festgelegt: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Die weitere Konfiguration von HTTP.sys erfolgt über [Registrierungseinstellungen](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows). | **HTTP.sys-Optionen**<br>Eigenschaft |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Beschreibung | Standard |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | `false`<br>'Blazor' |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | 'Identity' 'Let's Encrypt'<ul><li>'Razor'</li><li>'SignalR' uid:</li><li>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</li><li>'Blazor'</li><li>'Identity'</li><li>'Let's Encrypt'</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | 'Razor' 'SignalR' uid: ---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |  |

<a name="maxrequestbodysize"></a>

'Blazor'

'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity'

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

'Let's Encrypt'

'Razor' 'SignalR' uid:

![title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>'Blazor'

1. 'Identity' 'Let's Encrypt'

1. 'Razor' 'SignalR' uid:

1. ------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Hiermit steuern Sie, ob eine synchrone Eingabe/Ausgabe für `HttpContext.Request.Body` und `HttpContext.Response.Body` zulässig ist.

    | `false` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Hiermit erlauben Sie anonyme Anforderungen.  | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Hiermit geben Sie die zulässigen Authentifizierungsschemas an.

   Diese Eigenschaft kann jederzeit vor dem Verwerfen des Listeners geändert werden.

1. Die Werte werden durch die [AuthenticationSchemes-Enumeration](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) bereitgestellt: `Basic`, `Kerberos`, `Negotiate`, `None` und `NTLM`.

   *  | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Hiermit wird das Zwischenspeichern im [Kernelmodus](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) für Antworten mit geeigneten Headern versucht. Die Antwort enthält möglicherweise keine `Set-Cookie`-, `Vary`- oder `Pragma`-Header.
   * Sie muss einen `Cache-Control`-Header enthalten, der vom Typ `public` ist und entweder ein `shared-max-age`- oder ein `max-age`-Wert oder ein `Expires`-Header ist.  | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Die maximale Anzahl gleichzeitiger Aufrufe. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Die maximale Anzahl an gleichzeitigen Verbindungen, die akzeptiert werden.

   Verwenden Sie `-1`, um eine unbegrenzte Anzahl anzugeben. Verwenden Sie `null`, um die computerübergreifende Einstellung der Registrierung zu verwenden.

1. (computerweite

   setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Informationen hierzu finden Sie im Abschnitt <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30.000.000 Bytes

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * (~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Die maximale Anzahl von Anforderungen, die in der Warteschlange gespeichert werden kann.
   * | 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Hiermit geben Sie an, ob Schreibvorgänge für Antworttext, die einen Fehler zurückgeben, weil die Verbindung zum Client getrennt wird, eine Ausnahme auslösen oder normal beendet werden.
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   (complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Hiermit machen Sie die HTTP.sys-Konfiguration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> verfügbar. Diese kann auch in der Registrierung konfiguriert werden.

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   Unter folgenden API-Links finden Sie Informationen zu den einzelnen Einstellungen sowie die Standardwerte:

   [TimeoutManager.DrainEntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) die zulässige Zeit, in der die HTTP-Server-API den Entitätstext in einer Keep-Alive-Verbindung leeren muss [TimeoutManager.EntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) die zulässige Zeit für die Ankunft des Anforderungsentitätstexts

   [TimeoutManager.HeaderWait:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) die zulässige Zeit, in der die HTTP-Server-API den Anforderungsheader analysieren muss

   > [!WARNING]
   > [TimeoutManager.IdleConnection:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) die zulässige Zeit für eine Verbindung im Leerlauf [TimeoutManager.MinSendBytesPerSecond:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) die Mindestsenderate für die Antwort. [TimeoutManager.RequestQueue:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) die zulässige Zeit, die die Anforderung in der Anforderungswarteschlange verbleibt, bevor sie von der App übernommen wird |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Geben Sie die <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> an, die bei „HTTP.sys“ registriert werden soll. Besonders nützlich ist die Eigenschaft [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), mit der Sie der Sammlung ein Präfix hinzufügen können. Diese Eigenschaften können jederzeit vor dem Verwerfen des Listeners geändert werden.

1. |  |

   **MaxRequestBodySize** Die maximal zulässige Größe eines Anforderungstexts in Bytes. Wenn dieser Wert auf `null` festgelegt wird, ist die maximale Größe für Anforderungstexte unbegrenzt.

   Dieser Grenzwert wirkt sich nicht auf Verbindungen aus, für die ein Upgrade durchgeführt wurde – diese sind immer unbegrenzt.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * Die empfohlene Methode zum Überschreiben des Grenzwerts in einer ASP.NET Core-MVC-App für ein einzelnes `IActionResult` besteht im Verwenden von <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in einer Aktionsmethode: Eine Ausnahme wird ausgelöst, wenn die App versucht, den Grenzwert einer Anforderung zu konfigurieren, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat. Sie können eine `IsReadOnly`-Eigenschaft verwenden, um darauf hinzuweisen, dass sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, der Grenzwert also nicht mehr konfiguriert werden kann. Wenn die App <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> pro Anforderung außer Kraft setzen sollte, verwenden Sie das <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:
   * Wenn Sie Visual Studio verwenden, stellen Sie sicher, dass die App nicht für die Ausführung von IIS oder IIS Express konfiguriert ist.

   In Visual Studio ist das Standardstartprofil auf IIS Express ausgerichtet.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   Wenn das Projekt als Konsolen-App ausgeführt werden soll, ändern Sie das ausgewählte Profil manuell, wie im folgenden Screenshot dargestellt:

   Auswählen des Profils der App-Konsole

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. Konfigurieren von Windows Server

   Bestimmen Sie die Ports, die für die App geöffnet werden sollen, und verwenden Sie die [Windows-Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) oder das PowerShell-Cmdlet [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule), um Firewall-Ports zu öffnen, damit der Datenverkehr HTTP.sys erreichen kann.

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * In den folgenden Befehlen und der Appkonfiguration wird Port 443 verwendet. Öffnen Sie bei der Bereitstellung für eine Azure-VM die Ports in der [Netzwerksicherheitsgruppe](/azure/virtual-machines/windows/nsg-quickstart-portal). In den folgenden Befehlen und der Appkonfiguration wird Port 443 verwendet.
   * Beziehen Sie X.509-Zertifikate und installieren Sie sie, falls erforderlich.
   * Erstellen Sie unter Windows selbstsignierte Zertifikate mit dem [PowerShell-Cmdlet „New-SelfSignedCertificate“](/powershell/module/pkiclient/new-selfsignedcertificate).
   * Ein nicht unterstütztes Beispiel finden Sie unter [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).

   Installieren Sie entweder selbstsignierte oder CA-signierte Zertifikate im Speicher **Lokaler Server** > **Persönlich** des Servers.

   * Wenn es sich bei der App um eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) handelt, installieren Sie .NET Core, .NET Framework oder beides (wenn es sich um eine .NET Core-App für das .NET Framework handelt).
     * **.NET Core**: Wenn die App .NET Core erfordert, rufen Sie den Installer für die **.NET Core Runtime** über [.NET Core-Downloads](https://dotnet.microsoft.com/download) ab, und führen Sie ihn aus.
     * Installieren Sie nicht das vollständige SDK auf dem Server.
     * **.NET Framework**: Erfordert die App .NET Framework, rufen Sie das [.NET Framework-Installationshandbuch](/dotnet/framework/install/) auf.
   * Installieren Sie das erforderliche .NET Framework.
     * Der Installer für das neueste .NET Framework steht auf der Seite [.NET Core-Downloads](https://dotnet.microsoft.com/download) zur Verfügung.
     * Wenn die App eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) ist, enthält die App die Runtime in ihrer Bereitstellung.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Es ist keine Frameworkinstallation auf dem Server erforderlich.

   * Konfigurieren Sie URLs und Ports in der App.
   * Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   Zum Konfigurieren von URL-Präfixen und Ports stehen folgende Optionen zur Verfügung:

   Befehlszeilenargument `urls`

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Umgebungsvariable `ASPNETCORE_URLS`

   * Das folgende Codebeispiel zeigt, wie Sie <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> mit der lokalen IP-Adresse `10.0.0.4` des Servers auf Port 443 verwenden:
   * Ein Vorteil von `UrlPrefixes` besteht darin, dass bei falsch formatierten Präfixen sofort eine Fehlermeldung generiert wird.

1. Die Einstellungen in `UrlPrefixes` überschreiben die Einstellungen für `UseUrls`/`urls`/`ASPNETCORE_URLS`.

   Daher bieten `UseUrls`, `urls` und die Umgebungsvariable `ASPNETCORE_URLS` den Vorteil, dass sie den Wechsel zwischen Kestrel und HTTP.sys vereinfachen. HTTP.sys verwendet die [HTTP Server-API-UrlPrefix-Zeichenfolgenformate](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   Allgemeine Platzhalterbindungen (`http://*:80/` und `http://+:80`) dürfen **nicht** verwendet werden. Platzhalterbindungen auf oberster Ebene gefährden die Sicherheit Ihrer App.

   Dies gilt für starke und schwache Platzhalter. Verwenden Sie statt Platzhaltern explizite Hostnamen oder IP-Adressen.

   ![Platzhalterbindungen in untergeordneten Domänen (z.B. `*.mysub.com`) stellen kein Sicherheitsrisiko dar, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist).](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>Weitere Informationen finden Sie unter [RFC 7230: Abschnitt 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

URL-Präfixe können vorab auf dem Server registriert werden. Das integrierte Tool für die Konfiguration von HTTP.sys ist *netsh.exe*.

## <a name="additional-resources"></a>Mithilfe von *netsh.exe* können Sie URL-Präfixe reservieren und X.509-Zertifikate zuweisen.

* Das Tool erfordert Administratorrechte.
* Verwenden Sie das Tool *netsh.exe*, um die URLs für die App zu registrieren:
* `<URL>`: Der vollqualifizierte Uniform Resource Locator (URL).
* Verwenden Sie keine Platzhalterbindung.
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Verwenden Sie einen gültigen Hostnamen oder eine gültige lokale IP-Adresse. *Die URL muss einen nachgestellten Schrägstrich enthalten.*

> [!IMPORTANT]
> `<USER>`: gibt den Benutzer oder den Benutzergruppennamen an

Im folgenden Beispiel ist die lokale IP-Adresse des Servers `10.0.0.4`:

* Wenn eine URL registriert ist, antwortet das Tool mit `URL reservation successfully added`.
* Verwenden Sie zum Löschen einer registrierten URL den Befehl `delete urlacl`:
* Registrieren Sie X.509-Zertifikate auf dem Server.
* Verwenden Sie das Tool *netsh.exe*, um Zertifikate für die App zu registrieren:
* `<IP>`: gibt die lokale IP-Adresse für die Bindung an.
* Verwenden Sie keine Platzhalterbindung.
* Verwenden Sie eine gültige IP-Adresse.

`<PORT>`: gibt den Port für die Bindung an

* `<THUMBPRINT>`: der Zertifikatsfingerabdruck X.509
* `<GUID>`: eine vom Entwickler generierte GUID zur Darstellung der App zu Informationszwecken

Speichern Sie die GUID zu Referenzzwecken in der App als Paket-Tag:

## <a name="when-to-use-httpsys"></a>In Visual Studio:

Öffnen Sie die Projekteigenschaften der App, indem Sie mit der rechten Maustaste auf die App im **Projektmappen-Explorer** klicken und **Eigenschaften** auswählen.

* Wählen Sie die Registerkarte **Paket** aus.

  ![Geben Sie die GUID ein, die Sie im Feld **Tags** erstellt haben.](httpsys/_static/httpsys-to-internet.png)

* Wenn Sie Visual Studio nicht verwenden:

  ![Öffnen Sie die Projektdatei der App.](httpsys/_static/httpsys-to-internal.png)

Fügen Sie einer neuen oder vorhandenen `<PropertyGroup>` mit der von Ihnen erstellten GUID eine `<PackageTags>`-Eigenschaft hinzu: Im folgenden Beispiel:

## <a name="http2-support"></a>Die lokale IP-Adresse des Servers lautet `10.0.0.4`.

Ein zufälliger Online-GUID-Generator stellt den Wert `appid` bereit.

* Wenn ein Zertifikat registriert ist, antwortet das Tool mit `SSL Certificate successfully added`.
* Verwenden Sie zum Löschen einer Zertifikatsregistrierung den Befehl `delete sslcert`:
* Referenzdokumentation für *netsh.exe*:

[Netsh Commands for Hypertext Transfer Protocol (HTTP) (Netsh-Befehle für Hypertext Transfer-Protokolle (HTTP))](https://technet.microsoft.com/library/cc725882.aspx)

[UrlPrefix Strings (UrlPrefix-Zeichenfolgen)](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) Führen Sie die App aus. Administratorberechtigungen sind nicht erforderlich, um die App auszuführen, wenn die Verbindung zum lokalen Host über HTTP (nicht HTTPS) mit einer Portnummer größer als 1024 erfolgt.

## <a name="kernel-mode-authentication-with-kerberos"></a>Für andere Konfigurationen (z.B. über eine lokale IP-Adresse oder Bindung an Port 443) führen Sie die App mit Administratorberechtigungen aus.

Die App antwortet auf die öffentliche IP-Adresse des Servers. In diesem Beispiel wird der Server aus dem Internet mit seiner öffentlichen IP-Adresse `104.214.79.47` erreicht. In diesem Beispiel wird ein Entwicklungszertifikat verwendet. Die Seite wird sicher geladen, nachdem die Warnung vor nicht vertrauenswürdigen Zertifikaten des Browsers umgangen wurde.

## <a name="how-to-use-httpsys"></a>Browserfenster mit Anzeige der geladenen Indexseite der App

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Proxyserver und Lastenausgleichsszenarien

Für Apps, die von HTTP.sys gehostet werden und mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren, ist möglicherweise eine zusätzliche Konfiguration erforderlich, wenn sie hinter Proxyservern und Lastenausgleichsmodulen hosten. Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).

Zusätzliche Ressourcen [Aktivieren der Windows-Authentifizierung mit HTTP.sys](xref:security/authentication/windowsauth#httpsys)

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

[HTTP-Server-API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)

[aspnet/HttpSysServer – GitHub-Repository (Quellcode)](https://github.com/aspnet/HttpSysServer/)

| [Der Host](xref:fundamentals/index#host) | [HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) ist ein [Webserver für ASP.NET Core](xref:fundamentals/servers/index), der nur unter Windows ausgeführt werden kann. | HTTP.sys stellt eine Alternative zum [Kestrel](xref:fundamentals/servers/kestrel)-Server dar und bietet einige Features, die in Kestrel fehlen. |
| -------- | ----------- | :-----: |
| HTTP.sys ist nicht mit dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) kompatibel und kann nicht mit IIS oder IIS Express verwendet werden. | HTTP.sys unterstützt die folgenden Features: | `true` |
| [Windows-Authentifizierung](xref:security/authentication/windowsauth) | Portfreigabe | `true` |
| HTTPS mit SNI | HTTP/2 über TLS (Windows 10 oder höher) Direkte Dateiübertragung Zwischenspeichern von Antworten | `None` |
| WebSockets (Windows 8 oder höher) | Unterstützte Windows-Versionen: Windows 7 oder höher Windows Server 2008 R2 oder höher | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)) | Empfohlene Verwendung von HTTP.sys |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | HTTP.sys eignet sich für Bereitstellungen, auf die Folgendes zutrifft: Sie müssen den Server ohne IIS direkt mit dem Internet verbinden. HTTP.sys kommuniziert direkt mit dem Internet | `null`<br>Eine interne Bereitstellung erfordert ein Feature, das in Kestrel nicht verfügbar ist, z.B. die [Windows-Authentifizierung](xref:security/authentication/windowsauth).<br>HTTP.sys kommuniziert direkt mit dem internen Netzwerk |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Bei HTTP.sys handelt es sich um eine ausgereifte Technologie, die Schutz vor vielen Arten von Angriffen bietet und zudem die Stabilität, Sicherheit und Skalierbarkeit eines Webservers mit vollem Funktionsumfang bereitstellt. | IIS selbst wird auf HTTP.sys als HTTP-Listener ausgeführt.<br>HTTP/2-Unterstützung |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | [HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps aktiviert, wenn die folgenden Basisanforderungen erfüllt sind: | Windows Server 2016/Windows 10 oder höher |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation) | `false`<br>TLS 1.2-Verbindung oder höher |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/2`. HTTP/2 ist standardmäßig aktiviert.<ul><li>Für die Verbindung wird ein Fallback auf HTTP/1.1 ausgeführt, wenn keine HTTP/2-Verbindung hergestellt wurde.</li><li>In einer zukünftigen Version von Windows werden HTTP/2-Konfigurationsflags verfügbar sein, einschließlich der Möglichkeit, HTTP/2 mit HTTP.sys zu deaktivieren.</li><li>Kernelmodusauthentifizierung mit Kerberos</li><li>HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll.</li><li>Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt.</li><li>Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird.</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App. Empfohlene Verwendung von HTTP.sys Konfigurieren der ASP.NET Core-App für die Verwendung von HTTP.sys |  |

<a name="maxrequestbodysize"></a>

Bei Verwendung des [Microsoft.AspNetCore.App-Metapakets](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) ist kein Paketverweis in der Projektdatei erforderlich.

Wenn das `Microsoft.AspNetCore.App`-Metapaket nicht verwendet wird, fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) hinzu. Rufen Sie die Erweiterungsmethode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> auf, wenn Sie den Host erstellen, und geben Sie dabei alle erforderlichen <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>-Objekte an. Im folgenden Beispiel werden Optionen auf ihre Standardwerte festgelegt:

Die weitere Konfiguration von HTTP.sys erfolgt über [Registrierungseinstellungen](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

**HTTP.sys-Optionen** Eigenschaft

Beschreibung

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Standard

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

!['Identity'](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>'Let's Encrypt'

1. 'Razor' 'SignalR' uid:

1. title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

1. 'Identity'

   'Let's Encrypt' 'Razor'

   'SignalR' uid:

1. ---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

   * 'Blazor' 'Identity'
   * 'Let's Encrypt' 'Razor' 'SignalR' uid:

   title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

1. 'Identity'

   'Let's Encrypt' 'Razor'

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * 'SignalR' uid:
   * title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   'Blazor'

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   'Identity'

   'Let's Encrypt' 'Razor'

   'SignalR' uid:

   > [!WARNING]
   > ------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Hiermit steuern Sie, ob eine synchrone Eingabe/Ausgabe für `HttpContext.Request.Body` und `HttpContext.Response.Body` zulässig ist.  | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Hiermit erlauben Sie anonyme Anforderungen.  | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Hiermit geben Sie die zulässigen Authentifizierungsschemas an. Diese Eigenschaft kann jederzeit vor dem Verwerfen des Listeners geändert werden. Die Werte werden durch die [AuthenticationSchemes-Enumeration](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) bereitgestellt: `Basic`, `Kerberos`, `Negotiate`, `None` und `NTLM`.  | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Hiermit wird das Zwischenspeichern im [Kernelmodus](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) für Antworten mit geeigneten Headern versucht.

1. Die Antwort enthält möglicherweise keine `Set-Cookie`-, `Vary`- oder `Pragma`-Header.

   Sie muss einen `Cache-Control`-Header enthalten, der vom Typ `public` ist und entweder ein `shared-max-age`- oder ein `max-age`-Wert oder ein `Expires`-Header ist.  | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Die maximale Anzahl gleichzeitiger Aufrufe. | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Die maximale Anzahl an gleichzeitigen Verbindungen, die akzeptiert werden.

   Verwenden Sie `-1`, um eine unbegrenzte Anzahl anzugeben.

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * Verwenden Sie `null`, um die computerübergreifende Einstellung der Registrierung zu verwenden. (computerweite setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Informationen hierzu finden Sie im Abschnitt <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30.000.000 Bytes
   * (~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Die maximale Anzahl von Anforderungen, die in der Warteschlange gespeichert werden kann.

   | 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Hiermit geben Sie an, ob Schreibvorgänge für Antworttext, die einen Fehler zurückgeben, weil die Verbindung zum Client getrennt wird, eine Ausnahme auslösen oder normal beendet werden.

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   (complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Hiermit machen Sie die HTTP.sys-Konfiguration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> verfügbar. Diese kann auch in der Registrierung konfiguriert werden.

   Unter folgenden API-Links finden Sie Informationen zu den einzelnen Einstellungen sowie die Standardwerte:

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. [TimeoutManager.DrainEntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) die zulässige Zeit, in der die HTTP-Server-API den Entitätstext in einer Keep-Alive-Verbindung leeren muss

   [TimeoutManager.EntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) die zulässige Zeit für die Ankunft des Anforderungsentitätstexts

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * [TimeoutManager.HeaderWait:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) die zulässige Zeit, in der die HTTP-Server-API den Anforderungsheader analysieren muss [TimeoutManager.IdleConnection:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) die zulässige Zeit für eine Verbindung im Leerlauf [TimeoutManager.MinSendBytesPerSecond:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) die Mindestsenderate für die Antwort.
   * [TimeoutManager.RequestQueue:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) die zulässige Zeit, die die Anforderung in der Anforderungswarteschlange verbleibt, bevor sie von der App übernommen wird
   * |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Geben Sie die <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> an, die bei „HTTP.sys“ registriert werden soll.
   * Besonders nützlich ist die Eigenschaft [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), mit der Sie der Sammlung ein Präfix hinzufügen können.

   Diese Eigenschaften können jederzeit vor dem Verwerfen des Listeners geändert werden.

   * |  |
     * **MaxRequestBodySize**
     * Die maximal zulässige Größe eines Anforderungstexts in Bytes.
     * Wenn dieser Wert auf `null` festgelegt wird, ist die maximale Größe für Anforderungstexte unbegrenzt.
   * Dieser Grenzwert wirkt sich nicht auf Verbindungen aus, für die ein Upgrade durchgeführt wurde – diese sind immer unbegrenzt.
     * Die empfohlene Methode zum Überschreiben des Grenzwerts in einer ASP.NET Core-MVC-App für ein einzelnes `IActionResult` besteht im Verwenden von <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in einer Aktionsmethode:
     * Eine Ausnahme wird ausgelöst, wenn die App versucht, den Grenzwert einer Anforderung zu konfigurieren, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   Sie können eine `IsReadOnly`-Eigenschaft verwenden, um darauf hinzuweisen, dass sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, der Grenzwert also nicht mehr konfiguriert werden kann.

   * Wenn die App <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> pro Anforderung außer Kraft setzen sollte, verwenden Sie das <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:
   * Wenn Sie Visual Studio verwenden, stellen Sie sicher, dass die App nicht für die Ausführung von IIS oder IIS Express konfiguriert ist.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   In Visual Studio ist das Standardstartprofil auf IIS Express ausgerichtet.

   Wenn das Projekt als Konsolen-App ausgeführt werden soll, ändern Sie das ausgewählte Profil manuell, wie im folgenden Screenshot dargestellt:

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Auswählen des Profils der App-Konsole

   * Konfigurieren von Windows Server
   * Bestimmen Sie die Ports, die für die App geöffnet werden sollen, und verwenden Sie die [Windows-Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) oder das PowerShell-Cmdlet [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule), um Firewall-Ports zu öffnen, damit der Datenverkehr HTTP.sys erreichen kann.

1. In den folgenden Befehlen und der Appkonfiguration wird Port 443 verwendet.

   Öffnen Sie bei der Bereitstellung für eine Azure-VM die Ports in der [Netzwerksicherheitsgruppe](/azure/virtual-machines/windows/nsg-quickstart-portal). In den folgenden Befehlen und der Appkonfiguration wird Port 443 verwendet.

   Beziehen Sie X.509-Zertifikate und installieren Sie sie, falls erforderlich. Erstellen Sie unter Windows selbstsignierte Zertifikate mit dem [PowerShell-Cmdlet „New-SelfSignedCertificate“](/powershell/module/pkiclient/new-selfsignedcertificate).

   Ein nicht unterstütztes Beispiel finden Sie unter [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1). Installieren Sie entweder selbstsignierte oder CA-signierte Zertifikate im Speicher **Lokaler Server** > **Persönlich** des Servers.

   ![Wenn es sich bei der App um eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) handelt, installieren Sie .NET Core, .NET Framework oder beides (wenn es sich um eine .NET Core-App für das .NET Framework handelt).](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>**.NET Core**: Wenn die App .NET Core erfordert, rufen Sie den Installer für die **.NET Core Runtime** über [.NET Core-Downloads](https://dotnet.microsoft.com/download) ab, und führen Sie ihn aus.

Installieren Sie nicht das vollständige SDK auf dem Server. **.NET Framework**: Erfordert die App .NET Framework, rufen Sie das [.NET Framework-Installationshandbuch](/dotnet/framework/install/) auf.

## <a name="additional-resources"></a>Installieren Sie das erforderliche .NET Framework.

* Der Installer für das neueste .NET Framework steht auf der Seite [.NET Core-Downloads](https://dotnet.microsoft.com/download) zur Verfügung.
* Wenn die App eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) ist, enthält die App die Runtime in ihrer Bereitstellung.
* Es ist keine Frameworkinstallation auf dem Server erforderlich.
* Konfigurieren Sie URLs und Ports in der App.
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden. Zum Konfigurieren von URL-Präfixen und Ports stehen folgende Optionen zur Verfügung:

> [!IMPORTANT]
> Befehlszeilenargument `urls`

Umgebungsvariable `ASPNETCORE_URLS`

* Das folgende Codebeispiel zeigt, wie Sie <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> mit der lokalen IP-Adresse `10.0.0.4` des Servers auf Port 443 verwenden:
* Ein Vorteil von `UrlPrefixes` besteht darin, dass bei falsch formatierten Präfixen sofort eine Fehlermeldung generiert wird.
* Die Einstellungen in `UrlPrefixes` überschreiben die Einstellungen für `UseUrls`/`urls`/`ASPNETCORE_URLS`.
* Daher bieten `UseUrls`, `urls` und die Umgebungsvariable `ASPNETCORE_URLS` den Vorteil, dass sie den Wechsel zwischen Kestrel und HTTP.sys vereinfachen.
* HTTP.sys verwendet die [HTTP Server-API-UrlPrefix-Zeichenfolgenformate](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).
* Allgemeine Platzhalterbindungen (`http://*:80/` und `http://+:80`) dürfen **nicht** verwendet werden.
* Platzhalterbindungen auf oberster Ebene gefährden die Sicherheit Ihrer App.

Dies gilt für starke und schwache Platzhalter.

* Verwenden Sie statt Platzhaltern explizite Hostnamen oder IP-Adressen.
* Platzhalterbindungen in untergeordneten Domänen (z.B. `*.mysub.com`) stellen kein Sicherheitsrisiko dar, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist).

Weitere Informationen finden Sie unter [RFC 7230: Abschnitt 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).

## <a name="when-to-use-httpsys"></a>URL-Präfixe können vorab auf dem Server registriert werden.

Das integrierte Tool für die Konfiguration von HTTP.sys ist *netsh.exe*.

* Mithilfe von *netsh.exe* können Sie URL-Präfixe reservieren und X.509-Zertifikate zuweisen.

  ![Das Tool erfordert Administratorrechte.](httpsys/_static/httpsys-to-internet.png)

* Verwenden Sie das Tool *netsh.exe*, um die URLs für die App zu registrieren:

  ![`<URL>`: Der vollqualifizierte Uniform Resource Locator (URL).](httpsys/_static/httpsys-to-internal.png)

Verwenden Sie keine Platzhalterbindung. Verwenden Sie einen gültigen Hostnamen oder eine gültige lokale IP-Adresse.

## <a name="http2-support"></a>*Die URL muss einen nachgestellten Schrägstrich enthalten.*

`<USER>`: gibt den Benutzer oder den Benutzergruppennamen an

* Im folgenden Beispiel ist die lokale IP-Adresse des Servers `10.0.0.4`:
* Wenn eine URL registriert ist, antwortet das Tool mit `URL reservation successfully added`.
* Verwenden Sie zum Löschen einer registrierten URL den Befehl `delete urlacl`:

Registrieren Sie X.509-Zertifikate auf dem Server.

Verwenden Sie das Tool *netsh.exe*, um Zertifikate für die App zu registrieren: `<IP>`: gibt die lokale IP-Adresse für die Bindung an. Verwenden Sie keine Platzhalterbindung.

## <a name="kernel-mode-authentication-with-kerberos"></a>Verwenden Sie eine gültige IP-Adresse.

`<PORT>`: gibt den Port für die Bindung an `<THUMBPRINT>`: der Zertifikatsfingerabdruck X.509 `<GUID>`: eine vom Entwickler generierte GUID zur Darstellung der App zu Informationszwecken Speichern Sie die GUID zu Referenzzwecken in der App als Paket-Tag:

## <a name="how-to-use-httpsys"></a>In Visual Studio:

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Öffnen Sie die Projekteigenschaften der App, indem Sie mit der rechten Maustaste auf die App im **Projektmappen-Explorer** klicken und **Eigenschaften** auswählen.

Wählen Sie die Registerkarte **Paket** aus. Geben Sie die GUID ein, die Sie im Feld **Tags** erstellt haben.

Wenn Sie Visual Studio nicht verwenden: Öffnen Sie die Projektdatei der App.

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

Fügen Sie einer neuen oder vorhandenen `<PropertyGroup>` mit der von Ihnen erstellten GUID eine `<PackageTags>`-Eigenschaft hinzu:

Im folgenden Beispiel:

| Die lokale IP-Adresse des Servers lautet `10.0.0.4`. | Ein zufälliger Online-GUID-Generator stellt den Wert `appid` bereit. | Wenn ein Zertifikat registriert ist, antwortet das Tool mit `SSL Certificate successfully added`. |
| -------- | ----------- | :-----: |
| Verwenden Sie zum Löschen einer Zertifikatsregistrierung den Befehl `delete sslcert`: | Referenzdokumentation für *netsh.exe*: | `true` |
| [Netsh Commands for Hypertext Transfer Protocol (HTTP) (Netsh-Befehle für Hypertext Transfer-Protokolle (HTTP))](https://technet.microsoft.com/library/cc725882.aspx) | [UrlPrefix Strings (UrlPrefix-Zeichenfolgen)](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx) | `true` |
| Führen Sie die App aus. | Administratorberechtigungen sind nicht erforderlich, um die App auszuführen, wenn die Verbindung zum lokalen Host über HTTP (nicht HTTPS) mit einer Portnummer größer als 1024 erfolgt. Für andere Konfigurationen (z.B. über eine lokale IP-Adresse oder Bindung an Port 443) führen Sie die App mit Administratorberechtigungen aus. Die App antwortet auf die öffentliche IP-Adresse des Servers. | `None` |
| In diesem Beispiel wird der Server aus dem Internet mit seiner öffentlichen IP-Adresse `104.214.79.47` erreicht. | In diesem Beispiel wird ein Entwicklungszertifikat verwendet. Die Seite wird sicher geladen, nachdem die Warnung vor nicht vertrauenswürdigen Zertifikaten des Browsers umgangen wurde. Browserfenster mit Anzeige der geladenen Indexseite der App | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Proxyserver und Lastenausgleichsszenarien | Für Apps, die von HTTP.sys gehostet werden und mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren, ist möglicherweise eine zusätzliche Konfiguration erforderlich, wenn sie hinter Proxyservern und Lastenausgleichsmodulen hosten. |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer). Zusätzliche Ressourcen [Aktivieren der Windows-Authentifizierung mit HTTP.sys](xref:security/authentication/windowsauth#httpsys) | `null`<br>[HTTP-Server-API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)<br>[aspnet/HttpSysServer – GitHub-Repository (Quellcode)](https://github.com/aspnet/HttpSysServer/) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | [Der Host](xref:fundamentals/index#host) | [HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) ist ein [Webserver für ASP.NET Core](xref:fundamentals/servers/index), der nur unter Windows ausgeführt werden kann.<br>HTTP.sys stellt eine Alternative zum [Kestrel](xref:fundamentals/servers/kestrel)-Server dar und bietet einige Features, die in Kestrel fehlen. |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | HTTP.sys ist nicht mit dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) kompatibel und kann nicht mit IIS oder IIS Express verwendet werden. | HTTP.sys unterstützt die folgenden Features: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | [Windows-Authentifizierung](xref:security/authentication/windowsauth) | `false`<br>Portfreigabe |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | HTTPS mit SNI HTTP/2 über TLS (Windows 10 oder höher)<ul><li>Direkte Dateiübertragung</li><li>Zwischenspeichern von Antworten</li><li>WebSockets (Windows 8 oder höher)</li><li>Unterstützte Windows-Versionen:</li><li>Windows 7 oder höher</li><li>Windows Server 2008 R2 oder höher</li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | [Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)) Empfohlene Verwendung von HTTP.sys HTTP.sys eignet sich für Bereitstellungen, auf die Folgendes zutrifft: |  |

<a name="maxrequestbodysize"></a>

Sie müssen den Server ohne IIS direkt mit dem Internet verbinden.

HTTP.sys kommuniziert direkt mit dem Internet Eine interne Bereitstellung erfordert ein Feature, das in Kestrel nicht verfügbar ist, z.B. die [Windows-Authentifizierung](xref:security/authentication/windowsauth). HTTP.sys kommuniziert direkt mit dem internen Netzwerk

Bei HTTP.sys handelt es sich um eine ausgereifte Technologie, die Schutz vor vielen Arten von Angriffen bietet und zudem die Stabilität, Sicherheit und Skalierbarkeit eines Webservers mit vollem Funktionsumfang bereitstellt.

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

IIS selbst wird auf HTTP.sys als HTTP-Listener ausgeführt. HTTP/2-Unterstützung

[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps aktiviert, wenn die folgenden Basisanforderungen erfüllt sind:

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

Windows Server 2016/Windows 10 oder höher

[ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation) TLS 1.2-Verbindung oder höher

![Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)`HTTP/1.1`.](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>HTTP/2 ist standardmäßig aktiviert.

1. Für die Verbindung wird ein Fallback auf HTTP/1.1 ausgeführt, wenn keine HTTP/2-Verbindung hergestellt wurde. In einer zukünftigen Version von Windows werden HTTP/2-Konfigurationsflags verfügbar sein, einschließlich der Möglichkeit, HTTP/2 mit HTTP.sys zu deaktivieren.

1. Kernelmodusauthentifizierung mit Kerberos HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll.

1. Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt.

   Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird. Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App.

   Empfohlene Verwendung von HTTP.sys

1. Konfigurieren der ASP.NET Core-App für die Verwendung von HTTP.sys

   * Bei Verwendung des [Microsoft.AspNetCore.App-Metapakets](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) ist kein Paketverweis in der Projektdatei erforderlich. Wenn das `Microsoft.AspNetCore.App`-Metapaket nicht verwendet wird, fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) hinzu.
   * Rufen Sie die Erweiterungsmethode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> auf, wenn Sie den Host erstellen, und geben Sie dabei alle erforderlichen <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>-Objekte an. Im folgenden Beispiel werden Optionen auf ihre Standardwerte festgelegt: Die weitere Konfiguration von HTTP.sys erfolgt über [Registrierungseinstellungen](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **HTTP.sys-Optionen** Eigenschaft

1. Beschreibung

   Standard title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * 'Blazor'
   * 'Identity'
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   'Let's Encrypt'

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   'Razor'

   'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

   'Blazor'

   > [!WARNING]
   > 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: ---- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

1. 'Identity'

   'Let's Encrypt' 'Razor' 'SignalR' uid:

   title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'
   * 'SignalR' uid:

   title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   'Blazor'

   'Identity'

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. 'Let's Encrypt'

   'Razor'

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * 'SignalR' uid: ------ | :-----: | | [AllowSynchronousIO](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | Hiermit steuern Sie, ob eine synchrone Eingabe/Ausgabe für `HttpContext.Request.Body` und `HttpContext.Response.Body` zulässig ist.  | `true` | | [Authentication.AllowAnonymous](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | Hiermit erlauben Sie anonyme Anforderungen.
   *  | `true` | | [Authentication.Schemes](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | Hiermit geben Sie die zulässigen Authentifizierungsschemas an.
   * Diese Eigenschaft kann jederzeit vor dem Verwerfen des Listeners geändert werden.
   * Die Werte werden durch die [AuthenticationSchemes-Enumeration](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes) bereitgestellt: `Basic`, `Kerberos`, `Negotiate`, `None` und `NTLM`.

    | `None` | | [EnableResponseCaching](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | Hiermit wird das Zwischenspeichern im [Kernelmodus](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) für Antworten mit geeigneten Headern versucht.

   * Die Antwort enthält möglicherweise keine `Set-Cookie`-, `Vary`- oder `Pragma`-Header.
     * Sie muss einen `Cache-Control`-Header enthalten, der vom Typ `public` ist und entweder ein `shared-max-age`- oder ein `max-age`-Wert oder ein `Expires`-Header ist.
     *  | `true` | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | Die maximale Anzahl gleichzeitiger Aufrufe.
     * | 5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | Die maximale Anzahl an gleichzeitigen Verbindungen, die akzeptiert werden.
   * Verwenden Sie `-1`, um eine unbegrenzte Anzahl anzugeben.
     * Verwenden Sie `null`, um die computerübergreifende Einstellung der Registrierung zu verwenden.
     * (computerweite

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   setting) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | Informationen hierzu finden Sie im Abschnitt <a href="#maxrequestbodysize">MaxRequestBodySize</a>.

   * | 30.000.000 Bytes
   * (~28.6 MB) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | Die maximale Anzahl von Anforderungen, die in der Warteschlange gespeichert werden kann.

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   | 1000 | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | Hiermit geben Sie an, ob Schreibvorgänge für Antworttext, die einen Fehler zurückgeben, weil die Verbindung zum Client getrennt wird, eine Ausnahme auslösen oder normal beendet werden.

   (complete normally) | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | Hiermit machen Sie die HTTP.sys-Konfiguration <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> verfügbar. Diese kann auch in der Registrierung konfiguriert werden.

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   Unter folgenden API-Links finden Sie Informationen zu den einzelnen Einstellungen sowie die Standardwerte:

   * [TimeoutManager.DrainEntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody) die zulässige Zeit, in der die HTTP-Server-API den Entitätstext in einer Keep-Alive-Verbindung leeren muss
   * [TimeoutManager.EntityBody:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody) die zulässige Zeit für die Ankunft des Anforderungsentitätstexts

1. [TimeoutManager.HeaderWait:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait) die zulässige Zeit, in der die HTTP-Server-API den Anforderungsheader analysieren muss

   [TimeoutManager.IdleConnection:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection) die zulässige Zeit für eine Verbindung im Leerlauf [TimeoutManager.MinSendBytesPerSecond:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond) die Mindestsenderate für die Antwort.

   [TimeoutManager.RequestQueue:](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue) die zulässige Zeit, die die Anforderung in der Anforderungswarteschlange verbleibt, bevor sie von der App übernommen wird |  | | <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | Geben Sie die <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> an, die bei „HTTP.sys“ registriert werden soll.

   Besonders nützlich ist die Eigenschaft [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), mit der Sie der Sammlung ein Präfix hinzufügen können. Diese Eigenschaften können jederzeit vor dem Verwerfen des Listeners geändert werden.

   ![|  |](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a>**MaxRequestBodySize**

Die maximal zulässige Größe eines Anforderungstexts in Bytes. Wenn dieser Wert auf `null` festgelegt wird, ist die maximale Größe für Anforderungstexte unbegrenzt.

## <a name="additional-resources"></a>Dieser Grenzwert wirkt sich nicht auf Verbindungen aus, für die ein Upgrade durchgeführt wurde – diese sind immer unbegrenzt.

* Die empfohlene Methode zum Überschreiben des Grenzwerts in einer ASP.NET Core-MVC-App für ein einzelnes `IActionResult` besteht im Verwenden von <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> in einer Aktionsmethode:
* Eine Ausnahme wird ausgelöst, wenn die App versucht, den Grenzwert einer Anforderung zu konfigurieren, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat.
* Sie können eine `IsReadOnly`-Eigenschaft verwenden, um darauf hinzuweisen, dass sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, der Grenzwert also nicht mehr konfiguriert werden kann.
* Wenn die App <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> pro Anforderung außer Kraft setzen sollte, verwenden Sie das <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:
* <xref:test/troubleshoot>

::: moniker-end
