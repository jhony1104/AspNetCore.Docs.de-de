---
title: Sichere ASP.NET Blazor Core Server-Apps
author: guardrex
description: Erfahren Sie, wie Blazor Sie Sicherheitsbedrohungen für Server-Apps verringern.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/server
ms.openlocfilehash: bd03f811d0425fdfdb7bbbc24fea5481b49b8ed3
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80626023"
---
# <a name="secure-aspnet-core-blazor-server-apps"></a>Sichere ASP.NET Core Blazor Server-Apps

Von [Javier Calvarro Nelson](https://github.com/javiercn)

Blazor Server-Apps verwenden ein *zustandsbehaftetes* Datenverarbeitungsmodell, bei dem Server und Client eine langjährige Beziehung pflegen. Der persistente Zustand wird von einer [Schaltung](xref:blazor/state-management)verwaltet, die Verbindungen überspannen kann, die auch potenziell langlebig sind.

Wenn ein Benutzer eine Blazor Server-Site besucht, erstellt der Server eine Schaltung im Speicher des Servers. Die Schaltung zeigt dem Browser an, welche Inhalte gerendert werden sollen, und reagiert auf Ereignisse, z. B. wenn der Benutzer eine Schaltfläche in der Benutzeroberfläche auswählt. Um diese Aktionen auszuführen, ruft eine Verbindung JavaScript-Funktionen im Browser des Benutzers und .NET-Methoden auf dem Server auf. Diese zweiseitige JavaScript-basierte Interaktion wird als [JavaScript-Interop (JS-Interop)](xref:blazor/call-javascript-from-dotnet)bezeichnet.

Da JS-Interop über das Internet auftritt und der Client einen Remotebrowser verwendet, teilen Blazor Server-Apps die meisten Sicherheitsbedenken für Web-Apps. In diesem Thema werden häufige Bedrohungen für Blazor Server-Apps beschrieben und Anleitungen zur Bedrohungsminderung bereitgestellt, die sich auf Apps mit Internetanschluss konzentrieren.

In eingeschränkten Umgebungen, z. B. in Unternehmensnetzwerken oder Intranets, finden Sie einige der Minderungsrichtlinien:

* Gilt nicht in der eingeschränkten Umgebung.
* Die Implementierung lohnt sich nicht, da das Sicherheitsrisiko in einer eingeschränkten Umgebung gering ist.

## <a name="blazor-server-project-template"></a>Blazor Server-Projektvorlage

Die Blazor Server-Projektvorlage kann für die Authentifizierung konfiguriert werden, wenn das Projekt erstellt wird.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Befolgen Sie die Visual Studio-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen.

Nachdem Sie im Dialogfeld **Neue ASP.NET Core-Webanwendung erstellen** die Vorlage **Blazor-Server-App** ausgewählt haben, wählen Sie im Dialogfeld **Authentifizierung** die Option **Ändern**.

Ein Dialogfeld wird geöffnet, in dem dieselben Authentifizierungsmechanismen angeboten werden, die auch für andere ASP.NET-Core-Projekte verfügbar sind:

* **Keine Authentifizierung**
* **Einzelne Benutzerkonten** &ndash; Benutzerkonten können gespeichert werden:
  * Innerhalb der App anhand des Systems [Identität](xref:security/authentication/identity) von ASP.NET Core.
  * Mit [Azure AD B2C](xref:security/authentication/azure-ad-b2c).
* **Arbeits- oder Schulkonten**
* **Windows-Authentifizierung**

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Befolgen Sie die Visual Studio Code-Anweisungen im Artikel <xref:blazor/get-started>, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.

| Authentifizierungsmechanismus                                                                 | Wert vom Typ `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Keine Authentifizierung                                                                        | `None`                   |
| Einzelkonto<br>In der App mit ASP.NET Core-Identität gespeicherte Benutzer                        | `Individual`             |
| Einzelkonto<br>In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer | `IndividualB2C`          |
| Geschäfts-, Schul- oder Unikonten<br>Organisationauthentifizierung für einzelne Mandanten            | `SingleOrg`              |
| Geschäfts-, Schul- oder Unikonten<br>Organisationauthentifizierung für mehrere Mandanten           | `MultiOrg`               |
| Windows-Authentifizierung                                                                   | `Windows`                |

Der Befehl erstellt einen Ordner mit dem für den Platzhalter `{APP NAME}` angegebenen Wert und verwendet den Ordnernamen als Namen der App. Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.

# <a name="visual-studio-for-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Befolgen Sie die Anleitung <xref:blazor/get-started> von Visual Studio für Mac im Artikel.

1. Wählen Sie im Schritt **Konfigurieren der neuen Blazor Server-App** im Dropdown-Dropdown **"Authentifizierung"** **(in-app) die Option Individuelle Authentifizierung (in-App)** aus.

1. Die App wird für einzelne Benutzer erstellt, die in der App mit ASP.NET Core Identity gespeichert sind.

# <a name="net-core-cli"></a>[.NET Core-CLI](#tab/netcore-cli/)

Befolgen Sie die Richtlinien <xref:blazor/get-started> von .NET Core CLI im Artikel, um ein neues Blazor Server-Projekt mit einem Authentifizierungsmechanismus zu erstellen:

```dotnetcli
dotnet new blazorserver -o {APP NAME} -au {AUTHENTICATION}
```

Zulässige Authentifizierungswerte (`{AUTHENTICATION}`) werden in der folgenden Tabelle angezeigt.

| Authentifizierungsmechanismus                                                                 | Wert vom Typ `{AUTHENTICATION}` |
| ---------------------------------------------------------------------------------------- | :----------------------: |
| Keine Authentifizierung                                                                        | `None`                   |
| Einzelkonto<br>In der App mit ASP.NET Core-Identität gespeicherte Benutzer                        | `Individual`             |
| Einzelkonto<br>In [Azure AD B2C](xref:security/authentication/azure-ad-b2c) gespeicherte Benutzer | `IndividualB2C`          |
| Geschäfts-, Schul- oder Unikonten<br>Organisationauthentifizierung für einzelne Mandanten            | `SingleOrg`              |
| Geschäfts-, Schul- oder Unikonten<br>Organisationauthentifizierung für mehrere Mandanten           | `MultiOrg`               |
| Windows-Authentifizierung                                                                   | `Windows`                |

Der Befehl erstellt einen Ordner mit dem für den Platzhalter `{APP NAME}` angegebenen Wert und verwendet den Ordnernamen als Namen der App. Weitere Informationen finden Sie im Befehl [dotnet new](/dotnet/core/tools/dotnet-new) im Leitfaden für .NET Core.

---

## <a name="pass-tokens-to-a-blazor-server-app"></a>Übergeben von Token an eine Blazor Server-App

Authentifizieren Sie die Blazor Server-App wie mit einer normalen Razor Pages- oder MVC-App. Bereitstellen und Speichern der Token im Authentifizierungscookie. Beispiel:

```csharp
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

...

services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
{
    options.ResponseType = "code";
    options.SaveTokens = true;

    options.Scope.Add("offline_access");
    options.Scope.Add("{SCOPE}");
    options.Resource = "{RESOURCE}";
});
```

Beispielcode, einschließlich eines `Startup.ConfigureServices` vollständigen Beispiels, finden Sie in den [Übergabetoken an eine serverseitige Blazor-Anwendung](https://github.com/javiercn/blazor-server-aad-sample).

Definieren Sie eine Klasse, die im ursprünglichen App-Status mit den Zugriffs- und Aktualisierungstoken übergeben werden soll:

```csharp
public class InitialApplicationState
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

Definieren **scoped** Sie einen Bereichstokenanbieterdienst, der in der Blazor-App verwendet werden kann, um die Token aus DI aufzulösen:

```csharp
using System;
using System.Security.Claims;
using System.Threading.Tasks;

public class TokenProvider
{
    public string AccessToken { get; set; }
    public string RefreshToken { get; set; }
}
```

In `Startup.ConfigureServices`fügen Sie Dienste hinzu für:

* `IHttpClientFactory`
* `TokenProvider`

```csharp
services.AddHttpClient();
services.AddScoped<TokenProvider>();
```

Erstellen und instanziausen sie in `InitialApplicationState` der Datei *_Host.cshtml* erstellen und instanzionnen und übergeben Sie sie als Parameter an die App:

```cshtml
@using Microsoft.AspNetCore.Authentication

...

@{
    var tokens = new InitialApplicationState
    {
        AccessToken = await HttpContext.GetTokenAsync("access_token"),
        RefreshToken = await HttpContext.GetTokenAsync("refresh_token")
    };
}

<app>
    <component type="typeof(App)" param-InitialState="tokens" 
        render-mode="ServerPrerendered" />
</app>
```

Lösen `App` Sie in der Komponente (*App.razor*) den Dienst auf und initialisieren Sie ihn mit den Daten aus dem Parameter:

```razor
@inject TokenProvider TokensProvider

...

@code {
    [Parameter]
    public InitialApplicationState InitialState { get; set; }

    protected override Task OnInitializedAsync()
    {
        TokensProvider.AccessToken = InitialState.AccessToken;
        TokensProvider.RefreshToken = InitialState.RefreshToken;

        return base.OnInitializedAsync();
    }
}
```

Fügen Sie in dem Dienst, der eine sichere API-Anforderung ausführt, den Tokenanbieter ein, und rufen Sie das Token ab, um die API aufzurufen:

```csharp
public class WeatherForecastService
{
    private readonly TokenProvider _store;

    public WeatherForecastService(IHttpClientFactory clientFactory, 
        TokenProvider tokenProvider)
    {
        Client = clientFactory.CreateClient();
        _store = tokenProvider;
    }

    public HttpClient Client { get; }

    public async Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        var token = _store.AccessToken;
        var request = new HttpRequestMessage(HttpMethod.Get, 
            "https://localhost:5003/WeatherForecast");
        request.Headers.Add("Authorization", $"Bearer {token}");
        var response = await Client.SendAsync(request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadAsAsync<WeatherForecast[]>();
    }
}
```

## <a name="resource-exhaustion"></a>Ressourcenerschöpfung

Ressourcenerschöpfung kann auftreten, wenn ein Client mit dem Server interagiert und dazu führt, dass der Server übermäßige Ressourcen verbraucht. Übermäßiger Ressourcenverbrauch wirkt sich in erster Linie auf:

* [CPU](#cpu)
* [Memory](#memory)
* [Clientverbindungen](#client-connections)

DoS-Angriffe (Denial of Service) zielen in der Regel darauf ab, die Ressourcen einer App oder eines Servers auszuschöpfen. Die Ressourcenerschöpfung ist jedoch nicht unbedingt das Ergebnis eines Angriffs auf das System. Beispielsweise können endliche Ressourcen aufgrund der hohen Benutzernachfrage erschöpft sein. DoS wird im Abschnitt [DoS-Angriffe (Denial of Service)](#denial-of-service-dos-attacks) weiter behandelt.

Ressourcen außerhalb des Blazor-Frameworks, z. B. Datenbanken und Dateihandles (zum Lesen und Schreiben von Dateien), können ebenfalls ressourcenerabschöpfung erfahren. Weitere Informationen finden Sie unter <xref:performance/performance-best-practices>.

### <a name="cpu"></a>CPU

CPU-Erschöpfung kann auftreten, wenn ein oder mehrere Clients den Server zwingen, intensive CPU-Arbeiten durchzuführen.

Betrachten Sie beispielsweise eine Blazor Server-App, die eine *Fibonnacci-Nummer*berechnet. Eine Fibonnacci-Nummer wird aus einer Fibonnacci-Sequenz erzeugt, wobei jede Zahl in der Sequenz die Summe der beiden vorhergehenden Zahlen ist. Der Arbeitsaufwand, der erforderlich ist, um die Antwort zu erreichen, hängt von der Länge der Sequenz und der Größe des Anfangswerts ab. Wenn die App der Anforderung eines Clients keine Grenzen einhält, können die CPU-intensiven Berechnungen die Zeit der CPU dominieren und die Leistung anderer Aufgaben beeinträchtigen. Übermäßiger Ressourcenverbrauch ist ein Sicherheitsproblem, das sich auf die Verfügbarkeit auswirkt.

CPU-Erschöpfung ist ein Problem für alle öffentlich zugänglichen Apps. In normalen Web-Apps, Anfragen und Verbindungen Zeitauszeit als Schutz, aber Blazor Server-Apps bieten nicht die gleichen Sicherheitsvorkehrungen. Blazor Server-Apps müssen entsprechende Prüfungen und Grenzwerte enthalten, bevor potenziell CPU-intensive Arbeiten ausgeführt werden können.

### <a name="memory"></a>Arbeitsspeicher

Speicherentwirrung kann auftreten, wenn ein oder mehrere Clients den Server zwingen, eine große Menge an Arbeitsspeicher zu verbrauchen.

Betrachten Sie beispielsweise eine Blazor-Server-Seite-App mit einer Komponente, die eine Liste von Elementen akzeptiert und anzeigt. Wenn die Blazor-App keine Beschränkungen für die Anzahl der zulässigen Elemente oder die Anzahl der Elemente setzt, die dem Client wiedergegeben werden, kann die speicherintensive Verarbeitung und das Rendering den Speicher des Servers so stark dominieren, dass die Leistung des Servers leidet. Der Server kann abstürzen oder so langsam sein, dass er abgestürzt zu sein scheint.

Betrachten Sie das folgende Szenario zum Verwalten und Anzeigen einer Liste von Elementen, die sich auf ein potenzielles Speichererschöpfungsszenario auf dem Server beziehen:

* Die Elemente `List<MyItem>` in einer Eigenschaft oder einem Feld verwenden den Speicher des Servers. Wenn die App zulässt, dass die Liste der Elemente unbegrenzt wächst, besteht die Gefahr, dass dem Server der Arbeitsspeicher ausgeht. Wenn nicht mehr Arbeitsspeicher verfügbar ist, endet die aktuelle Sitzung (Absturz), und alle gleichzeitigen Sitzungen in dieser Serverinstanz erhalten eine Ausnahme ohne Arbeitsspeicher. Um zu verhindern, dass dieses Szenario auftritt, muss die App eine Datenstruktur verwenden, die gleichzeitigen Benutzern ein Elementlimit auferlegt.
* Wenn ein Pagingschema nicht zum Rendern verwendet wird, verwendet der Server zusätzlichen Speicher für Objekte, die in der Benutzeroberfläche nicht sichtbar sind. Ohne eine Begrenzung der Anzahl der Elemente kann der Speicherbedarf den verfügbaren Serverspeicher ausschöpfen. Um dieses Szenario zu verhindern, verwenden Sie einen der folgenden Ansätze:
  * Verwenden Sie paginierte Listen beim Rendern.
  * Zeigen Sie nur die ersten 100 bis 1.000 Elemente an, und der Benutzer muss Suchkriterien eingeben, um Elemente jenseits der angezeigten Elemente zu finden.
  * Implementieren Sie für ein erweitertes Renderingszenario Listen oder Raster, die *Virtualisierung*unterstützen. Mithilfe der Virtualisierung rendern Listen nur eine Teilmenge von Elementen, die derzeit für den Benutzer sichtbar sind. Wenn der Benutzer mit der Bildlaufleiste in der Benutzeroberfläche interagiert, rendert die Komponente nur die Elemente, die für die Anzeige erforderlich sind. Die Elemente, die derzeit nicht für die Anzeige benötigt werden, können im sekundären Speicher gespeichert werden, was der ideale Ansatz ist. Nicht angezeigte Elemente können auch im Speicher gespeichert werden, was weniger ideal ist.

Blazor Server-Apps bieten ein ähnliches Programmiermodell wie andere UI-Frameworks für zustandsbehaftete Apps, z. B. WPF, Windows Forms oder Blazor WebAssembly. Der Hauptunterschied besteht darin, dass in mehreren UI-Frameworks der von der App verbrauchte Speicher dem Client gehört und sich nur auf diesen einzelnen Client auswirkt. Beispielsweise wird eine Blazor WebAssembly-App vollständig auf dem Client ausgeführt und verwendet nur Clientspeicherressourcen. Im Blazor Server-Szenario gehört der von der App verbrauchte Speicher zum Server und wird von Clients auf der Serverinstanz gemeinsam genutzt.

Serverseitige Speicheranforderungen sind eine Überlegung für alle Blazor Server-Apps. Die meisten Web-Apps sind jedoch zustandslos, und der Speicher, der während der Verarbeitung einer Anforderung verwendet wird, wird freigegeben, wenn die Antwort zurückgegeben wird. Als allgemeine Empfehlung können Clients nicht zulassen, dass sie eine ungebundene Speichermenge zuweisen, wie in jeder anderen serverseitigen App, die Clientverbindungen beibehält. Der von einer Blazor Server-App belegte Speicher bleibt länger als eine einzelne Anforderung erhalten.

> [!NOTE]
> Während der Entwicklung kann ein Profiler verwendet oder eine Ablaufverfolgung erfasst werden, um speicheranforderungen von Clients zu bewerten. Ein Profiler oder eine Ablaufverfolgung erfasst nicht den Speicher, der einem bestimmten Client zugewiesen ist. Um die Speichernutzung eines bestimmten Clients während der Entwicklung zu erfassen, erfassen Sie ein Dump, und untersuchen Sie den Speicherbedarf aller Objekte, die in der Schaltung eines Benutzers verwurzelt sind.

### <a name="client-connections"></a>Clientverbindungen

Verbindungserlass kann auftreten, wenn ein oder mehrere Clients zu viele gleichzeitige Verbindungen zum Server öffnen, wodurch andere Clients daran gehindert werden, neue Verbindungen herzustellen.

Blazor-Clients stellen eine einzelne Verbindung pro Sitzung her und halten die Verbindung so lange geöffnet, wie das Browserfenster geöffnet ist. Die Anforderungen an den Server, alle Verbindungen zu verwalten, sind nicht spezifisch für Blazor-Apps. Angesichts der hartnäckigen Beständigkeit der Verbindungen und der zustandsbehafteten Natur von Blazor Server-Apps stellt die Erschöpfung der Verbindung ein größeres Risiko für die Verfügbarkeit der App dar.

Standardmäßig gibt es keine Begrenzung für die Anzahl der Verbindungen pro Benutzer für eine Blazor Server-App. Wenn die App ein Verbindungslimit erfordert, gehen Sie von einem oder mehreren der folgenden Ansätze aus:

* Erfordern Sie eine Authentifizierung, was natürlich die Fähigkeit nicht autorisierter Benutzer einschränkt, eine Verbindung mit der App herzustellen. Damit dieses Szenario wirksam ist, müssen Benutzer daran gehindert werden, neue Benutzer nach Belieben zur Bereitstellung zu stellen.
* Beschränken Sie die Anzahl der Verbindungen pro Benutzer. Das Einschränken von Verbindungen kann über die folgenden Ansätze erreicht werden. Üben Sie Vorsicht, damit legitime Benutzer auf die App zugreifen können (z. B. wenn ein Verbindungslimit basierend auf der IP-Adresse des Clients festgelegt wird).
  * Auf App-Ebene:
    * Erweiterbarkeit des Endpunktroutings.
    * Authentifizierung erforderlich, um eine Verbindung mit der App herzustellen und die aktiven Sitzungen pro Benutzer nachzuverfolgen.
    * Lehnen Sie neue Sitzungen ab, wenn Sie ein Limit erreichen.
    * Proxy WebSocket-Verbindungen mit einer App mithilfe eines Proxys, z. B. des [Azure SignalR-Dienstes,](/azure/azure-signalr/signalr-overview) der Verbindungen von Clients zu einer App beimultiplet. Dadurch wird eine App mit einer größeren Verbindungskapazität bereitgestellt, als ein einzelner Client einrichten kann, wodurch verhindert wird, dass ein Client die Verbindungen zum Server erschöpft.
  * Auf Serverebene: Verwenden Sie einen Proxy/Gateway vor der App. Azure [Front Door](/azure/frontdoor/front-door-overview) ermöglicht es Ihnen beispielsweise, das globale Routing von Webdatenverkehr zu einer App zu definieren, zu verwalten und zu überwachen.

## <a name="denial-of-service-dos-attacks"></a>DoS-Angriffe (Denial of Service)

DoS-Angriffe (Denial of Service) betreffen einen Client, der dazu führt, dass der Server eine oder mehrere seiner Ressourcen ausschöpft, sodass die App nicht verfügbar ist. Blazor Server-Apps enthalten einige Standardlimits und verlassen sich zum Schutz vor DoS-Angriffen auf andere ASP.NET Core- und SignalR-Grenzwerte:

| Blazor Server-App-Limit                            | BESCHREIBUNG | Standard |
| ------------------------------------------------------- | ----------- | ------- |
| `CircuitOptions.DisconnectedCircuitMaxRetained`         | Maximale Anzahl getrennter Schaltungen, die ein bestimmter Server gleichzeitig im Arbeitsspeicher hält. | 100 |
| `CircuitOptions.DisconnectedCircuitRetentionPeriod`     | Maximale Zeitdauer, die eine getrennte Schaltung im Speicher hält, bevor sie abgerissen wird. | 3 Minuten |
| `CircuitOptions.JSInteropDefaultCallTimeout`            | Maximale Zeitdauer, die der Server wartet, bevor ein asynchroner JavaScript-Funktionsaufruf ausdembesteht. | 1 Minute |
| `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches` | Maximale Anzahl nicht bestätigter Renderbatches, die der Server zu einem bestimmten Zeitpunkt im Arbeitsspeicher pro Schaltung aufbewahren, um eine robuste Wiederverbindung zu unterstützen. Nach Erreichen des Limits stellt der Server die Produktion neuer Renderbatches ein, bis ein oder mehrere Batches vom Client bestätigt wurden. | 10 |


| SignalR und ASP.NET Core-Limit             | BESCHREIBUNG | Standard |
| ------------------------------------------ | ----------- | ------- |
| `CircuitOptions.MaximumReceiveMessageSize` | Nachrichtengröße für eine einzelne Nachricht. | 32 KB |

## <a name="interactions-with-the-browser-client"></a>Interaktionen mit dem Browser (Client)

Ein Client interagiert mit dem Server über js interop-Ereignisdispatching und Renderabschluss. Die JS-Interopkommunikation verläuft in beide Richtungen zwischen JavaScript und .NET:

* Browserereignisse werden asynchron vom Client an den Server gesendet.
* Der Server reagiert bei Bedarf asynchron auf das erneute Rendern der Benutzeroberfläche.

### <a name="javascript-functions-invoked-from-net"></a>JavaScript-Funktionen, die von .NET aufgerufen werden

Für Aufrufe von .NET-Methoden an JavaScript:

* Alle Aufrufe haben ein konfigurierbares Timeout, nach <xref:System.OperationCanceledException> dem sie fehlschlagen, und geben einen an den Aufrufer zurück.
  * Es gibt ein Standardtimeout für`CircuitOptions.JSInteropDefaultCallTimeout`die Anrufe ( ) von einer Minute. Informationen zum Konfigurieren <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>dieses Limits finden Sie unter .
  * Ein Stornierungstoken kann bereitgestellt werden, um die Stornierung pro Anruf zu steuern. Verlassen Sie sich auf das Standardaufruftimeout, wo möglich, und zeitgebundene Anrufe an den Client, wenn ein Abbruchtoken bereitgestellt wird.
* Das Ergebnis eines JavaScript-Aufrufs kann nicht vertrauenswürdig sein. Der Blazor im Browser ausgeführte App-Client sucht nach der javaScript-Funktion, die aufgerufen werden soll. Die Funktion wird aufgerufen, und entweder wird das Ergebnis oder ein Fehler erzeugt. Ein böswilliger Client kann versuchen:
  * Verursachen Sie ein Problem in der App, indem Sie einen Fehler von der JavaScript-Funktion zurückgeben.
  * Induzieren Sie ein unbeabsichtigtes Verhalten auf dem Server, indem Sie ein unerwartetes Ergebnis von der JavaScript-Funktion zurückgeben.

Ergreifen Sie die folgenden Vorsichtsmaßnahmen, um sich vor den vorhergehenden Szenarien zu schützen:

* Umschließen sie JS-Interopaufrufe in [try-catch-Anweisungen,](/dotnet/csharp/language-reference/keywords/try-catch) um Fehler zu berücksichtigen, die während der Aufrufe auftreten können. Weitere Informationen finden Sie unter <xref:blazor/handle-errors#javascript-interop>.
* Überprüfen Sie Daten, die von JS-Interop-Aufrufen zurückgegeben werden, einschließlich Fehlermeldungen, bevor Sie Maßnahmen ergreifen.

### <a name="net-methods-invoked-from-the-browser"></a>.NET-Methoden, die vom Browser aufgerufen werden

Vertrauen Sie Aufrufen von JavaScript-Methoden nicht auf .NET-Methoden. Wenn eine .NET-Methode für JavaScript verfügbar gemacht wird, sollten Sie überlegen, wie die .NET-Methode aufgerufen wird:

* Behandeln Sie jede .NET-Methode, die javaScript verfügbar gemacht wird, wie ein öffentlicher Endpunkt für die App.
  * Überprüfen Sie die Eingabe.
    * Stellen Sie sicher, dass die Werte innerhalb der erwarteten Bereiche liegen.
    * Stellen Sie sicher, dass der Benutzer über die Berechtigung zum Ausführen der angeforderten Aktion verfügt.
  * Weisen Sie im Rahmen des .NET-Methodenaufrufs keine übermäßige Menge an Ressourcen zu. Führen Sie z. B. Prüfungen durch und setzen Sie Grenzwerte für die CPU- und Speichernutzung.
  * Berücksichtigen Sie, dass statische methoden und Instanzmethoden für JavaScript-Clients verfügbar gemacht werden können. Vermeiden Sie die gemeinsame Nutzung des Status über Sitzungen hinweg, es sei denn, der Entwurf fordert die freigabe des Status mit entsprechenden Einschränkungen.
    * Beispielsweise sollten Methoden, die durch `DotNetReference` Objekte verfügbar gemacht werden, die ursprünglich durch Abhängigkeitsinjektion (DI) erstellt wurden, als Bereichsobjekte registriert werden. Dies gilt für alle Blazor DI-Dienste, die von der Server-App verwendet werden.
    * Vermeiden Sie bei statischen Methoden das Festlegen eines Status, der nicht auf den Client beschränkt werden kann, es sei denn, die App gibt den Status für alle Benutzer auf einer Serverinstanz explizit frei.
  * Vermeiden Sie die Weitergabe von vom Benutzer bereitgestellten Daten in Parametern an JavaScript-Aufrufe. Wenn die Weitergabe von Daten in Parameter unbedingt erforderlich ist, stellen Sie sicher, dass der JavaScript-Code die Weitergabe der Daten verarbeitet, ohne [XSS-Schwachstellen (Cross-Site Scripting)](#cross-site-scripting-xss) einzuführen. Schreiben Sie z. B. keine vom Benutzer bereitgestellten Daten in `innerHTML` das Dokumentobjektmodell (DOM), indem Sie die Eigenschaft eines Elements festlegen. Erwägen Sie die Verwendung von Content `eval` Security Policy [(CSP),](https://developer.mozilla.org/docs/Web/HTTP/CSP) um andere unsichere JavaScript-Primitive zu deaktivieren.
* Vermeiden Sie die Implementierung des benutzerdefinierten Dispatchings von .NET-Aufrufen zusätzlich zur Dispatching-Implementierung des Frameworks. Das Verfügbarmachen von .NET-Methoden für den Browser Blazor ist ein erweitertes Szenario, das für die allgemeine Entwicklung nicht empfohlen wird.

### <a name="events"></a>Ereignisse

Ereignisse stellen einen Einstiegspunkt für eine Blazor Server-App bereit. Die gleichen Regeln zum Schutz von Endpunkten in Blazor Web-Apps gelten für die Ereignisbehandlung in Server-Apps. Ein böswilliger Client kann alle Daten senden, die er als Nutzlast für ein Ereignis senden möchte.

Beispiel:

* Ein Änderungsereignis `<select>` für a kann einen Wert senden, der nicht innerhalb der Optionen liegt, die die App dem Client präsentiert hat.
* An `<input>` kann beliebige Textdaten an den Server senden und dabei die clientseitige Validierung umgehen.

Die App muss die Daten für jedes Ereignis überprüfen, das die App verarbeitet. Die Blazor [Frameworkformskomponenten](xref:blazor/forms-validation) führen grundlegende Validierungen durch. Wenn die App benutzerdefinierte Formularkomponenten verwendet, muss benutzerdefinierter Code geschrieben werden, um Ereignisdaten entsprechend zu überprüfen.

BlazorServerereignisse sind asynchron, sodass mehrere Ereignisse an den Server gesendet werden können, bevor die App Zeit hat, durch die Erstellung eines neuen Renderings zu reagieren. Dies hat einige Sicherheitsauswirkungen zu berücksichtigen. Das Einschränken von Clientaktionen in der App muss innerhalb von Ereignishandlern ausgeführt werden und hängt nicht vom aktuellen gerenderten Ansichtszustand ab.

Betrachten Sie eine Zählerkomponente, die es einem Benutzer ermöglichen soll, einen Leistungsindikator maximal dreimal zu erhöhen. Die Schaltfläche zum Erhöhen des Zählers basiert `count`bedingt auf dem Wert von:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

Ein Client kann ein oder mehrere Inkrementereignisse aussenden, bevor das Framework ein neues Rendering dieser Komponente erzeugt. Das Ergebnis ist, dass die `count` vom Benutzer über das *Dreifache* erhöht werden kann, da die Schaltfläche von der Benutzeroberfläche nicht schnell genug entfernt wird. Der richtige Weg, um `count` die Grenze von drei Schritten zu erreichen, wird im folgenden Beispiel gezeigt:

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

Durch Hinzufügen `if (count < 3) { ... }` der Prüfung innerhalb des `count` Handlers basiert die Entscheidung zum Inkrementieren auf dem aktuellen App-Status. Die Entscheidung basiert nicht wie im vorherigen Beispiel auf dem Status der Benutzeroberfläche, der vorübergehend veraltet sein könnte.

### <a name="guard-against-multiple-dispatches"></a>Schutz vor mehreren Dispatches

Wenn ein Ereignisrückruf einen lang andauernden Vorgang asynchron aufruft, z. B. das Abrufen von Daten aus einem externen Dienst oder einer Datenbank, sollten Sie einen Guard verwenden. Der Wächter kann verhindern, dass der Benutzer mehrere Vorgänge in die Warteschlange stellt, während der Vorgang mit visueller Rückmeldung ausgeführt wird. Der folgende Komponentencode `true` `GetForecastAsync` wird festgelegt, `isLoading` während Daten vom Server abgerufen werden. Während `isLoading` `true`dieSchaltfläche ist, ist sie in der Benutzeroberfläche deaktiviert:

```razor
@page "/fetchdata"
@using BlazorServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

Das im vorherigen Beispiel gezeigte Schutzmuster funktioniert, wenn der `async` - `await` Hintergrundvorgang asynchron mit dem Muster ausgeführt wird.

### <a name="cancel-early-and-avoid-use-after-dispose"></a>Frühzeitig abbrechen und Verwendung nach Entsorgung vermeiden

Zusätzlich zur Verwendung eines Schutzes, wie im Abschnitt Schutz vor <xref:System.Threading.CancellationToken> mehreren [Dispatches](#guard-against-multiple-dispatches) beschrieben, sollten Sie eine verwenden, um lang andauernde Vorgänge abzubrechen, wenn die Komponente verworfen wird. Dieser Ansatz hat den zusätzlichen Vorteil, dass *die Verwendung nach der Entsorgung* in Komponenten vermieden wird:

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a>Vermeiden Sie Ereignisse, die große Datenmengen erzeugen

Einige DOM-Ereignisse, `oninput` `onscroll`z. B. oder , können eine große Datenmenge erzeugen. Vermeiden Sie die Blazor Verwendung dieser Ereignisse in Server-Apps.

## <a name="additional-security-guidance"></a>Zusätzliche Sicherheitshinweise

Die Anleitung zum Sichern ASP.NET Blazor Core-Apps gelten für Server-Apps und werden in den folgenden Abschnitten behandelt:

* [Protokollierung und sensible Daten](#logging-and-sensitive-data)
* [Schützen von Informationen während der Übertragung mit HTTPS](#protect-information-in-transit-with-https)
* [Cross-Site Scripting (XSS)](#cross-site-scripting-xss))
* [Ursprungsübergreifender Schutz](#cross-origin-protection)
* [Click-Jacking](#click-jacking)
* [Offene Umleitungen](#open-redirects)

### <a name="logging-and-sensitive-data"></a>Protokollierung und sensible Daten

JS-Interop-Interaktionen zwischen Client und Server werden <xref:Microsoft.Extensions.Logging.ILogger> in den Protokollen des Servers mit Instanzen aufgezeichnet. Blazorvermeidet die Protokollierung vertraulicher Informationen, z. B. tatsächliche Ereignisse oder JS-Interop-Ein- und -Ausgänge.

Wenn auf dem Server ein Fehler auftritt, benachrichtigt das Framework den Client und reißt die Sitzung ab. Standardmäßig erhält der Client eine allgemeine Fehlermeldung, die in den Entwicklertools des Browsers angezeigt werden kann.

Der clientseitige Fehler enthält nicht die Aufrufliste und enthält keine Details zur Fehlerursache, aber Serverprotokolle enthalten solche Informationen. Zu Entwicklungszwecken können dem Client vertrauliche Fehlerinformationen zur Verfügung gestellt werden, indem detaillierte Fehler aktiviert werden.

Aktivieren Sie detaillierte Fehler mit:

* `CircuitOptions.DetailedErrors`.
* `DetailedErrors`Konfigurationsschlüssel. Legen Sie beispielsweise `ASPNETCORE_DETAILEDERRORS` die Umgebungsvariable `true`auf den Wert von fest.

> [!WARNING]
> Das Aufstellen von Fehlerinformationen für Clients im Internet ist ein Sicherheitsrisiko, das immer vermieden werden sollte.

### <a name="protect-information-in-transit-with-https"></a>Schützen von Informationen während der Übertragung mit HTTPS

BlazorServer SignalR verwendet für die Kommunikation zwischen dem Client und dem Server. BlazorServer verwendet normalerweise SignalR den aushandelten Transport, der in der Regel WebSockets ist.

BlazorServer stellt die Integrität und Vertraulichkeit der zwischen dem Server und dem Client gesendeten Daten nicht sicher. Verwenden Sie immer HTTPS.

### <a name="cross-site-scripting-xss"></a>Siteübergreifende Skripterstellung (XSS)

Cross-Site Scripting (XSS) ermöglicht es einer nicht autorisierten Partei, beliebige Logik im Kontext des Browsers auszuführen. Eine kompromittierte App kann möglicherweise beliebigen Code auf dem Client ausführen. Die Sicherheitsanfälligkeit kann verwendet werden, um möglicherweise eine Reihe bösartiger Aktionen gegen den Server auszuführen:

* Senden Sie gefälschte/ungültige Ereignisse an den Server.
* Dispatch fail/invalid render completions.
* Vermeiden Sie das Dispatchen von Rendervervollständigungen.
* Senden Von Interopaufrufen aus JavaScript an .NET.
* Ändern Sie die Antwort von Interopaufrufen von .NET auf JavaScript.
* Vermeiden Sie das Versenden von .NET an JS-Interop-Ergebnisse.

Das Blazor Serverframework ergreift Maßnahmen zum Schutz vor einigen der vorherigen Bedrohungen:

* Beendet die Erstellung neuer UI-Aktualisierungen, wenn der Client keine Renderbatches bestätigt. Konfiguriert mit `CircuitOptions.MaxBufferedUnacknowledgedRenderBatches`.
* Wenn Sie einen .NET-zu-JavaScript-Aufruf nach einer Minute durchführen, ohne eine Antwort vom Client zu erhalten. Konfiguriert mit `CircuitOptions.JSInteropDefaultCallTimeout`.
* Führt eine grundlegende Validierung aller Eingaben aus dem Browser während des JS-Interops durch:
  * .NET-Referenzen sind gültig und werden vom Typ erwartet, der von der .NET-Methode erwartet wird.
  * Die Daten sind nicht falsch formatiert.
  * Die richtige Anzahl von Argumenten für die Methode ist in der Nutzlast vorhanden.
  * Die Argumente oder das Ergebnis können korrekt deserialisiert werden, bevor die Methode aufruft.
* Führt eine grundlegende Validierung aller Eingaben aus dem Browser aus ausgelösten Ereignissen durch:
  * Das Ereignis hat einen gültigen Typ.
  * Die Daten für das Ereignis können deserialisiert werden.
  * Dem Ereignis ist ein Ereignishandler zugeordnet.

Zusätzlich zu den Sicherheitsvorkehrungen, die das Framework implementiert, muss die App vom Entwickler codiert werden, um sich vor Bedrohungen zu schützen und geeignete Maßnahmen zu ergreifen:

* Überprüfen Sie immer Daten, wenn Sie Ereignisse behandeln.
* Ergreifen Sie beim Empfang ungültiger Daten geeignete Maßnahmen:
  * Ignorieren Sie die Daten, und geben Sie zurück. Dadurch kann die App die Verarbeitung von Anforderungen fortsetzen.
  * Wenn die App feststellt, dass die Eingabe illegitim ist und nicht vom legitimen Client erstellt werden kann, löst eine Ausnahme aus. Wenn Sie eine Ausnahme auslösen, wird die Schaltung heruntergeschraubt und die Sitzung beendet.
* Vertrauen Sie der Fehlermeldung nicht, die von Renderbatchvervollständigungen bereitgestellt wird, die in den Protokollen enthalten sind. Der Fehler wird vom Client bereitgestellt und kann im Allgemeinen nicht als vertrauenswürdig eingestuft werden, da der Client möglicherweise kompromittiert ist.
* Vertrauen Sie der Eingabe von JS-Interopaufrufen in beiden Richtungen zwischen JavaScript- und .NET-Methoden nicht.
* Die App ist dafür verantwortlich, zu überprüfen, ob der Inhalt von Argumenten und Ergebnissen gültig ist, auch wenn die Argumente oder Ergebnisse korrekt deserialisiert sind.

Damit eine XSS-Schwachstelle vorhanden ist, muss die App Benutzereingaben in die gerenderte Seite integrieren. BlazorServerkomponenten führen einen Kompilierungsschritt aus, bei dem das Markup in einer *Razor-Datei* in eine prozedurale C-Logik umgewandelt wird. Zur Laufzeit erstellt die C-Logik eine *Renderstruktur,* die die Elemente, den Text und die untergeordneten Komponenten beschreibt. Dies wird über eine Folge von JavaScript-Anweisungen auf das DOM des Browsers angewendet (oder im Falle des Prerenderings in HTML serialisiert):

* Benutzereingaben, die über die normale `@someStringValue`Razor-Syntax gerendert werden (z. B. ) machen keine XSS-Schwachstelle verfügbar, da die Razor-Syntax dem DOM über Befehle hinzugefügt wird, die nur Text schreiben können. Auch wenn der Wert HTML-Markup enthält, wird der Wert als statischer Text angezeigt. Beim Vorrendern ist die Ausgabe HTML-codiert, wodurch der Inhalt auch als statischer Text angezeigt wird.
* Skript-Tags sind nicht zulässig und sollten nicht in der Komponenten-Renderstruktur der App enthalten sein. Wenn ein Skript-Tag im Markup einer Komponente enthalten ist, wird ein Kompilierungsfehler generiert.
* Komponentenautoren können Komponenten in C-Code erstellen, ohne Razor zu verwenden. Der Komponentenautor ist für die Verwendung der richtigen APIs beim Ausgeben der Ausgabe verantwortlich. Verwenden Sie `builder.AddContent(0, someUserSuppliedString)` z. B. und *nicht* `builder.AddMarkupContent(0, someUserSuppliedString)`, da letztere eine XSS-Schwachstelle verursachen könnte.

Als Teil des Schutzes vor XSS-Angriffen sollten Sie die Implementierung von XSS-Abschwächungen in Betracht ziehen, z. B. [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).

Weitere Informationen finden Sie unter <xref:security/cross-site-scripting>.

### <a name="cross-origin-protection"></a>Ursprungsübergreifender Schutz

Ursprungsübergreifende Angriffe betreffen einen Client mit einem anderen Ursprung, der eine Aktion gegen den Server ausführt. Die böswillige Aktion ist in der Regel eine GET-Anforderung oder ein Formular-POST (Cross-Site Request Forgery, CSRF), aber das Öffnen eines bösartigen WebSocket ist auch möglich. BlazorServer-Apps bieten [die gleichen SignalR Garantien wie jede andere App, die das Hubprotokoll verwendet:](xref:signalr/security)

* BlazorAuf Server-Apps kann ursprungsübergreifend zugegriffen werden, es sei denn, es werden zusätzliche Maßnahmen ergriffen, um dies zu verhindern. Um den Ursprungsübergreifenden Zugriff zu deaktivieren, deaktivieren Sie entweder CORS im Endpunkt, indem Sie die CORS-Middleware zur Pipeline hinzufügen und die `DisableCorsAttribute` zu den Blazor Endpunktmetadaten hinzufügen, oder den Satz zulässiger Ursprünge einschränken, indem Sie für die [ursprungsübergreifende Ressourcenfreigabe konfigurieren. SignalR ](xref:signalr/security#cross-origin-resource-sharing)
* Wenn CORS aktiviert ist, sind möglicherweise zusätzliche Schritte erforderlich, um die App abhängig von der CORS-Konfiguration zu schützen. Wenn CORS global aktiviert ist, kann CORS für Blazor den `DisableCorsAttribute` Serverhub deaktiviert werden, `hub.MapBlazorHub()`indem die Metadaten nach dem Aufruf zu den Endpunktmetadaten hinzugefügt werden.

Weitere Informationen finden Sie unter <xref:security/anti-request-forgery>.

### <a name="click-jacking"></a>Click-Jacking

Click-Jacking beinhaltet das Rendern einer Website als `<iframe>` eine Website innerhalb einer Website von einem anderen Ursprung, um den Benutzer dazu zu bringen, Aktionen auf der angegriffenen Website auszuführen.

Um eine App vor dem `<iframe>`Rendern innerhalb einer zu schützen, verwenden Sie [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) und den `X-Frame-Options` Header. Weitere Informationen finden Sie unter [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).

### <a name="open-redirects"></a>Offene Umleitungen

Wenn Blazor eine Server-App-Sitzung gestartet wird, führt der Server eine grundlegende Überprüfung der URLs durch, die als Teil des Startens der Sitzung gesendet wurden. Das Framework überprüft, ob die Basis-URL ein übergeordnetes Element der aktuellen URL ist, bevor die Schaltung festgelegt wird. Vom Framework werden keine zusätzlichen Prüfungen durchgeführt.

Wenn ein Benutzer einen Link auf dem Client auswählt, wird die URL für den Link an den Server gesendet, der bestimmt, welche Aktion ausgeführt werden soll. Die App kann z. B. eine clientseitige Navigation durchführen oder dem Browser angeben, zum neuen Speicherort zu wechseln.

Komponenten können Navigationsanforderungen auch programmgesteuert über `NavigationManager`die Verwendung von auslösen. In solchen Szenarien führt die App möglicherweise eine clientseitige Navigation durch oder weist den Browser darauf hin, zum neuen Speicherort zu wechseln.

Komponenten müssen:

* Vermeiden Sie die Verwendung von Benutzereingaben als Teil der Navigationsaufrufargumente.
* Überprüfen Sie Argumente, um sicherzustellen, dass das Ziel von der App zugelassen wird.

Andernfalls kann ein böswilliger Benutzer den Browser zwingen, zu einer vom Angreifer kontrollierten Website zu wechseln. In diesem Szenario versucht der Angreifer die App, einige Benutzereingaben `NavigationManager.Navigate` als Teil des Aufrufs der Methode zu verwenden.

Diese Empfehlung gilt auch beim Rendern von Links als Teil der App:

* Verwenden Sie nach Möglichkeit relative Links.
* Überprüfen Sie, ob absolute Verknüpfungsziele gültig sind, bevor Sie sie in eine Seite einfügen.

Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Hinweise zur Authentifizierung und <xref:security/blazor/index>Autorisierung finden Sie unter .

## <a name="security-checklist"></a>Checkliste für die Sicherheit

Die folgende Liste von Sicherheitsüberlegungen ist nicht vollständig:

* Überprüfen sie Argumente aus Ereignissen.
* Überprüfen Sie Eingaben und Ergebnisse von JS-Interopaufrufen.
* Vermeiden Sie die Verwendung (oder Validierung im Voraus) Benutzereingaben für .NET für JS-Interop-Aufrufe.
* Verhindern Sie, dass der Client eine ungebundene Speichermenge zuweist.
  * Daten innerhalb der Komponente.
  * `DotNetObject`Verweise an den Client zurückgegeben.
* Schutz vor mehreren Dispatches.
* Abbrechen lang andauernder Vorgänge, wenn die Komponente freigegeben wird.
* Vermeiden Sie Ereignisse, die große Datenmengen erzeugen.
* Vermeiden Sie die Verwendung von `NavigationManager.Navigate` Benutzereingaben als Teil von Aufrufen an URLs und überprüfen Sie diese anhand einer Reihe zulässiger Ursprünge, wenn dies nicht möglich ist.
* Treffen Sie Autorisierungsentscheidungen nicht basierend auf dem Status der Benutzeroberfläche, sondern nur aus dem Komponentenstatus.
* Erwägen Sie die Verwendung von [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) zum Schutz vor XSS-Angriffen.
* Erwägen Sie die Verwendung von CSP- und [X-Frame-Optionen](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) zum Schutz vor Klick-Jacking.
* Stellen Sie sicher, dass die CORS-Einstellungen Blazor beim Aktivieren von CORS oder beim expliziten Deaktivieren von CORS für Apps geeignet sind.
* Testen Sie, um sicherzustellen, dass Blazor die serverseitigen Grenzwerte für die App eine akzeptable Benutzererfahrung ohne inakzeptable Senkwerte bieten.
