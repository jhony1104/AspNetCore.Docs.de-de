---
title: Kontextheader in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung von ASP.NET Core-Datenschutz Kontextheader.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/context-headers
ms.openlocfilehash: 2b8fd594672bf623d38bfae90d05a984f92ce6a3
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208365"
---
# <a name="context-headers-in-aspnet-core"></a>Kontextheader in ASP.NET Core

<a name="data-protection-implementation-context-headers"></a>

## <a name="background-and-theory"></a>Theorie und Praxis Hintergrund

Im System zum Schutz von Daten also ein "Schlüssel" ein Objekt, das für Sie bereithält Verschlüsselungsdienste authentifiziert. Jeder Schlüssel wird durch eine eindeutige Id (eine GUID) identifiziert, und es bringt mit sich algorithmische Informationen und entropic Material. Es ist vorgesehen, dass jeder Schlüssel enthalten eindeutige Entropie, aber das System kann nicht zu erzwingen, die, und wir müssen auch für Entwickler zu berücksichtigen, die den Schlüsselbund für den gewählten szenariooptionen passen die algorithmische Informationen eines vorhandenen Schlüssels in den Schlüsselbund manuell ändern können. Um unsere sicherheitsanforderungen, erhält diese Fälle zu erreichen System zum Schutz von Daten verfügt über ein Konzept von [kryptografische Flexibilität](https://www.microsoft.com/en-us/research/publication/cryptographic-agility-and-its-relation-to-circular-encryption/), wodurch sicher mit einem einzigen entropic-Wert über mehrere kryptografischen Algorithmen.

Die meisten Systeme, die kryptografische Flexibilität unterstützen dazu einige identifizierende Informationen zum Algorithmus innerhalb der Nutzlast einschließlich. Der Algorithmus die OID ist im Allgemeinen ein guter Kandidat für diese. Allerdings ist ein Problem, denen wir begegneten, es mehrere Möglichkeiten gibt, dieselbe Algorithmusgruppe angeben: "AES" (CNG) und die verwalteten Aes, AesManaged, AesCryptoServiceProvider, AesCng und RijndaelManaged (bestimmte Parameter angegeben) Klassen sind alle tatsächlich die gleiche Aufgabe aus, und müssen wir eine Zuordnung aller an die richtige OID zu verwalten. Wenn ein Entwickler beispielsweise geben Sie einen benutzerdefinierten Algorithmus (oder sogar eine andere Implementierung von AES), müssten sie uns mitteilen, dass die OID. Diese zusätzlichen Registrierungsschritt macht die Systemkonfiguration besonders unangenehm.

Ins Gedächtnis rufen, haben wir beschlossen, dass wir das Problem über die falsche Richtung annähert wurden. Eine OID gibt an, was der Algorithmus ist allerdings kümmern wir uns nicht tatsächlich dies. Wenn wir einen Einzelwert entropic sicher in zwei verschiedene Algorithmen verwenden müssen, ist es nicht erforderlich für uns wissen, was die Algorithmen eigentlich sind. Was wir wirklich wichtig ist, wie sie sich Verhalten. Verschlüsselungsalgorithmus guten symmetrischer Blöcke ist auch eine starke pseudozufällige Permutation (PRP): Beheben Sie die Eingaben (Schlüssel, IV-Modus nur-Text-Verkettung), und die Ausgabe des verschlüsselten Texts überfordern Wahrscheinlichkeit werden sich von anderen symmetrischen Blockchiffre der Algorithmus den gleichen Eingaben. Auf ähnliche Weise guten schlüsselgebundenen Hash-Funktion ist auch eine starke pseudozufällige-Funktion (PRF), und bei einer festen Eingabeset seine Ausgabe überwältigend werden alle anderen schlüsselgebundenen Hash-Funktion unterscheidet.

Wir verwenden dieses Konzept stark PRPs und PRFs, um die Kontext-Header zu erstellen. Dieser Kontextheader fungiert im Wesentlichen als einen stabilen Fingerabdruck über die Algorithmen für einen bestimmten Vorgang verwendet, und bietet die kryptografische Flexibilität, die vom System zum Schutz von Daten benötigten. Dieser Header ist reproduzierbar, und später verwendet wird, als Teil der [Unterschlüssel Ableitung Prozess](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation). Es gibt zwei Möglichkeiten, um die Kontext-Header abhängig von den Betriebsmodi der zugrunde liegenden Algorithmen zu erstellen.

## <a name="cbc-mode-encryption--hmac-authentication"></a>CBC-Modus-Verschlüsselung + HMAC-Authentifizierung

<a name="data-protection-implementation-context-headers-cbc-components"></a>

Die Kontext-Header besteht aus folgenden Komponenten:

* [16 Bits] Der Wert 00 00 für eine Markierung ist also "-CBC-Verschlüsselung + HMAC-Authentifizierung".

* [32 Bits] Die Schlüssellänge (in Byte, big-Endian) des Verschlüsselungsverfahrensalgorithmus symmetrischer Blöcke.

* [32 Bits] Die Blockgröße des Verschlüsselungsverfahrensalgorithmus symmetrischer Blöcke (in Byte, big-Endian).

* [32 Bits] Die Schlüssellänge (in Byte, big-Endian) des HMAC-Algorithmus. (Derzeit entspricht die Größe immer die Digest-Größe.)

* [32 Bits] Die Digest-Größe (in Byte, big-Endian) des HMAC-Algorithmus.

* EncCBC (K_E, IV, ""), die die Ausgabe der Verschlüsselungsalgorithmus symmetrischer Blöcke eine leere Zeichenfolge Eingaben und IV ist ein Vektor Nullen. Die Erstellung von K_E wird unten beschrieben.

* MAC (K_H, ""), dies ist die Ausgabe von den HMAC-Algorithmus, der eine leere Zeichenfolge Eingaben. Die Erstellung von K_H wird unten beschrieben.

Im Idealfall konnten wir Nullen Vektoren für K_E und K_H übergeben. Allerdings möchten wir die Situation zu vermeiden, in denen der zugrunde liegende Algorithmus das Vorhandensein von schwachen Schlüsseln vor der Durchführung aller Vorgänge (insbesondere DES und 3DES) prüft, ob die schließt die Verwendung von einem einfachen oder wiederholbare Muster wie ein Vektor Nullen.

Stattdessen verwenden wir die NIST SP800-108 KDF in der Counter-Modus (finden Sie unter [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), SEC 5.1) mit der Länge Null-Schlüssel, Bezeichnung und Kontext und HMACSHA512 als den zugrunde liegenden PRF. Wir leiten | K_E | + | K_H | Bytes von Ausgabe, klicken Sie dann zerlegen das Ergebnis in K_E und K_H selbst. Mathematisch gesehen wird dies wie folgt dargestellt.

( K_E || K_H ) = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-192-cbc--hmacsha256"></a>Beispiel: AES-192-CBC + HMACSHA256

Betrachten Sie beispielsweise den Fall, in dem der symmetrischer Blöcke Verschlüsselungsalgorithmus ist AES-192-CBC und der Validierungsalgorithmus HMACSHA256, aus. Das System würde die Kontext-Header mit den folgenden Schritten generieren.

Zunächst kann (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512 Key = "", Label = "", Kontext = ""), wobei | K_E | = 192 Bits und | K_H | = 256 Bits pro angegebenen Algorithmen. Dies führt zu K_E 5BB6 =... 21DD und K_H A04A =... 00A9 im folgenden Beispiel:

```
5B B6 C9 83 13 78 22 1D 8E 10 73 CA CF 65 8E B0
61 62 42 71 CB 83 21 DD A0 4A 05 00 5B AB C0 A2
49 6F A5 61 E3 E2 49 87 AA 63 55 CD 74 0A DA C4
B7 92 3D BF 59 90 00 A9
```

Als Nächstes berechnen Enc_CBC (K_E, IV, "") für die AES-192-CBC angegebenen IV = 0 * und K_E wie oben beschrieben.

result := F474B1872B3B53E4721DE19C0841DB6F

Als Nächstes berechnen MAC (K_H, "") für HMACSHA256 K_H wie oben angegeben.

result := D4791184B996092EE1202F36E8608FA8FBD98ABDFF5402F264B1D7211536220C

Dadurch wird den vollständigen Kontext-Header, der folgenden erzeugt:

```
00 00 00 00 00 18 00 00 00 10 00 00 00 20 00 00
00 20 F4 74 B1 87 2B 3B 53 E4 72 1D E1 9C 08 41
DB 6F D4 79 11 84 B9 96 09 2E E1 20 2F 36 E8 60
8F A8 FB D9 8A BD FF 54 02 F2 64 B1 D7 21 15 36
22 0C
```

Dieser Kontextheader ist der Fingerabdruck des Paars Algorithmus authentifizierte Verschlüsselung (AES-192-CBC-Verschlüsselung + HMACSHA256-Überprüfung). Die Komponenten, wie beschrieben [oben](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers-cbc-components) sind:

* der Marker (00 00)

* die Block-Verschlüsselungsverfahren Länge des Schlüssels (00-00-00-18)

* die Block-Verschlüsselungsblockgröße (00-00-00-10)

* die Länge des HMAC (00-00-00-20)

* der HMAC-Digest-Größe (00-00-00-20)

* der blockverschlüsselung PRP-Ausgabe (74 F4 - DB 6F) und

* der HMAC-PRF-Ausgabe (D4 79 - End).

> [!NOTE]
> Die Verschlüsselung CBC-Modus + HMAC Kontextheader für die Authentifizierung basiert auf die gleiche Weise, unabhängig davon, ob die Implementierungen von Algorithmen von Windows CNG oder verwaltete SymmetricAlgorithm und KeyedHashAlgorithm Typen bereitgestellt werden. Dadurch können Anwendungen, die auf unterschiedlichen Betriebssystemen ausgeführt wird, die den gleichen Kontextheader, obwohl die Implementierungen von Algorithmen navigationsgründen unterscheiden sich zuverlässig zu erzeugen. (In der Praxis die KeyedHashAlgorithm keinen richtigen HMAC sein. Es kann schlüsselgebundenen Hash-Algorithmus-Typ sein.)

### <a name="example-3des-192-cbc--hmacsha1"></a>Beispiel: 3DES-192-CBC + HMACSHA1

Zunächst kann (K_E || K_H) = SP800_108_CTR (prf = HMACSHA512 Key = "", Label = "", Kontext = ""), wobei | K_E | = 192 Bits und | K_H | = 160 Bits pro angegebenen Algorithmen. Dies führt zu K_E A219 =... E2BB und K_H DC4A =... B464 im folgenden Beispiel:

```
A2 19 60 2F 83 A9 13 EA B0 61 3A 39 B8 A6 7E 22
61 D9 F8 6C 10 51 E2 BB DC 4A 00 D7 03 A2 48 3E
D1 F7 5A 34 EB 28 3E D7 D4 67 B4 64
```

Als Nächstes berechnen Enc_CBC (K_E, IV, "") für 3DES-192-CBC angegebenen IV = 0 * und K_E wie oben beschrieben.

result := ABB100F81E53E10E

Als Nächstes berechnen MAC (K_H, "") für HMACSHA1 K_H wie oben angegeben.

result := 76EB189B35CF03461DDF877CD9F4B1B4D63A7555

Dies führt zu den vollständigen Kontext-Header handelt es sich einen Fingerabdruck, der die authentifizierte Verschlüsselung Algorithmus Paar (3DES-192-CBC-Verschlüsselung + HMACSHA1 Überprüfung), unten:

```
00 00 00 00 00 18 00 00 00 08 00 00 00 14 00 00
00 14 AB B1 00 F8 1E 53 E1 0E 76 EB 18 9B 35 CF
03 46 1D DF 87 7C D9 F4 B1 B4 D6 3A 75 55
```

Die Komponenten unterteilt wie folgt:

* der Marker (00 00)

* die Block-Verschlüsselungsverfahren Länge des Schlüssels (00-00-00-18)

* die Block-Verschlüsselungsblockgröße (00-00-00-08)

* die Länge des HMAC (00-00-00-14)

* der HMAC-Digest-Größe (00-00-00-14)

* der blockverschlüsselung PRP-Ausgabe (AB B1 - E1 0E) und

* der HMAC-PRF-Ausgabe (76 "EB" – Ende).

## <a name="galoiscounter-mode-encryption--authentication"></a>Galois/Counter-Modus-Verschlüsselung und Authentifizierung

Die Kontext-Header besteht aus folgenden Komponenten:

* [16 Bits] Der Wert 00 01, die eine Markierung ist also "GCM-Verschlüsselung + Authentifizierung".

* [32 Bits] Die Schlüssellänge (in Byte, big-Endian) des Verschlüsselungsverfahrensalgorithmus symmetrischer Blöcke.

* [32 Bits] Die Nonce Größe (in Byte, big-Endian) beim authentifizierte Verschlüsselungsvorgänge verwendet. (Für unser System, dies wurde behoben, bei Nonce Größe = 96 Bits.)

* [32 Bits] Die Blockgröße des Verschlüsselungsverfahrensalgorithmus symmetrischer Blöcke (in Byte, big-Endian). (Für GCM, dies wurde behoben, bei Blockgröße = 128 Bit.)

* [32 Bits] Die Authentifizierung taggröße (in Byte, big-Endian) von der Funktion für authentifizierte Verschlüsselung generiert wurde. (Für unser System, dies wurde behoben, bei taggröße = 128 Bit.)

* [128 Bits] Das Tag Enc_GCM (K_E, Nonce, ""), dies ist die Ausgabe der Verschlüsselungsalgorithmus symmetrischer Blöcke eine leere Zeichenfolge Eingaben und Nonce ist, in denen ein 96-Bit-Nullen Vektor.

K_E wird abgeleitet und nutzen denselben Mechanismus wie bei der CBC-Verschlüsselung + HMAC-authentifizierungsszenario. Da keine K_H in spielen hier eine Rolle vorhanden ist, wir im Wesentlichen müssen jedoch | K_H | = 0 (null) und der Algorithmus zu reduziert die unterhalb der Form.

K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = "")

### <a name="example-aes-256-gcm"></a>Beispiel: AES-256-GCM

First, let K_E = SP800_108_CTR(prf = HMACSHA512, key = "", label = "", context = ""), where | K_E | = 256 bits.

K_E := 22BC6F1B171C08C4AE2F27444AF8FC8B3087A90006CAEA91FDCFB47C1B8733B8

Als Nächstes Berechnen der authentifizierungstag von Enc_GCM (K_E, Nonce, "") für die AES-256-GCM erhält Nonce = 096 und K_E wie oben beschrieben.

result := E7DCCE66DF855A323A6BB7BD7A59BE45

Dadurch wird den vollständigen Kontext-Header, der folgenden erzeugt:

```
00 01 00 00 00 20 00 00 00 0C 00 00 00 10 00 00
00 10 E7 DC CE 66 DF 85 5A 32 3A 6B B7 BD 7A 59
BE 45
```

Die Komponenten unterteilt wie folgt:

* der Marker (00 01)

* die Block-Verschlüsselungsverfahren Länge des Schlüssels (00-00-00-20)

* die Größe des Nonce (00 00 00 0 C)

* die Block-Verschlüsselungsblockgröße (00-00-00-10)

* die Authentifizierung taggröße (00-00-00-10) und

* Das authentifizierungstag "von der Ausführung der blockverschlüsselung (E7-DC - End).
