---
title: Unterschlüsselableitung und authentifizierte Verschlüsselung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung des Datenschutzes für ASP.NET Core Ableitung Unterschlüssel und authentifizierte Verschlüsselung.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/subkeyderivation
ms.openlocfilehash: 37e7b01700e8a6b755b5ed16a9d7d75a9eeb970e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891837"
---
# <a name="subkey-derivation-and-authenticated-encryption-in-aspnet-core"></a>Unterschlüsselableitung und authentifizierte Verschlüsselung in ASP.NET Core

<a name="data-protection-implementation-subkey-derivation"></a>

Die meisten Schlüssel in den Schlüsselbund enthält eine Form der Entropie und verfügen über algorithmische Informationen, die mit dem Hinweis "CBC-Modus-Verschlüsselung + HMAC-Überprüfung" oder "GCM-Verschlüsselung und Überprüfung". Klicken Sie in diesen Fällen wir verweisen auf die eingebettete Entropie, als das Schlüsselmaterial (oder KM) für diesen Schlüssel, und wir führen eine schlüsselableitung-Funktion, um die Schlüssel abgeleitet werden, die für die tatsächlichen kryptografischen Vorgänge verwendet werden.

> [!NOTE]
> Schlüssel sind abstrakt, und eine benutzerdefinierte Implementierung Verhalten sich ggf. nicht wie unten gezeigt. Wenn der Schlüssel eine eigene Implementierung des bietet `IAuthenticatedEncryptor` statt eines integrierten Factorys verwenden, der in diesem Abschnitt beschriebenen Mechanismus nicht mehr angewendet wird.

<a name="data-protection-implementation-subkey-derivation-aad"></a>

## <a name="additional-authenticated-data-and-subkey-derivation"></a>Zusätzliche authentifizierte Daten "und" unterschlüsselableitung

Die `IAuthenticatedEncryptor` -Schnittstelle dient als die Kernschnittstelle, die für alle authentifizierten Verschlüsselungsvorgänge. Die `Encrypt` Methode nimmt zwei Puffer: nur-Text und AdditionalAuthenticatedData (AAD). Der Datenfluss der nur-Text-Inhalt unverändert, den Aufruf von `IDataProtector.Protect`, aber die AAD wird vom System generiert und besteht aus drei Komponenten:

1. Der 32-Bit-Magic-Header 09 F0 C9 F0, die diese Version von System zum Schutz von Daten identifiziert.

2. Die 128-Bit-Schlüssel-ID.

3. Eine Zeichenfolge variabler Länge aus der Kette von Zweck, die erstellt formatiert die `IDataProtector` , diesen Vorgang ausführt.

Da die AAD für das Tupel aller drei Komponenten eindeutig ist, können wir es KM neue Schlüssel abgeleitet, anstatt KM selbst in allen unseren kryptografischen Vorgänge verwenden. Für jeden Aufruf von `IAuthenticatedEncryptor.Encrypt`, der folgende Prozess für die schlüsselableitung findet:

( K_E, K_H ) = SP800_108_CTR_HMACSHA512(K_M, AAD, contextHeader || keyModifier)

Hier rufen wir die NIST SP800-108 KDF in der Counter-Modus (finden Sie unter [NIST SP800-108](http://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-108.pdf), SEC 5.1) mit den folgenden Parametern:

* Ableiten des Schlüssels Schlüssel (KDK) = K_M

* PRF = HMACSHA512

* Bezeichnung = AdditionalAuthenticatedData

* context = contextHeader || keyModifier

Der Kontextheader mit variabler Länge ist, und es dient im Wesentlichen als ein Fingerabdruck der Algorithmen für die wir K_E und K_H ableiten. Der Schlüssel-Modifizierer ist eine 128-Bit-Zeichenfolge, die nach dem Zufallsprinzip generiert für jeden Aufruf von `Encrypt` und dient, um sicherzustellen, dass mit einer Überlastung der Wahrscheinlichkeit, dass KE und KH für diesen spezifischen Verschlüsselungsvorgang eindeutig sind, auch wenn alle anderen Eingaben an die KDF konstant ist.

Für die Verschlüsselung CBC-Modus und HMAC-Überprüfungsvorgänge | K_E | die Länge des Schlüssels Verschlüsselung symmetrischer Blöcke und | K_H | ist die Digest-Größe, die HMAC-Routine. Für die Verschlüsselung von GCM und Überprüfungsvorgänge, | K_H | = 0.

## <a name="cbc-mode-encryption--hmac-validation"></a>CBC-Modus-Verschlüsselung + HMAC-Überprüfung

Nach K_E über die oben genannten-Mechanismus generiert wird, werden wir generiert einen zufälligen Initialisierungsvektor und führen Sie den Verschlüsselungsalgorithmus symmetrischer Blöcke um nur-Text zu verschlüsseln. Der Initialisierungsvektor und Chiffretext werden klicken Sie dann über die HMAC-Routine, die initialisiert werden, mit dem Schlüssel K_H zum Erzeugen von Mac ausgeführt Dieser Prozess und der Rückgabewert wird grafisch im folgenden dargestellt.

![CBC-Modus-Prozess und Rückgabetypen](subkeyderivation/_static/cbcprocess.png)

*output:= keyModifier || iv || E_cbc (K_E,iv,data) || HMAC(K_H, iv || E_cbc (K_E,iv,data))*

> [!NOTE]
> Die `IDataProtector.Protect` Implementierung wird [stellen Sie den Magic-Header und die Schlüssel-Id](xref:security/data-protection/implementation/authenticated-encryption-details) Ausgabe vor der Rückgabe an den Aufrufer. Da das Magic-Header und -Schlüssel-Id implizit Teil [AAD](xref:security/data-protection/implementation/subkeyderivation#data-protection-implementation-subkey-derivation-aad), da die Schlüsselmodifizierers als Eingabe für die KDF eingezogen wird, bedeutet, dass jedes einzelnes Byte die letzte zurückgegebene Nutzlast durch Mac authentifiziert wird

## <a name="galoiscounter-mode-encryption--validation"></a>Galois/Counter-Modus-Verschlüsselung und Überprüfung

Sobald K_E über die oben genannten-Mechanismus generiert wird, werden wir generiert eine zufällige 96-Bit-Nonce und führen Sie den Verschlüsselungsalgorithmus symmetrischer Blöcke zu verschlüsseln den nur-Text und die 128-Bit-authentifizierungstag erzeugt hat.

![GCM-Modus-Prozess und Rückgabetypen](subkeyderivation/_static/galoisprocess.png)

*output := keyModifier || nonce || E_gcm (K_E,nonce,data) || authTag*

> [!NOTE]
> Obwohl GCM nativ das Konzept von AAD unterstützt, sind wir immer noch AAD nur für die ursprüngliche KDF, eingezogen um GCM für ihren AAD-Parameter eine leere Zeichenfolge übergeben. Der Grund dafür ist zwei Stufen. Zuerst [zur Unterstützung von Agilität](xref:security/data-protection/implementation/context-headers#data-protection-implementation-context-headers) nie K_M direkt als Verschlüsselungsschlüssel verwendet werden soll. Darüber hinaus erzwingt GCM sehr strenge Eindeutigkeit Anforderungen in Bezug auf ihre Eingaben. Wahrscheinlichkeit, dass die Verschlüsselungsroutine von GCM jemals aufgerufene für zwei oder mehr unterschiedliche legt fest, der Eingabedaten mit dem gleichen (Schlüssel, Nonce) Paar darf nicht mehr als 2 ^ 32. Wenn wir K_E korrigieren wir nicht mehr als 2 möglich ^ 32 Verschlüsselungsvorgänge, bevor wir führen afoul der 2 of ^-32 zu beschränken. Dies mag wie eine sehr große Anzahl von Vorgängen kann, jedoch ein Webserver mit hohem Datenverkehr über 4 Milliarden Anfragen in reine Tage, innerhalb der normale Lebensdauer für diese Schlüssel. Der 2 konform bleiben ^-32 Wahrscheinlichkeit Grenzwert, wir weiterhin mit einem 128-Bit-Modifizierertaste und ein 96-Bit-Nonce, die die Anzahl der verwendbaren Vorgänge für alle angegebenen K_M radikal erweitert. Aus Gründen der Einfachheit des Entwurfs teilen wir der Pfad für den KDF zwischen CBC und GCM-Vorgängen, und da AAD bereits in die KDF gilt es nicht erforderlich, die an die Routine GCM weitergeleitet.
