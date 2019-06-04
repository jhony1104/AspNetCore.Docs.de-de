---
title: ASP.NET Core SignalR-Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.NET Core SignalR-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 6c7bd602e621917c491bfb1e26ff0fcfc3a565b0
ms.sourcegitcommit: a04eb20e81243930ec829a9db5dd5de49f669450
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/03/2019
ms.locfileid: "66470366"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.NET Core SignalR-Konfiguration

## <a name="jsonmessagepack-serialization-options"></a>JSON/MessagePack Serialisierungsoptionen

ASP.NET Core SignalR unterstützt zwei Protokolle für die Codierung von Nachrichten: [JSON](https://www.json.org/) und [MessagePack](https://msgpack.org/index.html). Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.

JSON-Serialisierung kann konfiguriert werden, auf dem Server mithilfe der [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) Erweiterungsmethode, die nach dem hinzuzufügenden [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in Ihre `Startup.ConfigureServices` Methode. Die `AddJsonProtocol` Methode verwendet einen Delegaten, der empfängt eine `options` Objekt. Die [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein JSON.NET `JsonSerializerSettings` -Objekt, das zum Konfigurieren der Serialisierung der Argumente und Rückgabewerte verwendet werden kann. Finden Sie unter den [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) Weitere Details.

Verwenden Sie beispielsweise zum Konfigurieren der Serialisierer, verwenden Sie "PascalCase" Eigenschaftennamen, anstelle der Standardnamen "CamelCase" den folgenden Code ein:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

In der .NET Client `AddJsonProtocol` Erweiterungsmethode vorhanden ist, auf [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder). Die `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode zu beheben:

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
> Es ist nicht möglich, das JSON-Serialisierung in der JavaScript-Client zu diesem Zeitpunkt zu konfigurieren.

### <a name="messagepack-serialization-options"></a>MessagePack Serialisierungsoptionen

MessagePack Serialisierung kann konfiguriert werden, indem ein Delegat, der die [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) aufrufen. Finden Sie unter [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) Weitere Details.

> [!NOTE]
> Es ist nicht möglich, MessagePack Serialisierung zu diesem Zeitpunkt im JavaScript-Client zu konfigurieren.

## <a name="configure-server-options"></a>Konfigurieren Sie Serveroptionen

Die folgende Tabelle beschreibt die Optionen zum Konfigurieren von SignalR-Hubs:

::: moniker range=">= aspnetcore-3.0"

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 Sekunden | Der Server berücksichtigt den Client getrennt wird, wenn sie eine Nachricht (einschließlich Keep-alive-) in einem bestimmten Intervall empfangen hat. Es kann länger als diese Timeoutintervall für den Client tatsächlich gekennzeichnet werden nicht verbunden ist, aufgrund der Implementierung dauern. Der empfohlene Wert ist doppelte die `KeepAliveInterval` Wert.|
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitraums eine anfängliche Handshake-Nachricht senden, nicht, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten. Weitere Einzelheiten für den handshakeprozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server eine Nachricht nicht innerhalb dieses Intervalls gesendet noch nicht, wird eine Ping-Nachricht automatisch gesendet, die Verbindung geöffnet bleiben. Wenn Sie `KeepAliveInterval`, Ändern der `ServerTimeout` / `serverTimeoutInMilliseconds` auf dem Client festlegen. Die empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert double der `KeepAliveInterval` Wert.  |
| `SupportedProtocols` | Alle installierten Protokolle | Von diesen Hub unterstützten Protokolle. Standardmäßig alle Protokolle, die auf dem Server registriert sind zulässig, aber Protokolle können aus dieser Liste Deaktivieren bestimmter Protokolle für individuelle Hubs entfernt werden. |
| `EnableDetailedErrors` | `false` | Wenn `true`, detaillierte Ausnahme Nachrichten werden an Clients zurückgegeben, wenn in einer hubmethode eine Ausnahme ausgelöst wird. Der Standardwert ist `false`, wie diese ausnahmemeldungen können vertraulichen Informationen enthalten. |
| `StreamBufferCapacity` | `10` | Die maximale Anzahl von Elementen, die für den Client gepuffert werden können hochladen Streams. Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung der Aufrufe blockiert, bis der Server Stream Elemente verarbeitet.|

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 Sekunden | Der Server berücksichtigt den Client getrennt wird, wenn sie eine Nachricht (einschließlich Keep-alive-) in einem bestimmten Intervall empfangen hat. Es kann länger als diese Timeoutintervall für den Client tatsächlich gekennzeichnet werden nicht verbunden ist, aufgrund der Implementierung dauern. Der empfohlene Wert ist doppelte die `KeepAliveInterval` Wert.|
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitraums eine anfängliche Handshake-Nachricht senden, nicht, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten. Weitere Einzelheiten für den handshakeprozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server eine Nachricht nicht innerhalb dieses Intervalls gesendet noch nicht, wird eine Ping-Nachricht automatisch gesendet, die Verbindung geöffnet bleiben. Wenn Sie `KeepAliveInterval`, Ändern der `ServerTimeout` / `serverTimeoutInMilliseconds` auf dem Client festlegen. Die empfohlene `ServerTimeout` / `serverTimeoutInMilliseconds` Wert double der `KeepAliveInterval` Wert.  |
| `SupportedProtocols` | Alle installierten Protokolle | Von diesen Hub unterstützten Protokolle. Standardmäßig alle Protokolle, die auf dem Server registriert sind zulässig, aber Protokolle können aus dieser Liste Deaktivieren bestimmter Protokolle für individuelle Hubs entfernt werden. |
| `EnableDetailedErrors` | `false` | Wenn `true`, detaillierte Ausnahme Nachrichten werden an Clients zurückgegeben, wenn in einer hubmethode eine Ausnahme ausgelöst wird. Der Standardwert ist `false`, wie diese ausnahmemeldungen können vertraulichen Informationen enthalten. |

::: moniker-end

Optionen für alle Hubs konfiguriert werden können, durch die Bereitstellung einer Optionen-Delegat, der die `AddSignalR` Aufrufen in `Startup.ConfigureServices`.

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

Optionen für einen einzelnen Hub überschreiben die globale Optionen im `AddSignalR` und kann konfiguriert werden, mithilfe von <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Erweiterte HTTP-Konfigurationsoptionen

Verwendung `HttpConnectionDispatcherOptions` so konfigurieren Sie erweiterte Einstellungen, die im Zusammenhang mit Transporte und Speicherverwaltung für Puffer. Diese Optionen werden durch Übergeben eines Delegaten, konfiguriert [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.

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

Die folgende Tabelle beschreibt die Optionen zum Konfigurieren von ASP.NET Core SignalR erweiterten HTTP-Optionen:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die vom Client empfangen, die die Server-Puffer. Durch Erhöhen dieses Wertes können den Server, größere Nachrichten zu empfangen, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann. |
| `AuthorizationData` | Sammeln von Daten automatisch aus der `Authorize` Attribute, die auf die hubklasse angewendet. | Eine Liste der [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) Objekte verwendet, um zu bestimmen, ob ein Client autorisiert ist, eine Verbindung mit dem Hub herstellen. |
| `TransportMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die von der app gesendet werden, die die Server-Puffer. Durch Erhöhen dieses Wertes können den Server aus, um größere Nachrichten zu senden, aber arbeitsspeichernutzung negativ beeinträchtigt werden kann. |
| `Transports` | Alle Transporte sind aktiviert. | Eine Bitmaske der `HttpTransportType` Werte, die die Transporte einschränken, können ein Client kann die Verbindung verwenden. |
| `LongPolling` | Siehe weiter unten. | Zusätzliche Optionen, die speziell für den Transport Long Polling. |
| `WebSockets` | Siehe weiter unten. | Zusätzliche Optionen, die speziell für die WebSockets-Übertragung. |

Der Transport Long Polling verfügt über zusätzliche Optionen, die mit konfiguriert werden können die `LongPolling` Eigenschaft:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 Sekunden | Die maximale Zeitspanne wartet der Server eine Nachricht an den Client gesendet werden soll, bevor eine Anforderung für die einzelnen Abfrage beendet. Durch senken dieses Werts bewirkt, dass den Client neue abrufanforderungen häufiger ausgeben. |

Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit konfiguriert werden können die `WebSockets` Eigenschaft:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 Sekunden | Nachdem der Server wird geschlossen, wenn der Client nicht innerhalb dieses Zeitraums zu schließen, wird die Verbindung beendet. |
| `SubProtocolSelector` | `null` | Ein Delegat, der verwendet werden kann, Festlegen der `Sec-WebSocket-Protocol` -Header auf einen benutzerdefinierten Wert. Der Delegat empfängt die Werte, die vom Client als Eingabe angefordert werden, und es wird erwartet, um den gewünschten Wert zurückzugeben. |

## <a name="configure-client-options"></a>Konfigurieren Sie Clientoptionen

Clientoptionen können konfiguriert werden, auf die `HubConnectionBuilder` Typ (verfügbar in den .NET- und JavaScript-Clients). Es ist auch verfügbar im Java-Client, aber die `HttpHubConnectionBuilder` Unterklasse ist, was die Builder-Konfigurationsoptionen, sowie auf enthält die `HubConnection` selbst.

### <a name="configure-logging"></a>Konfigurieren der Protokollierung

Protokollierung wurde so konfiguriert, der .NET Client die `ConfigureLogging` Methode. Protokollierung von Anbietern und Filter kann auf die gleiche Weise registriert werden, wie sie auf dem Server sind. Finden Sie unter den [Protokollierung in ASP.NET Core](xref:fundamentals/logging/index) Dokumentation zu informieren.

> [!NOTE]
> Um Anbieter für die Protokollierung zu registrieren, müssen Sie die erforderlichen Pakete installieren. Finden Sie unter den [integrierte Protokollierungsanbieter](xref:fundamentals/logging/index#built-in-logging-providers) Abschnitt der Dokumentation für eine vollständige Liste.

Beispielsweise um konsolenprotokollierung zu aktivieren, installieren die `Microsoft.Extensions.Logging.Console` NuGet-Paket. Rufen Sie die `AddConsole` Erweiterungsmethode:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Im JavaScript-Client, eine ähnliche `configureLogging` Methode vorhanden ist. Geben Sie einen `LogLevel` Wert, der angibt, der Mindestebene von protokollmeldungen zu erzeugen. Protokolle werden in das Browserfenster für die Konsole geschrieben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

Anstelle von eine `LogLevel` Wert, Sie bieten auch eine `string` Wert, der einen Ebenennamen Protokoll darstellt. Dies ist nützlich, wenn Sie SignalR, die Protokollierung in Umgebungen konfigurieren, in dem Sie haben keinen Zugriff auf, die `LogLevel` Konstanten.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

Die folgende Tabelle enthält die verfügbaren Protokolliergrade. Wert zu `configureLogging` legt die **minimale** Protokollebene, die protokolliert werden. Auf dieser Ebene protokollierte Nachrichten **oder die Ebenen, die danach in der Tabelle aufgeführten**, protokolliert werden.

| Zeichenfolge | LogLevel |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| `"info"` **Oder** `"information"` | `LogLevel.Information` |
| `"warn"` **Oder** `"warning"` | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> Geben Sie zum Deaktivieren der Protokollierung vollständig, `signalR.LogLevel.None` in die `configureLogging` Methode.

Weitere Informationen zur Protokollierung finden Sie unter den [SignalR-Diagnose – Dokumentation](xref:signalr/diagnostics).

Der SignalR-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) Bibliothek für die Protokollierung. Es ist eine allgemeine protokollierungs-API an, die Benutzer der Bibliothek, wählen ihre eigenen spezifischen protokollierungsimplementierung bereitgestellt werden, indem Sie in einer bestimmten Protokollierung Abhängigkeit bringen kann. Der folgende Codeausschnitt zeigt, wie Sie mit `java.util.logging` mit dem SignalR-Java-Client.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Anmeldung bei Ihrer Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Protokollierung standardmäßig nicht-Vorgang die folgende Warnmeldung an:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann problemlos ignoriert werden.

### <a name="configure-allowed-transports"></a>Konfigurieren von zulässigen Transporte

Die von SignalR verwendete Transporte können konfiguriert werden, der `WithUrl` aufrufen (`withUrl` in JavaScript). Eine bitweise OR-Operation der Werte von `HttpTransportType` kann verwendet werden, um den Client nur die angegebenen Transporte verwenden zu beschränken. Alle Transporte sind standardmäßig aktiviert.

Zulassen Sie z. B. um den Transport Server-Sent-Ereignisse zu deaktivieren, aber WebSockets oder Long Polling Verbindungen:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

In JavaScript-Client-Transporte werden konfiguriert, durch Festlegen der `transport` Feld für das Options-Objekt, das bereitgestellt, um `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

In dieser Version von Java ist Client Websockets der einzige verfügbare Transport.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Im Java-Client der Transport aktiviert ist, und die `withTransport` Methode für die `HttpHubConnectionBuilder`. Die Java-Client verwendet standardmäßig die WebSockets-Übertragung.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Der SignalR-Java-Client-unterstützt fallback Transport noch keine.

::: moniker-end

### <a name="configure-bearer-authentication"></a>Konfigurieren von Bearer-Authentifizierung

Um Authentifizierungsdaten zusammen mit Anforderungen für SignalR bereitzustellen, verwenden Sie die `AccessTokenProvider` Option (`accessTokenFactory` in JavaScript) an eine Funktion, die den gewünschten Zugangs-Token zurückgibt. In der .NET Client dieses Zugriffstoken wird als übergeben, eine HTTP-"Bearer-Authentifizierung" token (mithilfe der `Authorization` Header vom Typ `Bearer`). In JavaScript-Client wird das Zugriffstoken als bearertoken, verwendet **außer** in einigen Fällen, in dem Browser APIs beschränken die Möglichkeit, anzuwenden Header (insbesondere im Server-Sent-Ereignisse und WebSockets-Anforderungen). In diesen Fällen wird das Zugriffstoken als einen Abfragezeichenfolgenwert bereitgestellt `access_token`.

In der .NET Client der `AccessTokenProvider` Option kann angegeben werden, mithilfe der Optionen Delegaten in `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

In JavaScript-Client das Zugriffstoken ist konfiguriert, indem die `accessTokenFactory` Feld das Optionsobjekt in `withUrl`:

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

In den SignalR-Java-Client, können Sie ein trägertoken, das zur Authentifizierung verwendet wird, durch die Bereitstellung einer Access token Factory konfigurieren die [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java). Verwendung [WithAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) zu einer [RxJava](https://github.com/ReactiveX/RxJava) [einzelne\<Zeichenfolge >](http://reactivex.io/documentation/single.html). Mit einem Aufruf von [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), Sie können Logik schreiben, um Zugriffstoken für den Client zu generieren.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Timeout und Keep-alive-Optionen konfigurieren

Zusätzliche Optionen zum Konfigurieren von Timeouts und Keep-alive-Verhalten befinden sich die `HubConnection` Objekt selbst:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `Closed` Ereignis (`onclose` in JavaScript). Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen. Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen. |
| `HandshakeTimeout` | 15 Sekunden | Timeout für die erstmalige Server-Handshake. Wenn der Server eine Antwort Handshake in einem bestimmten Intervall nicht sendet, wird vom Client abgebrochen, der Handshake und der Trigger die `Closed` Ereignis (`onclose` in JavaScript). Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten. Weitere Einzelheiten für den Handshake-Prozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Im .NET Client Timeoutwerte angegeben sind, als `TimeSpan` Werte.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `onclose` Ereignis. Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen. Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option | Standardwert | Beschreibung |
| ----------- | ------------- | ----------- |
|`getServerTimeout` `setServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server eine Nachricht in einem bestimmten Intervall gesendet noch nicht, der Client betrachtet, der Server getrennt und der Trigger die `onClose` Ereignis. Dieser Wert muss groß genug für eine Ping-Nachricht, die vom Server gesendet werden **und** innerhalb des Timeoutintervalls vom Client empfangenen. Der empfohlene Wert ist einer Zahl mindestens doppelt auf dem Server `KeepAliveInterval` Wert fest, damit Zeit für Ping-Nachrichten eingehen. |
| `withHandshakeResponseTimeout` | 15 Sekunden | Timeout für die erstmalige Server-Handshake. Wenn der Server eine Antwort Handshake in einem bestimmten Intervall nicht sendet, wird vom Client abgebrochen, der Handshake und der Trigger die `onClose` Ereignis. Dies ist eine erweiterte Einstellung, die nur geändert werden soll, wenn der Handshake-Timeout-Fehlern aufgrund von schweren Netzwerklatenz auftreten. Weitere Einzelheiten für den Handshake-Prozess finden Sie in der [SignalR-Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

---

### <a name="configure-additional-options"></a>Konfigurieren zusätzlicher Optionen

Zusätzliche Optionen können konfiguriert werden, der `WithUrl` (`withUrl` in JavaScript)-Methode `HubConnectionBuilder` oder auf die verschiedenen Konfigurations-APIs auf die `HttpHubConnectionBuilder` im Java-Client:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option für .NET |  Standardwert | Beschreibung |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird. |
| `SkipNegotiation` | `false` | Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen. **Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**. Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service. |
| `ClientCertificates` | Empty | Eine Auflistung von TLS-Zertifikate zum Authentifizieren von Anforderungen senden. |
| `Cookies` | Empty | Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden soll. |
| `Credentials` | Empty | Die Anmeldeinformationen mit jeder HTTP-Anforderung senden. |
| `CloseTimeout` | 5 Sekunden | Nur WebSockets. Die maximale Zeitspanne wartet der Client nach dem Schließen für den Server die Anforderung schließende bestätigt. Wenn der Server das Schließen nicht innerhalb dieses Zeitraums bestätigt nicht, trennt den Client. |
| `Headers` | Empty | Eine Zuordnung von zusätzlichen HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden soll. |
| `HttpMessageHandlerFactory` | `null` | Ein Delegat, der verwendet werden kann, um zu konfigurieren, oder Ersetzen Sie die `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendet. WebSocket-Verbindungen verwendet nicht. Dieser Delegat muss einen Wert ungleich Null zurückgeben, und den Standardwert als Parameter erhält. Ändern Sie die Einstellungen auf dieser Standardwert und zurückgeben oder ein neues zurückgeben `HttpMessageHandler` Instanz. **Beim Ersetzen der Handler, stellen Sie sicher, kopieren Sie die Einstellungen, die Sie aus der bereitgestellte Handler beibehalten möchten, anwenden nicht die konfigurierten Optionen (z. B. Cookies und Header), andernfalls auf den neuen Handler.** |
| `Proxy` | `null` | Ein HTTP-Proxy beim Senden von HTTP-Anforderungen verwendet werden soll. |
| `UseDefaultCredentials` | `false` | Legen Sie diese boolescher Wert, um die Standardanmeldeinformationen für HTTP und WebSockets-Anforderungen zu senden. Dadurch wird die Verwendung der Windows-Authentifizierung. |
| `WebSocketConfiguration` | `null` | Ein Delegat, der so konfigurieren Sie zusätzliche "WebSocket"-Optionen verwendet werden kann. Empfängt eine Instanz von [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die verwendet werden kann, um Optionen zu konfigurieren. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| JavaScript-Option | Standardwert | Beschreibung |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird. |
| `skipNegotiation` | `false` | Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen. **Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**. Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Java Option | Standardwert | Beschreibung |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Eine Funktion zurückgeben einer Zeichenfolge, die als bearertoken in HTTP-Anforderungen Authentifizierung bereitgestellt wird. |
| `shouldSkipNegotiate` | `false` | Legen Sie diesen `true` um die Aushandlung Schritt zu überspringen. **Nur unterstützt, wenn die WebSockets-Übertragung der einzige aktiviert-Transport ist**. Diese Einstellung kann nicht aktiviert werden, wenn mithilfe des Azure SignalR Service. |
| `withHeader` `withHeaders` | Empty | Eine Zuordnung von zusätzlichen HTTP-Header, die mit jeder HTTP-Anforderung gesendet werden soll. |

---

In der .NET Client können diese Optionen von der Delegat Optionen geändert werden `WithUrl`:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

In JavaScript-Client können in einem JavaScript-Objekt, das bereitgestellt, um diese Optionen bereitgestellt werden `withUrl`:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Im Java-Client diese Optionen können konfiguriert werden mit den Methoden für die `HttpHubConnectionBuilder` zurückgegeben, die von der `HubConnectionBuilder.create("HUB URL")`

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
