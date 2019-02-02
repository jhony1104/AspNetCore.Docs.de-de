---
uid: web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
title: Konfigurieren eines Webservers für das Web Deploy-Veröffentlichung (Web Deploy-Handler) | Microsoft-Dokumentation
author: jrjlee
description: In diesem Thema wird beschrieben, wie ein Webserver für Internetinformationsdienste (Internet Information Services, IIS) zur Unterstützung von Webpublishing und die Bereitstellung mithilfe der IIS Web bereitstellen Han konfiguriert...
ms.author: riande
ms.date: 01/29/2017
ms.assetid: 90ebf911-1c46-4470-b876-1335bd0f590f
msc.legacyurl: /web-forms/overview/deployment/configuring-server-environments-for-web-deployment/configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler
msc.type: authoredcontent
ms.openlocfilehash: cf18a8860d34daa23f61e3dde13c2c79c6c0d4a5
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667322"
---
# <a name="configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler"></a>Konfigurieren eines Webservers für die Web Deploy-Veröffentlichung (Web Deploy-Handler)

[PDF herunterladen](https://msdnshared.blob.core.windows.net/media/MSDNBlogsFS/prod.evol.blogs.msdn.com/CommunityServer.Blogs.Components.WeblogFiles/00/00/00/63/56/8130.DeployingWebAppsInEnterpriseScenarios.pdf)

> In diesem Thema wird beschrieben, wie ein Webserver für Internetinformationsdienste (Internet Information Services, IIS) zur Unterstützung von Webpublishing und die Bereitstellung mithilfe des IIS-Handlers zum Bereitstellen von Web konfigurieren werden.
> 
> Wenn Sie mit Web Deploy 2.0 oder höher arbeiten, stehen drei wichtigsten Ansätze, die Sie verwenden können, um Ihre Anwendungen oder Websites auf einem Webserver zu erhalten. Sie haben folgende Möglichkeiten:
> 
> - Verwenden der *Web Deploy Remote-Agent-Dienst*. Dieser Ansatz erfordert weniger Konfiguration des Webservers, jedoch müssen Sie die Anmeldeinformationen eines lokalen Server-Administrators angeben, um alles auf dem Server bereitstellen.
> - Verwenden der *Web Deploy-Handler*. Dieser Ansatz ist sehr viel komplizierter und erfordert mehr anfänglichen Aufwand auf dem Webserver einrichten. Wenn Sie diesen Ansatz verwenden, können Sie IIS, damit Benutzer ohne Administratorrechte, zur Durchführung der Bereitstellung konfigurieren. Der Handler für die Web-Bereitstellung ist nur in IIS, Version 7 oder höher verfügbar.
> - Verwendung *offlinebereitstellung*. Dieser Ansatz erfordert nur geringfügige Konfigurationen des Webservers, aber ein Server-Administrator muss manuell kopieren des Pakets auf dem Server und importieren Sie es über IIS-Manager.
> 
> Weitere Informationen zu den Hauptfunktionen, Vorteile und Nachteile dieser Ansätze zu erhalten, finden Sie unter [Entscheidung zur Webbereitstellung rechts](choosing-the-right-approach-to-web-deployment.md).


Ja, wenn Sie nicht-Administratoren zum Bereitstellen von Inhalt auf bestimmte IIS-Websites zulassen möchten. Dieser Ansatz ist häufig wünschenswert, in diesen Szenarien:

- Staging- oder produktionsumgebung Umgebungen, in dem die Person oder ein Dienstkonto, die die Remotebereitstellung auslöst wahrscheinlich haben Zugriff auf die Anmeldeinformationen von einem Serveradministrator ist.
- Gehosteten Umgebung, in denen remote-Benutzer die Möglichkeit, ihre Websites zu aktualisieren, ohne Ihnen vollständige Kontrolle über Ihre Webserver (oder den Zugriff auf andere Websites) erhalten werden sollen.

In Szenarien für Entwicklungs- oder testumgebung oder in kleineren Organisationen, Inhalt bereitstellen, die mithilfe von Server-Administrator-Anmeldeinformationen ist häufig weniger strittigen. Konfigurieren Sie in diesen Szenarien wird Ihre Webserver zur Unterstützung der Bereitstellung mithilfe der [Web Deploy Remote-Agent Service](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md) bietet einen einfachen Ansatz.

## <a name="task-overview"></a>Übersicht über den Task

Zum Konfigurieren des Webserver, um zu übernehmen und Bereitstellen von Webpaketen von einem Remotecomputer mit dem Bereitstellen von Web-Handler-Ansatz müssen Sie Folgendes ausführen:

- Erstellen Sie, oder wählen Sie ein Domänenbenutzerkonto an (die "nicht-Administrator"), dessen Anmeldeinformationen Sie verwenden, um die Bereitstellung durchführen.
- Installieren Sie IIS 7.5, einschließlich der Web-Management-Dienst und das Modul zur Standardauthentifizierung.
- Installieren Sie Web Deploy 2.1 oder höher.
- Konfigurieren Sie die Web-Management-Dienst, um Remoteverbindungen zuzulassen, und starten Sie den Dienst.
- Erstellen einer IIS-Website zum Hosten des bereitgestellten Inhalts.
- Ihre nicht-Administrator-Berechtigungen für Ihre Website im IIS-Manager zu gewähren.
- Stellen Sie sicher, dass die Web-Management-Dienst Delegierungsregeln ermöglichen den Dienst hinzufügen und Ändern von Websiteinhalte, die mit Ihrem Konto Benutzer ohne Administratorrechte.
- Konfigurieren von Firewalls für eingehende Verbindungen an Port 8172.

Um die beispiellösung ContactManager speziell zu hosten, müssen Sie auch auf:

- Installieren von .NET Framework 4.0.
- Installieren Sie ASP.NET MVC 3.

In diesem Thema werden Sie zum Durchführen dieser Verfahren erläutert. Die Aufgaben und exemplarische Vorgehensweisen in diesem Thema wird davon ausgegangen, dass Sie mit einem "sauberen" Server-Build, der unter Windows Server 2016 beginnen. Bevor Sie fortfahren, stellen Sie sicher, dass:

- Windows Server 2016
- Der Server ist die Domäne eingebunden.
- Der Server hat eine statische IP-Adresse.

> [!NOTE]
> Weitere Informationen zum Einbinden von Computern in einer Domäne, finden Sie unter [Hinzufügen von Computern zur Domäne und Anmelden](https://technet.microsoft.com/library/cc725618(v=WS.10).aspx). Weitere Informationen zum Konfigurieren von statischen IP-Adressen finden Sie unter [Konfigurieren einer statischen IP-Adresse](https://technet.microsoft.com/library/cc754203(v=ws.10).aspx).


## <a name="install-products-and-components"></a>Installieren der Produkte und Komponenten

Dieser Abschnitt führt Sie durch die Installation der erforderlichen Produkte und Komponenten auf dem Webserver. Bevor Sie beginnen, hat sich bewährt, zum Ausführen von Windows Update aus, um sicherzustellen, dass der Server vollständig auf dem neuesten Stand ist.

In diesem Fall müssen Sie Folgendes installieren:

- **Empfohlene Konfiguration von IIS 7**. Dies ermöglicht die **Webserver (IIS)** -Rolle auf dem Webserver und installiert den Satz von IIS-Module und Komponenten, die Sie benötigen, um eine ASP.NET-Anwendung zu hosten.
- **IIS: Management-Dienst**. Dadurch wird den Webverwaltungsdienst (WMSvc) in IIS installiert. Dieser Dienst ermöglicht die Remoteverwaltung von IIS-Websites und macht den WebHandler-bereitstellen-Endpunkt für Clients verfügbar.
- **IIS: Standardauthentifizierung**. Dadurch wird das Modul der IIS-Standardauthentifizierung installiert. Mit dieser können den Webverwaltungsdienst (WMSvc) authentifizieren, die Anmeldeinformationen, die Sie bereitstellen.
- **Webbereitstellungstool 2.1 oder höher**. Dadurch wird Web Deploy (und die zugrunde liegende ausführbare Datei, die MSDeploy.exe) auf dem Server installiert. Im Rahmen dieses Prozesses der Handler für die Web-Bereitstellung installiert, und die Web-Management-Dienst integriert.
- **.NET Framework 4.0**. Dies ist erforderlich, um Anwendungen auszuführen, die in dieser Version von .NET Framework erstellt wurden.
- **ASP.NET MVC 3**. Dadurch werden die Assemblys, die Sie zum Ausführen von MVC 3-Anwendungen müssen installiert.

> [!NOTE]
> In dieser exemplarischen Vorgehensweise wird die Verwendung des Webplattform-Installers zum Installieren und konfigurieren die verschiedenen Komponenten beschrieben. Zwar Sie nicht mit dem Webplattform-Installer müssen, vereinfacht den Installationsvorgang durch automatisches Erkennen von Abhängigkeiten und sicherstellen, dass Sie immer die aktuellsten Versionen erhalten. Weitere Informationen finden Sie unter [Microsoft Web Platform Installer](https://go.microsoft.com/?linkid=9805118).


**Installieren der erforderlichen Produkte und Komponenten**

1. Herunterladen und Installieren der [Webplattform-Installer](https://go.microsoft.com/?linkid=9805118).
2. Wenn die Installation abgeschlossen ist, wird der Webplattform-Installer automatisch gestartet.

    > [!NOTE]
    > Sie können jetzt den Webplattform-Installer starten, zu einem beliebigen Zeitpunkt aus der **starten** Menü. Zu diesem Zweck die **starten** Menü klicken Sie auf **Programme**, und klicken Sie dann auf **Microsoft Web Platform Installer**.
3. Am oberen Rand der **Webplattform-Installer** Fenster, klicken Sie auf **Produkte**.
4. Klicken Sie auf der linken Seite des Fensters, klicken Sie im Navigationsbereich auf **Frameworks**.
5. In der **Microsoft .NET Framework 4** Zeile, wenn .NET Framework nicht bereits installiert ist, klicken Sie auf **hinzufügen**.

    > [!NOTE]
    > Möglicherweise haben Sie bereits über Windows Update .NET Framework 4.0 installiert. Wenn ein Produkt oder eine Komponente bereits installiert ist, den Webplattform-Installer wird angegeben, dies durch Ersetzen der **hinzufügen** Schaltfläche mit dem Text **installiert**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image1.png)
6. In der **ASP.NET MVC 3 (Visual Studio 2010)** auf **hinzufügen**.
7. Klicken Sie im Navigationsbereich auf **Server**.
8. In der **empfohlenen IIS 7-Konfiguration** auf **hinzufügen**.
9. In der **Web Deployment Tool 2.1** auf **hinzufügen**.
10. In der **IIS: Standardauthentifizierung** auf **hinzufügen**.
11. In der **IIS: Management-Dienst** auf **hinzufügen**.
12. Klicken Sie auf **Installieren**. Der Webplattform-Installer zeigt Ihnen eine Liste der Produkte&#x2014;zusammen mit verknüpften Abhängigkeiten&#x2014;installiert werden und werden Sie aufgefordert, den Lizenzbedingungen zustimmen.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image2.png)
13. Lesen Sie die Lizenzbedingungen, und wenn Sie den Bedingungen zustimmen, klicken Sie auf **akzeptieren**.
14. Wenn die Installation abgeschlossen ist, klicken Sie auf **Fertig stellen**, und schließen Sie dann die **Webplattform-Installer** Fenster.

Wenn Sie .NET Framework 4.0 vor der Installation von IIS installiert, müssen Sie zum Ausführen der [ASP.NET IIS Registration Tool](https://msdn.microsoft.com/library/k6h9cz8h(v=VS.100).aspx) (Aspnet\_regiis.exe) auf die neueste Version von ASP.NET bei IIS registrieren. Wenn Sie dies nicht beachten, Sie feststellen werden, dass IIS statischer Inhalte (z. B. HTML-Dateien bereitstellen) ohne Probleme, aber es gibt **HTTP-Fehler 404.0 – nicht gefunden.** beim Versuch, um ASP.NET-Inhalt zu suchen. Sie können im nächste Verfahren verwenden, um sicherzustellen, dass ASP.NET 4.0 registriert ist.

**Um ASP.NET 4.0 bei IIS registrieren.**

1. Klicken Sie auf **starten**, und geben Sie dann **Eingabeaufforderung**.
2. In den Suchergebnissen mit der Maustaste **Eingabeaufforderung**, und klicken Sie dann auf **als Administrator ausführen**.
3. Wechseln Sie in das Eingabeaufforderungsfenster zu dem **%WINDIR%\Microsoft.NET\Framework\v4.0.30319** Verzeichnis.
4. Geben Sie folgenden Befehl aus, und drücken Sie dann die EINGABETASTE:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample1.cmd)]
5. Wenn Sie zum Hosten von 64-Bit-Webanwendungen zu einem beliebigen Zeitpunkt planen, sollten Sie auch die 64-Bit-Version von ASP.NET bei IIS registrieren. Navigieren Sie im Eingabeaufforderungsfenster Befehl dazu, zu der **%WINDIR%\Microsoft.NET\Framework64\v4.0.30319** Verzeichnis.
6. Geben Sie folgenden Befehl aus, und drücken Sie dann die EINGABETASTE:

    [!code-console[Main](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/samples/sample2.cmd)]

Als sich bewährt verwenden Sie Windows Update erneut an diesem Punkt zum Herunterladen und installieren alle verfügbaren Updates für die neue Produkte und Komponenten, die Sie installiert haben.

## <a name="configure-the-web-management-service"></a>Konfigurieren Sie die Web-Dienst

Nun, da Sie alles, was installiert haben, die Sie benötigen, ist im nächste Schritt konfigurieren Sie die Web-Management-Dienst in IIS. Im Allgemeinen müssen Sie diese Aufgaben ausführen:

- Aktivieren der Standardauthentifizierung auf Serverebene.
- Konfigurieren Sie den Web-Management-Dienst, um Remoteverbindungen zu akzeptieren.
- Starten Sie den Web-Management-Dienst.
- Überprüfen Sie, dass die erforderlichen Web-Management-Dienst Delegierungsregeln vorhanden sind.

**So konfigurieren Sie die Web-Management-Dienst**

1. Auf der **starten** Startmenü **Verwaltung**, und klicken Sie dann auf **(Internet Information Services, IIS) Manager**.
2. Im IIS-Manager in der **Verbindungen** Bereich, klicken Sie auf den Serverknoten (z. B. **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image3.png)
3. Klicken Sie im mittleren Bereich unter **IIS**, doppelklicken Sie auf **Authentifizierung**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image20.png)
4. Mit der rechten Maustaste **Standardauthentifizierung**, und klicken Sie dann auf **aktivieren**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image5.png)
5. In der **Verbindungen** Bereich, klicken Sie auf den Serverknoten, um die Einstellungen der obersten Ebene zurückzukehren.
6. Klicken Sie im mittleren Bereich unter **Management**, doppelklicken Sie auf **Verwaltungsdienst**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image6.png)
7. Wählen Sie im mittleren Bereich **Remoteverbindungen**.

    > [!NOTE]
    > Wenn die Web-Management-Dienst bereits ausgeführt wird, müssen Sie zunächst beenden.
8. In der **Aktionen** Bereich, klicken Sie auf **starten** zum Starten des Web-Management-Diensts.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image7.png)
9. Wenn Sie aufgefordert werden, um Ihre Einstellungen zu speichern, klicken Sie auf **Ja**.

    > [!NOTE]
    > Sie sollten sich auch den Dienst für den automatischen start konfiguriert. Zu diesem Zweck öffnen Sie Dienste, mit der rechten Maustaste **Webverwaltungsdienst**, und klicken Sie dann auf **Eigenschaften**. In der **Starttyp** Dropdownliste **automatische**, und klicken Sie dann auf **OK**.
10. In der **Verbindungen** Bereich, klicken Sie auf den Serverknoten, um die Einstellungen der obersten Ebene zurückzukehren.
11. Klicken Sie im mittleren Bereich unter **Management**, doppelklicken Sie auf **Management-Dienst Delegierung**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image8.png)
12. Stellen Sie sicher, dass im mittleren Bereich einen Satz von Regeln enthält.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image9.png)

    Mit diesen Regeln können autorisierte Benutzer von Web-Management-Dienst auf verschiedene Web Deploy-Anbieter verwenden. Z. B. zum Bereitstellen von Webanwendungen und Inhalt in IIS über die Web-bereitstellen-Handler ist eine Delegierung zulässt, dass alle authentifizierten Benutzer der Web-Management-Dienst verwendet die **ContentPath** und **IisApp**  Anbieter (die letzte Regel, die Sie im Screenshot sehen können).

    Wenn Sie in der Reihenfolge, in diesem Thema beschriebenen Produkte und Komponenten installiert haben, sollten die neueste Version von Web Deploy automatisch an den Webdienst für die Verwaltung aller erforderlichen Delegierungsregeln hinzufügen. Wenn die Seite für die Delegierung der Management-Dienst keine Regeln, die angezeigt wird, müssen Sie diese selbst erstellen. Anleitungen hierzu finden Sie unter [konfigurieren Sie das Web Deployment Handler](https://go.microsoft.com/?linkid=9805124).
13. In der **Verbindungen** Bereich, klicken Sie auf den Serverknoten, um die Einstellungen der obersten Ebene zurückzukehren.

## <a name="create-and-configure-an-iis-website"></a>Erstellen Sie und konfigurieren Sie eine IIS-Website

Bevor Sie die Webinhalte auf Ihrem Server bereitstellen können, müssen Sie zum Erstellen und Konfigurieren einer IIS-Website, um den Inhalt zu hosten. Web Deploy kann Webpakete nur auf einer vorhandenen IIS-Website bereitgestellt werden; die Website kann nicht für Sie erstellt. Sie müssen außerdem führen Sie eine wenig zusätzliche Konfiguration, um Ihr Konto ohne Administratorrechte zum Bereitstellen von Inhalt per Remotezugriff zu ermöglichen. Im Allgemeinen müssen Sie diese Aufgaben ausführen:

- Erstellen Sie einen Ordner im Dateisystem, um Ihren Inhalt zu hosten.
- Erstellen einer IIS-Website, um den Inhalt zu verarbeiten, und ordnen sie den lokalen Ordner.
- Erteilen von Leseberechtigungen auf die Identität des Anwendungspools für den lokalen Ordner.
- Erteilen Sie die erforderlichen IIS-Berechtigungen für das Domänenkonto, das die Webanwendung bereitgestellt wird.

Es ist, zwar nichts hindert, dass Sie zum Bereitstellen von Inhalten an die Standardwebsite in IIS wird dieser Ansatz nicht für etwas anderes als Test oder Demo-Szenarien empfohlen. Um eine produktionsumgebung zu simulieren, sollten Sie eine neue IIS-Website mit Einstellungen erstellen, die den Anforderungen Ihrer Anwendung spezifisch sind.

**Zum Erstellen einer IIS-website**

1. Auf dem lokalen Dateisystem, erstellen Sie einen Ordner zum Speichern Ihrer Inhalte (z. B. **C:\DemoSite**).
2. Auf der **starten** Startmenü **Verwaltung**, und klicken Sie dann auf **(Internet Information Services, IIS) Manager**.
3. Im IIS-Manager in der **Verbindungen** Bereich, erweitern Sie den Serverknoten (z. B. **STAGEWEB1**).

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image10.png)
4. Mit der rechten Maustaste die **Websites** Knoten, und klicken Sie dann auf **Website hinzufügen**.
5. In der **Standortname** geben einen Namen für die IIS-Website (z. B. **DemoSite**).
6. In der **physischer Pfad** Feld aus, geben Sie (oder navigieren Sie zu) den Pfad zum lokalen Ordner (z. B. **C:\DemoSite**).
7. In der **Port** geben die Portnummer, die Sie auf die Website zu hosten (z. B. **85**).

    > [!NOTE]
    > Die standardmäßigen Portnummern sind 80 für HTTP und 443 für HTTPS. Wenn Sie diese Website an Port 80 hosten, müssen Sie jedoch die Standard-Website zu beenden, bevor Sie Ihre Website zugreifen können.
8. Lassen Sie die **Hostnamen** Feld leer, es sei denn, Sie verwenden möchten, konfigurieren Sie einen Domain Name System (DNS)-Eintrag für die Website, und klicken Sie dann auf **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image11.png)

    > [!NOTE]
    > In einer produktionsumgebung sollten Sie wahrscheinlich zum Hosten Ihrer Website an Port 80 und Konfigurieren eines Hostheaders, zusammen mit DNS-Datensätze werden abgeglichen. Weitere Informationen zum Konfigurieren von Host-Header in IIS 7 finden Sie unter [Konfigurieren eines Hostheaders für eine Website (IIS 7)](https://technet.microsoft.com/library/cc753195(WS.10).aspx). Weitere Informationen zu den DNS-Server-Rolle in Windows Server, finden Sie unter [DNS-Serverübersicht](https://technet.microsoft.com/en-gb/library/cc770392.aspx) und [DNS-Server](https://technet.microsoft.com/windowsserver/dd448607).
9. In der **Aktionen** Bereich unter **Site bearbeiten**, klicken Sie auf **Bindungen**.
10. In der **Sitebindungen** Dialogfeld klicken Sie auf **hinzufügen**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image12.png)
11. In der **Sitebindung hinzufügen** (Dialogfeld), legen die **IP-Adresse** und **Port** entsprechend Ihrer vorhandenen Standortkonfiguration.
12. In der **Hostnamen** geben den Namen des Webservers (z. B. **STAGEWEB1**), und klicken Sie dann auf **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image13.png)

    > [!NOTE]
    > Die erste sitebindung ermöglicht Ihnen Zugriff auf die Website, die lokal mithilfe der IP-Adresse und Port oder `http://localhost:85`. Die zweite sitebindung ermöglicht Ihnen Zugriff auf die Website von anderen Computern in der Domäne, die mit dem Computernamen (z. B. http://stageweb1:85).
13. In der **Sitebindungen** Dialogfeld klicken Sie auf **schließen**.
14. In der **Verbindungen** Bereich, klicken Sie auf **Anwendungspools**.
15. In der **Anwendungspools** Bereich mit der rechten Maustaste in des Namens des Anwendungspools, und klicken Sie dann auf **Grundeinstellungen**. Standardmäßig wird der Name des Ihr Anwendungspool mit dem Namen Ihrer Website übereinstimmen (z. B. **DemoSite**).
16. In der **.NET CLR-Version** Liste **.NET CLR-v4.0.30319**, und klicken Sie dann auf **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image21.png)

    > [!NOTE]
    > Die beispiellösung ist .NET Framework 4.0 erforderlich. Dies ist im Allgemeinen nicht erforderlich für Web Deploy.

In der Reihenfolge für Ihre Website Inhalte bereitstellen muss die Identität des Anwendungspools schreibgeschützten Zugriff auf den lokalen Ordner verfügen, in dem den Inhalt gespeichert. Führen in IIS 7.5 und Anwendungspools mit der eine eindeutige Identität des Anwendungspools standardmäßig (im Gegensatz zu früheren Versionen von IIS-Anwendungspools in der Regel Ausführungsort würde mit dem NETZWERKDIENST-Konto). Die Identität des Anwendungspools ist kein echtes Benutzerkonto und wird nicht für alle Listen von Benutzern oder Gruppen ausgewiesen&#x2014;stattdessen, er wird dynamisch erstellt, wenn der Anwendungspool gestartet wird. Jede Identität des Anwendungspools wird hinzugefügt, für die lokale **IIS\_IUSRS** Sicherheitsgruppe als ausgeblendetes Element.

Um Berechtigungen für eine Anwendungspoolidentität in einer Datei oder Ordner zu gewähren, haben Sie zwei Möglichkeiten:

- Zuweisen von Berechtigungen, die Identität des Anwendungspools direkt mit dem Format <strong>IIS AppPool\</ strong ><em>[Name des Anwendungspools]</em>(z. B. <strong>IIS AppPool\DemoSite</strong>).
- Zuweisen von Berechtigungen für die **IIS\_IUSRS** Gruppe.

Der gängigste Ansatz ist das Zuweisen von Berechtigungen für die lokale **IIS\_IUSRS** zu gruppieren, denn mit diesem Ansatz können Sie die Anwendungspools zu ändern, ohne dass eine Neukonfiguration Dateisystemberechtigungen. Das nächste Verfahren verwendet diese Gruppe basierenden Ansatz.

> [!NOTE]
> Weitere Informationen zu den Identitäten der Anwendungspools in IIS 7.5, finden Sie unter [Anwendungspoolidentitäten](https://go.microsoft.com/?linkid=9805123).


**So konfigurieren Sie Berechtigungen für eine IIS-website**

1. Navigieren Sie in Windows Explorer auf den Speicherort Ihres lokalen Ordners.
2. Mit der rechten Maustaste in des Ordners, und klicken Sie dann auf **Eigenschaften**.
3. Auf der **Sicherheit** auf **bearbeiten**, und klicken Sie dann auf **hinzufügen**.
4. Klicken Sie auf **Speicherorte**. In der **Speicherorte** Dialogfeld Wählen Sie den lokalen Server, und klicken Sie dann auf **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image15.png)
5. In der **Benutzer oder Gruppen auswählen** (Dialogfeld), Typ **IIS\_IUSRS**, klicken Sie auf **Namen überprüfen**, und klicken Sie dann auf **OK**.
6. In der <strong>Berechtigungen für</strong><em>[Ordnername]</em> (Dialogfeld), beachten Sie, die die neue Gruppe zugewiesen wurde die <strong>lesen &amp; ausführen</strong>, <strong>Ordner auflisten Inhalt</strong>, und <strong>lesen</strong> standardmäßig Berechtigungen. Lassen Sie diese unverändert, und klicken Sie auf <strong>OK</strong>.
7. Klicken Sie auf <strong>OK</strong> schließen die <em>[Ordnername]</em><strong>Eigenschaften</strong> Dialogfeld.

Als letzte Aufgabe müssen Sie die entsprechenden Berechtigungen für dem Benutzer ohne Administratorrechte gewähren, dessen Anmeldeinformationen, die Sie zum Bereitstellen von Inhalt verwenden. Dieser Benutzer benötigt die Berechtigungen zum Bereitstellen von Inhalt per Remotezugriff zu Ihrer Website.

**So konfigurieren Sie IIS-Website-Berechtigungen für einen Domänenbenutzer ohne Administratorrechte**

1. Im IIS-Manager in der **Verbindungen** Bereich mit der rechten Maustaste in Ihrer Website-Knotens (z. B. **DemoSite**), zeigen Sie auf **bereitstellen**, und klicken Sie dann auf **Web konfigurieren Deploy-Veröffentlichung**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image16.png)
2. In der **konfigurieren bereitstellen Webveröffentlichung** Dialogfeld rechts neben der **wählen Sie einen Benutzer veröffentlichen gewähren** Liste, klicken Sie auf die Schaltfläche mit den Auslassungspunkten.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image17.png)
3. In der **Benutzer** Dialogfeld geben die Domäne und den Namen des Kontos zu verwenden, um Inhalte bereitzustellen, und klicken Sie dann auf **OK**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image18.png)
4. In der **konfigurieren bereitstellen Webveröffentlichung** Dialogfeld klicken Sie auf **Setup**.

    ![](configuring-a-web-server-for-web-deploy-publishing-web-deploy-handler/_static/image19.png)

    > [!NOTE]
    > Dieser Vorgang führt zwei wichtige Funktionen in einem Schritt. Zunächst erteilt die Benutzer die Berechtigung zum Ändern der Website Remote über die Web-Management-Dienst, gemäß den Delegierungsregeln, die Sie im vorherigen Abschnitt untersucht. Zweitens erteilt dem Benutzer Vollzugriff auf den Quellordner für die Website, wodurch den Benutzer hinzufügen, ändern und Festlegen von Berechtigungen für die Inhalte der Website.
5. In der **konfigurieren bereitstellen Webveröffentlichung** Dialogfeld klicken Sie auf **schließen**.

## <a name="configure-firewall-exceptions"></a>Konfigurieren von Firewallausnahmen

Standardmäßig wird TCP-Port 8172 den IIS-Web-Management-Dienst lauscht. Wenn Sie Windows-Firewall auf dem Webserver aktiviert ist, müssen Sie erstellen eine neue Eingangsregel zum Zulassen von TCP-Datenverkehr an Port 8172 (in der Windows-Firewall standardmäßig wird sämtlicher ausgehender Datenverkehr zulässig). Wenn Sie eine Drittanbieter Firewall verwenden, müssen Sie zum Erstellen von Regeln zum Zulassen von Datenverkehr.

| Richtung | Über Port | So portieren Sie | Porttyp |
| --- | --- | --- | --- |
| Eingehende | Beliebig | 8172 | TCP |
| Ausgehende | 8172 | Beliebig | TCP |
  

Weitere Informationen zum Konfigurieren von Regeln in der Windows-Firewall finden Sie unter [Firewallregeln konfigurieren](https://technet.microsoft.com/library/dd448559(WS.10).aspx). Firewalls von Drittanbietern finden Sie in der Produktdokumentation.

## <a name="conclusion"></a>Schlussbemerkung

Ihrem Webserver sollte jetzt remote-Bereitstellungen aus dem Web bereitstellen Ereignishandler über den Webdienst für die Verwaltung zu akzeptieren. Bevor Sie versuchen, eine Webanwendung mit dem Server bereitstellen, sollten Sie diese wichtigen Punkte überprüfen:

- Haben Sie die Standardauthentifizierung auf Serverebene in IIS aktiviert?
- Haben Sie Remoteverbindungen mit den Web-Management-Dienst aktiviert?
- Haben Sie die Web-Management-Dienst gestartet?
- Gibt es Management Service-Delegierungsregeln vorhanden?
- Besitzt die Identität des Anwendungspools Lesezugriff auf den Quellordner für Ihre Website?
- Ist das Konto für Benutzer ohne Administratorrechte auf in IIS berechtigt?
- Erlaubt die Firewall eingehende Verbindungen an den Server an TCP-Port 8172?

## <a name="further-reading"></a>Weiterführende Themen

Anleitungen zum Konfigurieren von benutzerdefinierter Microsoft Build Engine (MSBuild)-Projektdateien zum Bereitstellen von Web-Pakete an den Handler für die Web-Bereitstellung finden Sie unter [Konfigurieren von Bereitstellungseigenschaften für eine Zielumgebung](configuring-deployment-properties-for-a-target-environment.md).

> [!div class="step-by-step"]
> [Zurück](configuring-a-web-server-for-web-deploy-publishing-remote-agent.md)
> [Weiter](configuring-a-web-server-for-web-deploy-publishing-offline-deployment.md)
