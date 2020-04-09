---
title: Schlüsselspeicherformat in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails des ASP.NET Core Data Protection-Schlüsselspeicherformat.
ms.author: riande
ms.date: 04/08/2020
uid: security/data-protection/implementation/key-storage-format
ms.openlocfilehash: 3072c673791b589027a910b80eaba52052eb9311
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976936"
---
# <a name="key-storage-format-in-aspnet-core"></a>Schlüsselspeicherformat in ASP.NET Core

<a name="data-protection-implementation-key-storage-format"></a>

Objekte werden im Ruhezustand in der XML-Darstellung gespeichert. Das Standardverzeichnis für die Schlüsselspeicherung lautet:

* Windows: *%LOCALAPPDATA%-ASP.NET-DataProtection-Keys\*
* macOS / Linux: *$HOME/.aspnet/DataProtection-Keys*

## <a name="the-key-element"></a>Das \<schlüsselelemente>-Element

Schlüssel sind als Objekte der obersten Ebene im Schlüsselrepository vorhanden. Nach Konventionsschlüsseln haben sie den Dateinamen **key-'guid'.xml**, wobei 'guid' die ID des Schlüssels ist. Jede dieser Dateien enthält einen einzelnen Schlüssel. Das Format der Datei ist wie folgt.

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

Der \<Schlüssel>-Elements enthält die folgenden Attribute und untergeordneten Elemente:

* Die Schlüssel-ID. Dieser Wert wird als autorisierend behandelt. der Dateiname ist einfach eine Nettigkeit für die menschliche Lesbarkeit.

* Die Version \<des Schlüsselelements> Element, das derzeit auf 1 festgelegt ist.

* Das Erstellungs-, Aktivierungs- und Ablaufdatum des Schlüssels.

* Ein \<Deskriptor>-Element, das Informationen zur authentifizierten Verschlüsselungsimplementierung enthält, die in diesem Schlüssel enthalten ist.

Im obigen Beispiel ist die ID des Schlüssels 80732141-ec8f-4b80-af9c-c4d2d1ff8901, er wurde am 19. März 2015 erstellt und aktiviert und hat eine Lebensdauer von 90 Tagen. (Gelegentlich liegt das Aktivierungsdatum möglicherweise etwas vor dem Erstellungsdatum wie in diesem Beispiel. Dies ist auf eine Nit in wie die APIs arbeiten und ist harmlos in der Praxis.)

## <a name="the-descriptor-element"></a>Der \<Deskriptor>-Element

Der \<äußere Deskriptor>-Element enthält ein Attribut deserializerType, der Assembly-qualifizierte Name eines Typs ist, der IAuthenticatedEncryptorDescriptorDeserializer implementiert. Dieser Typ ist verantwortlich \<für das Lesen des inneren Deskriptors> Elements und für das Analysieren der darin enthaltenen Informationen.

Das jeweilige Format \<des Deskriptors>-Elements hängt von der authentifizierten Verschlüsselungsimplementierung ab, die vom Schlüssel gekapselt wird, und jeder Deserialisierungstyp erwartet dafür ein etwas anderes Format. Im Allgemeinen enthält dieses Element jedoch algorithmische Informationen (Namen, Typen, OIDs oder ähnliches) und geheimes Schlüsselmaterial. Im obigen Beispiel gibt der Deskriptor an, dass dieser Schlüssel die AES-256-CBC-Verschlüsselung + HMACSHA256-Validierung umschließt.

## <a name="the-encryptedsecret-element"></a>Das \<verschlüsselte Secret->-Element

Ein ** &lt;verschlüsseltes&gt; Secret-Element,** das die verschlüsselte Form des geheimen Schlüsselmaterials enthält, kann vorhanden sein, wenn [die Verschlüsselung von Geheimnissen im Ruhezustand aktiviert ist.](xref:security/data-protection/implementation/key-encryption-at-rest) Das `decryptorType` Attribut ist der Assembly-qualifizierte Name eines Typs, der [IXmlDecryptor](/dotnet/api/microsoft.aspnetcore.dataprotection.xmlencryption.ixmldecryptor)implementiert. Dieser Typ ist verantwortlich für das Lesen des inneren ** &lt;verschlüsseltenSchlüsselelements&gt; ** und das Entschlüsseln, um den ursprünglichen Klartext wiederherzustellen.

Wie `<descriptor>`bei hängt das `<encryptedSecret>` jeweilige Format des Elements vom verwendungsgemäßen Verschlüsselungsmechanismus ab. Im obigen Beispiel wird der Hauptschlüssel mit Windows DPAPI pro Kommentar verschlüsselt.

## <a name="the-revocation-element"></a>Der \<Widerruf>-Element

Sperrungen sind als Objekte der obersten Ebene im Schlüsselrepository vorhanden. Nach Konventionswiderrufen wird der Dateiname **""timestamp".xml".xml** (zum Widerruf aller Schlüssel vor einem bestimmten Datum) oder **"Widerruf" (zum** Widerruf eines bestimmten Schlüssels) widerrufen. Jede Datei enthält \<einen einzelnen Widerruf>-Element.

Bei Widerrufen einzelner Schlüssel wird der Dateiinhalt wie folgt sein.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T22:45:30.2616742Z</revocationDate>
  <key id="eb4fc299-8808-409d-8a34-23fc83d026c9" />
  <reason>human-readable reason</reason>
</revocation>
```

In diesem Fall wird nur der angegebene Schlüssel widerrufen. Wenn die Schlüssel-ID "*" lautet, werden jedoch, wie im folgenden Beispiel, alle Schlüssel, deren Erstellungsdatum vor dem angegebenen Widerrufsdatum liegt, widerrufen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<revocation version="1">
  <revocationDate>2015-03-20T15:45:45.7366491-07:00</revocationDate>
  <!-- All keys created before the revocation date are revoked. -->
  <key id="*" />
  <reason>human-readable reason</reason>
</revocation>
```

Der \<Grund> Elements wird nie vom System gelesen. Es ist einfach ein bequemer Ort, um einen menschenlesbaren Grund für den Widerruf zu speichern.
