---
title: Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden
author: rick-anderson
description: Erfahren Sie, wie Sie den Schutz von Daten in einer ASP.net Core-App aufheben können, die mit Schlüsseln geschützt sind, die seit dem widerrufen wurden
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654595"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Aufheben des Schutzes von Nutzlasten, deren Schlüssel in ASP.net Core widerrufen wurden

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Die ASP.net Core-Datenschutz-APIs sind nicht in erster Linie für die unbegrenzte Persistenz von vertraulichen Nutzlasten vorgesehen. Andere Technologien wie [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) und [Azure Rights Management](/rights-management/) sind besser für das Szenario der unbegrenzten Speicherung geeignet und verfügen über die entsprechenden leistungsstarken Schlüssel Verwaltungsfunktionen. Dies bedeutet, dass ein Entwickler nicht daran hindert, die ASP.net Core Datenschutz-APIs für den langfristigen Schutz vertraulicher Daten zu verwenden. Schlüssel werden nie aus dem Schlüsselbund entfernt. Daher können `IDataProtector.Unprotect` vorhandene Nutzlasten jederzeit wiederherstellen, solange die Schlüssel verfügbar und gültig sind.

Es tritt jedoch ein Problem auf, wenn der Entwickler versucht, die Daten zu schützen, die mit einem gesperrten Schlüssel geschützt wurden, da `IDataProtector.Unprotect` in diesem Fall eine Ausnahme auslöst. Dies kann bei kurzlebigen oder vorübergehenden Nutzlasten (wie z. b. Authentifizierungs Token) in Ordnung sein, da diese Arten von Nutzlasten problemlos vom System neu erstellt werden können und sich der Standort Besucher im schlimmsten Fall erneut anmelden muss. Für persistente Nutzlasten könnte das `Unprotect` Throw jedoch zu unzulässiger Datenverlusten führen.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Um das Szenario zu unterstützen, in dem Nutzlasten auch bei widerrufenen Schlüsseln nicht geschützt werden können, enthält das Datenschutzsystem einen `IPersistedDataProtector` Typ. Um eine Instanz von `IPersistedDataProtector`zu erhalten, können Sie einfach eine Instanz von `IDataProtector` in der normalen Weise erhalten und versuchen, die `IDataProtector` in `IPersistedDataProtector`umzuwandeln.

> [!NOTE]
> Nicht alle `IDataProtector` Instanzen können in `IPersistedDataProtector`umgewandelt werden. Entwickler sollten den C# as-Operator oder ähnlich verwenden, um Lauf Zeit Ausnahmen durch ungültige Umwandlungen zu vermeiden, und Sie sollten darauf vorbereitet sein, den Fehlerfall entsprechend zu behandeln.

`IPersistedDataProtector` macht die folgende API-Oberfläche verfügbar:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Diese API übernimmt die geschützte Nutzlast (als Bytearray) und gibt die ungeschützte Nutzlast zurück. Es gibt keine Zeichen folgen basierte Überladung. Die zwei out-Parameter lauten wie folgt.

* `requiresMigration`: wird auf "true" festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, nicht mehr der aktive Standardschlüssel ist, z. b. ist der Schlüssel, der zum Schützen dieser Nutzlast verwendet wird, veraltet, und es wurde ein wichtiger rollingvorgang ausgeführt. Der Aufrufer möchte ggf. den erneuten Schutz der Nutzlast abhängig von Ihren geschäftlichen Anforderungen in Erwägung gezogen.

* `wasRevoked`: wird auf true festgelegt, wenn der Schlüssel, der zum Schützen dieser Nutzlast verwendet wurde, widerrufen wurde.

>[!WARNING]
> Wenn `ignoreRevocationErrors: true` an die `DangerousUnprotect`-Methode übergeben wird, gehen Sie äußerst vorsichtig vor. Wenn nach dem Aufrufen dieser Methode der `wasRevoked` Wert true ist, wurde der zum Schutz dieser Nutzlast verwendete Schlüssel widerrufen, und die Authentizität der Nutzlast sollte als Fehler verdächtig behandelt werden. In diesem Fall wird nur der Betrieb der nicht geschützten Nutzlast fortgesetzt, wenn Sie eine separate Gewissheit haben, dass Sie authentisch ist, z. b., dass Sie von einer sicheren Datenbank stammt, anstatt von einem nicht vertrauenswürdigen WebClient gesendet zu werden.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
