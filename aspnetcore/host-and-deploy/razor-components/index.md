---
title: Hosten und Bereitstellen von Razor Components
author: guardrex
description: 'Hier erfahren Sie, wie Sie Razor Components- und Blazor-Apps mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.'
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: host-and-deploy/razor-components/index
---
# <a name="host-and-deploy-razor-components"></a>Hosten und Bereitstellen von Razor Components

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

## <a name="publish-the-app"></a>Veröffentlichen der App

Apps werden für die Bereitstellung in Releasekonfigurationen mit dem Befehl [dotnet publish](/dotnet/core/tools/dotnet-publish) veröffentlicht. In einer IDE kann der Befehl `dotnet publish` mithilfe der integrierten Veröffentlichungsfunktionen automatisch ausgeführt werden, sodass der Befehl je nach verwendetem Entwicklungstool möglicherweise nicht manuell über eine Befehlszeile ausgeführt werden muss.

```console
dotnet publish -c Release
```

Mit `dotnet publish` wird eine [Wiederherstellung](/dotnet/core/tools/dotnet-restore) der Abhängigkeiten des Projekts ausgelöst und das Projekt [erstellt](/dotnet/core/tools/dotnet-build), bevor die Objekte für die Bereitstellung erstellt werden. Im Rahmen des Buildprozesses werden nicht verwendete Methoden und Assemblys entfernt, um die Downloadgröße und Ladezeiten von Apps zu reduzieren. Die Bereitstellung wird im Ordner */bin/Release/&lt;target-framework&gt;/publish* erstellt.

Die Objekte im Ordner *publish* werden auf dem Webserver bereitgestellt. Die Bereitstellung kann je nach verwendetem Entwicklungstool manuell oder automatisch erfolgen.

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

Bei Razor Components-Apps, für die das [serverseitige Hostingmodell](xref:razor-components/hosting-models#server-side-hosting-model) verwendet wird, können [Webhostkonfigurationswerte](xref:fundamentals/host/web-host#host-configuration-values) verwendet werden.

Bei Blazor-Apps, für die das [clientseitige Hostingmodell](xref:razor-components/hosting-models#client-side-hosting-model) verwendet wird, können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Laufzeit in der Entwicklungsumgebung verwendet werden.

### <a name="content-root"></a>Inhaltsstammverzeichnis

Mit dem Argument `--contentroot` wird der absolute Pfad zu dem Verzeichnis festgelegt, das die Inhaltsdateien der App enthält.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```console
  dotnet run --contentroot=/<content-root>
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird beim Ausführen der App mit Visual Studio Debugger sowie beim Ausführen der App über eine Befehlszeile mit `dotnet run` abgerufen.

  ```json
  "commandLineArgs": "--contentroot=/<content-root>"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --contentroot=/<content-root>
  ```

### <a name="path-base"></a>Basispfad

Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem virtuellen Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt). Weitere Informationen finden Sie im Abschnitt [Basispfad einer App](#app-base-path).

> [!IMPORTANT]
> Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt. Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/" />` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```console
  dotnet run --pathbase=/<virtual-path>
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird beim Ausführen der App mit Visual Studio Debugger sowie beim Ausführen der App über eine Befehlszeile mit `dotnet run` abgerufen.

  ```json
  "commandLineArgs": "--pathbase=/<virtual-path>"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --pathbase=/<virtual-path>
  ```

### <a name="urls"></a>URLs

Mit dem Argument `--urls` werden die IP-Adressen und Hostadressen mit Ports und Protokollen angegeben, denen für Anforderungen gelauscht werden soll.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird beim Ausführen der App mit Visual Studio Debugger sowie beim Ausführen der App über eine Befehlszeile mit `dotnet run` abgerufen.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deploy-a-client-side-blazor-app"></a>Bereitstellen einer clientseitigen Blazor-App

Mit dem [clientseitigen Hostingmodell](xref:razor-components/hosting-models#client-side-hosting-model) ist Folgendes möglich:

* Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen.
* Die App wird direkt im UI-Thread des Browsers ausgeführt. Dabei wird eine der folgenden beiden Strategien unterstützt:
  * Die Blazor-App wird von einer ASP.NET Core-App unterstützt. Eine Beschreibung hierzu finden Sie im Abschnitt [Clientseitige, von Blazor gehostete Bereitstellung mit ASP.NET Core](#client-side-blazor-hosted-deployment-with-aspnet-core).
  * Die Blazor-App wird auf einem statischen Hosting-Webserver oder -Webservice abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird. Eine Beschreibung hierzu finden Sie im Abschnitt [Clientseitige, eigenständige Blazor-Bereitstellung](#client-side-blazor-standalone-deployment).
  
### <a name="configure-the-linker"></a>Konfigurieren des Linkers

Blazor führt auf jedem Build eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte IL aus den Ausgabeassemblys zu entfernen. Die Assemblyverknüpfung auf Builds kann gesteuert werden. Weitere Informationen finden Sie unter <xref:host-and-deploy/razor-components/configure-linker>.

### <a name="rewrite-urls-for-correct-routing"></a>Erneutes Generieren von URLs für ein ordnungsgemäßes Routing

Routinganforderungen für Seitenkomponenten in einer clientseitigen App sind etwas komplexer als Routinganforderungen an eine serverseitige, gehostete App. Betrachten wir eine clientseitige App mit zwei Seiten:

* **_Main.cshtml:_** Lädt den Anwendungsstamm und enthält einen Link zur Infoseite (`href="About"`).
* **_About.cshtml:_** Infoseite.

Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:

1. Der Browser sendet eine Anforderung.
1. Die Standardseite wird zurückgegeben, in der Regel *index.html*.
1. *index.html* startet die App.
1. Der Router von Blazor wird geladen, und die Hauptseite von Razor (*Main.cshtml*) wird angezeigt.

Wenn auf der Hauptseite der Link zur Infoseite ausgewählt wird, wird die Infoseite geladen. Der Link zur Infoseite kann auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die Infoseite selbst bereitstellt. Alle Anforderungen von internen Seiten *innerhalb der clientseitigen App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst. Der Router verarbeitet Anforderungen intern.

Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf. Diese Ressource ist im Internethost der Anwendung nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.

Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite *index.html* erneut generieren. Wenn *index.html* zurückgegeben wird, übernimmt der clientseitige Router der App und antwortet mit der richtigen Ressource.

### <a name="app-base-path"></a>Basispfad einer App

Beim Basispfad einer App handelt es sich um den virtuellen Stammpfad der App auf dem Server. Eine App, die sich beispielsweise auf dem Contoso-Server in einem virtuellen Ordner unter `/CoolApp/` befindet, wird unter `https://www.contoso.com/CoolApp` erreicht, wobei der virtuelle Basispfad `/CoolApp/` lautet. Indem der Basispfad der App auf `CoolApp/` festgelegt wird, erkennt die App, wo sie sich virtuell auf dem Server befindet. Die App kann den Basispfad der App verwenden, um URLs relativ zum Stammverzeichnis der App über eine Komponente zu erstellen, die sich nicht im Stammverzeichnis befindet. Dadurch ist es möglich, dass Komponenten, die sich in unterschiedlichen Ebenen der Verzeichnisstruktur befinden, Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen können. Ferner wird der Basispfad der App verwendet, um Klicks auf Links abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet – um die interne Navigation kümmert sich der Blazor-Router.

Bei vielen Hostingszenarios ist der virtuelle Pfad des Servers zur App das Stammverzeichnis der App. In diesen Fällen ist der Basispfad der App ein Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für eine App. Bei anderen Hostingszenarios wie etwa bei GitHub-Seiten und virtuellen IIS-Verzeichnissen oder untergeordneten Anwendungen muss der Basispfad der App auf den virtuellen Pfad des Servers zur App festgelegt werden. Zum Festlegen des Basispfads der App fügen Sie das `<base>`-Tag in *index.html* innerhalb der `<head>`-Tagelemente hinzu bzw. aktualisieren es. Legen Sie den `href`-Attributwert auf `<virtual-path>/` (der nachgestellte Schrägstrich ist erforderlich) fest, wobei `<virtual-path>/` der vollständige virtuelle Stammpfad der App auf dem Server ist. Im vorherigen Beispiel wurde der virtuelle Pfad auf `CoolApp/` festgelegt: `<base href="CoolApp/" />`.

Bei einer App, für die ein virtueller Pfad konfiguriert wurde, der sich nicht im Stammverzeichnis befindet (z. B. `<base href="CoolApp/" />`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*. Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.

Damit das Pfadbasis-Argument bei der lokalen Ausführung der App mit dem Stammpfad (`/`) übergeben wird, führen Sie den folgenden Befehl über das Verzeichnis der App aus:

```console
dotnet run --pathbase=/CoolApp
```

Die App antwortet lokal unter `http://localhost:port/CoolApp`.

Weitere Informationen finden Sie im Abschnitt [Pfadbasis](#path-base).

> [!IMPORTANT]
> Wenn für eine App das [clientseitige Hostingmodell](xref:razor-components/hosting-models#client-side-hosting-model) (basierend auf der **Blazor**-Projektvorlage) verwendet wird und die App als untergeordnete IIS-Anwendung in einer ASP.NET Core-App gehostet wird, muss der geerbte ASP.NET Core Module-Handler deaktiviert werden, oder es muss sichergestellt werden, dass der `<handlers>`-Abschnitt der Stammanwendung (also der übergeordneten Anwendung) in der Datei *web.config* von der untergeordneten Anwendung nicht geerbt wird.
>
> Entfernen Sie den Handler in der veröffentlichen *web.config*-Datei der App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:
>
> ```xml
> <handlers>
>   <remove name="aspNetCore" />
> </handlers>
> ```
>
> Alternativ können Sie auch die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung) deaktivieren, indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:
>
> ```xml
> <?xml version="1.0" encoding="utf-8"?>
> <configuration>
>  <location path="." inheritInChildApplications="false">
>    <system.webServer>
>      <handlers>
>        <add name="aspNetCore" ... />
>      </handlers>
>      <aspNetCore ... />
>    </system.webServer>
>  </location>
> </configuration>
> ```
>
> Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zu der in diesem Abschnitt beschriebenen Konfiguration des Basispfads der App durchgeführt. Legen Sie den Basispfad der App in der *index.html*-Datei der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.

### <a name="client-side-blazor-hosted-deployment-with-aspnet-core"></a>Clientseitige, von Blazor gehostete Bereitstellung mit ASP.NET Core

Mit einer *gehosteten Bereitstellung* wird die clientseitige Blazor-App über eine auf einem Server ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.

Die Blazor-App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden. Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann. Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor-Projektvorlage (in ASP.NET Core gehostet)** (`blazorhosted`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).

Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.

Informationen zum Bereitstellen für Azure App Service finden Sie in den folgenden Themen:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.

### <a name="client-side-blazor-standalone-deployment"></a>Clientseitige, eigenständige Blazor-Bereitstellung

Mit einer *eigenständigen Bereitstellung* wird die clientseitige Blazor-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden. Für die Bereitstellung der Blazor-App wird kein Webserver verwendet.

#### <a name="client-side-blazor-standalone-hosting-with-iis"></a>Clientseitige, eigenständige Blazor-Bereitstellung mit IIS

IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps. Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS (Erstellen einer statischen Website unter IIS)](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Veröffentlichte Objekte werden im Ordner *\\bin\\Release\\&lt;target-framework&gt;\\publish* erstellt. Die Inhalte des Ordners *publish* werden auf dem Webserver oder über den Hostingdienst gehostet.

**web.config**

Beim Veröffentlichen eines Blazor-Projekts wird eine *web.config*-Datei mit der folgenden IIS-Konfiguration erstellt:

* Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:
  * *\*DLL*: `application/octet-stream`
  * *\*JSON*: `application/json`
  * *\*WASM*: `application/wasm`
  * *\*WOFF*: `application/font-woff`
  * *\*WOFF2*: `application/font-woff`
* Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:
  * `application/octet-stream`
  * `application/wasm`
* URL-Rewrite-Modul-Regeln werden eingerichtet:
  * Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden (*&lt;assembly_name&gt;\\dist\\&lt;path_requested&gt;*).
  * Erstellen Sie SPA-Fallbackrouting, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte (*&lt;assembly_name&gt;\\dist\\index.html*) umgeleitet werden.

**Installieren des URL-Rewrite-Moduls**

Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt. Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar. Das Modul muss von der IIS-Website heruntergeladen werden. Verwenden Sie den Webplattform-Installer zur Installation des Moduls:

1. Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus. Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.
1. Kopieren Sie den Installer auf den Server. Führen Sie den Installer aus. Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu. Der Server muss nach Abschluss der Installation nicht neu gestartet werden.

**Konfigurieren der Website**

Legen Sie für den **physischen Pfad** der Website den Ordner der App fest. Der Ordner enthält Folgendes:

* Die Datei *web.config*, die von IIS zum Konfigurieren der Website verwendet wird, mit den erforderlichen Umleitungsregeln und Dateiinhaltstypen
* Den Ordner der App für statische Objekte

**Problembehandlung**

Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist. Wenn das Modul nicht installiert ist, kann die Datei *web.config* von IIS nicht analysiert werden. Dadurch wird verhindert, dass die Konfiguration der Website von IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.

Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.

#### <a name="client-side-blazor-standalone-hosting-with-nginx"></a>Clientseitige, eigenständige Blazor-Bereitstellung mit Nginx

Die folgende *nginx.conf*-Datei wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei *Index.html* gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /Index.html =404;
        }
    }
}
```

Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

#### <a name="client-side-blazor-standalone-hosting-with-nginx-in-docker"></a>Clientseitige, eigenständige Blazor-Bereitstellung mit Docker

Richten Sie zum Hosten von Blazor in Docker mithilfe von Nginx die Dockerfile für die Verwendung des auf Alpine basierenden Nginx-Images ein. Aktualisieren Sie die Dockerfile zum Kopieren der Datei *nginx.config* in den Container.

Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

#### <a name="client-side-blazor-standalone-hosting-with-github-pages"></a>Clientseitige, eigenständige Blazor-Bereitstellung mit GitHub Pages

Fügen Sie zum Verarbeiten von URL-Umschreibungen eine *404.html*-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite *index.html* verarbeitet wird. Ein Beispiel für eine Implementierung, das von der Community bereitgestellt wurde, finden Sie unter [Single Page Apps for GitHub Pages (Single-Page-Apps für GitHub Pages)](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages auf GitHub](https://github.com/rafrex/spa-github-pages#readme)). Ein Beispiel für die Verwendung des Community-Ansatzes finden Sie unter [blazor-demo/blazor-demo.github.io auf GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([Livewebsite](https://blazor-demo.github.io/)).

Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, fügen Sie der Datei *index.html* das `<base>`-Tag hinzu, oder aktualisieren Sie das Tag in der Datei. Legen Sie den Wert des Attributs `href` auf `<repository-name>/` fest, wobei `<repository-name>/` der Name des GitHub-Repositorys ist.

## <a name="deploy-a-server-side-razor-components-app"></a>Bereitstellen einer serverseitigen Razor Components-App

Mit dem [serverseitigen Hostingmodell](xref:razor-components/hosting-models#server-side-hosting-model) wird Razor Components über eine ASP.NET Core-App auf dem Server ausgeführt. Benutzeroberflächenupdates, Ereignisbehandlung und JavaScript-Aufrufe werden über eine SignalR-Verbindung verarbeitet.

Die App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden. Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann. Bei einer serverseitigen Bereitstellung enthält Visual Studio die **Blazor-Projektvorlage (serverseitig in ASP.NET Core)** (`blazorserver`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).

<!--

**INSERT: Concerns are the same as publishing an ASP.NET Core SignalR app**

**INSERT: Content on the Azure SignalR Service**

**INSERT: Manually turn on WebSockets support**

-->

Wenn die ASP.NET Core-App veröffentlicht wird, ist die Razor Components-App in der veröffentlichten Ausgabe enthalten, sodass die ASP.NET Core-App und die Razor Components-App zusammen bereitgestellt werden können. Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.

Informationen zum Bereitstellen für Azure App Service finden Sie in den folgenden Themen:

<xref:tutorials/publish-to-azure-webapp-using-vs>  
Erfahren Sie, wie eine ASP.NET Core-App in Azure App Service mit Visual Studio veröffentlicht wird.
