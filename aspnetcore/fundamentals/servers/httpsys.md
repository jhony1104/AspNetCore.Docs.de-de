---
title: Implementierung des Http.sys-Webservers in ASP.NET Core
author: guardrex
description: Erfahren Sie mehr über HTTP.sys. einen Webserver für ASP.NET Core unter Windows. HTTP.sys basiert auf dem HTTP.sys-Kernelmodustreiber, stellt eine Alternative zu Kestrel dar und kann zum Herstellen einer direkten Verbindung mit dem Internet ohne Internetinformationsdienste (IIS) verwendet werden.
monikerRange: '>= aspnetcore-2.0'
ms.author: tdykstra
ms.custom: mvc
ms.date: 12/18/2018
uid: fundamentals/servers/httpsys
ms.openlocfilehash: a779fee53109d4c1cabb2005896e757f23467540
ms.sourcegitcommit: 816f39e852a8f453e8682081871a31bc66db153a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/19/2018
ms.locfileid: "53637624"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a>Implementierung des Http.sys-Webservers in ASP.NET Core

Von [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher) und [Luke Latham](https://github.com/guardrex)

[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) ist ein [Webserver für ASP.NET Core](xref:fundamentals/servers/index), der nur unter Windows ausgeführt werden kann. HTTP.sys stellt eine Alternative zum [Kestrel](xref:fundamentals/servers/kestrel)-Server dar und bietet einige Features, die in Kestrel fehlen.

> [!IMPORTANT]
> HTTP.sys ist nicht mit dem [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) kompatibel und kann nicht mit IIS oder IIS Express verwendet werden.

HTTP.sys unterstützt die folgenden Features:

* [Windows-Authentifizierung](xref:security/authentication/windowsauth)
* Portfreigabe
* HTTPS mit SNI
* HTTP/2 über TLS (Windows 10 oder höher)
* Direkte Dateiübertragung
* Zwischenspeichern von Antworten
* WebSockets (Windows 8 oder höher)

Unterstützte Windows-Versionen:

* Windows 7 oder höher
* Windows Server 2008 R2 oder höher

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="when-to-use-httpsys"></a>Empfohlene Verwendung von HTTP.sys

HTTP.sys eignet sich für Bereitstellungen, auf die Folgendes zutrifft:

* Sie müssen den Server ohne IIS direkt mit dem Internet verbinden.

  ![HTTP.sys kommuniziert direkt mit dem Internet](httpsys/_static/httpsys-to-internet.png)

* Eine interne Bereitstellung erfordert ein Feature, das in Kestrel nicht verfügbar ist, z.B. die [Windows-Authentifizierung](xref:security/authentication/windowsauth).

  ![HTTP.sys kommuniziert direkt mit dem internen Netzwerk](httpsys/_static/httpsys-to-internal.png)

Bei HTTP.sys handelt es sich um eine ausgereifte Technologie, die Schutz vor vielen Arten von Angriffen bietet und zudem die Stabilität, Sicherheit und Skalierbarkeit eines Webservers mit vollem Funktionsumfang bereitstellt. IIS selbst wird auf HTTP.sys als HTTP-Listener ausgeführt.

## <a name="http2-support"></a>HTTP/2-Unterstützung

[HTTP/2](https://httpwg.org/specs/rfc7540.html) ist für ASP.NET Core-Apps aktiviert, wenn die folgenden Basisanforderungen erfüllt sind:

* Windows Server 2016/Windows 10 oder höher
* [ALPN](https://tools.ietf.org/html/rfc7301#section-3)-Verbindung (Application-Layer Protocol Negotiation)
* TLS 1.2-Verbindung oder höher

::: moniker range=">= aspnetcore-2.2"

Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1`.

::: moniker-end

HTTP/2 ist standardmäßig aktiviert. Für die Verbindung wird ein Fallback auf HTTP/1.1 ausgeführt, wenn keine HTTP/2-Verbindung hergestellt wurde. In einer zukünftigen Version von Windows werden HTTP/2-Konfigurationsflags verfügbar sein, einschließlich der Möglichkeit, HTTP/2 mit HTTP.sys zu deaktivieren.

## <a name="kernel-mode-authentication-with-kerberos"></a>Kernelmodusauthentifizierung mit Kerberos

HTTP.sys delegiert zur Kernelmodusauthentifizierung mit dem Kerberos-Authentifizierungsprotokoll. Die Benutzermodusauthentifizierung wird nicht von Kerberos und HTTP.sys unterstützt. Das Computerkonto muss zum Entschlüsseln des Kerberos-Tokens/-Tickets verwendet werden, das von Active Directory abgerufen und zur Authentifizierung des Benutzers vom Client an den Server weitergeleitet wird. Registrieren Sie den Dienstprinzipalnamen (SPN) für den Host, nicht für den Benutzer der App.

## <a name="how-to-use-httpsys"></a>Empfohlene Verwendung von HTTP.sys

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a>Konfigurieren der ASP.NET Core-App für die Verwendung von HTTP.sys

1. Bei Verwendung des [Microsoft.AspNetCore.App-Metapakets](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) (ASP.NET Core 2.1 oder höher) ist kein Paketverweis in der Projektdatei erforderlich. Wenn das `Microsoft.AspNetCore.App`-Metapaket nicht verwendet wird, fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/) hinzu.

2. Rufen Sie die Erweiterungsmethode [UseHttpSys](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderhttpsysextensions.usehttpsys) auf, wenn Sie den Webhost erstellen, und geben Sie dabei alle erforderlichen [HTTP.sys-Optionen](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions) an:

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=4-12)]

   Die weitere Konfiguration von HTTP.sys erfolgt über [Registrierungseinstellungen](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).

   **HTTP.sys-Optionen**

   | Eigenschaft | Beschreibung | Standard |
   | -------- | ----------- | :-----: |
   | [AllowSynchronousIO](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.allowsynchronousio) | Hiermit steuern Sie, ob eine synchrone Eingabe/Ausgabe für `HttpContext.Request.Body` und `HttpContext.Response.Body` zulässig ist. | `true` |
   | [Authentication.AllowAnonymous](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.allowanonymous) | Hiermit lassen Sie anonyme Anforderungen zu. | `true` |
   | [Authentication.Schemes](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationmanager.schemes) | Hiermit geben Sie die zulässigen Authentifizierungsschemas an. Diese Eigenschaft kann jederzeit vor dem Verwerfen des Listeners geändert werden. Die Werte werden durch die [AuthenticationSchemes-Enumeration](/dotnet/api/microsoft.aspnetcore.server.httpsys.authenticationschemes) bereitgestellt: `Basic`, `Kerberos`, `Negotiate`, `None` und `NTLM`. | `None` |
   | [EnableResponseCaching](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.enableresponsecaching) | Hiermit wird das Caching im [Kernelmodus](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) für Antworten mit geeigneten Headern versucht. Die Antwort enthält möglicherweise keine `Set-Cookie`-, `Vary`- oder `Pragma`-Header. Sie muss einen `Cache-Control`-Header enthalten, der vom Typ `public` ist und entweder ein `shared-max-age`- oder ein `max-age`-Wert oder ein `Expires`-Header ist. | `true` |
   | [MaxAccepts](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxaccepts) | Die maximale Anzahl gleichzeitiger Aufrufe. | 5 &times; [Environment.<br>ProcessorCount](/dotnet/api/system.environment.processorcount) |
   | [MaxConnections](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxconnections) | Die maximale Anzahl an gleichzeitigen Verbindungen, die akzeptiert werden. Verwenden Sie `-1`, um eine unbegrenzte Anzahl anzugeben. Verwenden Sie `null`, um die computerübergreifende Einstellung der Registrierung zu verwenden. | `null`<br>(unbegrenzt) |
   | [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) | Informationen hierzu finden Sie im Abschnitt <a href="#maxrequestbodysize">MaxRequestBodySize</a>. | 30.000.000 Bytes<br>(~28,6 MB) |
   | [RequestQueueLimit](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.requestqueuelimit) | Die maximale Anzahl von Anforderungen, die in der Warteschlange gespeichert werden kann. | 1000 |
   | [ThrowWriteExceptions](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.throwwriteexceptions) | Hiermit geben Sie an, ob Schreibvorgänge für Antworttext, die einen Fehler zurückgeben, weil die Verbindung zum Client getrennt wird, eine Ausnahme auslösen oder normal beendet werden. | `false`<br>(normal beenden) |
   | [Timeouts](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.timeouts) | Machen Sie die HTTP.sys-Konfiguration [TimeoutManager](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager) verfügbar. Diese kann auch in der Registrierung konfiguriert werden. Unter folgenden API-Links finden Sie Informationen zu den einzelnen Einstellungen sowie die Standardwerte:<ul><li>[TimeoutManager.DrainEntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.drainentitybody) &ndash; Die zulässige Zeit, in der die HTTP-Server-API den Entitätskörper in einer Keep-Alive-Verbindung leeren muss.</li><li>[TimeoutManager.EntityBody](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.entitybody) &ndash; Die zulässige Zeit, in der der Entitätskörper der Anforderung eintreffen muss.</li><li>[TimeoutManager.HeaderWait](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.headerwait) &ndash; Die zulässige Zeit, in der die HTTP-Server-API den Anforderungsheader analysieren muss.</li><li>[TimeoutManager.IdleConnection](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.idleconnection) &ndash; Die zulässige Zeit für eine Verbindung im Leerlauf.</li><li>[TimeoutManager.MinSendBytesPerSecond](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.minsendbytespersecond) &ndash; Die Mindestsenderate für die Antwort.</li><li>[TimeoutManager.RequestQueue](/dotnet/api/microsoft.aspnetcore.server.httpsys.timeoutmanager.requestqueue) &ndash; Die zulässige Zeit, die die Anforderung in der Anforderungswarteschlange verbleibt, bevor sie von der App übernommen wird.</li></ul> |  |
   | [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes) | Hiermit geben Sie die [UrlPrefixCollection](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection) an, die mit HTTP.sys registriert wird. Besonders nützlich ist die Eigenschaft [UrlPrefixCollection.Add](/dotnet/api/microsoft.aspnetcore.server.httpsys.urlprefixcollection.add), mit der Sie der Sammlung ein Präfix hinzufügen können. Diese Eigenschaften können jederzeit vor dem Verwerfen des Listeners geändert werden. |  |

   <a name="maxrequestbodysize"></a>
   **MaxRequestBodySize**

   Die maximal zulässige Größe eines Anforderungstexts in Bytes. Wenn dieser Wert auf `null` festgelegt wird, ist die maximale Größe für Anforderungstexte unbegrenzt. Dieser Grenzwert wirkt sich nicht auf Verbindungen aus, für die ein Upgrade durchgeführt wurde – diese sind immer unbegrenzt.

   Die empfohlene Methode zum Überschreiben des Grenzwerts in einer ASP.NET Core-MVC-App für ein einzelnes `IActionResult` besteht im Verwenden von [RequestSizeLimitAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requestsizelimitattribute) in einer Aktionsmethode:

   ```csharp
   [RequestSizeLimit(100000000)]
   public IActionResult MyActionMethod()
   ```

   Eine Ausnahme wird ausgelöst, wenn die App versucht, den Grenzwert einer Anforderung zu konfigurieren, nachdem die App bereits mit dem Lesen der Anforderung begonnen hat. Sie können eine `IsReadOnly`-Eigenschaft verwenden, um darauf hinzuweisen, dass sich die `MaxRequestBodySize`-Eigenschaft im schreibgeschützten Zustand befindet, der Grenzwert also nicht mehr konfiguriert werden kann.

   Wenn Sie App [MaxRequestBodySize](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.maxrequestbodysize) pro Anforderung überschreiben soll, verwenden Sie [IHttpMaxRequestBodySizeFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpmaxrequestbodysizefeature):

   [!code-csharp[](httpsys/sample/Startup.cs?name=snippet1&highlight=6-7)]

3. Wenn Sie Visual Studio verwenden, stellen Sie sicher, dass die App nicht für die Ausführung von IIS oder IIS Express konfiguriert ist.

   In Visual Studio ist das Standardstartprofil auf IIS Express ausgerichtet. Wenn das Projekt als Konsolen-App ausgeführt werden soll, ändern Sie das ausgewählte Profil manuell, wie im folgenden Screenshot dargestellt:

   ![Auswählen des Profils der App-Konsole](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a>Konfigurieren von Windows Server

1. Wenn es sich bei der App um eine [frameworkabhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd) handelt, installieren Sie .NET Core, .NET Framework oder beides (wenn es sich um eine .NET Core-App für das .NET Framework handelt).

   * **.NET Core** &ndash;: Wenn die App .NET Core erfordert, rufen Sie das .NET Core-Installationsprogramm über die [.NET-Seite „All Downloads“](https://www.microsoft.com/net/download/all) ab, und führen Sie es aus.
   * **.NET Framework**: Erfordert die App .NET Framework, wechseln Sie zum [.NET Framework: Installationshandbuch](/dotnet/framework/install/), um die Installationsanweisungen zu lesen. Installieren Sie das erforderliche .NET Framework. Das Installationsprogramm für die neueste .NET Framework-Version finden Sie auf der [.NET-Seite „Alle Downloads“](https://www.microsoft.com/net/download/all).

2. Konfigurieren Sie URLs und Ports für die App.

   Standardmäßig ist ASP.NET Core an `http://localhost:5000` gebunden. Zum Konfigurieren von URL-Präfixen und Ports stehen folgende Optionen zur Verfügung:

   * [UseUrls](/dotnet/api/microsoft.aspnetcore.hosting.hostingabstractionswebhostbuilderextensions.useurls)
   * Befehlszeilenargument `urls`
   * Umgebungsvariable `ASPNETCORE_URLS`
   * [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes)

   Das folgende Codebeispiel veranschaulicht die Verwendung von [UrlPrefixes](/dotnet/api/microsoft.aspnetcore.server.httpsys.httpsysoptions.urlprefixes):

   [!code-csharp[](httpsys/sample/Program.cs?name=snippet1&highlight=11)]

   Ein Vorteil von `UrlPrefixes` besteht darin, dass bei falsch formatierten Präfixen sofort eine Fehlermeldung generiert wird.

   Die Einstellungen in `UrlPrefixes` überschreiben die Einstellungen für `UseUrls`/`urls`/`ASPNETCORE_URLS`. Daher bieten `UseUrls`, `urls` und die Umgebungsvariable `ASPNETCORE_URLS` den Vorteil, dass sie den Wechsel zwischen Kestrel und HTTP.sys vereinfachen. Weitere Informationen zu `UseUrls`, `urls` und `ASPNETCORE_URLS` finden Sie unter [Hosten in ASP.NET Core](xref:fundamentals/host/index).

   HTTP.sys verwendet die [HTTP Server-API-UrlPrefix-Zeichenfolgenformate](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx).

   > [!WARNING]
   > Allgemeine Platzhalterbindungen (`http://*:80/` und `http://+:80`) dürfen **nicht** verwendet werden. Platzhalterbindungen auf oberster Ebene gefährden die Sicherheit Ihrer App. Dies gilt für starke und schwache Platzhalter. Verwenden Sie statt Platzhaltern explizite Hostnamen. Platzhalterbindungen in untergeordneten Domänen (z.B. `*.mysub.com`) verursachen kein Sicherheitsrisiko, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist). Weitere Informationen finden Sie unter [rfc7230 im Abschnitt 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

3. Registrieren Sie URL-Präfixe vorab, um sie an HTTP.sys zu binden, und richten Sie X.509-Zertifikate ein.

   Wenn URL-Präfixe nicht vorab in Windows registriert wurden, führen Sie die App mit Administratorrechten aus. Die einzige Ausnahme besteht dann, wenn die Bindung an „localhost“ über HTTP (nicht HTTPS) mit einer höheren Portnummer als 1024 erfolgt. In diesem Fall sind keine Administratorrechte erforderlich.

   1. Das integrierte Tool für die Konfiguration von HTTP.sys ist *netsh.exe*. Mithilfe von *netsh.exe* können Sie URL-Präfixe reservieren und X.509-Zertifikate zuweisen. Das Tool erfordert Administratorrechte.

      Das folgende Beispiel zeigt die Befehle, mit denen URL-Präfixe für die Ports 80 und 443 reserviert werden:

      ```console
      netsh http add urlacl url=http://+:80/ user=Users
      netsh http add urlacl url=https://+:443/ user=Users
      ```

      Das folgende Beispiel zeigt, wie Sie ein X.509-Zertifikat zuweisen:

      ```console
      netsh http add sslcert ipport=0.0.0.0:443 certhash=MyCertHash_Here appid="{00000000-0000-0000-0000-000000000000}"
      ```

      Referenzdokumentation für *netsh.exe*:

      * [Netsh Commands for Hypertext Transfer Protocol (HTTP) (Netsh-Befehle für Hypertext Transfer-Protokolle (HTTP))](https://technet.microsoft.com/library/cc725882.aspx)
      * [UrlPrefix Strings (UrlPrefix-Zeichenfolgen)](https://msdn.microsoft.com/library/windows/desktop/aa364698.aspx)

   2. Erstellen Sie bei Bedarf selbstsignierte X.509-Zertifikate.

      [!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

4. Öffnen Sie Firewallports, damit der Datenverkehr HTTP.sys erreicht. Verwenden Sie *netsh.exe* oder [PowerShell-Cmdlets](https://technet.microsoft.com/library/jj554906).

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxyserver und Lastenausgleichsszenarien

Für Apps, die von HTTP.sys gehostet werden und mit Anforderungen aus dem Internet oder einem Unternehmensnetzwerk interagieren, ist möglicherweise eine zusätzliche Konfiguration erforderlich, wenn sie hinter Proxyservern und Lastenausgleichsmodulen hosten. Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [HTTP-Server-API](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)
* [aspnet/HttpSysServer – GitHub-Repository (Quellcode)](https://github.com/aspnet/HttpSysServer/)
* <xref:fundamentals/host/index>
* <xref:test/troubleshoot>
