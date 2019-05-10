---
title: Zweckhierarchie und mehrinstanzenfähigkeit in ASP.NET Core
author: rick-anderson
description: Lernen Sie Zeichenfolge zweckhierarchie und mehrinstanzenfähigkeit in Bezug auf ASP.NET Core Datenschutz-APIs.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings-multitenancy
ms.openlocfilehash: 1133d40e7b325d58b3f70e7387494dae36ff8ac9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896797"
---
# <a name="purpose-hierarchy-and-multi-tenancy-in-aspnet-core"></a>Zweckhierarchie und mehrinstanzenfähigkeit in ASP.NET Core

Da ein `IDataProtector` ist auch implizit eine `IDataProtectionProvider`, Zwecke können miteinander verkettet werden. In diesem Sinn `provider.CreateProtector([ "purpose1", "purpose2" ])` entspricht `provider.CreateProtector("purpose1").CreateProtector("purpose2")`.

Dies ermöglicht einige interessante hierarchischen Beziehungen mithilfe von System zum Schutz von Daten. Im früheren Beispiel [Contoso.Messaging.SecureMessage](xref:security/data-protection/consumer-apis/purpose-strings#data-protection-contoso-purpose), kann die Komponente SecureMessage Aufrufen `provider.CreateProtector("Contoso.Messaging.SecureMessage")` einmal Investitionen und Zwischenspeicherung des Ergebnisses in einer privaten `_myProvider` Feld. Zukünftige Schutzvorrichtungen können dann über Aufrufe von erstellt werden `_myProvider.CreateProtector("User: username")`, und diese Schutzvorrichtungen für die einzelnen Nachrichten sichern, eingesetzt werden.

Dies kann auch umgekehrt werden. Betrachten Sie eine einzelne logische welche Hosts, die mehrere Mandanten (ein CMS scheint vernünftig), und jeder Mandant mit seinem eigenen Authentifizierungs- und Status Management-System konfiguriert werden können. Die Kategorie-Anwendung verfügt über einen einzelnen master-Anbieter, und er ruft `provider.CreateProtector("Tenant 1")` und `provider.CreateProtector("Tenant 2")` jeder Mandant einen eigenen isolierten Segment System zum Schutz von Daten gewähren. Die Mandanten können leiten Sie ihre eigenen einzelnen Schutzvorrichtungen auf Grundlage seiner eigenen Anforderungen, aber unabhängig davon, wie sehr sie versuchen können nicht diese Schutzvorrichtungen, die in Konflikt stehen mit keinem anderen Mandanten erstellen, im System. Dies wird grafisch dargestellt, wie im folgenden dargestellt.

![Mit mehreren Mandanten zu](purpose-strings-multitenancy/_static/purposes-multi-tenancy.png)

>[!WARNING]
> Dies setzt voraus die Schirm anwendungssteuerung, welche APIs für einzelne Mandanten verfügbar sind und dass es sich bei Mandanten beliebigen Code auf dem Server nicht ausgeführt werden können. Verlangen diese Schritte aus, wenn ein Mandant kann willkürlichen Code ausführen, sie private Reflektion, um die Isolation Garantien zu durchbrechen ausführen oder sie einfach das Schlüsselmaterial direkt gelesen und leiten Sie beliebige Unterschlüssel konnte können.

Das System zum Schutz von Daten verwendet eine Sortierung der mehrinstanzenfähigkeit in der Standardkonfiguration für die Out-of-the-Box. Standardmäßig wird die Schlüsselmaterial in das Workerprozesskonto Benutzerprofilordner (oder der Registrierung für die IIS-Anwendungspoolidentitäten) gespeichert. Aber es ist tatsächlich ziemlich üblich, ein einzelnes Konto verwenden, um mehrere Anwendungen auszuführen, und daher alle diese Anwendungen würden am Ende den Master Schlüsselmaterial freigeben. Um dieses Problem zu lösen, fügt System zum Schutz von Daten automatisch einen Bezeichner der eindeutigen pro Anwendung als erstes Element in der Kette für allgemeine Zwecke. Implizite dazu dient, [einzelne Anwendungen isolieren](xref:security/data-protection/configuration/overview#per-application-isolation) voneinander, indem Sie jede Anwendung effektiv zu behandeln, wie ein eindeutige Mandanten innerhalb des Systems und der Prozess der Schutzvorrichtung identisch mit der obigen Abbildung dargestellt wird.
