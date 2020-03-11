---
title: Schlüsselspeicher Format in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails des Speicher Formats für die ASP.net Core Datenschutz Schlüssel.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 81df124f3dd0cadf8fd895ab55f66eec6415705f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654997"
---
# <a name="key-storage-format-in-aspnet-core"></a>Schlüsselspeicher Format in ASP.net Core

<a name="data-protection-implementation-key-storage-format"></a>

Objekte werden im Ruhezustand in der XML-Darstellung gespeichert. Das Standardverzeichnis für den Schlüsselspeicher ist%LocalAppData%\ASP.net\dataschutz-keys\.

## <a name="the-key-element"></a>Das \<Key >-Element

Schlüssel sind im schlüsselrepository als Objekte der obersten Ebene vorhanden. Gemäß den kontokonventionenschlüsseln haben Sie den Dateinamen **Key-{GUID}. XML**, wobei {GUID} die ID des Schlüssels ist. Jede dieser Dateien enthält einen einzelnen Schlüssel. Das Format der Datei lautet wie folgt.

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

Das \<Key >-Element enthält die folgenden Attribute und untergeordneten Elemente:

* Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt. der Dateiname ist einfach ein nicseinwert für die Lesbarkeit von Menschen.

* Die Version des \<Schlüssel > Elements, das derzeit bei 1 korrigiert ist.

* Die Erstellungs-, Aktivierungs-und Ablaufdatums Angaben des Schlüssels.

* Ein \<Deskriptor >-Element, das Informationen zur Implementierung der authentifizierten Verschlüsselung enthält, die in diesem Schlüssel enthalten ist.

Im obigen Beispiel lautet die ID des Schlüssels {80732141-ec8f-4b80-af9c-c4d2d1ff8901}, wurde am 19. März 2015 erstellt und aktiviert und verfügt über eine Gültigkeitsdauer von 90 Tagen. (Gelegentlich ist das Aktivierungsdatum möglicherweise etwas vor dem Erstellungsdatum, wie in diesem Beispiel. Der Grund hierfür ist die Funktionsweise der APIs und ist in der Praxis harmlos.)

## <a name="the-descriptor-element"></a>Das \<Deskriptor > Element

Das äußere \<Deskriptor >-Element enthält ein deserializertype-Attribut, bei dem es sich um den durch die Assembly qualifizierten Namen eines Typs handelt, der iauthenticatedencryptordescriptordeserializer implementiert. Dieser Typ ist für das Lesen des inneren \<Deskriptors >-Elements und für das Auswerten der in enthaltenen Informationen verantwortlich.

Das besondere Format des \<Deskriptors >-Elements hängt von der durch den Schlüssel gekapselten authentifizierten Verschlüsselungs Implementierung ab, und jeder deserialisierungstyp erwartet ein etwas anderes Format für dieses. Im Allgemeinen enthält dieses Element jedoch algorithmische Informationen (Namen, Typen, OIDs oder ähnliches) und Geheimnis Schlüsselmaterial. Im obigen Beispiel gibt der Deskriptor an, dass dieser Schlüssel die AES-256-CBC-Verschlüsselung + HMACSHA256-Überprüfung umschließt.

## <a name="the-encryptedsecret-element"></a>Das \<"verschlüsseltedsecret"-> Element

Ein **&lt;verschlüsseltedsecret-&gt;** Element, das die verschlüsselte Form des geheimen Schlüssel Materials enthält, kann vorhanden sein, wenn die [Verschlüsselung von geheimen Schlüsseln aktiviert ist](xref:security/data-protection/implementation/key-encryption-at-rest). Das Attribut `decryptorType` ist der durch die Assembly qualifizierte Name eines Typs, der [ixmldecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)implementiert. Dieser Typ ist für das Lesen der inneren **&lt;verschlüsseltedkey-&gt;** Elements und das Entschlüsseln der Methode zum Wiederherstellen des ursprünglichen Klartext verantwortlich.

Wie bei `<descriptor>`hängt das jeweilige Format des `<encryptedSecret>` Elements vom verwendeten ruhenden Verschlüsselungsmechanismus ab. Im obigen Beispiel wird der Hauptschlüssel mit Windows DPAPI gemäß dem Kommentar verschlüsselt.

## <a name="the-revocation-element"></a>Das \<-Sperr > Element

Widerrufen sind im schlüsselrepository als Objekte der obersten Ebene vorhanden. Gemäß der Konvention verfügen Widerrufungen über den Dateinamen " **Widerruf-{Timestamp}. XML** " (zum Aufheben aller Schlüssel vor einem bestimmten Datum) oder " **Widerruf-{GUID}. XML** " (für das Aufheben eines bestimmten Schlüssels). Jede Datei enthält ein einzelnes \<> Element.

Für das Aufheben der einzelnen Schlüssel wird der Inhalt der Datei wie unten beschrieben.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

In diesem Fall wird nur der angegebene Schlüssel widerrufen. Wenn die Schlüssel-ID jedoch "*" lautet, wie im folgenden Beispiel, werden alle Schlüssel, deren Erstellungsdatum vor dem angegebenen Sperr Datum liegt, widerrufen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Der \<Grund, warum > Element nie vom System gelesen wird. Es ist einfach ein guter Ort, um einen lesbaren Grund für den Widerruf zu speichern.
