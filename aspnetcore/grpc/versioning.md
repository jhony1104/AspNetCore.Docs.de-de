---
title: Versionierung von GrpC-Diensten
author: jamesnk
description: Erfahren Sie, wie Sie GrpC-Dienste in Version
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 01/09/2020
uid: grpc/versioning
ms.openlocfilehash: 61dd5dfca4064af3ae58ac288a1ee636450ff56b
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75694193"
---
# <a name="versioning-grpc-services"></a>Versionierung von GrpC-Diensten

Von [James Newton-King](https://twitter.com/jamesnk)

Neue Features, die einer app hinzugefügt werden, können für Clients bereitgestellte GrpC-Dienste erfordern, was manchmal auf unerwartete und unterbrechende Weise der Fall ist. Wenn sich die GrpC-Dienste ändern:

* Beachten Sie, welche Auswirkungen Änderungen auf Clients haben.
* Eine Versions Verwaltungs Strategie zur Unterstützung von Änderungen sollte implementiert werden.

## <a name="backwards-compatibility"></a>Abwärtskompatibilität

Das GrpC-Protokoll dient zur Unterstützung von Diensten, die sich im Laufe der Zeit ändern. Allgemeine Ergänzungen zu den GrpC-Diensten und-Methoden sind nicht unterbrechend. Nicht unterbrechend bedeutet, dass vorhandene Clients weiterhin funktionieren. Das ändern oder Löschen von GrpC-Diensten sind wichtige Änderungen. Wichtige Änderungen bedeuten, dass vorhandene Clients fehlschlagen.

Das vornehmen von nicht wichtigen Änderungen an einem Dienst hat eine Reihe von Vorteilen:

- Vorhandene Clients werden weiterhin ausgeführt.
- Vermeidet die Arbeit bei der Benachrichtigung von Clients über wichtige Änderungen und deren Aktualisierung.
- Es muss nur eine Version des Dienstanbieter dokumentiert und gewartet werden.

Dieser Inhalt konzentriert sich darauf, ob Änderungen **unter dem GrpC-Protokoll und dem .net-Binär Kompatibilitäts Grad unterbrechen**. Wenn Sie Änderungen vornehmen, sollten Sie überprüfen, ob ältere Clients logisch weiter arbeiten können. Beispiel: Hinzufügen eines neuen Felds zu einer Anforderungs Nachricht:

* Ist kein Protokoll Breaking Change.
* Wenn Sie einen Fehlerstatus auf dem Server zurückgeben, wenn das neue Feld nicht festgelegt ist, ist es ein Breaking Change für alte Clients.

### <a name="non-breaking-changes"></a>Geringfügige Änderungen

Diese Änderungen sind nicht unterbrechend auf der GrpC-Protokollebene und der .net-Binär Ebene.

- **Hinzufügen eines neuen Dienstanbieter**
- **Hinzufügen einer neuen Methode zu einem Dienst**
- **Hinzufügen eines Felds zu einer Anforderungs Nachricht** : Felder, die einer Anforderungs Nachricht hinzugefügt werden, werden auf dem Server mit dem [Standardwert](https://developers.google.com/protocol-buffers/docs/proto3#default) deserialisiert, wenn Sie nicht festgelegt sind. Damit der Dienst nicht unterbrochen wird, muss der Dienst erfolgreich ausgeführt werden, wenn er nicht von älteren Clients festgelegt wird.
- **Hinzufügen eines Felds zu einer Antwortnachricht** : Felder, die einer Antwortnachricht hinzugefügt werden, werden in die Auflistung [unbekannter Felder](https://developers.google.com/protocol-buffers/docs/proto3#unknowns) der Nachricht auf dem Client deserialisiert.
- Das **Hinzufügen eines Werts zu** enumerationsenumerationen wird als numerischer Wert serialisiert. Neue Enumerationswerte werden auf dem Client auf den-Enumerationswert ohne Enumerationsnamen deserialisiert. Ältere Clients müssen ordnungsgemäß ausgeführt werden, wenn Sie den Wert erhalten und nicht ausstehen.

### <a name="binary-breaking-changes"></a>Binäre wichtige Änderungen

Die folgenden Änderungen sind auf der GrpC-Protokollebene nicht unterbrechend, aber der Client muss aktualisiert werden, wenn er auf den neuesten *. proto* -Vertrag oder die Client .NET-Assembly aktualisiert wird. Die binäre Kompatibilität ist wichtig, wenn Sie beabsichtigen, eine GrpC-Bibliothek in nuget zu veröffentlichen.

- Das **Entfernen von Feldwerten** aus einem entfernten Feld wird in die [unbekannten Felder](https://developers.google.com/protocol-buffers/docs/proto3#unknowns)der Nachricht deserialisiert. Dabei handelt es sich nicht um ein GrpC-Protokoll Breaking Change, aber der Client muss aktualisiert werden, wenn ein Upgrade auf den letzten Vertrag durchführt. Es ist wichtig, dass eine entfernte Feldnummer in Zukunft nicht versehentlich wieder verwendet wird. Eine Möglichkeit, dies sicherzustellen, ist das Angeben von gelöschten Feldnummern und Namen in der Nachricht mithilfe des [`reserved`](https://developers.google.com/protocol-buffers/docs/proto3#reserved) -Schlüssel Worts von protobuf.
- Das **Umbenennen von feldfeldnamen** wird nur in generiertem Code verwendet. Die Feldzahl wird verwendet, um Felder im Netzwerk zu identifizieren. Wenn ein Upgrade auf den letzten Vertrag durchführt, muss der Client aktualisiert werden.
- Das **Umbenennen von Nachrichten** Namen wird nicht im Netzwerk gesendet, sodass dies kein GrpC-Protokoll Breaking Change ist. der Client muss jedoch aktualisiert werden, wenn ein Upgrade auf den letzten Vertrag durchführt.
- Wenn Sie **csharp_namespace** ändernde `csharp_namespace` ändern, ändert sich der Namespace generierter .NET-Typen. Dabei handelt es sich nicht um ein GrpC-Protokoll Breaking Change, aber der Client muss aktualisiert werden, wenn ein Upgrade auf den letzten Vertrag durchführt.

### <a name="breaking-changes"></a>Breaking Changes

Dabei handelt es sich um Protokoll-und binäre wichtige Änderungen.

- **Ändern eines Feld Datentyps** : das Ändern des Datentyps eines Felds in einen nicht [kompatiblen Typ](https://developers.google.com/protocol-buffers/docs/proto3#updating) führt beim Deserialisieren der Nachricht zu Fehlern. Auch wenn der neue Datentyp kompatibel ist, ist es wahrscheinlich, dass der Client aktualisiert werden muss, um den neuen Typ zu unterstützen, wenn er auf den letzten Vertrag aktualisiert wird.
- **Ändern einer Feldzahl** : die Feldzahl wird verwendet, um Felder im Netzwerk zu identifizieren.
- **Umbenennen eines Pakets, eines dienstangangs oder einer Methode** : GrpC verwendet den Paketnamen, den Dienstnamen und den Methodennamen, um die URL zu erstellen. Der Client erhält einen *nicht implementierten* Status vom Server.
- **Entfernen eines Dienstes oder einer Methode** : der Client erhält einen *nicht implementierten* Status vom Server, wenn die entfernte Methode aufgerufen wird.

## <a name="version-number-services"></a>Versionsnummern Dienste

Dienste sollten eine Abwärtskompatibilität mit alten Clients anstreben. Letztendlich können Änderungen an Ihrer APP zu wichtigen Änderungen erforderlich sein. Das Unterbrechen von alten Clients und das Erzwingen der Aktualisierung zusammen mit Ihrem Dienst sind keine guten Benutzer Möglichkeiten. Eine Möglichkeit, die Abwärtskompatibilität zu gewährleisten, während wichtige Änderungen vorgenommen werden, besteht in der Veröffentlichung mehrerer Versionen eines Dienstanbieter.

GrpC unterstützt einen optionalen [`package`](https://developers.google.com/protocol-buffers/docs/proto3#packages) Spezifizierer, der ähnlich wie ein .NET-Namespace funktioniert. Tatsächlich wird der `package` als .NET-Namespace für generierte .NET-Typen verwendet, wenn `option csharp_namespace` nicht in der *. proto* -Datei festgelegt ist. Das Paket kann verwendet werden, um eine Versionsnummer für den Dienst und seine Nachrichten anzugeben:

[!code-protobuf[](versioning/sample/greet.v1.proto?highlight=3)]

Der Paketname wird mit dem Dienstnamen kombiniert, um eine Dienst Adresse zu identifizieren. Eine Dienst Adresse ermöglicht, dass mehrere Versionen eines Dienstanbieter nebeneinander gehostet werden:

* `greet.v1.Greeter`
* `greet.v2.Greeter`

Implementierungen des versionierten Dienstanbieter werden in *Startup.cs*registriert:

```csharp
app.UseEndpoints(endpoints =>
{
    // Implements greet.v1.Greeter
    endpoints.MapGrpcService<GreeterServiceV1>();

    // Implements greet.v2.Greeter
    endpoints.MapGrpcService<GreeterServiceV2>();
});
```

Wenn Sie eine Versionsnummer in den Paketnamen einschließen, haben Sie die Möglichkeit, eine *v2* -Version Ihres Dienes mit wichtigen Änderungen zu veröffentlichen, während ältere Clients, die die *v1* -Version anrufen, weiterhin unterstützt werden. Nachdem die Clients so aktualisiert wurden, dass Sie den *v2* -Dienst verwenden, können Sie die alte Version entfernen. Beim Planen der Veröffentlichung mehrerer Versionen eines Dienstanbieter:

- Vermeiden Sie bei angemessener Änderung wichtige Änderungen.
- Aktualisieren Sie die Versionsnummer nicht, wenn Sie keine wichtigen Änderungen vornehmen.
- Aktualisieren Sie die Versionsnummer, wenn Sie wichtige Änderungen vornehmen.

Durch das Veröffentlichen mehrerer Versionen eines dienstanvers wird dieses dupliziert. Um die Duplizierung zu reduzieren, erwägen Sie, die Geschäftslogik von den Dienst Implementierungen an einen zentralisierten Speicherort zu verschieben, der von den alten und neuen Implementierungen verwendet werden kann:

[!code-csharp[](versioning/sample/GreeterServiceV1.cs?highlight=10,19)]

Dienste und Nachrichten, die mit verschiedenen Paketnamen generiert werden, sind **unterschiedliche .NET-Typen** Das Verschieben von Geschäftslogik an einen zentralisierten Speicherort erfordert die Zuordnung von Nachrichten zu allgemeinen Typen.
