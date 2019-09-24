---
title: Behandeln von Fehlern in ASP.net Core blazor-apps
author: guardrex
description: Erfahren Sie, wie ASP.net Core blazor, wie von blazor nicht behandelte Ausnahmen verwaltet werden, und wie Sie apps entwickeln, die Fehler erkennen und behandeln.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/handle-errors
ms.openlocfilehash: de0a2f74df84f41581ac93dbeec7a5c5e90c6fa2
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71207193"
---
# <a name="handle-errors-in-aspnet-core-blazor-apps"></a>Behandeln von Fehlern in ASP.net Core blazor-apps

Von [Steve Sanderson](https://github.com/SteveSandersonMS)

In diesem Artikel wird beschrieben, wie blazor nicht behandelte Ausnahmen verwaltet und wie Sie apps entwickeln, die Fehler erkennen und behandeln.

## <a name="how-the-blazor-framework-reacts-to-unhandled-exceptions"></a>Reagieren des blazor-Frameworks auf nicht behandelte Ausnahmen

Der blazor-Server ist ein Zustands behaftetes Framework. Während Benutzer mit einer APP interagieren, behalten *Sie eine Verbindung*mit dem Server bei, der als Verbindung bezeichnet wird. Die Verbindung enthält aktive Komponenten Instanzen sowie viele andere Aspekte des Zustands, wie z. b.:

* Die letzte gerenderte Ausgabe von-Komponenten.
* Die aktuelle Gruppe von Delegaten zur Ereignis Behandlung, die von Client seitigen Ereignissen ausgelöst werden könnten.

Wenn ein Benutzer die APP auf mehreren Browser Registerkarten öffnet, sind mehrere unabhängige Verbindungen vorhanden.

Blazor behandelt die meisten nicht behandelten Ausnahmen als schwerwiegend an der Verbindung, in der Sie auftreten. Wenn eine Verbindung aufgrund einer nicht behandelten Ausnahme beendet wird, kann der Benutzer die Interaktion mit der app nur fortsetzen, indem er die Seite erneut lädt, um eine neue Verbindung zu erstellen. Verbindungen außerhalb der Beendigungs Seite, bei der es sich um Verbindungen für andere Benutzer oder andere Browser Registerkarten handelt, sind nicht betroffen. Dieses Szenario ähnelt einer Desktop-App, die abstürzt&mdash;, dass die abstürzte APP neu gestartet werden muss, aber andere apps sind nicht betroffen.

Eine Verbindung wird beendet, wenn eine nicht behandelte Ausnahme aus den folgenden Gründen auftritt:

* Eine nicht behandelte Ausnahme verlässt die Verbindung häufig in einem nicht definierten Zustand.
* Der normale Vorgang der APP kann nach einer nicht behandelten Ausnahme nicht garantiert werden.
* Sicherheitsrisiken werden möglicherweise in der App angezeigt, wenn die Verbindung fortgesetzt wird.

## <a name="manage-unhandled-exceptions-in-developer-code"></a>Verwalten von nicht behandelten Ausnahmen in Entwickler Code

Damit eine APP nach einem Fehler fortgesetzt werden kann, muss die APP über eine Fehler Behandlungs Logik verfügen. In späteren Abschnitten dieses Artikels werden mögliche Quellen für nicht behandelte Ausnahmen beschrieben.

In der Produktionsumgebung müssen Sie keine Framework-Ausnahme Meldungen oder Stapel Überwachungen in der Benutzeroberfläche erstellen. Das Rendern von Ausnahme Meldungen oder Stapel Überwachungen könnte:

* Geben Sie den Endbenutzern vertrauliche Informationen offen.
* Helfen Sie einem böswilligen Benutzer dabei, Schwachstellen in einer APP zu erkennen, die die Sicherheit der APP, des Servers oder des Netzwerks beeinträchtigen können.

## <a name="log-errors-with-a-persistent-provider"></a>Protokollieren von Fehlern mit einem permanenten Anbieter

Wenn eine nicht behandelte Ausnahme auftritt, wird die Ausnahme an <xref:Microsoft.Extensions.Logging.ILogger> Instanzen protokolliert, die im Dienst Container konfiguriert sind. Standardmäßig melden sich blazor-apps mit dem Konsolen Protokollierungs Anbieter an der Konsolenausgabe an. Melden Sie sich bei einem Anbieter, der die Protokoll Größe und die Protokoll Rotation verwaltet, an einem permanenten Speicherort an. Weitere Informationen finden Sie unter <xref:fundamentals/logging/index>.

Während der Entwicklung sendet blazor in der Regel die vollständigen Details der Ausnahmen an die Konsole des Browsers, um das Debuggen zu unterstützen. In der Produktionsumgebung sind ausführliche Fehler in der Browser Konsole standardmäßig deaktiviert. Dies bedeutet, dass Fehler nicht an Clients gesendet werden, sondern dass die vollständigen Details der Ausnahme weiterhin serverseitig protokolliert werden. Weitere Informationen finden Sie unter <xref:fundamentals/error-handling>.

Sie müssen entscheiden, welche Vorfälle protokolliert werden sollen, und den Schweregrad protokollierter Vorfälle. Feindliche Benutzer können möglicherweise Fehler absichtlich auslöst. Protokollieren Sie z. b. keinen Incident von einem Fehler, `ProductId` bei dem ein unbekannter in der URL einer Komponente angegeben ist, in der Produktdetails angezeigt werden. Nicht alle Fehler sollten als Vorfälle mit hohem Schweregrad für die Protokollierung behandelt werden.

## <a name="places-where-errors-may-occur"></a>Orte, an denen Fehler auftreten können

Framework-und app-Code können nicht behandelte Ausnahmen an einem der folgenden Speicherorte auslöst:

* [Komponenten Instanziierung](#component-instantiation)
* [Lebenszyklus Methoden](#lifecycle-methods)
* [Renderinglogik](#rendering-logic)
* [Ereignishandler](#event-handlers)
* [Komponenten Beseitigung](#component-disposal)
* [JavaScript-Interop](#javascript-interop)
* [Verbindungs Handler](#circuit-handlers)
* [Freigabe Entfernung](#circuit-disposal)
* [Wird vorab durchgeführt](#prerendering)

Die vorangegangenen nicht behandelten Ausnahmen werden in den folgenden Abschnitten dieses Artikels beschrieben.

### <a name="component-instantiation"></a>Komponenten Instanziierung

Wenn blazor eine Instanz einer Komponente erstellt:

* Der Konstruktor der Komponente wird aufgerufen.
* Die Konstruktoren aller nicht-Singleton-di-Dienste, die über die [@inject](xref:blazor/dependency-injection#request-a-service-in-a-component) -Direktive oder das [[einschleusen]](xref:blazor/dependency-injection#request-a-service-in-a-component) -Attribut für den Konstruktor der Komponente bereitgestellt werden, werden aufgerufen. 

Eine Verbindung kann nicht hergestellt werden, wenn ein ausgeführter Konstruktor oder ein Setter für `[Inject]` eine Eigenschaft eine nicht behandelte Ausnahme auslöst. Die Ausnahme ist schwerwiegend, da das Framework die Komponente nicht instanziieren kann. Wenn die Konstruktorlogik Ausnahmen auslösen kann, sollte die APP die Ausnahmen mithilfe einer [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Anweisung mit Fehlerbehandlung und Protokollierung abfangen.

### <a name="lifecycle-methods"></a>Lebenszyklusmethoden

Während der Lebensdauer einer Komponente ruft blazor Lebenszyklus Methoden auf:

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

Wenn eine Lebenszyklus Methode eine Ausnahme synchron oder asynchron auslöst, ist die Ausnahme für die Verbindung schwerwiegend. Fügen Sie Fehler Behandlungs Logik hinzu, damit Komponenten Fehler in Lebenszyklus Methoden behandeln können.

Im folgenden Beispiel, in `OnParametersSetAsync` dem eine Methode zum Abrufen eines Produkts aufruft:

* Eine Ausnahme, die in `ProductRepository.GetProductByIdAsync` der-Methode ausgelöst wird `try-catch` , wird von einer-Anweisung behandelt.
* Wenn der `catch` -Block ausgeführt wird:
  * `loadFailed`ist auf `true`festgelegt, das verwendet wird, um dem Benutzer eine Fehlermeldung anzuzeigen.
  * Der Fehler wird protokolliert.

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>Renderinglogik

Das deklarative Markup in einer `.razor` Komponenten Datei wird in eine C# Methode namens `BuildRenderTree`kompiliert. Wenn eine Komponente gerendert wird, wird `BuildRenderTree` eine Datenstruktur ausgeführt und erstellt, die die Elemente, den Text und die untergeordneten Komponenten der gerenderten Komponente beschreibt.

Die Renderinglogik kann eine Ausnahme auslösen. Ein Beispiel für dieses Szenario tritt auf `@someObject.PropertyName` , wenn ausgewertet `@someObject` wird `null`, aber ist. Eine nicht behandelte Ausnahme, die von der Renderinglogik ausgelöst wird, ist schwerwiegend auf die

Um eine NULL-Verweis Ausnahme in Renderinglogik zu verhindern `null` , überprüfen Sie vor dem Zugriff auf die Member auf ein-Objekt Im folgenden Beispiel wird nicht `person.Address` auf Eigenschaften zugegriffen, `person.Address` Wenn `null`Folgendes ist:

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

Der vorangehende Code geht `person` davon `null`aus, dass nicht ist. Häufig gewährleistet die Struktur des Codes, dass ein Objekt vorhanden ist, wenn die Komponente gerendert wird. In diesen Fällen ist es nicht erforderlich, `null` in Renderinglogik zu überprüfen. Im vorherigen Beispiel ist möglich `person` erweise vorhanden, da `person` erstellt wird, wenn die Komponente instanziiert wird.

### <a name="event-handlers"></a>Ereignishandler

Client seitiger Code löst Aufrufe von Code aus C# , wenn Ereignishandler mit erstellt werden:

* `@onclick`
* `@onchange`
* Andere `@on...` Attribute
* `@bind`

Der Ereignishandlercode löst in diesen Szenarien möglicherweise eine nicht behandelte Ausnahme aus.

Wenn ein Ereignishandler eine nicht behandelte Ausnahme auslöst (z. b. Wenn eine Datenbankabfrage fehlschlägt), ist die Ausnahme für die Verbindung schwerwiegend. Wenn die app Code aufruft, der aus externen Gründen fehlschlagen könnte, fangen Sie Ausnahmen mithilfe einer [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Anweisung mit Fehlerbehandlung und Protokollierung ab.

Wenn Benutzercode die Ausnahme nicht abfängt und behandelt, protokolliert das Framework die Ausnahme und beendet die Verbindung.

### <a name="component-disposal"></a>Komponenten Beseitigung

Eine Komponente kann z. b. aus der Benutzeroberfläche entfernt werden, weil der Benutzer zu einer anderen Seite navigiert ist. Wenn eine Komponente, die <xref:System.IDisposable?displayProperty=fullName> implementiert, von der Benutzeroberfläche entfernt wird, ruft das Framework <xref:System.IDisposable.Dispose*> die-Methode der Komponente auf. 

Wenn die- `Dispose` Methode der Komponente eine nicht behandelte Ausnahme auslöst, ist die Ausnahme für die Verbindung schwerwiegend. Wenn die Entsorgungs Logik Ausnahmen auslösen kann, sollte die APP die Ausnahmen mithilfe einer [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Anweisung mit Fehlerbehandlung und Protokollierung abfangen.

Weitere Informationen zur Komponenten Beseitigung finden <xref:blazor/components#component-disposal-with-idisposable>Sie unter.

### <a name="javascript-interop"></a>JavaScript-Interoperabilität

`IJSRuntime.InvokeAsync<T>`ermöglicht .NET-Code das Ausführen von asynchronen Aufrufen der JavaScript-Laufzeit im Browser des Benutzers.

Die folgenden Bedingungen gelten für die Fehlerbehandlung `InvokeAsync<T>`mit:

* Wenn ein Rückruf von `InvokeAsync<T>` synchron fehlschlägt, tritt eine .NET-Ausnahme auf. `InvokeAsync<T>` Ein Beispiel für einen Fehler tritt beispielsweise auf, weil die angegebenen Argumente nicht serialisiert werden können. Der Entwickler Code muss die Ausnahme abfangen. Wenn der app-Code in einem Ereignishandler oder einer Komponenten Lebenszyklus-Methode keine Ausnahme behandelt, ist die resultierende Ausnahme schwerwiegend auf die Verbindung.
* Wenn ein Rückruf von `InvokeAsync<T>` asynchron fehlschlägt, schlägt <xref:System.Threading.Tasks.Task> .net fehl. Ein-Vorgang `Promise` `rejected`kann z. b. fehlschlagen, weil der JavaScript-seitige Code eine Ausnahme auslöst oder einen zurückgibt, der als `InvokeAsync<T>` abgeschlossen wurde. Der Entwickler Code muss die Ausnahme abfangen. Wenn Sie den [await](/dotnet/csharp/language-reference/keywords/await)-Operator verwenden, umschließen Sie den Methodenaufruf am besten in einer [try-catch](/dotnet/csharp/language-reference/keywords/try-catch)-Anweisung mit der Fehlerbehandlung und der Protokollierung. Andernfalls führt der fehlgeschlagene Code zu einer nicht behandelten Ausnahme, die für die Verbindung schwerwiegend ist.
* Standardmäßig `InvokeAsync<T>` müssen Aufrufe von innerhalb eines bestimmten Zeitraums durchgeführt werden, oder für den Aufruf wird ein Timeout festgestellt. Der Standard Timeout Zeitraum beträgt eine Minute. Das Timeout schützt den Code vor einem Verlust in Netzwerk Konnektivität oder JavaScript-Code, der niemals eine Abschluss Nachricht zurücksendet. Wenn für den-Rückruf ein Timeout auftritt `Task` , schlägt die <xref:System.OperationCanceledException>resultierende mit einem fehl. Trap und verarbeitet die Ausnahme mit der Protokollierung.

Ebenso kann JavaScript-Code Aufrufe von .NET-Methoden initiieren, die durch das [[jsinvokable]-Attribut](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)angegeben werden. Wenn diese .NET-Methoden eine nicht behandelte Ausnahme auslösen:

* Die Ausnahme wird nicht als schwerwiegender Fehler für die Verbindung behandelt.
* Die JavaScript-Seite `Promise` wird zurückgewiesen.

Sie haben die Möglichkeit, Fehler Behandlungs Code entweder auf der .NET-Seite oder auf der JavaScript-Seite des Methoden Aufrufes zu verwenden.

Weitere Informationen finden Sie unter <xref:blazor/javascript-interop>.

### <a name="circuit-handlers"></a>Verbindungs Handler

Mit blazor kann Code einen Verbindungs *Handler*definieren, der Benachrichtigungen empfängt, wenn sich der Status der Verbindung eines Benutzers ändert. Die folgenden Zustände werden verwendet:

* `initialized`
* `connected`
* `disconnected`
* `disposed`

Benachrichtigungen werden durch Registrieren eines di-Dienstanbieter verwaltet, der `CircuitHandler` von der abstrakten Basisklasse erbt.

Wenn die Methoden eines benutzerdefinierten Verbindungs Handlers eine nicht behandelte Ausnahme auslösen, ist die Ausnahme für die Verbindung schwerwiegend. Um Ausnahmen im Code eines Handlers zu tolerieren oder Methoden aufzurufen, packen Sie den Code in einer oder mehreren [try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Anweisungen mit Fehlerbehandlung und Protokollierung.

### <a name="circuit-disposal"></a>Freigabe Entfernung

Wenn eine Verbindung beendet wird, weil ein Benutzer die Verbindung getrennt hat und das Framework den Verbindungsstatus bereinigt, gibt das Framework den di-Bereich der Verbindung frei. Durch die Freigabe des Bereichs werden alle di-Dienste, die von implementiert <xref:System.IDisposable?displayProperty=fullName>werden, freigegeben. Wenn ein di-Dienst während der Entfernung eine nicht behandelte Ausnahme auslöst, protokolliert das Framework die Ausnahme.

### <a name="prerendering"></a>Wird vorab durchgeführt

Blazor-Komponenten können mithilfe von vorab verwendet `Html.RenderComponentAsync` werden, damit das gerenderte HTML-Markup als Teil der ursprünglichen HTTP-Anforderung des Benutzers zurückgegeben wird. Dies funktioniert wie folgt:

* Erstellen einer neuen Verbindung mit allen vorab erstellten Komponenten, die Teil der gleichen Seite sind.
* Das anfängliche HTML wird erzeugt.
* Die Verbindung wird so `disconnected` lange behandelt, bis der Browser des Benutzers eine signalr-Verbindung zum gleichen Server herstellt, um die Interaktivität auf der Verbindung fortzusetzen.

Wenn eine Komponente während der vorab Generierung eine nicht behandelte Ausnahme auslöst, z. b. während einer Lebenszyklus Methode oder in Renderinglogik:

* Die Ausnahme ist für die Verbindung schwerwiegend.
* Die Ausnahme wird aus `Html.RenderComponentAsync` dem-Befehl in der aufrufsstapel ausgelöst. Daher schlägt die gesamte HTTP-Anforderung fehl, es sei denn, die Ausnahme wird explizit durch den Entwickler Code abgefangen.

Unter normalen Umständen ist es nicht sinnvoll, die Komponente zu erstellen und zu rendern, da eine funktionierende Komponente nicht gerendert werden kann.

Um Fehler zu tolerieren, die möglicherweise während der vorab Generierung auftreten, muss die Fehler Behandlungs Logik in einer Komponente platziert werden, die Ausnahmen auslösen kann. Verwenden [Sie try-catch-](/dotnet/csharp/language-reference/keywords/try-catch) Anweisungen mit Fehlerbehandlung und Protokollierung. Anstatt den-Befehl in einer `RenderComponentAsync` `try-catch` -Anweisung zu umwickeln, platzieren Sie die Fehler Behandlungs Logik in `RenderComponentAsync`der von gerenderten Komponente.

## <a name="advanced-scenarios"></a>Erweiterte Szenarien

### <a name="recursive-rendering"></a>Rekursives Rendering

Komponenten können rekursiv eingefügt werden. Dies ist nützlich, um rekursive Datenstrukturen darzustellen. Beispielsweise kann eine `TreeNode` Komponente weitere `TreeNode` Komponenten für die untergeordneten Knoten des Knotens Rendering.

Vermeiden Sie beim rekursiv Rendering das Codieren von Mustern, die zu einer unendlichen Rekursion führen:

* Renweder eine Datenstruktur, die einen Cycle enthält, rekursiv. Erstellen Sie beispielsweise keinen Struktur Knoten, dessen untergeordnete Elemente sich selbst enthalten.
* Erstellen Sie keine Kette von Layouts, die einen Kreis enthalten. Erstellen Sie z. b. kein Layout, dessen Layout sich selbst ist.
* Erlauben Sie einem Endbenutzer nicht, mithilfe von böswilligen Daten Einträgen oder JavaScript-Interop-aufrufen gegen revarianten der Rekursion (Regeln) zu verstoßen.

Unendliche Schleifen beim Rendern:

* Bewirkt, dass der Renderingprozess immer fortgesetzt wird
* Entspricht dem Erstellen einer nicht abgeschlossenen Schleife.

In diesen Szenarien wird die betroffene Verbindung nicht mehr angezeigt, und der Thread versucht normalerweise Folgendes:

* Beanspruchen Sie beliebig viele CPU-Zeit, die das Betriebssystem unbegrenzt zulässt.
* Nutzen Sie eine unbegrenzte Menge an Server Arbeitsspeicher. Die Verwendung von unbegrenztem Arbeitsspeicher entspricht dem Szenario, in dem eine nicht abgeschlossene Schleife einer Auflistung bei jeder Iterationen Einträge hinzufügt.

Um unendliche Rekursions Muster zu vermeiden, stellen Sie sicher, dass der rekursive Renderingcode geeignete Stopp Bedingungen

### <a name="custom-render-tree-logic"></a>Benutzerdefinierte renderstrukturlogik

Die meisten blazor-Komponenten werden als *Razor* -Dateien implementiert und kompiliert, um Logik zu erzeugen, `RenderTreeBuilder` die auf einem zum Renderingergebnis von arbeitet. Ein Entwickler kann Logik mithilfe `RenderTreeBuilder` von prozeduralem C# Code manuell implementieren. Weitere Informationen finden Sie unter <xref:blazor/components#manual-rendertreebuilder-logic>.

> [!WARNING]
> Die Verwendung der Logik des manuellen renderbaum-Generators wird als erweitertes und unsichere Szenario angesehen und wird für die allgemeine Komponentenentwicklung nicht empfohlen.

Wenn `RenderTreeBuilder` Code geschrieben wird, muss der Entwickler die Richtigkeit des Codes sicherstellen. Der Entwickler muss beispielsweise Folgendes sicherstellen:

* Aufrufe von `OpenElement` und `CloseElement` sind ordnungsgemäß ausgeglichen.
* Attribute werden nur an den richtigen Stellen hinzugefügt.

Falsche manuelle Renderstruktur-Generator-Logik kann willkürlich nicht definiertes Verhalten verursachen, z. b. Abstürze, Serverabstürze und Sicherheitsrisiken.

Beachten Sie die manuelle renderbaum-Generator-Logik auf der gleichen Ebene der Komplexität und mit dem gleichen Maß an *Gefahr* wie das Schreiben von Assemblycode oder MSIL-Anweisungen per Hand.
