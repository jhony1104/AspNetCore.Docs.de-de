---
uid: web-api/overview/security/working-with-ssl-in-web-api
title: Arbeiten mit SSL in Web-API | Microsoft-Dokumentation
author: MikeWasson
description: Veranschaulicht die Verwendung von SSL mit ASP.NET Web-API, einschließlich der Verwendung von SSL-Clientzertifikate.
ms.author: riande
ms.date: 02/22/2019
ms.assetid: 97f6164f-59cf-45c0-b820-e4aa29b45396
msc.legacyurl: /web-api/overview/security/working-with-ssl-in-web-api
msc.type: authoredcontent
ms.openlocfilehash: 69c0d217f605096d968435c062ee9931f8dff75f
ms.sourcegitcommit: b3894b65e313570e97a2ab78b8addd22f427cac8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56744104"
---
<a name="working-with-ssl-in-web-api"></a>Arbeiten mit SSL in Web-API
====================
durch [Mike Wasson](https://github.com/MikeWasson)

Verschiedene häufig verwendete Authentifizierungsschemas sind über einfaches HTTP nicht sicher. Insbesondere senden die einfache Authentifizierung und Formularauthentifizierung unverschlüsselte Anmeldeinformationen. Sicher, diese Authentifizierungsschemas *müssen* SSL verwenden. Darüber hinaus können der SSL-Clientzertifikate verwendet werden, um Clients zu authentifizieren.

## <a name="enabling-ssl-on-the-server"></a>Aktivieren von SSL auf dem Server

So richten Sie SSL in IIS 7 oder höher

- Erstellen oder Abrufen eines Zertifikats. Zu Testzwecken können Sie ein selbstsigniertes Zertifikat erstellen.
- Fügen Sie eine HTTPS-Bindung hinzu.

Weitere Informationen finden Sie unter [wie Set Up SSL on IIS 7](https://www.iis.net/learn/manage/configuring-security/how-to-set-up-ssl-on-iis).

Bei lokalen Tests können Sie SSL in IIS Express aus Visual Studio aktivieren. Legen Sie im Eigenschaftenfenster **SSL aktiviert** zu **"true"**. Beachten Sie den Wert **SSL-URL**; verwenden Sie diese URL zum Testen von HTTPS-Verbindungen.

![](working-with-ssl-in-web-api/_static/image1.png)

### <a name="enforcing-ssl-in-a-web-api-controller"></a>Erzwingen von SSL in einem Web-API-Controller

Wenn Sie eine HTTPS und HTTP-Bindung haben, können Clients weiterhin HTTP verwenden, auf die Website zugreifen. Sie können zulassen, dass einige Ressourcen, die über HTTP verfügbar sein, während andere Ressourcen SSL erforderlich ist. In diesem Fall verwenden Sie einen Aktionsfilter zur Verwendung von SSL für die geschützten Ressourcen. Der folgende Code zeigt eine Web-API-Authentifizierungsfilter, die prüft, ob SSL:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample1.cs)]

Fügen Sie diesen Filter für alle Web-API-Aktionen, die SSL erfordern:

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample2.cs)]

## <a name="ssl-client-certificates"></a>SSL-Clientzertifikate

SSL ermöglicht die Authentifizierung mithilfe von Zertifikaten der Public Key-Infrastruktur. Der Server muss ein Zertifikat angeben, die der Server an den Client authentifiziert. Es ist weniger gängig ist, für den Client ein Zertifikat mit dem Server bereitstellen, aber dies ist eine Möglichkeit zum Authentifizieren von Clients. Um Clientzertifikate mit SSL zu verwenden, benötigen Sie eine Möglichkeit, selbstsignierte Zertifikate für Ihre Benutzer zu verteilen. Bei vielen Anwendungstypen dies keine gute benutzererfahrung, aber in einigen Umgebungen (z. B. Enterprise) kann es möglich sein.

| Vorteile | Nachteile |
| --- | --- |
| -Zertifikats-anmeldeinformationseinstellungen sind stärker als Benutzername und Kennwort. -SSL bietet es sich um einen vollständigen sicheren Kanal, Authentifizierung, Nachrichtenintegrität und die Verschlüsselung von Nachrichten. | – Sie müssen die abrufen und Verwalten von PKI-Zertifikate. -Die-Clientplattform muss SSL-Clientzertifikate unterstützen. |

Um IIS für die Clientzertifikate akzeptieren konfigurieren zu können, öffnen Sie IIS-Manager aus, und führen Sie die folgenden Schritte aus:

1. Klicken Sie auf den Knoten "Site" in der Strukturansicht angezeigt.
2. Doppelklicken Sie auf die **SSL-Einstellungen** -Funktion in der Mitte.
3. Klicken Sie unter **Clientzertifikate**, wählen Sie eine der folgenden Optionen: 

    - **Akzeptieren Sie**: IIS wird ein Zertifikat vom Client akzeptiert, aber es ist nicht erforderlich sind.
    - **Erfordern**: Benötigen Sie ein Clientzertifikat. (Wenn Sie diese Option aktivieren, müssen Sie auch "SSL erforderlich" auswählen)

Sie können auch diese Optionen in der Datei "applicationHost.config" festlegen:

[!code-xml[Main](working-with-ssl-in-web-api/samples/sample3.xml)]

Die **SslNegotiateCert** Flag bedeutet, dass IIS ein Zertifikat vom Client akzeptiert, aber es ist nicht erforderlich sind (entspricht der Option "Accept" im IIS-Manager). Um ein Zertifikat anzufordern, legen Sie die **SslRequireCert** Flag. Zu Testzwecken können Sie auch diese Optionen in IIS Express in der lokalen Applicationhost festlegen. Config-Datei befindet sich in "Documents\IISExpress\config".

### <a name="creating-a-client-certificate-for-testing"></a>Erstellen ein Clientzertifikat für das Testen

Zu Testzwecken können Sie [MakeCert.exe](/windows/desktop/SecCrypto/makecert) ein Clientzertifikat zu erstellen. Erstellen Sie zunächst eine Test-Stammzertifizierungsstelle:

[!code-console[Main](working-with-ssl-in-web-api/samples/sample4.cmd)]

"MakeCert" fordert Sie zur Eingabe eines Kennworts für den privaten Schlüssel.

Fügen Sie das Zertifikat mit dem Test, die des Servers "Vertrauenswürdige Stammzertifizierungsstellen", wie folgt speichern:

1. Öffnen Sie MMC.
2. Klicken Sie unter **Datei**Option **Snap-In hinzufügen/entfernen**.
3. Wählen Sie **Computerkonto**.
4. Wählen Sie **lokalen Computer** und Abschließen des Assistenten.
5. Erweitern Sie im Navigationsbereich "Vertrauenswürdige Stammzertifizierungsstellen".
6. Auf der **Aktion** Startmenü **alle Aufgaben**, und klicken Sie dann auf **Import** um den Zertifikatimport-Assistenten zu starten.
7. Navigieren Sie zu der Zertifikatdatei, die Datei TempCA.cer.
8. Klicken Sie auf **öffnen**, klicken Sie dann auf **Weiter** und Abschließen des Assistenten. (Sie werden aufgefordert werden, um das Kennwort erneut eingeben.)

Nun erstellen Sie ein Clientzertifikat, das durch das erste Zertifikat signiert ist:

[!code-console[Main](working-with-ssl-in-web-api/samples/sample5.cmd)]

### <a name="using-client-certificates-in-web-api"></a>Mithilfe von Clientzertifikaten in Web-API

Sie können das Clientzertifikat auf dem Server, abrufen, durch den Aufruf [GetClientCertificate](https://msdn.microsoft.com/library/system.net.http.httprequestmessageextensions.getclientcertificate.aspx) für die Anforderungsnachricht. Die Methode gibt null, wenn kein Clientzertifikat vorhanden ist. Andernfalls wird ein **X509Certificate2** Instanz. Verwenden Sie dieses Objekt zum Abrufen von Informationen aus dem Zertifikat, wie z. B. der Aussteller und Antragsteller. Anschließend können Sie diese Informationen für die Authentifizierung und/oder Autorisierung.

[!code-csharp[Main](working-with-ssl-in-web-api/samples/sample6.cs)]
