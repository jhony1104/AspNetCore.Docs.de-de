---
title: Hosten und Bereitstellen von clientseitigem Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine Blazor-App mithilfe von ASP.NET Core, Content Delivery Network (CDN), Dateiservern und GitHub-Seiten hosten und bereitstellen.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/21/2019
uid: host-and-deploy/blazor/client-side
ms.openlocfilehash: b50516b4dce28a6b105b2ab8b9386060d5392983
ms.sourcegitcommit: 4d05e30567279072f1b070618afe58ae1bcefd5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376390"
---
# <a name="host-and-deploy-blazor-client-side"></a>Hosten und Bereitstellen von clientseitigem Blazor

Von [Luke Latham](https://github.com/guardrex), [Rainer Stropek](https://www.timecockpit.com) und [Daniel Roth](https://github.com/danroth27)

## <a name="host-configuration-values"></a>Hostkonfigurationswerte

Bei Blazor-Apps, für die das [clientseitige Hostingmodell](xref:blazor/hosting-models#client-side) verwendet wird, können die folgenden Hostkonfigurationswerte als Befehlszeilenargumente zur Laufzeit in der Entwicklungsumgebung verwendet werden.

### <a name="content-root"></a>Inhaltsstammverzeichnis

Mit dem Argument `--contentroot` wird der absolute Pfad zu dem Verzeichnis festgelegt, das die Inhaltsdateien der App enthält. In den folgenden Beispielen ist `/content-root-path` der Stammpfad für Inhalte der App.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```console
  dotnet run --contentroot=/content-root-path
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--contentroot=/content-root-path"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --contentroot=/content-root-path
  ```

### <a name="path-base"></a>Basispfad

Mit dem Argument `--pathbase` wird der Basispfad für eine App festgelegt, die lokal mit einem virtuellen Pfad ausgeführt wird, der sich nicht im Stammverzeichnis befindet (d. h. das `<base>`-Tag `href` wird für das Staging und die Produktion auf einen anderen Pfad als `/` festgelegt). In den folgenden Beispielen ist `/virtual-path` die Pfadbasis der App. Weitere Informationen finden Sie im Abschnitt [Basispfad einer App](#app-base-path).

> [!IMPORTANT]
> Im Gegensatz zu dem Pfad, der für `href` des `<base>`-Tags bereitgestellt wird, wird beim Übergeben des `--pathbase`-Argumentwerts kein Schrägstrich (`/`) nachgestellt. Wenn der Basispfad einer App im `<base>`-Tag als `<base href="/CoolApp/">` (mit nachgestelltem Schrägstrich) bereitgestellt wird, wird der Argumentwert in der Befehlszeile als `--pathbase=/CoolApp` (ohne nachgestellten Schrägstrich) übergeben.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```console
  dotnet run --pathbase=/virtual-path
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--pathbase=/virtual-path"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --pathbase=/virtual-path
  ```

### <a name="urls"></a>URLs

Mit dem Argument `--urls` werden die IP-oder Hostadressen mit Ports und Protokollen festgelegt, die auf Anforderungen lauschen sollen.

* Das Argument wird beim lokalen Ausführen der App bei einer Eingabeaufforderung übergeben. Führen Sie den folgenden Befehl über das Verzeichnis der App aus:

  ```console
  dotnet run --urls=http://127.0.0.1:0
  ```

* Fügen Sie der Datei *launchSettings.json* der App im **IIS Express**-Profil einen Eintrag hinzu. Diese Einstellung wird verwendet, wenn die Anwendung mit dem Visual Studio-Debugger und an einer Eingabeaufforderung mit `dotnet run` ausgeführt wird.

  ```json
  "commandLineArgs": "--urls=http://127.0.0.1:0"
  ```

* Geben Sie in Visual Studio das Argument in **Eigenschaften** > **Debuggen** > **Anwendungsargumente** ein. Wenn Sie das Argument auf der Visual Studio-Eigenschaftenseite festlegen, wird das Argument der Datei *launchSettings.json* hinzugefügt.

  ```console
  --urls=http://127.0.0.1:0
  ```

## <a name="deployment"></a>Bereitstellung

Mit dem [clientseitigen Hostingmodell](xref:blazor/hosting-models#client-side) ist Folgendes möglich:

* Die Blazor-App, die jeweiligen Abhängigkeiten und die .NET-Runtime werden im Browser herunterladen.
* Die App wird direkt im UI-Thread des Browsers ausgeführt. Dabei wird eine der folgenden beiden Strategien unterstützt:
  * Die Blazor-App wird von einer ASP.NET Core-App unterstützt. Diese Strategie wird im Abschnitt [Gehostete Bereitstellung mit ASP.NET Core](#hosted-deployment-with-aspnet-core) behandelt.
  * Die Blazor-App wird auf einem statischen Hosting-Webserver oder -Webservice abgelegt, auf dem .NET nicht zur Unterstützung der Blazor-App verwendet wird. Diese Strategie wird im Abschnitt [Eigenständige Bereitstellung](#standalone-deployment) behandelt.

## <a name="configure-the-linker"></a>Konfigurieren des Linkers

Blazor führt auf jedem Build eine IL-Verknüpfung (Intermediate Language, Zwischensprache) durch, um nicht benötigte IL aus den Ausgabeassemblys zu entfernen. Verknüpfungen mit Assemblys können beim Erstellen des Builds gesteuert werden. Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/configure-linker>.

## <a name="rewrite-urls-for-correct-routing"></a>Erneutes Generieren von URLs für ein ordnungsgemäßes Routing

Routinganforderungen für Seitenkomponenten in einer clientseitigen App sind etwas komplexer als Routinganforderungen an eine serverseitige, gehostete App. Betrachten wir eine clientseitige App mit zwei Seiten:

* **_Main.razor**: Wird im Stammverzeichnis der App geladen und enthält einen Link zur Infoseite (`href="About"`).
* **_About.razor**: Infoseite.

Wenn das Standarddokument der App über die Adressleiste des Browsers (z. B. `https://www.contoso.com/`) angefordert wird, geschieht Folgendes:

1. Der Browser sendet eine Anforderung.
1. Die Standardseite wird zurückgegeben, in der Regel *index.html*.
1. *index.html* startet die App.
1. Der Router von Blazor wird geladen, und die Razor-Hauptseite (*Main.razor*) wird angezeigt.

Wenn auf der Hauptseite der Link zur Infoseite ausgewählt wird, wird die Infoseite geladen. Der Link zur Infoseite kann auf dem Client ausgewählt werden, da der Blazor-Router dafür sorgt, dass der Browser im Internet keine Anforderung für `About` an `www.contoso.com` sendet, und stattdessen die Infoseite selbst bereitstellt. Alle Anforderungen von internen Seiten *innerhalb der clientseitigen App* funktionieren auf dieselbe Weise: Durch Anforderungen werden keine browserbasierten Anforderungen an serverseitig gehostete Ressourcen im Internet ausgelöst. Der Router verarbeitet Anforderungen intern.

Wenn eine Anforderung für `www.contoso.com/About` über die Adressleiste des Browsers gesendet wird, tritt bei der Anforderung ein Fehler auf. Diese Ressource ist im Internethost der App nicht vorhanden. Daher wird die Antwort *404 Nicht gefunden* zurückgegeben.

Da Browser Anforderungen für clientseitige Seiten an internetbasierte Hosts senden, müssen Webserver und Hostingdienste alle Anforderungen für Ressourcen, die sich nicht physisch auf dem Server befinden, auf der Seite *index.html* erneut generieren. Wenn *index.html* zurückgegeben wird, übernimmt der clientseitige Router der App und antwortet mit der richtigen Ressource.

## <a name="app-base-path"></a>Basispfad einer App

Beim *Basispfad einer App* handelt es sich um den virtuellen Stammpfad der App auf dem Server. Eine App, die sich beispielsweise auf dem Contoso-Server in einem virtuellen Ordner unter `/CoolApp/` befindet, wird unter `https://www.contoso.com/CoolApp` erreicht, wobei der virtuelle Basispfad `/CoolApp/` lautet. Indem Sie den Basispfad der App auf den virtuellen Pfad (`<base href="/CoolApp/">`) festlegen, wird der App gezeigt, wo sie sich virtuell auf dem Server befindet. Die App kann den Basispfad der App verwenden, um URLs relativ zum Stammverzeichnis der App über eine Komponente zu erstellen, die sich nicht im Stammverzeichnis befindet. Dadurch ist es möglich, dass Komponenten, die sich in unterschiedlichen Ebenen der Verzeichnisstruktur befinden, Links zu anderen Ressourcen an Speicherorten in der gesamten App erstellen können. Ferner wird der Basispfad der App verwendet, um Klicks auf Links abzufangen, bei denen sich das `href`-Ziel des Links innerhalb des URI-Raums für den Basispfad der App befindet – um die interne Navigation kümmert sich der Blazor-Router.

Bei vielen Hostingszenarios ist der virtuelle Pfad des Servers zur App das Stammverzeichnis der App. In diesen Fällen ist der Basispfad der App ein Schrägstrich (`<base href="/" />`). Hierbei handelt es sich um die Standardkonfiguration für eine App. Bei anderen Hostingszenarios wie etwa bei GitHub-Seiten und virtuellen IIS-Verzeichnissen oder untergeordneten Anwendungen muss der Basispfad der App auf den virtuellen Pfad des Servers zur App festgelegt werden. Aktualisieren Sie das Tag `<base>` in den `<head>`-Tagelementen der Datei *wwwroot/index.html*, um den Basispfad der App festzulegen. Legen Sie den `href`-Attributwert auf `/virtual-path/` (der nachgestellte Schrägstrich ist erforderlich) fest, wobei `/virtual-path/` der vollständige virtuelle Stammpfad der App auf dem Server ist. Im vorherigen Beispiel wurde der virtuelle Pfad auf `/CoolApp/` festgelegt: `<base href="/CoolApp/">`.

Bei einer App, für die ein virtueller Pfad konfiguriert wurde, der sich nicht im Stammverzeichnis befindet (z. B. `<base href="/CoolApp/">`), werden die Ressourcen der App nicht gefunden, *wenn die App lokal ausgeführt wird*. Um dieses Problem bei der lokalen Entwicklung und bei Tests zu beheben, können Sie ein *Pfadbasis*-Argument bereitstellen, das dem `href`-Wert des `<base>`-Tags zur Laufzeit entspricht.

Führen Sie mithilfe der Option `--pathbase` den Befehl `dotnet run` über das Verzeichnis der App aus, um das Basispfadargument mit dem Stammverzeichnis (`/`) zu übergeben, wenn Sie die App lokal ausführen:

```console
dotnet run --pathbase=/{Virtual Path (no trailing slash)}
```

Für eine App mit dem virtuellen Basispfad `/CoolApp/` (`<base href="/CoolApp/">`) lautet der Befehl wie folgt:

```console
dotnet run --pathbase=/CoolApp
```

Die App antwortet lokal unter `http://localhost:port/CoolApp`.

Weitere Informationen finden Sie im Abschnitt zum [Hostkonfigurationswert für die Pfadbasis](#path-base).

Wenn eine App das [clientseitige Hostingmodell](xref:blazor/hosting-models#client-side) verwendet (basierend auf der **Blazor**-Projektvorlage; die `blazor`-Vorlage bei Verwendung des [dotnet.new](/dotnet/core/tools/dotnet-new)-Befehls) und als untergeordnete IIS-Anwendung in einer ASP.NET Core-App gehostet wird, muss der geerbte ASP.NET Core-Modulhandler deaktiviert werden, um sicherzustellen, dass der `<handlers>`-Abschnitt der Stammanwendung (bzw. der übergeordneten App) in der *web.config*-Datei von der untergeordneten App nicht geerbt wird.

Entfernen Sie den Handler in der veröffentlichen *web.config*-Datei der App, indem Sie der Datei einen `<handlers>`-Abschnitt hinzufügen:

```xml
<handlers>
  <remove name="aspNetCore" />
</handlers>
```

Alternativ können Sie auch die Vererbung des `<system.webServer>`-Abschnitts der Stammanwendung (d. h. der übergeordneten Anwendung) deaktivieren, indem Sie ein `<location>`-Element verwenden, bei dem `inheritInChildApplications` auf `false` festgelegt ist:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <location path="." inheritInChildApplications="false">
    <system.webServer>
      <handlers>
        <add name="aspNetCore" ... />
      </handlers>
      <aspNetCore ... />
    </system.webServer>
  </location>
</configuration>
```

Das Entfernen des Handlers bzw. das Deaktivieren der Vererbung wird zusätzlich zu der in diesem Abschnitt beschriebenen Konfiguration des Basispfads der App durchgeführt. Legen Sie den Basispfad der App in der *index.html*-Datei der App auf den IIS-Alias fest, der beim Konfigurieren der untergeordneten App in IIS verwendet wird.

## <a name="hosted-deployment-with-aspnet-core"></a>Gehostete Bereitstellung mit ASP.NET Core

Mit einer *gehosteten Bereitstellung* wird die clientseitige Blazor-App über eine auf einem Server ausgeführte [ASP.NET Core-App](xref:index) für Browser bereitgestellt.

Die Blazor-App ist mit der ASP.NET Core-App in der veröffentlichen Ausgabe enthalten, sodass die beiden Apps zusammen bereitgestellt werden. Hierfür wird ein Webserver benötigt, auf dem eine ASP.NET Core-App gehostet werden kann. Bei einer gehosteten Bereitstellung enthält Visual Studio die **Blazor-Projektvorlage (in ASP.NET Core gehostet)** (`blazorhosted`-Vorlage bei Verwendung des Befehls [dotnet new](/dotnet/core/tools/dotnet-new)).

Weitere Informationen zum Hosten und Bereitstellen von ASP.NET Core-Apps finden Sie unter <xref:host-and-deploy/index>.

Informationen zum Bereitstellen für Azure App Service finden Sie unter <xref:tutorials/publish-to-azure-webapp-using-vs>.

## <a name="standalone-deployment"></a>Eigenständige Bereitstellung

Mit einer *eigenständigen Bereitstellung* wird die clientseitige Blazor-App in Form von statischen Dateien bereitgestellt, die von Clients direkt angefordert werden. Jeder statische Dateiserver kann die Blazor-App bedienen.

Eigenständige Bereitstellungsobjekte werden im Ordner */bin/Release/{ZIELFRAMEWORK}/publish/{ASSEMBLYNAME}/dist* veröffentlicht.

### <a name="iis"></a>IIS

IIS ist ein leistungsfähiger Server für statische Dateien für Blazor-Apps. Informationen zum Konfigurieren von IIS zum Hosten von Blazor finden Sie unter [Build a Static Website on IIS (Erstellen einer statischen Website unter IIS)](/iis/manage/creating-websites/scenario-build-a-static-website-on-iis).

Veröffentlichte Objekte werden im Ordner */bin/Release/{Zielframework}/publish* erstellt. Die Inhalte des Ordners *publish* werden auf dem Webserver oder über den Hostingdienst gehostet.

#### <a name="webconfig"></a>web.config

Beim Veröffentlichen eines Blazor-Projekts wird eine *web.config*-Datei mit der folgenden IIS-Konfiguration erstellt:

* Für die folgenden Dateierweiterungen werden MIME-Typen festgelegt:
  * *.dll* &ndash; `application/octet-stream`
  * *.json* &ndash; `application/json`
  * *.wasm* &ndash; `application/wasm`
  * *.woff* &ndash; `application/font-woff`
  * *.woff2* &ndash; `application/font-woff`
* Für die folgenden MIME-Typen wird die HTTP-Komprimierung aktiviert:
  * `application/octet-stream`
  * `application/wasm`
* URL-Rewrite-Modul-Regeln werden eingerichtet:
  * Stellen Sie das Unterverzeichnis bereit, in dem sich die statischen Objekte der App befinden ( *{ASSEMBLYNAME}/dist/{ANGEFORDERTER PFAD}* ).
  * Richten Sie ein SPA-Fallbackrouting ein, sodass Anforderungen für nicht dateibasierte Objekte an das Standarddokument der App im entsprechenden Ordner für statische Objekte ( *{ASSEMBLYNAME}/dist/index.html*) umgeleitet werden.

#### <a name="install-the-url-rewrite-module"></a>Installieren des URL-Rewrite-Moduls

Das [URL-Rewrite-Modul](https://www.iis.net/downloads/microsoft/url-rewrite) wird zum Umschreiben von URLs benötigt. Das Modul wird nicht standardmäßig installiert und ist für die Installation als Webserver (IIS)-Rollendienstfunktion nicht verfügbar. Das Modul muss von der IIS-Website heruntergeladen werden. Verwenden Sie den Webplattform-Installer zur Installation des Moduls:

1. Navigieren Sie lokal zur [URL-Rewrite-Module-Downloadseite](https://www.iis.net/downloads/microsoft/url-rewrite#additionalDownloads). Wählen Sie zum Herunterladen der englischen Version des WebPI-Installers **WebPI** aus. Wählen Sie zum Herunterladen des Installers in einer anderen Sprache die entsprechende Architektur für den Server (x86/x64) aus.
1. Kopieren Sie den Installer auf den Server. Führen Sie den Installer aus. Klicken Sie auf die Schaltfläche **Installieren**, und stimmen Sie den Lizenzbedingungen zu. Der Server muss nach Abschluss der Installation nicht neu gestartet werden.

#### <a name="configure-the-website"></a>Konfigurieren der Website

Legen Sie für den **physischen Pfad** der Website den Ordner der App fest. Der Ordner enthält Folgendes:

* Die Datei *web.config*, die von IIS zum Konfigurieren der Website verwendet wird, mit den erforderlichen Umleitungsregeln und Dateiinhaltstypen
* Den Ordner der App für statische Objekte

#### <a name="troubleshooting"></a>Problembehandlung

Wenn die Fehlermeldung *500: Interner Serverfehler* angezeigt wird und IIS-Manager beim Zugriff auf die Konfiguration der Website eine Fehlermeldung anzeigt, überprüfen Sie, ob das URL-Rewrite-Modul installiert ist. Wenn das Modul nicht installiert ist, kann die Datei *web.config* von IIS nicht analysiert werden. Dadurch wird verhindert, dass die Konfiguration der Website von IIS-Manager geladen wird, und dass die statischen Blazor-Dateien auf der Website bereitgestellt werden.

Weitere Informationen zur Problembehandlung von Bereitstellungen für IIS finden Sie unter <xref:host-and-deploy/iis/troubleshoot>.

### <a name="azure-storage"></a>Azure Storage

Die Hosting statischer Dateien von Azure Storage ermöglicht das serverlose Blazor-App-Hosting. Benutzerdefinierte Domänennamen, das Azure Content Delivery Network (CDN) und HTTPS werden unterstützt.

Wenn der Blobdienst für das Hosten von statischen Websites in einem Speicherkonto aktiviert ist, gehen Sie folgendermaßen vor:

* Legen Sie den **Namen des Indexdokuments** auf `index.html` fest.
* Legen Sie den **Pfad des Fehlerdokuments** auf `index.html` fest. Razor-Komponenten und andere Endpunkte, bei denen es sich nicht um Dateien handelt, befinden sich nicht in physischen Pfaden in dem statischen Inhalt, der vom Blobdienst gespeichert wird. Wenn eine Anforderung für eine dieser Ressourcen empfangen wird, die vom Blazor-Router verarbeitet werden soll, leitet der vom Blobdienst generierte Fehler *404 – Nicht gefunden* die Anforderung an den **Pfad des Fehlerdokuments** weiter. Das Blob *index.html* wird zurückgegeben, und der Blazor-Router lädt und verarbeitet den Pfad.

Weitere Informationen finden Sie unter [Hosten von statischen Websites in Azure Storage](/azure/storage/blobs/storage-blob-static-website).

### <a name="nginx"></a>Nginx

Die folgende *nginx.conf*-Datei wurde vereinfacht, um zu zeigen, wie Nginx so konfiguriert wird, dass die Datei *Index.html* gesendet wird, wenn auf der Festplatte keine entsprechende Datei gefunden wird.

```
events { }
http {
    server {
        listen 80;

        location / {
            root /usr/share/nginx/html;
            try_files $uri $uri/ /index.html =404;
        }
    }
}
```

Weitere Informationen zur Nginx-Webserverkonfiguration für die Produktion finden Sie unter [Creating NGINX Plus and NGINX Configuration Files (Erstellen von Konfigurationsdateien für NGINX Plus und NGINX)](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/).

### <a name="nginx-in-docker"></a>Nginx in Docker

Richten Sie zum Hosten von Blazor in Docker mithilfe von Nginx die Dockerfile für die Verwendung des auf Alpine basierenden Nginx-Images ein. Aktualisieren Sie die Dockerfile zum Kopieren der Datei *nginx.config* in den Container.

Fügen Sie der Dockerfile eine Zeile hinzu, wie im folgenden Beispiel dargestellt:

```Dockerfile
FROM nginx:alpine
COPY ./bin/Release/netstandard2.0/publish /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/nginx.conf
```

### <a name="github-pages"></a>GitHub-Seiten

Fügen Sie zum Verarbeiten von URL-Umschreibungen eine *404.html*-Datei mit einem Skript hinzu, mit dem die Umleitung der Anforderung an die Seite *index.html* verarbeitet wird. Ein Beispiel für eine Implementierung, das von der Community bereitgestellt wurde, finden Sie unter [Single Page Apps for GitHub Pages (Single-Page-Apps für GitHub Pages)](http://spa-github-pages.rafrex.com/) ([rafrex/spa-github-pages auf GitHub](https://github.com/rafrex/spa-github-pages#readme)). Ein Beispiel für die Verwendung des Community-Ansatzes finden Sie unter [blazor-demo/blazor-demo.github.io auf GitHub](https://github.com/blazor-demo/blazor-demo.github.io) ([Livewebsite](https://blazor-demo.github.io/)).

Wenn Sie anstelle einer Organisationswebsite eine Projektwebsite verwenden, fügen Sie der Datei *index.html* das `<base>`-Tag hinzu, oder aktualisieren Sie das Tag in der Datei. Legen Sie den Wert des `href`-Attributs auf den Namen des GitHub-Repositorys mit einem nachfolgenden Slash fest (z.B. `my-repository/`).
