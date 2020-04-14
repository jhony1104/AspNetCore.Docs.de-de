---
title: ASP.NET SignalR Kernkonfiguration
author: bradygaster
description: Erfahren Sie, wie SignalR Sie ASP.NET Core-Apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228139"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR-Konfiguration

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack-Serialisierungsoptionen

ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html). Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.

Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert werden. `AddJsonProtocol`kann nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices`in hinzugefügt werden. Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt. Die [PayloadSerializerOptions-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) für `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> dieses Objekt ist ein Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann. Weitere Informationen finden Sie in der [Dokumentation System.Text.Json](/dotnet/api/system.text.json).

Verwenden Sie beispielsweise den folgenden Code, `Startup.ConfigureServices`um den Serialisierungsmodul so zu konfigurieren, dass das Groß-/Kleinschreibung von Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen nicht geändert wird:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden. Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.

### <a name="switch-to-newtonsoftjson"></a>Wechseln zu Newtonsoft.Json

Wenn Sie Funktionen `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, siehe [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>MessagePack-Serialisierungsoptionen

Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird. Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.

## <a name="configure-server-options"></a>Konfigurieren von Serveroptionen

In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 Sekunden | Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat. Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird. Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.|
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten. Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client. Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.  |
| `SupportedProtocols` | Alle installierten Protokolle | Protokolle, die von diesem Hub unterstützt werden. Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren. |
| `EnableDetailedErrors` | `false` | Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird. Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können. |
| `StreamBufferCapacity` | `10` | Die maximale Anzahl von Elementen, die für Client-Upload-Streams gepuffert werden können. Wenn dieser Grenzwert erreicht ist, wird die Verarbeitung von Aufrufen blockiert, bis der Server Streamelemente verarbeitet.|
| `MaximumReceiveMessageSize` | 32 KB | Maximale Größe einer einzelnen eingehenden Hubnachricht. |

Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Erweiterte HTTP-Konfigurationsoptionen

Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung. Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die vom Client empfangen werden, die der Server puffert, bevor der Gegendruck angewendet wird. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne Gegendruck anzuwenden, kann jedoch den Speicherverbrauch erhöhen. |
| `AuthorizationData` | Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden. | Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist. |
| `TransportMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert, bevor die Rückdruckmenge beobachtet wird. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller puffern, ohne auf Gegendruck zu warten, kann jedoch den Speicherverbrauch erhöhen. |
| `Transports` | Alle Transporte sind aktiviert. | Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann. |
| `LongPolling` | Siehe unten. | Zusätzliche Optionen speziell für den Long Polling-Transport. |
| `WebSockets` | Siehe unten. | Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind. |
| `MinimumProtocolVersion` | 0 | Geben Sie die Mindestversion des Aushandelnsprotokolls an. Dies wird verwendet, um Clients auf neuere Versionen zu beschränken. |

Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 Sekunden | Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird. Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus. |

Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 Sekunden | Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde. |
| `SubProtocolSelector` | `null` | Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen. Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben. |

## <a name="configure-client-options"></a>Konfigurieren von Clientoptionen

Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients). Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.

### <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert. Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server. Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)

> [!NOTE]
> Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren. Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.

Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket. Rufen `AddConsole` Sie die Erweiterungsmethode auf:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden. Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt. Protokolle werden in das Browserkonsolenfenster geschrieben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anstelle eines `LogLevel` Werts können Sie `string` auch einen Wert angeben, der einen Namen auf Protokollebene darstellt. Dies ist nützlich, wenn Sie die SignalR-Protokollierung `LogLevel` in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die Konstanten haben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

In der folgenden Tabelle sind die verfügbaren Protokollebenen aufgeführt. Der Wert, `configureLogging` den Sie angeben, um die **minimale** Protokollebene festzulegen, die protokolliert wird. Nachrichten, die auf dieser Ebene protokolliert werden, **oder die ebenen, die danach in der Tabelle aufgeführt**sind, werden protokolliert.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**oder**`information` | `LogLevel.Information` |
| `warn`**oder**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.

Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).

Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung. Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt. Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann getrost ignoriert werden.

### <a name="configure-allowed-transports"></a>Konfigurieren zulässiger Transporte

Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden. Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden. Alle Transporte sind standardmäßig aktiviert.

Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.

Im Java-Client wird der Transport `withTransport` mit `HttpHubConnectionBuilder`der Methode auf der ausgewählt. Der Java-Client verwendet standardmäßig den WebSockets-Transport.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Der SignalR Java-Client unterstützt den Transport-Fallback noch nicht.

### <a name="configure-bearer-authentication"></a>Konfigurieren der Bearer-Authentifizierung

Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt. Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ). Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen). In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.

Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen. Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen. Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurieren von Timeout- und Keep-Alive-Optionen

Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:

# <a name="net"></a>[.NET](#tab/dotnet)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript). Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `HandshakeTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript). Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `keepAliveIntervalInMilliseconds` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

# <a name="java"></a>[Java](#tab/java)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `withHandshakeResponseTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

---

### <a name="configure-additional-options"></a>Konfigurieren zusätzlicher Optionen

Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET-Option |  Standardwert | BESCHREIBUNG |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `SkipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `ClientCertificates` | Leer | Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen. |
| `Cookies` | Leer | Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `Credentials` | Leer | Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `CloseTimeout` | 5 Sekunden | Nur WebSockets. Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt. Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung. |
| `Headers` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `HttpMessageHandlerFactory` | `null` | Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann. Wird nicht für WebSocket-Verbindungen verwendet. Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter. Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück. **Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.** |
| `Proxy` | `null` | Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll. |
| `UseDefaultCredentials` | `false` | Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden. Dies ermöglicht die Verwendung der Windows-Authentifizierung. |
| `WebSocketConfiguration` | `null` | Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann. Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript-Option | Standardwert | BESCHREIBUNG |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `skipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |

# <a name="java"></a>[Java](#tab/java)

| Java-Option | Standardwert | BESCHREIBUNG |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `shouldSkipNegotiate` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `withHeader` `withHeaders` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |

---

Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack-Serialisierungsoptionen

ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html). Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.

Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert werden. `AddJsonProtocol`kann nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) `Startup.ConfigureServices`in hinzugefügt werden. Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt. Die [PayloadSerializerOptions-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) für `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> dieses Objekt ist ein Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann. Weitere Informationen finden Sie in der [Dokumentation System.Text.Json](/dotnet/api/system.text.json).

Verwenden Sie beispielsweise den folgenden Code, `Startup.ConfigureServices`um den Serialisierungsmodul so zu konfigurieren, dass das Groß-/Kleinschreibung von Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen nicht geändert wird:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden. Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.

### <a name="switch-to-newtonsoftjson"></a>Wechseln zu Newtonsoft.Json

Wenn Sie Funktionen `Newtonsoft.Json` benötigen, die in `System.Text.Json`nicht unterstützt werden, siehe [Wechseln zu Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).

### <a name="messagepack-serialization-options"></a>MessagePack-Serialisierungsoptionen

Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird. Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.

## <a name="configure-server-options"></a>Konfigurieren von Serveroptionen

In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 Sekunden | Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat. Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird. Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.|
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten. Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client. Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.  |
| `SupportedProtocols` | Alle installierten Protokolle | Protokolle, die von diesem Hub unterstützt werden. Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren. |
| `EnableDetailedErrors` | `false` | Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird. Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können. |
| `StreamBufferCapacity` | `10` | Die maximale Anzahl von Elementen, die für Client-Upload-Streams gepuffert werden können. Wenn dieser Grenzwert erreicht ist, wird die Verarbeitung von Aufrufen blockiert, bis der Server Streamelemente verarbeitet.|
| `MaximumReceiveMessageSize` | 32 KB | Maximale Größe einer einzelnen eingehenden Hubnachricht. |

Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Erweiterte HTTP-Konfigurationsoptionen

Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung. Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in übergeben.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die vom Client empfangen werden, die der Server puffert, bevor der Gegendruck angewendet wird. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne Gegendruck anzuwenden, kann jedoch den Speicherverbrauch erhöhen. |
| `AuthorizationData` | Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden. | Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist. |
| `TransportMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert, bevor die Rückdruckmenge beobachtet wird. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller puffern, ohne auf Gegendruck zu warten, kann jedoch den Speicherverbrauch erhöhen. |
| `Transports` | Alle Transporte sind aktiviert. | Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann. |
| `LongPolling` | Siehe unten. | Zusätzliche Optionen speziell für den Long Polling-Transport. |
| `WebSockets` | Siehe unten. | Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind. |

Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 Sekunden | Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird. Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus. |

Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 Sekunden | Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde. |
| `SubProtocolSelector` | `null` | Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen. Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben. |

## <a name="configure-client-options"></a>Konfigurieren von Clientoptionen

Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients). Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.

### <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert. Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server. Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)

> [!NOTE]
> Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren. Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.

Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket. Rufen `AddConsole` Sie die Erweiterungsmethode auf:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden. Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt. Protokolle werden in das Browserkonsolenfenster geschrieben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

Anstelle eines `LogLevel` Werts können Sie `string` auch einen Wert angeben, der einen Namen auf Protokollebene darstellt. Dies ist nützlich, wenn Sie die SignalR-Protokollierung `LogLevel` in Umgebungen konfigurieren, in denen Sie keinen Zugriff auf die Konstanten haben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

In der folgenden Tabelle sind die verfügbaren Protokollebenen aufgeführt. Der Wert, `configureLogging` den Sie angeben, um die **minimale** Protokollebene festzulegen, die protokolliert wird. Nachrichten, die auf dieser Ebene protokolliert werden, **oder die ebenen, die danach in der Tabelle aufgeführt**sind, werden protokolliert.

| String                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**oder**`information` | `LogLevel.Information` |
| `warn`**oder**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.

Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).

Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung. Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt. Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann getrost ignoriert werden.

### <a name="configure-allowed-transports"></a>Konfigurieren zulässiger Transporte

Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden. Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden. Alle Transporte sind standardmäßig aktiviert.

Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.

Im Java-Client wird der Transport `withTransport` mit `HttpHubConnectionBuilder`der Methode auf der ausgewählt. Der Java-Client verwendet standardmäßig den WebSockets-Transport.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Der SignalR Java-Client unterstützt den Transport-Fallback noch nicht.

### <a name="configure-bearer-authentication"></a>Konfigurieren der Bearer-Authentifizierung

Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt. Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ). Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen). In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.

Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen. Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen. Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurieren von Timeout- und Keep-Alive-Optionen

Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:

# <a name="net"></a>[.NET](#tab/dotnet)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript). Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `HandshakeTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript). Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `keepAliveIntervalInMilliseconds` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

# <a name="java"></a>[Java](#tab/java)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `withHandshakeResponseTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

---

### <a name="configure-additional-options"></a>Konfigurieren zusätzlicher Optionen

Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET-Option |  Standardwert | BESCHREIBUNG |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `SkipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `ClientCertificates` | Leer | Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen. |
| `Cookies` | Leer | Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `Credentials` | Leer | Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `CloseTimeout` | 5 Sekunden | Nur WebSockets. Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt. Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung. |
| `Headers` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `HttpMessageHandlerFactory` | `null` | Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann. Wird nicht für WebSocket-Verbindungen verwendet. Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter. Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück. **Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.** |
| `Proxy` | `null` | Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll. |
| `UseDefaultCredentials` | `false` | Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden. Dies ermöglicht die Verwendung der Windows-Authentifizierung. |
| `WebSocketConfiguration` | `null` | Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann. Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript-Option | Standardwert | BESCHREIBUNG |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `skipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |

# <a name="java"></a>[Java](#tab/java)

| Java-Option | Standardwert | BESCHREIBUNG |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `shouldSkipNegotiate` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `withHeader` `withHeaders` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |

---

Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack-Serialisierungsoptionen

ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html). Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.

Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert `Startup.ConfigureServices` werden, die nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihrer Methode hinzugefügt werden kann. Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt. Die [PayloadSerializerSettings-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) für dieses `JsonSerializerSettings` Objekt ist ein JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann. Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Um den Serialisierungsmodul beispielsweise für die Verwendung von "PascalCase"-Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen zu konfigurieren, verwenden Sie den folgenden Code in: `Startup.ConfigureServices`
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden. Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.

### <a name="messagepack-serialization-options"></a>MessagePack-Serialisierungsoptionen

Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird. Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.

## <a name="configure-server-options"></a>Konfigurieren von Serveroptionen

In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 Sekunden | Der Server betrachtet die Verbindung des Clients, wenn er in diesem Intervall keine Nachricht (einschließlich Keep-Alive) erhalten hat. Es kann länger dauern als dieses Timeoutintervall, bis der Client aufgrund der Implementierung tatsächlich als getrennt markiert wird. Der empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.|
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten. Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client. Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.  |
| `SupportedProtocols` | Alle installierten Protokolle | Protokolle, die von diesem Hub unterstützt werden. Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren. |
| `EnableDetailedErrors` | `false` | Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird. Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können. |

Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Erweiterte HTTP-Konfigurationsoptionen

Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung. Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die vom Client empfangen werden, den der Server puffert. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten empfangen, kann sich jedoch negativ auf den Speicherverbrauch auswirken. |
| `AuthorizationData` | Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden. | Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist. |
| `TransportMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf den Speicherverbrauch auswirken. |
| `Transports` | Alle Transporte sind aktiviert. | Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann. |
| `LongPolling` | Siehe unten. | Zusätzliche Optionen speziell für den Long Polling-Transport. |
| `WebSockets` | Siehe unten. | Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind. |

Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 Sekunden | Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird. Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus. |

Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 Sekunden | Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde. |
| `SubProtocolSelector` | `null` | Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen. Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben. |

## <a name="configure-client-options"></a>Konfigurieren von Clientoptionen

Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients). Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.

### <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert. Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server. Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)

> [!NOTE]
> Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren. Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.

Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket. Rufen `AddConsole` Sie die Erweiterungsmethode auf:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden. Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt. Protokolle werden in das Browserkonsolenfenster geschrieben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.

Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).

Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung. Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt. Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann getrost ignoriert werden.

### <a name="configure-allowed-transports"></a>Konfigurieren zulässiger Transporte

Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden. Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden. Alle Transporte sind standardmäßig aktiviert.

Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

In dieser Version des Java-Client-Websockets ist der einzige verfügbare Transport.

### <a name="configure-bearer-authentication"></a>Konfigurieren der Bearer-Authentifizierung

Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt. Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ). Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen). In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.

Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen. Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen. Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurieren von Timeout- und Keep-Alive-Optionen

Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:

# <a name="net"></a>[.NET](#tab/dotnet)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript). Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `HandshakeTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript). Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `keepAliveIntervalInMilliseconds` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

# <a name="java"></a>[Java](#tab/java)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `withHandshakeResponseTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Wenn Sie eine Nachricht vom Client senden, wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in `ClientTimeoutInterval` der Gruppe auf dem Server gesendet hat, betrachtet der Server die Verbindung zum Client. |

---

### <a name="configure-additional-options"></a>Konfigurieren zusätzlicher Optionen

Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET-Option |  Standardwert | BESCHREIBUNG |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `SkipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `ClientCertificates` | Leer | Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen. |
| `Cookies` | Leer | Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `Credentials` | Leer | Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `CloseTimeout` | 5 Sekunden | Nur WebSockets. Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt. Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung. |
| `Headers` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `HttpMessageHandlerFactory` | `null` | Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann. Wird nicht für WebSocket-Verbindungen verwendet. Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter. Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück. **Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.** |
| `Proxy` | `null` | Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll. |
| `UseDefaultCredentials` | `false` | Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden. Dies ermöglicht die Verwendung der Windows-Authentifizierung. |
| `WebSocketConfiguration` | `null` | Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann. Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript-Option | Standardwert | BESCHREIBUNG |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `skipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |

# <a name="java"></a>[Java](#tab/java)

| Java-Option | Standardwert | BESCHREIBUNG |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `shouldSkipNegotiate` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `withHeader` `withHeaders` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |

---

Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack-Serialisierungsoptionen

ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html). Jedes Protokoll verfügt über Serialisierungskonfigurationsoptionen.

Die JSON-Serialisierung kann auf dem Server mithilfe der [AddJsonProtocol-Erweiterungsmethode](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) konfiguriert `Startup.ConfigureServices` werden, die nach [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihrer Methode hinzugefügt werden kann. Die `AddJsonProtocol` Methode nimmt einen `options` Delegaten an, der ein Objekt empfängt. Die [PayloadSerializerSettings-Eigenschaft](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) für dieses `JsonSerializerSettings` Objekt ist ein JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabewerten verwendet werden kann. Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).
 
Um den Serialisierungsmodul beispielsweise für die Verwendung von "PascalCase"-Eigenschaftsnamen anstelle der standardmäßigen "camelCase"-Namen zu konfigurieren, verwenden Sie den folgenden Code in: `Startup.ConfigureServices`
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

Im .NET-Client ist `AddJsonProtocol` dieselbe Erweiterungsmethode auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden. Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> Es ist derzeit nicht möglich, die JSON-Serialisierung im JavaScript-Client zu konfigurieren.

### <a name="messagepack-serialization-options"></a>MessagePack-Serialisierungsoptionen

Die MessagePack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [AddMessagePackProtocol-Aufruf](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) angegeben wird. Weitere Informationen finden Sie [unter MessagePack in SignalR.](xref:signalr/messagepackhubprotocol)

> [!NOTE]
> Es ist derzeit nicht möglich, die MessagePack-Serialisierung im JavaScript-Client zu konfigurieren.

## <a name="configure-server-options"></a>Konfigurieren von Serveroptionen

In der folgenden Tabelle werden Optionen zum Konfigurieren von SignalR-Hubs beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitintervalls keine erste Handshake-Nachricht sendet, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten. Ändern `KeepAliveInterval`Sie beim `ServerTimeout` / `serverTimeoutInMilliseconds` Ändern die Einstellung auf dem Client. Der `ServerTimeout` / `serverTimeoutInMilliseconds` empfohlene Wert `KeepAliveInterval` ist das Doppelte des Wertes.  |
| `SupportedProtocols` | Alle installierten Protokolle | Protokolle, die von diesem Hub unterstützt werden. Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, Protokolle können jedoch aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren. |
| `EnableDetailedErrors` | `false` | Wenn `true`, werden detaillierte Ausnahmemeldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird. Der Standardwert ist `false`, da diese Ausnahmemeldungen vertrauliche Informationen enthalten können. |

Optionen können für alle Hubs konfiguriert werden, `AddSignalR` indem `Startup.ConfigureServices`ein Optionsdelegat für den Aufruf in zur Verfügung gestellt wird.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

Optionen für einen einzelnen Hub überschreiben `AddSignalR` die globalen Optionen, die in bereitgestellt werden und können mit <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>folgenden Optionen konfiguriert werden:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Erweiterte HTTP-Konfigurationsoptionen

Verwenden `HttpConnectionDispatcherOptions` Sie diese Datei zum Konfigurieren erweiterter Einstellungen für Transporte und Speicherpufferverwaltung. Diese Optionen werden konfiguriert, indem sie einen `Startup.Configure`Delegaten an [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in übergeben.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

In der folgenden Tabelle werden Optionen zum Konfigurieren ASP.NET erweiterten HTTP-Optionen von Core SignalR beschrieben:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die vom Client empfangen werden, den der Server puffert. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten empfangen, kann sich jedoch negativ auf den Speicherverbrauch auswirken. |
| `AuthorizationData` | Daten, die `Authorize` automatisch aus den Attributen gesammelt werden, die auf die Hub-Klasse angewendet werden. | Eine Liste von [IAuthorizeData-Objekten,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) die verwendet werden, um zu bestimmen, ob ein Client zum Herstellen einer Verbindung mit dem Hub autorisiert ist. |
| `TransportMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die von der App gesendet werden, die der Server puffert. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf den Speicherverbrauch auswirken. |
| `Transports` | Alle Transporte sind aktiviert. | Eine Bit-Flags-Enumere von `HttpTransportType` Werten, die die Transporte einschränken können, die ein Client zum Herstellen einer Verbindung verwenden kann. |
| `LongPolling` | Siehe unten. | Zusätzliche Optionen speziell für den Long Polling-Transport. |
| `WebSockets` | Siehe unten. | Zusätzliche Optionen, die für den WebSockets-Transport spezifisch sind. |

Der Long Polling-Transport verfügt über zusätzliche `LongPolling` Optionen, die mithilfe der Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 Sekunden | Die maximale Zeit, die der Server auf das Senden einer Nachricht an den Client wartet, bevor eine einzelne Abfrageanforderung beendet wird. Wenn Sie diesen Wert verringern, gibt der Client häufiger neue Abfrageanforderungen aus. |

Der WebSocket-Transport verfügt über zusätzliche Optionen, die mithilfe der `WebSockets` Eigenschaft konfiguriert werden können:

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 Sekunden | Wenn der Client nicht innerhalb dieses Zeitintervalls geschlossen werden kann, wird die Verbindung beendet, nachdem der Server geschlossen wurde. |
| `SubProtocolSelector` | `null` | Ein Delegat, der `Sec-WebSocket-Protocol` verwendet werden kann, um den Header auf einen benutzerdefinierten Wert festzulegen. Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und wird voraussichtlich den gewünschten Wert zurückgeben. |

## <a name="configure-client-options"></a>Konfigurieren von Clientoptionen

Clientoptionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .NET- und JavaScript-Clients). Es ist auch im Java-Client `HttpHubConnectionBuilder` verfügbar, aber die Unterklasse ist, was `HubConnection` die Builder-Konfigurationsoptionen enthält, sowie auf der selbst.

### <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die Protokollierung wird im .NET-Client mit der `ConfigureLogging` Methode konfiguriert. Protokollierungsanbieter und Filter können auf die gleiche Weise registriert werden wie auf dem Server. Weitere Informationen finden Sie in der Dokumentation ["Anmelden in ASP.NET Kern".](xref:fundamentals/logging/index)

> [!NOTE]
> Um Logging-Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren. Eine vollständige Liste finden Sie im Abschnitt [Integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) in den Dokumenten.

Um beispielsweise die Konsolenprotokollierung `Microsoft.Extensions.Logging.Console` zu aktivieren, installieren Sie das NuGet-Paket. Rufen `AddConsole` Sie die Erweiterungsmethode auf:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Im JavaScript-Client ist `configureLogging` eine ähnliche Methode vorhanden. Geben `LogLevel` Sie einen Wert an, der die Mindestebene der zu erzeugenden Protokollnachrichten angibt. Protokolle werden in das Browserkonsolenfenster geschrieben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> Um die Protokollierung `signalR.LogLevel.None` vollständig `configureLogging` zu deaktivieren, geben Sie in der Methode an.

Weitere Informationen zur Protokollierung finden Sie in der [SignalR-Diagnosedokumentation](xref:signalr/diagnostics).

Der SignalR Java-Client verwendet die [SLF4J-Bibliothek](https://www.slf4j.org/) für die Protokollierung. Es handelt sich um eine api auf hoher Ebene, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungsimplementierung zu wählen, indem sie eine bestimmte Protokollierungsabhängigkeit einführt. Der folgende Codeausschnitt zeigt, wie `java.util.logging` Sie mit dem SignalR Java-Client verwenden.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Protokollierung in Ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J einen standardmäßigen Nicht-Betriebsprotokollmitlogger mit der folgenden Warnmeldung:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann getrost ignoriert werden.

### <a name="configure-allowed-transports"></a>Konfigurieren zulässiger Transporte

Die von SignalR verwendeten Transporte können `WithUrl` im`withUrl` Aufruf (in JavaScript) konfiguriert werden. Ein bitweises-ODER der `HttpTransportType` Werte von kann verwendet werden, um den Client darauf zu beschränken, nur die angegebenen Transporte zu verwenden. Alle Transporte sind standardmäßig aktiviert.

Um z. B. den Transport servergesendeter Ereignisse zu deaktivieren, aber WebSockets- und Long Polling-Verbindungen zuzulassen:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Im JavaScript-Client werden Transporte konfiguriert, `transport` indem das Feld `withUrl`für das Optionsobjekt festgelegt wird, das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a>Konfigurieren der Bearer-Authentifizierung

Um Authentifizierungsdaten zusammen mit SignalR-Anforderungen bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (in`accessTokenFactory` JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffstoken zurückgibt. Im .NET Client wird dieses Zugriffstoken als HTTP-Token "Bearer `Authorization` Authentication" übergeben `Bearer`(Verwenden des Headers mit einem Typ von ). Im JavaScript-Client wird das Zugriffstoken als Bearer-Token verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Möglichkeit einschränken, Header anzuwenden (insbesondere in Server-Sent Events und WebSockets-Anforderungen). In diesen Fällen wird das Zugriffstoken als `access_token`Abfragezeichenfolgenwert bereitgestellt.

Im .NET-Client `AccessTokenProvider` kann die Option mithilfe der `WithUrl`Optionen delegate in angegeben werden:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Im JavaScript-Client wird das Zugriffstoken konfiguriert, indem das `accessTokenFactory` `withUrl`Feld für das Optionsobjekt in :

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

Im SignalR Java-Client können Sie ein Trägertoken für die Authentifizierung konfigurieren, indem Sie dem [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)eine Zugriffstokenfactory bereitstellen. Verwenden Sie [withAccessTokenFactory,](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) um eine [RxJava](https://github.com/ReactiveX/RxJava) [\<Single String>](https://reactivex.io/documentation/single.html)bereitzustellen. Mit einem Aufruf von [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)können Sie Logik schreiben, um Zugriffstoken für Ihren Client zu erstellen.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurieren von Timeout- und Keep-Alive-Optionen

Zusätzliche Optionen zum Konfigurieren von Timeout und `HubConnection` Keep-Alive-Verhalten sind für das Objekt selbst verfügbar:

# <a name="net"></a>[.NET](#tab/dotnet)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `Closed` Client`onclose` die Verbindung des Servers und löst das Ereignis aus (in JavaScript). Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |
| `HandshakeTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `Closed` Client`onclose` den Handshake ab und löst das Ereignis aus (in JavaScript). Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Im .NET-Client werden Timeoutwerte `TimeSpan` als Werte angegeben.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onclose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens doppelt so hoch ist wie der Wert des Servers, damit Zeit für Pings eintrifft. |

# <a name="java"></a>[Java](#tab/java)

| Option | Standardwert | BESCHREIBUNG |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der `onClose` Client die Verbindung zum Server und löst das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** vom Client innerhalb des Timeoutintervalls empfangen werden kann. Der empfohlene Wert ist eine Zahl, `KeepAliveInterval` die mindestens das Doppelte des Serverwerts beträgt, um Zeit für Pings zu haben. |
| `withHandshakeResponseTimeout` | 15 Sekunden | Timeout für den ersten Serverhandshake. Wenn der Server in diesem Intervall keine Handshake-Antwort sendet, bricht der `onClose` Client den Handshake ab und löst das Ereignis aus. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn Handshake-Timeoutfehler aufgrund einer schwerwiegenden Netzwerklatenz auftreten. Weitere Informationen zum Handshake-Prozess finden Sie in der [SignalR Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Konfigurieren zusätzlicher Optionen

Zusätzliche Optionen können in `WithUrl` der`withUrl` ( in JavaScript) Methode auf `HubConnectionBuilder` oder `HttpHubConnectionBuilder` auf den verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:

# <a name="net"></a>[.NET](#tab/dotnet)

| .NET-Option |  Standardwert | BESCHREIBUNG |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `SkipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `ClientCertificates` | Leer | Eine Auflistung von TLS-Zertifikaten, die an Authentifizierenvon Anforderungen gesendet werden sollen. |
| `Cookies` | Leer | Eine Sammlung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `Credentials` | Leer | Anmeldeinformationen, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `CloseTimeout` | 5 Sekunden | Nur WebSockets. Die maximale Zeitdauer, die der Client nach dem Schließen wartet, damit der Server die Close-Anforderung bestätigt. Wenn der Server den Abschluss innerhalb dieser Zeit nicht bestätigt, trennt der Client die Verbindung. |
| `Headers` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `HttpMessageHandlerFactory` | `null` | Ein Delegat, der zum `HttpMessageHandler` Konfigurieren oder Ersetzen der zum Senden von HTTP-Anforderungen verwendeten Delegaten verwendet werden kann. Wird nicht für WebSocket-Verbindungen verwendet. Dieser Delegat muss einen Wert ungleich NULL zurückgeben, und er empfängt den Standardwert als Parameter. Ändern Sie entweder die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, oder geben Sie eine neue `HttpMessageHandler` Instanz zurück. **Wenn Sie den Handler ersetzen, sollten Sie die Einstellungen, die Sie vom bereitgestellten Handler beibehalten möchten, kopieren, andernfalls gelten die konfigurierten Optionen (z. B. Cookies und Header) nicht für den neuen Handler.** |
| `Proxy` | `null` | Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet werden soll. |
| `UseDefaultCredentials` | `false` | Legen Sie diese boolesche Einstellung fest, um die Standardanmeldeinformationen für HTTP- und WebSockets-Anforderungen zu senden. Dies ermöglicht die Verwendung der Windows-Authentifizierung. |
| `WebSocketConfiguration` | `null` | Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann. Empfängt eine Instanz von [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) die zum Konfigurieren der Optionen verwendet werden kann. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

| JavaScript-Option | Standardwert | BESCHREIBUNG |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `skipNegotiation` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |

# <a name="java"></a>[Java](#tab/java)

| Java-Option | Standardwert | BESCHREIBUNG |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als Bearer-Authentifizierungstoken in HTTP-Anforderungen bereitgestellt wird. |
| `shouldSkipNegotiate` | `false` | Legen Sie `true` diesen Vorgang fest, um den Aushandlungsschritt zu überspringen. **Wird nur unterstützt, wenn der WebSockets-Transport der einzige aktivierte Transport ist.** Diese Einstellung kann nicht aktiviert werden, wenn Sie den Azure SignalR-Dienst verwenden. |
| `withHeader` `withHeaders` | Leer | Eine Zuordnung zusätzlicher HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden sollen. |

---

Im .NET Client können diese Optionen durch den `WithUrl`Optionen-Delegaten geändert werden, der für:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Im Java-Client können diese Optionen mit den `HttpHubConnectionBuilder` Methoden auf dem zurückgegebenen`HubConnectionBuilder.create("HUB URL")`

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
