---
title: Hosten von ASP.NET Core unter Linux mit Apache
description: Informationen zum Einrichten von Apache als Reverseproxyserver für CentOS zur Weiterleitung von HTTP-Datenverkehr an eine ASP.NET Core-Web-App, die unter Kestrel ausgeführt wird.
author: spboyer
ms.author: spboyer
ms.custom: mvc
ms.date: 12/20/2018
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 8c590743328885336498ca2446c618b13a7d2ce2
ms.sourcegitcommit: e1cc4c1ef6c9e07918a609d5ad7fadcb6abe3e12
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2019
ms.locfileid: "53997226"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a>Hosten von ASP.NET Core unter Linux mit Apache

Von [Shayne Boyer](https://github.com/spboyer)

In diesem Leitfaden finden Sie Informationen zur Einrichtung von [Apache](https://httpd.apache.org/) als Reverseproxyserver für [CentOS 7](https://www.centos.org/) zur Weiterleitung von HTTP-Datenverkehr an eine ASP.NET Core-Web-App, die auf einem [Kestrel](xref:fundamentals/servers/kestrel)-Server ausgeführt wird. Durch die Erweiterung [mod_proxy](http://httpd.apache.org/docs/2.4/mod/mod_proxy.html) und zugehörige Module wird der Reverseproxy des Servers erstellt.

## <a name="prerequisites"></a>Erforderliche Komponenten

* Ein Server, auf dem CentOS 7 ausgeführt wird, mit einem Standardbenutzerkonto mit sudo-Berechtigung.
* Installieren Sie die .NET Core-Runtime auf dem Server.
   1. Navigieren Sie zu der [.NET-Seite „All Downloads“ (Alle Downloads)](https://www.microsoft.com/net/download/all).
   1. Wählen Sie unter **Runtime** die aktuelle Nicht-Vorschau-Runtime aus der Liste aus.
   1. Wählen Sie die Anweisungen für CentOS/Oracle aus, und befolgen Sie diese.
* Eine vorhandene ASP.NET Core-App.

## <a name="publish-and-copy-over-the-app"></a>Veröffentlichen und Kopieren der App

Konfigurieren Sie die App für eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd).

Führen Sie [dotnet publish](/dotnet/core/tools/dotnet-publish) in der Entwicklungsumgebung aus, um eine App in ein Verzeichnis zu packen (z.B. *bin/Release/&lt;target_framework_moniker&gt;/publish*), das auf dem Server ausgeführt werden kann:

```console
dotnet publish --configuration Release
```

Die App kann auch als eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht werden, wenn Sie die .NET Core-Runtime nicht auf dem Server verwalten möchten.

Kopieren Sie die ASP.NET Core-App auf den Server, indem Sie ein beliebiges Tool verwenden, das in den Workflow der Organisation integriert ist (z.B. SCP oder SFTP). Web-Apps befinden sich üblicherweise im Verzeichnis *var* (z.B. *var/www/helloapp*).

> [!NOTE]
> In einem Szenario für die Bereitstellung in der Produktion übernimmt ein Continuous Integration-Workflow die Veröffentlichung der App und das Kopieren der Objekte auf den Server.

## <a name="configure-a-proxy-server"></a>Konfigurieren eines Proxyservers

Ein Reverseproxy wird im Allgemeinen zur Unterstützung dynamischer Web-Apps eingerichtet. Der Reverseproxy beendet die HTTP-Anforderung und leitet diese an die ASP.NET-App weiter.

Ein Proxyserver dient zum Weiterleiten von Clientanforderungen an einen anderen Server, anstatt Anforderungen selbst zu erfüllen. Ein Reverseproxy leitet Daten an ein festes Ziel meist im Auftrag beliebiger Clients weiter. In diesem Handbuch wird Apache als Reverseproxy konfiguriert, der auf demselben Server ausgeführt wird, auf dem Kestrel die ASP.NET Core-App verarbeitet.

Da Anforderungen vom Reverseproxy weitergeleitet werden, sollten Sie die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) verwenden. Diese Middleware aktualisiert `Request.Scheme` mithilfe des `X-Forwarded-Proto`-Headers, sodass Umleitungs-URIs und andere Sicherheitsrichtlinien ordnungsgemäß funktionieren.

Jede vom Schema abhängige Komponente, wie etwa Authentifizierung, Linkgenerierung, Umleitungen und Geolocation, muss nach dem Aufrufen der Middleware für weitergeleitete Header eingefügt werden. Allgemein gilt, dass Middleware für weitergeleitete Header vor jeder anderen Middleware ausgeführt werden sollte, mit Ausnahme von Middleware für die Diagnose und Fehlerbehandlung. Mit dieser Reihenfolge wird sichergestellt, dass die auf Informationen von weitergeleiteten Headern basierende Middleware die zu verarbeitenden Headerwerte nutzen kann.

::: moniker range=">= aspnetcore-2.0"

Rufen Sie die Methode [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` auf, bevor Sie [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication) oder eine ähnliche Middleware für Authentifizierungsschemas aufrufen. Konfigurieren Sie die Middleware so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` weitergeleitet werden:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Rufen Sie die Methode [UseForwardedHeaders](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) in `Startup.Configure` auf, bevor Sie [UseIdentity](/dotnet/api/microsoft.aspnetcore.builder.builderextensions.useidentity) und [UseFacebookAuthentication](/dotnet/api/microsoft.aspnetcore.builder.facebookappbuilderextensions.usefacebookauthentication) oder eine ähnliche Middleware für Authentifizierungsschemas aufrufen. Konfigurieren Sie die Middleware so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` weitergeleitet werden:

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseIdentity();
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

Wenn für die Middleware keine [ForwardedHeadersOptions](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) angegeben sind, lauten die weiterzuleitenden Standardheader `None`.

Nur Proxys, die unter localhost (127.0.0.0.1, [::1]) ausgeführt werden, gelten standardmäßig als vertrauenswürdig. Wenn andere vertrauenswürdige Proxys oder Netzwerke innerhalb des Unternehmens Anforderungen zwischen dem Internet und dem Webserver verarbeiten, fügen Sie diese der Liste der <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> oder <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> hinzu. Das folgende Beispiel fügt einen vertrauenswürdigen Proxyserver mit der IP-Adresse 10.0.0.0.100 der Middleware für weitergeleitete Header `KnownProxies` in `Startup.ConfigureServices` hinzu:

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.

### <a name="install-apache"></a>Installieren von Apache

So aktualisieren Sie CentOS-Pakete auf ihre aktuellsten stabilen Versionen:

```bash
sudo yum update -y
```

Installieren Sie die Apache-Webserver unter CentOS mit einem einzelnen `yum`-Befehl:

```bash
sudo yum -y install httpd mod_ssl
```

Beispielausgabe nach der Ausführung des Befehls:

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> In diesem Beispiel enthält die Ausgabe „httpd.86_64“, da die CentOS 7-Version eine 64-Bit-Version ist. Um zu prüfen, wo Apache installiert ist, führen Sie an einer Eingabeaufforderung `whereis httpd` aus.

### <a name="configure-apache"></a>Konfigurieren von Apache

Konfigurationsdateien für Apache befinden sich im Verzeichnis `/etc/httpd/conf.d/`. Dateien mit der Erweiterung *.conf* werden in alphabetischer Reihenfolge zusätzlich zu den Modulkonfigurationsdateien im Verzeichnis `/etc/httpd/conf.modules.d/` verarbeitet, das Konfigurationsdateien enthält, die zum Laden von Modulen erforderlich sind.

So erstellen Sie eine Konfigurationsdatei mit dem Namen *helloapp.conf* für die App:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

Der Block `VirtualHost` kann mehrmals erscheinen, in mindestens einer Datei auf einem Server. In der vorangehenden Konfigurationsdatei akzeptiert Apache öffentlichen Datenverkehr über Port 80. Die Domäne `www.example.com` wird bearbeitet, und der Alias `*.example.com` wird auf der gleichen Website aufgelöst. Weitere Informationen finden Sie unter [Namensbasierte virtuelle Hostunterstützung](https://httpd.apache.org/docs/current/vhosts/name-based.html). Anforderungen werden über einen Proxy auf Stammebene an Port 5000 des Servers unter 127.0.0.1 übergeben. Für bidirektionale Kommunikation sind `ProxyPass` und `ProxyPassReverse` erforderlich. Informationen zum Ändern der IP-Adresse bzw. des Ports von Kestrel finden Sie unter [Kestrel: Endpoint configuration (Kestrel: Endpunktkonfiguration)](xref:fundamentals/servers/kestrel#endpoint-configuration).

> [!WARNING]
> Schlägt die Angabe einer ordnungsgemäßen [ServerName-Anweisung](https://httpd.apache.org/docs/current/mod/core.html#servername) im Block **VirtualHost** fehl, ist Ihre App Sicherheitsrisiken ausgesetzt. Platzhalterbindungen in untergeordneten Domänen (z.B. `*.example.com`) verursachen kein Sicherheitsrisiko, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist). Weitere Informationen finden Sie unter [rfc7230 im Abschnitt 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

Die Protokollierung kann über `VirtualHost` mit den `ErrorLog`- und `CustomLog`-Anweisungen konfiguriert werden. `ErrorLog` ist der Speicherort, an dem der Server Fehler protokolliert. `CustomLog` legt den Dateinamen und das Format der Protokolldatei fest. In diesem Fall werden hier Anforderungsinformationen protokolliert. Für jede Anforderung gibt es eine Zeile.

Speichern Sie die Datei, und testen Sie die Konfiguration. Wenn alle Anforderungen durchgehen, muss die Antwort `Syntax [OK]` lauten.

```bash
sudo service httpd configtest
```

So starten Sie Apache neu:

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a>Überwachen der App

Apache ist jetzt dafür eingerichtet, Anforderungen an `http://localhost:80` an die ASP.NET Core-App weiterzuleiten, die unter Kestrel unter `http://127.0.0.1:5000` ausgeführt wird. Apache ist jedoch nicht dafür eingerichtet, den Kestrel-Prozess zu verwalten. Verwenden Sie *systemd*, und erstellen eine Dienstdatei, um die zugrunde liegende Web-App zu starten und zu überwachen. *SystemD* ist ein Initialisierungssystem, das viele leistungsstarke Features zum Starten, Beenden und Verwalten von Prozessen bereitstellt.

### <a name="create-the-service-file"></a>Erstellen der Dienstdatei

Erstellen Sie die Dienstdefinitionsdatei:

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

Eine Beispieldienstdatei für die App:

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

Wenn der Benutzer *apache* von Ihrer Konfiguration nicht verwendet wird, muss der Benutzer zunächst erstellt werden. Darüber hinaus müssen ihm die ordnungsgemäßen Besitzrechte für Dateien zugewiesen werden.

Verwenden Sie `TimeoutStopSec`, um die Dauer der Wartezeit bis zum Herunterfahren der App zu konfigurieren, nachdem diese das anfängliche Interruptsignal empfangen hat. Wenn die App in diesem Zeitraum nicht heruntergefahren wird, wird SIGKILL ausgegeben, um die App zu beenden. Geben Sie den Wert als einheitenlose Sekunden (z.B. `150`), einen Zeitspannenwert (z.B. `2min 30s`) oder `infinity` an, um das Timeout zu deaktivieren. `TimeoutStopSec` ist standardmäßig der Wert `DefaultTimeoutStopSec` in der Manager-Konfigurationsdatei (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*). Das Standardtimeout für die meisten Distributionen beträgt 90 Sekunden.

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

Einige Werte (z.B. SQL-Verbindungszeichenfolgen) müssen mit Escapezeichen versehen werden, damit die Konfigurationsanbieter die Umgebungsvariablen lesen können. Mit dem folgenden Befehl können Sie einen ordnungsgemäß mit Escapezeichen versehenen Wert für die Verwendung in der Konfigurationsdatei generieren:

```console
systemd-escape "<value-to-escape>"
```

So speichern Sie die Datei und aktivieren den Dienst:

```bash
sudo systemctl enable kestrel-helloapp.service
```

So starten Sie den Dienst und überprüfen, ob er ausgeführt wird:

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

Wenn der Reverseproxy konfiguriert ist und Kestrel durch *systemd* verwaltet wird, ist die Web-App vollständig konfiguriert. Auf diese kann dann über einen Browser auf dem lokalen Computer unter `http://localhost` zugegriffen werden. Beim Überprüfen der Antwortheader wird im Header **Server** angezeigt, dass die ASP.NET Core-App von Kestrel verarbeitet wird:

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a>Protokoll anzeigen...

Da die von Kestrel verwendete Web-App von *systemd* verwaltet wird, werden Ereignisse und Protokolle in einem zentralen Journal protokolliert. Dieses Journal enthält jedoch Einträge für alle Dienste und Prozesse, die von *systemd* verwaltet werden. Verwenden Sie folgenden Befehl, um die `kestrel-helloapp.service`-spezifischen Elemente anzuzeigen:

```bash
sudo journalctl -fu kestrel-helloapp.service
```

Geben Sie für die Uhrzeitfilterung mit dem Befehl Uhrzeitoptionen an. Verwenden Sie beispielsweise `--since today` zum Filtern für den aktuellen Tag oder `--until 1 hour ago`, um die Einträge der letzten Stunde anzuzeigen. Weitere Informationen finden Sie auf der [Manpage für „journalctl“](https://www.unix.com/man-page/centos/1/journalctl/).

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a>Schutz von Daten

Der [Stapel zum Schutz von Daten in ASP.NET Core](xref:security/data-protection/introduction) wird von mehreren [ASP.NET Core-Middlewares](xref:fundamentals/middleware/index) verwendet. Hierzu gehören die Authentifizierungsmiddleware (zum Beispiel die Cookiemiddleware) und Maßnahmen zum Schutz vor websiteübergreifenden Anforderungsfälschungen (CSRF). Selbst wenn Datenschutz-APIs nicht im Benutzercode aufgerufen werden, sollte der Schutz von Daten konfiguriert werden, um einen persistenten kryptografischen [Schlüsselspeicher](xref:security/data-protection/implementation/key-management) zu erstellen. Wenn der Schutz von Daten nicht konfiguriert ist, werden die Schlüssel beim Neustarten der App im Arbeitsspeicher gespeichert und verworfen.

Falls der Schlüsselbund im Arbeitsspeicher gespeichert wird, wenn die App neu gestartet wird, gilt Folgendes:

* Alle cookiebasierten Authentifizierungstoken für ungültig erklärt.
* Benutzer müssen sich bei ihrer nächsten Anforderung erneut anmelden.
* Alle mit dem Schlüsselbund geschützte Daten können nicht mehr entschlüsselt werden. Dies kann [CSRF-Token](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) und [ASP.NET Core-MVC-TempData-Cookies](xref:fundamentals/app-state#tempdata) einschließen.

Wenn Sie den Schutz von Daten konfigurieren möchten, um den Schlüsselring persistent zu speichern und zu verschlüsseln, finden Sie in den folgenden Artikeln weitere Informationen:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a>Sichern der App

### <a name="configure-firewall"></a>Konfigurieren der Firewall

*Firewalld* ist ein dynamischer Daemon zum Verwalten der Firewall mit Unterstützung für Netzwerkzonen. Ports und Paketfilterung können weiterhin mit „iptables“ verwaltet werden. *Firewalld* sollte standardmäßig installiert werden. `yum` kann für die Installation oder zur Überprüfung einer erfolgreichen Installation verwendet werden.

```bash
sudo yum install firewalld -y
```

Mit `firewalld` können Sie nur die für die App erforderlichen Ports öffnen. In diesem Fall werden die Ports 80 und 443 verwendet. Über die folgenden Befehle werden die Ports 80 und 443 dauerhaft geöffnet:

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

Laden Sie die Firewalleinstellungen erneut. Überprüfen Sie die verfügbaren Dienste und Ports in der Standardzone. Über `firewall-cmd -h` können Sie verfügbare Optionen ermitteln.

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="ssl-configuration"></a>SSL-Konfiguration

Für die Konfiguration von Apache für SSL wird das Modul *mod_ssl* verwendet. Wenn das Modul *httpd* installiert wurde, wurde das Modul *mod_ssl* ebenfalls installiert. Wurde es nicht installiert, fügen Sie es mit `yum` zur Konfiguration hinzu.

```bash
sudo yum install mod_ssl
```

Installieren Sie zur Erzwingung von SSL das Modul `mod_rewrite`, um eine URL-Umschreibung zu aktivieren:

```bash
sudo yum install mod_rewrite
```

Ändern Sie die Datei *helloapp.conf*, um eine URL-Umschreibung und sichere Kommunikation an Port 443 zu aktivieren:

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> In diesem Beispiel wird ein lokal generiertes Zertifikat verwendet. **SSLCertificateFile** muss die primäre Zertifikatdatei für den Domänennamen sein. **SSLCertificateKeyFile** muss die Schlüsseldatei sein, die beim Erstellen der Zertifikatsignaturanforderung (Certificate Signing Request, CSR) generiert wurde. **SSLCertificateChainFile** muss die Zwischenzertifikatdatei (falls vorhanden) sein, die von der Zertifizierungsstelle bereitgestellt wurde.

So speichern Sie die Datei und testen die Konfiguration:

```bash
sudo service httpd configtest
```

So starten Sie Apache neu:

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a>Weitere Vorschläge für Apache

### <a name="additional-headers"></a>Zusätzliche Header

Als Schutz gegen böswillige Angriffe müssen einige Header geändert oder hinzugefügt werden. So stellen Sie sicher, dass das Modul `mod_headers` installiert wurde:

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a>Schützen von Apache vor Clickjacking-Angriffen

[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), auch bekannt als *UI Redress-Angriff*, ist ein böswilliger Angriff, bei dem ein Websitebesucher dazu verleitet wird, auf einen Link oder eine Schaltfläche zu klicken, der bzw. die sich auf einer anderen Seite als der aktuell besuchten Seite befindet. Verwenden Sie `X-FRAME-OPTIONS` zum Sichern der Website.

So wehren Sie Clickjacking-Angriffe ab:

1. So bearbeiten Sie die Datei *httpd.conf*:

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   Fügen Sie die Zeile `Header append X-FRAME-OPTIONS "SAMEORIGIN"` hinzu.
1. Speichern Sie die Datei.
1. Starten Sie Apache neu.

#### <a name="mime-type-sniffing"></a>MIME-Typermittlung

Der Header `X-Content-Type-Options` verhindert, dass eine *MIME-Ermittlung* über Internet Explorer (dabei wird der `Content-Type` einer Datei aus dem Dateiinhalt bestimmt) erfolgt. Wenn der Server den Header `Content-Type` auf `text/html` festlegt und die Option `nosniff` festgelegt ist, rendert Internet Explorer den Inhalt unabhängig vom Dateiinhalt als `text/html`.

So bearbeiten Sie die Datei *httpd.conf*:

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

Fügen Sie die Zeile `Header set X-Content-Type-Options "nosniff"` hinzu. Speichern Sie die Datei. Starten Sie Apache neu.

### <a name="load-balancing"></a>Lastenausgleich

Dieses Beispiel veranschaulicht das Einrichten und Konfigurieren von Apache unter CentOS 7 und Kestrel auf demselben Instanzcomputer. Damit es zu keinem Single Point of Failure kommt, ermöglicht die Verwendung von *mod_proxy_balancer* und das Ändern von **VirtualHost** das Verwalten mehrerer Instanzen der Web-Apps hinter dem Apache-Proxyserver.

```bash
sudo yum install mod_proxy_balancer
```

In der unten dargestellten Konfigurationsdatei wird eine zusätzliche Instanz von `helloapp` zur Ausführung an Port 5001 eingerichtet. Der Abschnitt *Proxy* wird mit einer Lastenausgleichskonfiguration mit zwei Mitgliedern für den Lastenausgleich von *byrequests* festgelegt.

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a>Begrenzung der Bandbreite

Mit dem Modul *mod_ratelimit*, das im Modul *httpd* enthalten ist, kann die Bandbreite von Clients begrenzt werden:

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

In der Beispieldatei wird die Bandbreite am Stammspeicherort auf 600 KB/Sek. begrenzt:

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a>Lange Anforderungsheaderfelder

Wenn die App Anforderungsheaderfelder benötigt, die länger sind, als es die Standardeinstellung des Proxyservers zulässt (meist 8.190 Byte), passen Sie den Wert der [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize)-Anweisung an. Der anzuwendende Wert hängt von Szenario ab. Weitere Informationen finden Sie in der Dokumentation Ihres Servers.

> [!WARNING]
> Erhöhen Sie den Standardwert von `LimitRequestFieldSize` nur dann, wenn dies absolut erforderlich ist. Ein Erhöhen dieses Werts vergrößert das Risiko von Pufferüberlauf- (Überlauf-) und Denial-of-Service-Angriffen durch böswillige Benutzer.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Voraussetzungen für .NET Core unter Linux](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
