---
title: MessagePack Hub-Protokoll SignalR für ASP.NET Core verwenden
author: bradygaster
description: Fügen Sie MessagePack Hub SignalRProtocol zu ASP.NET Core hinzu.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277235"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a>MessagePack Hub-Protokoll SignalR für ASP.NET Core verwenden

::: moniker range=">= aspnetcore-5.0"

In diesem Artikel wird davon ausgegangen, dass der Leser mit den unter [Erste Schritte](xref:tutorials/signalr)behandelten Themen vertraut ist.

## <a name="what-is-messagepack"></a>Was ist MessagePack?

[MessagePack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat. Es ist nützlich, wenn Leistung und Bandbreite ein Problem darstellen, da es kleinere Nachrichten im Vergleich zu [JSON](https://www.json.org/)erzeugt. Die binären Nachrichten sind beim Betrachten von Netzwerkablaufverfolgungen und -protokollen nicht lesbar, es sei denn, die Bytes werden über einen MessagePack-Parser übergeben. SignalRhat integrierte Unterstützung für das MessagePack-Format und stellt APIs für den Client und Server bereit.

## <a name="configure-messagepack-on-the-server"></a>Konfigurieren von MessagePack auf dem Server

Um das MessagePack Hub-Protokoll auf `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` dem Server zu aktivieren, installieren Sie das Paket in Ihrer App. Fügen `Startup.ConfigureServices` Sie `AddMessagePackProtocol` in der `AddSignalR` Methode dem Aufruf hinzu, um die MessagePack-Unterstützung auf dem Server zu aktivieren.

> [!NOTE]
> JSON ist standardmäßig aktiviert. Das Hinzufügen von MessagePack ermöglicht die Unterstützung sowohl für JSON- als auch für MessagePack-Clients.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Um anzupassen, wie MessagePack `AddMessagePackProtocol` Ihre Daten formatiert, benötigt ein Delegates zum Konfigurieren von Optionen. In diesem Delegaten kann die `SerializerOptions` Eigenschaft zum Konfigurieren von MessagePack-Serialisierungsoptionen verwendet werden. Weitere Informationen zur Funktionsweise der Resolver finden Sie in der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie sie behandelt werden sollen.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden. Rufen Sie `.WithSecurity(MessagePackSecurity.UntrustedData)` z. `SerializerOptions`B. beim Ersetzen der auf.

## <a name="configure-messagepack-on-the-client"></a>Konfigurieren von MessagePack auf dem Client

> [!NOTE]
> JSON ist standardmäßig für die unterstützten Clients aktiviert. Clients können nur ein einzelnes Protokoll unterstützen. Durch das Hinzufügen der MessagePack-Unterstützung werden alle zuvor konfigurierten Protokolle ersetzt.

### <a name="net-client"></a>.NET-Client

Um MessagePack im .NET Client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` zu aktivieren, `HubConnectionBuilder`installieren Sie das Paket, und rufen Sie es auf. `AddMessagePackProtocol`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Dieser `AddMessagePackProtocol` Aufruf erfordert einen Delegaten zum Konfigurieren von Optionen genau wie der Server.

### <a name="javascript-client"></a>JavaScript-Client

Die MessagePack-Unterstützung für den JavaScript-Client wird vom [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm-Paket bereitgestellt. Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Nach der Installation des npm-Pakets kann das Modul direkt über einen JavaScript-Modullader verwendet oder in den Browser importiert werden, indem auf die folgende Datei verwiesen wird:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

In einem Browser `msgpack5` muss auch auf die Bibliothek verwiesen werden. Verwenden `<script>` Sie ein Tag, um einen Verweis zu erstellen. Die Bibliothek finden Sie unter *node_modules.msgpack5.dist-msgpack5.js*.

> [!NOTE]
> Bei der `<script>` Verwendung des Elements ist die Reihenfolge wichtig. Wenn *auf signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt beim Versuch, eine Verbindung mit MessagePack herzustellen, einen Fehler auf. *signalr.js* ist auch vor *signalr-protocol-msgpack.js*erforderlich.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Durch `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` Hinzufügen `HubConnectionBuilder` zu dem wird der Client so konfiguriert, dass er das MessagePack-Protokoll verwendet, wenn eine Verbindung mit einem Server hergestellt wird.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Derzeit gibt es keine Konfigurationsoptionen für das MessagePack-Protokoll auf dem JavaScript-Client.

## <a name="messagepack-quirks"></a>MessagePack Eigenheiten

Bei der Verwendung des MessagePack Hub-Protokolls sind einige Probleme zu beachten.

### <a name="messagepack-is-case-sensitive"></a>MessagePack berücksichtigt die Groß-/Kleinschreibung

Beim MessagePack-Protokoll wird die Groß-/Kleinschreibung beachtet. Betrachten Sie z. B. die folgende C-Klasse:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Beim Senden vom JavaScript-Client `PascalCased` müssen Sie Eigenschaftsnamen verwenden, da das Gehäuse genau mit der C-Klasse übereinstimmen muss. Beispiel:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Die `camelCased` Verwendung von Namen wird nicht ordnungsgemäß an die C-Klasse gebunden. Sie können dies umgehen, indem Sie das `Key` Attribut verwenden, um einen anderen Namen für die MessagePack-Eigenschaft anzugeben. Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind bleibt beim Serialisieren/Deserialisieren nicht erhalten

Das MessagePack-Protokoll bietet keine Möglichkeit, `Kind` den Wert `DateTime`einer zu codieren. Daher wird das MessagePack Hub-Protokoll beim Deserialisieren eines Datums `DateTime.Kind` in `DateTimeKind.Local` das UTC-Format konvertiert, wenn es andernfalls die Zeit nicht berührt und wie besehen wird. Wenn Sie mit `DateTime` Werten arbeiten, empfehlen wir, vor dem Senden in UTC zu konvertieren. Konvertieren Sie sie von UTC in Ortszeit, wenn Sie sie erhalten.

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue wird von MessagePack in JavaScript nicht unterstützt

Die [msgpack5-Bibliothek,](https://github.com/mcollina/msgpack5) die SignalR vom JavaScript-Client verwendet wird, unterstützt den `timestamp96` Typ in MessagePack nicht. Dieser Typ wird verwendet, um sehr große Datumswerte zu kodieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft). Der Wert `DateTime.MinValue` `January 1, 0001`von ist , der in `timestamp96` einem Wert codiert werden muss. Aus diesem Grund `DateTime.MinValue` wird das Senden an einen JavaScript-Client nicht unterstützt. Wenn `DateTime.MinValue` der JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In `DateTime.MinValue` der Regel wird verwendet, um `null` einen "fehlenden" oder Wert zu kodieren. Wenn Sie diesen Wert in MessagePack codieren `DateTime` müssen,`DateTime?`verwenden Sie einen `bool` nullablen Wert ( ) oder codieren Sie einen separaten Wert, der angibt, ob das Datum vorhanden ist.

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack-Unterstützung in der "Ahead-of-Time"-Kompilierungsumgebung

Die [MessagePack-CSharp-Bibliothek,](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) die vom .NET-Client und -Server verwendet wird, verwendet die Codegenerierung, um die Serialisierung zu optimieren. Daher wird es standardmäßig nicht für Umgebungen unterstützt, die die Kompilierung "vor der Zeit" verwenden (z. B. Xamarin iOS oder Unity). Es ist möglich, MessagePack in diesen Umgebungen zu verwenden, indem Sie den Serialisierungs-/Deserialisierungscode "vorgenerieren". Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin). Nachdem Sie die Serialisierungen vorgeneriert haben, können Sie sie `AddMessagePackProtocol`mit dem Konfigurationsdelegat registrieren, der an:

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a>Typprüfungen sind in MessagePack strenger

Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch. Wenn das eingehende Objekt beispielsweise über einen Eigenschaftswert`{ foo: 42 }`verfügt, der eine Zahl ( `string`) ist, die Eigenschaft der .NET-Klasse jedoch vom Typ ist, wird der Wert konvertiert. MessagePack führt diese Konvertierung jedoch nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der Konsole) angezeigt werden kann:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Verwandte Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

In diesem Artikel wird davon ausgegangen, dass der Leser mit den unter [Erste Schritte](xref:tutorials/signalr)behandelten Themen vertraut ist.

## <a name="what-is-messagepack"></a>Was ist MessagePack?

[MessagePack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat. Es ist nützlich, wenn Leistung und Bandbreite ein Problem darstellen, da es kleinere Nachrichten im Vergleich zu [JSON](https://www.json.org/)erzeugt. Die binären Nachrichten sind beim Betrachten von Netzwerkablaufverfolgungen und -protokollen nicht lesbar, es sei denn, die Bytes werden über einen MessagePack-Parser übergeben. SignalRbietet integrierte Unterstützung für das MessagePack-Format und stellt APIs für den Client und Server bereit.

## <a name="configure-messagepack-on-the-server"></a>Konfigurieren von MessagePack auf dem Server

Um das MessagePack Hub-Protokoll auf `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` dem Server zu aktivieren, installieren Sie das Paket in Ihrer App. Fügen `Startup.ConfigureServices` Sie `AddMessagePackProtocol` in der `AddSignalR` Methode dem Aufruf hinzu, um die MessagePack-Unterstützung auf dem Server zu aktivieren.

> [!NOTE]
> JSON ist standardmäßig aktiviert. Das Hinzufügen von MessagePack ermöglicht die Unterstützung sowohl für JSON- als auch für MessagePack-Clients.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Um anzupassen, wie MessagePack `AddMessagePackProtocol` Ihre Daten formatiert, benötigt ein Delegates zum Konfigurieren von Optionen. In diesem Delegaten kann die `FormatterResolvers` Eigenschaft zum Konfigurieren von MessagePack-Serialisierungsoptionen verwendet werden. Weitere Informationen zur Funktionsweise der Resolver finden Sie in der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie sie behandelt werden sollen.

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

> [!WARNING]
> Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden. Legen Sie z. B. die `MessagePackSecurity.Active` statische Eigenschaft auf `MessagePackSecurity.UntrustedData`fest. Das `MessagePackSecurity.Active` Festlegen der erfordert die manuelle Installation einer [1.9.x-Version von MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Die `MessagePack` Installation von 1.9.x aktualisiert die Version. SignalR Wenn `MessagePackSecurity.Active` nicht auf `MessagePackSecurity.UntrustedData`festgelegt ist, kann ein böswilliger Client einen Denial-of-Service verursachen. Setzen `MessagePackSecurity.Active` `Program.Main`Sie in ein, wie im folgenden Code gezeigt:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurieren von MessagePack auf dem Client

> [!NOTE]
> JSON ist standardmäßig für die unterstützten Clients aktiviert. Clients können nur ein einzelnes Protokoll unterstützen. Durch das Hinzufügen der MessagePack-Unterstützung werden alle zuvor konfigurierten Protokolle ersetzt.

### <a name="net-client"></a>.NET-Client

Um MessagePack im .NET Client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` zu aktivieren, `HubConnectionBuilder`installieren Sie das Paket, und rufen Sie es auf. `AddMessagePackProtocol`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Dieser `AddMessagePackProtocol` Aufruf erfordert einen Delegaten zum Konfigurieren von Optionen genau wie der Server.

### <a name="javascript-client"></a>JavaScript-Client

Die MessagePack-Unterstützung für den JavaScript-Client wird vom [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm-Paket bereitgestellt. Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

```bash
npm install @microsoft/signalr-protocol-msgpack
```

Nach der Installation des npm-Pakets kann das Modul direkt über einen JavaScript-Modullader verwendet oder in den Browser importiert werden, indem auf die folgende Datei verwiesen wird:

*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* 

In einem Browser `msgpack5` muss auch auf die Bibliothek verwiesen werden. Verwenden `<script>` Sie ein Tag, um einen Verweis zu erstellen. Die Bibliothek finden Sie unter *node_modules.msgpack5.dist-msgpack5.js*.

> [!NOTE]
> Bei der `<script>` Verwendung des Elements ist die Reihenfolge wichtig. Wenn *auf signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt beim Versuch, eine Verbindung mit MessagePack herzustellen, einen Fehler auf. *signalr.js* ist auch vor *signalr-protocol-msgpack.js*erforderlich.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Durch `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` Hinzufügen `HubConnectionBuilder` zu dem wird der Client so konfiguriert, dass er das MessagePack-Protokoll verwendet, wenn eine Verbindung mit einem Server hergestellt wird.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Derzeit gibt es keine Konfigurationsoptionen für das MessagePack-Protokoll auf dem JavaScript-Client.

## <a name="messagepack-quirks"></a>MessagePack Eigenheiten

Bei der Verwendung des MessagePack Hub-Protokolls sind einige Probleme zu beachten.

### <a name="messagepack-is-case-sensitive"></a>MessagePack berücksichtigt die Groß-/Kleinschreibung

Beim MessagePack-Protokoll wird die Groß-/Kleinschreibung beachtet. Betrachten Sie z. B. die folgende C-Klasse:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Beim Senden vom JavaScript-Client `PascalCased` müssen Sie Eigenschaftsnamen verwenden, da das Gehäuse genau mit der C-Klasse übereinstimmen muss. Beispiel:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Die `camelCased` Verwendung von Namen wird nicht ordnungsgemäß an die C-Klasse gebunden. Sie können dies umgehen, indem Sie das `Key` Attribut verwenden, um einen anderen Namen für die MessagePack-Eigenschaft anzugeben. Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind bleibt beim Serialisieren/Deserialisieren nicht erhalten

Das MessagePack-Protokoll bietet keine Möglichkeit, `Kind` den Wert `DateTime`einer zu codieren. Daher geht das MessagePack Hub-Protokoll bei der Deserialisierung eines Datums davon aus, dass das eingehende Datum im UTC-Format liegt. Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfehlen wir, vor dem Senden in UTC zu konvertieren. Konvertieren Sie sie von UTC in Ortszeit, wenn Sie sie erhalten.

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue wird von MessagePack in JavaScript nicht unterstützt

Die [msgpack5-Bibliothek,](https://github.com/mcollina/msgpack5) die SignalR vom JavaScript-Client verwendet wird, unterstützt den `timestamp96` Typ in MessagePack nicht. Dieser Typ wird verwendet, um sehr große Datumswerte zu kodieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft). Der Wert `DateTime.MinValue` `January 1, 0001`von ist , der in `timestamp96` einem Wert codiert werden muss. Aus diesem Grund `DateTime.MinValue` wird das Senden an einen JavaScript-Client nicht unterstützt. Wenn `DateTime.MinValue` der JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In `DateTime.MinValue` der Regel wird verwendet, um `null` einen "fehlenden" oder Wert zu kodieren. Wenn Sie diesen Wert in MessagePack codieren `DateTime` müssen,`DateTime?`verwenden Sie einen `bool` nullablen Wert ( ) oder codieren Sie einen separaten Wert, der angibt, ob das Datum vorhanden ist.

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack-Unterstützung in der "Ahead-of-Time"-Kompilierungsumgebung

Die [MessagePack-CSharp-Bibliothek,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) die vom .NET-Client und -Server verwendet wird, verwendet die Codegenerierung, um die Serialisierung zu optimieren. Daher wird es standardmäßig nicht für Umgebungen unterstützt, die die Kompilierung "vor der Zeit" verwenden (z. B. Xamarin iOS oder Unity). Es ist möglich, MessagePack in diesen Umgebungen zu verwenden, indem Sie den Serialisierungs-/Deserialisierungscode "vorgenerieren". Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Nachdem Sie die Serialisierungen vorgeneriert haben, können Sie sie `AddMessagePackProtocol`mit dem Konfigurationsdelegat registrieren, der an:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Typprüfungen sind in MessagePack strenger

Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch. Wenn das eingehende Objekt beispielsweise über einen Eigenschaftswert`{ foo: 42 }`verfügt, der eine Zahl ( `string`) ist, die Eigenschaft der .NET-Klasse jedoch vom Typ ist, wird der Wert konvertiert. MessagePack führt diese Konvertierung jedoch nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der Konsole) angezeigt werden kann:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Verwandte Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

In diesem Artikel wird davon ausgegangen, dass der Leser mit den unter [Erste Schritte](xref:tutorials/signalr)behandelten Themen vertraut ist.

## <a name="what-is-messagepack"></a>Was ist MessagePack?

[MessagePack](https://msgpack.org/index.html) ist ein schnelles und kompaktes binäres Serialisierungsformat. Es ist nützlich, wenn Leistung und Bandbreite ein Problem darstellen, da es kleinere Nachrichten im Vergleich zu [JSON](https://www.json.org/)erzeugt. Die binären Nachrichten sind beim Betrachten von Netzwerkablaufverfolgungen und -protokollen nicht lesbar, es sei denn, die Bytes werden über einen MessagePack-Parser übergeben. SignalRbietet integrierte Unterstützung für das MessagePack-Format und stellt APIs für den Client und Server bereit.

## <a name="configure-messagepack-on-the-server"></a>Konfigurieren von MessagePack auf dem Server

Um das MessagePack Hub-Protokoll auf `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` dem Server zu aktivieren, installieren Sie das Paket in Ihrer App. Fügen `Startup.ConfigureServices` Sie `AddMessagePackProtocol` in der `AddSignalR` Methode dem Aufruf hinzu, um die MessagePack-Unterstützung auf dem Server zu aktivieren.

> [!NOTE]
> JSON ist standardmäßig aktiviert. Das Hinzufügen von MessagePack ermöglicht die Unterstützung sowohl für JSON- als auch für MessagePack-Clients.

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

Um anzupassen, wie MessagePack `AddMessagePackProtocol` Ihre Daten formatiert, benötigt ein Delegates zum Konfigurieren von Optionen. In diesem Delegaten kann die `FormatterResolvers` Eigenschaft zum Konfigurieren von MessagePack-Serialisierungsoptionen verwendet werden. Weitere Informationen zur Funktionsweise der Resolver finden Sie in der MessagePack-Bibliothek unter [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp). Attribute können für die Objekte verwendet werden, die Sie serialisieren möchten, um zu definieren, wie sie behandelt werden sollen.

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

> [!WARNING]
> Es wird dringend empfohlen, [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) zu überprüfen und die empfohlenen Patches anzuwenden. Legen Sie z. B. die `MessagePackSecurity.Active` statische Eigenschaft auf `MessagePackSecurity.UntrustedData`fest. Das `MessagePackSecurity.Active` Festlegen der erfordert die manuelle Installation einer [1.9.x-Version von MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3). Die `MessagePack` Installation von 1.9.x aktualisiert die Version. SignalR Wenn `MessagePackSecurity.Active` nicht auf `MessagePackSecurity.UntrustedData`festgelegt ist, kann ein böswilliger Client einen Denial-of-Service verursachen. Setzen `MessagePackSecurity.Active` `Program.Main`Sie in ein, wie im folgenden Code gezeigt:

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a>Konfigurieren von MessagePack auf dem Client

> [!NOTE]
> JSON ist standardmäßig für die unterstützten Clients aktiviert. Clients können nur ein einzelnes Protokoll unterstützen. Durch das Hinzufügen der MessagePack-Unterstützung werden alle zuvor konfigurierten Protokolle ersetzt.

### <a name="net-client"></a>.NET-Client

Um MessagePack im .NET Client `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` zu aktivieren, `HubConnectionBuilder`installieren Sie das Paket, und rufen Sie es auf. `AddMessagePackProtocol`

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> Dieser `AddMessagePackProtocol` Aufruf erfordert einen Delegaten zum Konfigurieren von Optionen genau wie der Server.

### <a name="javascript-client"></a>JavaScript-Client

Die MessagePack-Unterstützung für den JavaScript-Client wird vom [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm-Paket bereitgestellt. Installieren Sie das Paket, indem Sie den folgenden Befehl in einer Befehlsshell ausführen:

```bash
npm install @aspnet/signalr-protocol-msgpack
```

Nach der Installation des npm-Pakets kann das Modul direkt über einen JavaScript-Modullader verwendet oder in den Browser importiert werden, indem auf die folgende Datei verwiesen wird:

*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*

In einem Browser `msgpack5` muss auch auf die Bibliothek verwiesen werden. Verwenden `<script>` Sie ein Tag, um einen Verweis zu erstellen. Die Bibliothek finden Sie unter *node_modules.msgpack5.dist-msgpack5.js*.

> [!NOTE]
> Bei der `<script>` Verwendung des Elements ist die Reihenfolge wichtig. Wenn *auf signalr-protocol-msgpack.js* vor *msgpack5.js*verwiesen wird, tritt beim Versuch, eine Verbindung mit MessagePack herzustellen, einen Fehler auf. *signalr.js* ist auch vor *signalr-protocol-msgpack.js*erforderlich.

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

Durch `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` Hinzufügen `HubConnectionBuilder` zu dem wird der Client so konfiguriert, dass er das MessagePack-Protokoll verwendet, wenn eine Verbindung mit einem Server hergestellt wird.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> Derzeit gibt es keine Konfigurationsoptionen für das MessagePack-Protokoll auf dem JavaScript-Client.

## <a name="messagepack-quirks"></a>MessagePack Eigenheiten

Bei der Verwendung des MessagePack Hub-Protokolls sind einige Probleme zu beachten.

### <a name="messagepack-is-case-sensitive"></a>MessagePack berücksichtigt die Groß-/Kleinschreibung

Beim MessagePack-Protokoll wird die Groß-/Kleinschreibung beachtet. Betrachten Sie z. B. die folgende C-Klasse:

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

Beim Senden vom JavaScript-Client `PascalCased` müssen Sie Eigenschaftsnamen verwenden, da das Gehäuse genau mit der C-Klasse übereinstimmen muss. Beispiel:

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

Die `camelCased` Verwendung von Namen wird nicht ordnungsgemäß an die C-Klasse gebunden. Sie können dies umgehen, indem Sie das `Key` Attribut verwenden, um einen anderen Namen für die MessagePack-Eigenschaft anzugeben. Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a>DateTime.Kind bleibt beim Serialisieren/Deserialisieren nicht erhalten

Das MessagePack-Protokoll bietet keine Möglichkeit, `Kind` den Wert `DateTime`einer zu codieren. Daher geht das MessagePack Hub-Protokoll bei der Deserialisierung eines Datums davon aus, dass das eingehende Datum im UTC-Format liegt. Wenn Sie mit `DateTime` Werten in der Ortszeit arbeiten, empfehlen wir, vor dem Senden in UTC zu konvertieren. Konvertieren Sie sie von UTC in Ortszeit, wenn Sie sie erhalten.

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2632SignalR](https://github.com/aspnet/SignalR/issues/2632).

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a>DateTime.MinValue wird von MessagePack in JavaScript nicht unterstützt

Die [msgpack5-Bibliothek,](https://github.com/mcollina/msgpack5) die SignalR vom JavaScript-Client verwendet wird, unterstützt den `timestamp96` Typ in MessagePack nicht. Dieser Typ wird verwendet, um sehr große Datumswerte zu kodieren (entweder sehr früh in der Vergangenheit oder sehr weit in der Zukunft). Der Wert `DateTime.MinValue` `January 1, 0001` von ist, der in `timestamp96` einem Wert codiert werden muss. Aus diesem Grund `DateTime.MinValue` wird das Senden an einen JavaScript-Client nicht unterstützt. Wenn `DateTime.MinValue` der JavaScript-Client empfangen wird, wird der folgende Fehler ausgelöst:

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

In `DateTime.MinValue` der Regel wird verwendet, um `null` einen "fehlenden" oder Wert zu kodieren. Wenn Sie diesen Wert in MessagePack codieren `DateTime` müssen,`DateTime?`verwenden Sie einen `bool` nullablen Wert ( ) oder codieren Sie einen separaten Wert, der angibt, ob das Datum vorhanden ist.

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2228SignalR](https://github.com/aspnet/SignalR/issues/2228).

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a>MessagePack-Unterstützung in der "Ahead-of-Time"-Kompilierungsumgebung

Die [MessagePack-CSharp-Bibliothek,](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) die vom .NET-Client und -Server verwendet wird, verwendet die Codegenerierung, um die Serialisierung zu optimieren. Daher wird es standardmäßig nicht für Umgebungen unterstützt, die die Kompilierung "vor der Zeit" verwenden (z. B. Xamarin iOS oder Unity). Es ist möglich, MessagePack in diesen Umgebungen zu verwenden, indem Sie den Serialisierungs-/Deserialisierungscode "vorgenerieren". Weitere Informationen finden Sie [in der MessagePack-CSharp-Dokumentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports). Nachdem Sie die Serialisierungen vorgeneriert haben, können Sie sie `AddMessagePackProtocol`mit dem Konfigurationsdelegat registrieren, der an:

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

### <a name="type-checks-are-more-strict-in-messagepack"></a>Typprüfungen sind in MessagePack strenger

Das JSON Hub-Protokoll führt während der Deserialisierung Typkonvertierungen durch. Wenn das eingehende Objekt beispielsweise über einen Eigenschaftswert`{ foo: 42 }`verfügt, der eine Zahl ( `string`) ist, die Eigenschaft der .NET-Klasse jedoch vom Typ ist, wird der Wert konvertiert. MessagePack führt diese Konvertierung jedoch nicht durch und löst eine Ausnahme aus, die in serverseitigen Protokollen (und in der Konsole) angezeigt werden kann:

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

Weitere Informationen zu dieser Einschränkung finden Sie unter GitHub-Problem [aspnet/#2937SignalR](https://github.com/aspnet/SignalR/issues/2937).

## <a name="related-resources"></a>Verwandte Ressourcen

* [Erste Schritte](xref:tutorials/signalr)
* [.NET-Client](xref:signalr/dotnet-client)
* [JavaScript-Client](xref:signalr/javascript-client)

::: moniker-end
