---
uid: web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
title: 'ASP.NET-webbereitstellung mithilfe von Visual Studio: Bereitstellen für Testzwecke | Microsoft-Dokumentation'
author: tdykstra
description: Dieser tutorialreihe erfahren Sie, wie bereitzustellende (veröffentlichen) aus einer ASP.NET web-Anwendung auf Azure App Service-Web-Apps oder bei einem Hostinganbieter von Drittanbietern, indem Warnungsprovider...
ms.author: riande
ms.date: 01/16/2019
ms.assetid: 8bf2c4fb-4ee5-4841-bfc2-03462c1f7a7a
msc.legacyurl: /web-forms/overview/deployment/visual-studio-web-deployment/deploying-to-iis
msc.type: authoredcontent
ms.openlocfilehash: d49dfad368ca4b81bb865103a99ec223a1cc66df
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396336"
---
<a name="aspnet-web-deployment-using-visual-studio-deploying-to-test"></a>ASP.NET-webbereitstellung mithilfe von Visual Studio: Bereitstellen für Testzwecke
====================
durch [Tom Dykstra](https://github.com/tdykstra)

> Dieser tutorialreihe erfahren Sie, wie bereitzustellende (veröffentlichen) aus einer ASP.NET web-Anwendung auf Azure App Service-Web-Apps oder mit einem Drittanbieter-Hostinganbieter, die mit Visual Studio 2017. Weitere Informationen über die Reihe finden Sie unter [im ersten Tutorial der Reihe](introduction.md).

## <a name="overview"></a>Übersicht

In diesem Tutorial stellen Sie eine ASP.NET-Webanwendung zum Internet Information Server (IIS) auf dem lokalen Computer bereit.

In der Regel, wenn Sie eine Anwendung entwickeln, Sie führen es und Testen Sie es in Visual Studio. In der Standardeinstellung verwenden Webanwendungsprojekte in Visual Studio 2017 als Entwicklungswebserver IIS Express. IIS Express verhält sich ähnlich wie der vollständigen Variante von IIS als Visual Studio Development Server (auch als Cassini bezeichnet), der Visual Studio 2017 standardmäßig verwendet. Aber weder Entwicklungswebserver funktioniert genauso wie IIS. Daher eine app konnte ausführen und Testen ordnungsgemäß in Visual Studio jedoch fehl, wenn es in IIS bereitgestellt wird.

Sie können die Anwendung zuverlässig auf zwei Arten testen:

1. Bereitstellen Sie Ihrer Anwendung in IIS auf Ihrem Entwicklungscomputer, mit der gleichen Methode, die Sie später verwenden sie in Ihrer produktionsumgebung bereitstellen.

   Sie können konfigurieren, dass Visual Studio, um IIS zu verwenden, wenn Sie ein Webprojekt ausführen, aber wäre nicht, die den Bereitstellungsprozess zu testen. Diese Methode überprüft, ob Ihr Bereitstellungsprozess und, die Ihre Anwendung unter IIS ordnungsgemäß ausgeführt.

2. Bereitstellen Sie Ihrer Anwendung in einer testumgebung bereit, die ähnlich wie in der produktionsumgebung bereit. 
 
   Die produktionsumgebung für diese Tutorials ist in Azure App Service-Web-Apps. Der idealen Test-Umgebung handelt es sich um eine zusätzliche Web-app, die im Azure-Dienst erstellt. Obwohl er die gleiche Weise wie eine Produktions-Web-app eingerichtet werden würde, würden Sie nur zu Testzwecken verwenden.

Option 2 ist die zuverlässigste Möglichkeit, um zu testen. Wenn Sie Option 2 verwenden, muss nicht unbedingt mit der Option 1 aus. Jedoch wenn Sie einen Drittanbieter bereitstellen Hostinganbieter, Option 2 möglicherweise nicht möglich oder aufwändig sein, damit diese lernprogrammreihe beide Methoden veranschaulicht. Anleitungen für Option 2 finden Sie unter den [in der Produktionsumgebung bereitstellen](deploying-to-production.md) Tutorial.

Weitere Informationen zur Verwendung von Webservern in Visual Studio finden Sie unter [Webserver in Visual Studio für ASP.NET-Webprojekte](https://msdn.microsoft.com/library/58wxa9w5.aspx).

Erinnerung: Wenn Sie eine Fehlermeldung erhalten, oder etwas nicht funktioniert, wie Sie das Lernprogramm durchzuarbeiten, sollten Sie unbedingt die [Problembehandlungsseite](troubleshooting.md).

## <a name="download-the-contoso-university-starter-project"></a>Laden Sie das Startprojekt für die Contoso University

Herunterladen Sie und installieren Sie die Contoso University Visual Studio-Starter-Projektmappe und das Projekt. Diese Lösung umfasst das Lernprogramm abgeschlossene. 

[Startprojekt herunterladen](http://go.microsoft.com/fwlink/p/?LinkId=282627)

## <a name="install-iis"></a>Installieren von IIS

Vergewissern Sie sich, dass IIS und Web Deploy installiert sind, zum Bereitstellen in IIS auf Ihrem Entwicklungscomputer. Standardmäßig installiert Visual Studio, Web Deploy, aber IIS nicht in der Standardkonfiguration für Windows 10, Windows 8 oder Windows 7 enthalten. Wenn IIS bereits installiert haben und der Standardanwendungspool in .NET 4 bereits festgelegt ist, fahren Sie mit [im nächsten Abschnitt](#sqlexpress).

1. Es wird empfohlen, Sie verwenden die [Web Platform Installer (WPI)](https://www.microsoft.com/web/downloads/platform.aspx) IIS und Web Deploy zu installieren. WPI wird eine empfohlene IIS-Konfiguration, die Voraussetzungen für IIS und Web Deploy enthält, bei Bedarf installiert.

   Wenn Sie IIS, Web Deploy und die erforderlichen Komponenten bereits installiert haben, installiert die WPI nur was fehlt.

   * Verwenden Sie den Webplattform-Installer, um IIS und Web Deploy zu installieren:
    
     ![Installieren von IIS über WPI](deploying-to-iis/_static/image30.png)

     ![Installieren Sie Web Deploy WPI verwenden](deploying-to-iis/_static/image31.png)

     Sie sehen, die darauf hinweist, dass IIS 7 installiert werden soll. Der Link funktioniert für IIS 8 unter Windows 8; aber für Windows 8 und höher, über die folgenden Schritte aus, um sicherzustellen, dass ASP.NET 4.7 installiert ist:

   * Open **Systemsteuerung** > **Programme** > **Programme und Funktionen** > **Aktivieren von Windows-Funktionen ein- oder ausschalten** .

   * Erweitern Sie **Internetinformationsdienste**, **WWW-Dienste**, und **Anwendungsentwicklungsfeatures**.
   
   * Überprüfen Sie, ob **ASP.NET 4.7** ausgewählt ist.

     ![Select ASP.NET 4.7](deploying-to-iis/_static/image1a.png)

   * Überprüfen Sie, ob **WWW-Dienste** und **IIS-Verwaltungskonsole** ausgewählt ist. IIS und IIS-Manager wird installiert.
    
     ![Wählen Sie die WWW-Dienste](deploying-to-iis/_static/image24.png)    
  
   * Klicken Sie auf **OK**. Fehlermeldungen, der angibt, dass die Installation ausgeführt wird, werden angezeigt.

Führen Sie nach dem Installieren von IIS, **IIS-Manager** um sicherzustellen, dass .NET Framework, Version 4 des Standardanwendungspools zugewiesen ist.

1. Drücken Sie WINDOWS + R, zum Öffnen der **ausführen** Dialogfeld.

   (Für Windows 8 oder höher, geben Sie "ausführen", auf die **starten** Seite. Wählen Sie in Windows 7, **ausführen** aus der **starten** Menü. Wenn **ausführen** befindet sich nicht in der **starten** Menü, mit der rechten Maustaste in der Taskleiste, wählen Sie **Eigenschaften**, wählen die **Menü "Start"** wählen **Anpassen**, und wählen Sie **Ausführungsbefehl**.)

2. Geben Sie "Inetmgr", und wählen Sie **OK**.

3. In der **Verbindungen** Bereich, erweitern Sie den Serverknoten und wählen Sie **Anwendungspools**. In der **Anwendungspools** Bereich Wenn **DefaultAppPool** ist .NET Frameworkversion 4, wie in der folgenden Abbildung werden zugewiesen, mit dem nächsten Abschnitt überspringen.

   ![Inetmgr_showing_4.0_app_pools](deploying-to-iis/_static/image5a.png)

4. Wenn Sie sehen, dass nur zwei Anwendungspools, und sowohl die für .NET Framework 2.0 festgelegt sind, installieren Sie ASP.NET 4 in IIS.

   Für Windows 8 oder höher finden Sie im vorherigen Abschnitt Anweisungen und stellen Sie sicher, ASP.NET 4.7 installiert ist, oder finden Sie unter [installieren Sie ASP.NET 4.5 auf Windows 8 und Windows Server 2012](https://support.microsoft.com/kb/2736284). Für Windows 7, öffnen Sie ein Eingabeaufforderungsfenster, indem Sie mit der rechten Maustaste **Eingabeaufforderung** in der Windows **starten** Menü und auswählen **als Administrator ausführen**. Führen Sie [Aspnet\_regiis.exe](https://msdn.microsoft.com/library/k6h9cz8h.aspx) ASP.NET 4 in IIS mit den folgenden Befehlen installieren. (Ersetzen Sie in 32-Bit-Systemen, die "Framework64" mit "Framework").

   [!code-console[Main](deploying-to-iis/samples/sample1.cmd)]

   Dieser Befehl erstellt die neue Anwendung die Pools für .NET Framework 4, aber den Standardanwendungspool bleiben auf 2.0 festgelegt. Sie stellen eine Anwendung, die Ziele von .NET 4 an diesen Anwendungspool ändern des Anwendungspools also in .NET 4 bereit.

5. Wenn Sie geschlossen **IIS-Manager**, führen sie erneut aus, erweitern Sie den Serverknoten und wählen Sie **Anwendungspools**.

6. In der **Anwendungspools** wählen Sie im Bereich **DefaultAppPool**. In der **Aktionen** wählen Sie im Bereich **Grundeinstellungen**.

   ![Inetmgr_selecting_Basic_Settings_for_app_pool](deploying-to-iis/_static/image25.png)

7. In der **Anwendungspool bearbeiten** im Dialogfeld die **.NET CLR-Version** zu **.NET CLR-v4.0.30319**. Klicken Sie auf **OK**.

   ![Selecting_.NET_4_for_DefaultAppPool](deploying-to-iis/_static/image6a.png)

Sie sind jetzt bereit zum Veröffentlichen einer Webanwendung in IIS. Erstellen Sie zunächst jedoch Datenbanken für das Testen.

<a id="sqlexpress"></a>

## <a name="install-sql-server-express"></a>Installieren von SQL Server Express

LocalDB ist nicht vorgesehen, in IIS zu arbeiten, damit die testumgebung SQL Server Express installiert haben muss. Wenn Sie Visual Studio 2010 SQL Server Express verwenden, ist es bereits standardmäßig installiert. Wenn Sie Visual Studio 2012 oder höher verwenden, installieren Sie SQL Server Express.

Klicken Sie zum Installieren von SQL Server Express herunterladen und Installieren von [Download Center: Microsoft SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express). 

Wählen Sie auf der ersten Seite des SQL Server-Installationscenter **eigenständige neue SQL Server-Installation oder Hinzufügen von Funktionen zu einer vorhandenen Installation** , und befolgen Sie die Anweisungen aus, übernehmen Sie die Standardoptionen. Übernehmen Sie im Installations-Assistenten die Standardeinstellungen aus. Weitere Informationen zu Installationsoptionen finden Sie unter [Installieren von SQL Server vom Installations-Assistenten (Setup)](https://msdn.microsoft.com/library/ms143219.aspx).

## <a name="create-sql-server-express-databases-for-the-test-environment"></a>Erstellen von SQL Server Express-Datenbanken, für die testumgebung

Der Contoso University-Anwendung besteht aus zwei Datenbanken: 

1. Mitgliedschaftsdatenbank 
2. dienstanwendungs-Datenbank 

Sie können diese Datenbanken auf zwei separate Datenbanken oder auf eine einzelne Datenbank bereitstellen. Kombinieren sie macht Datenbankjoins zwischen ihnen vereinfacht. 

Wenn Sie bei einem Hostinganbieter von Drittanbietern bereitstellen, kann Ihren hostingplan einen Grund, diese zu kombinieren Ihnen auch. Beispielsweise wird der Anbieter möglicherweise mehr für mehrere Datenbanken berechnet oder u. u. nicht mehr als eine Datenbank selbst zu.

In diesem Tutorial stellen Sie mit zwei Datenbanken in der testumgebung und eine Datenbank in der Staging- und produktionsumgebungen bereit.

Von der **Ansicht** in Visual Studio, wählen Sie im Menü **Server-Explorer** (**Datenbank-Explorer** in Visual Web Developer). Mit der rechten Maustaste **Datenverbindungen** , und wählen Sie **neue SQL Server-Datenbank erstellen**.

![Selecting_Create_New_SQL_Server_Database](deploying-to-iis/_static/image8.png)

In der **neue SQL Server-Datenbank erstellen** Dialogfeld Geben Sie ". \SQLExpress" in der **Servernamen** Box als auch "Aspnet-ContosoUniversity" in der **neuen Datenbanknamen** Feld. Klicken Sie auf **OK**.

![Create aspnet-ContosoUniversity](deploying-to-iis/_static/image9.png)

Führen Sie das gleiche Verfahren zum Erstellen einer neuen SQL Server Express School-Datenbank, die mit dem Namen `ContosoUniversity`.

**Server-Explorer** zeigt die beiden neuen Datenbanken.

![Neue Datenbanken im Server-Explorer](deploying-to-iis/_static/image10.png)

## <a name="create-a-grant-script-for-the-new-databases"></a>Erstellen Sie ein Skript gewähren, für die neuen Datenbanken

Wenn die Anwendung in IIS auf Ihrem Entwicklungscomputer ausgeführt wird, verwendet die Anwendung den Standardanwendungspool Anmeldeinformationen Zugriff auf die Datenbank an. Standardmäßig, allerdings verfügt nicht der Anwendungspool, öffnen Sie die Datenbanken Berechtigung. Dies bedeutet, dass Sie zum Ausführen eines Skripts, um diese Berechtigung zu erteilen. In diesem Abschnitt werden Sie das Skript zu erstellen und führen Sie es später noch Mal, stellen Sie sicher, dass die Anwendung die Datenbanken öffnen kann, wenn sie in IIS ausgeführt wird.

In einem Text-Editor, kopieren Sie die folgenden SQL-Befehle in eine neue Datei, und speichern Sie ihn *Grant.sql*. 

[!code-sql[Main](deploying-to-iis/samples/sample2.sql)]

Öffnen Sie in Visual Studio die Contoso University-Projektmappe ein. Mit der rechten Maustaste in der Projektmappe (nicht auf eines der Projekte), und wählen **hinzufügen**. Wählen Sie **vorhandenes Element**, navigieren Sie zu *Grant.sql*, und öffnen Sie sie.

> [!NOTE]
> Dieses Skript wurde zum Arbeiten mit SQL Server Express 2012 oder höher und mit den IIS-Einstellungen in Windows 10, Windows 8 oder Windows 7 entwickelt, wie sie in diesem Tutorial angegeben sind. Wenn Sie eine andere Version von SQL Server oder Windows verwenden, oder wenn Sie IIS auf Ihrem Computer anders festlegen, können Änderungen an diesem Skript erforderlich sein. Weitere Informationen zu SQL Server-Skripts, finden Sie unter [SQL Server-Onlinedokumentation](https://go.microsoft.com/fwlink/?LinkId=132511).

> [!NOTE] 
> **Sicherheitshinweis** dieses Skript gibt `db_owner` Berechtigungen für den Benutzer, die Zugriff auf die Datenbank zur Laufzeit handelt es sich in der produktionsumgebung wiederholen müssen. In einigen Fällen empfiehlt es sich ein Benutzer an, der vollständige Datenbankschema Aktualisieren der Berechtigungen nur für die Bereitstellung, und geben Sie für die Laufzeit einen anderen Benutzer, der berechtigt nur zum Lesen und Schreiben von Daten verfügt. Weitere Informationen finden Sie unter [überprüfen die automatischen Änderungen der Datei "Web.config" für Code First-Migrationen](#reviewingmigrations) weiter unten in diesem Tutorial.

<a id="publish"></a>

## <a name="run-the-grant-script-in-the-application-database"></a>Führen Sie das Skript zum erteilen, in der Datenbank

Sie können konfigurieren, dass das Veröffentlichungsprofil, um das Skript zum Erteilen in der Mitgliedschaftsdatenbank während der Bereitstellung ausgeführt wird, da die datenbankbereitstellung verwendet die [DbDacFx](https://docs.microsoft.com/iis/publish/using-web-deploy/dbdacfx-provider-for-incremental-database-publishing) Anbieter. Während der Bereitstellung von Code First-Migrationen, die ist, wie Sie die Datenbank bereitstellen möchten, können Sie können keine Skripts ausführen. Dies bedeutet, dass Sie das Skript vor der Bereitstellung manuell in der Datenbank ausführen.

1. Öffnen Sie in Visual Studio die *Grant.sql* -Datei, die Sie zuvor erstellt haben.

2. Wählen Sie **verbinden**. 

    ![Schaltfläche "Verbinden"](deploying-to-iis/_static/image11.png)

3. In der **Herstellen einer Verbindung mit Server** Dialogfeld Geben Sie *. \SQLExpress* als die **Servernamen**. Wählen Sie **verbinden**.

4. Wählen Sie in der Datenbank-Dropdown-Liste **ContosoUniversity**. Wählen Sie **ausführen**. 

   ![](deploying-to-iis/_static/image12.png)

Die Standardidentität des Anwendungspools verfügt jetzt über ausreichende Berechtigungen in der Anwendungsdatenbank für Code First-Migrationen auf die Tabellen der Datenbank zu erstellen, wenn die Anwendung ausgeführt wird.

## <a name="publish-to-iis"></a>Veröffentlichen in IIS

Es gibt mehrere Möglichkeiten, die Sie IIS mithilfe von Visual Studio und Web Deploy bereitstellen können:

* Verwenden Sie Visual Studio-One-Click-Veröffentlichung.
* Veröffentlichen Sie über die Befehlszeile.
* Erstellen eines Bereitstellungspakets aus, und installieren sie die IIS-Manager verwenden. Das Paket verfügt über eine ZIP-Datei mit allen Dateien und Metadaten, die zum Installieren eines Standorts in IIS erforderlich.
* Erstellen eines Bereitstellungspakets aus, und installieren Sie es über die Befehlszeile.

Der Prozess, die in Visual Studio einrichten, um automatisieren den vorherigen Tutorials durcharbeiten Bereitstellungsaufgaben gilt für alle diese Methoden. In diesen Lernprogrammen verwenden Sie die ersten beiden Methoden. Weitere Informationen zur Verwendung von Bereitstellungspaketen, finden Sie unter [Bereitstellen einer Web-Anwendung erstellen und Installieren eines Webbereitstellungspakets](https://go.microsoft.com/fwlink/p/?LinkId=282413#package) in den Einstieg in die Webbereitstellung für Visual Studio und ASP.NET.

Stellen Sie vor der Veröffentlichung sicher, dass Sie Visual Studio im Administratormodus ausgeführt werden. Wenn Sie nicht sehen **(Administrator)** in der Titelleiste angezeigt wird, schließen Sie Visual Studio. In Windows 8 (oder höher) **starten** Seite oder die Windows 7 **starten** Menü, mit der rechten Maustaste in des Visual Studio-Symbols, und wählen Sie **als Administrator ausführen**. Administrator-Modus ist nur erforderlich, für die Veröffentlichung, wenn Sie IIS auf dem lokalen Computer veröffentlichen.

### <a name="create-the-publish-profile"></a>Erstellen Sie das Veröffentlichungsprofil

1. In **Projektmappen-Explorer**, mit der rechten Maustaste die **ContosoUniversity** Projekt (nicht die **ContosoUniversity.DAL** Projekt). Wählen Sie **Veröffentlichen**. Die **veröffentlichen** Seite wird angezeigt.

2. Wählen Sie **neues Profil**. Die **Veröffentlichungsziel** Dialogfeld wird angezeigt.

3. Wählen Sie **IIS, FTP usw.**. Klicken Sie auf **Profil erstellen**. Die **veröffentlichen** -Assistent wird angezeigt.

   ![Veröffentlichen Sie die Assistenten "Web", Registerkarte "Profil"](deploying-to-iis/_static/image26.png)

4. Von der **Veröffentlichungsmethode** wählen Sie im Dropdownmenü **Web Deploy**.

5. Für **Server**, geben Sie *"localhost"*.

6. Für **Standortname**, geben Sie *Default Web Site/ContosoUniversity*.

7. Für **Ziel-URL**, geben Sie *http://localhost/ContosoUniversity*.

   Die **Ziel-URL** Einstellung ist nicht erforderlich. Wenn Visual Studio abgeschlossen ist, die Anwendung bereitstellen, wird er automatisch Ihren Standardbrowser mit dieser URL geöffnet. Wenn Sie nicht möchten, dass den Browser nach der Bereitstellung automatisch geöffnet wird, lassen Sie dieses Feld leer.

8. Wählen Sie **Verbindung überprüfen** zu überprüfen, ob die Einstellungen richtig sind und Sie können mit IIS auf dem lokalen Computer verbinden.

   Ein grünes Häkchen stellt sicher, dass die Verbindung erfolgreich ist.

   ![Veröffentlichen von Web-Assistent – Registerkarte "Verbindung"](deploying-to-iis/_static/image27.png)

9. Wählen Sie **Weiter** , fahren Sie fort, um die **Einstellungen** Registerkarte.

10. Die **Konfiguration** Dropdown-Feld gibt an, die Build-Konfiguration bereitstellen. Übernehmen sie auf den Standardwert des **Version**. Sie wird nicht in diesem Tutorial Debug-Builds bereitgestellt werden.

11. Erweitern Sie **Dateiveröffentlichungsoptionen**. Wählen Sie **Ausschließen von Dateien aus der App\_Datenordner**.

    In der testumgebung, die Anwendung greift auf die Datenbanken, die Sie in der lokalen SQL Server Express-Instanz, nicht die MDF-Dateien in erstellt die *App\_Daten* Ordner.

12. Lassen Sie die **während der Veröffentlichung Vorkompilieren** und **entfernen weiterer Dateien am Ziel** Kontrollkästchen deaktiviert.

    ![Dateiveröffentlichungsoptionen in der Registerkarte "Einstellungen"](deploying-to-iis/_static/image15a.png)

    Vorkompilieren ist eine Option, die vor allem für große Standorte geeignet ist. Sie können die Startzeit der ersten reduzieren, die eine Seite angefordert wird, nachdem der Standort veröffentlicht wird.

    Sie müssen zusätzliche Dateien zu entfernen, da dies Ihre erste Bereitstellung und es keine Dateien im Zielordner noch nicht.

    > [!NOTE] 
    > Bei Auswahl von **entfernen weiterer Dateien am Ziel** für eine nachfolgende Bereitstellung desselben Standorts sicher, dass verwenden Sie das Vorschaufeature, sodass Sie im Voraus sehen, welche Dateien vor der Bereitstellung gelöscht werden. Das erwartete Verhalten ist, dass Web Deploy-Dateien auf dem Zielserver gelöscht werden, die Sie in Ihrem Projekt gelöscht haben. Allerdings wird die gesamte Ordnerstruktur unter der Quell-und Ziel verglichen. und in einigen Szenarien, Web Deploy-Dateien, die Sie löschen möchten, nicht löschen kann.
    > 
    > Z. B. Wenn Sie eine Webanwendung in einem Unterordner auf dem Server, beim Bereitstellen eines Projekts in den Stammordner haben, Unterordner gelöscht werden. Sie können ein Projekt für die Hauptwebsite auf "contoso.com" und ein anderes Projekt für einen Blog unter Objekte Remote verfügen. Die Bloganwendung ist in einem Unterordner. Bei Auswahl von **entfernen weiterer Dateien am Ziel** beim zentralen Standort bereitstellen, die Bloganwendung gelöscht werden.
    > 
    > Ein weiteres Beispiel, Ihre App\_Datenordner möglicherweise unerwartet gelöscht. Bestimmte Datenbanken wie SQL Server Compact-Datenbankdateien speichern, in der App\_Datenordner. Nach der ersten Bereitstellung, die Sie die Datenbankdateien bei nachfolgenden Bereitstellungen kopieren beibehalten, sodass Sie auswählen möchten nicht **Ausschließen von App\_Daten** auf der Registerkarte "Web packen/veröffentlichen". Danach, auch wenn man **entfernen weiterer Dateien am Ziel** ausgewählt, die Datenbankdateien und die App\_Datenordner selbst werden gelöscht, wenn Sie das nächste Mal, die Sie veröffentlichen.

### <a name="configure-deployment-for-the-membership-database"></a>Konfigurieren der Bereitstellung für die Mitgliedschaftsdatenbank

Die folgenden Schritte gelten für die **DefaultConnection** Datenbank in des Dialogfelds **Datenbanken** Abschnitt.

1. In der **remoteverbindungszeichenfolge** Geben Sie die folgende Verbindungszeichenfolge, die auf der neuen SQL Server Express-Mitgliedschaftsdatenbank verweist.

   [!code-console[Main](deploying-to-iis/samples/sample3.cmd)]

   Während des Bereitstellungsvorgangs wird diese Verbindungszeichenfolge in der bereitgestellten Web.config-Datei, da **verwenden Sie diese Verbindungszeichenfolge zur Laufzeit** ausgewählt ist.

    Sie können auch die Verbindungszeichenfolge aus abrufen **Server-Explorer**. In **Server-Explorer**, erweitern Sie **Datenverbindungen** , und wählen Sie die  **&lt;"MachineName"&gt;\sqlexpress.aspnet-ContosoUniversity** Datenbank, klicken Sie dann aus der **Eigenschaften** Fenster Kopieren der **Verbindungszeichenfolge** Wert. Dass die Verbindungszeichenfolge eine weitere Einstellung haben, die Sie löschen können: `Pooling=False`.

2. Wählen Sie **Aktualisieren einer Datenbank**.

   Dies bewirkt, dass das Datenbankschema in der Zieldatenbank, die während der Bereitstellung erstellt werden. In den nächsten Schritten Geben Sie die zusätzlichen Skripts, die Sie ausführen müssen: eine zum Gewähren des Datenbankzugriffs an den Standardanwendungspool und zur Bereitstellung von Daten.

3. Wählen Sie **Datenbankupdates konfigurieren**.
 
4. In der **Datenbankupdates konfigurieren** wählen Sie im Dialogfeld **SQL-Skript hinzufügen**. Navigieren Sie zu der *Grant.sql* -Skript, das Sie zuvor im Projektmappenordner gespeichert haben.

5. Wiederholen Sie den Vorgang zum Hinzufügen der *Aspnet-Daten-dev.sql* Skript.

   ![Konfigurieren von Datenbank-Updates für die Mitgliedschaftsdatenbank](deploying-to-iis/_static/image16.png)

6. Wählen Sie **schließen**.

### <a name="configure-deployment-for-the-application-database"></a>Die Bereitstellung für die Datenbank konfigurieren

Wenn Visual Studio ein Entity Framework erkennt `DbContext` -Klasse, erstellt es einen Eintrag im der **Datenbanken** Abschnitt eine **Execute Code First Migrations** Kontrollkästchen anstelle von einer  **Aktualisieren der Datenbank** Kontrollkästchen. In diesem Tutorial verwenden Sie das Kontrollkästchen, an der Bereitstellung von Code First-Migrationen.

In einigen Szenarien, verwenden Sie möglicherweise eine `DbContext` Datenbank, aber Sie möchten den DbDacFx-Anbieter statt Migrationen zu verwenden, zum Bereitstellen der Datenbank. In diesem Fall finden Sie unter [wie Stelle ich eine Code First-Datenbank ohne Migrationen bereit?](https://msdn.microsoft.com/library/ee942158.aspx#deploy_code_first_without_migrations) in der ASP.NET Web-Bereitstellung – häufig gestellte Fragen auf MSDN.

Die folgenden Schritte gelten für die **SchoolContext** Datenbank in des Dialogfelds **Datenbanken** Abschnitt.

1. In der **remoteverbindungszeichenfolge** Geben Sie die folgende Verbindungszeichenfolge, die auf die neue Datenbank für SQL Server Express-Anwendung verweist.

   [!code-console[Main](deploying-to-iis/samples/sample4.cmd)]

   Während des Bereitstellungsvorgangs wird diese Verbindungszeichenfolge in der bereitgestellten Web.config-Datei, da **verwenden Sie diese Verbindungszeichenfolge zur Laufzeit** ausgewählt ist.

   Außerdem erhalten Sie die Anwendung Datenbank-Verbindungszeichenfolge aus **Server-Explorer** auf die gleiche Weise haben Sie die Mitgliedschaft in Datenbank-Verbindungszeichenfolge.

2. Wählen Sie **Code First-Migrationen ausführen (wird beim Anwendungsstart ausgeführt)**.

   Diese Option bewirkt, dass den Bereitstellungsprozess so konfigurieren Sie die bereitgestellte Datei "Web.config", an die `MigrateDatabaseToLatestVersion` Initialisierer. Diese Initialisierer aktualisiert die Datenbank automatisch auf die neueste Version, wenn die Anwendung zum ersten Mal nach der Bereitstellung auf die Datenbank zugreift.

### <a name="configure-publish-profile-transforms"></a>Konfigurieren von Transformationen von Profil veröffentlichen

1. Wählen Sie **schließen**. Wählen Sie **Ja** Wenn Sie gefragt werden, wenn Sie Änderungen speichern möchten.

2. In **Projektmappen-Explorer**, erweitern Sie **Eigenschaften**, erweitern Sie **PublishProfiles**.

3. Mit der rechten Maustaste *CustomProfile.pubxml* und benennen Sie sie *Test.pubxml*.

4. Mit der rechten Maustaste *Test.pubxml*. Wählen Sie **Konfigurationstransformation hinzufügen**.

   ![Konfigurationstransformation-Menü "hinzufügen"](deploying-to-iis/_static/image17.png)

   Visual Studio erstellt die *Web.Test.config* Transform-Datei und öffnet sie.

5. In der *Web.Test.config* Transformationsdatei, fügen Sie den folgenden Code unmittelbar nach dem öffnenden Konfigurationstag.

   [!code-xml[Main](deploying-to-iis/samples/sample5.xml)]

    Wenn Sie den Test verwenden das Veröffentlichungsprofil, diese Transformation legt den Indikator "Umgebung" zu "Test". In der bereitgestellten Website sehen Sie nach dem "Contoso University" H1-Überschrift "(Test)".

6. Speichern und schließen Sie die Datei.

7. Mit der rechten Maustaste die *Web.Test.config* und wählen Sie **Vorschau transformieren** um sicherzustellen, dass die Transformation, die Sie programmiert die erwarteten Änderungen erzeugt.

    Die **Vorschau der Datei "Web.config"** Fenster zeigt das Ergebnis der Anwendung sowohl die *Datei "Web.Release.config"* transformiert und die *Web.Test.config* transformiert.

### <a name="preview-the-deployment-updates"></a>Vorschau der bereitgestellten updates

1. Öffnen der **Webveröffentlichung** Assistenten erneut aus (mit der rechten Maustaste in des ContosoUniversity-Projekts, wählen Sie **veröffentlichen**, klicken Sie dann **Vorschau**).

2. In der **Vorschau** wählen Sie im Dialogfeld **Vorschau starten** um eine Liste der Dateien anzuzeigen, die kopiert werden. 

   ![Vorschau veröffentlichen](deploying-to-iis/_static/image29.png)

   Sie können auch auswählen, die **datenbankvorschau** Link zum Anzeigen der Skripts, die in der Mitgliedschaftsdatenbank ausgeführt werden. (Keine Skripts werden für die Bereitstellung von Code First-Migrationen, damit keine Vorschau für die Datenbank ausgeführt.)

3. Wählen Sie **Veröffentlichen**.

   Ist Visual Studio nicht im Administratormodus, erhalten Sie möglicherweise eine Fehlermeldung für die Berechtigungen. In diesem Fall schließen Sie Visual Studio im Administratormodus öffnen, und versuchen Sie es erneut veröffentlichen.

   Wenn Visual Studio im Administratormodus, ist die **Ausgabe** Fenster Berichte erfolgreich erstellen und veröffentlichen.

   ![Output_window_publish_Test](deploying-to-iis/_static/image20.png)

   Wenn Sie die URL eingeben und die **Ziel-URL** Feld auf das Veröffentlichungsprofil **Verbindung** Registerkarte im Browser automatisch geöffnet wird, die Contoso University-Startseite in IIS auf Ihrem Computer ausgeführt.

## <a name="test-in-the-test-environment"></a>In der testumgebung zu testen

Beachten Sie, dass die Anzeige für die Umgebung "(Test)" angezeigt. anstelle von "(Entwicklung)," zeigt die, dass die *"Web.config"* Transformation für den Indikator für die Umgebung erfolgreich war.

Führen Sie die **Dozenten** Seite, um sicherzustellen, dass Code First die Datenbank mit Daten für "Instructor" mit Anfangsdaten gefüllt. Wenn Sie diese Seite auswählen, dauert möglicherweise einige Minuten, die geladen werden, da der Code First die Datenbank wird erstellt und führt dann die `Seed` Methode. (Es nicht, die ausgeführt werden, wenn Sie auf der Startseite waren, weil die Anwendung versucht nicht, Zugriff auf die Datenbank noch.)

Wählen Sie die **Schüler/Studenten** Tab, um sicherzustellen, dass die bereitgestellte Datenbank keine Schüler/Studenten verfügt.

Wählen Sie **hinzufügen Schüler/Studenten** aus der **Schüler/Studenten** Menü. Fügen Sie eine "Student" hinzu, und zeigen Sie den neuen Studenten in der **Schüler/Studenten** Seite. Hiermit wird überprüft, ob Sie erfolgreich in die Datenbank schreiben können.

Von der **Kurse** , wählen Sie im Menü **Update Gutschriften**. Die **Update Gutschriften** Seite sind Administratorberechtigungen erforderlich, sodass die **anmelden** angezeigt wird. Geben Sie Anmeldeinformationen für das Administratorkonto, dass Sie frühere ("Admin" und "Devpwd") erstellt. Die **Update Gutschriften** angezeigt wird. Dies stellt sicher, dass es sich bei das Administratorkonto an, dem Sie im vorherigen Tutorial erstellt in der testumgebung ordnungsgemäß bereitgestellt wurde.

Überprüfen Sie, ob ein *ELMAH* Ordner vorhanden ist, der *c:\inetpub\wwwroot\ContosoUniversity* nur der Platzhalterdatei in ihrem Ordner.

<a id="reviewingmigrations"></a>

## <a name="review-the-automatic-webconfig-changes-for-code-first-migrations"></a>Überprüfen Sie die automatische Web.config-Änderungen für Code First-Migrationen

Öffnen der *"Web.config"* -Datei in der bereitgestellten Anwendung auf *C:\inetpub\wwwroot\ContosoUniversity* und Sie sehen, in denen während des Bereitstellungsvorgangs Code First-Migrationen zu automatisch konfiguriert Aktualisieren Sie die Datenbank, auf die neueste Version.

![](deploying-to-iis/_static/image21.png)

Der Bereitstellungsprozess erstellt auch eine neue Verbindungszeichenfolge für die Code First-Migrationen verwenden ausschließlich für das Datenbankschema zu aktualisieren:

![Database_Publish-Verbindungszeichenfolge](deploying-to-iis/_static/image22.png)

Diese zusätzliche Verbindungszeichenfolge können Sie ein Benutzerkonto für Datenbank-Schema-Updates und ein anderes Benutzerkonto für den Datenzugriff für die Anwendung angeben. Sie könnten z. B. Zuweisen der **Db\_Besitzer** Rolle Code First-Migrationen und **Db\_Datareader** mit **Db\_Datawriter**Rollen der Anwendung. Dies ist ein gängiges Defense-in-Depth-Muster, das verhindert, dass potenziell bösartigen Code in der Anwendung das Datenbankschema zu ändern. (Zum Beispiel könnte dies in einen erfolgreichen SQL Injection-Angriff vorkommen.) In diesen Tutorials verwenden Sie dieses Muster nicht. Um dieses Muster in Ihrem Szenario zu implementieren, führen Sie diese Schritte aus:

1. In der **Webveröffentlichung** Assistenten unter den **Einstellungen** Registerkarte, geben Sie die Verbindungszeichenfolge, die ein Benutzer mit vollständigen Berechtigungen zum Aktualisieren Schema angibt. Deaktivieren der **verwenden Sie diese Verbindungszeichenfolge zur Laufzeit** Kontrollkästchen. In der bereitgestellten Web.config-Datei, dies ist die `DatabasePublish` Verbindungszeichenfolge.

2. Erstellen Sie eine Web.config-Datei-Transformation für die Verbindungszeichenfolge, die Sie die Anwendung zur Laufzeit verwenden soll.

## <a name="summary"></a>Zusammenfassung

Sie haben Ihre Anwendung in IIS auf Ihrem Entwicklungscomputer bereitgestellt und es getestet.

![Startseite im Test](deploying-to-iis/_static/image23.png)

Dies stellt sicher, dass während des Bereitstellungsvorgangs der Inhalt der Anwendung auf den richtigen Speicherort kopiert (mit Ausnahme der Dateien, die Sie nicht bereitstellen möchten) und auch, Web Deploy IIS ordnungsgemäß während der Bereitstellung konfiguriert. Führen Sie im nächsten Tutorial, einem weiteren Test, die eine Bereitstellungsaufgabe findet, die noch nicht geschehen: Festlegen von Berechtigungen für den Ordner für die *Elm ah* Ordner.

## <a name="more-information"></a>Weitere Informationen

Informationen zum Ausführen von IIS oder IIS Express in Visual Studio finden Sie unter den folgenden Ressourcen:

- [Übersicht über IIS Express](https://www.iis.net/learn/extensions/introduction-to-iis-express/iis-express-overview) auf der Website IIS.net.
- [Einführung in IIS Express](https://weblogs.asp.net/scottgu/archive/2010/06/28/introducing-iis-express.aspx) auf Scott Guthries Blog.
- [Webserver in Visual Studio für ASP.NET-Webprojekte](https://msdn.microsoft.com/library/58wxa9w5.aspx).
- [Unterschiede zwischen IIS und ASP.NET Development Server Core-](../../older-versions-getting-started/deploying-web-site-projects/core-differences-between-iis-and-the-asp-net-development-server-cs.md) auf der ASP.NET-Website.

Weitere Informationen darüber, welche Probleme auftreten kann, wenn Ihre Anwendung bei mittlerer Vertrauenswürdigkeit ausgeführt wird, finden Sie unter [Hosting von ASP.NET-Anwendungen in mittlere Vertrauensebene in](http://www.4guysfromrolla.com/articles/100307-1.aspx) auf die vier Guys von Rolla-Website.

> [!div class="step-by-step"]
> [Zurück](project-properties.md)
> [Weiter](setting-folder-permissions.md)
