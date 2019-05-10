---
title: Aufheben des Schutzes von Nutzlasten, die in ASP.NET Core, deren Schlüssel gesperrt wurden
author: rick-anderson
description: Informationen Sie zum Aufheben des Schutzes von Daten, die mit den Schlüsseln, die seit, in einer ASP.NET Core-app gesperrt wurden geschützt.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/data-protection/consumer-apis/dangerous-unprotect
ms.openlocfilehash: 26061d048dcd9c1e3d8909e9388d8b565376fa2f
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897107"
---
# <a name="unprotect-payloads-whose-keys-have-been-revoked-in-aspnet-core"></a>Aufheben des Schutzes von Nutzlasten, die in ASP.NET Core, deren Schlüssel gesperrt wurden

<a name="data-protection-consumer-apis-dangerous-unprotect"></a>

Die ASP.NET Core die Datenschutz-APIs sind in erster Linie für unbestimmte Persistenz vertrauliche Nutzlasten nicht vorgesehen. Andere Technologien wie [Windows CNG DPAPI](https://msdn.microsoft.com/library/windows/desktop/hh706794%28v=vs.85%29.aspx) und [Azure Rights Management](/rights-management/) eignen sich besser für das Szenario unbegrenzten Speicher, und dementsprechend sichere schlüsselverwaltung Funktionen haben. Allerdings nichts verbietet Entwickler mithilfe der ASP.NET Core die Datenschutz-APIs für den langfristigen Schutz von vertraulichen Daten. Schlüssel aus dem Schlüsselbund also nie entfernt `IDataProtector.Unprotect` vorhandene Nutzlasten können immer wiederhergestellt werden, solange die Schlüssel verfügbar und gültig sind.

Allerdings ein Problem tritt auf, wenn der Entwickler versucht, Daten, die mit einem gesperrten Schlüssel als geschützt wurden, deren Schutz aufheben `IDataProtector.Unprotect` wird in diesem Fall eine Ausnahme ausgelöst. Dies kann für kurzzeitiger oder vorübergehender Nutzlasten (z. B.-Authentifizierungstoken), in Ordnung sein, diese Arten von Nutzlasten leicht vom System erstellt werden können, und im schlimmsten Fall der Besucher der Website erforderlich sein, sich erneut anmelden. Aber für persistente Nutzlasten mit `Unprotect` Throw zu nicht akzeptablen Datenverlust führen kann.

## <a name="ipersisteddataprotector"></a>IPersistedDataProtector

Zur Unterstützung der Szenarios ermöglichen-Nutzlasten, um auch bei widerrufenen Schlüssel ungeschützt sein System zum Schutz von Daten enthält eine `IPersistedDataProtector` Typ. Um eine Instanz der `IPersistedDataProtector`, rufen Sie einfach eine Instanz des `IDataProtector` in die normale Art und Weise und versuchen Sie es Umwandlung der `IDataProtector` zu `IPersistedDataProtector`.

> [!NOTE]
> Nicht alle `IDataProtector` Instanzen umgewandelt werden können, um `IPersistedDataProtector`. Entwickler sollten verwenden die C# als Operator oder ähnliche Laufzeitausnahmen zu vermeiden, die durch ungültig wandelt verursacht, und sie sollten darauf vorbereitet sein, der Fehler entsprechend behandeln.

`IPersistedDataProtector` Stellt die folgende API-Oberfläche:

```csharp
DangerousUnprotect(byte[] protectedData, bool ignoreRevocationErrors,
     out bool requiresMigration, out bool wasRevoked) : byte[]
```

Diese API wird die geschützte Nutzlast (als Bytearray) und gibt zurück, die nicht geschützte Nutzlast. Es ist keine Zeichenfolge basierende Überladung. Die beiden out-Parameter lauten wie folgt aus.

* `requiresMigration`: Festlegung auf true fest, wenn der Schlüssel zum Schützen dieser Nutzlast ist nicht mehr den aktiven Standardschlüssel, z. B. der Schlüssel verwendet, um diese Nutzlast schützen alten und ein Schlüssel mit dem parallelen Vorgang, seitdem hat gelangen direkt. Der Aufrufer möchten möglicherweise sollten die Schritte zum erneuten Schützen der Nutzlast je nach ihren geschäftlichen Anforderungen.

* `wasRevoked`: wird auf true festgelegt werden, wenn der Schlüssel zum Schützen dieser Nutzlast aufgehoben wurde.

>[!WARNING]
> Äußerste Vorsicht walten lassen beim Übergeben von `ignoreRevocationErrors: true` auf die `DangerousUnprotect` Methode. Wenn Sie nach einer Aufrufen dieser Methode die `wasRevoked` Wert ist "true", dann wurde der Schlüssel zum Schützen dieser Nutzlast gesperrt und der Nutzlast Authentizität als fehlerverdächtig behandelt werden soll. In diesem Fall den Vorgang nur fortsetzen Sie für die Nutzlast der ungeschützt ausgeführt wird, wenn Sie eine separate Assurance verfügen, z. B. authentisch ist, ist, dass es aus, die von einem nicht vertrauenswürdigen WebClient gesendet werden, statt einer sicheren Datenbank stammt.

[!code-csharp[](dangerous-unprotect/samples/dangerous-unprotect.cs)]
