---
title: Authentifizierte Verschlüsselungsdetails in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung der Verschlüsselung von ASP.NET Core-Datenschutz authentifiziert.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/authenticated-encryption-details
ms.openlocfilehash: 9def03e6b27e19fc34a839e923d6152e086889db
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208579"
---
# <a name="authenticated-encryption-details-in-aspnet-core"></a>Authentifizierte Verschlüsselungsdetails in ASP.NET Core

<a name="data-protection-implementation-authenticated-encryption-details"></a>

Aufrufe von IDataProtector.Protect sind authentifizierte Verschlüsselung von Vorgängen. Die Protect-Methode bietet sowohl Vertraulichkeit und Authentizität, und es gebunden an die Kette von Zweck, die zum Ableiten von dieser bestimmten Instanz des IDataProtector ab dessen Stammverzeichnis IDataProtectionProvider verwendet wurde.

IDataProtector.Protect nimmt einen Byte []-nur-Text-Parameter auf und erzeugt eine Byte [] geschützte Nutzlast, deren Format nachfolgend genauer beschrieben wird. (Es gibt auch eine Überladung der Erweiterung-Methode verwendet einen Zeichenfolgenparameter für nur-Text, und gibt Sie eine geschützte Zeichenfolge-Nutzlast zurück. Wenn diese API verwendet wird das geschützte nutzlastformat haben weiterhin die unterhalb der Struktur, aber sie werden [base64url-codierte](https://tools.ietf.org/html/rfc4648#section-5).)

## <a name="protected-payload-format"></a>Geschützte Nutzlast-format

Die geschützte Nutzlast-Format besteht aus drei Hauptkomponenten:

* Ein 32-Bit-Header-Magic, der die Version des System zum Schutz von Daten angibt.

* Eine 128-Bit-Schlüssel-Id, die den Schlüssel zum Schützen dieser bestimmten Nutzlast identifiziert.

* Der Rest der geschützte Nutzlast ist [speziell für die Verschlüsselung, die von diesem Schlüssel gekapselt](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Im folgenden Beispiel wird den Schlüssel darstellt, eine AES-256-CBC + HMACSHA256-Verschlüsselung, und die Nutzlast ist weiter wie folgt unterteilt:
  * Ein 128-Bit-Schlüssel-Modifizierer.
  * Eine 128-Bit-Initialisierungsvektor.
  * 48 Bytes für den AES-256-CBC-Ausgabe.
  * Ein Tag für den HMACSHA256-Authentifizierung.

Eine geschützte Beispielnutzlast wird im folgenden dargestellt.

```
09 F0 C9 F0 80 9C 81 0C 19 66 19 40 95 36 53 F8
AA FF EE 57 57 2F 40 4C 3F 7F CC 9D CC D9 32 3E
84 17 99 16 EC BA 1F 4A A1 18 45 1F 2D 13 7A 28
79 6B 86 9C F8 B7 84 F9 26 31 FC B1 86 0A F1 56
61 CF 14 58 D3 51 6F CF 36 50 85 82 08 2D 3F 73
5F B0 AD 9E 1A B2 AE 13 57 90 C8 F5 7C 95 4E 6A
8A AA 06 EF 43 CA 19 62 84 7C 11 B2 C8 71 9D AA
52 19 2E 5B 4C 1E 54 F0 55 BE 88 92 12 C1 4B 5E
52 C9 74 A0
```

Sind nicht mit dem nutzlastformat über die ersten 32 Bits oder 4 Bytes der Magic-Header, die die Version (09 F0 C9 F0) identifiziert

Die nächsten 128 Bits oder 16 Bytes ist der Schlüsselbezeichner (80 9C 81 0C 19 66 19 40 95 36 53 F8 AA FF EE 57)

Im weiteren Verlauf enthält die Nutzlast und bezieht sich auf das verwendete Format.

> [!WARNING]
> Alle Nutzlasten, die auf einen bestimmten Schlüssel geschützt werden mit den gleichen Header von 20-Byte (Magic-Wert, Schlüssel-Id) beginnen. Administratoren können diese Tatsache zu Diagnosezwecken verwenden, um zu ermitteln, wenn eine Nutzlast generiert wurde. Beispielsweise entspricht die Nutzlast, die oben aufgeführten Schlüssel {0c819c80-6619-4019-9536-53f8aaffee57}. Wenn Sie nach der Überprüfung der wichtigsten Repository feststellen, dass dieser bestimmte Schlüssel Aktivierungsdatum 2015-01-01 wurde und das Ablaufdatum 2015-03-01 wurde, und es sinnvoll ist, Sie davon ausgehen, dass die Nutzlast (sofern Sie nicht manipuliert) innerhalb dieses Fensters, bieten generiert wurde oder nehmen eine kleine Mogelfaktor auf beiden Seiten.
