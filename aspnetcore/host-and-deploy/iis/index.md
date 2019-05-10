---
title: Hosten von ASP.NET Core unter Windows mit IIS
author: guardrex
description: Erfahren Sie, wie ASP.NET Core-Apps in Windows Server Internet Information Services (IIS) gehostet werden.
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2019
uid: host-and-deploy/iis/index
ms.openlocfilehash: 65721a734cb35a2b20fd283ad54237eb896083a9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882615"
---
# <a name="host-aspnet-core-on-windows-with-iis"></a>Hosten von ASP.NET Core unter Windows mit IIS

Von [Luke Latham](https://github.com/guardrex)

[Installieren des .NET Core Hosting-Pakets](#install-the-net-core-hosting-bundle)

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Die folgenden Betriebssysteme werden unterstützt:

* Windows 7 oder höher
* Windows Server 2008 R2 oder höher

Der [HTTP.sys-Server](xref:fundamentals/servers/httpsys) (zuvor WebListener genannt) funktioniert nicht in einer Reverseproxykonfiguration mit IIS. Verwenden Sie den [Kestrel-Server](xref:fundamentals/servers/kestrel).

Weitere Informationen zum Hosten in Azure finden Sie unter <xref:host-and-deploy/azure-apps/index>.

## <a name="supported-platforms"></a>Unterstützte Plattformen

Apps, die für Bereitstellungen für 32-Bit-Systeme (x86) und 64-Bit-Systeme (x64) veröffentlicht wurden, werden unterstützt. Stellen Sie eine 32-Bit-App bereit, sofern für die App nicht Folgendes zutrifft:

* Die App benötigt den größeren Adressraum des virtuellen Arbeitsspeichers, der 64-Bit-Apps zur Verfügung steht.
* Die App erfordert den größeren IIS-Stapel.
* Die App weist native 64-Bit-Abhängigkeiten auf.

## <a name="application-configuration"></a>Anwendungskonfiguration

### <a name="enable-the-iisintegration-components"></a>Aktivieren der IISIntegration-Komponenten

::: moniker range=">= aspnetcore-2.1"

Eine typische *Program.cs*-Datei ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, um mit der Einrichtung eines Hosts zu beginnen:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Eine typische *Program.cs*-Datei ruft <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> auf, um mit der Einrichtung eines Hosts zu beginnen:

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        ...
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

**In-Process-Hostingmodell**

`CreateDefaultBuilder` ruft die `UseIIS`-Methode auf, um die [CoreCLR](/dotnet/standard/glossary#coreclr) zu starten und die App im IIS-Workerprozess zu hosten (*w3wp.exe* oder *iisexpress.exe*). Leistungstests weisen darauf hin, dass das In-Process-Hosting einer .NET Core-App einen weitaus höheren Anforderungsdurchsatz im Vergleich zum Out-of-Process-Hosting der App mit Weiterleitung der Anforderungen über einen Proxy an [Kestrel](xref:fundamentals/servers/kestrel) Server bietet.

Das In-Process-Hostingmodell wird nicht für ASP.NET Core-Apps unterstützt, die auf .NET Framework abzielen.

**Out-of-Process-Hostingmodell**

Für das Out-of-Process-Hosting mit IIS konfiguriert `CreateDefaultBuilder` [Kestrel](xref:fundamentals/servers/kestrel) Server als Webserver und aktiviert die IIS-Integration durch Konfigurieren des Basispfads und -ports für das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).

Das ASP.NET Core-Modul generiert einen dynamischen Port, der dem Back-End-Prozess zugewiesen wird. `CreateDefaultBuilder` ruft die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>-Methode auf. `UseIISIntegration` konfiguriert Kestrel so, dass an dem dynamischen Port an der Localhost-IP-Adresse (`127.0.0.1`) gelauscht wird. Wenn der dynamische Port 1234 ist, lauscht Kestrel an `127.0.0.1:1234`. Diese Konfiguration ersetzt andere Konfigurationen von:

* `UseUrls`
* [der Listen-API von Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* der [Konfiguration](xref:fundamentals/configuration/index) (oder [der Befehlszeilenoption „--urls](xref:fundamentals/host/web-host#override-configuration))

Aufrufe von `UseUrls` oder der `Listen`-API von Kestrel sind nicht erforderlich, wenn das Modul verwendet wird. Wenn `UseUrls` oder `Listen` aufgerufen wird, lauscht Kestrel nur an die Ports, die bei Ausführung der App ohne IIS angegeben werden.

Weitere Informationen zu In-Process- und Out-of-Process-Hostingmodellen finden Sie im [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) und in der [Referenz zur ASP.NET Core-Modulkonfiguration](xref:host-and-deploy/aspnet-core-module).

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`CreateDefaultBuilder` konfiguriert [Kestrel](xref:fundamentals/servers/kestrel) Server als Webserver und aktiviert die IIS-Integration durch Konfigurierung des Basispfads und Ports für das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).

Das ASP.NET Core-Modul generiert einen dynamischen Port, der dem Back-End-Prozess zugewiesen wird. `CreateDefaultBuilder` ruft die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>-Methode auf. `UseIISIntegration` konfiguriert Kestrel so, dass an dem dynamischen Port an der Localhost-IP-Adresse (`127.0.0.1`) gelauscht wird. Wenn der dynamische Port 1234 ist, lauscht Kestrel an `127.0.0.1:1234`. Diese Konfiguration ersetzt andere Konfigurationen von:

* `UseUrls`
* [der Listen-API von Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* der [Konfiguration](xref:fundamentals/configuration/index) (oder [der Befehlszeilenoption „--urls](xref:fundamentals/host/web-host#override-configuration))

Aufrufe von `UseUrls` oder der `Listen`-API von Kestrel sind nicht erforderlich, wenn das Modul verwendet wird. Wenn `UseUrls` oder `Listen` aufgerufen wird, lauscht Kestrel an dem Port, der bei der bei Ausführung der App ohne den IIS angegeben wird.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`CreateDefaultBuilder` konfiguriert [Kestrel](xref:fundamentals/servers/kestrel) Server als Webserver und aktiviert die IIS-Integration durch Konfigurierung des Basispfads und Ports für das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).

Das ASP.NET Core-Modul generiert einen dynamischen Port, der dem Back-End-Prozess zugewiesen wird. `CreateDefaultBuilder` ruft die <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>-Methode auf. `UseIISIntegration` konfiguriert Kestrel so, dass an dem dynamischen Port an der Localhost-IP-Adresse (`localhost`) gelauscht wird. Wenn der dynamische Port 1234 ist, lauscht Kestrel an `localhost:1234`. Diese Konfiguration ersetzt andere Konfigurationen von:

* `UseUrls`
* [der Listen-API von Kestrel](xref:fundamentals/servers/kestrel#endpoint-configuration)
* der [Konfiguration](xref:fundamentals/configuration/index) (oder [der Befehlszeilenoption „--urls](xref:fundamentals/host/web-host#override-configuration))

Aufrufe von `UseUrls` oder der `Listen`-API von Kestrel sind nicht erforderlich, wenn das Modul verwendet wird. Wenn `UseUrls` oder `Listen` aufgerufen wird, lauscht Kestrel an dem Port, der bei der bei Ausführung der App ohne den IIS angegeben wird.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Nehmen Sie eine Abhängigkeit vom Paket [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/) in die App-Abhängigkeiten auf. Verwenden Sie Middleware für die Integration von IIS, indem Sie die Erweiterungsmethode <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> zu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder> hinzufügen:

```csharp
var host = new WebHostBuilder()
    .UseKestrel()
    .UseIISIntegration()
    ...
```

Es sind jeweils <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> und <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> erforderlich. Ein Code, der `UseIISIntegration` aufruft, hat keinen Einfluss auf die Codeportabilität. Wenn die App nicht hinter IIS ausgeführt wird (die App wird z.B. direkt auf Kestrel ausgeführt), hat `UseIISIntegration` keine Funktion.

Das ASP.NET Core-Modul generiert einen dynamischen Port, der dem Back-End-Prozess zugewiesen wird. `UseIISIntegration` konfiguriert Kestrel so, dass an dem dynamischen Port an der Localhost-IP-Adresse (`localhost`) gelauscht wird. Wenn der dynamische Port 1234 ist, lauscht Kestrel an `localhost:1234`. Diese Konfiguration ersetzt andere Konfigurationen von:

* `UseUrls`
* der [Konfiguration](xref:fundamentals/configuration/index) (oder [der Befehlszeilenoption „--urls](xref:fundamentals/host/web-host#override-configuration))

Ein Aufruf von `UseUrls` ist nicht erforderlich, wenn das Modul verwendet wird. Wenn `UseUrls` aufgerufen wird, lauscht Kestrel an den Port, der nur bei der Ausführung der App ohne den IIS angegeben wird.

Wenn `UseUrls` in einer ASP.NET Core 1.0-App aufgerufen wird, rufen Sie es **vor** Aufrufen von `UseIISIntegration` auf, damit der vom Modul konfigurierte Port nicht überschrieben wird. Diese Aufrufreihenfolge ist in ASP.NET Core 1.1 nicht erforderlich, weil die Moduleinstellung `UseUrls` überschreibt.

::: moniker-end

Weitere Informationen zum Hosten finden Sie unter [Hosten in ASP.NET Core](xref:fundamentals/index#host).

### <a name="iis-options"></a>IIS-Optionen

::: moniker range=">= aspnetcore-2.2"

**In-Process-Hostingmodell**

Schließen Sie zur Konfiguration von IIS-Serveroptionen eine Dienstkonfiguration für <xref:Microsoft.AspNetCore.Builder.IISServerOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> ein. Mit dem folgenden Beispiel wird AutomaticAuthentication deaktiviert:

```csharp
services.Configure<IISServerOptions>(options => 
{
    options.AutomaticAuthentication = false;
});
```

| Option                         | Standard | Einstellung |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Bei Festlegung auf `true` legt der Server den per [Windows-Authentifizierung](xref:security/authentication/windowsauth) authentifizierten `HttpContext.User` fest. Bei Festlegung auf `false` stellt der Server nur eine Identität für `HttpContext.User` bereit und antwortet auf explizite Anforderungen durch `AuthenticationScheme`. Die Windows-Authentifizierung muss in IIS aktiviert sein, damit `AutomaticAuthentication` funktioniert. Weitere Informationen finden Sie unter [Windows-Authentifizierung](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Legt den Anzeigename fest, der Benutzern auf Anmeldungsseiten angezeigt wird |

**Out-of-Process-Hostingmodell**

::: moniker-end

Schließen Sie zur Konfiguration von IIS-Optionen eine Dienstkonfiguration für <xref:Microsoft.AspNetCore.Builder.IISOptions> in <xref:Microsoft.AspNetCore.Hosting.IStartup.ConfigureServices*> ein. Im folgenden Beispiel wird Verhindert, dass die App `HttpContext.Connection.ClientCertificate` auffüllt:

```csharp
services.Configure<IISOptions>(options => 
{
    options.ForwardClientCertificate = false;
});
```

| Option                         | Standard | Einstellung |
| ------------------------------ | :-----: | ------- |
| `AutomaticAuthentication`      | `true`  | Bei Festlegung auf `true` legt die Middleware für die IIS-Integration den per [Windows-Authentifizierung](xref:security/authentication/windowsauth) authentifizierten `HttpContext.User` fest. Bei Festlegung auf `false` stellt die Middleware nur eine Identität für `HttpContext.User` bereit und antwortet auf explizite Anforderungen durch `AuthenticationScheme`. Die Windows-Authentifizierung muss in IIS aktiviert sein, damit `AutomaticAuthentication` funktioniert. Weitere Informationen finden Sie im Thema [Windows-Authentifizierung](xref:security/authentication/windowsauth). |
| `AuthenticationDisplayName`    | `null`  | Legt den Anzeigename fest, der Benutzern auf Anmeldungsseiten angezeigt wird |
| `ForwardClientCertificate`     | `true`  | Wenn diese Option `true` ist und der Anforderungsheader `MS-ASPNETCORE-CLIENTCERT` vorhanden ist, wird das `HttpContext.Connection.ClientCertificate` aufgefüllt. |

### <a name="proxy-server-and-load-balancer-scenarios"></a>Proxyserver und Lastenausgleichsszenarien

Die Middleware für die Integration von IIS, die ForwardedHeadersMiddleware konfiguriert, und das ASP.NET Core-Modul sind so konfiguriert, dass sie das Schema (HTTP/HTTPS) und die Remote-IP-Adresse an die Stelle weiterleiten, von der die Anforderung stammte. Möglicherweise ist zusätzliche Konfiguration für Apps erforderlich, die hinter weiteren Proxyservern und Lastenausgleichsmodulen (Load Balancer) gehostet werden. Weitere Informationen hierzu feinden Sie unter [Konfigurieren von ASP.NET Core zur Verwendung mit Proxyservern und Lastenausgleich](xref:host-and-deploy/proxy-load-balancer).

### <a name="webconfig-file"></a>Datei „web.config“

Mit der Datei *web.config* wird das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) konfiguriert. Die Erstellung, Transformation und Veröffentlichung der Datei *web.config* wird bei der Projektveröffentlichung von einem MSBuild-Ziel (`_TransformWebConfig`) abgewickelt. Dieses Ziel ist in den Web SDK-Zielen (`Microsoft.NET.Sdk.Web`) vorhanden. Das SDK wird am Anfang der Projektdatei festgelegt:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Wenn in der Projektdatei keine Datei namens *web.config* vorhanden ist, wird sie zur Konfiguration des [ASP.NET Core-Moduls](xref:host-and-deploy/aspnet-core-module) mit dem richtigen *processPath*- und *arguments*-Attribut erstellt und in die [veröffentlichte Ausgabe](xref:host-and-deploy/directory-structure) verschoben.

Ist eine Datei namens *web.config* im Projekt vorhanden, wird sie zur Konfiguration des ASP.NET Core-Moduls mit dem richtigen *processPath*- und *arguments*-Attribut transformiert und in die veröffentlichte Ausgabe verschoben. Die Transformation ändert die IIS-Konfigurationseinstellungen in der Datei nicht.

Die Datei *web.config* kann zusätzliche IIS-Konfigurationseinstellungen zum Steuern der aktiven IIS-Module bereitstellen. Informationen zu IIS-Modulen, die Anforderungen mit ASP.NET Core-Apps verarbeiten können, finden Sie im Thema [IIS-Module](xref:host-and-deploy/iis/modules).

Um zu verhindern, dass das Web-SDK die Datei *web.config* transformiert, verwenden Sie die Eigenschaft **\<IsTransformWebConfigDisabled>** in der Projektdatei:

```xml
<PropertyGroup>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

Wenn die Transformation der Datei durch das Web-SDK deaktiviert wird, müssen die Attribute *processPath* und *arguments* manuell durch den Entwickler festgelegt werden. Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module).

### <a name="webconfig-file-location"></a>Speicherort der Datei „web.config“

Zum Erstellen des [ASP.NET Core-Moduls](xref:host-and-deploy/aspnet-core-module) muss die Datei *web.config* im Stammpfad des Inhalts (normalerweise dem App-Basispfad) der bereitgestellten App vorhanden sein. Dies ist der physische Pfad der Website, der in IIS bereitgestellt wurde. Die Datei *Web.config* wird im Stammverzeichnis der App benötigt, um die Veröffentlichung mehrerer Apps mit Web Deploy zu ermöglichen.

Im physischen Pfad der App sind vertrauliche Dateien vorhanden, wie z.B. *\<assembly_name>.runtimeconfig.json*, *\<assembly_name>.xml* (XML-Dokumentationskommentare) und *\<assembly_name>.deps.json*. Wenn die *web.config*-Datei vorhanden ist und die Website normal startet, werden diese vertraulichen Dateien bei Anforderung nicht offengelegt. Wenn die Datei *web.config* fehlt, falsch benannt wurde oder die Website nicht für den normalen Start konfigurieren kann, macht IIS vertrauliche Dateien möglicherweise öffentlich verfügbar.

**Die Datei *web.config* muss immer in der Bereitstellung vorhanden, richtig benannt und in der Lage sein, die Website für einen normalen Start zu konfigurieren. Entfernen Sie die Datei *web.config* niemals aus einer Produktionsbereitstellung.**

### <a name="transform-webconfig"></a>Transformieren von web.config

Wenn Sie *web.config* beim Veröffentlichen transformieren müssen (z.B. Umgebungsvariablen basierend auf der Konfiguration, dem Profil oder der Umgebung festlegen müssen), finden Sie weitere Informationen unter <xref:host-and-deploy/iis/transform-webconfig>.

## <a name="iis-configuration"></a>IIS-Konfiguration

**Windows Server-Betriebssysteme**

Aktivieren Sie die Serverrolle **Webserver (IIS)**, und richten Sie Rollendienste ein.

1. Verwenden Sie den Assistenten **Rollen und Features hinzufügen** im Menü **Verwalten** oder den Link in **Server-Manager**. Aktivieren Sie im Schritt **Serverrollen** das Kontrollkästchen für **Webserver (IIS)**.

   ![Die Rolle „Webserver (IIS)“ wird im Schritt „Serverrollen auswählen“ ausgewählt.](index/_static/server-roles-ws2016.png)

1. Nach dem Schritt **Features** wird der Schritt **Rollendienste** für Webserver (IIS) geladen. Wählen Sie die gewünschten IIS-Rollendienste aus, oder übernehmen Sie die bereitgestellten Standardrollendienste.

   ![Die Standardrollendienste werden im Schritt „Rollendienste auswählen“ ausgewählt.](index/_static/role-services-ws2016.png)

   **Windows-Authentifizierung (optional)**  
   Um die Windows-Authentifizierung zu aktivieren, erweitern Sie die folgenden Knoten: **Webserver** > **Sicherheit**. Wählen Sie das Feature **Windows-Authentifizierung** aus. Weitere Informationen finden Sie unter [Windows-Authentifizierung \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) und [Konfigurieren der Windows-Authentifizierung](xref:security/authentication/windowsauth).

   **WebSockets (optional)**  
   WebSockets wird mit ASP.NET Core 1.1 oder höher unterstützt. Um WebSockets zu aktivieren, erweitern Sie die folgenden Knoten: **Webserver** > **Anwendungsentwicklung**. Wählen Sie das Feature **WebSocket-Protokoll** aus. Weitere Informationen finden Sie unter [WebSockets](xref:fundamentals/websockets).

1. Fahren Sie mit dem Schritt **Bestätigung** fort, um die Webserverrolle und die Dienste zu installieren. Ein Server-/IIS-Neustart ist nach der Installation der Rolle **Webserver (IIS)** nicht erforderlich.

**Windows-Desktopbetriebssysteme**

Aktivieren Sie die **IIS-Verwaltungskonsole** und die **WWW-Dienste**.

1. Navigieren Sie zu **Systemsteuerung** > **Programme** > **Programme und Features** > **Windows-Features aktivieren oder deaktivieren** (links auf dem Bildschirm).

1. Öffnen Sie den Knoten **Internetinformationsdienste**. Öffnen Sie den Knoten **Webverwaltungstools**.

1. Aktivieren Sie das Kontrollkästchen für **IIS-Verwaltungskonsole**.

1. Aktivieren Sie das Kontrollkästchen für **WWW-Dienste**.

1. Akzeptieren Sie die Standardfeatures für **WWW-Dienste**, oder passen Sie die IIS-Features an.

   **Windows-Authentifizierung (optional)**  
   Um die Windows-Authentifizierung zu aktivieren, erweitern Sie die folgenden Knoten: **WWW-Dienste** > **Sicherheit**. Wählen Sie das Feature **Windows-Authentifizierung** aus. Weitere Informationen finden Sie unter [Windows-Authentifizierung \<windowsAuthentication>](/iis/configuration/system.webServer/security/authentication/windowsAuthentication/) und [Konfigurieren der Windows-Authentifizierung](xref:security/authentication/windowsauth).

   **WebSockets (optional)**  
   WebSockets wird mit ASP.NET Core 1.1 oder höher unterstützt. Um WebSockets zu aktivieren, erweitern Sie die folgenden Knoten: **WWW-Dienste** > **Anwendungsentwicklungsfeatures**. Wählen Sie das Feature **WebSocket-Protokoll** aus. Weitere Informationen finden Sie unter [WebSockets](xref:fundamentals/websockets).

1. Wenn die IIS-Installation einen Neustart erfordert, starten Sie das System neu.

![Die IIS-Verwaltungskonsole und WWW-Dienste werden in Windows-Features ausgewählt.](index/_static/windows-features-win10.png)

## <a name="install-the-net-core-hosting-bundle"></a>Installieren des .NET Core Hosting-Pakets

Installieren Sie das *Paket „.NET Core Hosting“* im Hostsystem. Das Paket installiert die .NET Core-Runtime, die .NET Core-Bibliothek und das [ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module). Mit dem Modul können ASP.NET Core-Apps hinter IIS ausgeführt werden. Wenn das System nicht über eine Internetverbindung verfügt, beziehen und installieren Sie [Microsoft Visual C++ 2015 Redistributable](https://www.microsoft.com/download/details.aspx?id=53840), bevor Sie das Paket „.NET Core Hosting“ installieren.

> [!IMPORTANT]
> Wenn das Hosting-Paket vor IIS installiert wird, muss die Paketinstallation repariert werden. Führen Sie nach der Installation von IIS erneut den Installer des Hosting-Pakets aus.
>
> Wenn das Hosting-Paket nach der Installation der 64-Bit-Version (x64) von .NET Core installiert wird, kann es den Anschein haben, dass SDKs fehlen ([Es wurden keine .NET Core SDKs erkannt](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Informationen zum Beheben des Problems finden Sie unter <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle>.

### <a name="direct-download-current-version"></a>Direkter Download (aktuelle Version)

Laden Sie den Installer über folgenden Link herunter:

[Aktueller Installer für das .NET Core Hosting-Paket (direkter Download)](https://www.microsoft.com/net/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Frühere Versionen des Installers

So erhalten Sie eine frühere Version des Installers:

1. Navigieren Sie zu den[.NET-Downloadarchiven](https://www.microsoft.com/net/download/archives).
1. Wählen Sie unter **.NET Core** die .NET Core-Version aus.
1. Suchen Sie in der Spalte **Run apps - Runtime** (Apps ausführen – Runtime) die Zeile mit der gewünschten .NET Core-RuntimeRuntime-Version.
1. Laden Sie den Installer über den Link **Runtime & Hosting Bundle** (Runtime- und Hosting-Paket) herunter.

> [!WARNING]
> Einige Installer enthalten Releaseversionen, die das Ende ihres Lebenszyklus erreicht haben und nicht mehr von Microsoft unterstützt werden. Weitere Informationen finden Sie in den [Unterstützungsrichtlinien](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Installieren des Hosting-Pakets

1. Führen Sie das Installationsprogramm auf dem Server aus. Die folgenden Parameter sind verfügbar, wenn Sie das Installationsprogramm über eine Administratorbefehlsshell ausführen.

   * `OPT_NO_ANCM=1` &ndash; Überspringen Sie die Installation des ASP.NET Core-Moduls.
   * `OPT_NO_RUNTIME=1` &ndash; Überspringen Sie die Installation der .NET Core Runtime.
   * `OPT_NO_SHAREDFX=1` &ndash; Überspringen Sie die Installation des geteilten ASP.NET Frameworks (ASP.NET Runtime).
   * `OPT_NO_X86=1` &ndash; Überspringen Sie die Installation von X86 Runtimes. Verwenden Sie diesen Parameter, wenn Sie wissen, dass Sie keine 32-Bit-Apps hosten. Sollte die Möglichkeit bestehen, dass Sie sowohl 32-Bit- als auch 64-Bit-Apps hosten könnten, verwenden Sie diesen Parameter nicht, und installieren Sie beide Runtimes.
   * `OPT_NO_SHARED_CONFIG_CHECK=1` &ndash; Deaktivieren Sie die Überprüfung auf Verwendung einer gemeinsamen IIS-Konfiguration (*applicationHost.config*), wenn die gemeinsam genutzte Konfiguration sich auf demselben Computer wie die IIS-Installation befindet. *Nur für Installationsprogramme für Hostingbundles für ASP.NET Core 2.2 oder höher verfügbar.* Weitere Informationen finden Sie unter <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Starten Sie das System neu, oder führen Sie **net stop was /y** gefolgt von **net start w3svc** über eine Befehlsshell aus. Durch den Neustart von IIS wird eine Änderung an der PATH-Systemeinstellung – einer Umgebungsvariable – angewendet, die durch den Installer vorgenommen wurde.

> [!NOTE]
> Informationen zur Verwendung einer IIS-Freigabekonfiguration finden Sie unter [ASP.NET Core-Modul mit IIS-Freigabekonfiguration](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="install-web-deploy-when-publishing-with-visual-studio"></a>Installieren von Web Deploy beim Veröffentlichen mit Visual Studio

Wenn Sie Apps auf Servern mit [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) bereitstellen, installieren Sie die neueste Version von Web Deploy auf dem Server. Um Web Deploy zu installieren, verwenden Sie den [Webplattform-Installer (Web PI)](https://www.microsoft.com/web/downloads/platform.aspx) oder rufen einen Installer direkt aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=43717) ab. Die bevorzugte Methode ist die Verwendung von WebPI. WebPI bietet ein eigenständiges Setup und eine Konfiguration für Hostinganbieter.

## <a name="create-the-iis-site"></a>Erstellen der IIS-Website

1. Erstellen Sie auf dem Hostingsystem einen Ordner zum Speichern der veröffentlichten Ordner und Dateien der App. Das Layout der App-Bereitstellung wird im Thema [Verzeichnisstruktur](xref:host-and-deploy/directory-structure) beschrieben.

1. Öffnen Sie in IIS-Manager den Serverknoten im Bereich **Verbindungen**. Klicken Sie mit der rechten Maustaste auf den Ordner **Websites**. Klicken Sie im Kontextmenü auf **Website hinzufügen**.

1. Geben Sie einen **Websitenamen** an, und legen Sie den **physischen Pfad** auf den Bereitstellungsordner der App fest. Geben Sie die Konfiguration unter **Bindung** an, und erstellen Sie die Website, indem Sie auf **OK** klicken:

   ![Geben Sie den Websitenamen, den physischen Pfad und den Hostnamen im Schritt „Website hinzufügen“ an.](index/_static/add-website-ws2016.png)

   > [!WARNING]
   > Allgemeine Platzhalterbindungen (`http://*:80/` und `http://+:80`) dürfen **nicht** verwendet werden. Platzhalterbindungen auf oberster Ebene gefährden die Sicherheit Ihrer App. Dies gilt für starke und schwache Platzhalter. Verwenden Sie statt Platzhaltern explizite Hostnamen. Platzhalterbindungen in untergeordneten Domänen (z.B. `*.mysub.com`) verursachen kein Sicherheitsrisiko, wenn Sie die gesamte übergeordnete Domäne steuern (im Gegensatz zu `*.com`, das angreifbar ist). Weitere Informationen finden Sie unter [rfc7230 im Abschnitt 5.4](https://tools.ietf.org/html/rfc7230#section-5.4).

1. Wählen Sie unter dem Serverknoten **Anwendungspools** aus.

1. Klicken Sie mit der rechten Maustaste auf den App-Pool der Website, und wählen Sie im Kontextmenü **Grundeinstellungen** aus.

1. Legen Sie im Fenster **Anwendungspool bearbeiten** die **.NET CLR-Version** auf **Kein verwalteter Code** fest:

   ![Legen Sie „Kein verwalteter Code“ für die .NET CLR-Version fest.](index/_static/edit-apppool-ws2016.png)

    ASP.NET Core wird in einem separaten Prozess ausgeführt und verwaltet die Runtime. Für ASP.NET Core ist das Laden der Desktop-CLR nicht erforderlich. Das Festlegen der **.NET CLR-Version** auf **Kein verwalteter Code** ist optional.

1. *ASP.NET Core 2.2 oder höher*: Deaktivieren Sie für eine [eigenständige Bereitstellung](/dotnet/core/deploying/#self-contained-deployments-scd) für 64-Bit-Systeme (x64), die das [In-Process-Hostingmodell](xref:fundamentals/servers/index#in-process-hosting-model) verwendet, den App-Pool für 32-Bit-Prozesse (x86).

   Wählen Sie in der Seitenleiste **Aktionen** von „IIS-Manager > **Anwendungspools**“ die Option **Standardwerte für Anwendungspool festlegen** oder **Erweiterte Einstellungen** aus. Suchen Sie nach **32-Bit-Anwendungen aktivieren**, und legen Sie den Wert auf `False` fest. Diese Einstellung wirkt sich nicht auf Apps aus, die für [Out-of-Process-Hosting](xref:host-and-deploy/aspnet-core-module#out-of-process-hosting-model) bereitgestellt wurden.

1. Vergewissern Sie sich, dass die Prozessmodellidentität über die richtigen Berechtigungen verfügt.

   Wenn die Standardidentität des App-Pools (**Prozessmodell** > **Identität**) von **ApplicationPoolIdentity** in eine andere Identität geändert wird, stellen Sie sicher, dass die neue Identität über die erforderlichen Berechtigungen zum Zugriff auf den Ordner, die Datenbank und andere erforderliche Ressourcen der App verfügt. Der App-Pool benötigt beispielsweise Lese- und Schreibzugriff für Ordner, in denen die App Lese- und Schreibvorgänge für Dateien ausführt.

**Konfigurieren der Windows-Authentifizierung (optional)**  
Weitere Informationen finden Sie unter [Konfigurieren der Windows-Authentifizierung](xref:security/authentication/windowsauth).

## <a name="deploy-the-app"></a>Bereitstellen der App

Stellen Sie die App in dem Ordner bereit, den Sie im Hostingsystem erstellt haben. [Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) ist die empfohlene Methode zur Bereitstellung.

### <a name="web-deploy-with-visual-studio"></a>Web Deploy mit Visual Studio

Informationen zum Erstellen eines Veröffentlichungsprofils zur Verwendung mit Web Deploy finden Sie im Thema [Visual Studio publish profiles for ASP.NET Core app deployment (Visual Studio-Veröffentlichungsprofile zum Bereitstellen von ASP.NET Core-Apps)](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles). Wenn der Hostinganbieter ein Veröffentlichungsprofil oder Unterstützung für das Erstellen eines solchen Profils bereitstellt, laden Sie dessen Profil herunter, und importieren Sie es mithilfe des Visual Studio-Dialogfelds **Veröffentlichen**.

![Dialogfeld „Veröffentlichen“](index/_static/pub-dialog.png)

### <a name="web-deploy-outside-of-visual-studio"></a>Web Deploy außerhalb von Visual Studio

[Web Deploy](/iis/publish/using-web-deploy/introduction-to-web-deploy) kann über die Befehlszeile auch außerhalb von Visual Studio verwendet werden. Weitere Informationen finden Sie unter [Web Deployment Tool](/iis/publish/using-web-deploy/use-the-web-deployment-tool) (Webbereitstellungstool).

### <a name="alternatives-to-web-deploy"></a>Alternativen zu Web Deploy

Verwenden Sie eine der folgenden Methoden, um die App zum Hostingsystem zu migrieren: manuelles Kopieren, XCopy, Robocopy oder PowerShell.

Weitere Informationen zum Bereitstellen von ASP.NET Core in IIS finden Sie im nachfolgenden Abschnitt [Bereitstellungsressourcen für IIS-Administratoren](#deployment-resources-for-iis-administrators).

## <a name="browse-the-website"></a>Navigieren auf der Website

![Der Microsoft Edge-Browser hat die IIS-Startseite geladen.](index/_static/browsewebsite.png)

## <a name="locked-deployment-files"></a>Gesperrte Bereitstellungsdateien

Die Dateien im Bereitstellungsordner werden gesperrt, wenn die App ausgeführt wird. Gesperrte Dateien können während der Bereitstellung nicht überschrieben werden. Um gesperrte Dateien in einer Bereitstellung freizugeben, beenden Sie den App-Pool mit **einer** der folgenden Methoden:

* Verwenden Sie Web Deploy, und verweisen Sie auf `Microsoft.NET.Sdk.Web` in der Projektdatei. Eine Datei namens *app_offline.htm* befindet sich im Stammverzeichnis des Web-App-Verzeichnisses. Wenn die Datei vorhanden ist, fährt das ASP.NET Core Module die App ordnungsgemäß herunter und verarbeitet die Datei *app_offline.htm* während der Bereitstellung. Weitere Informationen finden Sie unter [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).
* Beenden Sie den App-Pool im IIS-Manager auf dem Server manuell.
* Verwenden Sie PowerShell, um *app_offline.html* abzulegen (erfordert PowerShell 5 oder höher):

  ```PowerShell
  $pathToApp = 'PATH_TO_APP'

  # Stop the AppPool
  New-Item -Path $pathToApp app_offline.htm

  # Provide script commands here to deploy the app

  # Restart the AppPool
  Remove-Item -Path $pathToApp app_offline.htm

  ```

## <a name="data-protection"></a>Schutz von Daten

Der [ASP.NET Core-Stapel zum Schutz von Daten](xref:security/data-protection/introduction) wird von mehreren ASP.NET-[Middlewarekomponenten](xref:fundamentals/middleware/index) genutzt, darunter auch von Middleware, die bei der Authentifizierung verwendet wird. Selbst wenn die APIs zum Schutz von Daten nicht aus dem Benutzercode aufgerufen werden, sollte der Schutz von Daten mit einem Bereitstellungsskript oder im Benutzercode konfiguriert werden, um einen persistenten kryptografischen [Schlüsselspeicher](xref:security/data-protection/implementation/key-management) zu erstellen. Wenn der Schutz von Daten nicht konfiguriert ist, werden die Schlüssel beim Neustarten der App im Arbeitsspeicher gespeichert und verworfen.

Falls der Schlüsselbund im Arbeitsspeicher gespeichert wird, wenn die App neu gestartet wird, gilt Folgendes:

* Alle cookiebasierten Authentifizierungstoken für ungültig erklärt. 
* Benutzer müssen sich bei ihrer nächsten Anforderung erneut anmelden. 
* Alle mit dem Schlüsselbund geschützte Daten können nicht mehr entschlüsselt werden. Dies kann [CSRF-Token](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) und [ASP.NET Core-MVC-TempData-Cookies](xref:fundamentals/app-state#tempdata) einschließen.

Zum Konfigurieren des Schutzes von Daten unter IIS mithilfe des persistenten Schlüsselbunds verwenden Sie **einen** der folgenden Ansätze:

* **Erstellen einer Registrierungsstruktur für den Schutz von Daten**

  Schlüssel für den Schutz von Daten, die von ASP.NET Core-Apps verwendet werden, werden in der Registrierung außerhalb der Apps gespeichert. Um die Schlüssel für eine bestimmte App zu dauerhaft zu speichern, müssen Sie Registrierungsschlüssel für den App-Pool erstellen.

  Bei eigenständigen IIS-Installationen, die ohne Webfarm vorgesehen sind, kann das [PowerShell-Skript „Provision-AutoGenKeys.ps1“ für den Schutz von Daten](https://github.com/aspnet/AspNetCore/blob/master/src/DataProtection/Provision-AutoGenKeys.ps1) für jeden App-Pool genutzt werden, das mit einer ASP.NET Core-App verwendet wird. Dieses Skript erstellt einen Registrierungsschlüssel in der HKLM-Registrierung, der nur für das Workerprozesskonto des App-Pools der App zugänglich ist. Schlüssel werden in ruhendem Zustand mit DPAPI mit einem computerweiten Schlüssel verschlüsselt.

  In Webfarmszenarios kann eine App so konfiguriert werden, dass sie einen UNC-Pfad verwendet, um den Schlüsselbund für den Schutz von Daten zu speichern. Standardmäßig werden die Schlüssel für den Schutz von Daten nicht verschlüsselt. Stellen Sie sicher, dass die Dateiberechtigungen für die Netzwerkfreigabe auf das Windows-Konto beschränkt sind, mit dem die App ausgeführt wird. Ein X.509-Zertifikat kann zum Schutz von Schlüsseln im ruhenden Zustand verwendet werden. Richten Sie ggf. einen Mechanismus ein, um es Benutzern zu ermöglichen, Zertifikate hochzuladen: Platzieren Sie Zertifikate im Zertifikatspeicher des Benutzers für vertrauenswürdige Anbieter, und stellen Sie sicher, dass sie auf allen Computern verfügbar sind, auf denen die App des Benutzers ausgeführt wird. Details finden Sie unter [Konfigurieren des Schutzes von Daten in ASP.NET Core](xref:security/data-protection/configuration/overview).

* **Konfigurieren des IIS-Anwendungspools zum Laden des Benutzerprofils**

  Diese Einstellung befindet sich im Abschnitt **Prozessmodell** unter **Erweiterte Einstellungen** für den App-Pool. Legen Sie **Benutzerprofil laden** auf `True` fest. Wenn diese Option auf `True` festgelegt ist, werden Schlüssel im Benutzerprofilverzeichnis gespeichert und mit DPAPI mit einem für das Benutzerkonto spezifischen Schlüssel geschützt. Schlüssel werden im Ordner *%LOCALAPPDATA%/ASP.NET/DataProtection-Keys* gespeichert.

  Das [setProfileEnvironment-Attribut](/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration) des App-Pools muss ebenfalls aktiviert sein. Der Standardwert von `setProfileEnvironment` ist `true`. In einigen Szenarien (z.B. Windows-Betriebssystem) ist `setProfileEnvironment` auf `false` festgelegt. Gehen Sie folgendermaßen vor, wenn Schlüssel nicht wie erwartet im Benutzerprofilverzeichnis gespeichert werden:

  1. Navigieren Sie zum Ordner *%windir%/system32/inetsrv/config*.
  1. Öffnen Sie die Datei *applicationHost.config*.
  1. Suchen Sie das Element `<system.applicationHost><applicationPools><applicationPoolDefaults><processModel>` .
  1. Bestätigen Sie, dass das `setProfileEnvironment`-Attribut nicht vorhanden ist, das standardmäßig den Wert `true` aufweist, oder legen Sie den Wert des Attributs explizit auf `true` fest.

* **Verwenden des Dateisystems als Schlüsselbundspeicher**

  Passen Sie den App-Code so an, dass er [das Dateisystem als Schlüsselbundspeicher verwendet](xref:security/data-protection/configuration/overview). Verwenden Sie ein X.509-Zertifikat, um den Schlüsselbund zu schützen, und stellen Sie sicher, dass es sich bei dem Zertifikat um ein vertrauenswürdiges Zertifikat handelt. Wenn es sich um ein selbstsigniertes Zertifikat handelt, müssen Sie es im vertrauenswürdigen Stammspeicher platzieren.

  Wenn IIS in einer Webfarm verwendet wird:

  * Verwenden Sie eine Dateifreigabe, auf die alle Computer zugreifen können.
  * Stellen Sie ein X509-Zertifikat auf jedem Computer bereit. Konfigurieren Sie den [Schutz von Daten im Code](xref:security/data-protection/configuration/overview).

* **Festlegen einer computerweiten Richtlinie für den Schutz von Daten**

  Das System zum Schutz von Daten verfügt über eine eingeschränkte Unterstützung zum Festlegen einer [computerweiten Standardrichtlinie](xref:security/data-protection/configuration/machine-wide-policy) für alle Apps, die die Datenschutz-APIs nutzen. Weitere Informationen finden Sie unter <xref:security/data-protection/introduction>.

## <a name="virtual-directories"></a>Virtuelle Verzeichnisse

[Virtuelle IIS-Verzeichnisse](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories) werden mit ASP.NET Core-Apps nicht unterstützt. Eine App kann als [untergeordnete Anwendung](#sub-applications) gehostet werden.

## <a name="sub-applications"></a>Untergeordnete Anwendungen

Eine ASP.NET Core-App kann als [untergeordnete IIS-Anwendung (untergeordnete App)](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#applications) gehostet werden. Der Pfad der untergeordneten App wird ein Teil der URL der Stamm-App.

::: moniker range="< aspnetcore-2.2"

Eine untergeordnete App sollte kein ASP.NET Core-Modul als Handler enthalten. Wenn das Modul als Handler in einer *web.config* einer untergeordneten App hinzugefügt wird, erhalten Sie beim Versuch, zur untergeordneten App zu navigieren, den Fehler *500.19: Interner Serverfehler*. Dieser verweist auf die fehlerhafte Konfigurationsdatei.

Das folgende Beispiel zeigt den Inhalt einer veröffentlichten Datei *web.config* für eine untergeordnete ASP.NET Core-App:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

Wenn Sie eine untergeordnete App ohne ASP.NET Core unterhalb einer ASP.NET Core-App hosten, entfernen Sie den geerbten Handler ausdrücklich aus der Datei *web.config* der untergeordneten App:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <system.webServer>
    <handlers>
      <remove name="aspNetCore" />
    </handlers>
    <aspNetCore processPath="dotnet" 
      arguments=".\MyApp.dll" 
      stdoutLogEnabled="false" 
      stdoutLogFile=".\logs\stdout" />
  </system.webServer>
</configuration>
```

::: moniker-end

Statische Assetlinks in der untergeordneten App sollten die Tilde/Schrägstrich-Notation verwenden (`~/`). Die Tilde/Schrägstrich-Notation löst ein [Taghilfsprogramm](xref:mvc/views/tag-helpers/intro) aus, um die Pfadbasis der untergeordneten App dem gerenderten relativen Link voranzustellen. Für eine untergeordnete App unter `/subapp_path` wird ein mit `src="~/image.png"` verknüpftes Bild als `src="/subapp_path/image.png"` gerendert. Die Static File Middleware verarbeitet die Anforderung der statischen Datei nicht. Die Anforderung wird von der Static File Middleware der untergeordneten App verarbeitet.

Wenn das `src`-Attribut eines statischen Objekts auf einen absoluten Pfad festgelegt wird (z.B. `src="/image.png"`), wird der Link ohne die Pfadbasis der untergeordneten App gerendert. Die Middleware für statische Dateien der Stamm-App versucht, das Objekt vom [Webstamm](xref:fundamentals/index#web-root) der Stamm-App aus bereitzustellen, was zu einer *404 Nicht gefunden*-Antwort führt, solange das statische Objekt in der Stamm-App nicht verfügbar ist.

So hosten Sie eine ASP.NET Core-App als untergeordnete App unter einer anderen ASP.NET Core-App:

1. Richten Sie einen App-Pool für die untergeordnete App ein. Legen Sie die **.NET CLR-Version** auf **Kein verwalteter Code** fest.

1. Fügen Sie die Stammwebsite im IIS-Manager mit der untergeordneten App in einem unter der Stammwebsite liegenden Ordner hinzu.

1. Klicken Sie mit der rechten Maustaste im IIS-Manager auf den Ordner der untergeordneten App, und wählen Sie **In Anwendung konvertieren** aus.

1. Weisen Sie im Dialogfeld **Anwendung hinzufügen** mit der Schaltfläche **Auswählen** den **Anwendungspool** dem App-Pool zu, den Sie für die untergeordnete App erstellt haben. Klicken Sie auf **OK**.

Die Zuweisung eines separaten App-Pools zur untergeordneten App ist eine Anforderung, wenn Sie das In-Process-Hostingmodell verwenden.

Weitere Informationen zum In-Process-Hostingmodell und Konfigurieren des ASP.NET Core-Moduls finden Sie unter <xref:host-and-deploy/aspnet-core-module> und <xref:host-and-deploy/aspnet-core-module>.

## <a name="configuration-of-iis-with-webconfig"></a>Konfiguration von IIS mit der Datei „web.config“

Die IIS-Konfiguration wird von dem Abschnitt `<system.webServer>` der Datei *web.config* für IIS-Szenarien beeinflusst, die für ASP.NET Core-Apps mit dem ASP.NET Core-Modul funktional sind. Beispielsweise eignet sich die IIS-Konfiguration für dynamische Komprimierung. Wenn IIS auf Serverebene für die Verwendung der dynamischen Komprimierung konfiguriert ist, kann diese Einstellung mit dem `<urlCompression>`-Element in der Datei *web.config* der App für eine ASP.NET Core-App deaktiviert werden.

Weitere Informationen finden Sie in der [Konfigurationsreferenz für \<system.webServer>](/iis/configuration/system.webServer/), der [Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module) und unter [IIS-Module mit ASP.NET Core](xref:host-and-deploy/iis/modules). Um Umgebungsvariablen für einzelne Apps festzulegen, die in isolierten App-Pools ausgeführt werden (unterstützt für IIS 10.0 oder höher), lesen Sie den Abschnitt zum *Befehl „AppCmd.exe“* im Thema [Umgebungsvariablen \<environmentVariables>](/iis/configuration/system.applicationHost/applicationPools/add/environmentVariables/#appcmdexe) in der IIS-Referenzdokumentation.

## <a name="configuration-sections-of-webconfig"></a>Konfigurationsabschnitte von „web.config“

Konfigurationsabschnitte von ASP.NET 4.x-Apps in der Datei *web.config* werden von ASP.NET Core-Apps nicht zur Konfiguration verwendet:

* `<system.web>`
* `<appSettings>`
* `<connectionStrings>`
* `<location>`

ASP.NET Core-Apps werden mit anderen Konfigurationsanbietern konfiguriert. Weitere Informationen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).

## <a name="application-pools"></a>Anwendungspools

::: moniker range=">= aspnetcore-2.2"

Die App-Poolisolation wird durch das Hostingmodell bestimmt.

* In-Process-Hosting: Apps müssen in separaten App-Pools ausgeführt werden.
* Out-of-Process-Hosting: Es wird empfohlen, die Apps voneinander zu isolieren, indem Sie jede App in ihrem eigenen App-Pool ausführen.

Im IIS-Dialogfeld **Website hinzufügen** wird standardmäßig ein einzelner App-Pool pro App eingesetzt. Wenn ein **Websitename** angegeben ist, wird der Text automatisch in das Textfeld **Anwendungspool** übertragen. Ein neuer App-Pool mit dem Namen der Website wird erstellt, wenn die Website hinzugefügt wird.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Wenn Sie mehrere Websites auf einem Server hosten, wird empfohlen, die Apps voneinander zu isolieren, indem Sie jede App in ihrem eigenen App-Pool ausführen. Im IIS-Dialogfeld **Website hinzufügen** wird standardmäßig diese Konfiguration eingesetzt. Wenn ein **Websitename** angegeben ist, wird der Text automatisch in das Textfeld **Anwendungspool** übertragen. Ein neuer App-Pool mit dem Namen der Website wird erstellt, wenn die Website hinzugefügt wird.

::: moniker-end

## <a name="application-pool-identity"></a>Identität des Anwendungspools

Mit einem Konto für die Identität des App-Pools können Sie eine App unter einem eindeutigen Konto ausführen, ohne Domänen oder lokale Konten erstellen und verwalten zu müssen. Unter IIS 8.0 oder höher erstellt der IIS-Administratorworkerprozess (WAS) ein virtuelles Konto mit dem Namen des neuen App-Pools und führt die Workerprozesse des App-Pools standardmäßig unter diesem Konto aus. Stellen Sie sicher, dass in der IIS-Verwaltungskonsole unter **Erweiterte Einstellungen** für den App-Pool die **Identität** auf **ApplicationPoolIdentity** festgelegt ist:

![Dialogfeld „Erweiterte Einstellungen“ für den Anwendungspool](index/_static/apppool-identity.png)

Der IIS-Verwaltungsprozess erstellt im Windows-Sicherheitssystem einen sicheren Bezeichner mit dem Namen des App-Pools. Ressourcen können mithilfe dieser Identität geschützt werden. Allerdings ist diese Identität kein echtes Benutzerkonto und wird in der Windows-Benutzerverwaltungskonsole nicht angezeigt.

Wenn der IIS-Workerprozess erhöhte Rechte für den Zugriff auf Ihre Anwendung erfordert, ändern Sie die Zugriffssteuerungsliste (ACL) für das Verzeichnis mit der App:

1. Öffnen Sie Windows Explorer, und navigieren Sie zum Verzeichnis.

1. Klicken Sie mit der rechten Maustaste auf das Verzeichnis, und klicken Sie auf **Eigenschaften**.

1. Klicken Sie auf der Registerkarte **Sicherheit** auf die Schaltfläche **Bearbeiten** und dann auf die Schaltfläche **Hinzufügen**.

1. Wählen Sie die Schaltfläche **Speicherorte** aus, und stellen Sie sicher, dass das System ausgewählt ist.

1. Geben Sie im Bereich **Geben Sie die Namen der auszuwählenden Objekte ein** den Wert **IIS AppPool\\<Name_des_AppPools>** ein. Klicken Sie auf die Schaltfläche **Namen überprüfen**. Überprüfen Sie für *DefaultAppPool* die Namen mit **IIS AppPool\DefaultAppPool**. Bei Auswahl der Schaltfläche **Namen überprüfen** wird im Bereich für Objektnamen der Wert **DefaultAppPool** angegeben. Es ist nicht möglich, den Namen des App-Pools direkt in den Bereich für Objektnamen einzugeben. Verwenden Sie das Format **IIS AppPool\\<Name_des_AppPools>**, wenn Sie die Objektnamen überprüfen.

   ![Dialogfeld „Benutzer oder Gruppen auswählen“ für den App-Ordner: Der Name des App-Pools, „DefaultAppPool“, wird an „IIS AppPool\"“ im Bereich der Objektnamen angehängt, bevor „Namen überprüfen“ ausgewählt wird.](index/_static/select-users-or-groups-1.png)

1. Klicken Sie auf **OK**.

   ![Dialogfeld „Benutzer oder Gruppen auswählen“ für den App-Ordner: Nach Auswahl von „Namen überprüfen“ wird der Objektname „DefaultAppPool“ im Bereich der Objektnamen angezeigt.](index/_static/select-users-or-groups-2.png)

1. Standardmäßig sollten Lese- und Schreibberechtigungen gewährt werden. Erteilen Sie weitere Berechtigungen, sofern erforderlich.

Zugriff kann auch über eine Eingabeaufforderung mit dem Tool **ICACLS** gewährt werden. Im folgenden Befehl wird als Beispiel *DefaultAppPool* verwendet:

```console
ICACLS C:\sites\MyWebApp /grant "IIS AppPool\DefaultAppPool":F
```

Weitere Informationen finden Sie im Thema [icacls](/windows-server/administration/windows-commands/icacls).

## <a name="http2-support"></a>HTTP/2-Unterstützung

::: moniker range=">= aspnetcore-2.2"

[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird mit ASP.NET Core in den folgenden IIS-Bereitstellungsszenarien unterstützt:

* In-Process
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * TLS 1.2-Verbindung oder höher
* Out-of-Process
  * Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
  * Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit dem [Kestrel-Server](xref:fundamentals/servers/kestrel) verwendet HTTP/1.1.
  * TLS 1.2-Verbindung oder höher

Für eine In-Process-Bereitstellung, wenn eine HTTP/2-Verbindung hergestellt wurde, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)-Berichte `HTTP/2`. Für eine Out-of-Process-Bereitstellung. wenn eine HTTP/2-Verbindung hergestellt wurde, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)-Berichte `HTTP/1.1`.

Weitere Informationen zu den In-Process- und Out-of-Process-Hostingmodellen finden Sie unter <xref:host-and-deploy/aspnet-core-module>.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

[HTTP/2](https://httpwg.org/specs/rfc7540.html) wird für Out-of-Process-Bereitstellungen unterstützt, die die folgenden Grundanforderungen erfüllen:

* Windows Server 2016/Windows 10 oder höher, IIS 10 oder höher
* Öffentlich zugängliche Edge-Server-Verbindungen verwenden HTTP/2, aber die Reverseproxyverbindung mit dem [Kestrel-Server](xref:fundamentals/servers/kestrel) verwendet HTTP/1.1.
* Zielframework: Nicht zutreffend für Out-of-Process-Bereitstellungen, da die HTTP/2-Verbindung vollständig von IIS verarbeitet wird.
* TLS 1.2-Verbindung oder höher

Wenn eine HTTP/2-Verbindung hergestellt wurde, meldet [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/1.1`.

::: moniker-end

HTTP/2 ist standardmäßig aktiviert. Verbindungen führen ein Fallback auf HTTP/1.1 aus, wenn keine HTTP/2-Verbindung hergestellt wurde. Weitere Informationen zur HTTP/2-Konfiguration mit IIS-Bereitstellungen finden Sie unter [HTTP/2 unter IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis).

## <a name="cors-preflight-requests"></a>CORS-Preflightanforderungen

*Dieser Abschnitt gilt nur für ASP.NET Core-Apps, die auf das .NET Framework ausgerichtet sind.*

Für eine ASP.NET Core-App, die auf das .NET Framework ausgerichtet ist, werden OPTIONS-Anforderungen in IIS standardmäßig nicht an die App übergeben. Informationen dazu, wie Sie die IIS-Handler der App in *web.config* so konfigurieren, dass OPTIONS-Anforderungen übergeben werden, finden Sie unter [Aktivieren ursprungsübergreifender Anforderungen in ASP.NET-Web-API 2: Funktionsweise von CORS](/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api#how-cors-works).

## <a name="deployment-resources-for-iis-administrators"></a>Bereitstellungsressourcen für IIS-Administratoren

Ausführliche Informationen zu IIS erhalten Sie in der IIS-Dokumentation.  
[IIS-Dokumentation](/iis)

Erfahren Sie mehr über .NET Core-App-Bereitstellungsmodelle.  
[.NET Core-Anwendungsbereitstellung](/dotnet/core/deploying/)

Erfahren Sie, wie der Kestrel-Webserver durch das ASP.NET Core-Modul IIS oder IIS Express als Reverseproxyserver zu verwenden.  
[ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module)

Erfahren Sie, wie Sie das ASP.NET Core-Modul so konfigurieren, dass es ASP.NET Core-Apps hosten kann.  
[Konfigurationsreferenz für das ASP.NET Core-Modul](xref:host-and-deploy/aspnet-core-module)

Erfahren Sie mehr über die Verzeichnisstruktur veröffentlichter ASP.NET Core-Apps.  
[Verzeichnisstruktur](xref:host-and-deploy/directory-structure)

Lernen Sie aktive und inaktive IIS-Module für ASP.NET Core-Apps kennen, und erfahren Sie, wie Sie diese verwalten können.  
[IIS-Module](xref:host-and-deploy/iis/troubleshoot)

Erfahren Sie, wie Sie Probleme mit IIS-Bereitstellungen von ASP.NET Core-Apps diagnostizieren können.  
[Problembehandlung](xref:host-and-deploy/iis/troubleshoot)

Erfahren Sie, wie Sie häufige Fehler beim Hosten von ASP.NET Core-Apps in IIS erkennen.  
[Referenz zu häufigen Fehlern bei Azure App Service und IIS](xref:host-and-deploy/azure-iis-errors-reference)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:test/troubleshoot>
* [Einführung in ASP.NET Core](xref:index)
* [Die offizielle Microsoft IIS-Website](https://www.iis.net/)
* [Bibliothek mit technischem Inhalt zu Windows Server](/windows-server/windows-server)
* [HTTP/2 unter IIS](/iis/get-started/whats-new-in-iis-10/http2-on-iis)
* <xref:host-and-deploy/iis/transform-webconfig>
