---
uid: aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
title: OWIN OAuth 2.0-Autorisierungsserver | Microsoft-Dokumentation
author: hongyes
description: Dieses Tutorial führt Sie zum Implementieren von OAuth 2.0-Autorisierungsserver mit OWIN-OAuth-Middleware. Dies ist ein erweitertes Lernprogramm, nur Outlin...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: 20acee16-c70c-41e9-b38f-92bfcf9a4c1c
msc.legacyurl: /aspnet/overview/owin-and-katana/owin-oauth-20-authorization-server
msc.type: authoredcontent
ms.openlocfilehash: b8451d2d9e346bd5e2f51ba45e48030a5221b549
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667647"
---
# <a name="owin-oauth-20-authorization-server"></a>OWIN-OAuth 2.0-Autorisierungsserver

> Dieses Tutorial führt Sie zum Implementieren von OAuth 2.0-Autorisierungsserver mit OWIN-OAuth-Middleware. Dies ist eine erweiterte Tutorial, in dem nur die Schritte zum Erstellen einer OWIN OAuth 2.0-Autorisierungsserver erläutert. Dies ist keine Schritt-für-Schritt-Tutorial. [Beispielcode herunterladen](https://code.msdn.microsoft.com/OWIN-OAuth-20-Authorization-ba2b8783/file/114932/1/AuthorizationServer.zip).
>
> > [!NOTE]
> > Dieser Umriss sollte nicht dazu vorgesehen werden, zum Erstellen einer sicheren Produktions-Apps verwendet werden. Dieses Tutorial soll nur einen Umriss zum Implementieren von OAuth 2.0-Autorisierungsserver mit OWIN-OAuth-Middleware zu bieten.
>
>
> ## <a name="software-versions"></a>Software-Versionen
>
> | **In diesem Tutorial gezeigt** | **Funktioniert auch mit** |
> | --- | --- |
> | Windows 8.1 | Windows 10, Windows 8, Windows 7 |
> | [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
> | .NET 4.7.2 |  |
>
> ## <a name="questions-and-comments"></a>Fragen und Kommentare
>
> Wenn Sie Fragen, die nicht direkt mit dem Tutorial verknüpft sind haben, können Sie diese unter buchen [Katana-Projekt auf GitHub](https://github.com/aspnet/AspNetKatana/). Finden Sie für Fragen und Kommentare in Bezug auf das Tutorial selbst im Kommentarabschnitt "am unteren Rand der Seite.


Die [OAuth 2.0-Framework](http://tools.ietf.org/html/rfc6749) können Sie eine Drittanbieter-app zum eingeschränkten Zugriff auf einen HTTP-Dienst abrufen. Nicht der Besitzer der Ressource der Anmeldeinformationen auf eine geschützte Ressource zuzugreifen, ruft der Client ein Zugriffstoken ab (Dies ist eine Zeichenfolge, die einen bestimmten Bereich, Lebensdauer und andere Zugriffsattribute bezeichnet). Zugriffstoken werden mit der Genehmigung des ressourcenbesitzers für Drittanbieter-Clients von einem autorisierungsserver ausgegeben.

In diesem Tutorial werden behandelt:

- Vorgehensweise: Erstellen von einem autorisierungsserver zur Unterstützung von vier Autorisierung gewährungstypen und Aktualisieren von Token:
    - Authorization Code grant
    - Die implizite Gewährung
    - Kennwort des Ressourcenbesitzers Clientanmeldeinformationen
    - Gewährung von Clientanmeldeinformationen
- Erstellen einen Ressourcenserver, die durch ein Zugriffstoken für den geschützt ist.
- Erstellen von OAuth 2.0-Clients.

<a id="prerequisites"></a>
## <a name="prerequisites"></a>Vorraussetzungen

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) gemäß **Softwareversionen** am oberen Rand der Seite.
- Vertrautheit mit OWIN. Finden Sie unter [erste Schritte mit dem Katana-Projekt](https://msdn.microsoft.com/magazine/dn451439.aspx) und [Neuigkeiten in der OWIN und Katana](index.md).
- Vertrautheit mit [OAuth](http://tools.ietf.org/html/rfc6749) Terminologie, einschließlich [Rollen](http://tools.ietf.org/html/rfc6749#section-1.1), [Protocol Flow](http://tools.ietf.org/html/rfc6749#section-1.2), und [Gewährung der Autorisierung](http://tools.ietf.org/html/rfc6749#section-1.3). [Einführung in die OAuth 2.0](http://tools.ietf.org/html/rfc6749#section-1) bietet eine gute Einführung in.

## <a name="create-an-authorization-server"></a>Einen Autorisierungsserver erstellen

In diesem Tutorial werden wir ungefähr, wie Sie mit skizzieren [OWIN](https://msdn.microsoft.com/magazine/dn451439.aspx) und ASP.NET MVC zum Erstellen von eines autorisierungsservers. Wir hoffen, geben Sie einen Download für das vollständige Beispiel, in Kürze, wie in diesem Tutorial nicht in jedem Schritt enthalten ist. Erstellen Sie zunächst eine leere Web-app, die mit dem Namen *AuthorizationServer* herunter, und installieren Sie die folgenden Pakete:

- Microsoft.AspNet.Mvc
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth
- Microsoft.Owin.Security.Cookies
- Microsoft.Owin.Security.Google (oder jede andere Anmeldung für soziale Netzwerke wie z. B. Microsoft.Owin.Security.Facebook-Paket)

Hinzufügen einer [OWIN-Startklasse](owin-startup-class-detection.md) unter dem Stammordner des Projekts mit dem Namen *Start*.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample1.cs?highlight=8)]

Erstellen Sie eine *App\_starten* Ordner. Wählen Sie die *App\_starten* Ordner und verwenden Sie Umschalt + Alt + A, Hinzufügen der heruntergeladene Version der *AuthorizationServer\App\_Start\Startup.Auth.cs* Datei.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample2.cs)]

Der obige Code ermöglicht die Anwendung/eine externe Anmeldung Cookies und Google-Authentifizierung, die vom autorisierungsserver selbst verwendet werden, um Konten zu verwalten.

Die `UseOAuthAuthorizationServer` Erweiterungsmethode ist den autorisierungsserver einrichten. Die Setupoptionen sind:

- `AuthorizeEndpointPath`: Der Anforderungspfad, in denen Clientanwendungen User-Agent umgeleitet wird, um die Benutzer erhalten, stimmen ein Token oder Code ausgeben. Es muss beginnen mit einem führenden Schrägstrich, z. B. "`/Authorize`".
- `TokenEndpointPath`: Die Anforderung Pfad-Clientanwendungen kommunizieren direkt zum Abrufen des Zugriffstokens. Es muss mit einem führenden Schrägstrich, z. B. "/ Token" beginnen. Wenn der Client ausgestellt wird eine [Client\_geheimen Schlüssel](http://tools.ietf.org/html/rfc6749#appendix-A.2), muss an diesen Endpunkt bereitgestellt werden.
- `ApplicationCanDisplayErrors`: Legen Sie auf `true` Wenn möchte, dass die Webanwendung auf eine benutzerdefinierten Fehlerseite für den clientvalidierungsfehler generieren `/Authorize` Endpunkt. Dies ist nur erforderlich, für Fälle, in dem der Browser wird nicht umgeleitet, an die Clientanwendung, z. B. sichern, wenn die `client_id` oder `redirect_uri` sind falsch. Die `/Authorize` Endpunkt sollte aber in den "Oauth. Fehler","Oauth. ErrorDescription"und"Oauth. Argument ErrorUri"-Eigenschaften werden in der OWIN-Umgebung hinzugefügt.

    > [!NOTE]
    > Wenn nicht "true", der autorisierungsserver gibt eine Standardfehlerseite mit den Fehlerdetails.
- `AllowInsecureHttp`: True, um zuzulassen, Autorisierungs- und tokenanforderungen für HTTP-URI-Adressen eingehen und eingehende `redirect_uri` autorisieren Anforderungsparameter, um HTTP-URI-Adressen verfügen.

    > [!WARNING]
    > Sicherheit – ist dies nur für die Entwicklung.
- `Provider`: Das Objekt bereitgestellt, die von der Anwendung zum Verarbeiten von Ereignissen, die von der autorisierungsservermiddleware ausgelöst. Die Anwendung kann die Schnittstelle vollständig implementieren oder es möglicherweise erstellen Sie eine Instanz des `OAuthAuthorizationServerProvider` und Zuweisen von Delegaten, die für die OAuth-Flows, die dieser Server unterstützt.
- `AuthorizationCodeProvider`: Erstellt einen einmaligen Verwendung Autorisierungscode an die Clientanwendung zurückgeben. Sichern Sie die Anwendung für die OAuth-Server **müssen** stellen eine Instanz für `AuthorizationCodeProvider` , in dem das Token erstellt, indem die `OnCreate/OnCreateAsync` Ereignis gilt für nur einen Aufruf von `OnReceive/OnReceiveAsync`.
- `RefreshTokenProvider`: Generiert ein Aktualisierungstoken, das verwendet werden kann, um ein neues Zugriffstoken bei Bedarf zu erzeugen. Wenn nicht angegeben. der autorisierungsserver keine Aktualisierungstoken aus zurück der `/Token` Endpunkt.

## <a name="account-management"></a>Kontoverwaltung

OAuth ist unerheblich, wo und wie Sie Ihre Kontoinformationen der Benutzer verwalten. Sie verfügt über [ASP.NET Identity](../../../identity/index.md) ist dafür zuständig. In diesem Tutorial haben wir den Konto Management Code vereinfachen und achten Sie darauf, dass sich der Benutzer anmelden kann mithilfe von OWIN Cookie-Middleware. Hier ist der vereinfachte Code für die `AccountController`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample3.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample4.cs?highlight=1)]

`ValidateClientRedirectUri` wird verwendet, um den Client mit der registrierte umleitungs-URL zu überprüfen. `ValidateClientAuthentication` überprüft die basic-Schema-Header und Formulartext, um die Anmeldeinformationen des Clients zu erhalten.

Die Anmeldeseite wird unten gezeigt:

![](owin-oauth-20-authorization-server/_static/image1.png)

Überprüfen Sie der IETF OAuth 2 [Authorization Code Grant](http://tools.ietf.org/html/rfc6749#section-4.1) jetzt Abschnitt.

**Anbieter** (in der Tabelle unten) ist der [OAuthAuthorizationServerOptions](https://msdn.microsoft.com/library/microsoft.owin.security.oauth.oauthauthorizationserveroptions(v=vs.111).aspx). Anbieter, der vom Typ `OAuthAuthorizationServerProvider`, die alle OAuth-Server-Ereignisse enthält.

| Authorization Code Grant-Abschnitt die einzelnen Schritte | Beispiel zum Herunterladen führt diese Schritte aus: |
| --- | --- |
|  |  |
| (A) der Client initiiert den Flow, indem die vom Benutzer-Agent des ressourcenbesitzers an den autorisierungsendpunkt umleiten. Der Client enthält, der Clientbezeichner, angeforderten Bereich, lokalen Status und einen umleitungs-URI, der an den der autorisierungsserver den Benutzer-Agent sendet zurück, sobald der Zugriff gewährt (oder verweigert) wird. | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) der autorisierungsserver authentifiziert den Ressourcenbesitzer (über den Benutzer-Agent) und legt fest, ob der Besitzer der Ressource gewährt oder die Anforderung des Clients den Zugriff verweigert. | **&lt;Wenn Benutzer den Zugriff gewährt&gt;**  Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) vorausgesetzt der Ressourcenbesitzer Zugriff gewährt, der autorisierungsserver leitet den Benutzer-Agent zurück an den Client mit der umleitungs-URI angegeben (in der Anforderung oder während der Clientregistrierung) früher. ... |  |
|  |  |
| (D) der Client fordert ein Zugriffstoken vom tokenendpunkt des autorisierungsservers mit den im vorherigen Schritt empfangene Autorisierungscode. Wenn die Anforderung ausführen, authentifiziert der Client beim autorisierungsserver. Der Client enthält den Redirection, den URI verwendet, um den Autorisierungscode für die Überprüfung zu erhalten. | Provider.MatchEndpoint Provider.ValidateClientAuthentication AuthorizationCodeProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantAuthorizationCode Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |

Eine beispielimplementierung für `AuthorizationCodeProvider.CreateAsync` und `ReceiveAsync` steuern die Erstellung und Überprüfung der Autorisierungscode wird unten angezeigt.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample5.cs)]

Der obige Code verwendet ein parallele in-Memory-Wörterbuch, um das Ticket Code und die Identität zu speichern und Wiederherstellen von die Identität nach dem Empfang des Codes. In einer echten Anwendung würden sie von einem persistenten Datenspeicher ersetzt werden. Der autorisierungsendpunkt wird für den Besitzer der Ressource gewähren von Zugriff an den Client. In der Regel benötigt eine Benutzeroberfläche, damit Benutzer auf eine Schaltfläche klicken und die Zuweisung zu bestätigen. OWIN-OAuth-Middleware kann Anwendungscode, um den autorisierungsendpunkt zu behandeln. In unserem Beispiel-app, die wir verwenden eines MVC-Controllers namens `OAuthController` verarbeiten. Dies ist die beispielimplementierung:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample6.cs?highlight=15)]

Die `Authorize` Aktion zunächst überprüft, ob der Benutzer an den autorisierungsserver angemeldet hat. Wenn dies nicht der Fall, die authentifizierungsmiddleware Herausforderungen bei der Aufrufer für die Authentifizierung mit das Cookie "Application" und auf die Anmeldeseite umgeleitet. (Siehe oben hervorgehobenen Code). Wenn der Benutzer angemeldet hat, wird es die Authorize-Ansicht rendern, wie unten dargestellt:

![](owin-oauth-20-authorization-server/_static/image2.png)

Wenn die **Grant** ausgewählt ist, die `Authorize` Aktion wird eine neue "Bearer" Identität und melden Sie sich mit der sie erstellt. Löst den autorisierungsserver generiert ein trägertoken, das und zurück an den Client mit JSON-Nutzlast zu senden.

### <a name="implicit-grant"></a>Die implizite Gewährung

Finden Sie in der IETF OAuth 2 [implizite Gewährung](http://tools.ietf.org/html/rfc6749#section-4.2) jetzt Abschnitt.

 Die [implizite Gewährung](http://tools.ietf.org/html/rfc6749#section-4.2) dargestellt in Abbildung 4 ist der Flow und Middleware Zuordnen der OWIN-OAuth folgt auf.

| Schritte im Abschnitt die implizite Gewährung des Datenflusses | Beispiel zum Herunterladen führt diese Schritte aus: |
| --- | --- |
|  |  |
| (A) der Client initiiert den Flow, indem die vom Benutzer-Agent des ressourcenbesitzers an den autorisierungsendpunkt umleiten. Der Client enthält, der Clientbezeichner, angeforderten Bereich, lokalen Status und einen umleitungs-URI, der an den der autorisierungsserver den Benutzer-Agent sendet zurück, sobald der Zugriff gewährt (oder verweigert) wird. | Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint |
|  |  |
| (B) der autorisierungsserver authentifiziert den Ressourcenbesitzer (über den Benutzer-Agent) und legt fest, ob der Besitzer der Ressource gewährt oder die Anforderung des Clients den Zugriff verweigert. | **&lt;Wenn Benutzer den Zugriff gewährt&gt;**  Provider.MatchEndpoint Provider.ValidateClientRedirectUri Provider.ValidateAuthorizeRequest Provider.AuthorizeEndpoint AuthorizationCodeProvider.CreateAsync |
|  |  |
| (C) vorausgesetzt der Ressourcenbesitzer Zugriff gewährt, der autorisierungsserver leitet den Benutzer-Agent zurück an den Client mit der umleitungs-URI angegeben (in der Anforderung oder während der Clientregistrierung) früher. ... |  |
|  |  |
| (D) der Client fordert ein Zugriffstoken vom tokenendpunkt des autorisierungsservers mit den im vorherigen Schritt empfangene Autorisierungscode. Wenn die Anforderung ausführen, authentifiziert der Client beim autorisierungsserver. Der Client enthält den Redirection, den URI verwendet, um den Autorisierungscode für die Überprüfung zu erhalten. |  |

Da wir bereits den autorisierungsendpunkt implementiert (`OAuthController.Authorize` Aktion) für die autorisierungscodegewährung, es ebenfalls impliziten Fluss automatisch aktiviert. Hinweis: `Provider.ValidateClientRedirectUri` wird verwendet, um die Client-ID mit der umleitungs-URL, zu überprüfen, bei denen der impliziten Gewährung schützt, senden Sie den Zugriff-Token genutzt, um böswillige Clients ([Man-in-the-Middle-Angriff](https://www.owasp.org/index.php/Man-in-the-middle_attack)).

### <a name="resource-owner-password-credentials-grant"></a>Kennwort des Ressourcenbesitzers Clientanmeldeinformationen

Finden Sie in der IETF OAuth 2 [Kennwort-Anmeldeinformationen-Ressourcenbesitzererteilung](http://tools.ietf.org/html/rfc6749#section-4.3) jetzt Abschnitt.

 Die [Kennwort-Anmeldeinformationen-Ressourcenbesitzererteilung](http://tools.ietf.org/html/rfc6749#section-4.3) dargestellt in Abbildung 5 ist der Flow und Middleware Zuordnen der OWIN-OAuth folgt auf.

| Resource Owner Password Gewährung von Clientanmeldeinformationen im Abschnitt die einzelnen Schritte | Beispiel zum Herunterladen führt diese Schritte aus: |
| --- | --- |
|  |  |
| (A) der Besitzer der Ressource bereitgestellt dem Client, mit dessen Benutzername und Kennwort. |  |
|  |  |
| (B) der Client fordert ein Zugriffstoken vom tokenendpunkt des autorisierungsservers, dazu die Anmeldeinformationen des ressourcenbesitzers empfangen. Wenn die Anforderung ausführen, authentifiziert der Client beim autorisierungsserver. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantResourceOwnerCredentials Provider.TokenEndpoint AccessToken Provider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (C) der autorisierungsserver authentifiziert den Client überprüft die Anmeldeinformationen des Resource Owner, und wenn gültig ist, gibt ein Zugriffstoken. |  |

Hier ist die Implementierung des Beispiels für `Provider.GrantResourceOwnerCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample7.cs)]

> [!NOTE]
> Der obige Code sollte in diesem Abschnitt des Tutorials erläutert und sollte nicht verwendet werden, in der sicheren oder Produktions-apps. Es wird nicht der Besitzer ressourcenanmeldeinformationen überprüft. Es wird vorausgesetzt, alle Anmeldeinformationen gültig ist und erstellt eine neue Identität für diese. Die neue Identität wird generieren das Zugriffstoken und Aktualisierungstoken verwendet werden. Ersetzen Sie den Code mit Ihrem eigenen sicheren Account Management-Code ein.


### <a name="client-credentials-grant"></a>Gewährung von Clientanmeldeinformationen

Finden Sie in der IETF OAuth 2 [Gewährung von Clientanmeldeinformationen](http://tools.ietf.org/html/rfc6749#section-4.4) jetzt Abschnitt.

 Die [Gewährung von Clientanmeldeinformationen](http://tools.ietf.org/html/rfc6749#section-4.4) dargestellt in Abbildung 6 ist der Flow und Middleware Zuordnen der OWIN-OAuth folgt auf.

| Gewährung von Clientanmeldeinformationen im Abschnitt die einzelnen Schritte | Beispiel zum Herunterladen führt diese Schritte aus: |
| --- | --- |
|  |  |
| (A) der Client authentifiziert sich mit dem autorisierungsserver und fordert ein Zugriffstoken vom tokenendpunkt. | Provider.MatchEndpoint Provider.ValidateClientAuthentication Provider.ValidateTokenRequest Provider.GrantClientCredentials Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (B) der autorisierungsserver authentifiziert den Client, und wenn gültig ist, gibt ein Zugriffstoken. |  |

Hier ist die Implementierung des Beispiels für `Provider.GrantClientCredentials`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample8.cs)]

> [!NOTE]
> Der obige Code sollte in diesem Abschnitt des Tutorials erläutert und sollte nicht verwendet werden, in der sicheren oder Produktions-apps. Ersetzen Sie den Code mit Ihrem eigenen sichere Client-Management-Code ein.


### <a name="refresh-token"></a>Aktualisierungstoken

Finden Sie in der IETF OAuth 2 [Aktualisierungstoken](http://tools.ietf.org/html/rfc6749#section-1.5) jetzt Abschnitt.

 Die [Aktualisierungstoken](http://tools.ietf.org/html/rfc6749#section-1.5) dargestellt in Abbildung 2 ist der Flow und Middleware Zuordnen der OWIN-OAuth folgt auf.

| Gewährung von Clientanmeldeinformationen im Abschnitt die einzelnen Schritte | Beispiel zum Herunterladen führt diese Schritte aus: |
| --- | --- |
|  |  |
| (G) der Client fordert ein neues Zugriffstoken, durch die Authentifizierung mit dem autorisierungsserver und das Aktualisierungstoken, das darstellen. Die Client-authentifizierungsanforderungen basieren auf dem Client und auf den Autorisierungsrichtlinien für den Server. | Provider.MatchEndpoint Provider.ValidateClientAuthentication RefreshTokenProvider.ReceiveAsync Provider.ValidateTokenRequest Provider.GrantRefreshToken Provider.TokenEndpoint AccessTokenProvider.CreateAsync RefreshTokenProvider.CreateAsync |
|  |  |
| (H) der autorisierungsserver authentifiziert den Client das Aktualisierungstoken, das überprüft, und gültig ist, gibt ein neues Zugriffstoken (und optional ein neues Aktualisierungstoken). |  |

Hier ist die Implementierung des Beispiels für `Provider.GrantRefreshToken`:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample9.cs)]

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample10.cs)]

## <a name="create-a-resource-server-which-is-protected-by-access-token"></a>Erstellen Sie einen Ressourcen-Server, die durch Access-Token geschützt wird

Erstellen Sie eine leere Web-app-Projekt, und folgende Pakete im Projekt installieren:

- Microsoft.AspNet.WebApi.Owin
- Microsoft.Owin.Host.SystemWeb
- Microsoft.Owin.Security.OAuth

Erstellen Sie eine Startup-Klasse, und Konfigurieren von Authentifizierung und Web-API. Finden Sie unter *AuthorizationServer\ResourceServer\Startup.cs* im Downloadbeispiel.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample11.cs)]

Finden Sie unter *AuthorizationServer\ResourceServer\App\_Start\Startup.Auth.cs* im Downloadbeispiel.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample12.cs)]

Finden Sie unter *AuthorizationServer\ResourceServer\App\_Start\Startup.WebApi.cs* im Downloadbeispiel.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample13.cs)]

- `UseCors` Methode können CORS für alle Domänen.
- `UseOAuthBearerAuthentication` Methode können OAuth Bearer-token-Authentifizierung-Middleware empfangen und Überprüfen von bearertoken aus der "Authorization"-Header in der Anforderung.
- `Config.SuppressDefaultHostAuthenticaiton` Unterdrückt die standardmäßige authentifizierten Prinzipal aus der app zu hosten, daher alle Anforderungen werden anonyme nach diesem Aufruf.
- `HostAuthenticationFilter` ermöglicht die Authentifizierung nur für den angegebenen Authentifizierungstyp. In diesem Fall ist es an der bearerauthentifizierungstyp.

Um die authentifizierte Identität zu veranschaulichen, erstellen wir ein ApiController-Element des aktuellen Benutzers Ansprüche ausgeben.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample14.cs)]

Wenn der autorisierungsserver und der Ressourcenserver nicht auf demselben Computer sind, verwendet die OAuth-Middleware die anderen Computerschlüssel zum Verschlüsseln und Entschlüsseln von bearertoken für Zugriff. Um den gleichen privaten Schlüssel zwischen den beiden Projekten verwenden zu können, fügen wir gleich `machinekey` festlegen, die in beiden *"Web.config"* Dateien.

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample15.xml?highlight=8-10)]

## <a name="create-oauth-20-clients"></a>Erstellen von OAuth 2.0-Clients

 Wir verwenden die [DotNetOpenAuth.OAuth2.Client](http://www.nuget.org/packages/DotNetOpenAuth.OAuth2.Client) NuGet-Paket, um den Clientcode zu vereinfachen.

### <a name="authorization-code-grant-client"></a>Authorization Code Grant-Client

 Dieser Client ist eine MVC-Anwendung. Es wird einen Authorization Code Grant-Datenfluss um den Back-End-Abrufen des Zugriffstokens von ausgelöst. Es verfügt über eine einzelne Seite, wie unten dargestellt:

![](owin-oauth-20-authorization-server/_static/image3.png)

- Die **autorisieren** Schaltfläche werden Browser umgeleitet werden, mit dem autorisierungsserver, um den Besitzer der Ressource, um Zugriff auf diesen Client zu benachrichtigen.
- Die **aktualisieren** Schaltfläche erhalten ein neues Zugriffstoken und Aktualisierungstoken, das die aktuelle Aktualisierung mit token.
- Die **Zugriff geschützte Ressource API** Schaltfläche wird den Ressourcenserver zum Abrufen von Daten von Ansprüchen des aktuellen Benutzers, und zeigen sie auf der Seite aufrufen.

Hier ist der Beispielcode die `HomeController` des Clients.

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample16.cs)]

`DotNetOpenAuth` erfordert SSL standardmäßig. Da unsere Demo HTTP verwendet wird, müssen Sie fügen folgende Einstellung in der Config-Datei:

[!code-xml[Main](owin-oauth-20-authorization-server/samples/sample17.xml?highlight=4-6)]

> [!WARNING]
> Sicherheit – nie deaktivieren SSL in einer Produktions-app. Ihre Anmeldeinformationen werden jetzt im Klartext über das Netzwerk gesendet wird. Der obige Code ist nur für lokale Beispiel zu debuggen und durchsuchen.


### <a name="implicit-grant-client"></a>Implizite Gewährung-Client

Dieser Client verwendet JavaScript:

1. Öffnen Sie ein neues Fenster, und leiten Sie an den autorisierungsendpunkt des Autorisierungsservers.
2. Abrufen des Zugriffstokens aus URL-Fragmenten, wenn es wieder leitet.

Die folgende Abbildung veranschaulicht diesen Prozess:

![](owin-oauth-20-authorization-server/_static/image4.png)

Der Client sollte über zwei Seiten verfügen: eine für die Startseite und der andere für den Rückruf. So sieht die Beispiel-JavaScript-Code finden Sie in der *"Index.cshtml"* Datei:

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample18.cshtml)]

Hier ist der Rückruf, der Verarbeitung von Code in *SignIn.cshtml* Datei:

[!code-cshtml[Main](owin-oauth-20-authorization-server/samples/sample19.cshtml)]

> [!NOTE]
> Eine bewährte Methode ist, verschieben den JavaScript-Code in eine externe Datei, und diese nicht mit dem Razor-Markup einzubetten. Um dieses Beispiel einfach zu halten, wurden sie kombiniert.


### <a name="resource-owner-password-credentials-grant-client"></a>Kennwort des Ressourcenbesitzers Credentials Grant-Client

Wir verwendet eine Konsolen-app, um diesen Client-demo. Hier ist der Code ein:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample20.cs)]

### <a name="client-credentials-grant-client"></a>Client Credentials Grant-Client

Ähnlich wie die Kennwort-Anmeldeinformationen-Ressourcenbesitzererteilung, hier ist Console app-Code:

[!code-csharp[Main](owin-oauth-20-authorization-server/samples/sample21.cs)]
