---
title: Verwenden des messagepack-Hub-Protokolls in signalr für ASP.net Core
author: bradygaster
description: Fügen Sie ASP.net Core signalr das messagepack-Hub-Protokoll hinzu.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 10/08/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: fe09b646eba5ae15cbd9e568b276aaf7763e4b1b
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165404"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a>Verwenden des messagepack-Hub-Protokolls in signalr für ASP.net Core

Von [Brennan](https://github.com/BrennanConroy) "a"

In diesem Artikel wird davon ausgegangen, dass der Reader mit den Themen in " [Get Started](xref:tutorials/signalr)" vertraut ist.

## <a name="what-is-messagepack"></a>Was ist messagepack?

[Messagepack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat. Dies ist hilfreich, wenn Leistung und Bandbreite wichtig sind, da im Vergleich zu [JSON](https://www.json.org/)kleinere Nachrichten erstellt werden. Da es sich um ein binäres Format handelt, sind Nachrichten bei der Betrachtung von Netzwerk Ablauf Verfolgungen und Protokollen nicht lesbar, es sei denn, die Bytes werden über einen messagepack-Parser übergeben Signalr verfügt über eine integrierte Unterstützung für das messagepack-Format und stellt APIs für den Client und den Server zur Verfügung.

## <a name="configure-messagepack-on-the-server"></a>Konfigurieren von messagepack auf dem Server

Um das messagepack-Hub-Protokoll auf dem Server zu aktivieren, installieren Sie das `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`-Paket in Ihrer APP. Fügen Sie in der Datei "Startup.cs" `AddMessagePackProtocol` zum `AddSignalR`-Befehl hinzu, um die Unterstützung von messagepack auf dem Server zu aktivieren.

> [!NOTE]
> JSON ist standardmäßig aktiviert. Das Hinzufügen von messagepack ermöglicht die Unterstützung für JSON-und messagepack-Clients.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Um die Art und Weise anzupassen, wie messagepack Ihre Daten formatiert, verwendet `AddMessagePackProtocol` einen Delegaten für die Konfiguration von Optionen. In diesem Delegaten kann die `FormatterResolvers`-Eigenschaft verwendet werden, um messagepack-Serialisierungsoptionen zu konfigurieren. Weitere Informationen zur Funktionsweise der Konflikt Löser finden Sie in der messagepack-Bibliothek unter [messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie Sie behandelt werden sollen.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurieren von messagepack auf dem Client

> [!NOTE]
> JSON ist für die unterstützten Clients standardmäßig aktiviert. Clients können nur ein einzelnes Protokoll unterstützen. Durch Hinzufügen der Unterstützung von messagepack werden alle zuvor konfigurierten Protokolle ersetzt.

### <a name="net-client"></a>.NET-Client

Um messagepack im .NET-Client zu aktivieren, installieren Sie das Paket `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`, und nennen Sie `AddMessagePackProtocol` auf `HubConnectionBuilder`.

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Dieser `AddMessagePackProtocol`-Aufruf verwendet einen Delegaten zum Konfigurieren von Optionen wie dem Server.

### <a name="javascript-client"></a>JavaScript-Client

::: moniker range=">= aspnetcore-3.0"

Die Unterstützung von messagepack für den JavaScript-Client wird durch das NPM-Paket `@microsoft/signalr-protocol-msgpack` bereitgestellt. Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Die Unterstützung von messagepack für den JavaScript-Client wird durch das NPM-Paket `@aspnet/signalr-protocol-msgpack` bereitgestellt. Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

Nach der Installation des NPM-Pakets kann das Modul direkt über ein JavaScript-Modul Lade Modul verwendet oder in den Browser importiert werden, indem Sie auf die folgende Datei verweisen:

::: moniker range=">= aspnetcore-3.0"

*node_modules @ no__t-1 @ no__t-2* 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

*node_modules @ no__t-1 @ no__t-2* 

::: moniker-end

In einem Browser muss auch auf die `msgpack5`-Bibliothek verwiesen werden. Verwenden Sie ein `<script>`-Tag, um einen Verweis zu erstellen. Die Bibliothek finden Sie unter *node_modules\msgpack5\dist\msgpack5.js*.

> [!NOTE]
> Wenn das `<script>`-Element verwendet wird, ist die Reihenfolge wichtig. Wenn auf *signalr-Protocol-msgpack. js* vor *msgpack5. js*verwiesen wird, tritt ein Fehler auf, wenn versucht wird, eine Verbindung mit messagepack herzustellen. *signalr. js* ist auch vor *signalr-Protocol-msgpack. js*erforderlich.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Beim Hinzufügen von `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` zur `HubConnectionBuilder` wird der Client für die Verwendung des messagepack-Protokolls konfiguriert, wenn eine Verbindung mit einem Server hergestellt wird.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Zurzeit gibt es keine Konfigurationsoptionen für das messagepack-Protokoll auf dem JavaScript-Client.

## <a name="messagepack-quirks"></a>Messagepack-Quiri

Bei der Verwendung des messagepack-Hub-Protokolls sind einige Aspekte zu beachten.

### <a name="messagepack-is-case-sensitive"></a>Beim messagepack wird die Groß-/Kleinschreibung beachtet

Beim messagepack-Protokoll wird die Groß-/Kleinschreibung beachtet. Sehen Sie sich beispielsweise die C# folgende Klasse an:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Beim Senden vom JavaScript-Client müssen Sie `PascalCased`-Eigenschaftsnamen verwenden, da die Groß-/Kleinschreibung genau mit der C# -Klasse übereinstimmen muss. Zum Beispiel:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Die Verwendung von `camelCased`-Namen wird nicht C# ordnungsgemäß an die-Klasse gebunden. Sie können dieses Problem umgehen, indem Sie das `Key`-Attribut verwenden, um einen anderen Namen für die messagepack-Eigenschaft anzugeben. Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime. Kind wird bei der Serialisierung/Deserialisierung nicht beibehalten.

Das messagepack-Protokoll bietet keine Möglichkeit, den `Kind`-Wert einer `DateTime` zu codieren. Demzufolge geht das messagepack-Hub-Protokoll beim Deserialisieren eines Datums davon aus, dass das eingehende Datum im UTC-Format vorliegt. Wenn Sie in der Ortszeit mit `DateTime`-Werten arbeiten, empfiehlt es sich, vor dem Senden in die UTC zu wechseln. Konvertieren Sie Sie von UTC in lokale Zeit, wenn Sie Sie empfangen.

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/signalr # 2632](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>"DateTime. MinValue" wird von messagepack in JavaScript nicht unterstützt.

Die vom signalr-JavaScript-Client verwendete [msgpack5](https://github.com/mcollina/msgpack5) -Bibliothek unterstützt den Typ "`timestamp96`" in messagepack nicht. Dieser Typ wird verwendet, um sehr große Datumswerte zu codieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft). Der Wert von `DateTime.MinValue` ist `January 1, 0001`, der in einem `timestamp96`-Wert codiert werden muss. Aus diesem Grund wird das Senden von `DateTime.MinValue` an einen JavaScript-Client nicht unterstützt. Wenn `DateTime.MinValue` vom JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In der Regel wird `DateTime.MinValue` verwendet, um einen "Missing"-oder `null`-Wert zu codieren. Wenn Sie diesen Wert in messagepack codieren müssen, verwenden Sie einen NULL@no__t able-Wert (`DateTime?`), oder codieren Sie einen separaten `bool`-Wert, der angibt, ob das Datum vorhanden ist.

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/signalr # 2228](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>Unterstützung von messagepack in der "Ahead-of-Time"-Kompilierungs Umgebung

Die vom .NET-Client und-Server verwendete [messagepack-CSharp-](https://github.com/neuecc/MessagePack-CSharp) Bibliothek verwendet die Codegenerierung zum Optimieren der Serialisierung. Daher wird Sie standardmäßig nicht in Umgebungen unterstützt, in denen die "vorab"-Kompilierung (z. b. xamarin IOS oder Unity) verwendet wird. Es ist möglich, das messagepack in diesen Umgebungen zu verwenden, indem der Serialisierungsprogramme-/Deserialisierungscode "vorab erstellt" wird. Weitere Informationen finden Sie in [der Dokumentation zu messagepack-CSharp](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports). Nachdem Sie die serialisierungsinitialisierer vorab generiert haben, können Sie Sie mit dem an `AddMessagePackProtocol` über gebenden Konfigurations Delegaten registrieren:

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>Typüberprüfungen sind in messagepack strenger.

Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch. Wenn das eingehende Objekt z. b. einen-Eigenschafts Wert hat, der eine Zahl (`{ foo: 42 }`) ist, aber die-Eigenschaft in der .NET-Klasse den Typ `string` aufweist, wird der Wert konvertiert. Allerdings führt messagepack diese Konvertierung nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der-Konsole) angezeigt wird:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub [-Problem ASPNET/signalr # 2937](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Verwandte Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)
