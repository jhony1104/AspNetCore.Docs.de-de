---
uid: identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
title: Kontobestätigung und Kennwortwiederherstellung in ASP.NET Identity (c#) | Microsoft-Dokumentation
author: HaoK
description: Vor der Durchführung dieses Tutorials sollten Sie zuerst erstellen Sie eine sichere ASP.NET MVC 5-Web-app mit Anmeldung, e-Mail-Bestätigung und kennwortzurücksetzung abschließen. In diesem Tutorial...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 8d54180d-f826-4df7-b503-7debf5ed9fb3
msc.legacyurl: /identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 47dc2c1044a5964624ba2f8af4f174a2fd99d3e8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236405"
---
# <a name="account-confirmation-and-password-recovery-with-aspnet-identity-c"></a>Bestätigung und Wiederherstellung mit ASP.NET Identity-Konto (C#)

> Vor der Durchführung dieses Tutorials sollten Sie zuerst abschließen [erstellen eine sichere ASP.NET MVC 5-Web-app mit Anmeldung, e-Mail-Bestätigung und kennwortzurücksetzung](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md). Dieses Tutorial enthält weitere Informationen und erfahren Sie, wie e-Mail zur Bestätigung des lokalen Konto einrichten und Benutzer zum Zurücksetzen des Kennworts in ASP.NET Identity vergessen haben.

Ein lokales Benutzerkonto muss der Benutzer ein Kennwort für das Konto zu erstellen, und das Kennwort (sicher) in der Web-app gespeichert ist. ASP.NET Identity unterstützt auch die Konten für soziale Netzwerke, die den Benutzer, erstellen ein Kennwort für die app nicht erforderlich. [Konten für soziale Netzwerke](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) ein Dritter (z. B. Google, Twitter, Facebook oder Microsoft) zum Authentifizieren von Benutzern verwenden. Dieses Thema behandelt Folgendes:

- [Erstellen einer ASP.NET MVC-app](#createMvc) und Untersuchen von ASP.NET Identity-Funktionen.
- [Erstellen Sie das Identity-Beispiel](#build)
- [Richten Sie e-Mail-Bestätigung](#email)

Neuer Benutzer registrieren ihre e-Mail-Alias, der ein lokales Konto erstellt.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image1.png)

Auswählen der Schaltfläche "registrieren" sendet eine Bestätigung per e-Mail, die eine Überprüfungstoken an ihre e-Mail-Adresse enthält.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image2.png)

Der Benutzer erhält eine e-Mail mit einem bestätigungstoken für ihr Konto.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image3.png)

Wählen Sie den Link klicken, wird das Konto bestätigt.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image4.png)

<a id="passwordReset"></a>

## <a name="password-recoveryreset"></a>Zurücksetzen des Kennworts/Wiederherstellung

Lokale Benutzer, die ihr Kennwort vergessen haben ein Sicherheitstoken gesendet, um ihre e-Mail-Konto, aktivieren sie zum Zurücksetzen des Kennworts.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image5.png)  
  
Der Benutzer wird in Kürze eine e-Mail mit einem Link, sodass sie zum Zurücksetzen des Kennworts erhalten.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image6.png)  
Wählen Sie den Link gelangen sie zu der Seite "Zurücksetzen".  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image7.png)  
  
Auswählen der **zurücksetzen** Schaltfläche wird bestätigt das Kennwort wurde zurückgesetzt.  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image8.png)

<a id="createMvc"></a>

## <a name="create-an-aspnet-web-app"></a>Erstellen einer ASP.NET-Web-App

Zunächst installieren und Ausführen von [Visual Studio 2017](https://visualstudio.microsoft.com/).


1. Erstellen Sie ein neues ASP.NET Web-Projekt, und wählen Sie die MVC-Vorlage. Web Forms unterstützen auch ASP.NET Identity, sodass Sie ähnliche Schritte in einer Web Forms-app ausführen konnte.
2. Ändern Sie die Authentifizierung auf **einzelne Benutzerkonten**.
3. Die app auszuführen, wählen Sie die **registrieren** verknüpfen, und registrieren Sie einen Benutzer. Die ausschließliche Überprüfung der auf die e-Mail-Adresse an diesem Punkt ist, mit der [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) Attribut.
4. Wechseln Sie in Server-Explorer zu **Daten Connections\DefaultConnection\Tables\AspNetUsers**mit der rechten Maustaste auf, und wählen Sie **Tabellendefinition öffnen**.

    Die folgende Abbildung zeigt die `AspNetUsers` Schema:

    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image9.png)
5. Mit der rechten Maustaste auf die **"aspnetusers"** Tabelle, und wählen Sie **Tabellendaten anzeigen**.  
  
    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image10.png)  
  
   Die e-Mail wurde an diesem Punkt nicht bestätigt.

Der Standard-Datenspeicher für ASP.NET Identity ist Entity Framework, aber Sie können konfigurieren, verwenden Sie andere Datenspeicher und zusätzliche Felder hinzufügen. Finden Sie unter [Zusatzressourcen](#addRes) Abschnitt am Ende dieses Tutorials.

Die [OWIN-Startklasse](../../../aspnet/overview/owin-and-katana/owin-startup-class-detection.md) ( *"Startup.cs"* ) wird aufgerufen, wenn die Anwendung gestartet und ruft die `ConfigureAuth` -Methode in der *App\_Start\Startup.Auth.cs*, die OWIN-Pipeline konfiguriert und initialisiert ASP.NET Identity. Untersuchen Sie die Methode `ConfigureAuth`. Jede `CreatePerOwinContext` Aufruf registriert einen Rückruf (gespeichert der `OwinContext`) aufgerufen wird einmal pro Anforderung zum Erstellen einer Instanz des angegebenen Typs. Sie können einen Haltepunkt festlegen, in den Konstruktor und `Create` Methode jedes Typs (`ApplicationDbContext, ApplicationUserManager`) und überprüfen Sie, ob sie bei jeder Anforderung aufgerufen werden. Eine Instanz der `ApplicationDbContext` und `ApplicationUserManager` befindet sich in der OWIN-Kontext, die in der gesamten Anwendung zugegriffen werden kann. ASP.NET Identity klinkt sich in der OWIN-Pipeline, über Cookie-Middleware. Weitere Informationen finden Sie unter [pro Anforderung Prozesslebensdauer-Verwaltung für UserManager-Klasse in ASP.NET Identity](https://blogs.msdn.com/b/webdev/archive/2014/02/12/per-request-lifetime-management-for-usermanager-class-in-asp-net-identity.aspx).

Wenn Sie Ihrem Sicherheitsprofil ändern, ein neuen sicherheitsstempel generiert und gespeichert, der `SecurityStamp` Feld der *"aspnetusers"* Tabelle. Beachten Sie, dass die `SecurityStamp` Feld unterscheidet sich das Sicherheitscookie. Das Sicherheitscookie befindet sich nicht in der `AspNetUsers` Tabelle (oder an anderer Stelle in der Identity-DB). Das Sicherheitstoken für das Cookie ist selbstsigniert mit [DPAPI](https://msdn.microsoft.com/library/system.security.cryptography.protecteddata.aspx) und wird erstellt, mit der `UserId, SecurityStamp` und Ablauf von Uhrzeitangaben.

Die Cookie-Middleware überprüft das Cookie bei jeder Anforderung. Die `SecurityStampValidator` -Methode in der die `Startup` Klasse erreicht die Datenbank und in regelmäßigen Abständen, überprüft der sicherheitsstempel gemäß der Angabe der `validateInterval`. Dies geschieht nur alle 30 Minuten (in unserem Beispiel), es sei denn, Sie zu Ihrem Sicherheitsprofil ändern. Das 30-Minuten-Intervall wurde gewählt, um Roundtrips zur Datenbank zu minimieren. Finden Sie unter meinem [Tutorial zur zweistufigen Authentifizierung](index.md) Weitere Details.

Pro die Kommentare im Code die `UseCookieAuthentication` -Methode unterstützt die Cookie-Authentifizierung. Die `SecurityStamp` Feld und den zugehörigen Code ist eine zusätzliche Sicherheitsschicht von Sicherheit für Ihre app, wenn Sie Ihr Kennwort ändern Sie protokolliert außerhalb des Browsers, die Sie sich angemeldet haben. Die `SecurityStampValidator.OnValidateIdentity` Methode ermöglicht die app, die das Sicherheitstoken zu überprüfen, wenn der Benutzer anmeldet, die verwendet wird, wenn Sie ein Kennwort ändern oder mithilfe der externe Anmeldung. Dies ist erforderlich, um sicherzustellen, dass alle Token (Cookies) generiert, mit dem alten Kennwort ungültig sind. Im Beispielprojekt, wenn Sie ändern das Benutzerkennwort klicken Sie dann ein neues Token für den Benutzer generiert wird, werden alle vorherigen Token für ungültig erklärt und die `SecurityStamp` Feld wird aktualisiert.

Die Identity-System können Sie zum Konfigurieren Ihrer app also wenn sich das Sicherheitsprofil Benutzer ändert (z. B. wenn der Benutzer das Kennwort oder die Änderungen ändert Anmeldung verknüpft (z. B. von Facebook, Google, Microsoft-Konto usw.), der Benutzer angemeldet ist, von allen Browserinstanzen. Z. B. die folgende Abbildung zeigt die [einmaliges Abmelden Beispiel](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt) -app, die dem Benutzer ermöglicht, alle Browserinstanzen (in diesem Fall IE, Firefox und Chrome) abmelden, indem Sie eine Schaltfläche auswählen. Alternativ können mit das Beispiel nur einen bestimmten Browser-Instanz anmelden.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image11.png)

Die [einmaliges Abmelden Beispiel](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt) -app zeigt, wie ASP.NET Identity das Token erneut generieren können. Dies ist erforderlich, um sicherzustellen, dass alle Token (Cookies) generiert, mit dem alten Kennwort ungültig sind. Diese Funktion bietet eine zusätzliche Sicherheitsebene für Ihre Anwendung; Wenn Sie Ihr Kennwort ändern, werden Sie abgemeldet, in dem Sie sich diese Anwendung angemeldet haben.

Die *App\_Start\IdentityConfig.cs* -Datei enthält die `ApplicationUserManager`, `EmailService` und `SmsService` Klassen. Die `EmailService` und `SmsService` Klassen implementieren jeweils die `IIdentityMessageService` Schnittstelle, damit Sie häufige Methoden in jeder Klasse zum Konfigurieren von e-Mail und SMS verwenden. Obwohl dieses Tutorial nur das zeigt Hinzufügen von e-Mail-Benachrichtigung über [SendGrid](http://sendgrid.com/), senden Sie eine e-Mail mit SMTP und andere Mechanismen.

Die `Startup` Klasse enthält auch eine Textvorlage zum Hinzufügen von Anmeldungen per sozialem Netzwerk (Facebook, Twitter usw.), finden Sie unter meinem Tutorial [MVC 5 App with Facebook, Twitter, LinkedIn und Google OAuth2 Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) für Weitere Informationen.

Überprüfen Sie die `ApplicationUserManager` -Klasse, die die Identitätsinformationen für den Benutzer enthält, und konfiguriert die folgenden Funktionen:

- Kennwort-sicherheitsanforderungen.
- Benutzer gesperrt (Versuche und -Uhrzeit).
- Zwei-Faktor-Authentifizierung (2FA). 2FA und SMS behandelt in ein anderes Tutorial.
- Einbinden von der e-Mail und SMS-Dienste. (Ich werde in ein anderes Tutorial SMS behandeln).

Die `ApplicationUserManager` Klasse leitet sich von der generischen `UserManager<ApplicationUser>` Klasse. `ApplicationUser` leitet sich von ["identityuser"](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityuser.aspx). `IdentityUser` abgeleitet von der generischen `IdentityUser` Klasse:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample1.cs)]

Die oben genannten Eigenschaften übereinstimmen, mit den Eigenschaften in der `AspNetUsers` Tabelle oben.

Generische Argumente auf `IUser` ermöglichen es Ihnen, eine Klasse, die Verwendung verschiedener Typen für den primären Schlüssel abgeleitet werden. Finden Sie unter den [ChangePK](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt) Beispiel zeigt, wie sich den Primärschlüssel von String in Int "oder" GUID zu ändern.

### <a name="applicationuser"></a>ApplicationUser

`ApplicationUser` (`public class ApplicationUserManager : UserManager<ApplicationUser>`) wird in definiert *Models\IdentityModels.cs* als:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample2.cs?highlight=8-9)]

Der hervorgehobene Code oben generiert eine ["ClaimsIdentity"](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx). ASP.NET Identity und OWIN Cookie-Authentifizierung sind Claims-basierte, daher das Framework erfordert die app zum Generieren einer `ClaimsIdentity` für den Benutzer. `ClaimsIdentity` enthält Informationen über alle Ansprüche für Benutzer, z. B. den Namen des Benutzers, Alter und welche Rollen der Benutzer angehört. Sie können auch weitere Ansprüche für den Benutzer in dieser Phase hinzufügen.

Die OWIN `AuthenticationManager.SignIn` Methode übergibt der `ClaimsIdentity` und der Benutzer anmeldet:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample3.cs?highlight=4-6)]

[MVC 5-App mit Facebook, Twitter, LinkedIn und Google OAuth2 Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) zeigt, wie Sie zusätzliche Eigenschaften zum Hinzufügen der `ApplicationUser` Klasse.

## <a name="email-confirmation"></a>E-Mail-Bestätigung

Es ist eine gute Idee, bestätigen die e-Mail-Adresse, um zu überprüfen, ob sie keine Identität einer anderen Person mit Registrierung neuer Benutzer (d. h. sie noch nicht registriert mit einer anderen Person für den e-Mail-Adresse). Angenommen, Sie ein Diskussionsforum hatten, Sie möchten zu verhindern, dass `"bob@example.com"` aus der Registrierung als `"joe@contoso.com"`. Ohne e-Mail-Bestätigung `"joe@contoso.com"` unerwünschte e-Mail-Adresse Ihrer app abrufen konnte. Angenommen, das Bob versehentlich als registriert `"bib@example.com"` und noch nicht bemerkt, er wäre Kennwort wiederherstellen zu verwenden, da die app nicht seine richtige e-Mail-Nachricht nicht. E-Mail-Bestätigung enthält nur begrenzt Schutz von Bots und nicht bieten Schutz vor bestimmt Spammern, sie haben viele funktionierenden e-Mail-Aliase, die sie verwenden können, um zu registrieren. Im folgenden Beispiel wird der Benutzer nicht ihr Kennwort zu ändern, bis ihr Konto bestätigt wurde (durch diese Auswahl von eines Links zur Bestätigung empfangen wird, auf das e-Mail-Konto an, die, dem Sie registriert.) Sie können diesem Workflow auf andere Szenarien anwenden sendet z. B. einen Link, um zu bestätigen und Zurücksetzen des Kennworts für neue Konten, die durch den Administrator, dem Benutzer eine e-Mail senden, wenn sie ihr Profil und so weiter geändert wurden erstellt. Im Allgemeinen möchten Sie verhindern, dass neue Benutzer keine Daten zu Ihrer Website veröffentlichen, bevor sie per e-Mail, eine SMS oder einen anderen Mechanismus bestätigt wurden. <a id="build"></a>

## <a name="build-a-more-complete-sample"></a>Erstellen Sie ein ausführlicheres Beispiel

In diesem Abschnitt werden Sie NuGet verwenden, um ein ausführlicheres Beispiel herunterzuladen, mit denen, dem wir zusammenarbeiten werden.

1. Erstellen Sie ein neues ***leere*** ASP.NET-Webprojekt.
2. Geben Sie in der Paket-Manager-Konsole die folgenden Befehle aus: 

    [!code-console[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample4.cmd)]

   In diesem Tutorial verwenden wir [SendGrid](http://sendgrid.com/) zum Senden von e-Mails. Die `Identity.Samples` Paket wird installiert, den wir arbeiten mit Code.
3. Legen Sie die [Projekt für die Verwendung von SSL](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md).
4. Testen Sie die Erstellung von lokalen Konten durch Ausführen der app, wählen die **registrieren** verknüpfen, und veröffentlichen Sie das Registrierungsformular.
5. Wählen Sie die Demo-e-Mail-Verknüpfung, die e-Mail-Bestätigung simuliert.
6. Entfernen Sie den Democode e-Mail-Link zur Bestätigung aus dem Beispiel (das `ViewBag.Link` Code im Kontocontroller. Finden Sie unter den `DisplayEmail` und `ForgotPasswordConfirmation` Aktionsmethoden und Razor-Ansichten).

> [!WARNING]
> Wenn Sie die Sicherheitseinstellungen in diesem Beispiel ändern, müssen Produktionen apps eine sicherheitsüberprüfung unterzogen werden, die die Änderungen explizit aufruft.


## <a name="examine-the-code-in-appstartidentityconfigcs"></a>Überprüfen Sie den Code in App\_Start\IdentityConfig.cs

Das Beispiel zeigt, wie Sie ein Konto erstellen und Hinzufügen der *Admin* Rolle. Ersetzen Sie die e-Mail-Adresse in das Beispiel mit der e-Mail-Adresse, die Sie für das Administratorkonto verwenden möchten. Die einfachste Möglichkeit jetzt zum Erstellen eines Administratorkontos wird programmgesteuert in die `Seed` Methode. Wir hoffen, haben in der Zukunft ein Tool, mit denen Sie zum Erstellen und Verwalten von Benutzern und Rollen. Der Beispielcode ist möglich, Sie erstellen und Verwalten von Benutzern und Rollen, jedoch benötigen Sie ein Administratorkonto zum Ausführen von Rollen und Verwaltungsseiten für Benutzer. In diesem Beispiel wird das Administratorkonto erstellt, wenn die Datenbank ein Seeding durchgeführt wird.

Ändern Sie das Kennwort ein, und ändern Sie den Namen mit einem Konto, in dem Sie die e-Mail-Benachrichtigungen empfangen können.

> [!WARNING]
> Sicherheit: vertrauliche Daten niemals in Ihrem Quellcode speichern.

Wie bereits erwähnt, die `app.CreatePerOwinContext` Aufruf in der Startup-Klasse fügt Rückrufe an die `Create` Methode der app DB Inhalten, Benutzer-Manager und die Rolle Manager Klassen. Die OWIN-pipeline Ruft die `Create` Methode für diese Klassen für jede Anforderung, und der Kontext für jede Klasse gespeichert. Der Kontocontroller macht den Benutzer-Manager aus dem HTTP-Kontext (mit der OWIN-Kontext):

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample5.cs)]

Wenn ein Benutzer ein lokales Konto, registriert die `HTTP Post Register` aufgerufen:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample6.cs)]

Der obige Code verwendet die Modelldaten zum Erstellen eines neuen Benutzerkontos mithilfe der e-Mail-Adresse und Kennwort eingegeben werden. Wenn der e-Mail-Alias im Datenspeicher, kontoerstellung fehlschlägt und das Formular wird erneut angezeigt. Die `GenerateEmailConfirmationTokenAsync` Methode erstellt eine sichere bestätigungstoken und speichert sie in der ASP.NET Identity-Datenspeicher. Die [Url.Action](https://msdn.microsoft.com/library/dd505232(v=vs.118).aspx) -Methode erstellt eine Verknüpfung mit der `UserId` und bestätigungstoken. Dieser Link wird dann per e-Mail gesendet, die dem Benutzer, der Benutzer kann auf den Link in ihren e-Mail-app auswählen, um ihr Konto zu bestätigen.

<a id="email"></a>

## <a name="set-up-email-confirmation"></a>Richten Sie e-Mail-Bestätigung

Wechseln Sie zu der [Anmeldeseite von Azure-SendGrid](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/) und kostenloses Konto registrieren. Fügen Sie Code ähnlich dem folgenden SendGrid zu konfigurieren:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample7.cs?highlight=5)]

> [!NOTE]
> E-Mail-Clients akzeptieren häufig nur SMS-Nachrichten (HTML). Sie sollten die Nachricht in Text und HTML bereitstellen. Im obigen SendGrid-Beispiel ist dies mit der `myMessage.Text` und `myMessage.Html` oben gezeigte Code.


Der folgende Code zeigt, wie zum Senden von e-Mails unter Verwendung der [MailMessage](https://msdn.microsoft.com/library/system.net.mail.mailmessage.aspx) Klasse Where `message.Body` gibt nur die Verknüpfung.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample8.cs)]

> [!WARNING]
> Sicherheit: vertrauliche Daten niemals in Ihrem Quellcode speichern. Das Konto und die Anmeldeinformationen werden in der AppSetting gespeichert. In Azure können sicher gespeichert werden diese Werte auf die **[konfigurieren](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** Registerkarte im Azure-Portal. Finden Sie unter [bewährte Methoden für die Bereitstellung von Kennwörtern und anderen vertraulichen Daten in ASP.NET und Azure](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md).


Geben Sie Ihre SendGrid-Anmeldeinformationen, die app ausführen, kann registrieren Sie sich mit einem e-Mail-Alias den Confirm-Link in Ihre e-Mail-Adresse auswählen. Informationen zu diesem Zweck Ihre [Outlook.com](http://outlook.com) e-Mail-Konto, finden Sie unter der John Atten [ C# SMTP-Konfiguration für die SMTP-Host Outlook.Com](http://typecastexception.com/post/2013/12/20/C-SMTP-Configuration-for-OutlookCom-SMTP-Host.aspx) sowie seine[ASP.NET Identity 2.0: Festlegen der Kontovalidierung und zwei-Faktor-Autorisierung](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx) sendet.

Nach Auswahl der **registrieren** Schaltfläche eine Bestätigung per e-Mail, eine Validierung-Token enthält, wird ihre e-Mail-Adresse an.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image12.png)

Der Benutzer erhält eine e-Mail mit einem bestätigungstoken für ihr Konto.

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image13.png)

## <a name="examine-the-code"></a>Überprüfen Sie den code

Der folgende Code veranschaulicht die `POST ForgotPassword`-Methode.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample9.cs)]

Die Methode schlägt ohne Fehlermeldung fehl, wenn die e-Mail-Adresse des Benutzers nicht bestätigt wurde. Wenn ein Fehler für eine ungültige e-Mail-Adresse bereitgestellt wurde, können böswillige Benutzer diese Informationen verwenden, gültige Benutzer-ID (e-Mail-Aliase) Angriffe gefunden.

Der folgende code zeigt die `ConfirmEmail` -Methode im Kontocontroller, die aufgerufen wird, wenn der Benutzer den Link zur Bestätigung in der an Sie verschickten e-Mail auswählt:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample10.cs)]

Sobald ein vergessenes Kennwort-Token verwendet wurde, wird es ungültig. Die folgende codeänderung in der `Create` Methode (in der *App\_Start\IdentityConfig.cs* Datei) legt die Token läuft in drei Stunden ab.

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample11.cs?highlight=6-8)]

Mit dem obigen Code läuft das Kennwort vergessen haben und die e-Mail-bestätigungstoken in 3 Stunden. Der Standardwert `TokenLifespan` ist ein Tag.

Der folgende Code zeigt die e-Mail-Bestätigung-Methode:

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample12.cs)]

 Um Ihre app sicherer machen, unterstützt ASP.NET Identity zweistufige Authentifizierung (2FA). Finden Sie unter [ASP.NET-Identität 2.0: Einrichten der Kontoüberprüfung und zwei-Faktor-Autorisierung](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx) von John Atten. Obwohl Sie die kontosperrung auf Anmeldefehler in Verbindung mit Kennwort Versuch festlegen können, wird dieser Ansatz Ihr Anmeldename anfällig für [DOS](http://en.wikipedia.org/wiki/Denial-of-service_attack) Sperrungen. Es wird empfohlen, dass Sie nur mit 2FA Sperrung von Konten verwenden.  
<a id="addRes"></a>

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Übersicht über benutzerdefinierte Speicheranbieter für ASP.NET Identity](../extensibility/overview-of-custom-storage-providers-for-aspnet-identity.md)
- [MVC 5-App mit Facebook, Twitter, LinkedIn und Google OAuth2 Sign-on](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md) außerdem gezeigt, wie der Tabelle Informationen zum Profil hinzufügen.
- [ASP.NET MVC und Identität 2.0: Die Grundlagen](http://typecastexception.com/post/2014/04/20/ASPNET-MVC-and-Identity-20-Understanding-the-Basics.aspx) von John Atten.
- [Einführung zu ASP.NET Identity](../getting-started/introduction-to-aspnet-identity.md)
- [Ankündigung der RTM-Version von ASP.NET Identity 2.0.0](https://blogs.msdn.com/b/webdev/archive/2014/03/20/test-announcing-rtm-of-asp-net-identity-2-0-0.aspx) von Pranav Rastogi.
