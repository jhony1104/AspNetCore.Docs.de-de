---
title: Erzwingen von HTTPS in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie HTTPS/TLS in einer ASP.NET Core-Web-app benötigen.
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2018
uid: security/enforcing-ssl
ms.openlocfilehash: 16cfa672fe4a81d9e8f09fc3dd1e6c036edd4c4e
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208975"
---
# <a name="enforce-https-in-aspnet-core"></a>Erzwingen von HTTPS in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Dieses Dokument zeigt, wie Sie:

* HTTPS für alle Anforderungen erforderlich.
* Alle HTTP-Anforderungen auf HTTPS umleiten.

Keine API kann verhindern, dass einen Client sensible Daten bei der ersten Anforderung senden.

> [!WARNING]
> Führen Sie **nicht** verwenden [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) für Web-APIs, die vertraulichen Informationen zu erhalten. `RequireHttpsAttribute` leitet Browsers per HTTP-Statuscode von HTTP an HTTPS weiter. API-Clients verstehen diese Codes möglicherweise nicht, oder Sie führen keine Weiterleitung von HTTP an HTTPS durch. Dies kann dazu führen, dass solche Clients Daten unverschlüsselt mittels HTTP versenden. Web-APIs sollten daher entweder:
>
> * nicht auf HTTP lauschen oder
> * die Verbindung mit dem Statuscode 400 („Ungültige Anforderung“) schließen und die Anforderung nicht verarbeiten.

## <a name="require-https"></a>Erforderlichkeit von HTTPS

::: moniker range=">= aspnetcore-2.1"

Es wird empfohlen, Produktion ASP.NET Core Web-apps-Aufruf:

* HTTPS-Umleitung-Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) HTTP-Anforderungen zu HTTPS umleiten.
* HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)), HTTP Strict Transport Security Protocol (HSTS)-Header für Clients zu senden.

> [!NOTE]
> In einer Reverseproxykonfiguration bereitgestellte Apps ermöglichen die Proxys, für die verbindungssicherheit (HTTPS) behandelt. Wenn der Proxy auch HTTPS-Umleitung behandelt wird, besteht keine Notwendigkeit zur Verwendung von HTTPS-Umleitung-Middleware. Wenn der Proxyserver verarbeitet auch das Schreiben von HSTS-Header (z. B. [native HSTS zu unterstützen, in IIS 10.0 (1709) oder höher](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware nicht erforderlich, von der app. Weitere Informationen finden Sie unter [Opt-Out-of HTTPS/HSTS bei projekterstellung](#opt-out-of-httpshsts-on-project-creation).

### <a name="usehttpsredirection"></a>UseHttpsRedirection

Der folgende code ruft `UseHttpsRedirection` in die `Startup` Klasse:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=13)]

Der oben markierte Code:

* Verwendet die standardmäßige [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).
* Verwendet die standardmäßige [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null), es sei denn, durch Überschreiben der `ASPNETCORE_HTTPS_PORT` Umgebungsvariable oder [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).

Es wird empfohlen, statt temporäre umleitungen dauerhafte umleitungen. Die Zwischenspeicherung von Ressourcenlinks kann dazu führen, dass instabiles Verhalten in entwicklungsumgebungen. Wenn Sie einen permanente Umleitungsstatuscode senden lieber, wenn die app in einer nicht-Entwicklungsumgebung ist, finden Sie unter der [dauerhafte umleitungen zu konfigurieren, in der Produktion](#configure-permanent-redirects-in-production) Abschnitt. Es wird empfohlen, [HSTS](#http-strict-transport-security-protocol-hsts) , um Clients zu signalisieren, die Ressource nur sichere Anforderungen an die app (nur in der Produktion) gesendet werden sollen.

### <a name="port-configuration"></a>Portkonfiguration

Ein Port muss für die Middleware verfügbar sein, eine unsichere Anforderung an HTTPS umgeleitet wird. Wenn kein Port verfügbar ist:

* Umleitung zu HTTPS auftreten nicht.
* Die Middleware protokolliert die Warnung "Fehler bei den Https-Port für die Umleitung zu bestimmen."

Geben Sie den HTTPS-Port mit einer der folgenden Methoden:

* Set [HttpsRedirectionOptions.HttpsPort](#options).
* Legen Sie die `ASPNETCORE_HTTPS_PORT` Umgebungsvariable oder [Https_port Webhost-Konfigurationseinstellung](xref:fundamentals/host/web-host#https-port):

  **Schlüssel**: `https_port`  
  **Typ:** *Zeichenfolge*  
  **Standard**: Es ist kein Standardwert festgelegt.  
  **Festlegen mit:** `UseSetting`  
  **Umgebungsvariable**: `<PREFIX_>HTTPS_PORT` (Das Präfix ist `ASPNETCORE_` bei Verwendung der [Webhost](xref:fundamentals/host/web-host).)

  Beim Konfigurieren einer <xref:Microsoft.AspNetCore.Hosting.IWebHostBuilder> in `Program`:

  [!code-csharp[](enforcing-ssl/sample-snapshot/Program.cs?name=snippet_Program&highlight=10)]
* Geben Sie einen Port mit dem sicheren Schema unter Verwendung der `ASPNETCORE_URLS` -Umgebungsvariablen angegeben. Die Umgebungsvariable konfiguriert den Server. Die Middleware indirekt ermittelt die HTTPS-Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>. Dieser Ansatz funktioniert nicht bei reverse-Proxy-Bereitstellungen.
* Legen Sie bei der Entwicklung eine HTTPS-URL in *"launchsettings.JSON"*. Aktivieren Sie HTTPS, bei der IIS Express verwendet wird.
* Konfigurieren Sie einen HTTPS-URL-Endpunkt für die edgebereitstellung von öffentlich zugänglichen [Kestrel](xref:fundamentals/servers/kestrel) Server oder [HTTP.sys](xref:fundamentals/servers/httpsys) Server. Nur **eine HTTPS-Port** wird von der app verwendet. Die Middleware ermittelt, den Port über <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.

> [!NOTE]
> Beim Ausführen einer app in einer Reverseproxykonfiguration <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ist nicht verfügbar. Legen Sie den Port, der mit einem der anderen in diesem Abschnitt beschriebenen Ansätze.

Wenn Kestrel oder HTTP.sys als öffentlich zugängliche Edgeserver verwendet wird, muss Kestrel oder HTTP.sys zum Lauschen an beide konfiguriert werden:

* Die, in dem der Client umgeleitet wird, sicherer Port (in der Regel Port 443 in der Produktion und 5001 in der Entwicklung).
* Der unsichere Port (üblicherweise 80 in der Produktion) und 5000 in der Entwicklung.

Der unsichere Port muss der Client erhält eine unsichere Anforderung und den Client mit dem sicheren Port umleiten, damit die app zugreifen können.

Weitere Informationen finden Sie unter [Kestrel-Endpunktkonfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration) oder <xref:fundamentals/servers/httpsys>.

### <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Jeder Firewall zwischen dem Client und Server müssen auch Kommunikationsports für den Datenverkehr zu öffnen.

Wenn die Anforderungen in einer Reverseproxykonfiguration weitergeleitet werden, verwenden Sie [Forwardedheadersmiddleware](xref:host-and-deploy/proxy-load-balancer) vor dem Aufruf von HTTPS-Umleitung-Middleware. Forwarded-Header-Middleware Updates der `Request.Scheme`unter Verwendung der `X-Forwarded-Proto` Header. Die Middleware ermöglicht umleitungs-URIs und andere Sicherheitsrichtlinien ordnungsgemäß funktionieren. Wenn Forwardedheadersmiddleware nicht verwendet wird, möglicherweise nicht die Back-End-app erhalten das richtige Schema und letztlich in eine Umleitungsschleife entsteht. Eine allgemeine Fehlermeldung für Endbenutzer ist, dass zu viele umleitungen aufgetreten sind.

Wenn Sie in Azure App Service bereitstellen möchten, befolgen Sie die Anweisungen in [Lernprogramm: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).

### <a name="options"></a>Optionen

Die folgenden hervorgehobenen Code ruft [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) middlewareoptionen konfigurieren:

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=14-99)]

Aufrufen von `AddHttpsRedirection` ist nur erforderlich, ändern Sie die Werte der `HttpsPort` oder `RedirectStatusCode`.

Der oben markierte Code:

* Legt [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) zu <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, dies ist der Standardwert. Verwenden Sie die Felder von der <xref:Microsoft.AspNetCore.Http.StatusCodes> -Klasse für Zuweisungen zu `RedirectStatusCode`.
* Legt den HTTPS-Port in 5001 fest. Der Standardwert ist 443.

#### <a name="configure-permanent-redirects-in-production"></a>Konfigurieren Sie dauerhafte umleitungen in der Produktion

Die Middleware standardmäßig zum Senden einer [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) mit alle umleitungen. Falls gewünscht, einen permanente Umleitung-Statuscode senden, wenn die app in einer Entwicklungsumgebung, umschließen Sie die Konfiguration der Middleware-Optionen in eine bedingungsüberprüfung für eine nicht-Entwicklungsumgebung.

Beim Konfigurieren einer `IWebHostBuilder` in *"Startup.cs"*:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

## <a name="https-redirection-middleware-alternative-approach"></a>Alternativer Ansatz für HTTPS-Umleitung-Middleware

Eine Alternative zur Verwendung von HTTPS-Umleitung-Middleware (`UseHttpsRedirection`) ist die Verwendung von URL-Umschreibenden Middleware (`AddRedirectToHttps`). `AddRedirectToHttps` können den Statuscode und den Port auch festlegen, wenn die Umleitung ausgeführt wird. Weitere Informationen finden Sie unter [URL-Umschreibenden Middleware](xref:fundamentals/url-rewriting).

Ohne die Notwendigkeit von zusätzlichen umleitungs-Regeln an HTTPS umleiten möchten, empfehlen wir Ihnen unter Verwendung von HTTPS-Umleitung-Middleware (`UseHttpsRedirection`) in diesem Thema beschrieben.

::: moniker-end

::: moniker range="< aspnetcore-2.1"

Die [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) wird verwendet, um die HTTPS erforderlich ist. `[RequireHttpsAttribute]` ergänzen können, Controllern oder Methoden oder global angewendet werden können. Um das Attribut global anzuwenden, fügen Sie den folgenden Code zur `ConfigureServices` in `Startup`:

[!code-csharp[](~/security/authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet2&highlight=4-999)]

Der oben markierte Code ist erforderlich, alle Anforderungen verwenden `HTTPS`daher HTTP-Anforderungen werden ignoriert. Der folgende hervorgehobene Code leitet alle HTTP-Anfragen an HTTPS um:

[!code-csharp[](authentication/accconfirm/sample/WebApp1/Startup.cs?name=snippet_AddRedirectToHttps&highlight=7-999)]

Weitere Informationen finden Sie unter [URL-Umschreibenden Middleware](xref:fundamentals/url-rewriting). Die Middleware kann außerdem die app, die den Statuscode oder den Statuscode sowie den Port festgelegt, wenn die Umleitung ausgeführt wird.

Das globale Erzwingen der Verwendung von HTTPS (`options.Filters.Add(new RequireHttpsAttribute());`) ist eine bewährte Sicherheitsmethode. Dieser Ansatz gilt im Vergleich zur Anwendung des `[RequireHttps]` -Attributs auf alle Controller und Razor Pages als sicherer. denn Sie können nicht gewährleisten, dass das `[RequireHttps]` -Attribut angewendet wird, wenn neue Controller oder Razor Pages hinzugefügt werden.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="http-strict-transport-security-protocol-hsts"></a>HTTP Strict Transport Security-Protokoll (HSTS)

Pro [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) ist eine optionale sicherheitserweiterung, die von einer Web-app durch die Verwendung eines Antwortheaders angegeben wird. Wenn eine [Browser mit Unterstützung von HSTS](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support) diesen Header empfängt:

* Der Browser speichert die Konfiguration für die Domäne, die verhindert, dass eine Kommunikation über HTTP senden. Der Browser wird die gesamte Kommunikation über HTTPS erzwungen.
* Der Browser wird verhindert, dass der Benutzer mithilfe von Zertifikaten für nicht vertrauenswürdig oder ungültig. Der Browser deaktiviert eingabeaufforderungen, die einen solches Zertifikat vorübergehend vertrauen Benutzer zu ermöglichen.

Da HSTS vom Client erzwungen wird, hat es einige Einschränkungen:

* Der Client muss HSTS unterstützen.
* HSTS muss mindestens eine erfolgreiche HTTPS-Anforderung zu, um die Richtlinie HSTS herzustellen.
* Die Anwendung muss jede HTTP-Anforderung überprüfen und umleiten oder die HTTP-Anforderung ablehnen.

ASP.NET Core 2.1 oder höher implementiert HSTS mit der `UseHsts` -Erweiterungsmethode. Der folgende code ruft `UseHsts` bei der app nicht im [Entwicklungsmodus](xref:fundamentals/environments):

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet1&highlight=10)]

`UseHsts` ist nicht in der Entwicklung empfohlen, da die Einstellungen HSTS hohem Maße zwischenspeicherbar sind von Browsern. In der Standardeinstellung `UseHsts` schließt die lokalen Loopback-Adresse.

Für produktionsumgebungen HTTPS zum ersten Mal implementieren legen Sie den ersten [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) auf einen niedrigen Wert eines der <xref:System.TimeSpan> Methoden. Legen Sie den Wert von Stunden keine mehr als ein Tag für den Fall, dass Sie die HTTP-HTTPS-Infrastruktur wiederherstellen müssen. Nachdem Sie sich die Nachhaltigkeit der HTTPS-Konfiguration sicher sind, erhöhen Sie den HSTS-Max-Age-Wert; häufig verwendeter Wert ist ein Jahr.

Der folgende Code

[!code-csharp[](enforcing-ssl/sample/Startup.cs?name=snippet2&highlight=5-12)]

* Legt den preload Parameter des Strict-Transport-Security-Headers. Preload ist nicht Teil der [RFC HSTS Spezifikation](https://tools.ietf.org/html/rfc6797), aber vom Webbrowser zum Vorabladen von HSTS Websites Neuinstallation unterstützt wird. Weitere Informationen finden Sie unter [https://hstspreload.org/](https://hstspreload.org/).
* Ermöglicht [IncludeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), denen Unterdomänen der Host die HSTS-Richtlinie gilt.
* Explizit festlegt den Max-Age-Parameter, der den Strict-Transport-Security-Header auf 60 Tage. Wenn nicht festgelegt, der Standardwert ist 30 Tage. Finden Sie unter den [Max-Age-Direktive](https://tools.ietf.org/html/rfc6797#section-6.1.1) für Weitere Informationen.
* Fügt `example.com` zur Liste der Hosts zu schließen.

`UseHsts` Schließt die folgenden Loopback-Hosts an:

* `localhost` : Die IPv4-Loopback-Adresse.
* `127.0.0.1` : Die IPv4-Loopback-Adresse.
* `[::1]` : Die IPv6-Loopback-Adresse.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="opt-out-of-httpshsts-on-project-creation"></a>Deaktivieren von HTTPS/HSTS bei projekterstellung

In einigen Back-End-Dienst-Szenarien, in denen verbindungssicherheit am Rand des Netzwerks öffentlich zugänglichen behandelt wird, ist nicht Konfigurieren der Sicherheit der Serververbindung über jeden Knoten erforderlich. Web-apps, die aus den Vorlagen in Visual Studio oder über generiert die [Dotnet neue](/dotnet/core/tools/dotnet-new) Befehls aktivieren [HTTPS-Umleitung](#require-https) und [HSTS](#http-strict-transport-security-protocol-hsts). Für Bereitstellungen, die diese Szenarien erfordern nicht, Sie können HTTPS/HSTS deaktivieren, wenn die app aus der Vorlage erstellt wird.

Zum Deaktivieren von HTTPS/HSTS:

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio) 

Deaktivieren Sie die **für HTTPS konfigurieren** Kontrollkästchen.

![Dialogfeld "neues ASP.NET Core-Webanwendung" mit der konfigurieren für HTTPS-Kontrollkästchen deaktiviert.](enforcing-ssl/_static/out.png)

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli) 

Verwenden Sie die `--no-https`-Option. Beispiel:

```console
dotnet new webapp --no-https
```

---

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a>Vertrauen Sie das ASP.NET Core-HTTPS-Entwicklungszertifikat unter Windows und macOS

.NET Core SDK umfasst ein Entwicklungszertifikat HTTPS. Das Zertifikat wird als Teil der ersten Ausführung-Umgebung installiert. Z. B. `dotnet --info` erzeugt eine Ausgabe ähnlich der folgenden:

```text
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

Installieren von .NET Core SDK wird das ASP.NET Core-HTTPS-Entwicklungszertifikat in den Zertifikatspeicher des lokalen Benutzers installiert. Das Zertifikat wurde installiert, aber es ist nicht als vertrauenswürdig eingestuft. Führen Sie den einmaligen Schritt zum Ausführen der Dotnet Vertrauen des Zertifikats `dev-certs` Tool:

```console
dotnet dev-certs https --trust
```

Der folgende Befehl enthält die Hilfe für die `dev-certs` Tool:

```console
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a>So richten Sie ein entwicklerzertifikat für Docker

Finden Sie unter [GitHub-Problem](https://github.com/aspnet/Docs/issues/6199).

::: moniker-end

## <a name="additional-information"></a>Zusätzliche Informationen

* <xref:host-and-deploy/proxy-load-balancer>
* [Hosten von ASP.NET Core unter Linux mit Apache: HTTPS-Konfiguration](xref:host-and-deploy/linux-apache#https-configuration)
* [Hosten von ASP.NET Core unter Linux mit Nginx: HTTPS-Konfiguration](xref:host-and-deploy/linux-nginx#https-configuration)
* [Gewusst wie: Einrichten von SSL auf IIS](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [OWASP HSTS-Browserunterstützung](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)
