---
title: Zweck Zeichenfolgen in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Zweck Zeichenfolgen in den ASP.net Core Datenschutz-APIs verwendet werden.
ms.author: riande
ms.date: 10/14/2016
uid: security/data-protection/consumer-apis/purpose-strings
ms.openlocfilehash: 4c85423f8de7e4b784ae1bb304a884541df251b6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654001"
---
# <a name="purpose-strings-in-aspnet-core"></a>Zweck Zeichenfolgen in ASP.net Core

<a name="data-protection-consumer-apis-purposes"></a>

Komponenten, die `IDataProtectionProvider` nutzen, müssen einen eindeutigen *Verwendungs* Parameter an die `CreateProtector`-Methode übergeben. Der *Parameter* "zwecks" ist der Sicherheit des Datenschutzsystems, da es die Isolation zwischen kryptografieconsumern ermöglicht, auch wenn die Stamm Kryptografieschlüssel identisch sind.

Wenn ein Consumer einen Zweck angibt, wird die Zweck Zeichenfolge zusammen mit den Stamm-Kryptografieschlüsseln verwendet, um kryptografische Unterschlüssel abzuleiten, die für diesen Consumer eindeutig sind. Dadurch wird der Consumer von allen anderen kryptografieverbrauchern in der Anwendung isoliert: keine andere Komponente kann seine Nutzlasten lesen, und er kann keine Nutzlasten anderer Komponenten lesen. Diese Isolation rendert außerdem alle möglichen Angriffs Kategorien für die Komponente.

![Beispiel für Zweck Diagramm](purpose-strings/_static/purposes.png)

In der obigen Abbildung können `IDataProtector` Instanzen A und B **nicht** die Nutzlasten anderer Benutzer, sondern nur Ihre eigenen Nutzlasten lesen.

Die Zweck Zeichenfolge muss nicht geheim sein. Es sollte einfach so eindeutig sein, dass keine andere gut verhaltene Komponente jemals die gleiche Zweck Zeichenfolge bereitstellen wird.

>[!TIP]
> Die Verwendung des Namespace und Typnamens der Komponente, die die Datenschutz-APIs nutzt, ist eine gute Faustregel, da diese Informationen in der Praxis niemals in Konflikt stehen.
>
>Eine von Configuration Manager erstellte Komponente, die für das Minting von bearertoken zuständig ist, kann "" als Zweck Zeichenfolge "" "" als Zweck Zeichenfolge verwenden. Oder-noch besser: Es kann "" als Zweck Zeichenfolge "" verwendet werden. Das Anfügen der Versionsnummer ermöglicht einer zukünftigen Version die Verwendung von "fitoso. Security. bearertoken. v2" als Zweck, und die verschiedenen Versionen werden vollständig voneinander isoliert, soweit Nutzlasten erfolgen.

Da der Parameters "zwecks" `CreateProtector` ein Zeichen folgen Array ist, kann der obige Wert stattdessen als `[ "Contoso.Security.BearerToken", "v1" ]`angegeben werden. Dies ermöglicht das Einrichten einer Hierarchie von Zwecken und eröffnet die Möglichkeit von Szenarien mit mehreren Mandanten mit dem Datenschutzsystem.

<a name="data-protection-contoso-purpose"></a>

>[!WARNING]
> Komponenten sollten nicht vertrauenswürdige Benutzereingaben nicht als alleinige Quelle für die Eingabe Quelle für die Zweck Kette zulassen.
>
>Stellen Sie sich z. b. eine Komponente "" "" ". Wenn die Secure Messaging-Komponente `CreateProtector([ username ])`aufruft, kann ein böswilliger Benutzer ein Konto mit dem Benutzernamen "" `CreateProtector([ "Contoso.Security.BearerToken" ])`, "", "" "" "", "" mit dem Benutzernamen "kontoso. Security. bearertoken".
>
>Eine bessere Verwendungs Kette für die Messaging Komponente wäre `CreateProtector([ "Contoso.Messaging.SecureMessage", "User: username" ])`, die eine ordnungsgemäße Isolation bereitstellt.

Die von und Verhaltensweisen `IDataProtectionProvider`, `IDataProtector`und Zwecke bereitgestellte Isolation lauten wie folgt:

* Für ein bestimmtes `IDataProtectionProvider` Objekt erstellt die `CreateProtector`-Methode ein `IDataProtector` Objekt, das eindeutig an das `IDataProtectionProvider` Objekt gebunden ist, von dem es erstellt wurde, und den-Parameter, der an die-Methode übergeben wurde.

* Der Zweck Parameter darf nicht NULL sein. (Wenn Zwecke als Array angegeben wird, bedeutet dies, dass das Array nicht die Länge 0 (null) aufweisen darf und dass alle Elemente des Arrays ungleich NULL sein müssen.) Ein leerer Zeichen folgen Zweck ist technisch zulässig, wird jedoch nicht empfohlen.

* Zwei Zweck Argumente sind nur dann gleichwertig, wenn Sie dieselben Zeichen folgen (mit einem Ordinalvergleich) in derselben Reihenfolge enthalten. Ein Argument mit einem einzelnen Zweck entspricht dem entsprechenden Array von Einzelelement Zwecken.

* Zwei `IDataProtector` Objekte sind gleichwertig, wenn Sie aus äquivalenten `IDataProtectionProvider` Objekten mit entsprechenden Parametern erstellt werden.

* Für ein bestimmtes `IDataProtector` Objekt gibt ein Aufruf`Unprotect(protectedData)` den ursprünglichen `unprotectedData` zurück, wenn `protectedData := Protect(unprotectedData)` für ein entsprechendes `IDataProtector` Objekt ist.

> [!NOTE]
> Der Fall, in dem eine Komponente absichtlich eine Zweck Zeichenfolge auswählt, die bekanntermaßen mit einer anderen Komponente in Konflikt steht, wird nicht berücksichtigt. Eine solche Komponente würde im Grunde als schädlich angesehen werden, und dieses System ist nicht für die Bereitstellung von Sicherheitsgarantien gedacht, wenn bereits bösartiger Code innerhalb des Arbeitsprozesses ausgeführt wird.
