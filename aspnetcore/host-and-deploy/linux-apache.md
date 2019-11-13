---
title: Hosten von ASP.NET Core unter Linux mit Apache
author: guardrex
description: Informationen zum Einrichten von Apache als Reverseproxyserver für CentOS zur Weiterleitung von HTTP-Datenverkehr an eine ASP.NET Core-Web-App, die unter Kestrel ausgeführt wird.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 11/05/2019
uid: host-and-deploy/linux-apache
ms.openlocfilehash: fce91db736908e433ba6803319aa8984bb68a554
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73659885"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="a04dd-103">Hosten von ASP.NET Core unter Linux mit Apache</span><span class="sxs-lookup"><span data-stu-id="a04dd-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="a04dd-104">Von [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="a04dd-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="a04dd-105">In diesem Leitfaden finden Sie Informationen zur Einrichtung von [Apache](https://httpd.apache.org/) als Reverseproxyserver für [CentOS 7](https://www.centos.org/) zur Weiterleitung von HTTP-Datenverkehr an eine ASP.NET Core-Web-App, die auf einem [Kestrel](xref:fundamentals/servers/kestrel)-Server ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a04dd-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="a04dd-106">Durch die Erweiterung [mod_proxy](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) und zugehörige Module wird der Reverseproxy des Servers erstellt.</span><span class="sxs-lookup"><span data-stu-id="a04dd-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a04dd-107">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="a04dd-107">Prerequisites</span></span>

* <span data-ttu-id="a04dd-108">Ein Server, auf dem CentOS 7 ausgeführt wird, mit einem Standardbenutzerkonto mit sudo-Berechtigung.</span><span class="sxs-lookup"><span data-stu-id="a04dd-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="a04dd-109">Installieren Sie die .NET Core-Runtime auf dem Server.</span><span class="sxs-lookup"><span data-stu-id="a04dd-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="a04dd-110">Navigieren Sie zu der [.NET-Seite „All Downloads“ (Alle Downloads)](https://www.microsoft.com/net/download/all).</span><span class="sxs-lookup"><span data-stu-id="a04dd-110">Visit the [.NET Core All Downloads page](https://www.microsoft.com/net/download/all).</span></span>
   1. <span data-ttu-id="a04dd-111">Wählen Sie unter **Runtime** die aktuelle Nicht-Vorschau-Runtime aus der Liste aus.</span><span class="sxs-lookup"><span data-stu-id="a04dd-111">Select the latest non-preview runtime from the list under **Runtime**.</span></span>
   1. <span data-ttu-id="a04dd-112">Wählen Sie die Anweisungen für CentOS/Oracle aus, und befolgen Sie diese.</span><span class="sxs-lookup"><span data-stu-id="a04dd-112">Select and follow the instructions for CentOS/Oracle.</span></span>
* <span data-ttu-id="a04dd-113">Eine vorhandene ASP.NET Core-App.</span><span class="sxs-lookup"><span data-stu-id="a04dd-113">An existing ASP.NET Core app.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="a04dd-114">Veröffentlichen und Kopieren der App</span><span class="sxs-lookup"><span data-stu-id="a04dd-114">Publish and copy over the app</span></span>

<span data-ttu-id="a04dd-115">Konfigurieren Sie die App für eine [Framework-abhängige Bereitstellung](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span><span class="sxs-lookup"><span data-stu-id="a04dd-115">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="a04dd-116">Wenn die App lokal ausgeführt wird und nicht so konfiguriert wurde, dass sie sichere Verbindungen (HTTPS) herstellt, können Sie einen der folgenden Ansätze verwenden:</span><span class="sxs-lookup"><span data-stu-id="a04dd-116">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="a04dd-117">Konfigurieren der App, sodass diese sichere lokale Verbindungen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-117">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="a04dd-118">Weitere Informationen finden Sie im Abschnitt [HTTPS-Konfiguration](#https-configuration).</span><span class="sxs-lookup"><span data-stu-id="a04dd-118">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="a04dd-119">Entfernen Sie `https://localhost:5001` (falls vorhanden) aus der `applicationUrl`-Eigenschaft in der Datei *Properties/launchSettings.json*.</span><span class="sxs-lookup"><span data-stu-id="a04dd-119">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="a04dd-120">Führen Sie [dotnet publish](/dotnet/core/tools/dotnet-publish) in der Entwicklungsumgebung aus, um eine App in ein Verzeichnis zu packen (z.B. *bin/Release/&lt;target_framework_moniker&gt;/publish*), das auf dem Server ausgeführt werden kann:</span><span class="sxs-lookup"><span data-stu-id="a04dd-120">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="a04dd-121">Die App kann auch als eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) veröffentlicht werden, wenn Sie die .NET Core-Runtime nicht auf dem Server verwalten möchten.</span><span class="sxs-lookup"><span data-stu-id="a04dd-121">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="a04dd-122">Kopieren Sie die ASP.NET Core-App auf den Server, indem Sie ein beliebiges Tool verwenden, das in den Workflow der Organisation integriert ist (z.B. SCP oder SFTP).</span><span class="sxs-lookup"><span data-stu-id="a04dd-122">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="a04dd-123">Web-Apps befinden sich üblicherweise im Verzeichnis *var* (z.B. *var/www/helloapp*).</span><span class="sxs-lookup"><span data-stu-id="a04dd-123">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="a04dd-124">In einem Szenario für die Bereitstellung in der Produktion übernimmt ein Continuous Integration-Workflow die Veröffentlichung der App und das Kopieren der Objekte auf den Server.</span><span class="sxs-lookup"><span data-stu-id="a04dd-124">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="a04dd-125">Konfigurieren eines Proxyservers</span><span class="sxs-lookup"><span data-stu-id="a04dd-125">Configure a proxy server</span></span>

<span data-ttu-id="a04dd-126">Ein Reverseproxy wird im Allgemeinen zur Unterstützung dynamischer Web-Apps eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-126">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="a04dd-127">Der Reverseproxy beendet die HTTP-Anforderung und leitet diese an die ASP.NET-App weiter.</span><span class="sxs-lookup"><span data-stu-id="a04dd-127">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="a04dd-128">Ein Proxyserver dient zum Weiterleiten von Clientanforderungen an einen anderen Server, anstatt Anforderungen selbst zu erfüllen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-128">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="a04dd-129">Ein Reverseproxy leitet Daten an ein festes Ziel meist im Auftrag beliebiger Clients weiter.</span><span class="sxs-lookup"><span data-stu-id="a04dd-129">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="a04dd-130">In diesem Handbuch wird Apache als Reverseproxy konfiguriert, der auf demselben Server ausgeführt wird, auf dem Kestrel die ASP.NET Core-App verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-130">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="a04dd-131">Da Anforderungen vom Reverseproxy weitergeleitet werden, sollten Sie die [Middleware für weitergeleitete Header](xref:host-and-deploy/proxy-load-balancer) aus dem Paket [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) verwenden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-131">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="a04dd-132">Diese Middleware aktualisiert `Request.Scheme` mithilfe des `X-Forwarded-Proto`-Headers, sodass Umleitungs-URIs und andere Sicherheitsrichtlinien ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="a04dd-132">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="a04dd-133">Jede vom Schema abhängige Komponente, wie etwa Authentifizierung, Linkgenerierung, Umleitungen und Geolocation, muss nach dem Aufrufen der Middleware für weitergeleitete Header eingefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-133">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="a04dd-134">Allgemein gilt, dass Middleware für weitergeleitete Header vor jeder anderen Middleware ausgeführt werden sollte, mit Ausnahme von Middleware für die Diagnose und Fehlerbehandlung.</span><span class="sxs-lookup"><span data-stu-id="a04dd-134">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="a04dd-135">Mit dieser Reihenfolge wird sichergestellt, dass die auf Informationen von weitergeleiteten Headern basierende Middleware die zu verarbeitenden Headerwerte nutzen kann.</span><span class="sxs-lookup"><span data-stu-id="a04dd-135">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="a04dd-136">Rufen Sie die <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>-Methode in `Startup.Configure` auf, bevor Sie <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> oder eine ähnliche Middleware für das Authentifizierungsschema aufrufen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-136">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or similar authentication scheme middleware.</span></span> <span data-ttu-id="a04dd-137">Konfigurieren Sie die Middleware so, dass die Header `X-Forwarded-For` und `X-Forwarded-Proto` weitergeleitet werden:</span><span class="sxs-lookup"><span data-stu-id="a04dd-137">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="a04dd-138">Wenn für die Middleware keine <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> angegeben sind, lauten die weiterzuleitenden Standardheader `None`.</span><span class="sxs-lookup"><span data-stu-id="a04dd-138">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="a04dd-139">Proxys, die unter Loopbackadressen (127.0.0.0/8, [::1]) ausgeführt werden, einschließlich der standardmäßigen Localhostadresse (127.0.0.1), werden standardmäßig als vertrauenswürdig eingestuft.</span><span class="sxs-lookup"><span data-stu-id="a04dd-139">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="a04dd-140">Wenn andere vertrauenswürdige Proxys oder Netzwerke innerhalb des Unternehmens Anforderungen zwischen dem Internet und dem Webserver verarbeiten, fügen Sie diese der Liste der <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> oder <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> mit <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> hinzu.</span><span class="sxs-lookup"><span data-stu-id="a04dd-140">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="a04dd-141">Das folgende Beispiel fügt einen vertrauenswürdigen Proxyserver mit der IP-Adresse 10.0.0.0.100 der Middleware für weitergeleitete Header `KnownProxies` in `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="a04dd-141">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="a04dd-142">Weitere Informationen finden Sie unter <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="a04dd-142">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="a04dd-143">Installieren von Apache</span><span class="sxs-lookup"><span data-stu-id="a04dd-143">Install Apache</span></span>

<span data-ttu-id="a04dd-144">So aktualisieren Sie CentOS-Pakete auf ihre aktuellsten stabilen Versionen:</span><span class="sxs-lookup"><span data-stu-id="a04dd-144">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="a04dd-145">Installieren Sie die Apache-Webserver unter CentOS mit einem einzelnen `yum`-Befehl:</span><span class="sxs-lookup"><span data-stu-id="a04dd-145">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="a04dd-146">Beispielausgabe nach der Ausführung des Befehls:</span><span class="sxs-lookup"><span data-stu-id="a04dd-146">Sample output after running the command:</span></span>

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
> <span data-ttu-id="a04dd-147">In diesem Beispiel enthält die Ausgabe „httpd.86_64“, da die CentOS 7-Version eine 64-Bit-Version ist.</span><span class="sxs-lookup"><span data-stu-id="a04dd-147">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="a04dd-148">Um zu prüfen, wo Apache installiert ist, führen Sie an einer Eingabeaufforderung `whereis httpd` aus.</span><span class="sxs-lookup"><span data-stu-id="a04dd-148">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="a04dd-149">Konfigurieren von Apache</span><span class="sxs-lookup"><span data-stu-id="a04dd-149">Configure Apache</span></span>

<span data-ttu-id="a04dd-150">Konfigurationsdateien für Apache befinden sich im Verzeichnis `/etc/httpd/conf.d/`.</span><span class="sxs-lookup"><span data-stu-id="a04dd-150">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="a04dd-151">Dateien mit der Erweiterung *.conf* werden in alphabetischer Reihenfolge zusätzlich zu den Modulkonfigurationsdateien im Verzeichnis `/etc/httpd/conf.modules.d/` verarbeitet, das Konfigurationsdateien enthält, die zum Laden von Modulen erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="a04dd-151">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="a04dd-152">So erstellen Sie eine Konfigurationsdatei mit dem Namen *helloapp.conf* für die App:</span><span class="sxs-lookup"><span data-stu-id="a04dd-152">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

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

<span data-ttu-id="a04dd-153">Der Block `VirtualHost` kann mehrmals erscheinen, in mindestens einer Datei auf einem Server.</span><span class="sxs-lookup"><span data-stu-id="a04dd-153">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="a04dd-154">In der vorangehenden Konfigurationsdatei akzeptiert Apache öffentlichen Datenverkehr über Port 80.</span><span class="sxs-lookup"><span data-stu-id="a04dd-154">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="a04dd-155">Die Domäne `www.example.com` wird bearbeitet, und der Alias `*.example.com` wird auf der gleichen Website aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="a04dd-155">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="a04dd-156">Weitere Informationen finden Sie unter [Namensbasierte virtuelle Hostunterstützung](https://httpd.apache.org/docs/current/vhosts/name-based.html).</span><span class="sxs-lookup"><span data-stu-id="a04dd-156">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="a04dd-157">Anforderungen werden über einen Proxy auf Stammebene an Port 5000 des Servers unter 127.0.0.1 übergeben.</span><span class="sxs-lookup"><span data-stu-id="a04dd-157">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="a04dd-158">Für bidirektionale Kommunikation sind `ProxyPass` und `ProxyPassReverse` erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a04dd-158">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="a04dd-159">Informationen zum Ändern der IP-Adresse bzw. des Ports von Kestrel finden Sie unter [Kestrel: Endpoint configuration (Kestrel: Endpunktkonfiguration)](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="a04dd-159">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="a04dd-160">Schlägt die Angabe einer ordnungsgemäßen [ServerName-Anweisung](https://httpd.apache.org/docs/current/mod/core.html#servername) im Block **VirtualHost** fehl, ist Ihre App Sicherheitsrisiken ausgesetzt.</span><span class="sxs-lookup"><span data-stu-id="a04dd-160">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="a04dd-161">Platzhalterbindungen in untergeordneten Domänen (z.B. `*.example.com`) verursachen kein Sicherheitsrisiko, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist).</span><span class="sxs-lookup"><span data-stu-id="a04dd-161">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="a04dd-162">Weitere Informationen finden Sie unter [rfc7230 im Abschnitt 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="a04dd-162">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="a04dd-163">Die Protokollierung kann über `VirtualHost` mit den `ErrorLog`- und `CustomLog`-Anweisungen konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-163">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="a04dd-164">`ErrorLog` ist der Speicherort, an dem der Server Fehler protokolliert. `CustomLog` legt den Dateinamen und das Format der Protokolldatei fest.</span><span class="sxs-lookup"><span data-stu-id="a04dd-164">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="a04dd-165">In diesem Fall werden hier Anforderungsinformationen protokolliert.</span><span class="sxs-lookup"><span data-stu-id="a04dd-165">In this case, this is where request information is logged.</span></span> <span data-ttu-id="a04dd-166">Für jede Anforderung gibt es eine Zeile.</span><span class="sxs-lookup"><span data-stu-id="a04dd-166">There's one line for each request.</span></span>

<span data-ttu-id="a04dd-167">Speichern Sie die Datei, und testen Sie die Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="a04dd-167">Save the file and test the configuration.</span></span> <span data-ttu-id="a04dd-168">Wenn alle Anforderungen durchgehen, muss die Antwort `Syntax [OK]` lauten.</span><span class="sxs-lookup"><span data-stu-id="a04dd-168">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="a04dd-169">So starten Sie Apache neu:</span><span class="sxs-lookup"><span data-stu-id="a04dd-169">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="a04dd-170">Überwachen der App</span><span class="sxs-lookup"><span data-stu-id="a04dd-170">Monitor the app</span></span>

<span data-ttu-id="a04dd-171">Apache ist jetzt dafür eingerichtet, Anforderungen an `http://localhost:80` an die ASP.NET Core-App weiterzuleiten, die unter Kestrel unter `http://127.0.0.1:5000` ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a04dd-171">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="a04dd-172">Apache ist jedoch nicht dafür eingerichtet, den Kestrel-Prozess zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="a04dd-172">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="a04dd-173">Verwenden Sie *systemd*, und erstellen eine Dienstdatei, um die zugrunde liegende Web-App zu starten und zu überwachen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-173">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="a04dd-174">*SystemD* ist ein Initialisierungssystem, das viele leistungsstarke Features zum Starten, Beenden und Verwalten von Prozessen bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="a04dd-174">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="a04dd-175">Erstellen der Dienstdatei</span><span class="sxs-lookup"><span data-stu-id="a04dd-175">Create the service file</span></span>

<span data-ttu-id="a04dd-176">Erstellen Sie die Dienstdefinitionsdatei:</span><span class="sxs-lookup"><span data-stu-id="a04dd-176">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="a04dd-177">Eine Beispieldienstdatei für die App:</span><span class="sxs-lookup"><span data-stu-id="a04dd-177">An example service file for the app:</span></span>

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

<span data-ttu-id="a04dd-178">Wenn der Benutzer *apache* von Ihrer Konfiguration nicht verwendet wird, muss der Benutzer zunächst erstellt werden. Darüber hinaus müssen ihm die ordnungsgemäßen Besitzrechte für Dateien zugewiesen werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-178">If the user *apache* isn't used by the configuration, the user must be created first and given proper ownership of files.</span></span>

<span data-ttu-id="a04dd-179">Verwenden Sie `TimeoutStopSec`, um die Dauer der Wartezeit bis zum Herunterfahren der App zu konfigurieren, nachdem diese das anfängliche Interruptsignal empfangen hat.</span><span class="sxs-lookup"><span data-stu-id="a04dd-179">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="a04dd-180">Wenn die App in diesem Zeitraum nicht heruntergefahren wird, wird SIGKILL ausgegeben, um die App zu beenden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-180">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="a04dd-181">Geben Sie den Wert als einheitenlose Sekunden (z.B. `150`), einen Zeitspannenwert (z.B. `2min 30s`) oder `infinity` an, um das Timeout zu deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="a04dd-181">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="a04dd-182">`TimeoutStopSec` ist standardmäßig der Wert `DefaultTimeoutStopSec` in der Manager-Konfigurationsdatei (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="a04dd-182">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="a04dd-183">Das Standardtimeout für die meisten Distributionen beträgt 90 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-183">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="a04dd-184">Einige Werte (z.B. SQL-Verbindungszeichenfolgen) müssen mit Escapezeichen versehen werden, damit die Konfigurationsanbieter die Umgebungsvariablen lesen können.</span><span class="sxs-lookup"><span data-stu-id="a04dd-184">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="a04dd-185">Mit dem folgenden Befehl können Sie einen ordnungsgemäß mit Escapezeichen versehenen Wert für die Verwendung in der Konfigurationsdatei generieren:</span><span class="sxs-lookup"><span data-stu-id="a04dd-185">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="a04dd-186">Doppelpunkte (`:`) als Trennzeichen werden in Umgebungsvariablennamen nicht unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a04dd-186">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="a04dd-187">Verwenden Sie statt eines Doppelpunkts zwei Unterstriche (`__`).</span><span class="sxs-lookup"><span data-stu-id="a04dd-187">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="a04dd-188">Der [Umgebungsvariablen-Konfigurationsanbieter](xref:fundamentals/configuration/index#environment-variables-configuration-provider) konvertiert jeweils die zwei Unterstriche in einen Doppelpunkt, wenn die Umgebungsvariablen in die Konfiguration gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-188">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="a04dd-189">Im folgenden Beispiel wird der Verbindungszeichenfolgeschlüssel `ConnectionStrings:DefaultConnection` als `ConnectionStrings__DefaultConnection` in die Dienstdefinitionsdatei platziert:</span><span class="sxs-lookup"><span data-stu-id="a04dd-189">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="a04dd-190">So speichern Sie die Datei und aktivieren den Dienst:</span><span class="sxs-lookup"><span data-stu-id="a04dd-190">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="a04dd-191">So starten Sie den Dienst und überprüfen, ob er ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="a04dd-191">Start the service and verify that it's running:</span></span>

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

<span data-ttu-id="a04dd-192">Wenn der Reverseproxy konfiguriert ist und Kestrel durch *systemd* verwaltet wird, ist die Web-App vollständig konfiguriert. Auf diese kann dann über einen Browser auf dem lokalen Computer unter `http://localhost` zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-192">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="a04dd-193">Beim Überprüfen der Antwortheader wird im Header **Server** angezeigt, dass die ASP.NET Core-App von Kestrel verarbeitet wird:</span><span class="sxs-lookup"><span data-stu-id="a04dd-193">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="a04dd-194">Protokoll anzeigen...</span><span class="sxs-lookup"><span data-stu-id="a04dd-194">View logs</span></span>

<span data-ttu-id="a04dd-195">Da die von Kestrel verwendete Web-App von *systemd* verwaltet wird, werden Ereignisse und Protokolle in einem zentralen Journal protokolliert.</span><span class="sxs-lookup"><span data-stu-id="a04dd-195">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="a04dd-196">Dieses Journal enthält jedoch Einträge für alle Dienste und Prozesse, die von *systemd* verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-196">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="a04dd-197">Verwenden Sie folgenden Befehl, um die `kestrel-helloapp.service`-spezifischen Elemente anzuzeigen:</span><span class="sxs-lookup"><span data-stu-id="a04dd-197">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="a04dd-198">Geben Sie für die Uhrzeitfilterung mit dem Befehl Uhrzeitoptionen an.</span><span class="sxs-lookup"><span data-stu-id="a04dd-198">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="a04dd-199">Verwenden Sie beispielsweise `--since today` zum Filtern für den aktuellen Tag oder `--until 1 hour ago`, um die Einträge der letzten Stunde anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-199">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="a04dd-200">Weitere Informationen finden Sie auf der [Manpage für „journalctl“](https://www.unix.com/man-page/centos/1/journalctl/).</span><span class="sxs-lookup"><span data-stu-id="a04dd-200">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="a04dd-201">Schutz von Daten</span><span class="sxs-lookup"><span data-stu-id="a04dd-201">Data protection</span></span>

<span data-ttu-id="a04dd-202">Der [Stapel zum Schutz von Daten in ASP.NET Core](xref:security/data-protection/introduction) wird von mehreren [ASP.NET Core-Middlewares](xref:fundamentals/middleware/index) verwendet. Hierzu gehören die Authentifizierungsmiddleware (zum Beispiel die Cookiemiddleware) und Maßnahmen zum Schutz vor websiteübergreifenden Anforderungsfälschungen (CSRF).</span><span class="sxs-lookup"><span data-stu-id="a04dd-202">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="a04dd-203">Selbst wenn Datenschutz-APIs nicht im Benutzercode aufgerufen werden, sollte der Schutz von Daten konfiguriert werden, um einen persistenten kryptografischen [Schlüsselspeicher](xref:security/data-protection/implementation/key-management) zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-203">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="a04dd-204">Wenn der Schutz von Daten nicht konfiguriert ist, werden die Schlüssel beim Neustarten der App im Arbeitsspeicher gespeichert und verworfen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-204">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="a04dd-205">Falls der Schlüsselbund im Arbeitsspeicher gespeichert wird, wenn die App neu gestartet wird, gilt Folgendes:</span><span class="sxs-lookup"><span data-stu-id="a04dd-205">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="a04dd-206">Alle cookiebasierten Authentifizierungstoken für ungültig erklärt.</span><span class="sxs-lookup"><span data-stu-id="a04dd-206">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="a04dd-207">Benutzer müssen sich bei ihrer nächsten Anforderung erneut anmelden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-207">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="a04dd-208">Alle mit dem Schlüsselbund geschützte Daten können nicht mehr entschlüsselt werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-208">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="a04dd-209">Dies kann [CSRF-Token](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) und [ASP.NET Core-MVC-TempData-Cookies](xref:fundamentals/app-state#tempdata) einschließen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-209">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="a04dd-210">Wenn Sie den Schutz von Daten konfigurieren möchten, um den Schlüsselring persistent zu speichern und zu verschlüsseln, finden Sie in den folgenden Artikeln weitere Informationen:</span><span class="sxs-lookup"><span data-stu-id="a04dd-210">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="a04dd-211">Sichern der App</span><span class="sxs-lookup"><span data-stu-id="a04dd-211">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="a04dd-212">Konfigurieren der Firewall</span><span class="sxs-lookup"><span data-stu-id="a04dd-212">Configure firewall</span></span>

<span data-ttu-id="a04dd-213">*Firewalld* ist ein dynamischer Daemon zum Verwalten der Firewall mit Unterstützung für Netzwerkzonen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-213">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="a04dd-214">Ports und Paketfilterung können weiterhin mit „iptables“ verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-214">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="a04dd-215">*Firewalld* sollte standardmäßig installiert werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-215">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="a04dd-216">`yum` kann für die Installation oder zur Überprüfung einer erfolgreichen Installation verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-216">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="a04dd-217">Mit `firewalld` können Sie nur die für die App erforderlichen Ports öffnen.</span><span class="sxs-lookup"><span data-stu-id="a04dd-217">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="a04dd-218">In diesem Fall werden die Ports 80 und 443 verwendet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-218">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="a04dd-219">Über die folgenden Befehle werden die Ports 80 und 443 dauerhaft geöffnet:</span><span class="sxs-lookup"><span data-stu-id="a04dd-219">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="a04dd-220">Laden Sie die Firewalleinstellungen erneut.</span><span class="sxs-lookup"><span data-stu-id="a04dd-220">Reload the firewall settings.</span></span> <span data-ttu-id="a04dd-221">Überprüfen Sie die verfügbaren Dienste und Ports in der Standardzone.</span><span class="sxs-lookup"><span data-stu-id="a04dd-221">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="a04dd-222">Über `firewall-cmd -h` können Sie verfügbare Optionen ermitteln.</span><span class="sxs-lookup"><span data-stu-id="a04dd-222">Options are available by inspecting `firewall-cmd -h`.</span></span>

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

### <a name="https-configuration"></a><span data-ttu-id="a04dd-223">HTTPS-Konfiguration</span><span class="sxs-lookup"><span data-stu-id="a04dd-223">HTTPS configuration</span></span>

<span data-ttu-id="a04dd-224">**Konfigurieren der App für sichere (HTTPS) lokale Verbindungen**</span><span class="sxs-lookup"><span data-stu-id="a04dd-224">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="a04dd-225">Der [dotnet run](/dotnet/core/tools/dotnet-run)-Befehl verwendet die *Properties/launchSettings.json*-Datei der App, die die App so konfiguriert, dass diese an den URLs lauscht, die von der `applicationUrl`-Eigenschaft bereitgestellt werden, z. B. `https://localhost:5001;http://localhost:5000`.</span><span class="sxs-lookup"><span data-stu-id="a04dd-225">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="a04dd-226">Konfigurieren Sie mithilfe eines der folgenden Ansätze die App so, dass sie bei der Entwicklung für den Befehl `dotnet run` oder die Entwicklungsumgebung (F5 oder STRG+F5 in Visual Studio Code) ein Zertifikat verwendet:</span><span class="sxs-lookup"><span data-stu-id="a04dd-226">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="a04dd-227">[Ersetzen des Standardzertifikats aus der Konfiguration](xref:fundamentals/servers/kestrel#configuration) (*Empfohlen*)</span><span class="sxs-lookup"><span data-stu-id="a04dd-227">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="a04dd-228">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="a04dd-228">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="a04dd-229">**Konfigurieren des Reverseproxys für sichere (HTTPS) Clientverbindungen**</span><span class="sxs-lookup"><span data-stu-id="a04dd-229">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="a04dd-230">Für die Konfiguration von Apache für HTTPS wird das Modul *mod_ssl* verwendet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-230">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="a04dd-231">Wenn das Modul *httpd* installiert wurde, wurde das Modul *mod_ssl* ebenfalls installiert.</span><span class="sxs-lookup"><span data-stu-id="a04dd-231">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="a04dd-232">Wurde es nicht installiert, fügen Sie es mit `yum` zur Konfiguration hinzu.</span><span class="sxs-lookup"><span data-stu-id="a04dd-232">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="a04dd-233">Installieren Sie zur Erzwingung von HTTPS das Modul `mod_rewrite`, um eine URL-Umschreibung zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="a04dd-233">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="a04dd-234">Ändern Sie die Datei *helloapp.conf*, um eine URL-Umschreibung und sichere Kommunikation an Port 443 zu aktivieren:</span><span class="sxs-lookup"><span data-stu-id="a04dd-234">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

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
> <span data-ttu-id="a04dd-235">In diesem Beispiel wird ein lokal generiertes Zertifikat verwendet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-235">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="a04dd-236">**SSLCertificateFile** muss die primäre Zertifikatdatei für den Domänennamen sein.</span><span class="sxs-lookup"><span data-stu-id="a04dd-236">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="a04dd-237">**SSLCertificateKeyFile** muss die Schlüsseldatei sein, die beim Erstellen der Zertifikatsignaturanforderung (Certificate Signing Request, CSR) generiert wurde.</span><span class="sxs-lookup"><span data-stu-id="a04dd-237">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="a04dd-238">**SSLCertificateChainFile** muss die Zwischenzertifikatdatei (falls vorhanden) sein, die von der Zertifizierungsstelle bereitgestellt wurde.</span><span class="sxs-lookup"><span data-stu-id="a04dd-238">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="a04dd-239">So speichern Sie die Datei und testen die Konfiguration:</span><span class="sxs-lookup"><span data-stu-id="a04dd-239">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="a04dd-240">So starten Sie Apache neu:</span><span class="sxs-lookup"><span data-stu-id="a04dd-240">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="a04dd-241">Weitere Vorschläge für Apache</span><span class="sxs-lookup"><span data-stu-id="a04dd-241">Additional Apache suggestions</span></span>

### <a name="additional-headers"></a><span data-ttu-id="a04dd-242">Zusätzliche Header</span><span class="sxs-lookup"><span data-stu-id="a04dd-242">Additional headers</span></span>

<span data-ttu-id="a04dd-243">Als Schutz gegen böswillige Angriffe müssen einige Header geändert oder hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-243">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="a04dd-244">So stellen Sie sicher, dass das Modul `mod_headers` installiert wurde:</span><span class="sxs-lookup"><span data-stu-id="a04dd-244">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="a04dd-245">Schützen von Apache vor Clickjacking-Angriffen</span><span class="sxs-lookup"><span data-stu-id="a04dd-245">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="a04dd-246">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), auch bekannt als *UI Redress-Angriff*, ist ein böswilliger Angriff, bei dem ein Websitebesucher dazu verleitet wird, auf einen Link oder eine Schaltfläche zu klicken, der bzw. die sich auf einer anderen Seite als der aktuell besuchten Seite befindet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-246">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="a04dd-247">Verwenden Sie `X-FRAME-OPTIONS` zum Sichern der Website.</span><span class="sxs-lookup"><span data-stu-id="a04dd-247">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="a04dd-248">So wehren Sie Clickjacking-Angriffe ab:</span><span class="sxs-lookup"><span data-stu-id="a04dd-248">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="a04dd-249">So bearbeiten Sie die Datei *httpd.conf*:</span><span class="sxs-lookup"><span data-stu-id="a04dd-249">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="a04dd-250">Fügen Sie die Zeile `Header append X-FRAME-OPTIONS "SAMEORIGIN"` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a04dd-250">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="a04dd-251">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="a04dd-251">Save the file.</span></span>
1. <span data-ttu-id="a04dd-252">Starten Sie Apache neu.</span><span class="sxs-lookup"><span data-stu-id="a04dd-252">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="a04dd-253">MIME-Typermittlung</span><span class="sxs-lookup"><span data-stu-id="a04dd-253">MIME-type sniffing</span></span>

<span data-ttu-id="a04dd-254">Der Header `X-Content-Type-Options` verhindert, dass eine *MIME-Ermittlung* über Internet Explorer (dabei wird der `Content-Type` einer Datei aus dem Dateiinhalt bestimmt) erfolgt.</span><span class="sxs-lookup"><span data-stu-id="a04dd-254">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="a04dd-255">Wenn der Server den Header `Content-Type` auf `text/html` festlegt und die Option `nosniff` festgelegt ist, rendert Internet Explorer den Inhalt unabhängig vom Dateiinhalt als `text/html`.</span><span class="sxs-lookup"><span data-stu-id="a04dd-255">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="a04dd-256">So bearbeiten Sie die Datei *httpd.conf*:</span><span class="sxs-lookup"><span data-stu-id="a04dd-256">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="a04dd-257">Fügen Sie die Zeile `Header set X-Content-Type-Options "nosniff"` hinzu.</span><span class="sxs-lookup"><span data-stu-id="a04dd-257">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="a04dd-258">Speichern Sie die Datei.</span><span class="sxs-lookup"><span data-stu-id="a04dd-258">Save the file.</span></span> <span data-ttu-id="a04dd-259">Starten Sie Apache neu.</span><span class="sxs-lookup"><span data-stu-id="a04dd-259">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="a04dd-260">Lastenausgleich</span><span class="sxs-lookup"><span data-stu-id="a04dd-260">Load Balancing</span></span>

<span data-ttu-id="a04dd-261">Dieses Beispiel veranschaulicht das Einrichten und Konfigurieren von Apache unter CentOS 7 und Kestrel auf demselben Instanzcomputer.</span><span class="sxs-lookup"><span data-stu-id="a04dd-261">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="a04dd-262">Damit es zu keinem Single Point of Failure kommt, ermöglicht die Verwendung von *mod_proxy_balancer* und das Ändern von **VirtualHost** das Verwalten mehrerer Instanzen der Web-Apps hinter dem Apache-Proxyserver.</span><span class="sxs-lookup"><span data-stu-id="a04dd-262">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="a04dd-263">In der unten dargestellten Konfigurationsdatei wird eine zusätzliche Instanz von `helloapp` zur Ausführung an Port 5001 eingerichtet.</span><span class="sxs-lookup"><span data-stu-id="a04dd-263">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="a04dd-264">Der Abschnitt *Proxy* wird mit einer Lastenausgleichskonfiguration mit zwei Mitgliedern für den Lastenausgleich von *byrequests* festgelegt.</span><span class="sxs-lookup"><span data-stu-id="a04dd-264">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

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

### <a name="rate-limits"></a><span data-ttu-id="a04dd-265">Begrenzung der Bandbreite</span><span class="sxs-lookup"><span data-stu-id="a04dd-265">Rate Limits</span></span>

<span data-ttu-id="a04dd-266">Mit dem Modul *mod_ratelimit*, das im Modul *httpd* enthalten ist, kann die Bandbreite von Clients begrenzt werden:</span><span class="sxs-lookup"><span data-stu-id="a04dd-266">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="a04dd-267">In der Beispieldatei wird die Bandbreite am Stammspeicherort auf 600 KB/Sek. begrenzt:</span><span class="sxs-lookup"><span data-stu-id="a04dd-267">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="a04dd-268">Lange Anforderungsheaderfelder</span><span class="sxs-lookup"><span data-stu-id="a04dd-268">Long request header fields</span></span>

<span data-ttu-id="a04dd-269">Die Standardeinstellungen für den Proxyserver schränken die Anforderungsheaderfelder in der Regel auf 8.190 Bytes ein.</span><span class="sxs-lookup"><span data-stu-id="a04dd-269">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="a04dd-270">Eine App erfordert möglicherweise Felder, die länger als die Standardwerte sind (z. B. Apps, die [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) verwenden).</span><span class="sxs-lookup"><span data-stu-id="a04dd-270">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="a04dd-271">Wenn längere Felder erforderlich sind, muss die Anweisung [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) des Proxyservers angepasst werden.</span><span class="sxs-lookup"><span data-stu-id="a04dd-271">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="a04dd-272">Der anzuwendende Wert hängt vom jeweiligen Szenario ab.</span><span class="sxs-lookup"><span data-stu-id="a04dd-272">The value to apply depends on the scenario.</span></span> <span data-ttu-id="a04dd-273">Weitere Informationen finden Sie in der Dokumentation Ihres Servers.</span><span class="sxs-lookup"><span data-stu-id="a04dd-273">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="a04dd-274">Erhöhen Sie den Standardwert von `LimitRequestFieldSize` nur dann, wenn dies absolut erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="a04dd-274">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="a04dd-275">Ein Erhöhen dieses Werts vergrößert das Risiko von Pufferüberlauf- (Überlauf-) und Denial-of-Service-Angriffen durch böswillige Benutzer.</span><span class="sxs-lookup"><span data-stu-id="a04dd-275">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a04dd-276">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="a04dd-276">Additional resources</span></span>

* [<span data-ttu-id="a04dd-277">Voraussetzungen für .NET Core unter Linux</span><span class="sxs-lookup"><span data-stu-id="a04dd-277">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
