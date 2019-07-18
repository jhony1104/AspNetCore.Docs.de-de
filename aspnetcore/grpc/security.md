---
title: Sicherheitsüberlegungen in GrpC für ASP.net Core
author: jamesnk
description: Erfahren Sie mehr über die Sicherheitsüberlegungen für GrpC für ASP.net Core.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 07/07/2019
uid: grpc/security
ms.openlocfilehash: 4a70cb16d8397dbc69a626435fb72a0512788b14
ms.sourcegitcommit: b40613c603d6f0cc71f3232c16df61550907f550
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68308753"
---
# <a name="security-considerations-in-grpc-for-aspnet-core"></a>Sicherheitsüberlegungen in GrpC für ASP.net Core

Von [James Newton-King](https://twitter.com/jamesnk)

Dieser Artikel enthält Informationen zum Sichern von GrpC mit .net Core.

## <a name="transport-security"></a>Transport Sicherheit

GrpC-Nachrichten werden mithilfe von http/2 gesendet und empfangen. Wir empfehlen Folgendes:

* [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246) zum Sichern von Nachrichten in Produktions-GrpC-Apps verwendet.
* GrpC-Dienste sollten nur gesicherte Ports lauschen und darauf reagieren.

TLS ist in Kestrel konfiguriert. Weitere Informationen zum Konfigurieren von Kestrel-Endpunkten finden Sie unter [Kestrel-Endpunkt Konfiguration](xref:fundamentals/servers/kestrel#endpoint-configuration).

## <a name="exceptions"></a>Ausnahmen

Ausnahme Meldungen werden im Allgemeinen als sensible Daten betrachtet, die für einen Client nicht offengelegt werden sollen. Standardmäßig sendet GrpC nicht die Details einer Ausnahme, die von einem GrpC-Dienst ausgelöst wird, an den Client. Stattdessen empfängt der Client eine generische Meldung, die angibt, dass ein Fehler aufgetreten ist. Die Übermittlung von Ausnahme Meldungen an den Client kann überschrieben werden (z. b. in "Entwicklung" oder "Test") mit " [enabledetailederrors](xref:grpc/configuration#configure-services-options)". Ausnahme Meldungen sollten nicht in Produktions-Apps für den Client verfügbar gemacht werden.

## <a name="message-size-limits"></a>Größen Limits für Nachrichten

Eingehende Nachrichten an GrpC-Clients und-Dienste werden in den Arbeitsspeicher geladen. Die Größenbeschränkungen für Nachrichten sind ein Mechanismus, mit dem verhindert wird, dass GrpC übermäßig viele Ressourcen beansprucht.

GrpC verwendet Größenbeschränkungen pro Nachricht, um eingehende und ausgehende Nachrichten zu verwalten. Standardmäßig schränkt der GrpC eingehende Nachrichten auf 4 MB ein. Es gibt keine Beschränkung für ausgehende Nachrichten.

Auf dem-Server können die GrpC-Nachrichten Limits für alle Dienste in einer APP mit `AddGrpc`folgenden Aktionen konfiguriert werden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddGrpc(options =>
    {
        options.ReceiveMaxMessageSize = 1 * 1024 * 1024;  // 1 megabyte
        options.SendMaxMessageSize = 1 * 1024 * 1024;     // 1 megabyte
    });
}
```

Grenzwerte können auch für einen einzelnen Dienst mithilfe `AddServiceOptions<TService>`von konfiguriert werden. Weitere Informationen zum Konfigurieren von Größen Limits für Nachrichten finden Sie unter [GrpC-Konfiguration](xref:grpc/configuration).

## <a name="client-certificate-validation"></a>Überprüfung des Client Zertifikats

[Client Zertifikate](https://tools.ietf.org/html/rfc5246#section-7.4.4) werden anfänglich überprüft, wenn die Verbindung hergestellt wird. Standardmäßig führt Kestrel keine zusätzliche Überprüfung des Client Zertifikats einer Verbindung durch.

Es wird empfohlen, dass die von Client Zertifikaten gesicherten GrpC-Dienste das [Microsoft. aspnetcore. Authentication. Certificate](xref:security/authentication/certauth) -Paket verwenden. ASP.net Core Zertifizierungs Authentifizierung führt eine zusätzliche Überprüfung für ein Client Zertifikat durch, einschließlich:

* Das Zertifikat hat eine gültige erweiterte Schlüssel Verwendung (EKU).
* Liegt innerhalb seines Gültigkeits Zeitraums
* Zertifikat Sperrung überprüfen
