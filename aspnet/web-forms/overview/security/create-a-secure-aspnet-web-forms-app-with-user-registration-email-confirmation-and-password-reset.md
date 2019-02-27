---
uid: web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
title: Erstellen eine sichere ASP.NET Web Forms-app mit benutzerregistrierung, e-Mail-Bestätigung und kennwortzurücksetzung (c#) | Microsoft-Dokumentation
author: Erikre
description: In diesem Tutorial erfahren Sie, wie Sie eine ASP.NET Web Forms-app mit benutzerregistrierung, e-Mail-Bestätigung und kennwortzurücksetzung mithilfe von der ASP.NET Identity-Element zu erstellen...
ms.author: riande
ms.date: 10/02/2014
ms.assetid: 0a8d6044-5fab-4213-82d6-5618d5601358
msc.legacyurl: /web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
msc.type: authoredcontent
ms.openlocfilehash: acc13776840408756901e20589b9efacc83ff2a9
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833682"
---
<a name="create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset-c"></a>Erstellen einer sicheren ASP.NET Web Forms-App mit Benutzerregistrierung, E-Mail-Bestätigung und Kennwortzurücksetzung (C#)
====================
by [Erik Reitan](https://github.com/Erikre)

> In diesem Tutorial erfahren Sie, wie Sie eine ASP.NET Web Forms-app mit benutzerregistrierung, e-Mail-Bestätigung und kennwortzurücksetzung mithilfe von ASP.NET Identity-Mitgliedschaftssystem zu erstellen. In diesem Tutorial basiert auf andersons [MVC-Tutorial](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md).


## <a name="introduction"></a>Einführung

Dieses Tutorial führt Sie durch die erforderlichen Schritte zum Erstellen einer ASP.NET Web Forms-Anwendung mithilfe von Visual Studio und ASP.NET 4.5 zum Erstellen einer sicheren Web Forms-app mit benutzerregistrierung, e-Mail-Bestätigung und kennwortzurücksetzung.

### <a name="tutorial-tasks-and-information"></a>Tutorial Aufgaben und Informationen:

- [Erstellen einer ASP.NET Web Forms-app](#createWebForms)
- [Verknüpfen mit SendGrid](#SG)
- [E-Mail-Bestätigung vor der Anmeldung verlangen](#require)
- [Das Wiederherstellen und Zurücksetzen](#reset)
- [Bestätigungslink erneut-e-Mail](#rsend)
- [Problembehandlung bei der App](#dbg)
- [Additional Resources](#addRes) (Zusätzliche MSBuild-Ressourcen)

<a id="createWebForms"></a>
## <a name="create-an-aspnet-web-forms-app"></a>Erstellen einer ASP.NET Web Forms-App

Zunächst installieren und Ausführen von [Visual Studio Express 2013 für Web](https://go.microsoft.com/fwlink/?LinkId=299058) oder [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566). Installieren Sie [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) oder höher sowie.

> [!NOTE]
> Warnung: Sie müssen installieren [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) oder höher, um dieses Lernprogramm abzuschließen.


1. Erstellen eines neuen Projekts (**Datei**  - &gt; **neues Projekt**), und wählen Sie die **ASP.NET-Webanwendung** Vorlage und die neueste .NET Framework Version aus der **neues Projekt** Dialogfeld.
2. Von der **neues ASP.NET-Projekt** wählen Sie im Dialogfeld die **Web Forms** Vorlage. Lassen Sie die Standardauthentifizierung als **einzelne Benutzerkonten**. Wenn Sie die app in Azure hosten möchten, lassen Sie die **in der Cloud hosten** Kontrollkästchen aktiviert.   
 Klicken Sie auf **OK** zum Erstellen eines neuen Projekts.  
    ![Neues ASP.NET-Projekt (Dialogfeld)](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image1.png)
3. Aktivieren Sie Secure Sockets Layer (SSL) für das Projekt ein. Führen Sie die Schritte zur Verfügung, in der **SSL aktivieren, für das Projekt** Teil der [erste Schritte mit Web Forms-tutorialreihe](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms).
4. Die app auszuführen, klicken Sie auf die **registrieren** verknüpfen und einen neuen Benutzer registrieren. An diesem Punkt die einzige Validierung für die e-Mail-Adresse hängt von der [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) Attribut, um sicherzustellen, dass die e-Mail-Adresse ordnungsgemäß formatiert ist. Ändern Sie den Code zum Hinzufügen von e-Mail-Bestätigung. Schließen Sie das Browserfenster.
5. In **Server-Explorer** von Visual Studio (**Ansicht**  - &gt; **Server-Explorer**), navigieren Sie zu **Daten Connections\ DefaultConnection\Tables\AspNetUsers**, mit der rechten Maustaste, und wählen Sie **Tabellendefinition öffnen**. 

    Die folgende Abbildung zeigt die `AspNetUsers` Tabellenschema:

    ![Tabelle "aspnetusers"-schema](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image2.png)
6. In **Server-Explorer**, mit der rechten Maustaste auf die **"aspnetusers"** Tabelle, und wählen Sie **Tabellendaten anzeigen**.  
  
    ![Tabellendaten "aspnetusers"](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image3.png)  
 An diesem Punkt wurde der registrierte Benutzer die e-Mail-Adresse nicht bestätigt.
7. Klicken Sie auf die Zeile, und wählen Sie zu löschen des Benutzers. Sie fügen Sie diese e-Mail erneut im nächsten Schritt hinzu und senden eine bestätigungsmeldung an die e-Mail-Adresse.

## <a name="email-confirmation"></a>E-Mail-Bestätigung

Es hat sich bewährt, bestätigen die e-Mail-Adresse während der Registrierung eines neuen Benutzers ein, um zu überprüfen, ob sie keine Identität einer anderen Person (d. h. sie noch nicht registriert mit einer anderen Person für den e-Mail-Adresse). Angenommen, Sie ein Diskussionsforum hatten, Sie möchten zu verhindern, dass `"bob@cpandl.com"` aus der Registrierung als `"joe@contoso.com"`. Ohne e-Mail-Bestätigung `"joe@contoso.com"` unerwünschte e-Mail-Adresse Ihrer app abrufen konnte. Angenommen, das Bob versehentlich als registriert `"bib@cpandl.com"` und noch nicht bemerkt, er wäre kennwortwiederherstellung zu verwenden, da die app nicht seine richtige e-Mail-Nachricht nicht. E-Mail-Bestätigung enthält nur begrenzt Schutz von Bots und nicht bieten Schutz vor bestimmt Spammern.

Im Allgemeinen möchten Sie verhindern, dass neue Benutzer keine Daten zu Ihrer Website veröffentlichen, bevor sie von beiden e-Mail-Adresse, eine SMS oder einen anderen Mechanismus bestätigt wurden. In den folgenden Abschnitten werden wir Aktivieren von e-Mail-Bestätigung und ändern Sie den Code, um zu verhindern, dass die neu registrierte Benutzer anmelden, bis ihre e-Mail-Adresse bestätigt wurde. In diesem Tutorial verwenden Sie den e-Mail-Dienst SendGrid.

<a id="SG"></a>
## <a name="hook-up-sendgrid"></a>Verknüpfen mit SendGrid

SendGrid wurde dessen-API-geändert werden, da in diesem Tutorial geschrieben wurde. Aktuelle SendGrid-Anweisungen, finden Sie unter [SendGrid](http://sendgrid.com/) oder [Konto Bestätigung und -Wiederherstellung aktivieren](xref:security/authentication/accconfirm#enable-account-confirmation-and-password-recovery).

Obwohl dieses Tutorial nur das zeigt Hinzufügen von e-Mail-Benachrichtigung über [SendGrid](http://sendgrid.com/), senden Sie eine e-Mail mit SMTP und andere Mechanismen (finden Sie unter [Zusatzressourcen](#addRes)).

1. Öffnen Sie in Visual Studio die **-Paket-Manager-Konsole** (**Tools**  - &gt; **NuGet-Paket-Manager**  - &gt; **-Paket-Manager-Konsole**), und geben Sie den folgenden Befehl aus:  
    `Install-Package SendGrid`
2. Wechseln Sie zu der [Azure SendGrid-Registrierungsseite](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/) und kostenloses SendGrid-Konto zu registrieren. Sie können auch registrieren Sie sich für ein kostenloses SendGrid-Konto direkt auf [SendGrid Website](http://www.sendgrid.com).
3. Aus **Projektmappen-Explorer** öffnen Sie die *IdentityConfig.cs* Datei die *App\_starten* Ordner, und fügen Sie folgenden Code in Gelb zu den hervorgehoben`EmailService` Klasse so konfigurieren Sie **SendGrid**:

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample1.cs?highlight=3,5,8-37)]
4. Darüber hinaus fügen Sie die folgenden `using` Anweisungen am Anfang der *IdentityConfig.cs* Datei: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample2.cs?highlight=1-4)]
5. Um dieses Beispiel einfach zu halten, speichern Sie die e-Mail-Dienst-Konto-Werte in der `appSettings` Teil der *"Web.config"* Datei. Fügen Sie die folgenden XML-Code in Gelb zu markiert die *"Web.config"* Datei im Stammverzeichnis des Projekts:

    [!code-xml[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample3.xml?highlight=2-5)]

    > [!WARNING]
    > Sicherheit: vertrauliche Daten niemals in Ihrem Quellcode speichern. In diesem Beispiel ist das Konto und die Anmeldeinformationen werden gespeichert der **AppSetting** Teil der *"Web.config"* Datei. In Azure können sicher gespeichert werden diese Werte auf die **[konfigurieren](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** Registerkarte im Azure-Portal. Weitere Informationen finden Sie im Thema andersons [bewährte Methoden für die Bereitstellung von Kennwörtern und anderen vertraulichen Daten in ASP.NET und Azure](https://go.microsoft.com/fwlink/?LinkId=513141).
6. Fügen Sie die e-Mail-Dienst-Werte entsprechend, dass Ihre Werte der SendGrid-Authentifizierung (Benutzername und Kennwort), damit Sie erfolgreich können e-Mail-Adresse aus Ihrer app senden. Achten Sie darauf, dass Ihr SendGrid-Kontoname anstelle der e-Mail-Adresse, die Sie angegeben, dass SendGrid verwenden.

### <a name="enable-email-confirmation"></a>Aktivieren von e-Mail-Bestätigung

 Um e-Mail-Bestätigung zu aktivieren, ändern Sie den Registrierungscode, verwenden die folgenden Schritte aus.  
 

1. In der *Konto* Ordner die *Register.aspx.cs* Code-Behind und aktualisieren Sie die `CreateUser_Click` Methode zum Aktivieren von den folgenden hervorgehobenen Änderungen vor: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample4.cs?highlight=9-11)]
2. In **Projektmappen-Explorer**, mit der rechten Maustaste *"default.aspx"* , und wählen Sie **als Startseite festlegen**.
3. Führen Sie die app durch Drücken von **F5.** Nachdem die Seite angezeigt wird, klicken Sie auf die **registrieren** Link, um die Seite "registrieren" anzuzeigen.
4. Geben Sie Ihre e-Mail-Adresse und Kennwort, und klicken Sie dann die **registrieren** Schaltfläche, um eine e-Mail über SendGrid zu senden.  
   Der aktuelle Zustand des Projekts und Code können die Benutzer anmelden, nachdem sie das Registrierungsformular abgeschlossen haben, obwohl sie ihr Konto bestätigt dies nicht getan haben.
5. Überprüfen Sie Ihre e-Mail-Konto, und klicken Sie auf den Link, um Ihre e-Mail-Adresse zu bestätigen.  
   Nachdem Sie das Registrierungsformular übermitteln, werden Sie angemeldet sein.  
    ![Beispielwebsite - Anmeldung](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image4.png)

<a id="require"></a>
## <a name="require-email-confirmation-before-log-in"></a>E-Mail-Bestätigung vor der Anmeldung verlangen

Obwohl Sie das e-Mail-Konto bestätigt haben, müssten an diesem Punkt Sie keinen, klicken auf die Links in der e-Mail zur Verifizierung vollständig angemeldet sein. Im folgenden Abschnitt ändern Sie den Code an, dass neue Benutzer, um eine bestätigte e-Mail-Adresse haben, bevor sie angemeldet sind (authentifiziert).

1. In **Projektmappen-Explorer** Aktualisieren von Visual Studio die `CreateUser_Click` Ereignis in der *Register.aspx.cs* CodeBehind in enthalten die *Konten* Ordner mit der folgenden hervorgehobenen Änderungen vor: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample5.cs?highlight=13-14,17-21)]
2. Update der `LogIn` -Methode in der die *Login.aspx.cs* Code-Behind, mit den folgenden hervorgehobenen Änderungen vor: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample6.cs?highlight=9-19,45-46)]

### <a name="run-the-application"></a>Ausführen der Anwendung

 Nun, da Sie den Code, um zu überprüfen, ob die e-Mail-Adresse des Benutzers bestätigt wurde implementiert haben, sehen Sie sich die Funktionalität sowohl die **registrieren** und **Anmeldung** Seiten. 

1. Löschen von Konten in der **"aspnetusers"** Tabelle, die den e-Mail-Alias enthalten, Sie testen möchten.
2. Führen Sie die app (**F5**) und stellen Sie sicher, Sie können nicht als ein Benutzer registrieren, wenn Sie Ihre e-Mail-Adresse bestätigt haben.
3. Bestätigen Ihr neue Konto über die e-Mail-Adresse, die gerade gesendet wurde, bevor versuchen Sie, sich mit dem neuen Konto anzumelden.  
 Sie sehen, dass Sie nicht anmelden können, und dass Sie ein bestätigte e-Mail-Konto verfügen müssen.
4. Nachdem Sie Ihre e-Mail-Adresse bestätigt haben, melden Sie sich an die app.

<a id="reset"></a>
## <a name="password-recovery-and-reset"></a>Das Wiederherstellen und Zurücksetzen

1. In Visual Studio, entfernen Sie die Kommentarzeichen aus der `Forgot` -Methode in der die *Forgot.aspx.cs* CodeBehind in enthalten die *Konto* Ordner, damit die Methode angezeigt wird, wie folgt: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample7.cs?highlight=16-18)]
2. Öffnen der *"Login.aspx"* Seite. Ersetzen Sie das Markup in der Nähe des der **LoginForm** im Abschnitt unten hervorgehoben: 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample8.aspx?highlight=52-53)]
3. Öffnen der *Login.aspx.cs* CodeBehind und kommentieren Sie die folgende Codezeile in Gelb aus markiert die `Page_Load` -Ereignishandler: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample9.cs?highlight=5)]
4. Führen Sie die app durch Drücken von **F5.** Nachdem die Seite angezeigt wird, klicken Sie auf die **melden Sie sich bei** Link.
5. Klicken Sie auf die **haben Ihr Kennwort vergessen?** Link zum Anzeigen der **Kennwort vergessen** Seite.
6. Geben Sie Ihre e-Mail-Adresse ein, und klicken Sie auf die **senden** Schaltfläche, um eine e-Mail an Ihre Adresse zu senden, denen Sie Ihr Kennwort zurücksetzen können.   
   Überprüfen Sie Ihre e-Mail-Konto, und klicken Sie auf den Link zum Anzeigen der **Kennwort zurücksetzen** Seite.
7. Auf der **Kennwort zurücksetzen** geben Ihre e-Mail-Adresse, Kennwort und bestätigte Kennwort. Drücken Sie dann die **zurücksetzen** Schaltfläche.  
   Wenn Sie Ihr Kennwort erfolgreich zurückgesetzt der **Kennwortänderung** Seite wird angezeigt. Jetzt können Sie sich mit Ihrem neuen Kennwort anmelden.

<a id="rsend"></a>
## <a name="resend-email-confirmation-link"></a>Bestätigungslink erneut-e-Mail

Sobald ein Benutzer ein neues lokales Konto erstellt wird, werden sie einen Link zur Bestätigung per e-Mail gesendet, die, den Sie für erforderlich sind, verwenden, bevor sie sich anmelden können. Wenn der Benutzer versehentlich die Bestätigungs-e-Mail löscht oder die e-Mail nicht ankommt, benötigen sie den Link zur Bestätigung erneut gesendet. Die folgenden codeänderungen zeigen, wie dies zu ermöglichen.

1. Öffnen Sie in Visual Studio die **Login.aspx.cs** Code-Behind "und" den folgenden Ereignishandler nach dem Hinzufügen der `LogIn` -Ereignishandler:   

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample10.cs)]
2. Ändern der `LogIn` -Ereignishandler in der *Login.aspx.cs* CodeBehind durch Ändern des Codes, die wie folgt in gelb hervorgehoben: 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample11.cs?highlight=15-17)]
3. Update der *"Login.aspx"* Seite durch Hinzufügen des Codes, die wie folgt in gelb hervorgehoben: 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample12.aspx?highlight=45-46)]
4. Löschen von Konten in der **"aspnetusers"** Tabelle, die den e-Mail-Alias enthalten, Sie testen möchten.
5. Führen Sie die app (**F5**), und registrieren Sie Ihre e-Mail-Adresse.
6. Bestätigen Ihr neue Konto über die e-Mail-Adresse, die gerade gesendet wurde, bevor versuchen Sie, sich mit dem neuen Konto anzumelden.  
   Sie sehen, dass Sie nicht anmelden können, und dass Sie ein bestätigte e-Mail-Konto verfügen müssen. Darüber hinaus können Sie jetzt eine bestätigungsmeldung an an Ihre e-Mail-Konto erneut senden.
7. Geben Sie Ihre e-Mail-Adresse und Kennwort, drücken Sie dann die **erneut senden der Bestätigung** Schaltfläche.
8. Nachdem Sie Ihre e-Mail-Adresse, die basierend auf die neu gesendete e-Mail-Nachricht bestätigt haben, melden Sie sich an die app.

<a id="dbg"></a>
## <a name="troubleshooting-the-app"></a>Problembehandlung bei der App

Wenn Sie eine e-Mail mit den Link, um Ihre Anmeldeinformationen überprüfen nicht erhalten:

- Überprüfen Sie Ihren Junk-e- bzw. Spam-Ordner.
- Melden Sie sich bei Ihrem SendGrid-Konto, und klicken Sie auf die [-e-Mail-Aktivitätslink](https://sendgrid.com/logs/index).
- Stellen Sie sicher, dass Sie Ihre SendGrid-Benutzernamen-Konto als verwendet eine *"Web.config"* Wert anstelle der e-Mail-Adresse für Ihr SendGrid-Konto.

<a id="addRes"></a>
## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Links zu ASP.NET Identity – empfohlene Ressourcen](../../../identity/overview/getting-started/aspnet-identity-recommended-resources.md)
- [Kontobestätigung und Kennwortwiederherstellung in ASP.NET Identity](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md)
- [ASP.NET Web Forms-tutorialreihe - Hinzufügen eines OAuth 2.0-Anbieters](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#OAuthWebForms)
- [Bereitstellen einer sicheren ASP.NET Web Forms-App mit Mitgliedschaft, OAuth und SQL-Datenbank in Azure App Service](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/)
- [ASP.NET Web Forms tutorialreihe - SSL für das Projekt aktivieren](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms)
