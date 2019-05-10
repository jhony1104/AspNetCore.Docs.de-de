---
title: Unveränderlichkeit von Schlüsseln und wichtige Einstellungen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, die Implementierungsdetails der ASP.NET Core-Datenschutz Unveränderlichkeit von Schlüsseln APIs.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/implementation/key-immutability
ms.openlocfilehash: 7796cb102c0f6f03809704016fd36b28c7a82438
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892287"
---
# <a name="key-immutability-and-key-settings-in-aspnet-core"></a>Unveränderlichkeit von Schlüsseln und wichtige Einstellungen in ASP.NET Core

Nachdem ein Objekt in den Sicherungsspeicher beibehalten wird, ist für immer seine Darstellung fest. Der Sicherungsspeicher können neue Daten hinzugefügt werden, aber vorhandene Daten können nicht geändert werden. Der Hauptzweck dieses Verhalten ist um datenbeschädigung zu verhindern.

Ein Vorteil der dieses Verhalten ist, dass nachdem ein Schlüssel in den Sicherungsspeicher geschrieben wurde, unveränderlich ist. Die Erstellung, Aktivierung und Ablauf Datumsangaben nie geändert werden können, obwohl es widerrufen können, mithilfe von `IKeyManager`. Darüber hinaus sind die zugrunde liegenden algorithmische Informationen Schlüsselmaterial und Rest-Eigenschaften für die Verschlüsselung auch unveränderlich.

Wenn der Entwickler eine Einstellung geändert wird, die wichtige Persistenz wirkt sich auf, diese Änderungen wird nicht wirksam, bis das nächste Mal ein Schlüssel generiert wird, entweder über ein expliziter Aufruf von `IKeyManager.CreateNewKey` oder über die Datensystem zum Schutz des eigenen [automatische Schlüssel Generation](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management) Verhalten. Die Einstellungen, die beeinflussen schlüsselpersistenz lauten wie folgt aus:

* [Die Lebensdauer der Standard-Schlüssel](xref:security/data-protection/implementation/key-management#data-protection-implementation-key-management)

* [Rest-Mechanismus für die wichtigsten Verschlüsselung](xref:security/data-protection/implementation/key-encryption-at-rest)

* [Algorithmische Informationen innerhalb der Schlüssel](xref:security/data-protection/configuration/overview#changing-algorithms-with-usecryptographicalgorithms)

Wenn Sie diese Einstellungen vor der nächsten automatische schlüsselrollover Mal aktiviert wird, empfiehlt sich ein expliziter Aufruf an `IKeyManager.CreateNewKey` um die Erstellung eines neuen Schlüssels zu erzwingen. Denken Sie daran, eine explizite Aktivierung bietet ({jetzt + 2 Tage} ist eine gute Faustregel damit Zeit für die Änderung weitergegeben werden) und das Ablaufdatum im Aufruf.

>[!TIP]
> Alle Anwendungen, die durch berühren der Repository sollte mit die gleichen Einstellungen geben die `IDataProtectionBuilder` Erweiterungsmethoden. Andernfalls werden die Eigenschaften des permanenten Schlüssels abhängig von der betreffenden Anwendung sein, die die schlüsselgenerierung Routinen aufgerufen.
