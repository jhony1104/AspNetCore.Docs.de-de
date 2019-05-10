---
title: Computerweite Richtlinie für den Unternehmensdatenschutz-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie mehr über die Unterstützung für das Festlegen einer computerweiten Standardrichtlinie für alle apps, die ASP.NET Core-Datenschutz nutzen.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/configuration/machine-wide-policy
ms.openlocfilehash: 70aaca7afcd3df22cebb4466fbd9845a2277688c
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897267"
---
# <a name="data-protection-machine-wide-policy-support-in-aspnet-core"></a>Computerweite Richtlinie für den Unternehmensdatenschutz-Unterstützung in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Nach der Ausführung unter Windows, bietet das System den Schutz von Daten eine eingeschränkte Unterstützung für das Festlegen einer computerweiten Standardrichtlinie für alle apps, die ASP.NET Core-Datenschutz nutzen. Der Grundgedanke ist, dass ein Administrator ändern möchten, kann eine Standardeinstellung, z. B. die Algorithmen verwendet oder die Gültigkeitsdauer, ohne die Notwendigkeit, jede app auf dem Computer manuell aktualisieren.

> [!WARNING]
> Der Systemadministrator kann die Standardrichtlinie festlegen, aber sie können nicht erzwungen. Der app-Entwickler kann immer einen beliebigen Wert mit einem eigenen Auswahl überschreiben. Die Standardrichtlinie wirkt sich nur auf apps, in denen der Entwickler einen expliziten Wert für eine Einstellung angegeben wurde nicht, aus.

## <a name="setting-default-policy"></a>Festlegen der Standardrichtlinie

Um Standardrichtlinie festgelegt, kann ein Administrator den bekannte Werten in der Registrierung unter dem folgenden Registrierungsschlüssel festlegen:

**HKLM\SOFTWARE\Microsoft\DotNetPackages\Microsoft.AspNetCore.DataProtection**

Wenn Sie auf einem 64-Bit-Betriebssystem und wirken sich auf das Verhalten von 32-Bit-Anwendungen, denken Sie daran, das Äquivalent Wow6432Node des oben angegebenen Schlüssels konfigurieren möchten.

Die unterstützten Werte werden unten angezeigt.

| Wert              | Typ   | Beschreibung |
| ------------------ | :----: | ----------- |
| EncryptionType     | Zeichenfolge | Gibt an, welche Algorithmen für den Schutz von Daten verwendet werden soll. Der Wert muss CNG-CBC, CNG-GCM und verwaltet werden und wird im folgenden ausführlicher beschrieben. |
| DefaultKeyLifetime | DWORD  | Gibt die Lebensdauer für die neu generierten Schlüssel. Der Wert wird in Tagen angegeben und muss > = 7. |
| KeyEscrowSinks     | Zeichenfolge | Gibt die Typen, die zur schlüsselhinterlegung verwendet werden. Der Wert ist eine durch Semikolons getrennte Liste von schlüsselhinterlegung senken, wobei jedes Element in der Liste den assemblyqualifizierten Namen eines Typs, die implementiert ist [IKeyEscrowSink](/dotnet/api/microsoft.aspnetcore.dataprotection.keymanagement.ikeyescrowsink). |

## <a name="encryption-types"></a>Verschlüsselungstypen

Wenn EncryptionType CNG-CBC ist, das System für die Verwendung konfiguriert symmetrische Blockchiffre CBC-Modus für die Vertraulichkeit und HMAC Authentizität mit Diensten, die von Windows CNG bereitgestellten (finden Sie unter [angeben benutzerdefinierte Windows CNG-Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) für Weitere Informationen). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder einer Eigenschaft für den CngCbcAuthenticatedEncryptionSettings-Typ entspricht.

| Wert                       | Typ   | Beschreibung |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | Zeichenfolge | Der Name eines Algorithmus für Verschlüsselung symmetrischer Blöcke von CNG verstanden. Dieser Algorithmus wird im CBC-Modus geöffnet. |
| EncryptionAlgorithmProvider | Zeichenfolge | Der Name der die CNG-Anbieter-Implementierung, die den Algorithmus "EncryptionAlgorithm" erstellt werden kann. |
| EncryptionAlgorithmKeySize  | DWORD  | Die Länge (in Bit) des Schlüssels, der für den Verschlüsselungsalgorithmus symmetrischer Blöcke abgeleitet werden. |
| HashAlgorithm               | Zeichenfolge | Der Name eines Hashalgorithmus, die von CNG verstanden. Dieser Algorithmus wird im HMAC-Modus geöffnet. |
| HashAlgorithmProvider       | Zeichenfolge | Der Name der die CNG-Anbieter-Implementierung, die den HashAlgorithm-Algorithmus erstellt werden kann. |

Wenn EncryptionType CNG-GCM ist, das System für die Verwendung konfiguriert symmetrische Blockchiffre Galois/Counter-Modus für die Vertraulichkeit und Authentizität mit Diensten, die von Windows CNG bereitgestellten (finden Sie unter [angeben benutzerdefinierte Windows CNG-Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-windows-cng-algorithms) Weitere Informationen). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder einer Eigenschaft für den CngGcmAuthenticatedEncryptionSettings-Typ entspricht.

| Wert                       | Typ   | Beschreibung |
| --------------------------- | :----: | ----------- |
| EncryptionAlgorithm         | Zeichenfolge | Der Name eines Algorithmus für Verschlüsselung symmetrischer Blöcke von CNG verstanden. Dieser Algorithmus wird im Galois/Counter-Modus geöffnet. |
| EncryptionAlgorithmProvider | Zeichenfolge | Der Name der die CNG-Anbieter-Implementierung, die den Algorithmus "EncryptionAlgorithm" erstellt werden kann. |
| EncryptionAlgorithmKeySize  | DWORD  | Die Länge (in Bit) des Schlüssels, der für den Verschlüsselungsalgorithmus symmetrischer Blöcke abgeleitet werden. |

Wenn EncryptionType verwaltet wird, wird das System konfiguriert, um eine verwaltete SymmetricAlgorithm für Vertraulichkeit und KeyedHashAlgorithm Authentizität zu verwenden (finden Sie unter [angeben benutzerdefinierte verwaltete Algorithmen](xref:security/data-protection/configuration/overview#specifying-custom-managed-algorithms) Weitere Details). Die folgenden zusätzlichen Werte werden unterstützt, von denen jeder einer Eigenschaft für den ManagedAuthenticatedEncryptionSettings-Typ entspricht.

| Wert                      | Typ   | Beschreibung |
| -------------------------- | :----: | ----------- |
| EncryptionAlgorithmType    | Zeichenfolge | Die Assembly qualifizierten Namen eines Typs, der SymmetricAlgorithm implementiert. |
| EncryptionAlgorithmKeySize | DWORD  | Die Länge (in Bit) des Schlüssels, der für den symmetrischen Verschlüsselungsalgorithmus abgeleitet werden. |
| ValidationAlgorithmType    | Zeichenfolge | Die Assembly qualifizierten Namen eines Typs, der KeyedHashAlgorithm implementiert. |

Wenn EncryptionType einen anderer Wert als Null oder leer ist, in das System den Schutz von Daten beim Start eine Ausnahme ausgelöst.

> [!WARNING]
> Wenn eine Standard-Einstellung konfigurieren, die Typnamen (EncryptionAlgorithmType, ValidationAlgorithmType KeyEscrowSinks) umfasst, müssen die Typen für die app verfügbar sein. Dies bedeutet, dass für apps, die auf Desktop-CLR ausgeführt wird, die Assemblys, die diese Typen enthalten, die in den globalen Assemblycache (GAC) vorhanden sein sollte. Für ASP.NET Core-apps, die auf .NET Core ausgeführt wird sollte die Pakete, die diese Typen enthalten, die installiert werden.
