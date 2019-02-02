---
uid: web-api/overview/security/external-authentication-services
title: Externe Authentifizierungsdienste mit ASP.NET-Web-API (c#) | Microsoft-Dokumentation
author: rmcmurray
description: Beschreibt die Verwendung von externen Authentifizierungsdienste in ASP.NET Web-API.
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 3bb8eb15-b518-44f5-a67d-a27e051aedc6
msc.legacyurl: /web-api/overview/security/external-authentication-services
msc.type: authoredcontent
ms.openlocfilehash: de9b64e6c582059ec66ab352f60773f50af7b1ff
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667855"
---
# <a name="external-authentication-services-with-aspnet-web-api-c"></a>Externe Authentifizierungsdienste mit ASP.NET-Web-API (c#)

Visual Studio 2017 und ASP.NET 4.7.2 Erweitern Sie die Sicherheitsoptionen für [Single Page Applications](../../../single-page-application/index.md) (SPA) und [Web-API-](../../index.md) Dienste zur Integration in externe Authentifizierung-Dienste, die mehrere enthalten OAuth-/OpenID und Dienste für soziale Authentifizierung: Microsoft-Konten, Twitter, Facebook und Google.  

### <a name="in-this-walkthrough"></a>In dieser exemplarischen Vorgehensweise

- [Verwenden externe Authentifizierungsdienste](#USING)
- [Erstellen der Beispiel-Webanwendung](#SAMPLE)
- [Facebook-Authentifizierung aktivieren](#FACEBOOK)
- [Aktivieren von Google-Authentifizierung](#GOOGLE)
- [Aktivieren der Microsoft-Authentifizierung](#MICROSOFT)
- [Twitter-Authentifizierung aktivieren](#TWITTER)
- [Weitere Informationen](#MOREINFO)

    - [Kombinieren von externen Authentifizierungsdienste](#COMBINE)
    - [Konfiguration von IIS Express, um einen vollständig qualifizierten Domänennamen verwenden](#FQDN)
    - [So erhalten Sie die Einstellungen Ihrer Anwendung für die Microsoft-Authentifizierung](#OBTAIN)
    - [Optional: Deaktivieren Sie die lokale Registrierung](#DISABLE)

### <a name="prerequisites"></a>Vorraussetzungen

Um den Beispielen in dieser exemplarischen Vorgehensweise folgen zu können, müssen Sie über Folgendes verfügen:

- Visual Studio 2017
- Eine Developer-Konto mit dem Anwendungs-ID und geheimen Schlüssel für eines der folgenden Dienste für soziale Authentifizierung:

  - Microsoft-Konten ([https://go.microsoft.com/fwlink/?LinkID=144070](https://go.microsoft.com/fwlink/?LinkID=144070))
  - Twitter ([https://dev.twitter.com/](https://dev.twitter.com/))
  - Facebook ([https://developers.facebook.com/](https://developers.facebook.com/))
  - Google ([https://developers.google.com/](https://developers.google.com))

<a id="USING"></a>
## <a name="using-external-authentication-services"></a>Verwenden externe Authentifizierungsdienste

Die Fülle der externen Authentifizierungsdienste für Entwickler Webhilfe Entwicklung reduzieren aktuell verfügbar sind, die Zeit, wenn neue Webanwendung erstellen. Web-Benutzer haben in der Regel mehrere vorhandene Konten für beliebte Web Services und Websites für soziale Medien, daher beim ein Web-Anwendung implementiert die Authentifizierung aus einem externen Webdienst oder die Website für social Media-Dienste, der Entwicklungszeit, das Speichern von würde aufgewendeten erstellen eine authentifizierungsimplementierung. Mit einem externen Authentication-Dienst speichert die Endbenutzer, erspart, um ein anderes Konto für Ihre Webanwendung zu erstellen und zu einem anderen Benutzernamen und ein Kennwort merken müssen.

In der Vergangenheit mussten Entwickler zwei Möglichkeiten: Erstellen Sie ihre eigene authentifizierungsimplementierung, oder erfahren Sie, wie einen externer Authentifizierungsdienst in ihre Anwendungen integrieren. Auf der untersten Ebene, das folgende Diagramm zeigt eine einfache Request-Ablauf für einen Benutzer-Agent (Webbrowser) an, der Informationen über eine Webanwendung anfordert, die zum Verwenden eines externen Diensts konfiguriert ist:

[![](external-authentication-services/_static/image2.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image1.png)

Die Benutzer-Agent (oder Web-Browser in diesem Beispiel) sendet eine Anforderung im obigen Diagramm um eine Webanwendung den Webbrowser an einen externen Dienst umgeleitet. Der Benutzer-Agent sendet die Anmeldeinformationen an den Dienst für die externe Authentifizierung und, wenn der Benutzer-Agent erfolgreich authentifiziert hat, leitet des Diensts für die externe Authentifizierung des Benutzer-Agents in der ursprünglichen Webanwendung mit einer Art token, die die Benutzer-Agent sendet an die Webanwendung. Die Webanwendung wird das Token verwenden, um sicherzustellen, dass der Benutzer-Agent wurde erfolgreich vom externen Authentication-Dienst authentifiziert, und die Webanwendung das Token verwenden kann, um weitere Informationen zu den Benutzer-Agent zu sammeln. Sobald die Anwendung wird verarbeitet, die Benutzer-Agent-Informationen, die Webanwendung gibt die entsprechende Antwort an den Benutzeragent basierend auf den autorisierungseinstellungen.

In diesem zweiten Beispiel der Benutzer-Agent handelt, mit der Webanwendung und externen autorisierungsserver und die Web-Anwendung führt weitere Kommunikation mit dem externe Autorisierung-Server, um weitere Informationen über den Benutzer abzurufen -Agent:

[![](external-authentication-services/_static/image4.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image3.png)

Visual Studio 2017 und ASP.NET 4.7.2 erleichtern Integration in externe Authentifizierungsdienste für Entwickler durch die integrierte Integration für die folgenden Authentifizierungsdienste:

- Facebook
- Google
- Microsoft-Accounts (Windows Live ID-Konten)
- Twitter

In die Beispielen in dieser exemplarischen Vorgehensweise werden veranschaulicht, konfigurieren Sie jeden der unterstützte externe Authentifizierungsdienste mit die neue ASP.NET Web Application-Vorlage, die bereitgestellt wird mit Visual Studio 2017.

> [!NOTE]
> Ggf. müssen Sie möglicherweise Ihren FQDN an den Einstellungen für den Dienst für die externe Authentifizierung hinzufügen. Diese Anforderung basiert auf sicherheitseinschränkungen für einige externe Authentifizierungsdienste erfordern den FQDN in den Anwendungseinstellungen Ihrer, mit dem FQDN übereinstimmen, die von den Clients verwendet wird. (Für diese Schritte variiert stark für jeden Dienst für die externe Authentifizierung, müssen Sie in der Dokumentation für jeden Dienst externe Authentifizierung, um festzustellen, ob dies erforderlich ist und wie Sie diese Einstellungen zu konfigurieren.) Wenn Sie müssen zum Konfigurieren von IIS Express zur Verwendung eines FQDN für das Testen dieser Umgebung finden die [Konfigurieren von IIS Express verwenden einen vollständig qualifizierten Domänennamen](#FQDN) weiter unten in dieser exemplarischen Vorgehensweise.


<a id="SAMPLE"></a>
## <a name="create-a-sample-web-application"></a>Erstellen einer Beispiel-Webanwendung

Die folgenden Schritte führt Sie durch das Erstellen einer Beispiel-Anwendung mithilfe der ASP.NET Web Application-Vorlage, und Sie diese beispielanwendung für jeden der Dienste externe Authentifizierung weiter unten in dieser exemplarischen Vorgehensweise verwenden.

Starten Sie Visual Studio 2017, und wählen Sie **neues Projekt** von der Startseite. Alternativ wählen Sie in der **Datei** , wählen Sie im Menü **neu** und dann **Projekt**.

<!-- [![](external-authentication-services/_static/image6.png "Click to Expand the Image")](external-authentication-services/_static/image5.png) -->

Wenn die **neues Projekt** Dialogfeld angezeigt wird, wählen **installiert** und erweitern Sie **Visual C#** . Klicken Sie unter **Visual C#-** Option **Web**. Wählen Sie in der Liste der Projektvorlagen das Projekt **ASP.NET-Webanwendung (.Net Framework)**. Geben Sie einen Namen für Ihr Projekt, und klicken Sie auf **OK**.

[![](external-authentication-services/_static/image71.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image71.png)

Wenn die **neues ASP.NET-Projekt** angezeigt, wählen die **Single Page Application** Vorlage, und klicken Sie auf **Erstellen eines Projekts**.

[![](external-authentication-services/_static/image72.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image72.png)

Warten, wie Visual Studio 2017 wird das Projekt erstellt.

<!-- [![](external-authentication-services/_static/image12.png "Click to Expand the Image")](external-authentication-services/_static/image11.png) -->

Wenn Sie Visual Studio 2017 Erstellen Ihres Projekts abgeschlossen ist, öffnen Sie die *Startup.Auth.cs* -Datei auf dem ist die **App\_starten** Ordner.

Wenn Sie zuerst das Projekt erstellen, sind keine externen Authentifizierungsdienste aktiviert *Startup.Auth.cs* file; im folgenden wird veranschaulicht, was Ihr Code aussehen kann, mit der Position, an hervorgehobenen Abschnitten würden Sie aktivieren ein externer Authentifizierungsdienst und alle relevanten Einstellungen, um die Microsoft-Accounts, Twitter, Facebook oder Google-Authentifizierung für Ihre ASP.NET-Anwendung zu verwenden:

[!code-csharp[Main](external-authentication-services/samples/sample1.cs)]

Wenn Sie F5 drücken, um das Erstellen und Debuggen Ihre Web-Anwendung, wird einen Anmeldebildschirm angezeigt, in denen sehen Sie, dass keine externen Authentifizierungsdienste definiert wurden.

[![](external-authentication-services/_static/image73.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image73.png)

In den folgenden Abschnitten erfahren Sie, wie die externe Authentifizierungsdienste aktivieren, die mit ASP.NET in Visual Studio 2017 bereitgestellt werden.

<a id="FACEBOOK"></a>
## <a name="enabling-facebook-authentication"></a>Facebook-Authentifizierung aktivieren

Verwendung von Facebook Authentifizierung müssen Sie ein Facebook-Entwickler-Konto zu erstellen, und Ihr Projekt benötigen eine Anwendungs-ID und den geheimen Clientschlüssel aus Facebook um zu funktionieren. Weitere Informationen zu einer Facebook-Entwickler-Konto erstellen und Abrufen von Anwendungs-ID und geheimen Schlüssel, finden Sie unter [ https://go.microsoft.com/fwlink/?LinkID=252166 ](https://go.microsoft.com/fwlink/?LinkID=252166).

Einmal Anwendungs-ID und geheimen Schlüssel erhalten haben, verwenden die folgenden Schritte aus, um Facebook-Authentifizierung für Ihre Webanwendung zu aktivieren:

1. Wenn Sie Ihr Projekt in Visual Studio 2017 geöffnet ist, öffnen Sie die *Startup.Auth.cs* Datei.

2. Suchen Sie im Bereich der Facebook-Authentifizierung des Codes:

    [!code-csharp[Main](external-authentication-services/samples/sample2.cs)]
3. Entfernen Sie die &quot; // &quot; Zeichen, kommentieren Sie die hervorgehobenen Codezeilen, und dann Ihre Anwendungs-ID und geheimen Schlüssel hinzufügen. Nachdem Sie diese Parameter hinzugefügt haben, können Sie das Projekt neu kompilieren:

    [!code-csharp[Main](external-authentication-services/samples/sample3.cs)]
4. Beim Drücken von F5, um Ihre Webanwendung in Ihrem Webbrowser zu öffnen, sehen Sie sich, dass es sich bei Facebook als externer Authentifizierungsdienst definiert wurde:

    [![](external-authentication-services/_static/image74.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image74.png)
5. Beim Klicken auf die **Facebook** Schaltfläche, wird Ihr Browser an die Facebook-Anmeldeseite weitergeleitet werden:

    [![](external-authentication-services/_static/image22.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image21.png)
6. Nachdem Sie Ihre Facebook-Anmeldeinformationen eingeben, und klicken Sie auf **melden Sie sich bei**, Webbrowser gelangen Sie zurück an Ihre Webanwendung zu der Sie aufgefordert werden die **Benutzernamen** , die Sie zuordnen möchten Ihre Facebook-Konto:

    [![](external-authentication-services/_static/image24.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image23.png)
7. Nachdem Sie Ihren Benutzernamen eingegeben haben, und auf die **registrieren** Schaltfläche Ihre Webanwendung wird standardmäßig angezeigt **auf der Startseite** für Ihr Facebook-Konto:

    [![](external-authentication-services/_static/image26.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image25.png)

<a id="GOOGLE"></a>
## <a name="enabling-google-authentication"></a>Aktivieren von Google-Authentifizierung

Mithilfe von Google Authentication müssen Sie ein Google Developer-Konto zu erstellen, und Ihr Projekt benötigen eine Anwendungs-ID und geheimen Schlüssel von Google um zu funktionieren. Weitere Informationen über ein Google Developer-Konto erstellen und Abrufen von Anwendungs-ID und geheimen Schlüssel, finden Sie unter [ https://developers.google.com ](https://developers.google.com).


Verwenden Sie zum Aktivieren der Google-Authentifizierung für Ihre Webanwendung die folgenden Schritte aus:

1. Wenn Sie Ihr Projekt in Visual Studio 2017 geöffnet ist, öffnen Sie die *Startup.Auth.cs* Datei.

2. Suchen Sie im Bereich der Google-Authentifizierung des Codes:

    [!code-csharp[Main](external-authentication-services/samples/sample4.cs)]
3. Entfernen Sie die &quot; // &quot; Zeichen, kommentieren Sie die hervorgehobenen Codezeilen, und dann Ihre Anwendungs-ID und geheimen Schlüssel hinzufügen. Nachdem Sie diese Parameter hinzugefügt haben, können Sie das Projekt neu kompilieren:

    [!code-csharp[Main](external-authentication-services/samples/sample5.cs)]
4. Beim Drücken von F5, um Ihre Webanwendung in Ihrem Webbrowser zu öffnen, sehen Sie sich, dass die Google als externer Authentifizierungsdienst definiert wurde:

    [![](external-authentication-services/_static/image75.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image75.png)
5. Beim Klicken auf die **Google** Schaltfläche Ihr Browser wird auf die Google-Anmeldeseite weitergeleitet werden:

    [![](external-authentication-services/_static/image32.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image31.png)
6. Nachdem Sie Ihre Google-Anmeldeinformationen eingeben, und klicken Sie auf **Anmeldung**, Google werden Sie aufgefordert, stellen Sie sicher, dass die Webanwendung Berechtigungen zum Zugriff auf Ihr Google-Konto verfügt:

    [![](external-authentication-services/_static/image34.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image33.png)
7. Beim Klicken auf **Accept**, Webbrowser gelangen Sie zurück an Ihre Webanwendung zu der Sie aufgefordert werden die **Benutzernamen** , die mit Ihrem Google-Konto zugeordnet werden soll:

    [![](external-authentication-services/_static/image36.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image35.png)
8. Nachdem Sie Ihren Benutzernamen eingegeben haben, und auf die **registrieren** Schaltfläche Ihre Webanwendung wird standardmäßig angezeigt **auf der Startseite** für Ihr Google-Konto:

    [![](external-authentication-services/_static/image38.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image37.png)

<a id="MICROSOFT"></a>
## <a name="enabling-microsoft-authentication"></a>Aktivieren der Microsoft-Authentifizierung

Microsoft-Authentifizierung müssen Sie ein Developer-Konto zu erstellen, und erfordert eine Client-ID und geheimer Clientschlüssel um zu funktionieren. Weitere Informationen zu Microsoft Developer-Konto erstellen und Abrufen von Ihrem Client-ID und den geheimen Clientschlüssel, finden Sie unter [ https://go.microsoft.com/fwlink/?LinkID=144070 ](https://go.microsoft.com/fwlink/?LinkID=144070).

Nachdem Sie Ihre Consumer Key und consumergeheimnis abgerufen haben, verwenden die folgenden Schritte aus, um Microsoft-Authentifizierung für Ihre Webanwendung zu aktivieren:

1. Wenn Sie Ihr Projekt in Visual Studio 2017 geöffnet ist, öffnen Sie die *Startup.Auth.cs* Datei.

2. Suchen Sie den Microsoft-Authentifizierung-Abschnitt des Codes:

    [!code-csharp[Main](external-authentication-services/samples/sample6.cs)]
3. Entfernen Sie die &quot; // &quot; Zeichen, kommentieren Sie die hervorgehobenen Codezeilen, und dann Ihre Client-ID und geheimer Clientschlüssel hinzufügen. Nachdem Sie diese Parameter hinzugefügt haben, können Sie das Projekt neu kompilieren:

    [!code-csharp[Main](external-authentication-services/samples/sample7.cs)]
4. Beim Drücken von F5, um Ihre Webanwendung in Ihrem Webbrowser zu öffnen, sehen Sie sich, dass es sich bei Microsoft als externer Authentifizierungsdienst definiert wurde:

    [![](external-authentication-services/_static/image42.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image41.png)
5. Beim Klicken auf die **Microsoft** Schaltfläche Ihr Browser wird auf die Microsoft-Anmeldeseite weitergeleitet werden:

    [![](external-authentication-services/_static/image44.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image43.png)
6. Nachdem Sie Ihre Microsoft-Anmeldeinformationen eingeben und auf **Anmeldung**, werden Sie aufgefordert, stellen Sie sicher, dass die Webanwendung Berechtigungen zum Zugriff auf Ihr Microsoft-Konto verfügt:

    [![](external-authentication-services/_static/image46.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image45.png)
7. Beim Klicken auf **Ja**, Webbrowser gelangen Sie zurück an Ihre Webanwendung zu der Sie aufgefordert werden die **Benutzernamen** , die mit Ihrem Microsoft-Konto zugeordnet werden soll:

    [![](external-authentication-services/_static/image48.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image47.png)
8. Nachdem Sie Ihren Benutzernamen eingegeben haben, und auf die **registrieren** Schaltfläche Ihre Webanwendung wird standardmäßig angezeigt **auf der Startseite** für Ihr Microsoft-Konto:

    [![](external-authentication-services/_static/image50.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image49.png)

<a id="TWITTER"></a>
## <a name="enabling-twitter-authentication"></a>Twitter-Authentifizierung aktivieren

Twitter-Authentifizierung müssen Sie ein Developer-Konto zu erstellen und erfordert ein Consumer Key und consumergeheimnis um zu funktionieren. Informationen zum Erstellen einer Twitter-Entwicklerkonto und Abrufen Ihrer Consumer Key und consumergeheimnis, finden Sie unter [ https://go.microsoft.com/fwlink/?LinkID=252166 ](https://go.microsoft.com/fwlink/?LinkID=252166).

Nachdem Sie Ihre Consumer Key und consumergeheimnis abgerufen haben, verwenden die folgenden Schritte aus, um die Twitter-Authentifizierung für Ihre Webanwendung zu aktivieren:

1. Wenn Sie Ihr Projekt in Visual Studio 2017 geöffnet ist, öffnen Sie die *Startup.Auth.cs* Datei.

2. Suchen Sie im Bereich der Twitter-Authentifizierung des Codes:

    [!code-csharp[Main](external-authentication-services/samples/sample8.cs)]
3. Entfernen Sie die &quot; // &quot; Zeichen kommentieren die hervorgehobenen Zeilen des Codes, und fügen Sie dann Ihre Consumer Key und consumergeheimnis hinzu. Nachdem Sie diese Parameter hinzugefügt haben, können Sie das Projekt neu kompilieren:

    [!code-csharp[Main](external-authentication-services/samples/sample9.cs)]
4. Beim Drücken von F5, um Ihre Webanwendung in Ihrem Webbrowser zu öffnen, sehen Sie sich, dass es sich bei Twitter als externer Authentifizierungsdienst definiert wurde:

    [![](external-authentication-services/_static/image54.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image53.png)
5. Beim Klicken auf die **Twitter** Schaltfläche Ihr Browser wird auf die Twitter-Anmeldeseite weitergeleitet werden:

    [![](external-authentication-services/_static/image56.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image55.png)
6. Nachdem Sie Ihre Twitter-Anmeldeinformationen eingeben, und klicken Sie auf **app autorisieren**, Webbrowser gelangen Sie zurück zu Ihrer Webanwendung, die zur Eingabe wird die **Benutzernamen** , die Sie zuordnen möchten Ihr Twitter-Konto:

    [![](external-authentication-services/_static/image58.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image57.png)
7. Nachdem Sie Ihren Benutzernamen eingegeben haben, und auf die **registrieren** Schaltfläche Ihre Webanwendung wird standardmäßig angezeigt **auf der Startseite** für Ihr Twitter-Konto:

    [![](external-authentication-services/_static/image60.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image59.png)

<a id="MOREINFO"></a>
## <a name="additional-information"></a>Zusätzliche Informationen

Weitere Informationen zum Erstellen von Anwendungen, die OAuth und OpenID verwenden, können, finden Sie in den folgenden URLs:

- [https://go.microsoft.com/fwlink/?LinkID=252166](https://go.microsoft.com/fwlink/?LinkID=252166)
- [https://go.microsoft.com/fwlink/?LinkID=243995](https://go.microsoft.com/fwlink/?LinkID=243995)

<a id="COMBINE"></a>
### <a name="combining-external-authentication-services"></a>Kombinieren von externen Authentifizierungsdienste

Flexibler Sie können mehrere externe Authentifizierungsdienste definieren, zur gleichen Zeit, – dies ermöglicht Ihrer Web der Anwendung verwenden Sie ein Konto aus der externen Authentifizierungsdienste aktiviert:

[![](external-authentication-services/_static/image62.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image61.png)

<a id="FQDN"></a>
### <a name="configure-iis-express-to-use-a-fully-qualified-domain-name"></a>Konfigurieren von IIS Express, um einen vollständig qualifizierten Domänennamen zu verwenden.

Einige externe Authentifizierungsanbieter unterstützen nicht das Testen Ihrer Anwendung mithilfe einer HTTP-Adresse wie `http://localhost:port/`. Um dieses Problem zu umgehen, können Sie eine statische Zuordnung für den vollständig qualifizierten Domänennamen (FQDN) der HOSTS-Datei hinzufügen und konfigurieren Ihren Projektoptionen in Visual Studio 2017 auf den vollqualifizierten Domänennamen für das Testen/Debuggen verwenden. Zu diesem Zweck verwenden Sie die folgenden Schritte aus:

- Fügen Sie einen statischen FQDN Zuordnen der Datei "HOSTS" hinzu:

  1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten in Windows.
  2. Geben Sie folgenden Befehl ein:

      <kbd>notepad %WinDir%\system32\drivers\etc\hosts</kbd>
  3. Fügen Sie der Hostdatei einen Eintrag wie folgt hinzu:

      <kbd>127.0.0.1 www.wingtiptoys.com</kbd>
  4. Speichern Sie und schließen Sie die Datei "HOSTS".

- Konfigurieren Sie Visual Studio-Projekt zum Verwenden des FQDN:

  1. Wenn Ihr Projekt in Visual Studio 2017 geöffnet ist, klicken Sie auf die **Projekt** Menü, und wählen Sie dann den Eigenschaften Ihres Projekts. Sie können beispielsweise auswählen **WebApplication1 Eigenschaften**.
  2. Wählen Sie die **Web** Registerkarte.
  3. Geben Sie Ihren FQDN für den <strong>Projekt Url</strong>. Geben Sie z. B. <kbd> <http://www.wingtiptoys.com> </kbd> war, die die FQDN-Zuordnung, die Datei "HOSTS" hinzugefügt.

- Konfigurieren von IIS Express, um den vollqualifizierten Domänennamen für Ihre Anwendung verwenden:

    1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten in Windows.
    2. Geben Sie den folgenden Befehl so ändern Sie in Ihrem Ordner IIS Express:

        <kbd>cd /d &quot;%ProgramFiles%\IIS Express&quot;</kbd>
    3. Geben Sie den folgenden Befehl aus, um den vollqualifizierten Domänennamen für Ihre Anwendung hinzuzufügen:

        <kbd>appcmd.exe set config -section:system.applicationHost/sites /+&quot;[name='WebApplication1'].bindings.[protocol='http',bindingInformation='*:80:www.wingtiptoys.com']&quot; /commit:apphost</kbd>

  Wo **WebApplication1** ist der Name des Projekts und **BindingInformation** enthält die Portnummer und den vollqualifizierten Domänennamen, den Sie für Ihre Tests verwenden möchten.

<a id="OBTAIN"></a>
### <a name="how-to-obtain-your-application-settings-for-microsoft-authentication"></a>So erhalten Sie die Einstellungen Ihrer Anwendung für die Microsoft-Authentifizierung

Verknüpfen eine Anwendung in Windows Live für Microsoft Authentication ist ein einfacher Prozess. Wenn Sie nicht bereits eine Anwendung in Windows Live verknüpft haben, können Sie die folgenden Schritte aus:

1. Navigieren Sie zu [ https://go.microsoft.com/fwlink/?LinkID=144070 ](https://go.microsoft.com/fwlink/?LinkID=144070) Geben Sie Ihren Microsoft-Kontonamen und das Kennwort ein, wenn Sie dazu aufgefordert werden, und klicken Sie auf **Anmeldung**:

   <!--  [![](external-authentication-services/_static/image64.png "Click to Expand the Image")](external-authentication-services/_static/image63.png) -->
2. Wählen Sie **fügen Sie eine app** und geben Sie den Namen Ihrer Anwendung, wenn Sie aufgefordert werden, und klicken Sie dann auf **erstellen**:

    [![](external-authentication-services/_static/image79.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image79.png)
3. Wählen Sie Ihre app unter **Namen** und die Seite mit den Anwendungseigenschaften wird angezeigt.

4. Geben Sie die umleitungsdomäne für Ihre Anwendung ein. Kopieren der **Anwendungs-ID** und klicken Sie unter **Anwendungsgeheimnisse**Option **Kennwort generieren**. Kopieren Sie das Kennwort, das angezeigt wird. Die Anwendungs-ID und Kennwort sind Ihre Client-ID und den geheimen Clientschlüssel. Wählen Sie **Ok** und dann **speichern**.

    [![](external-authentication-services/_static/image77.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image77.png)

<a id="DISABLE"></a>
### <a name="optional-disable-local-registration"></a>Optional: Deaktivieren Sie die lokale Registrierung

Die aktuelle lokale Registrierung-Funktionalität von ASP.NET wird nicht verhindert, dass automatisierten Programmen (Bots) Erstellen von Konten für den Member; z. B. durch Einsatz einer Technologie zum Verhindern von Bots und Überprüfung wie [CAPTCHA](../../../web-pages/overview/security/16-adding-security-and-membership.md). Aus diesem Grund sollten Sie die lokale Anmeldung Form und die Registrierung einen Link auf der Anmeldeseite entfernen. Öffnen Sie hierzu die  *\_Login.cshtml* Seite in Ihrem Projekt, und klicken Sie dann auf die Zeilen für den Bereich für die lokale Anmeldung und den Registrierungslink auskommentieren. Die entsprechende Seite sollte wie im folgenden Codebeispiel aussehen:

[!code-html[Main](external-authentication-services/samples/sample10.html)]

Nachdem der Bereich für die lokale Anmeldung und den Registrierungslink deaktiviert wurden, zeigt die Anmeldeseite nur die externe Authentifizierungsanbieter, die Sie aktiviert haben:

[![](external-authentication-services/_static/image70.png "Klicken Sie auf, um das Bild zu erweitern.")](external-authentication-services/_static/image69.png)
