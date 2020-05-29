---
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="aspnet-core-blazor-routing"></a>ASP.NET Core Blazor-Routing

Von [Luke Latham](https://github.com/guardrex)

Erfahren Sie, wie Sie Anforderungen weiterleiten und die `NavLink`-Komponente verwenden, um Navigationslinks in Blazor-Apps zu erstellen.

## <a name="aspnet-core-endpoint-routing-integration"></a>Integration von ASP.NET Core-Endpunktrouting

Blazor Server ist in das [ASP.NET Core-Endpunktrouting](xref:fundamentals/routing) integriert. Eine ASP.NET Core-App ist so konfiguriert, dass sie eingehende Verbindungen für interaktive Komponenten mit `MapBlazorHub` in `Startup.Configure` akzeptiert:

[!code-csharp[](routing/samples_snapshot/3.x/Startup.cs?highlight=5)]

Die typischste Konfiguration ist die Weiterleitung aller Anforderungen an eine Razor-Seite, die als Host für den serverseitigen Teil der Blazor Server-App fungiert. Gemäß der Konvention wird die *Hostseite* normalerweise *_Host.cshtml* genannt. Die in der Hostdatei angegebene Route wird als *Fallbackroute* bezeichnet, da sie mit einer niedrigen Priorität bei der Routenanpassung arbeitet. Die Fallbackroute wird verwendet, wenn andere Routen nicht passen. Dadurch kann die App andere Controller und Seiten verwenden, ohne die Blazor Server-App zu beeinträchtigen.

## <a name="route-templates"></a>Routenvorlagen

Die `Router`-Komponente ermöglicht das Routing zu jeder Komponente mit einer bestimmten Route. Die `Router`-Komponente wird in der Datei *App.razor* angezeigt:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <p>Sorry, there's nothing at this address.</p>
    </NotFound>
</Router>
```

Wenn eine *.razor*-Datei mit einer `@page`-Anweisung kompiliert wird, erhält die generierte Klasse ein <xref:Microsoft.AspNetCore.Components.RouteAttribute>-Element, das die Routenvorlage angibt.

Zur Laufzeit führt die `RouteView`-Komponente Folgendes aus:

* Sie empfängt das `RouteData`-Element aus dem `Router`-Element zusammen mit den gewünschten Parametern.
* Sie rendert die angegebene Komponente mit ihrem Layout (oder einem optionalen Standardlayout) unter Verwendung der angegebenen Parameter.

Optional können Sie einen `DefaultLayout`-Parameter mit einer Layoutklasse angeben, die für Komponenten verwendet werden soll, die kein Layout festlegen. In den Blazor-Standardvorlagen wird die `MainLayout`-Komponente angegeben. *MainLayout.razor* befindet sich im *Freigabeordner* mit den Vorlagen für das Projekt. Weitere Informationen zu Layouts finden Sie unter <xref:blazor/layouts>.

Mehrere Routenvorlagen können auf eine Komponente angewendet werden. Die folgende Komponente antwortet auf Anforderungen für `/BlazorRoute` und `/DifferentBlazorRoute`:

```razor
@page "/BlazorRoute"
@page "/DifferentBlazorRoute"

<h1>Blazor routing</h1>
```

> [!IMPORTANT]
> Damit URLs korrekt aufgelöst werden können, muss die Anwendung ein `<base>`-Tag in ihrer *wwwwroot/index.html*-Datei (Blazor WebAssembly) oder *Pages/_Host.cshtml*-Datei (Blazor Server) mit dem im `href`-Attribut (`<base href="/">`) angegebenen App-Basispfad enthalten. Weitere Informationen finden Sie unter <xref:host-and-deploy/blazor/index#app-base-path>.

## <a name="provide-custom-content-when-content-isnt-found"></a>Bereitstellen von benutzerdefiniertem Inhalt, wenn kein Inhalt gefunden wurde

Die `Router`-Komponente ermöglicht es der App, benutzerdefinierte Inhalte anzugeben, wenn für die angeforderte Route keine Inhalte gefunden werden.

Legen Sie in der Datei *App.razor* den benutzerdefinierten Inhalt im `NotFound`-Vorlagenparameter der `Router`-Komponente fest:

```razor
<Router AppAssembly="typeof(Startup).Assembly">
    <Found Context="routeData">
        <RouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <h1>Sorry</h1>
        <p>Sorry, there's nothing at this address.</p> b
    </NotFound>
</Router>
```

Der Inhalt von `<NotFound>`-Tags kann beliebige Elemente beinhalten, z. B. andere interaktive Komponenten. Informationen zum Anwenden eines Standardlayouts auf `NotFound`-Inhalte finden Sie unter <xref:blazor/layouts>.

## <a name="route-to-components-from-multiple-assemblies"></a>Weiterleiten an Komponenten aus mehreren Assemblys

Verwenden Sie den `AdditionalAssemblies`-Parameter, um zusätzliche Assemblys anzugeben, die die `Router`-Komponente bei der Suche nach für das Routing geeigneten Komponenten beachten soll. Angegebene Assemblys werden zusätzlich zur mit `AppAssembly` angegebenen Assembly berücksichtigt. Im folgenden Beispiel ist `Component1` eine für das Routing geeignete Komponente, die in einer referenzierten Klassenbibliothek definiert ist. Im folgenden Beispiel zu `AdditionalAssemblies` wird die Routingunterstützung für `Component1` ermöglicht:

```razor
<Router
    AppAssembly="typeof(Program).Assembly"
    AdditionalAssemblies="new[] { typeof(Component1).Assembly }">
    ...
</Router>
```

## <a name="route-parameters"></a>Routenparameter

Der Router verwendet Routenparameter, um die entsprechenden Komponentenparameter mit demselben Namen (mit Beachtung der Groß-/Kleinschreibung) aufzufüllen:

```razor
@page "/RouteParameter"
@page "/RouteParameter/{text}"

<h1>Blazor is @Text!</h1>

@code {
    [Parameter]
    public string Text { get; set; }

    protected override void OnInitialized()
    {
        Text = Text ?? "fantastic";
    }
}
```

Optionale Parameter werden nicht unterstützt. Im vorherigen Beispiel werden zwei `@page`-Anweisungen angewendet. Die erste ermöglicht die Navigation zur Komponente ohne einen Parameter. Die zweite `@page`-Anweisung empfängt den `{text}`-Routenparameter und weist den Wert der `Text`-Eigenschaft zu.

## <a name="route-constraints"></a>Routeneinschränkungen

Eine Routeneinschränkung erzwingt die Typübereinstimmung in einem Routensegment zu einer Komponente.

Im folgenden Beispiel stimmt die Route zur `Users`-Komponente nur überein, wenn:

* Ein `Id`-Routensegment in der Anforderungs-URL vorhanden ist.
* Das `Id`-Segment eine Ganzzahl (`int`) ist.

[!code-razor[](routing/samples_snapshot/3.x/Constraint.razor?highlight=1)]

Die in der folgenden Tabelle aufgeführten Routeneinschränkungen sind verfügbar. Informationen zu den Routeneinschränkungen, die mit der invarianten Kultur abgeglichen werden, finden Sie in der Warnung unterhalb der Tabelle.

| Constraint | Beispiel           | Beispiele für Übereinstimmungen                                                                  | Invariante<br>Kultur<br>Übereinstimmend |
| ---
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

----- | --- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

--------- | --- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---------------------------------------- | :--- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---------------: | | `bool`     | `{active:bool}`   | `true`, `FALSE`                                                                  | Nein                               | | `datetime` | `{dob:datetime}`  | `2016-12-31`, `2016-12-31 7:32pm`                                                | Ja                              | | `decimal`  | `{price:decimal}` | `49.99`, `-1,000.01`                                                             | Ja                              | | `double`   | `{weight:double}` | `1.234`, `-1,001.01e8`                                                           | Ja                              | | `float`    | `{weight:float}`  | `1.234`, `-1,001.01e8`                                                           | Ja                              | | `guid`     | `{id:guid}`       | `CD2C1638-1638-72D5-1638-DEADBEEF1638`, `{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | Nein                               | | `int`      | `{id:int}`        | `123456789`, `-123456789`                                                        | Ja                              | | `long`     | `{ticks:long}`    | `123456789`, `-123456789`                                                        | Ja                              |

> [!WARNING]
> Für Routeneinschränkungen, mit denen die URL überprüft wird und die in den CLR-Typ umgewandelt werden (beispielsweise `int` oder `DateTime`), wird immer die invariante Kultur verwendet. Diese Einschränkungen setzen voraus, dass die URL nicht lokalisierbar ist.

### <a name="routing-with-urls-that-contain-dots"></a>Routing mit URLs, die Punkte enthalten

In Blazor Server-Apps ist die Standardroute in *_Host.cshtml* `/` (`@page "/"`). Eine Anforderungs-URL, die einen Punkt (`.`) enthält, wird nicht mit der Standardroute abgeglichen, da die URL zum Anfordern einer Datei angezeigt wird. Eine Blazor-App gibt eine Antwort *404 – Nicht gefunden* für eine statische Datei zurück, die nicht vorhanden ist. Um Routen zu verwenden, die einen Punkt enthalten, konfigurieren Sie *_Host.cshtml* mit der folgenden Routenvorlage:

```cshtml
@page "/{**path}"
```

Die `"/{**path}"`-Vorlage enthält Folgendes:

* *catch-all*-Syntax mit doppelten Sternchen (`**`) zur Erfassung des Pfades über mehrere Ordnergrenzen hinweg ohne Codierung von Schrägstrichen (`/`).
* Name des `path`-Routenparameters.

> [!NOTE]
> Die *catch-all*-Parametersyntax (`*`/`**`) wird **nicht** in Razor-Komponenten ( *.razor*) unterstützt.

Weitere Informationen finden Sie unter <xref:fundamentals/routing>.

## <a name="navlink-component"></a>NavLink-Komponente

Verwenden Sie bei der Erstellung von Navigationslinks eine `NavLink`-Komponente anstelle von HTML-Hyperlinkelementen (`<a>`). Eine `NavLink`-Komponente verhält sich wie ein `<a>`-Element, abgesehen davon, dass sie eine `active`-CSS-Klasse umschaltet, je nachdem, ob das `href`-Element mit der aktuellen URL übereinstimmt. Die `active`-Klasse zeigt einem Benutzer auf, welche Seite unter den angezeigten Navigationslinks aktiv ist.

Die folgende `NavMenu`-Komponente erstellt eine [Bootstrap](https://getbootstrap.com/docs/)-Navigationsleiste, die zeigt, wie `NavLink`-Komponenten verwendet werden:

[!code-razor[](routing/samples_snapshot/3.x/NavMenu.razor?highlight=4,9)]

Es gibt zwei `NavLinkMatch`-Optionen, die Sie dem `Match`-Attribut des `<NavLink>`-Elements zuweisen können:

* `NavLinkMatch.All` &ndash; `NavLink` ist aktiv, wenn die gesamte aktuelle URL übereinstimmt.
* `NavLinkMatch.Prefix` (*Standard*) &ndash; `NavLink` ist aktiv, wenn ein Präfix der aktuellen URL übereinstimmt.

Im vorherigen Beispiel stimmt die Startseite `NavLink` `href=""` mit der Startseiten-URL überein und empfängt nur die CSS-Klasse `active` in der Standardbasispfad-URL der App (z. B. `https://localhost:5001/`). Die zweite `NavLink`-Komponente empfängt die `active`-Klasse, wenn der Benutzer eine beliebige URL mit einem `MyComponent`-Präfix aufruft (z. B. `https://localhost:5001/MyComponent` und `https://localhost:5001/MyComponent/AnotherSegment`).

Zusätzliche `NavLink`-Komponentenattribute werden an das gerenderte Ankertag weitergegeben. Im folgenden Beispiel schließt die `NavLink`-Komponente das `target`-Attribut ein:

```razor
<NavLink href="my-page" target="_blank">My page</NavLink>
```

Das folgende HTML-Markup wird gerendert:

```html
<a href="my-page" target="_blank" rel="noopener noreferrer">My page</a>
```

## <a name="uri-and-navigation-state-helpers"></a>Hilfsprogramme für URI und Navigationszustand

Verwenden Sie <xref:Microsoft.AspNetCore.Components.NavigationManager>, um mit URIs und Navigationselementen in C#-Code zu arbeiten. `NavigationManager` stellt das Ereignis und die Methoden bereit, die in der folgenden Tabelle aufgeführt sind.

| Member | Beschreibung |
| ---
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

--- | --- title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title: 'ASP.NET Core Blazor-Routing' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------ | | Uri | Ruft den aktuellen absoluten URI ab. | | BaseUri | Ruft den Basis-URI (mit einem nachgestellten Schrägstrich) ab, der relativen URI-Pfaden vorangestellt werden kann, um einen absoluten URI zu erhalten. In der Regel entspricht `BaseUri` dem `href`-Attribut im `<base>`-Element des Dokuments in *wwwroot/index.html* (Blazor WebAssembly) oder *Pages/_Host.cshtml* (Blazor Server). | | NavigateTo | Navigiert zum angegebenen URI. Bei `forceLoad` lautet der Wert `true`:<ul><li>Clientseitiges Routing wird umgangen.</li><li>Der Browser ist gezwungen, die neue Seite vom Server zu laden, unabhängig davon, ob der URI normalerweise vom clientseitigen Router verarbeitet wird oder nicht.</li></ul> | | LocationChanged | Ein Ereignis, das ausgelöst wird, wenn sich die Navigationsposition geändert hat. | | ToAbsoluteUri | Konvertiert einen relativen URI in einen absoluten URI. | | <span style="word-break:normal;word-wrap:normal">ToBaseRelativePath</span> | Wenn ein Basis-URI (z. B. ein URI, der zuvor von `GetBaseUri` zurückgegeben wurde) vorhanden ist, wird ein absoluter URI in einen URI relativ zum Basis-URI-Präfix konvertiert. |

Die folgende Komponente navigiert zu der `Counter`-Komponente der App, wenn die Schaltfläche ausgewählt wird:

```razor
@page "/navigate"
@inject NavigationManager NavigationManager

<h1>Navigate in Code Example</h1>

<button class="btn btn-primary" @onclick="NavigateToCounterComponent">
    Navigate to the Counter component
</button>

@code {
    private void NavigateToCounterComponent()
    {
        NavigationManager.NavigateTo("counter");
    }
}
```

Die folgende Komponente verarbeitet ein Speicherortwechselereignis. Die Einbindung der `HandleLocationChanged`-Methode wird aufgehoben, wenn `Dispose` vom Framework aufgerufen wird. Durch das Aufheben der Einbindung der Methode wird die Garbage Collection für die Komponente ermöglicht.

```razor
@implements IDisposable
@inject NavigationManager NavigationManager

...

protected override void OnInitialized()
{
    NavigationManager.LocationChanged += HandleLocationChanged;
}

private void HandleLocationChanged(object sender, LocationChangedEventArgs e)
{
    ...
}

public void Dispose()
{
    NavigationManager.LocationChanged -= HandleLocationChanged;
}
```

<xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs> stellt die folgenden Informationen zum Ereignis bereit:

* <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.Location> entspricht der URL des neuen Speicherorts.
* Wenn <xref:Microsoft.AspNetCore.Components.Routing.LocationChangedEventArgs.IsNavigationIntercepted> den Wert `true` aufweist, wird die Navigation des Browsers von Blazor abgefangen. Wenn der Wert `false` vorliegt, wurde die Navigation von [NavigationManager.NavigateTo](xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A) ausgelöst.

Weitere Informationen zur Beseitigung von Komponenten finden Sie unter <xref:blazor/lifecycle#component-disposal-with-idisposable>.
