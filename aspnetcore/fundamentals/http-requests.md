---
title: Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core
author: stevejgordon
description: Erfahren Sie mehr über die Verwendung der IHttpClientFactory-Schnittstelle, um logische HttpClient-Instanzen in ASP.NET Core zu verwalten.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 08/01/2019
uid: fundamentals/http-requests
ms.openlocfilehash: bcf2a2eaf6910222d274c38bac343c92fab9cb5b
ms.sourcegitcommit: b5e63714afc26e94be49a92619586df5189ed93a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/02/2019
ms.locfileid: "68739533"
---
# <a name="make-http-requests-using-ihttpclientfactory-in-aspnet-core"></a>Stellen von HTTP-Anforderungen mithilfe von IHttpClientFactory in ASP.NET Core

Von [Glenn Condron](https://github.com/glennc), [Ryan Nowak](https://github.com/rynowak), und [Steve Gordon](https://github.com/stevejgordon)

<xref:System.Net.Http.IHttpClientFactory> kann registriert und zum Konfigurieren und Erstellen von <xref:System.Net.Http.HttpClient>-Instanzen in einer App verwendet werden. Dies hat folgende Vorteile:

* Ein zentraler Ort für das Benennen und Konfigurieren logischer `HttpClient`-Instanzen wird damit geboten. Ein *GitHub*-Client kann beispielsweise registriert werden und so konfiguriert werden, um auf [GitHub](https://github.com/) zuzugreifen. Ein Standard-Client kann für andere Zwecke registriert werden.
* Das Konzept der ausgehenden Middleware wird über delegierende Handler in `HttpClient` in Code umgesetzt. Außerdem werden Erweiterungen für auf Polly basierende Middleware bereitgestellt, die dies nutzen.
* Das Pooling und die Lebensdauer von zugrunde liegenden `HttpClientMessageHandler`-Instanzen werden verwaltet, um gängige DNS-Probleme zu vermeiden, die bei der manuellen Verwaltung der `HttpClient`-Lebensdauer auftreten.
* Eine konfigurierbare Protokollierungsfunktion wird (über `ILogger`) für alle Anforderungen hinzugefügt, die über Clients gesendet werden, die von der Factory erstellt wurden.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/http-requests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

::: moniker range="<= aspnetcore-2.2"

## <a name="prerequisites"></a>Erforderliche Komponenten

Für Projekte mit der Zielplattform .NET Framework muss das NuGet-Paket [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) installiert werden. Projekte für .NET Core, die auf das [Metapaket Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) verweisen, enthalten bereits das `Microsoft.Extensions.Http`-Paket.

::: moniker-end

## <a name="consumption-patterns"></a>Verbrauchsmuster

Es gibt mehrere Möglichkeiten `IHttpClientFactory` in einer App zu verwenden:

* [Grundlegende Verwendung](#basic-usage)
* [Benannte Clients](#named-clients)
* [Typisierte Clients](#typed-clients)
* [Generierte Clients](#generated-clients)

Keine dieser Möglichkeiten ist einer anderen überlegen. Der beste Ansatz richtet sich nach den Einschränkungen der App.

### <a name="basic-usage"></a>Grundlegende Verwendung

`IHttpClientFactory` kann durch Aufrufen der Erweiterungsmethode `AddHttpClient` auf `IServiceCollection` in der Methode `Startup.ConfigureServices` registriert werden.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet1)]

Nach der Registrierung kann Code `IHttpClientFactory` überall akzeptieren, wo Dienste mithilfe der [Dependency Injection](xref:fundamentals/dependency-injection) (DI) eingefügt werden können. `IHttpClientFactory` kann zum Erstellen einer `HttpClient`-Instanz verwendet werden:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/BasicUsage.cshtml.cs?name=snippet1&highlight=9-12,21)]

`IHttpClientFactory` auf diese Weise zu verwenden, ist eine gute Möglichkeit zum Umgestalten einer vorhandenen App. Dies hat keine Auswirkung auf die Verwendung von `HttpClient`. An Stellen, in denen `HttpClient`-Instanzen derzeit erstellt werden, können Sie diese Ereignisse mit einem Aufruf von <xref:System.Net.Http.IHttpClientFactory.CreateClient*> ersetzen.

### <a name="named-clients"></a>Benannte Clients

Wenn eine App mehrere verschiedene Verwendungen von `HttpClient` mit jeweils unterschiedlicher Konfiguration erfordert, ist die Verwendung von **benannten Clients** eine Option. Die Konfiguration einer benannten `HttpClient` kann während der Registrierung in `Startup.ConfigureServices` angegeben werden.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet2)]

Im oben stehenden Code wird `AddHttpClient` aufgerufen, und dabei wird der Name *github* angegeben. Für diesen Client wird Standardkonfiguration angewendet, d.h. die Basisadresse und zwei Header, die erforderlich sind, um mit der GitHub-API zu funktionieren.

Wenn `CreateClient` aufgerufen wird, wird jedes Mal eine neue Instanz von `HttpClient` erstellt und die Konfigurationsaktion aufgerufen.

Zum Verarbeiten eines benannten Clients kann ein Zeichenfolgenparameter an `CreateClient` übergeben werden. Geben Sie den Namen des zu erstellenden Clients an:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/NamedClient.cshtml.cs?name=snippet1&highlight=21)]

Im vorangehenden Code muss die Anforderung keinen Hostnamen angeben. Sie muss nur den Pfad übergeben, da die für den Client konfigurierte Basisadresse verwendet wird.

### <a name="typed-clients"></a>Typisierte Clients

Typisierte Clients:

* Stellen dieselben Funktionen wie benannte Clients bereit, ohne Zeichenfolgen als Schlüssel verwenden zu müssen.
* Bieten Hilfe durch IntelliSense und Compiler beim Verarbeiten von Clients.
* Stellen einen einzelnen Ort zum Konfigurieren und Interagieren mit einem bestimmten `HttpClient` bereit. Zum Beispiel kann ein einzelner typisierter Client für einen einzelnen Back-End-Endpunkt verwendet werden, der jegliche Logik kapselt, die mit diesem Endpunkt interagiert.
* Funktionieren mit DI und können überall in Ihrer App eingefügt werden, wo sie benötigt werden.

Ein typisierter Client akzeptiert einen `HttpClient`-Parameter in seinem Konstruktor:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/GitHubService.cs?name=snippet1&highlight=5)]

Im vorangehenden Code wird die Konfiguration in den typisierten Client verschoben. Das `HttpClient`-Objekt wird als öffentliche Eigenschaft zur Verfügung gestellt. Es ist möglich, API-spezifische Methoden zu definieren, die die `HttpClient`-Funktionalität zur Verfügung stellen. Die Methode `GetAspNetDocsIssues` kapselt den Code, der für die Abfrage und Analyse des neuesten offenen Problems aus dem GitHub-Repository erforderlich ist.

Zum Registrieren eines typisierten Clients kann die generische Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientFactoryServiceCollectionExtensions.AddHttpClient*> innerhalb von `Startup.ConfigureServices` verwendet werden, die die typisierte Klasse angeben:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet3)]

Der typisierte Client wird mit DI als „vorübergehend“ registriert. Der typisierte Client kann direkt eingefügt und verwendet werden:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Pages/TypedClient.cshtml.cs?name=snippet1&highlight=11-14,20)]

Die Konfiguration kann für einen typisierten Client während der Registrierung in `Startup.ConfigureServices` angegeben werden, anstatt im Konstruktor des typisierten Clients, falls dies bevorzugt wird:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet4)]

Es ist möglich, `HttpClient` vollständig in einem typisierten Client zu kapseln. Anstatt ihn als eine Eigenschaft zur Verfügung zu stellen, können öffentliche Methoden bereitgestellt werden, die die `HttpClient`-Instanz intern aufrufen.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/GitHub/RepoService.cs?name=snippet1&highlight=4)]

Im vorangehenden Code wird `HttpClient` als ein privates Feld gespeichert. Alle Zugriffe, die externe Aufrufe durchführen, durchlaufen die Methode `GetRepos`.

### <a name="generated-clients"></a>Generierte Clients

`IHttpClientFactory` kann in Verbindung mit anderen Bibliotheken von Drittanbietern verwendet werden, z.B. [Refit](https://github.com/paulcbetts/refit). Refit ist eine REST-Bibliothek für .NET. Sie konvertiert REST-APIs in Live-Schnittstellen. Eine Implementierung der Schnittstelle wird dynamisch von `RestService` generiert. `HttpClient` wird für die externen HTTP-Aufrufe verwendet.

Eine Schnittstelle und eine Antwort werden definiert, um die externe API und die Antwort darzustellen:

```csharp
public interface IHelloClient
{
    [Get("/helloworld")]
    Task<Reply> GetMessageAsync();
}

public class Reply
{
    public string Message { get; set; }
}
```

Ein typisierter Client kann hinzugefügt werden. Refit wird zum Generieren der Implementierung verwendet:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddHttpClient("hello", c =>
    {
        c.BaseAddress = new Uri("http://localhost:5000");
    })
    .AddTypedClient(c => Refit.RestService.For<IHelloClient>(c));

    services.AddMvc();
}
```

Die definierte Schnittstelle kann bei Bedarf mit der von DI und Refit bereitgestellten Implementierung verwendet werden:

```csharp
[ApiController]
public class ValuesController : ControllerBase
{
    private readonly IHelloClient _client;

    public ValuesController(IHelloClient client)
    {
        _client = client;
    }

    [HttpGet("/")]
    public async Task<ActionResult<Reply>> Index()
    {
        return await _client.GetMessageAsync();
    }
}
```

## <a name="outgoing-request-middleware"></a>Middleware für ausgehende Anforderungen

`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden können. `IHttpClientFactory` erleichtert das Definieren der Handler, die für jeden benannten Client angewendet werden. Die Registrierung und Verkettung von mehreren Handlern wird unterstützt, um eine Pipeline für die Middleware für ausgehende Anforderungen zu erstellen. Jeder dieser Handler kann vor und nach der ausgehenden Anforderung Aufgaben ausführen. Dieses Muster ähnelt der eingehenden Pipeline für Middleware in ASP.NET Core. Das Muster bietet einen Mechanismus zum Verwalten von übergreifenden Belangen bezüglich HTTP-Anforderungen, einschließlich der Zwischenspeicherung, Fehlerbehandlung, Serialisierung und Protokollierung.

Definieren Sie zum Erstellen eines Handlers eine von <xref:System.Net.Http.DelegatingHandler> abgeleitete Klasse. Überschreiben Sie die Methode `SendAsync`, um Code auszuführen, bevor die Anforderung an den nächsten Handler in der Pipeline übergeben wird:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Handlers/ValidateHeaderHandler.cs?name=snippet1)]

Der vorangehende Code definiert einen einfachen Handler. Er überprüft, ob ein `X-API-KEY`-Header in die Anforderung eingefügt wurde. Wenn der Header fehlt, kann er den HTTP-Aufruf vermeiden und eine geeignete Antwort zurückgeben.

Während der Registrierung kann mindestens ein Handler der Konfiguration eines `HttpClient` hinzugefügt werden. Dieser Vorgang wird über die Erweiterungsmethoden von <xref:Microsoft.Extensions.DependencyInjection.IHttpClientBuilder> ermöglicht.

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet5)]

::: moniker range=">= aspnetcore-2.2"

Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert. Die `IHttpClientFactory` erstellt einen separaten DI-Bereich für jeden Handler. Handler können von Diensten eines beliebigen Bereichs abhängen. Dienste, von denen Handler abhängig sind, werden verworfen, wenn der Handler verworfen wird.

Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, was den Typ für den Handler übergibt.

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Im vorangehenden Code ist `ValidateHeaderHandler` mit DI registriert. Der Handler **muss** in DI als vorübergehender Dienst registriert sein, niemals bereichsbezogen. Wenn der Handler als bereichsbezogener Dienst registriert ist und alle Dienste, von denen der Handler abhängig ist, gelöscht werden können:

* Die Dienste des Handlers können verworfen werden, bevor der Handler den Gültigkeitsbereich verlässt.
* Der verworfenen Handlerdienst bewirken, dass der Handler fehlschlägt.

Nach der Registrierung kann <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.AddHttpMessageHandler*> aufgerufen werden, wobei der Typ für den Handler übergeben wird.

::: moniker-end

Mehrere Handler können in der Reihenfolge registriert werden, in der sie ausgeführt werden sollen. Jeder Handler umschließt den nächsten Handler, bis der endgültige `HttpClientHandler` die Anforderung ausführt:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet6)]

Verwenden Sie einen der folgenden Ansätze, um den anforderungsspezifischen Zustand mit Meldungshandlern zu teilen:

* Übergeben Sie Daten mit `HttpRequestMessage.Properties` an den Handler.
* Greifen Sie mit `IHttpContextAccessor` auf die aktuelle Anforderung zu.
* Erstellen Sie ein benutzerdefiniertes `AsyncLocal`-Speicherobjekt, um die Daten zu übergeben.

## <a name="use-polly-based-handlers"></a>Verwenden von Polly-basierten Handlern

`IHttpClientFactory` integriert mit der beliebten Drittanbieter-Bibliothek namens [Polly](https://github.com/App-vNext/Polly). Polly ist eine umfassende Bibliothek für die Behandlung von beständigen und vorübergehenden Fehlern für .NET. Entwicklern wird damit ermöglicht, Richtlinien wie Wiederholungsrichtlinien, Trennschalterrichtlinien, Timeout-Richtlinien, Bulkhead Isolation-Richtlinien und Ausweichrichtlinien in einer flüssigen und threadsicheren Art und Weise auszudrücken.

Erweiterungsmethoden werden bereitgestellt, um die Verwendung von Polly-Richtlinien mit konfigurierten `HttpClient`-Instanzen zu aktivieren. Die Polly-Erweiterungen:

* Unterstützen das Hinzufügen von Polly-basierten Handlern zu Clients.
* Können nach Installation des [Microsoft.Extensions.Http.Polly](https://www.nuget.org/packages/Microsoft.Extensions.Http.Polly/)-NuGet-Pakets verwendet werden. Das Paket ist nicht im freigegebenen ASP.NET Core-Framework enthalten.

### <a name="handle-transient-faults"></a>Behandeln von vorrübergehenden Fehlern

Am häufigsten treten Fehler auf, wenn externe HTTP-Aufrufe vorübergehend sind. Eine praktische Erweiterungsmethode namens `AddTransientHttpErrorPolicy` ist enthalten. Sie ermöglicht das Definieren von Richtlinien zum Behandeln vorübergehender Fehler. Richtlinien, die mit dieser Erweiterungsmethode konfiguriert wurden, behandeln `HttpRequestException`, HTTP 5xx-Antworten und HTTP 408-Antworten.

Die `AddTransientHttpErrorPolicy`-Erweiterung kann in `Startup.ConfigureServices` verwendet werden. Die Erweiterung ermöglicht den Zugriff auf ein `PolicyBuilder`-Objekt, das für die Fehlerbehandlung konfiguriert wurde und mögliche vorübergehende Fehler darstellt:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet7)]

Im vorangehenden Code wird eine `WaitAndRetryAsync`-Richtlinie definiert. Anforderungsfehler werden bis zu dreimal mit einer Verzögerung von 600 ms wiederholt.

### <a name="dynamically-select-policies"></a>Dynamisches Auswählen von Richtlinien

Es gibt zusätzliche Erweiterungsmethoden, die zum Hinzufügen von Polly-basierten Handlern verwendet werden können. Eine dieser Erweiterungen ist `AddPolicyHandler`, die über mehrere Überladungen verfügt. Eine Überladung ermöglicht, dass die Anforderung beim Definieren der zu verwendenden Richtlinie überprüft werden kann:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet8)]

Im vorangehenden Code wird ein 10 Sekunden langer Timeout angewendet, wenn die ausgehende Anforderung eine HTTP GET-Anforderung ist. Für alle anderen HTTP-Methoden wird ein 30 Sekunden langer Timeout verwendet.

### <a name="add-multiple-polly-handlers"></a>Hinzufügen mehrerer Polly-Handler

Es ist üblich, Polly-Richtlinien zu schachteln, um erweiterte Funktionen bereitzustellen:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet9)]

Im vorangehenden Beispiel werden zwei Handler hinzugefügt. Der Erste verwendet die `AddTransientHttpErrorPolicy`-Erweiterung, um eine Wiederholungsrichtlinie hinzuzufügen. Fehlgeschlagene Anforderungen werden bis zu drei Mal wiederholt. Der zweite Aufruf von `AddTransientHttpErrorPolicy` fügt eine Trennschalterrichtlinie hinzu. Weitere externe Anforderungen werden 30 Sekunden lang blockiert, wenn fünf Fehlversuche hintereinander stattfinden. Trennschalterrichtlinien sind zustandsbehaftet. Alle Aufrufe über diesen Client teilen den gleichen Schalterzustand.

### <a name="add-policies-from-the-polly-registry"></a>Hinzufügen von Richtlinien aus der Polly-Registrierung

Eine Methode zum Verwalten regelmäßig genutzter Richtlinien ist, Sie einmal zu definieren und mit `PolicyRegistry` zu registrieren. Eine Erweiterungsmethode wird bereitgestellt, die einem Handler ermöglicht, mithilfe einer Richtlinie aus der Registrierung hinzugefügt zu werden:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet10)]

Im oben stehenden Code werden zwei Richtlinien registriert, wenn `PolicyRegistry` zu `ServiceCollection` hinzugefügt wird. Damit eine Richtlinie aus der Registrierung verwendet werden kann, wird die Methode `AddPolicyHandlerFromRegistry` verwendet. Dabei wird der Name der anzuwendenden Richtlinie übergeben.

Weitere Informationen zu `IHttpClientFactory` und Polly-Integrationen finden Sie im [Polly Wiki](https://github.com/App-vNext/Polly/wiki/Polly-and-HttpClientFactory).

## <a name="httpclient-and-lifetime-management"></a>HttpClient und die Verwaltung der Lebensdauer

Bei jedem Aufruf von `CreateClient` in der `IHttpClientFactory` wird eine neue Instanz von `HttpClient` zurückgegeben. Es gibt einen <xref:System.Net.Http.HttpMessageHandler> pro benanntem Client. Die Factory verwaltet die Lebensdauer der `HttpMessageHandler`-Instanzen.

`IHttpClientFactory` legt die `HttpMessageHandler`-Instanzen zusammen, die von der Factory zum Reduzieren des Ressourcenverbrauchs erstellt wurden. Eine `HttpMessageHandler`-Instanz kann aus dem Pool wiederverwendet werden, wenn eine neue `HttpClient`-Instanz erstellt wird und deren Lebensdauer noch nicht abgelaufen ist.

Das Zusammenlegen von Handlern ist ein wünschenswerter Vorgang, da jeder Handler in der Regel seine zugrunde liegenden HTTP-Verbindungen selbst verwaltet. Wenn mehr Handler als nötig erstellt werden, können Verzögerungen bei Verbindungen entstehen. Einige Handler halten Verbindungen auch unbegrenzt offen, was verhindert, dass der Handler auf DNS-Änderungen reagiert.

Die Standardlebensdauer von Handlern beträgt zwei Minuten. Der Standardwert kann für jeden benannten Client überschrieben werden. Rufen Sie <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.SetHandlerLifetime*> auf dem bei der Erstellung des Clients zurückgegebenen `IHttpClientBuilder` auf, um den Wert zu überschreiben:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet11)]

Das Verwerfen des Client ist nicht erforderlich. Beim Verwerfen werden ausgehende Anforderungen abgebrochen, und es wird sichergestellt, dass die angegebene `HttpClient`-Instanz nach dem Aufruf von <xref:System.IDisposable.Dispose*> nicht mehr verwendet werden kann. `IHttpClientFactory` verfolgt von `HttpClient`-Instanzen verwendete Ressourcen nach und verwirft sie. Die `HttpClient`-Instanzen können im Allgemeinen als .NET-Objekte behandelt werden, die nicht verworfen werden müssen.

Das Beibehalten einer einzelnen `HttpClient`-Instanz für einen langen Zeitraum ist ein allgemeines Muster, das vor der Einführung von `IHttpClientFactory` verwendet wurde. Dieses Muster wird nach der Migration zu `IHttpClientFactory` überflüssig.

## <a name="logging"></a>Protokollierung

Über `IHttpClientFactory` erstellte Clients zeichnen Protokollmeldungen für alle Anforderungen auf. Aktivieren Sie die entsprechende Informationsebene in Ihrer Protokollierungskonfiguration, um die Standardprotokollmeldungen anzuzeigen. Zusätzliche Protokollierung, z.B. das Protokollieren von Anforderungsheadern, wird nur auf der Ablaufverfolgungsebene enthalten.

Die Protokollierungskategorie für jeden Client enthält den Namen des Clients. Ein Client namens *MyNamedClient* protokolliert beispielsweise Meldungen mit der Kategorie `System.Net.Http.HttpClient.MyNamedClient.LogicalHandler`. Meldungen mit dem Suffix *LogicalHandler* treten außerhalb der Anforderungshandlerpipeline auf. In der Anforderung werden Meldungen protokolliert, bevor andere Handler in der Pipeline sie verarbeitet haben. In der Antwort werden Meldungen protokolliert, nachdem andere Handler in der Pipeline die Antwort empfangen haben.

Die Protokollierung tritt ebenfalls innerhalb der Anforderungshandlerpipeline auf. Im Beispiel *MyNamedClient* werden diese Meldungen für die Protokollkategorie `System.Net.Http.HttpClient.MyNamedClient.ClientHandler` protokolliert. Dies findet für die Anforderung statt, nachdem alle anderen Handler ausgeführt wurden und bevor die Anforderung an das Netzwerk gesendet wird. Diese Protokollierung enthält den Zustand der Antwort in der Antwort, bevor sie an die Handlerpipeline zurückgegeben wird.

Das Aktivieren der Protokollierung außerhalb und innerhalb der Pipeline ermöglicht die Überprüfung der Änderungen, die durch andere Handler in der Pipeline erfolgt sind. Dies kann beispielsweise die Änderungen an Anforderungsheadern oder am Statuscode der Antwort enthalten.

Das Einschließen des Namens des Clients in der Protokollkategorie aktiviert bei Bedarf die Protokollfilterung für spezifische benannte Clients.

## <a name="configure-the-httpmessagehandler"></a>Konfigurieren von HttpMessageHandler

Es kann notwendig sein, die Konfiguration des inneren von einem Client verwendeten `HttpMessageHandler` zu steuern.

`IHttpClientBuilder` wird zurückgegeben, wenn benannte oder typisierte Clients hinzugefügt werden. Die Erweiterungsmethode <xref:Microsoft.Extensions.DependencyInjection.HttpClientBuilderExtensions.ConfigurePrimaryHttpMessageHandler*> kann zum Definieren eines Delegaten verwendet werden. Der Delegat wird verwendet, um den primären `HttpMessageHandler` zu erstellen und konfigurieren, der von dem Client verwendet wird:

[!code-csharp[](http-requests/samples/2.x/HttpClientFactorySample/Startup.cs?name=snippet12)]

## <a name="use-ihttpclientfactory-in-a-console-app"></a>Verwenden von IHttpClientFactory in einer Konsolen-App

Fügen Sie in einer Konsolen-App dem Projekt die folgenden Paketverweise hinzu:

* [Microsoft.Extensions.Hosting](https://www.nuget.org/packages/Microsoft.Extensions.Hosting)
* [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http)

Im folgenden Beispiel:

* <xref:System.Net.Http.IHttpClientFactory> ist im Dienstcontainer des [generischen Hosts](xref:fundamentals/host/generic-host) registriert.
* `MyService` erstellt eine Clientfactoryinstanz aus dem-Dienst, der zum Erstellen eines `HttpClient`verwendet wird. `HttpClient` wird zum Abrufen einer Webseite verwendet.
* `Main` erstellt einen Bereich, um die `GetPage`-Methode des Diensts auszuführen und die ersten 500 Zeichen des Webseiteninhalts in die Konsole zu schreiben.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](http-requests/samples/3.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](http-requests/samples/2.x/HttpClientFactoryConsoleSample/Program.cs?highlight=14-15,20,26-27,59-62)]

::: moniker-end

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwenden von HttpClientFactory zur Implementierung robuster HTTP-Anforderungen](/dotnet/standard/microservices-architecture/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)
* [Implementieren von Wiederholungen von HTTP-Aufrufen mit exponentiellem Backoff mit HttpClientFactory und Polly-Richtlinien](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-http-call-retries-exponential-backoff-polly)
* [Implementieren des Trennschaltermusters](/dotnet/standard/microservices-architecture/implement-resilient-applications/implement-circuit-breaker-pattern)
