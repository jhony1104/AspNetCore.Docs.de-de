---
title: Purpose-Zeichenfolgen in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Purpose-Zeichenfolgen in ASP.NET Core Datenschutz-APIs verwendet werden.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087530"
---
# <a name="purpose-strings-in-aspnet-core"></a>Purpose-Zeichenfolgen in ASP.NET Core

<a name="data-protection-consumer-apis-purposes"></a>

Komponenten, die nutzen `IDataProtectionProvider` muss einen eindeutigen bestehen *Zwecke* Parameter, um die `CreateProtector` Methode. Im Sinne *Parameter* ist dieses System zum Schutz von Daten, die Sicherheit Isolation zwischen kryptografischen Consumern, auch wenn die Stamm-kryptografischen Schlüssel identisch sind.

Wenn ein Consumer einen Zweck angegeben ist, wird die Zweck-Zeichenfolge zusammen mit der Stamm-Kryptografieschlüssel verwendet, um kryptografische Unterschlüssel eindeutig, kann der Kunde abgeleitet werden. Dadurch wird den Consumer vor allen anderen kryptografischen Consumern in der Anwendung isoliert: keine andere Komponente der Nutzlasten lesen kann, und keine andere Komponente-Nutzlasten gelesen. Diese Isolierung wird auch nicht realisierbare ganze Kategorien der Angriff auf die Komponente gerendert.

![Beispiel für ein allgemeines Diagramm](purpose-strings/_static/purposes.png)

In der Abbildung oben `IDataProtector` Instanzen A und B **kann nicht** gegenseitig-Nutzlasten für Lesen, nur ihre eigenen.

Die Zweck Zeichenfolge nicht geheim sein. Es sollte einfach insofern eindeutig sein, dass keine andere kaum Komponente immer die gleiche Zeichenfolge für den Zweck bereitstellen.

>[!TIP]
> Unter Verwendung des Namespace und Typ der Komponente, die die Datenschutz-APIs nutzen, ist eine gute Faustregel, wie in der Praxis, die diese Informationen nicht in Konflikt steht.
>
>Eine Contoso autorisierten-Komponente, die für die minting Bearer-Tokens zuständig ist, kann Contoso.Security.BearerToken als dessen Zweck Zeichenfolge verwenden. Oder – noch besser – sie können Contoso.Security.BearerToken.v1 als dessen Zweck-Zeichenfolge. Ermöglicht das Anfügen der Versionsnummer einer zukünftigen Version Contoso.Security.BearerToken.v2 als ihren Zweck zu verwenden, und die verschiedenen Versionen wäre vollständig voneinander isoliert, so weit Nutzlasten wechseln.

Seit den Zwecke-Parameter, um `CreateProtector` ist ein Zeichenfolgenarray der oben genannten konnte haben stattdessen angegeben wurde als `[ "Contoso.Security.BearerToken", "v1" ]`. Dies ermöglicht das Erstellen einer Hierarchie von Zwecken und eröffnet die Möglichkeit der mehrinstanzenfähigkeit-Szenarien mit dem System zum Schutz von Daten.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Komponenten sollten nicht nicht vertrauenswürdiger Benutzereingaben als einzige Quelle der Eingabe für die Kette zu ausgewählt werden können.
>
>Betrachten Sie beispielsweise eine Komponente Contoso.Messaging.SecureMessage, die für das Speichern von sicheren Nachrichten zuständig ist. Würde die sichere Messagingkomponente Aufrufen `CreateProtector([ username ])`, und klicken Sie dann ein böswilliger Benutzer ein Konto mit dem Benutzernamen "Contoso.Security.BearerToken" Fehler beim Abrufen der Komponente aufrufen, erstellen kann `CreateProtector([ "Contoso.Security.BearerToken" ])`, so unbeabsichtigt verursacht das secure messaging System Mint-Nutzlasten, die als Authentifizierungstoken wahrgenommen werden kann.
>
>Es wäre eine bessere Zwecke-Kette für die messaging-Komponente `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, die richtigen Isolation bietet.

Die Isolierung durch und das Verhalten der `IDataProtectionProvider`, `IDataProtector`, und Zwecke lauten wie folgt:

* Für eine angegebenen `IDataProtectionProvider` -Objekt, das `CreateProtector` Methode erstellt ein `IDataProtector` Objekt eindeutig gebunden werden, sowohl die `IDataProtectionProvider` Objekt, das erstellt hat und der Zweck-Parameter, der an die Methode übergeben wurde.

* Der Zweck-Parameter darf nicht null sein. (Wenn Zwecke als Bytearray angegeben ist, bedeutet dies, dass das Array nicht der Länge Null sein darf und alle Elemente des Arrays müssen ungleich Null sein.) Ein leere Zeichenfolge Zweck ist technisch zulässig, wird jedoch abgeraten.

* Argumente für zwei Zwecke sind gleichwertig, nur, wenn sie die gleichen Zeichenfolgen (einen ordinalen Vergleich mit) in der gleichen Reihenfolge enthalten. Ein Zweck-Argument ist das entsprechende begründungsarray von einem Element entspricht.

* Zwei `IDataProtector` Objekte sind äquivalent, wenn sie über entsprechende erstellt werden `IDataProtectionProvider` Objekte mit entsprechenden Zwecke-Parameter.

* Für einen bestimmten `IDataProtector` -Objekt, das einen Aufruf von `Unprotect(protectedData)` zurück, wird der ursprüngliche `unprotectedData` nur, wenn `protectedData := Protect(unprotectedData)` ein entsprechendes `IDataProtector` Objekt.

> [!NOTE]
> Wir sind nicht den Fall in Betracht ziehen, in denen eine Komponente absichtlich eine Zeichenfolge Zweck wählt die einen Konflikt mit einer anderen Komponente bezeichnet wird. Eine solche Komponente wird im Wesentlichen böswillige berücksichtigt werden, und dieses System ist nicht vorgesehen, Sicherheitsgarantien bereitstellen, die bösartiger Code innerhalb des Arbeitsprozesses bereits ausgeführt wird.
