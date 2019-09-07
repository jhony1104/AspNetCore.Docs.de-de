---
title: ASP.net Core signalr-Konfiguration
author: bradygaster
description: Erfahren Sie, wie Sie ASP.net Core signalr-apps konfigurieren.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 156ffac83fbdf61fd88ad8acc307c2c701c46bca
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773930"
---
# <a name="aspnet-core-signalr-configuration"></a>ASP.net Core signalr-Konfiguration

## <a name="jsonmessagepack-serialization-options"></a>JSON/messagepack-Serialisierungsoptionen

ASP.net Core signalr unterstützt zwei Protokolle für das Codieren von Nachrichten: [JSON](https://www.json.org/) und [messagepack](https://msgpack.org/index.html). Jedes Protokoll verfügt über Konfigurationsoptionen für die Serialisierung.

Die JSON-Serialisierung kann mithilfe der [addjsonprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) -Erweiterungsmethode auf dem Server konfiguriert werden, die nach [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices` der-Methode hinzugefügt werden kann. Die `AddJsonProtocol` -Methode nimmt einen Delegaten an `options` , der ein-Objekt empfängt. Die [payloadserializersettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) -Eigenschaft für dieses Objekt ist ein `JsonSerializerSettings` JSON.NET-Objekt, das zum Konfigurieren der Serialisierung von Argumenten und Rückgabe Werten verwendet werden kann. Weitere Informationen finden Sie in der [JSON.NET-Dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) .

Verwenden Sie beispielsweise den folgenden Code, um das Serialisierungsprogramm so zu konfigurieren, dass "PascalCase"-Eigenschaftsnamen anstelle der Standardnamen "CamelCase" verwendet werden:

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

Im .NET-Client ist dieselbe `AddJsonProtocol` Erweiterungsmethode auf [hubconnectionbuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder)vorhanden. Der `Microsoft.Extensions.DependencyInjection` Namespace muss importiert werden, um die Erweiterungsmethode aufzulösen:

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
> Zurzeit ist es nicht möglich, JSON-Serialisierung im JavaScript-Client zu konfigurieren.

### <a name="messagepack-serialization-options"></a>Messagepack-Serialisierungsoptionen

Die messagepack-Serialisierung kann konfiguriert werden, indem ein Delegat für den [addmessagepackprotocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) -Aufruf bereitgestellt wird. Weitere Informationen finden Sie [unter messagepack in signalr](xref:signalr/messagepackhubprotocol) .

> [!NOTE]
> Zurzeit ist es nicht möglich, die messagepack-Serialisierung im JavaScript-Client zu konfigurieren.

## <a name="configure-server-options"></a>Konfigurieren von Serveroptionen

In der folgenden Tabelle werden die Optionen zum Konfigurieren von signalr Hubs beschrieben:

::: moniker range=">= aspnetcore-3.0"

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 Sekunden | Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde. Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist. Der empfohlene Wert ist Double `KeepAliveInterval` -Wert.|
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten. Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten. Ändern Sie `KeepAliveInterval`die `ServerTimeout` Einstellungaufdem`serverTimeoutInMilliseconds` Client, wenn Sie sich ändern. / Der empfohlene `ServerTimeout` / Wertist`KeepAliveInterval` Double-Wert. `serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Alle installierten Protokolle | Protokolle, die von diesem Hub unterstützt werden. Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren. |
| `EnableDetailedErrors` | `false` | Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird. Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können. |
| `StreamBufferCapacity` | `10` | Die maximale Anzahl von Elementen, die für Client-uploadstreams gepuffert werden können. Wenn dieser Grenzwert erreicht wird, wird die Verarbeitung von Aufrufen blockiert, bis der Server streamingelemente verarbeitet.|
| `MaximumReceiveMessageSize` | 32 KB | Maximale Größe einer einzelnen eingehenden Hub-Nachricht. |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | 30 Sekunden | Der Server wird vom-Client als getrennt betrachtet, wenn in diesem Intervall keine Nachricht (einschließlich Keep-Alive) empfangen wurde. Es kann länger dauern, bis der Client als getrennt gekennzeichnet ist, weil dies implementiert ist. Der empfohlene Wert ist Double `KeepAliveInterval` -Wert.|
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten. Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten. Ändern Sie `KeepAliveInterval`die `ServerTimeout` Einstellungaufdem`serverTimeoutInMilliseconds` Client, wenn Sie sich ändern. / Der empfohlene `ServerTimeout` / Wertist`KeepAliveInterval` Double-Wert. `serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Alle installierten Protokolle | Protokolle, die von diesem Hub unterstützt werden. Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren. |
| `EnableDetailedErrors` | `false` | Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird. Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können. |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | 15 Sekunden | Wenn der Client innerhalb dieses Zeitraums keine anfängliche Hand Shake Nachricht sendet, wird die Verbindung geschlossen. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten. Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Wenn der Server innerhalb dieses Intervalls keine Nachricht gesendet hat, wird automatisch eine Ping-Nachricht gesendet, um die Verbindung offen zu halten. Ändern Sie `KeepAliveInterval`die `ServerTimeout` Einstellungaufdem`serverTimeoutInMilliseconds` Client, wenn Sie sich ändern. / Der empfohlene `ServerTimeout` / Wertist`KeepAliveInterval` Double-Wert. `serverTimeoutInMilliseconds`  |
| `SupportedProtocols` | Alle installierten Protokolle | Protokolle, die von diesem Hub unterstützt werden. Standardmäßig sind alle auf dem Server registrierten Protokolle zulässig, aber Protokolle können aus dieser Liste entfernt werden, um bestimmte Protokolle für einzelne Hubs zu deaktivieren. |
| `EnableDetailedErrors` | `false` | Wenn `true`der Wert ist, werden ausführliche Ausnahme Meldungen an Clients zurückgegeben, wenn eine Ausnahme in einer Hub-Methode ausgelöst wird. Der Standardwert `false`ist, da diese Ausnahme Meldungen vertrauliche Informationen enthalten können. |

::: moniker-end

Optionen können für alle Hubs konfiguriert werden, indem ein Options Delegat für `AddSignalR` den Aufruf `Startup.ConfigureServices`in bereitgestellt wird.

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

Optionen für einen einzelnen Hub überschreiben die in `AddSignalR` bereitgestellten globalen Optionen und können mithilfe <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>von konfiguriert werden:

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a>Erweiterte http-Konfigurationsoptionen

::: moniker range=">= aspnetcore-3.0"

Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren. Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`übergeben wird.

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
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Verwenden `HttpConnectionDispatcherOptions` Sie, um erweiterte Einstellungen im Zusammenhang mit Transporte und der Speicherpuffer Verwaltung zu konfigurieren. Diese Optionen werden konfiguriert, indem ein [Delegat an\<maphub T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`übergeben wird.

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

::: moniker-end

In der folgenden Tabelle werden die Optionen zum Konfigurieren der erweiterten http-Optionen von ASP.net Core signalr beschrieben:

::: moniker range=">= aspnetcore-3.0"

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die vom Client vor dem Anwenden des backdrucks vom Server gepuffert werden. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller empfangen, ohne dass ein Rückdruck angewendet wird, der Arbeitsspeicher Verbrauch kann jedoch gesteigert werden. |
| `AuthorizationData` | Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden. | Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist. |
| `TransportMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert, bevor der Rückdruck beobachtet wird Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten schneller Puffern, ohne dass auf den Rückstand gewartet wird, aber die Arbeitsspeicher Nutzung erhöhen kann. |
| `Transports` | Alle Transporte sind aktiviert. | Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann. |
| `LongPolling` | Siehe weiter unten. | Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind. |
| `WebSockets` | Siehe weiter unten. | Zusätzliche Optionen für den websockets-Transport. |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die vom Client empfangen werden, der vom Server gepuffert wird. Das Erhöhen dieses Werts ermöglicht dem Server, größere Nachrichten zu empfangen, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken. |
| `AuthorizationData` | Daten, die automatisch von `Authorize` den auf die Hub-Klasse angewendeten Attributen gesammelt werden. | Eine Liste von [iautorizedata](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) -Objekten, die verwendet werden, um zu bestimmen, ob ein Client für die Verbindung mit dem Hub autorisiert ist. |
| `TransportMaxBufferSize` | 32 KB | Die maximale Anzahl von Bytes, die von der APP gesendet werden, die der Server puffert. Wenn Sie diesen Wert erhöhen, kann der Server größere Nachrichten senden, kann sich jedoch negativ auf die Arbeitsspeicher Auslastung auswirken. |
| `Transports` | Alle Transporte sind aktiviert. | Ein Bitflags-Enumeration `HttpTransportType` von Werten, die die Transporte einschränken können, die ein Client für die Verbindungs Herstellung verwenden kann. |
| `LongPolling` | Siehe weiter unten. | Zusätzliche Optionen, die für den langen Abruf Transport spezifisch sind. |
| `WebSockets` | Siehe weiter unten. | Zusätzliche Optionen für den websockets-Transport. |

::: moniker-end

Der lange Abruf Transport bietet zusätzliche Optionen, die mit der `LongPolling` -Eigenschaft konfiguriert werden können:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `PollTimeout` | 90 Sekunden | Die maximale Zeitspanne, die der Server wartet, bis eine Nachricht an den Client gesendet wird, bevor eine einzelne Abruf Anforderung beendet wird. Das verringern dieses Werts bewirkt, dass der Client neue Abruf Anforderungen häufiger ausgibt. |

Der WebSocket-Transport verfügt über zusätzliche Optionen, die mit der `WebSockets` -Eigenschaft konfiguriert werden können:

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `CloseTimeout` | 5 Sekunden | Wenn der Client nach dem Schließen des Servers nicht innerhalb dieses Zeitraums geschlossen werden kann, wird die Verbindung beendet. |
| `SubProtocolSelector` | `null` | Ein Delegat, der verwendet werden kann, `Sec-WebSocket-Protocol` um den-Header auf einen benutzerdefinierten Wert festzulegen. Der Delegat empfängt die vom Client angeforderten Werte als Eingabe und erwartet, dass der gewünschte Wert zurückgegeben wird. |

## <a name="configure-client-options"></a>Konfigurieren von Client Optionen

Client Optionen können für den `HubConnectionBuilder` Typ konfiguriert werden (verfügbar in den .net-und JavaScript-Clients). Sie steht auch im Java-Client zur Verfügung, aber `HttpHubConnectionBuilder` die Unterklasse enthält die Generator-Konfigurationsoptionen sowie die `HubConnection` selbst.

### <a name="configure-logging"></a>Konfigurieren der Protokollierung

Die Protokollierung wird mithilfe der `ConfigureLogging` -Methode im .NET-Client konfiguriert. Protokollierungs Anbieter und Filter können auf die gleiche Weise wie auf dem Server registriert werden. Weitere Informationen finden Sie in der Dokumentation zur [Protokollierung ASP.net Core](xref:fundamentals/logging/index) .

> [!NOTE]
> Um Protokollierungs Anbieter zu registrieren, müssen Sie die erforderlichen Pakete installieren. Eine vollständige Liste finden Sie im Abschnitt [integrierte Protokollierungs Anbieter](xref:fundamentals/logging/index#built-in-logging-providers) der Dokumentation.

Um z. b. die Konsolen Protokollierung zu `Microsoft.Extensions.Logging.Console` aktivieren, installieren Sie das nuget-Paket. Die `AddConsole` Erweiterungsmethode aufzurufen:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

Im JavaScript-Client ist eine ähnliche `configureLogging` Methode vorhanden. Geben Sie `LogLevel` einen Wert an, der die minimale Ebene der zu erstellenden Protokollmeldungen angibt. Protokolle werden in das Browser Konsolenfenster geschrieben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

Anstelle eines `LogLevel` Werts können Sie auch einen `string` Wert angeben, der den Namen einer Protokollebene darstellt. Dies ist hilfreich beim Konfigurieren der signalr-Protokollierung in Umgebungen, in denen Sie `LogLevel` keinen Zugriff auf die Konstanten haben.

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

In der folgenden Tabelle sind die verfügbaren Protokoll Ebenen aufgeführt. Der von Ihnen bereitgestellte `configureLogging` Wert, der die **minimale** Protokollierung festlegt, die protokolliert werden soll. Nachrichten, die auf dieser Ebene protokolliert werden, **oder die nach der Tabelle aufgeführten Ebenen**werden protokolliert.

| Zeichenfolge                      | LogLevel               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| `info`**oder**`information` | `LogLevel.Information` |
| `warn`**oder**`warning`     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> Um die Protokollierung vollständig `signalR.LogLevel.None` zu deaktivieren `configureLogging` , geben Sie in der Methode an

Weitere Informationen zur Protokollierung finden Sie in der [signalr-Diagnose Dokumentation](xref:signalr/diagnostics).

Der signalr-Java-Client verwendet die [SLF4J](https://www.slf4j.org/) -Bibliothek für die Protokollierung. Dabei handelt es sich um eine allgemeine Protokollierungs-API, die es Benutzern der Bibliothek ermöglicht, ihre eigene spezifische Protokollierungs Implementierung zu wählen, indem Sie eine bestimmte Protokollierungs Abhängigkeit einbinden. Der folgende Code Ausschnitt zeigt, wie mit dem `java.util.logging` signalr-Java-Client verwendet wird.

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

Wenn Sie die Protokollierung in ihren Abhängigkeiten nicht konfigurieren, lädt SLF4J eine Standard Protokollierung ohne Vorgang mit folgender Warnmeldung:

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

Dies kann sicher ignoriert werden.

### <a name="configure-allowed-transports"></a>Zulässige Transporte konfigurieren

Die von signalr verwendeten Transporte können im `WithUrl` -Befehl (`withUrl` in JavaScript) konfiguriert werden. Ein bitweises OR der Werte von `HttpTransportType` kann verwendet werden, um den Client so einzuschränken, dass nur die angegebenen Transporte verwendet werden. Alle Transporte sind standardmäßig aktiviert.

So können Sie beispielsweise den Transport von Server gesendeten Ereignissen deaktivieren, aber websockets und lange Abruf Verbindungen zulassen:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

Im JavaScript-Client werden Transporte durch Festlegen des-Felds `transport` für das Options-Objekt konfiguriert, `withUrl`das für bereitgestellt wird:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

In dieser Version des Java-Clients ist websockets der einzige verfügbare Transport.

::: moniker-end

::: moniker range="= aspnetcore-3.0"

Im Java-Client wird der Transport mit der `withTransport` -Methode `HttpHubConnectionBuilder`im ausgewählt. Der Java-Client verwendet standardmäßig den websockets-Transport.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> Der signalr-Java-Client unterstützt noch keinen Transport Fall Back.

::: moniker-end

### <a name="configure-bearer-authentication"></a>Konfigurieren der bearerauthentifizierung

Um Authentifizierungsdaten zusammen mit signalr-Anforderungen bereitzustellen, `AccessTokenProvider` verwenden Sie`accessTokenFactory` die-Option (in JavaScript), um eine Funktion anzugeben, die das gewünschte Zugriffs Token zurückgibt. Im .NET-Client wird dieses Zugriffs Token als HTTP- `Authorization` `Bearer`tokenauthentifizierungstoken (mit dem-Header mit dem Typ) an das Token "bearerauthentifizierung" geleitet. Im JavaScript-Client wird das Zugriffs Token als bearertoken verwendet, **außer** in einigen Fällen, in denen Browser-APIs die Fähigkeit zum Anwenden von Headern einschränken (insbesondere bei vom Server gesendeten Ereignissen und websockets-Anforderungen). In diesen Fällen wird das Zugriffs Token als Abfrage Zeichenfolgen-Wert `access_token`bereitgestellt.

Im .NET-Client kann die `AccessTokenProvider` Option mithilfe des Options Delegaten in `WithUrl`angegeben werden:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

Im JavaScript-Client wird das Zugriffs Token konfiguriert, indem das `accessTokenFactory` -Feld für das Options-Objekt in `withUrl`festgelegt wird:

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

Im signalr-Java-Client können Sie ein bearertoken für die Authentifizierung konfigurieren, indem Sie eine zugriffstokenfactory für " [httphubconnectionbuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)" bereitstellen. Verwenden Sie [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__), um einen [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html) bereitzustellen. Beim Aufrufen von " [Single.](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)Write" können Sie Logik schreiben, um Zugriffs Token für den Client zu erstellen.

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a>Konfigurieren von Timeout-und Keep-Alive-Optionen

Zusätzliche Optionen zum Konfigurieren von Timeout und Keep-Alive-Verhalten sind für `HubConnection` das Objekt selbst verfügbar:


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript). Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird. Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen. |
| `HandshakeTimeout` | 15 Sekunden | Timeout für den anfänglichen Server Hand Shake. Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript). Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten. Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `KeepAliveInterval` | 15 Sekunden | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet. |

Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird. Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen. |
| `keepAliveIntervalInMilliseconds` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird. Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen. |
| `withHandshakeResponseTimeout` | 15 Sekunden | Timeout für den anfänglichen Server Hand Shake. Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten. Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |
| `getKeepAliveInterval` / `setKeepAliveInterval` | 15 Sekunden (15.000 Millisekunden) | Bestimmt das Intervall, in dem der Client Ping-Nachrichten sendet. Beim Senden von Nachrichten vom Client wird der Timer auf den Anfang des Intervalls zurückgesetzt. Wenn der Client keine Nachricht in der `ClientTimeoutInterval` Gruppe auf dem Server gesendet hat, wird der Client vom Server als getrennt betrachtet. |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `ServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `Closed` das Ereignis`onclose` aus (in JavaScript). Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird. Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen. |
| `HandshakeTimeout` | 15 Sekunden | Timeout für den anfänglichen Server Hand Shake. Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `Closed` Ereignis aus`onclose` (in JavaScript). Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten. Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

Im .NET-Client werden Timeout Werte als `TimeSpan` Werte angegeben.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onclose` das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird. Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Option | Standardwert | Beschreibung |
| ------ | ------------- | ----------- |
| `getServerTimeout` / `setServerTimeout` | 30 Sekunden (30.000 Millisekunden) | Timeout für die Serveraktivität. Wenn der Server in diesem Intervall keine Nachricht gesendet hat, betrachtet der Client den Server als getrennt und löst `onClose` das Ereignis aus. Dieser Wert muss groß genug sein, damit eine Ping-Nachricht vom Server gesendet **und** innerhalb des Timeout Intervalls vom Client empfangen wird. Der empfohlene Wert ist eine Zahl, die mindestens dem `KeepAliveInterval` Wert des Servers entspricht, um Zeit für das Eintreffen von Pings zuzulassen. |
| `withHandshakeResponseTimeout` | 15 Sekunden | Timeout für den anfänglichen Server Hand Shake. Wenn der Server in diesem Intervall keine Hand Shake Antwort sendet, bricht der Client den Handshake ab und löst das `onClose` -Ereignis aus. Dies ist eine erweiterte Einstellung, die nur geändert werden sollte, wenn aufgrund schwerer Netzwerk Latenz Fehler aufgrund von Hand Shake Timeout auftreten. Weitere Details zum Hand Shake Prozess finden Sie in der [signalr Hub-Protokollspezifikation](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md). |

::: moniker-end

---

### <a name="configure-additional-options"></a>Zusätzliche Optionen konfigurieren

Zusätzliche `WithUrl` Optionen können in der-Methode (`withUrl` in JavaScript) für `HubConnectionBuilder` `HttpHubConnectionBuilder` oder für die verschiedenen Konfigurations-APIs auf dem im Java-Client konfiguriert werden:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

| .Net-Option |  Standardwert | Beschreibung |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt |
| `SkipNegotiation` | `false` | Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**. Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird. |
| `ClientCertificates` | Empty | Eine Auflistung von TLS-Zertifikaten, die zum Authentifizieren von Anforderungen gesendet werden sollen. |
| `Cookies` | Empty | Eine Auflistung von HTTP-Cookies, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `Credentials` | Empty | Anmelde Informationen, die mit jeder HTTP-Anforderung gesendet werden. |
| `CloseTimeout` | 5 Sekunden | Nur websockets. Die maximale Zeitspanne, die der Client nach dem Schließen des Servers wartet, um die Anforderung zum Schließen zu bestätigen. Wenn der Server die Schließung innerhalb dieses Zeitraums nicht anerkennt, trennt der Client die Verbindung. |
| `Headers` | Empty | Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen. |
| `HttpMessageHandlerFactory` | `null` | Ein Delegat, der verwendet werden kann, um den `HttpMessageHandler` zum Senden von HTTP-Anforderungen verwendeten zu konfigurieren oder zu ersetzen. Wird nicht für WebSocket-Verbindungen verwendet. Dieser Delegat muss einen Wert zurückgeben, der nicht NULL ist, und erhält den Standardwert als Parameter. Ändern Sie die Einstellungen für diesen Standardwert, und geben Sie ihn zurück, `HttpMessageHandler` oder geben Sie eine neue Instanz zurück. **Wenn Sie den Handler ersetzen, stellen Sie sicher, dass die Einstellungen, die Sie beibehalten möchten, vom bereitgestellten Handler kopiert werden. Andernfalls gelten die konfigurierten Optionen (z. b. Cookies und Header) nicht für den neuen Handler.** |
| `Proxy` | `null` | Ein HTTP-Proxy, der beim Senden von HTTP-Anforderungen verwendet wird. |
| `UseDefaultCredentials` | `false` | Legen Sie diesen booleschen Wert fest, um die Standard Anmelde Informationen für http-und websockets-Anforderungen zu senden Dies ermöglicht die Verwendung der Windows-Authentifizierung. |
| `WebSocketConfiguration` | `null` | Ein Delegat, der zum Konfigurieren zusätzlicher WebSocket-Optionen verwendet werden kann. Empfängt eine Instanz von [clientwebsocketoptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , die zum Konfigurieren der Optionen verwendet werden kann. |

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

| JavaScript-Option | Standardwert | Beschreibung |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt |
| `skipNegotiation` | `false` | Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**. Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird. |

# <a name="javatabjava"></a>[Java](#tab/java)

| Java-Option | Standardwert | Beschreibung |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | Eine Funktion, die eine Zeichenfolge zurückgibt, die als bearerauthentifizierungstoken in HTTP-Anforderungen bereitgestellt |
| `shouldSkipNegotiate` | `false` | Legen Sie dies `true` auf fest, um den Aushandlungs Schritt zu überspringen Wird **nur unterstützt, wenn der websockets-Transport der einzige aktivierte Transport ist**. Diese Einstellung kann nicht aktiviert werden, wenn der Azure signalr-Dienst verwendet wird. |
| `withHeader` `withHeaders` | Empty | Eine Karte mit zusätzlichen HTTP-Headern, die mit jeder HTTP-Anforderung gesendet werden sollen. |

---

Im .NET-Client können diese Optionen durch den für `WithUrl`bereitgestellten Options Delegaten geändert werden:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

Im JavaScript-Client können diese Optionen in einem JavaScript-Objekt bereitgestellt werden, `withUrl`das für Folgendes bereitgestellt wird:

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

Im Java-Client können diese Optionen mit den Methoden auf der `HttpHubConnectionBuilder` konfiguriert werden, die von der`HubConnectionBuilder.create("HUB URL")`

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
