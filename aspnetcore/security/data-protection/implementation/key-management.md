---
title: Schlüsselverwaltung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie Details zur Implementierung der ASP.NET Core-Datenschutz Key Management-APIs.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-management
ms.openlocfilehash: 431bdf2d3076c83279b78f327ddb647f69e6e584
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892297"
---
# <a name="key-management-in-aspnet-core"></a>Schlüsselverwaltung in ASP.NET Core

<a name="data-protection-implementation-key-management"></a>

Das System zum Schutz von Daten verwaltet automatisch die Lebensdauer der Hauptschlüssel zum Schützen und Aufheben des Schutzes von Nutzlasten verwendet. Jeder Schlüssel kann in einer von vier Phasen vorhanden sein:

* Erstellt: der Schlüssel vorhanden ist, in dem Schlüsselbund jedoch noch nicht aktiviert wurde. Für neue schützen Vorgänge der Schlüssel sollte nicht verwendet werden, bis genügend Zeit verstrichen ist, dass der Schlüssel Gelegenheit hatten wurde, auf alle Computer verteilt, die diese Schlüsselbund verbrauchen.

* Aktiv - der Schlüssel vorhanden ist, in dem Schlüsselbund und sollte für alle neuen schützen Vorgänge verwendet werden.

* Abgelaufen – der Schlüssel seiner natürlichen Lebensdauer ausgeführt wurde, und sollte nicht mehr für neue schützen Vorgänge verwendet werden.

* Widerrufen: der Schlüssel gefährdet ist, und darf nicht für neue schützen Vorgänge verwendet werden.

Erstellte, aktive und abgelaufene Schlüssel möglicherweise alle zum Aufheben des Schutzes von eingehenden Nutzlasten verwendet. Widerrufenen Schlüssel in der Standardeinstellung können nicht zum Aufheben des Schutzes von Nutzlasten verwendet werden, aber der Anwendungsentwickler kann [dieses Verhalten überschreiben](xref:security/data-protection/consumer-apis/dangerous-unprotect#data-protection-consumer-apis-dangerous-unprotect) bei Bedarf.

>[!WARNING]
> Der Entwickler möglicherweise versucht, einen Schlüssel aus dem Schlüsselbund (z. B. durch das Löschen der entsprechenden Datei aus dem Dateisystem) zu löschen. An diesem Punkt alle durch den Schlüssel geschützte Daten dauerhaft nicht lesbar ist, und es gibt keine Notfall außer Kraft setzen, wie bei der widerrufenen Schlüssel. Löschen eines Schlüssels ist wirklich destruktive Verhalten, und daher macht System zum Schutz von Daten keine erstklassige API zum Ausführen des Vorgangs.

## <a name="default-key-selection"></a>Wichtige Standardauswahl

Wenn das System zum Schutz von Daten den Schlüsselbund aus dem Repository dahinter liegende liest, wird versucht, einen Schlüssel "Standard" aus dem Schlüsselbund gesucht werden soll. Der Standardschlüssel wird für neue schützen Vorgänge verwendet.

Die allgemeine Heuristik ist, dass das System zum Schutz von Daten über die Taste mit dem letzten Aktivierungsdatum als Standardschlüssel auswählt. (Es ist eine kleine Mogelfaktor, um Server-zu-Server Uhr datenschiefe zu ermöglichen.) Wenn der Schlüssel ist abgelaufen oder gesperrt, und wenn die Anwendung nicht automatisch deaktiviert hat schlüsselgenerierung, und klicken Sie dann ein neuer Schlüssel mit unmittelbaren Aktivierung pro generiert werden die [für den Ablauf und parallele](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management-expiration) Richtlinie weiter unten.

Der Grund System zum Schutz von Daten generiert einen neuen Schlüssel sofort statt Fallback auf einen anderen Schlüssel besteht darin, dass die Generierung eines neuen Schlüssels als eine implizite Ablauf aller Schlüssel behandelt werden sollen, die vor dem neuen Schlüssel aktiviert wurden. Der Grundgedanke ist, dass neue Schlüssel mit verschiedenen Algorithmen oder Verschlüsselung für ruhende-Mechanismen als alten Schlüssel konfiguriert wurden können, und das System vorziehen sollten, dass die aktuelle Konfiguration zurückgreifen.

Es ist eine Ausnahme aus. Wenn der Anwendungsentwickler kann [deaktiviert die automatische Generierung von Schlüsseln](xref:security/data-protection/configuration/overview#disableautomatickeygeneration), und klicken Sie dann das System zum Schutz von Daten etwas als Standardschlüssel auswählen muss. In diesem Szenario fallback wird das System den Schlüssel nicht widerrufen, mit dem das letzte Aktivierungsdatum, wählen Sie eingeräumt an Schlüsseln, die mit anderen Computern im Cluster verteilt werden mussten. Das fallback-System kann am Ende einer abgelaufenen Standardschlüssel daher auswählen. Das Fallbacksystem wählt nie einen widerrufenen Schlüssel als den Standardschlüssel, und wenn dem Schlüsselbund leer ist oder wurde jeder Schlüssel gesperrt klicken Sie dann das System tritt ein Fehler bei der Initialisierung.

<a name="data-protection-implementation-key-management-expiration"></a>

## <a name="key-expiration-and-rolling"></a>Schlüsselablauf und parallele

Wenn ein Schlüssel erstellt wird, erhält er automatisch ein Aktivierungs- und ein Ablaufdatum von {Now + 90 Tage} von {Now + 2 Tage}. Die Verzögerung von 2 Tagen vor der Aktivierung erhalten die Schlüsselzeit über das System eine Weile dauern. D. h. können sie andere Anwendungen, die auf den in den Sicherungsspeicher, beobachten Sie den Schlüssel an die nächste Aktualisierungsintervall, Maximieren daher die Wahrscheinlichkeit, dass wenn der Schlüssel des ringpufferziels werden wird aktiv, die Gruppe propagiert wurde für alle Anwendungen, die erforderlich sind verwendet werden.

Wenn der Standardschlüssel innerhalb von 2 Tagen ablaufen und den Schlüsselbund nicht bereits einen Schlüssel verfügen, der nach Ablauf des den Standardschlüssel aktiv sind, wird das System zum Schutz von Daten automatisch einen neuen Schlüssel, den Schlüsselbund beibehalten. Dieses neuen Schlüssels verfügt über ein Aktivierungs- und Ablaufdatum {Now + 90 Tage} {Ablaufdatum der Standardschlüssel}. Dadurch kann es sich um das System die Schlüssel automatisch in regelmäßigen Abständen ohne Unterbrechung des Diensts zurückzusetzen.

Es gibt möglicherweise Situationen, in dem ein Schlüssel mit der sofortigen Aktivierung erstellt wird. Ein Beispiel wäre, wenn die Anwendung noch nicht für einen Zeitraum ausgeführt, und alle Schlüssel in den Schlüsselbund abgelaufen sind. In diesem Fall erhält der Schlüssel ein Datum der Aktivierung von {jetzt} ohne die normalen aktivierungsverzögerung von 2 Tagen.

Die Lebensdauer der Standard-Schlüssel ist 90 Tage lang auf, wenn dies konfiguriert ist, wie im folgenden Beispiel ist.

```csharp
services.AddDataProtection()
       // use 14-day lifetime instead of 90-day lifetime
       .SetDefaultKeyLifetime(TimeSpan.FromDays(14));
```

Ein Administrator kann auch standardmäßig eine systemweite, ändern, wenn ein expliziter Aufruf von `SetDefaultKeyLifetime` wird Richtlinie für eine systemweite außer Kraft gesetzt. Die Lebensdauer der Standard-Schlüssel darf nicht weniger als 7 Tage sein.

## <a name="automatic-key-ring-refresh"></a>Automatische Schlüsselbund aktualisieren

Wenn das System zum Schutz von Daten initialisiert wird, liest den Schlüsselbund aus dem zugrunde liegenden Repository und im Arbeitsspeicher zwischengespeichert. Dieser Cache ermöglicht schützen und Unprotect-Vorgänge um den Vorgang fortzusetzen, ohne dass dafür auf den Sicherungsspeicher an. Der Sicherungsspeicher für Änderungen wird von vom System automatisch geprüft werden, ungefähr 24 Stunden oder wenn der aktuelle Standardschlüssel abläuft, je nachdem, was zuerst eintritt.

>[!WARNING]
> Entwickler sollten nur sehr selten (wenn überhaupt) die Schlüssel-APIs direkt verwenden müssen. Das System zum Schutz von Daten führt automatische schlüsselverwaltung, wie oben beschrieben.

Das System zum Schutz von Daten stellt eine Schnittstelle `IKeyManager` , die verwendet werden kann, um zu überprüfen, und nehmen Sie Änderungen an den Schlüsselbund. Das DI-System, das die Instanz angegebenen `IDataProtectionProvider` bieten auch eine Instanz von `IKeyManager` für den Verbrauch. Alternativ können Sie abrufen, die `IKeyManager` direkt aus der `IServiceProvider` wie im folgenden Beispiel.

Jeder Vorgang, der bestimmt, den Schlüsselbund (einen neuen Schlüssel explizit erstellen oder Ausführen einer Sperrung) wird in-Memory-Cache ungültig. Beim nächsten Aufruf von `Protect` oder `Unprotect` bewirkt, dass das System zum Schutz von Daten gebundenes den Schlüsselbund und neu erstellen, den Cache.

Das folgende Beispiel veranschaulicht die Verwendung der `IKeyManager` Schnittstelle, um zu überprüfen und ändern den Schlüsselbund, einschließlich entziehen vorhandenen Schlüssel und einen neuen Schlüssel manuell zu generieren.

[!code-csharp[](key-management/samples/key-management.cs)]

## <a name="key-storage"></a>Speichern von Schlüsseln

Das System zum Schutz von Daten verfügt über eine Heuristik, bei dem versucht wird, einen entsprechenden Schlüssel Speicherort und die Verschlüsselung im Ruhezustand Methode automatisch hergeleitet werden. Der schlüsselpersistenz-Mechanismus kann auch vom app-Entwickler konfiguriert werden. In den folgenden Dokumenten erläutert die integrierten Implementierungen dieser Mechanismen:

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>
