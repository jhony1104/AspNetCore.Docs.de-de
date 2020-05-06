---
title: Zweck Hierarchie und mehr Instanzen Fähigkeit in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über die Zeichen folgen Hierarchie und die mehr Instanzen Fähigkeit in Bezug auf die ASP.net Core Datenschutz-APIs.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 73edb8082d2df263bc1e6d73fee1360fa6840514
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776772"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Zweck Hierarchie und mehr Instanzen Fähigkeit in ASP.net Core

Da ein `IDataProtector` auch implizit eine `IDataProtectionProvider`ist, können-Zwecke miteinander verkettet werden. In diesem Sinne `provider.CreateProtector([ "purpose1", "purpose2" ])` entspricht `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.

Dies ermöglicht einige interessante hierarchische Beziehungen durch das Datenschutzsystem. Im vorherigen Beispiel von " [fitoso. Messaging. securemessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose)" kann die securemessage-Komponente `provider.CreateProtector("Contoso.Messaging.SecureMessage")` einmal vorab aufrufen und das Ergebnis in einem privaten `_myProvider` Feld Zwischenspeichern. Zukünftige Schutzvorrichtungen können dann über Aufrufe von `_myProvider.CreateProtector("User: username")`erstellt werden, und diese Schutzvorrichtungen werden zum Sichern der einzelnen Nachrichten verwendet.

Dies kann auch gekippt werden. Stellen Sie sich eine einzige logische Anwendung vor, die mehrere Mandanten hostet (ein CMS erscheint vernünftig), und jeder Mandant kann mit einem eigenen Authentifizierungs-und Zustands Verwaltungssystem konfiguriert werden. Die Anwendung "Umbrella" verfügt über einen einzigen Master Anbieter und `provider.CreateProtector("Tenant 1")` ruft `provider.CreateProtector("Tenant 2")` und auf, um jedem Mandanten einen eigenen isolierten Slice des Datenschutzsystems zu übergeben. Die Mandanten können dann Ihre eigenen individuellen Schutzvorrichtungen basierend auf Ihren eigenen Anforderungen ableiten, aber unabhängig davon, wie schwierig Sie sind, können Sie keine Schutzvorrichtungen erstellen, die mit einem anderen Mandanten im System kollidieren. Grafisch dargestellt wird dies wie unten dargestellt.

![Mehr Instanzen Fähigkeit](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Dabei wird davon ausgegangen, dass die Anwendung steuert, welche APIs einzelnen Mandanten zur Verfügung stehen und dass Mandanten keinen beliebigen Code auf dem Server ausführen können. Wenn ein Mandant beliebigen Code ausführen kann, kann er eine private Reflektion durchführen, um die Isolations Garantien zu unterbrechen, oder er könnte einfach das Hauptschlüssel Material direkt lesen und die gewünschten Unterschlüssel ableiten.

Das Datenschutzsystem verwendet in seiner standardmäßigen Standardkonfiguration tatsächlich eine Art mehr Instanzen Fähigkeit. Standardmäßig wird das Masterschlüssel Material im Benutzerprofil Ordner des Arbeitsprozess Kontos (oder in der Registrierung für IIS-Anwendungs Pool Identitäten) gespeichert. Es ist jedoch ziemlich üblich, ein einzelnes Konto zum Ausführen mehrerer Anwendungen zu verwenden. Daher würden alle diese Anwendungen das Hauptmaterial für die schlüsselfreigabe freigeben. Um dieses Problem zu beheben, fügt das Datenschutzsystem automatisch einen eindeutigen Bezeichner für die Anwendung als erstes Element in der gesamten Ziel Kette ein. Dieser implizite Zweck dient dazu, [einzelne Anwendungen](xref:security/data-protection/configuration/overview#per-application-isolation) voneinander zu isolieren, indem jede Anwendung als eindeutiger Mandant innerhalb des Systems behandelt wird und der Erstellungs Prozess der Schutzvorrichtung mit der obigen Abbildung identisch aussieht.
