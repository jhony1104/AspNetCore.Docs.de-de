---
title: Schlüsselspeicherformat in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung des Schutzes von Daten ASP.NET Core Schlüsselspeicher Formats aus.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897477"
---
# <a name="key-storage-format-in-aspnet-core"></a>Schlüsselspeicherformat in ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Objekte werden im Ruhezustand in XML-Darstellung gespeichert. Das Standardverzeichnis für das Speichern von Schlüsseln ist % LOCALAPPDATA%\ASP.NET\DataProtection-Keys\.

## <a name="the-key-element"></a>Die \<Key >-Element

Schlüssel, die als Objekte der obersten Ebene im Key-Repository vorhanden sind. Gemäß der Konvention über Schlüssel verfügen, den Dateinamen **Schlüssel: {Guid} .xml**, wobei {Guid} die Id des Schlüssels ist. Jede dieser Dateien enthält einen einzelnen Schlüssel. Das Format der Datei lautet wie folgt aus.

```xml
<?xml version="1.0" encoding="utf-8"?>
<key id="80732141-ec8f-4b80-af9c-c4d2d1ff8901" version="1">
  <creationDate>2015-03-19T23:32:02.3949887Z</creationDate>
  <activationDate>2015-03-19T23:32:02.3839429Z</activationDate>
  <expirationDate>2015-06-17T23:32:02.3839429Z</expirationDate>
  <descriptor deserializerType="{deserializerType}">
    <descriptor>
      <encryption algorithm="AES_256_CBC" />
      <validation algorithm="HMACSHA256" />
      <enc:encryptedSecret decryptorType="{decryptorType}" xmlns:enc="...">
        <encryptedKey>
          <!-- This key is encrypted with Windows DPAPI. -->
          <value>AQAAANCM...8/zeP8lcwAg==</value>
        </encryptedKey>
      </enc:encryptedSecret>
    </descriptor>
  </descriptor>
</key>
```

Die \<Key >-Element enthält die folgenden Attribute und untergeordnete Elemente:

* Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt. der Dateiname ist einfach eine Besonderheit für Menschen lesbar.

* Die Version der \<Schlüssel > Element, das zurzeit auf 1 fest.

* Datumsangaben, des Schlüssels erstellen, Aktivierung und Ablauf.

* Ein \<Deskriptor >-Element, das Objektberechtigungsinformationen für die authentifizierte Verschlüsselung-Implementierung, die innerhalb dieser Schlüssel enthält.

Im obigen Beispiel der mit dem Schlüssel-Id ist {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, Erstellung und der am 19. März 2015, aktiviert und wird eine Lebensdauer von 90 Tagen. (Gelegentlich kann das Datum der Aktivierung etwas vor dem Erstellungsdatum wie in diesem Beispiel sein. Dies ist aufgrund einer übertreibenden wie die APIs arbeiten, und ist in der Praxis harmlos.)

## <a name="the-descriptor-element"></a>Die \<Deskriptor >-Element

Die äußere \<Deskriptor >-Element enthält ein Attribut-DeserializerType, wird die Assembly qualifizierten Namen eines Typs der IAuthenticatedEncryptorDescriptorDeserializer implementiert. Dieser Typ ist verantwortlich für das Lesen des inneren \<Deskriptor >-Element und für die Analyse der Informationen in.

Das besondere Format, der die \<Deskriptor >-Elements hängt von der authentifizierte Verschlüsselung Implementierung gekapselt durch den Schlüssel, und jeder Deserialisierer-Typ erwartet, dass ein geringfügig anderes Format für diese. Im Allgemeinen jedoch dieses Element enthält algorithmische Informationen (Namen, Typen, OIDs, oder ähnliches) und geheimem Schlüsselmaterial. Im obigen Beispiel gibt an, dass dieser Schlüssel dient als Wrapper für AES-256-CBC-Verschlüsselung und HMACSHA256-Überprüfung der Deskriptor.

## <a name="the-encryptedsecret-element"></a>Die \<EncryptedSecret >-Element

Ein **&lt;EncryptedSecret&gt;** Element enthält die verschlüsselte Form des Schlüsselmaterials geheimen darf vorhanden sein, wenn [die Verschlüsselung von Geheimnissen im Ruhezustand aktiviert](xref:security/data-protection/implementation/key-encryption-at-rest). Das Attribut `decryptorType` wird die Assembly qualifizierten Namen eines Typs, der implementiert [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor). Dieser Typ ist verantwortlich für das Lesen des inneren **&lt;EncryptedKey&gt;** Element- und entschlüsseln, um den ursprünglichen Klartext zu gewinnen.

Wie bei `<descriptor>`, das besondere Format, der die `<encryptedSecret>` -Elements hängt von der im Ruhezustand Verschlüsselungsmechanismus verwendet. Im obigen Beispiel wird der Hauptschlüssel verschlüsselt mithilfe von Windows-DPAPI pro des Kommentars.

## <a name="the-revocation-element"></a>Die \<Sperrung >-Element

Widerrufe sind als Objekte der obersten Ebene im Key-Repository vorhanden. Gemäß der Konvention Widerrufe Dateinamen haben **Zertifikatsperrlisten-{Zeitstempel} .xml** (für das Aufheben der alle Schlüssel vor einem bestimmten Datum) oder **Zertifikatsperrlisten-{Guid} .xml** (zum Aufheben von eines bestimmten Schlüssels). Jede Datei enthält eine einzelne \<Sperrung > Element.

Widerrufe der einzelnen Schlüssel, den Inhalt der Datei werden wie folgt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

In diesem Fall wird nur auf der angegebene Schlüssel gesperrt. Wenn der Schlüssel-Id ist "*", jedoch wie in der folgenden Beispiel werden alle Schlüssel, dessen Datum der Erstellung vor dem angegebenen Sperrdatum wird, widerrufen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Die \<Grund >-Element wird vom System nie gelesen. Es ist lediglich eine bequeme Möglichkeit, einen lesbaren Grund für die Sperrung zu speichern.
