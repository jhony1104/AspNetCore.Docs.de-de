---
title: ASP.NET Core-Middleware
author: rick-anderson
description: Erfahren Sie mehr über ASP.NET Core-Middleware und die Anforderungspipeline.
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: bac121441d6856ca79affe1a3130e5cbc76debd9
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64882335"
---
# <a name="aspnet-core-middleware"></a>ASP.NET Core-Middleware

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://ardalis.com/)

Middleware ist Software, die zu einer Anwendungspipeline zusammengesetzt wird, um Anforderungen und Antworten zu verarbeiten. Jede Komponente kann Folgendes ausführen:

* Entscheiden, ob die Anforderung an die nächste Komponente in der Pipeline übergeben werden soll.
* Ausführen von Arbeiten, bevor oder nachdem die nächste Komponente in der Pipeline aufgerufen wird.

Anforderungsdelegaten werden verwendet, um die Anforderungspipeline zu erstellen. Die Anforderungsdelegaten behandeln jede HTTP-Anforderung.

Anforderungsdelegaten werden mit den Erweiterungsmethoden <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> und <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> konfiguriert. Ein einzelner Anforderungsdelegat kann inline als anonyme Methode angegeben werden (sogenannte Inline-Middleware), oder er kann in einer wiederverwendbaren Klasse definiert werden. Diese wiederverwendbaren Klassen und anonymen Inline-Methoden sind *Middleware* bzw. *Middlewarekomponenten*. Jede Middlewarekomponente in der Anforderungspipeline ist für das Aufrufen der jeweils nächsten Komponente in der Pipeline oder, wenn nötig, für das Kurzschließen der Pipeline zuständig. Wenn eine Middleware einen Kurzschluss verursacht, wird diese als *Terminalmiddleware* bezeichnet, da sie verhindert, dass weitere Middleware die Anforderung verarbeiten kann.

Unter <xref:migration/http-modules> wird der Unterschied zwischen Anforderungspipelines in ASP.NET Core und ASP.NET 4.x erklärt. Außerdem werden dort weitere Beispiele für Middleware gegeben.

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a>Erstellen einer Middlewarepipeline mit IApplicationBuilder

Die ASP.NET Core-Anforderungspipeline besteht aus einer Sequenz von Anforderungsdelegaten, die nacheinander aufgerufen werden. Das Konzept wird im folgenden Diagramm veranschaulicht. Der Ausführungsthread folgt den schwarzen Pfeilen.

![Anforderungsverarbeitungsmuster mit eingehender Anforderung, deren Verarbeitung von drei Middlewares und die ausgehende Antwort der Anwendung. Jede Middleware führt ihre Logik aus und übergibt die Anforderung an der next()-Anweisung an die nächste Middleware. Nachdem die Anforderung von der dritten Middleware verarbeitet wurde, wird sie wieder an die vorherigen Middlewares in umgekehrter Reihenfolge übergeben, nachdem die next()-Anweisungen erreicht wurden. Dann verlässt sie die Anwendung als Antwort an den Client.](index/_static/request-delegate-pipeline.png)

Jeder Delegat kann Vorgänge vor und nach dem nächsten Delegaten ausführen. Die Ausnahmebehandlungsdelegaten müssen am Anfang der Pipeline aufgerufen werden, sodass sie Ausnahmen abfangen können, die zu einem späteren Zeitpunkt in der Pipeline ausgelöst werden.

Die einfachste mögliche ASP.NET Core-App enthält einen einzigen Anforderungsdelegaten, der alle Anforderungen verarbeitet. In diesem Fall ist keine tatsächliche Anforderungspipeline vorhanden. Stattdessen wird eine einzelne anonyme Funktion als Antwort auf jede HTTP-Anforderung aufgerufen.

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

Der erste <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>-Delegat beendet die Pipeline.

Mit <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> können Sie mehrere Anforderungedelegate miteinander verknüpfen. Der Parameter `next` steht für den nächsten Delegaten in der Pipeline. Sie können die Pipeline kurzschließen, indem Sie den Parameter *next* *nicht* aufrufen. Normalerweise können Sie Aktionen sowohl vor als auch nach dem nächsten Delegaten durchführen. Dies wird in folgendem Beispiel veranschaulicht:

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

Wenn ein keine Anforderung an den nächsten Delegaten übergibt, wird dies als *Kurzschluss der Anforderungspipeline* bezeichnet. Das Kurzschließen ist oft sinnvoll, da es unnötige Arbeit verhindert. Die [Middleware für statische Dateien](xref:fundamentals/static-files) kann beispielsweise als *Terminalmiddleware* fungieren, indem sie eine Anforderung für eine statische Datei zurückgibt und den Rest der Pipeline kurzschließt. Middleware, die noch vor der Middleware, die die weitere Verarbeitung beendet, zur Pipeline hinzugefügt wird, verarbeitet Code noch nach den `next.Invoke`-Anweisungen weiter. Sehen Sie sich allerdings die folgende Warnung zum Versuch an, in eine Antwort zu schreiben, die bereits gesendet wurde.

> [!WARNING]
> Rufen Sie `next.Invoke` nicht auf, nachdem die Antwort an den Client gesendet wurde. An <xref:Microsoft.AspNetCore.Http.HttpResponse> vorgenommene Änderungen lösen nach dem Start der Antwort eine Ausnahme aus. Änderungen wie das Festlegen von Headern und einem Statuscode lösen beispielsweise eine Ausnahme aus. Wenn Sie nach dem Aufruf von `next` in den Antworttext schreiben, kann dies:
>
> * einen Protokollverstoß verursachen, wenn Sie z.B. mehr als das genannte `Content-Length`-Objekt schreiben.
> * Fehler im Textformat auslösen, wenn Sie z.B. eine HTML-Fußzeile in eine CSS-Datei schreiben.
>
> <xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> ist ein nützlicher Hinweis, der angibt, ob Header gesendet wurden oder ob in den Text geschrieben wurde.

## <a name="order"></a>Auftrag

Die Reihenfolge, in der Middlewarekomponenten in der `Startup.Configure`-Methode hinzugefügt werden, legt die Reihenfolge fest, in der die Middlewarekomponenten bei Anforderungen aufgerufen werden. Bei Antworten gilt die umgekehrte Reihenfolge. Die Reihenfolge trägt wesentlich zur Sicherheit, Leistung und Funktionalität bei.

Die folgenden `Startup.Configure`-Methode fügt Middlewarekomponenten für allgemeine App-Szenarien hinzu:

::: moniker range=">= aspnetcore-2.0"

1. Ausnahme-/Fehlerbehandlung
1. HTTP Strict Transport Security Protocol
1. HTTPS-Umleitung
1. Statischer Dateiserver
1. Cookierichtliniendurchsetzung
1. Authentifizierung
1. Sitzung
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. Ausnahme-/Fehlerbehandlung
1. Statische Dateien
1. Authentifizierung
1. Sitzung
1. MVC

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

Im vorhergehenden Beispielcode wird jede Middleware-Erweiterungsmethode in <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> über den <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName>-Namespace verfügbar gemacht.

<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> ist die erste Middlewarekomponente, die der Pipeline hinzugefügt wird. Aus diesem Grund fängt die Middleware für den Ausnahmehandler alle Ausnahmen ab, die in späteren Aufrufen auftreten.

Die Middleware für statische Dateien wird am Anfang der Pipeline aufgerufen, damit sie Anforderungen und Kurzschlüsse verarbeiten kann, ohne dass die verbleibenden Komponenten durchlaufen werden müssen. Die Middleware für statische Dateien stellt **keine** Autorisierungsüberprüfungen bereit. Alle Dateien, die von ihr bearbeitet werden, Dateien unter *wwwroot* inbegriffen, sind öffentlich verfügbar. Informationen zum Sichern statischer Dateien finden Sie unter <xref:fundamentals/static-files>.

::: moniker range=">= aspnetcore-2.0"

Wenn die Anforderung nicht von der Middleware für statische Dateien verarbeitet wird, wird sie an die Authentifizierungsmiddleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) übergeben, welche die Authentifizierung durchführt. Die Authentifizierung schließt nicht authentifizierte Anforderungen nicht kurz. Auch wenn die Authentifizierungsmiddleware Anforderungen authentifiziert, erfolgt die Autorisierung (und Ablehnung) erst dann, wenn MVC eine spezifische Razor Page oder einen MVC-Controller und eine Aktion ausgewählt hat.

::: moniker-end

::: moniker range="< aspnetcore-2.0"

Wenn die Anforderung nicht von der Middleware für statische Dateien verarbeitet wird, wird sie an die Identity-Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>) übergeben, welche die Authentifizierung durchführt. Identity schließt keine unautorisierten Anforderungen kurz. Auch wenn Identity Anforderungen authentifiziert, erfolgt die Autorisierung (und Ablehnung) erst dann, wenn MVC einen spezifischen Controller und eine Aktion ausgewählt hat.

::: moniker-end

Im folgenden Beispiel wird eine Middlewarereihenfolge veranschaulicht, bei der Anforderungen statischer Dateien von der Middleware für statische Dateien vor der Middleware für die Antwortkomprimierung verarbeitet werden. Die statischen Dateien werden bei dieser Middlewarereihenfolge nicht komprimiert. Die MVC-Antworten von <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> können komprimiert werden.

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a>Use, Run und Map

Konfigurieren Sie die HTTP-Pipeline mit `Use`, `Run` und `Map`. Die `Use`-Methode kann die Pipeline kurzschließen (wenn sie keinen `next`-Anforderungsdelegaten aufruft). `Run` ist eine Konvention. Einige Middlewarekomponenten machen möglicherweise `Run[Middleware]`-Methoden verfügbar, die am Ende einer Pipeline ausgeführt werden.

<xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>-Erweiterungen werden als Konvention zum Branchen der Pipeline verwendet. `Map*` brancht die Anforderungspipeline auf Grundlage von Übereinstimmungen des angegebenen Anforderungspfads. Wenn der Anforderungspfad mit dem angegebenen Pfad beginnt, wird der Branch ausgeführt.

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet.

| Anforderung             | Antwort                     |
| ------------------- | ---------------------------- |
| localhost:1234      | Hello from non-Map delegate. |
| localhost:1234/map1 | Map Test 1                   |
| localhost:1234/map2 | Map Test 2                   |
| localhost:1234/map3 | Hello from non-Map delegate. |

Wenn `Map` verwendet wird, werden die übereinstimmenden Pfadsegmente bzw. das übereinstimmende Pfadsegment aus `HttpRequest.Path` entfernt und für jede Anforderung an `HttpRequest.PathBase` angehängt.

[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) brancht die Anforderungspipeline auf Grundlage des Ergebnisses des angegebenen Prädikats. Jedes Prädikat vom Typ `Func<HttpContext, bool>` kann verwendet werden, um Anforderungen einem neuen Branch der Pipeline zuzuordnen. Im folgenden Beispiel wird ein Prädikat verwendet, um das Vorhandensein der Abfragezeichenfolgenvariablen `branch` zu ermitteln:

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

In der folgenden Tabelle sind die Anforderungen und Antworten von `http://localhost:1234` mit dem oben stehenden Code aufgelistet.

| Anforderung                       | Antwort                     |
| ----------------------------- | ---------------------------- |
| localhost:1234                | Hello from non-Map delegate. |
| localhost:1234/?branch=master | Branch used = master         |

`Map` unterstützt das Schachteln, wie z.B. in folgendem Code:

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
   ```

`Map` kann auch mehrere Segmente auf einmal zuordnen:

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a>Integrierte Middleware

Die folgenden Middlewarekomponenten sind im Lieferumfang von ASP.NET Core enthalten. Die Spalte *Reihenfolge* enthält Hinweise zur Platzierung der Middleware in der Pipeline, die die Anforderung verarbeitet, und zu den Bedingungen, unter denen die Middleware die Anforderungsverarbeitung möglicherweise beendet. Wenn eine Middleware einen Kurzschluss in der Anforderungsverarbeitungspipeline verursacht und verhindert, dass Downstreammiddleware eine Anforderung verarbeitet, wird diese als *Terminalmiddleware* bezeichnet. Weitere Informationen zu Kurzschlüssen finden Sie im Abschnitt [Erstellen einer Middlewarepipeline mit IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder).

| Middleware | Beschreibung | Auftrag |
| ---------- | ----------- | ----- |
| [Authentifizierung](xref:security/authentication/identity) | Bietet Unterstützung für Authentifizierungen. | Bevor `HttpContext.User` erforderlich ist. Terminal für OAuth-Rückrufe. |
| [Cookierichtlinie](xref:security/gdpr) | Verfolgt die Zustimmung von Benutzern zum Speichern persönlicher Informationen nach und erzwingt die Mindeststandards für Cookiefelder, z.B. `secure` und `SameSite`. | Befindet sich vor der Middleware, die Cookies ausstellt. Beispiele: Authentifizierung, Sitzung, MVC (TempData). |
| [CORS](xref:security/cors) | Konfiguriert die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS). | Vor Komponenten, die CORS verwenden. |
| [Ausnahmebehandlung](xref:fundamentals/error-handling) | Behandelt Ausnahmen. | Vor Komponenten, die Fehler erzeugen. |
| [Weitergeleitete Header](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | Leitet Proxyheader an die aktuelle Anforderung weiter. | Vor Komponenten, die die aktualisierten Felder nutzen. Beispiele: Schema, Host, Client-IP, Methode. |
| [Integritätsprüfung](xref:host-and-deploy/health-checks) | Überprüft die Integrität der ASP.NET Core-App und ihrer Abhängigkeiten, z. B. Überprüfung der Datenbankverfügbarkeit. | Abschließend, wenn eine Anforderung mit einem Integritätsprüfungs-Endpunkt übereinstimmt. |
| [Außerkraftsetzung der HTTP-Methode](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | Ermöglicht es eingehenden POST-Anforderungen, die Methode außer Kraft zu setzen. | Vor Komponenten, die die aktualisierte Methode nutzen. |
| [HTTPS-Umleitung](xref:security/enforcing-ssl#require-https) | Leitet alle HTTP-Anforderungen an HTTPS um (ASP.NET Core 2.1 oder höher). | Vor Komponenten, die die URL nutzen. |
| [HTTP Strict Transport Security (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | Sicherheits-Middleware, die einen besonderen Antwortheader hinzufügt (ASP.NET Core 2.1 oder höher). | Bevor Antworten gesendet werden und nach Komponenten, die Anforderungen ändern. Beispiele: weitergeleitete Header, URL-Umschreibung. |
| [MVC](xref:mvc/overview) | Verarbeitet Anforderungen mit MVC/Razor Pages (ASP.NET Core 2.0 oder höher). | Abschließend, wenn eine Anforderung mit einer Route übereinstimmt. |
| [OWIN](xref:fundamentals/owin) | Interoperabilität mit auf OWIN basierten Apps, Servern und Middleware. | Abschließend, wenn die OWIN-Middleware die Anforderung vollständig verarbeitet. |
| [Zwischenspeichern von Antworten](xref:performance/caching/middleware) | Bietet Unterstützung für das Zwischenspeichern von Antworten. | Vor Komponenten, für die das Zwischenspeichern erforderlich ist. |
| [Antwortkomprimierung](xref:performance/response-compression) | Bietet Unterstützung für das Komprimieren von Antworten. | Vor Komponenten, für die das Komprimieren erforderlich ist. |
| [Lokalisierung von Anforderungen](xref:fundamentals/localization) | Bietet Unterstützung für die Lokalisierung. | Vor der Lokalisierung vertraulicher Komponenten. |
| [Routing](xref:fundamentals/routing) | Definiert Anforderungsrouten und schränkt diese ein. | Terminal für entsprechende Routen. |
| [Sitzung](xref:fundamentals/app-state) | Bietet Unterstützung für das Verwalten von Benutzersitzungen. | Vor Komponenten, für die Sitzungen erforderlich sind. |
| [Statische Dateien](xref:fundamentals/static-files) | Bietet Unterstützung für das Verarbeiten statischer Dateien und das Durchsuchen des Verzeichnisses. | Abschließend, wenn eine Anforderung mit einer Datei übereinstimmt. |
| [URL-Umschreibung](xref:fundamentals/url-rewriting) | Bietet Unterstützung für das Umschreiben von URLs und das Umleiten von Anforderungen. | Vor Komponenten, die die URL nutzen. |
| [WebSockets](xref:fundamentals/websockets) | Aktiviert das WebSockets-Protokoll. | Vor Komponenten, die WebSocket-Anforderungen annehmen müssen. |

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
