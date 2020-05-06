---
title: Unterschlüssel Ableitung und authentifizierte Verschlüsselung in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Implementierungsdetails ASP.net Core Unterschlüssel Ableitung von Datenschutz und authentifizierte Verschlüsselung.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: c4b4076d532e33b48b3438f842507a8cda2d71b6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776850"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Unterschlüssel Ableitung und authentifizierte Verschlüsselung in ASP.net Core

<a name="data-protection-implementation-subkey-derivation"></a>

Die meisten Schlüssel im Schlüsselbund enthalten eine Form der Entropie und enthalten algorithmische Informationen, die "CBC-modusverschlüsselung + HMAC-Validierung" oder "GCM-Verschlüsselung + Validierung" angeben. In diesen Fällen verweisen wir auf die eingebettete Entropie als Hauptschlüssel Material (oder km) für diesen Schlüssel und führen eine Schlüssel Ableitungen Funktion aus, um die Schlüssel abzuleiten, die für die eigentlichen Kryptografievorgänge verwendet werden.

> [!NOTE]
> Schlüssel sind abstrakt, und eine benutzerdefinierte Implementierung verhält sich möglicherweise nicht wie unten beschrieben. Wenn der Schlüssel seine eigene Implementierung von `IAuthenticatedEncryptor` bereitstellt, anstatt eine unserer integrierten Factorys zu verwenden, gilt der in diesem Abschnitt beschriebene Mechanismus nicht mehr.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Zusätzliche authentifizierte Daten und Unterschlüssel Ableitung

Die `IAuthenticatedEncryptor` -Schnittstelle fungiert als Kernschnittstelle für alle authentifizierten Verschlüsselungs Vorgänge. Die `Encrypt` zugehörige-Methode nimmt zwei Puffer an: Klartext und additionalauthentiereddata (AAD). Der nur-Text-Inhalt fließt unverändert zum Aufrufen `IDataProtector.Protect`von, aber das Aad wird vom System generiert und besteht aus drei Komponenten:

1. Der 32-Bit Magic Header 09 F0 C9 F0, der diese Version des Datenschutzsystems identifiziert.

2. Die 128-Bit-Schlüssel-ID.

3. Eine Zeichenfolge variabler Länge, die aus der Zweck Kette gebildet wird `IDataProtector` , die den erstellt hat, der diesen Vorgang ausführt.

Da Aad für das Tupel aller drei Komponenten eindeutig ist, kann es verwendet werden, um neue Schlüssel von km zu ableiten, anstatt km selbst in allen kryptografischen Vorgängen zu verwenden. Für jeden-Aufrufe `IAuthenticatedEncryptor.Encrypt`findet der folgende Schlüssel abderivationsprozess statt:

(K_E, K_H) = SP800_108_CTR_HMACSHA512 (K_M, AAD, contexderader | | keymodifier)

Hier rufen wir die NIST SP800-108 KDF im Counter-Modus (siehe [NIST SP800-108](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), sec. 5,1) mit den folgenden Parametern auf:

* Key derivations Schlüssel (KDK) = K_M

* PRF = HMACSHA512

* Bezeichnung = additionalauthentiereddata

* Context = contexteader | | keymodifier

Der Kontext Header weist eine Variable Länge auf und fungiert im Wesentlichen als Fingerabdruck der Algorithmen, für die K_E und K_H abgeleitet werden. Der schlüsselmodifizierer ist eine 128-Bit-Zeichenfolge, die `Encrypt` nach dem Zufallsprinzip für jeden-Befehl generiert wird, und stellt sicher, dass KE und KH für diesen spezifischen Authentifizierungs Verschlüsselungs Vorgang eindeutig sind, auch wenn alle anderen Eingaben für die KDF konstant sind.

Für CBC-modusverschlüsselung + HMAC-Validierungs Vorgänge | K_E | die Länge des symmetrischen Blockchiffre Schlüssels und | K_H | Die Hashgröße der HMAC-Routine. Für GCM-Verschlüsselung und Validierungs Vorgänge | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>Verschlüsselung im CBC-Modus + HMAC-Validierung

Nachdem K_E über den obigen Mechanismus generiert wurde, generieren wir einen zufälligen Initialisierungs Vektor und führen den symmetrischen Blockchiffre Algorithmus aus, um den Klartext zu entschlüsseln. Der Initialisierungs Vektor und der Chiffre Text werden dann über die HMAC-Routine ausgeführt, die mit dem Schlüssel K_H initialisiert wurde, um den Mac zu entwickeln. Dieser Prozess und der Rückgabewert werden im folgenden grafisch dargestellt.

![CBC-Modus-Prozess und-Rückgabe](subkeyderivation/_static/cbcprocess.png)

*Output: = keymodifier | | IV | | E_cbc (K_E, IV, Daten) | | HMAC (K_H, IV | | E_cbc (K_E, IV, Daten))*

> [!NOTE]
> Die `IDataProtector.Protect` -Implementierung stellt [den Magic-Header und die Schlüssel-ID](xref:security/data-protection/implementation/authenticated-encryption-details) vor der Rückgabe an den Aufrufer voran. Da der Magic-Header und die Schlüssel-ID implizit zu [Aad](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad)gehören und der schlüsselmodifizierer als Eingabe für die KDF ausgegeben wird, bedeutet dies, dass jedes einzelne Byte der letzten zurückgegebenen Nutzlast vom Mac authentifiziert wird.

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/Counter-modusverschlüsselung + Validierung

Nachdem K_E über den obigen Mechanismus generiert wurde, generieren wir eine zufällige 96-Bit-Nonce und führen den symmetrischen Blockchiffre Algorithmus aus, um den Klartext zu entschlüsseln und das 128-Bit-authentifizierungstag zu erzeugen.

![GCM-Modus-Prozess und-Rückgabe](subkeyderivation/_static/galoisprocess.png)

*Output: = keymodifier | | Nonce | | E_gcm (K_E, Nonce, Daten) | | authtag*

> [!NOTE]
> Auch wenn GCM das Konzept von Aad nativ unterstützt, wird Aad immer noch mit der ursprünglichen KDF-Datei versorgt, sodass für den Aad-Parameter eine leere Zeichenfolge an GCM übergeben werden kann. Der Grund dafür ist zwei fache. [Zur Unterstützung von Agilität](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) möchten wir K_M nicht direkt als Verschlüsselungsschlüssel verwenden. Darüber hinaus erzwingt GCM sehr strenge Eindeutigkeits Anforderungen für die Eingaben. Die Wahrscheinlichkeit, dass die GCM-Verschlüsselungsroutine für zwei oder mehr unterschiedliche Sätze von Eingabedaten mit dem gleichen (Key, Nonce)-Paar aufgerufen wird, darf nicht größer als 2 ^ 32 sein. Wenn wir den K_E korrigieren, dürfen nicht mehr als 2 ^ 32-Verschlüsselungs Vorgänge ausgeführt werden, bevor das Limit von 2 ^-32 erreicht wird. Dies mag eine sehr große Anzahl von Vorgängen sein, aber ein Webserver mit hohem Datenverkehr kann 4 Milliarden Anforderungen in nur wenigen Tagen durchlaufen, und zwar innerhalb der normalen Lebensdauer für diese Schlüssel. Um die Einhaltung der Wahrscheinlichkeits Grenze von 2 ^-32 einzuhalten, verwenden wir weiterhin einen 128-Bit-schlüsselmodifizierer und 96-Bit Nonce, wodurch die nutzbare Vorgangs Anzahl für jede angegebene K_M radikal erweitert wird. Aus Gründen der Einfachheit des Entwurfs wird der KDF-Codepfad zwischen CBC-und GCM-Vorgängen gemeinsam genutzt, und da Aad bereits in der KDF berücksichtigt wird, ist es nicht erforderlich, ihn an die GCM-Routine weiterzuleiten.
