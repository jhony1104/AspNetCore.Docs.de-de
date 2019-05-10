---
title: Überlegungen zum Entwurf der SignalR-API
author: anurse
description: Erfahren Sie, wie SignalR-APIs für die Kompatibilität zwischen Versionen Ihrer App zu entwerfen.
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: mvc
ms.date: 11/06/2018
uid: signalr/api-design
ms.openlocfilehash: 3f17bf055b793e8fc91fbcc15f668928ca261f77
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897807"
---
# <a name="signalr-api-design-considerations"></a>Überlegungen zum Entwurf der SignalR-API

Durch [Andrew Stanton-Nurse](https://twitter.com/anurse)

Dieser Artikel enthält Anleitungen zum Erstellen von SignalR-basierten APIs.

## <a name="use-custom-object-parameters-to-ensure-backwards-compatibility"></a>Verwenden Sie benutzerdefiniertes Objekt-Parameter, um Abwärtskompatibilität zu gewährleisten.

Hinzufügen von Parametern an eine SignalR-Hub-Methode (auf dem Client oder Server) ist eine *bedeutende Änderung*. Dies bedeutet, dass ältere Clients oder Server Fehler erhalten, wenn sie versuchen, die ohne die entsprechende Anzahl von Parametern für Methodenaufrufe. Allerdings ist das Hinzufügen von Eigenschaften für einen Parameter des benutzerdefinierten Objekts **nicht** eine unterbrechende Änderung. Dies kann verwendet werden, zum Entwerfen von kompatiblen APIs, die auf Änderungen auf dem Client oder Server stabil sind.

Betrachten Sie beispielsweise eine serverseitige API wie folgt aus:

[!code-csharp[ParameterBasedOldVersion](api-design/sample/Samples.cs?name=ParameterBasedOldVersion)]

Der JavaScript-Client ruft diese Methode mit `invoke` wie folgt:

[!code-typescript[CallWithOneParameter](api-design/sample/Samples.ts?name=CallWithOneParameter)]

Wenn Sie später einen zweiten Parameter an die Servermethode hinzufügen, wird nicht ältere Clients diesen Parameterwert angeben. Zum Beispiel:

[!code-csharp[ParameterBasedNewVersion](api-design/sample/Samples.cs?name=ParameterBasedNewVersion)]

Wenn der alte Client versucht, diese Methode aufzurufen, erhalten sie eine Fehlermeldung wie folgt:

```
Microsoft.AspNetCore.SignalR.HubException: Failed to invoke 'GetTotalLength' due to an error on the server.
```

Auf dem Server sehen Sie eine protokollmeldung wie folgt:

```
System.IO.InvalidDataException: Invocation provides 1 argument(s) but target expects 2.
```

Die alten Clients nur einen Parameter gesendet, aber der neuere API-Server erforderlich, zwei Parameter. Verwenden von benutzerdefinierten Objekten als Parameter können Sie flexibler. Wir ändern die original-API, um ein benutzerdefiniertes Objekt zu verwenden:

[!code-csharp[ObjectBasedOldVersion](api-design/sample/Samples.cs?name=ObjectBasedOldVersion)]

Nun verwendet der Client ein Objekt zum Aufrufen der Methode:

[!code-typescript[CallWithObject](api-design/sample/Samples.ts?name=CallWithObject)]

Anstatt einen Parameter hinzufügen, fügen Sie eine Eigenschaft, um die `TotalLengthRequest` Objekt:

[!code-csharp[ObjectBasedNewVersion](api-design/sample/Samples.cs?name=ObjectBasedNewVersion&highlight=4,9-13)]

Wenn die alten Clients sendet einen einzelnen Parameter, die zusätzlichen `Param2` Eigenschaft wechselt `null`. Sie können erkennen, dass eine Nachricht von einem älteren Client gesendet werden, indem Sie überprüfen die `Param2` für `null` und Standardwert anwenden. Ein neuer Client kann beide Parameter senden.

[!code-typescript[CallWithObjectNew](api-design/sample/Samples.ts?name=CallWithObjectNew)]

Dieselbe Vorgehensweise funktioniert für Methoden, die auf dem Client definiert. Sie können ein benutzerdefiniertes Objekt von der Serverseite senden:

[!code-csharp[ClientSideObjectBasedOld](api-design/sample/Samples.cs?name=ClientSideObjectBasedOld)]

Klicken Sie auf der Clientseite, in dem Sie Zugriff auf die `Message` -Eigenschaft, anstatt einen Parameter:

[!code-typescript[OnWithObjectOld](api-design/sample/Samples.ts?name=OnWithObjectOld)]

Wenn Sie später die Nutzlast der Absender der Nachricht hinzu, fügen Sie auf das Objekt eine Eigenschaft hinzu:

[!code-csharp[ClientSideObjectBasedNew](api-design/sample/Samples.cs?name=ClientSideObjectBasedNew&highlight=5)]

Die älteren Clients wird nicht erwartet werden die `Sender` Wert, sodass sie es ignoriert werden. Ein neuer Client können sie übernehmen, indem Sie aktualisiert, um die neue Eigenschaft zu lesen:

[!code-typescript[OnWithObjectNew](api-design/sample/Samples.ts?name=OnWithObjectNew&highlight=2-5)]

Der neue Client ist in diesem Fall auch von einer alten Server, die nicht zur Verfügung stellt, fehlertolerante der `Sender` Wert. Da es sich bei der alte Server sind die `Sender` Wert, der Client überprüft, um festzustellen, ob es vorhanden ist, bevor Sie darauf zugreifen.
