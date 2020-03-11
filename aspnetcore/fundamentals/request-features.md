---
title: Anforderungsfeatures in ASP.NET Core
author: ardalis
description: Erfahren Sie mehr zur Webserverimplementierung, die mit HTTP-Anforderungen und -antworten in Verbindung stehen, die in Schnittstellen definiert werden.
ms.author: riande
ms.date: 10/14/2016
uid: fundamentals/request-features
ms.openlocfilehash: d0f3ae521d1f314dd04cb581d9a921da4719273d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651991"
---
# <a name="request-features-in-aspnet-core"></a>Anforderungsfeatures in ASP.NET Core

Von [Steve Smith](https://ardalis.com/)

Ausführliche Informationen zur Webserverimplementierung, die mit HTTP-Anforderungen und -antworten in Verbindung stehen, werden in Schnittstellen definiert. Diese Schnittstellen werden von Serverimplementierungen und Middleware verwendet, um die Hostingpipeline der App zu erstellen und anzupassen.

## <a name="feature-interfaces"></a>Featureschnittstellen

ASP.NET Core definiert einige HTTP-Featureschnittstellen in `Microsoft.AspNetCore.Http.Features`, die von Servern verwendet werden, um unterstützte Features zu erkennen. Die folgenden Featureschnittstellen verarbeiten Anforderungen und geben Antworten zurück:

`IHttpRequestFeature` definiert die Struktur einer HTTP-Anforderung, einschließlich Protokoll, Pfad, Abfragezeichenfolge, Header und Text.

`IHttpResponseFeature` definiert die Struktur einer HTTP-Antwort, einschließlich Statuscode, Header und Antworttext.

`IHttpAuthenticationFeature` definiert die Unterstützung für das Erkennen von Benutzern auf Grundlage eines `ClaimsPrincipal` und für das Festlegen eines Authentifizierungshandlers.

`IHttpUpgradeFeature` definiert Unterstützung für [HTTP-Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), die es dem Client ermöglichen, festzulegen, welche zusätzlichen Protokolle er verwenden möchte, wenn der Server Protokolle wechseln möchte.

`IHttpBufferingFeature` definiert Methoden zum Deaktivieren des Pufferns von Anforderungen und/oder Antworten.

`IHttpConnectionFeature` definiert Eigenschaften für lokale und Remote-Adressen und Ports.

`IHttpRequestLifetimeFeature` definiert Unterstützung für das Abbrechen von Verbindungen oder das Erkennen einer verfrühten Beendung einer Anforderung, z.B. durch eine abgebrochene Verbindung zum Client.

`IHttpSendFileFeature` definiert eine Methode zum asynchronen Senden von Dateien.

`IHttpWebSocketFeature` definiert eine API zur Unterstützung von Websockets.

`IHttpRequestIdentifierFeature` fügt eine Eigenschaft hinzu, die implementiert werden kann, um eine Anforderung eindeutig identifizieren zu können.

`ISessionFeature` definiert die Abstraktionen `ISessionFactory` und `ISession` zur Unterstützung von Benutzersitzungen.

`ITlsConnectionFeature` definiert eine API zum Abrufen von Clientzertifikaten.

`ITlsTokenBindingFeature` definiert Methoden zum Arbeiten mit TLS-Tokenbindungsparametern.

> [!NOTE]
> `ISessionFeature` ist kein Serverfeature, wird aber von der `SessionMiddleware` implementiert. Weitere Informationen finden Sie im Artikel zur [Verwaltung des Anwendungszustandes](app-state.md).

## <a name="feature-collections"></a>Featuresammlung

Die `Features`-Eigenschaft von `HttpContext` bietet eine Schnittstelle zum Abrufen und Festlegen der verfügbaren HTTP-Features für die aktuelle Anforderung. Da die Featuresammlung auch im Kontext einer Anforderung änderbar ist, kann Middleware verwendet werden, um die Sammlung zu modifizieren und Unterstützung für zusätzliche Features hinzuzufügen.

## <a name="middleware-and-request-features"></a>Middleware- und Anforderungsfeatures

Obwohl Server für das Erstellen von Featuresammlungen zuständig sind, kann Middleware Features zu dieser hinzufügen und darin befindliche Features nutzen. Die `StaticFileMiddleware` greift z.B. auf das `IHttpSendFileFeature`-Feature zu. Wenn das Feature vorhanden ist, wird es verwendet, um die angeforderte statische Datei von deren physischem Pfad aus zu senden. Andernfalls wird eine alternative, langsamere Methode zum Senden der Datei verwendet. Wenn dies verfügbar ist, ermöglicht das `IHttpSendFileFeature` dem Betriebssystem das Öffnen der Datei und das Erstellen einer direkten Kopie im Kernelmodus auf der Netzwerkkarte.

Zusätzlich kann Middleware Features zur Featuresammlung des Servers hinzufügen. Vorhandene Features können von Middleware ersetzt werden, sodass die Middleware die Funktionalität des Servers beeinflussen kann. Features, die der Auflistung hinzugefügt wurden, stehen umgehend auch anderer Middleware oder der zugrunde liegenden Anwendung selbst zu einem späteren Zeitpunkt in der Anforderungspipeline zur Verfügung.

Anhand der Kombination benutzerdefinierter Serverimplementierungen und spezifischer Middlewareverbesserungen kann der genaue Satz von Features, den eine Anwendung erfordert, erstellt werden. So können fehlende Features hinzugefügt werden, ohne dass der Server verändert werden muss. Zudem wird sichergestellt, dass nur so viele Features wie unbedingt nötig verfügbar gemacht werden, sodass die Angriffsfläche auf einem Minimum gehalten und die Leistung verbessert wird.

## <a name="summary"></a>Zusammenfassung

Featureschnittstellen definieren spezifische HTTP-Features, die von einer angegebenen Anforderung möglicherweise unterstützt werden. Server definieren Featuresammlungen und den ersten Satz von Features, die von diesem Server unterstützt werden. Middleware kann jedoch verwendet werden, um diese Features zu verbessern.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Server](xref:fundamentals/servers/index)
* [Middleware](xref:fundamentals/middleware/index)
* [Open Web Interface for .NET (OWIN)](xref:fundamentals/owin)
