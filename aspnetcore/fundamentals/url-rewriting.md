---
title: 'title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:'
author: rick-anderson
description: "'Blazor'"
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/url-rewriting
ms.openlocfilehash: e43cd5055737feaef451d27b651c1d301c1f93d2
ms.sourcegitcommit: 58722eb309767e462bdbf3082bd38737a4ef168f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84105947"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a>'Identity'

'Let's Encrypt'

::: moniker range=">= aspnetcore-3.0"

'Razor'

'SignalR' uid: URL-umschreibende Middleware in ASP.NET Core Von [Mikael Mengistu](https://github.com/mikaelm12)

* In diesem Artikel wird das Umschreiben von URLs beschrieben. Außerdem erhalten Sie Anweisungen zur Verwendung der URL-umschreibenden Middleware in ASP.NET Core-Apps.
* Bei der URL-Umschreibung werden die Anforderungs-URLs verändert, die auf mindesten einer vordefinierten Regel basieren.
* Es wird eine Abstraktion zwischen den Speicherorten und Adressen von Ressourcen erstellt, sodass diese nicht eng miteinander verknüpft sind.
* Die URL-Neuschreibung ist hilfreich in verschiedenen Szenarios wie:
* Kurzzeitiges oder permanentes Verschieben oder Ersetzen von Serverressourcen, sowie Erhalten von stabilen Locators für diese Ressourcen.
* Verteilen der Verarbeitung von Anforderungen auf verschiedene Apps oder Bereiche einer App.
* Entfernen, Hinzufügen oder Umorganisieren von URL-Segmenten bei eingehenden Anforderungen.

> [!NOTE]
> Optimieren von öffentlichen URLs für die Suchmaschinenoptimierung (Search Engine Optimization, SEO). Gestatten der Verwendung von angezeigten öffentlichen URLs, um Besuchern zu helfen, den Inhalt vorherzusagen, der durch die Anforderung einer Ressource zurückgegeben wird.

Umleiten von unsicheren Anforderungen auf sichere Endpunkte.

## <a name="url-redirect-and-url-rewrite"></a>Verhindern von Hotlinks zu externen Websites, die eine gehostete statische Ressource auf einer anderen Website verwenden, bei der die Ressource zu ihrem eigenen Inhalt verlinkt wird.

Wenn Sie URLs umschreiben, kann das negative Auswirkungen auf die Leistung einer App haben. Wenn möglich, sollten Sie so wenig Regeln wie möglich erstellen und darauf achten, dass diese nicht zu kompliziert sind.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample)) Umleiten und Umschreiben von URLs Der Unterschied zwischen dem *Umleiten* und *Neu schreiben* von URLs ist zwar gering, allerdings haben die beiden Verfahren sehr unterschiedliche Auswirkungen auf die Bereitstellung von Ressourcen für Clients.

Die URL-umschreibenden Middleware von ASP.NET Core kann für beide Vorgänge verwendet werden.

![Bei der *Umleitung von URLs* findet ein clientseitiger Vorgang statt, bei dem der Client angewiesen wird, auf eine Ressource unter einer anderen Adresse zuzugreifen, als die, die der Client ursprünglich angefordert hat. Dafür ist ein Roundtrip zum Server erforderlich. Die Umleitungs-URL, die an den Client zurückgegeben wird, wird in der Adressleiste des Browsers angezeigt, wenn der Client eine neue Anforderung an die Ressource sendet. Wenn `/resource` auf `/different-resource`*umgeleitet* wird, sendet der Server die Antwort, dass der Client die Ressource unter `/different-resource` abrufen soll. In der Antwort ist außerdem ein Statuscode enthalten, aus dem entnommen werden kann, ob die Umleitung temporär oder permanent ist. Ein Web-API-Dienstendpunkt wurde auf dem Server kurzzeitig von Version 1 (v1) auf Version 2 (v2) geändert.](url-rewriting/_static/url_redirect.png)

Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“.

* Der Server sendet eine „302 – Gefunden“-Antwort mit dem neuen, temporären Pfad für Version 2: „/v2/api“. Der Client sendet eine zweite Anforderung an den Dienst unter der Umleitungs-URL.

* Der Server gibt den Statuscode „200 – OK“ zurück. Wenn Anforderungen auf eine andere URL umgeleitet werden, muss angegeben werden, ob die Umleitung temporär oder permanent sein soll. Geben Sie hierzu den Statuscode mit der Antwort an:

Der Statuscode *301 – Permanent verschoben* wird verwendet, wenn der Ressource eine neue permanente URL zugewiesen wurde, und Sie dem Client die Anweisung geben möchten, dass alle zukünftigen Anforderungen an die Ressource die neue URL verwenden sollen.

*Der Client kann die Antwort zwischenspeichern und wiederverwenden, wenn der Statuscode 301 empfangen wird.* Der Statuscode *302 – Gefunden* wird verwendet, wenn die Umleitung temporär ist oder sowieso Änderungen vorbehalten sind. Der Statuscode 302 teilt dem Client mit, dass die URL nicht gespeichert und nicht wiederverwendet werden soll.

Weitere Informationen zu Statuscodes finden Sie unter [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

Bei der *Neuschreibung einer URL* handelt es sich um einen serverseitigen Vorgang, bei dem eine Ressource von einer anderen Ressourcenadresse, als der vom Client angeforderten, bereitgestellt wird.

![Wenn eine URL neu geschrieben wird, ist kein Roundtrip zum Server erforderlich. Die neu geschriebene URL wird nicht an den Server zurückgegeben und nicht in der Adressleiste des Browsers angezeigt. Wenn `/resource` in `/different-resource`*umgeschrieben* wird, ruft der Server die Ressource *intern* ab und gibt sie zurück an `/different-resource`. Auch wenn der Client die Ressource unter der neu geschriebene URL abrufen kann, erhält er nicht die Information, dass die Ressource unter der umgeschriebenen URL gespeichert ist, wenn er die Anforderung sendet und eine Antwort erhält.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Ein Web-API-Dienstendpunkt wurde auf dem Server von Version 1 (v1) auf Version 2 (v2) geändert.

Der Client sendet eine Anforderung an den Dienst unter dem Pfad für Version 1: „/v1/api“. Die Anforderungs-URL wird umgeschrieben, damit über den Pfad für Version 2 („/v2/api“) auf den Dienst zugegriffen werden kann.

## <a name="when-to-use-url-rewriting-middleware"></a>Der Dienst sendet eine Antwort an den Client mit dem Statuscode „200 – OK“.

URL-umschreibende Beispiel-App

* Mit der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) können Sie die Features der Middleware zur URL-Neuschreibung testen.
* Die App wendet Umleitungs- und Neuschreibungsregeln an und zeigt die umgeleitete oder neu geschriebene URL für verschiedene Szenarios an.
* Empfohlene Verwendung der URL-umschreibenden Middleware

Sie können Middleware zur URL-Neuschreibung verwenden, wenn die folgenden Ansätze nicht möglich sind:

[URL Rewrite module with IIS on Windows Server (URL-Neuschreibungsmodul mit IIS auf Windows-Server)](https://www.iis.net/downloads/microsoft/url-rewrite)

* [Apache mod_rewrite module on Apache Server (Apache mod_rewrite-Modul auf Apache-Server)](https://httpd.apache.org/docs/2.4/rewrite/)

  [URL rewriting on Nginx (URL-Neuschreibung auf Nginx)](https://www.nginx.com/blog/creating-nginx-rewrite-rules/) Sie können die Middleware auch verwenden, wenn die App auf dem [HTTP.sys-Server](xref:fundamentals/servers/httpsys) (früher als WebListener bezeichnet) gehostet wird.
* Die Hauptgründe für die Verwendung der serverbasierten Technologien zur URL-Neuschreibung in IIS, Apache und Nginx sind:

  Die Middleware unterstützt nicht alle Features dieser Module.

## <a name="package"></a>Einige Features der Servermodule funktionieren nicht mit ASP.NET Core-Projekten – z.B. die Einschränkungen `IsFile` und `IsDirectory` des IIS-Neuschreibungsmoduls.

Verwenden Sie in diesem Szenario stattdessen die Middleware.

## <a name="extension-and-options"></a>Die Leistung der Middleware stimmt wahrscheinlich nicht mit der Leistung der Module überein.

Benchmarking ist der einzige Weg, um sicher festzustellen, welcher Ansatz die Leistung am meisten beeinträchtigt oder ob die nachlassende Leistung nur geringfügig ist. Package Die URL-umschreibende Middleware wird über das [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite)-Paket bereitgestellt, das implizit in ASP.NET Core-Apps enthalten ist.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>Erweiterung und Optionen

Legen Sie URL-Neuschreibungs- und -Umleitungsregeln fest, indem Sie eine Instanz der [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions)-Klasse erstellen und Erweiterungsmethoden für jede Neuschreibungsregel hinzufügen.

* Verketten Sie mehrere Regeln in der Reihenfolge miteinander, in der sie verarbeitet werden sollen. Die `RewriteOptions` werden an die Middleware zur URL-Neuschreibung übergeben, wenn diese mit <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> zu der Anforderungspipeline hinzugefügt wird:

* Umleitung einer Nicht-WWW-Anforderung an eine WWW-Anforderung Drei Optionen ermöglichen der App, Nicht-`www`-Anforderungen an `www` umzuleiten: <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Die Anforderung wird dauerhaft an die Unterdomäne `www` umgeleitet, sofern die Anforderung nicht `www` ist. Wird mit dem Statuscode [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) umgeleitet.

### <a name="url-redirect"></a><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Die Anforderung wird an die Unterdomäne `www` umgeleitet, sofern die eingehende Anforderung nicht `www` ist.

Wird mit dem Statuscode [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) umgeleitet. Eine Überladung ermöglicht es Ihnen, den Statuscode für die Antwort zu senden. Verwenden Sie ein Feld der <xref:Microsoft.AspNetCore.Http.StatusCodes>-Klasse, um einen Statuscode zuzuweisen. Umleitungs-URL Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>, um Anforderungen umzuleiten.

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

Der erste Parameter enthält Ihren RegEx, damit dieser dem Pfad der eingehenden URL zugeordnet werden kann. Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge. Der dritte Parameter gibt, falls vorhanden, den Statuscode an. Wenn Sie den Statuscode nicht angeben, wird standardmäßig *302 – Gefunden* zurückgegeben, was bedeutet, dass die Ressource kurzzeitig verschoben oder ersetzt wurde. Aktivieren Sie in Ihrem Browser die Entwicklertools, und senden Sie eine Anforderung an die Beispiel-App mit dem Pfad `/redirect-rule/1234/5678`.

* Der RegEx stimmt mit dem Anforderungspfad unter `redirect-rule/(.*)` überein, und der Pfad wird durch `/redirected/1234/5678` ersetzt.
* Die Umleitungs-URL wird mit dem Statuscode *302 – Gefunden* an den Client zurückgesendet.

Unter der Umleitungs-URL sendet der Browser eine neue Anforderung, die in dessen Adressleiste angezeigt wird.

> [!WARNING]
> Da keine Regel in der Beispiel-App mit der Umleitungs-URL übereinstimmt: Die zweite Anforderung erhält die Antwort *200 – OK* von der App. Der Antworttext zeigt die Umleitungs-URL an.

Wenn eine URL *weitergeleitet* wird, wird ein Roundtrip zum Server ausgelöst.

![Seien Sie vorsichtig, wenn Sie Umleitungsregeln einrichten.](url-rewriting/_static/add_redirect.png)

Bei jeder Anforderung an die App werden Umleitungsregeln überprüft – auch nach einer Umleitung. Dabei kann schnell aus Versehen eine *Dauerschleife von Umleitungen* entstehen. Ursprüngliche Anforderung: `/redirect-rule/1234/5678` Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen Der Teil des Ausdruck in Klammern wird als *Erfassungsgruppe* bezeichnet.

Der Punkt (`.`) im Ausdruck steht für *Übereinstimmung mit beliebigem Zeichen*. Das Sternchen (`*`) steht für *Übereinstimmung mit dem vorausgehenden Zeichen (keinmal oder mindestens einmal)* . Daher werden die letzten beiden Pfadsegmente der URL (`1234/5678`) von der Erfassungsgruppe erfasst `(.*)`. Alle Werte, die Sie in der Anforderungs-URL nach `redirect-rule/` angeben, werden von dieser Erfassungsgruppe erfasst.

### <a name="url-redirect-to-a-secure-endpoint"></a>Erfassungsgruppen werden in der Ersetzungszeichenfolge mit dem Dollarzeichen (`$`) in die Zeichenfolge eingefügt. Danach folgt die Sequenznummer der Erfassung.

Der erste Wert der Erfassungsgruppe wird mit `$1` abgerufen, der zweite mit `$2`. Dies wird in Sequenzen für die Erfassungsgruppen Ihres RegEx weitergeführt. Nur eine Erfassungsgruppe ist in der Beispiel-App im RegEx der Umleitungsregel enthalten. Das bedeutet, dass es in die Ersetzungszeichenfolge nur eine Gruppe eingefügt wird, nämlich `$1`. Wenn die Regel angewendet wird, ändert sich die URL in `/redirected/1234/5678`.

* URL-Umleitung an einen sicheren Endpunkt
* Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>, um HTTP-Anforderungen auf denselben Host und Pfad mithilfe des HTTPS-Protokolls umzuleiten.

Wenn kein Statuscode angegeben wird, wird für die Middleware standardmäßig *302 – Gefunden* zurückgegeben.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

Wenn kein Port angegeben ist: Für die Middleware wird standardmäßig `null` zurückgegeben.

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> Das Schema ändert sich in `https` (HTTPS-Protokoll), und der Client hat über Port 443 Zugriff auf die Ressource. Im folgenden Beispiel wird dargestellt, wie Sie den Statuscode *301 – Permanent verschoben* festlegen und den Port in 5001 ändern.

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>, um unsichere Anforderungen auf denselben Host und Pfad mit einem sicheren HTTPS-Protokoll auf Port 443 umzuleiten. Die Middleware legt den Statuscode auf *301 – Permanent verschoben* fest. Wenn Sie eine Umleitung an einen sicheren Endpunkt ohne weitere erforderliche Umleitungsregeln durchführen, wird empfohlen, Middleware für HTTPS-Umleitung zu verwenden. Weitere Informationen finden Sie im Artikel zum [Erzwingen von HTTPS](xref:security/enforcing-ssl#require-https).

Anhand der Beispiel-App wird veranschaulicht, wie `AddRedirectToHttps` oder `AddRedirectToHttpsPermanent` verwendet werden sollen.

![Fügen Sie die Erweiterungsmethode zu den `RewriteOptions` hinzu.](url-rewriting/_static/add_redirect_to_https.png)

Senden Sie eine unsichere Anforderung an die App unter einer beliebigen URL.

![Schließen Sie die Sicherheitswarnung des Browsers, in der Sie darüber informiert werden, dass das selbstsignierte Zertifikat nicht vertrauenswürdig ist, oder erstellen sie eine Ausnahme, um dem Zertifikat zu vertrauen.](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>Ursprüngliche Anforderung über `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`

Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen Ursprüngliche Anforderung über `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure` Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen Umschreiben einer URL

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Erstellen Sie mithilfe von <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> eine Regel zum Umschreiben von URLs.

![Der erste Parameter enthält den RegEx, damit dieser der eingehenden URL zugeordnet werden kann.](url-rewriting/_static/add_rewrite.png)

Beim zweiten Parameter handelt es sich um eine Ersatzzeichenfolge.

Der dritte Parameter (`skipRemainingRules: {true|false}`) teilt der Middleware mit, ob sie zusätzliche Umschreibungsregeln überspringen soll, wenn die aktuelle Regel angewendet wird. Ursprüngliche Anforderung: `/rewrite-rule/1234/5678`

| Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen                               | Das Zirkumflexzeichen (`^`) am Anfang des Ausdrucks bedeutet, dass die Übereinstimmung schon am Anfang des URL-Pfads beginnt. |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | In dem vorherigen Beispiel zu den Umleitungsregeln, `redirect-rule/(.*)`, beginnt der RegEx nicht mit einem Zirkumflexzeichen (`^`).   |
| `/my-cool-redirect-rule/1234/5678` | So kann für eine Übereinstimmung ein beliebiges Zeichen im Pfad vor `redirect-rule/` stehen.   |
| `/anotherredirect-rule/1234/5678`  | Pfad   |

Match title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

| 'Blazor'                              | 'Identity' |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | 'Let's Encrypt'   |
| `/my-cool-rewrite-rule/1234/5678` | 'Razor'    |
| `/anotherrewrite-rule/1234/5678`  | 'SignalR' uid:    |

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

> [!NOTE]
> title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'
>
> * 'Identity'
> * 'Let's Encrypt'

### <a name="apache-mod_rewrite"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

'Identity'

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

'Let's Encrypt' 'Razor'

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

'SignalR' uid:

![title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:](url-rewriting/_static/add_apache_mod_redirect.png)

'Blazor'

* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

### <a name="iis-url-rewrite-module-rules"></a>'Blazor'

'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

'Identity' 'Let's Encrypt'

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

'Razor'

!['SignalR' uid:](url-rewriting/_static/add_iis_url_rewrite.png)

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

#### <a name="unsupported-features"></a>'Identity'

'Let's Encrypt'

* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'

#### <a name="supported-server-variables"></a>'Identity'

'Let's Encrypt'

* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* ----------------- | :---: | | `/redirect-rule/1234/5678`         | Ja   | | `/my-cool-redirect-rule/1234/5678` | Ja   | | `/anotherredirect-rule/1234/5678`  | Ja   |
* Die Umschreibungsregel (`^rewrite-rule/(\d+)/(\d+)`) stimmt nur mit Pfaden überein, wenn sie mit `rewrite-rule/` beginnen.
* Beachten Sie die unterschiedliche Übereinstimmung in der folgenden Tabelle:
* Pfad
* Match
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'

> [!NOTE]
> 'Let's Encrypt' 'Razor' 'SignalR' uid:
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

| `RewriteContext.Result`              | 'SignalR' uid:                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor'                                         |
| `RuleResult.EndResponse`             | 'Identity'                       |
| `RuleResult.SkipRemainingRules`      | 'Let's Encrypt' |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

'Razor'

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

'Identity'

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

'Blazor'

!['Identity'](url-rewriting/_static/add_redirect_png_requests.png)

'Let's Encrypt'

!['Razor'](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>'SignalR' uid:

| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | 'Blazor'<br>'Identity' | 'Let's Encrypt'<br>'Razor' |
| ---- | ------------------------------- | -------------------------------------- |
| 'SignalR' uid: | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| 'Blazor' | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| 'Identity' | 'Let's Encrypt'<br>'Razor'<br>'SignalR' uid: | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| 'Blazor' | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

> [!NOTE]
> 'Blazor' 'Identity'

'Let's Encrypt'

## <a name="url-redirect-and-url-rewrite"></a>'Razor'

'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt'

'Razor'

!['SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'](url-rewriting/_static/url_redirect.png)

'Razor'

* 'SignalR' uid: ----------------- | :---: | | `/rewrite-rule/1234/5678`         | Ja   | | `/my-cool-rewrite-rule/1234/5678` | Nein    | | `/anotherrewrite-rule/1234/5678`  | Nein    |

* Auf den `^rewrite-rule/`-Teil des Ausdruck folgen zwei Erfassungsgruppen: `(\d+)/(\d+)`. `\d` steht für *Übereinstimmung mit einer Ziffer (Zahl)* .

Das Pluszeichen (`+`) steht für *match one or more of the preceding character* (Übereinstimmung mit mindestens einem vorausgehenden Zeichen).

Aus diesem Grund muss die URL eine Zahl enthalten, auf die ein Schrägstrich und eine weitere Zahl folgt. Die Erfassungsgruppen werden in die umgeschriebene URL als `$1` und `$2` eingefügt. Über die Ersetzungszeichenfolge der Neuschreibungsregel werden die Erfassungsgruppen in die Abfragezeichenfolge eingefügt.

Der angeforderte `/rewrite-rule/1234/5678`-Pfad wird umgeschrieben, um eine Ressource unter `/rewritten?var1=1234&var2=5678` abzurufen.

Wenn es in der ursprünglichen Anforderung eine Abfragezeichenfolge gibt, bleibt diese erhalten, wenn die URL umgeschrieben wird.

![Es gibt keinen Roundtrip zum Server, um die Ressource abzurufen. Wenn es die Ressource gibt, wird sie abgerufen und dem Client mit dem Statuscode *200 – OK* zurückgegeben. Da der Client nicht umgeleitet wird, ändert sich die URL in der Adressleiste des Browsers nicht. Clients können nicht erkennen, dass ein Vorgang zum erneuten Schreiben einer URL auf dem Server stattgefunden hat.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a>Verwenden Sie `skipRemainingRules: true`, wo immer das möglich ist, da das Abgleichen von Regeln rechnerisch sehr aufwendig ist und die Antwortzeit der App erhöht.

Führen Sie folgende Schritte aus, um die schnellsten App-Antwortzeiten zu erreichen: Sortieren Sie Neuschreibungsregeln von der am häufigsten abgeglichenen Regel zu der am seltensten abgeglichenen Regel.

## <a name="when-to-use-url-rewriting-middleware"></a>Überspringen Sie die Verarbeitung der übrigen Regeln, wenn es zu einer Übereinstimmung kommt und keine zusätzlichen Regelverarbeitungen erforderlich sind.

Apache: „mod_rewrite“

* Wenden Sie die Apache-Regeln „mod_rewrite“ mit <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> an.
* Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird.
* Weitere Informationen zu diesen Regeln finden Sie unter [Apache: „mod_rewrite“](https://httpd.apache.org/docs/2.4/rewrite/).

Zum Lesen der Regeldatei *ApacheModRewrite.txt* wird <xref:System.IO.StreamReader> verwendet:

Die Beispiel-App leitet Anforderungen von `/apache-mod-rules-redirect/(.\*)` auf `/redirected?id=$1` um.

* Der Antwortstatuscode lautet *302 – Gefunden*.

  Ursprüngliche Anforderung: `/apache-mod-rules-redirect/1234` Browserfenster mit Entwicklertools, die die Anforderungen und Antworten nachverfolgen
* Die Middleware unterstützt die folgenden Servervariablen für die Apache „mod_rewrite“:

  CONN_REMOTE_ADDR

## <a name="package"></a>HTTP_ACCEPT

HTTP_CONNECTION

HTTP_COOKIE

## <a name="extension-and-options"></a>HTTP_FORWARDED

HTTP_HOST HTTP_REFERER HTTP_USER_AGENT

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a>HTTPS

IPV6

* QUERY_STRING REMOTE_ADDR

* REMOTE_PORT REQUEST_FILENAME REQUEST_METHOD REQUEST_SCHEME

### <a name="url-redirect"></a>REQUEST_URI

SCRIPT_FILENAME SERVER_ADDR SERVER_PORT SERVER_PROTOCOL TIME

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

TIME_DAY TIME_HOUR TIME_MIN TIME_MON TIME_SEC

* TIME_WDAY
* TIME_YEAR

Regeln zum IIS-Umschreibungsmodul

> [!WARNING]
> Um denselben Regelsatz zu verwenden, der für das IIS-Moduls für URL-Neuschreibung gilt, verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>. Vergewissern Sie sich, dass die Regeldatei mit der App bereitgestellt wird. Geben Sie der Middleware nicht die Anweisung, die *web.config*-Datei der App zu verwenden, wenn sie auf der Windows Server-IIS ausgeführt wird.

Bei IIS sollten diese Regeln außerhalb der *web.config*-Datei der App gespeichert werden, um Konflikte mit dem IIS-Neuschreibungsmodul zu vermeiden.

![Weitere Informationen und Beispiele zu den Regeln zum IIS-Umschreibungsmodul finden Sie unter [Using Url Rewrite Module 2.0 (Verwenden des URL-Umschreibungsmoduls 2.0)](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) und [URL Rewrite Module Configuration Reference (Konfigurationsreferenz des URL-Umschreibungsmoduls)](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).](url-rewriting/_static/add_redirect.png)

Zum Lesen der Regeldatei *IISUrlRewrite.xml* wird <xref:System.IO.StreamReader> verwendet: Die Beispiel-App schreibt Anforderungen von `/iis-rules-rewrite/(.*)` in `/rewritten?id=$1` um. Die Antwort wird an den Client mit dem Statuscode *200 – OK* gesendet. Ursprüngliche Anforderung: `/iis-rules-rewrite/1234` Browserfenster mit Entwicklertools, die die Anforderung und Antwort nachverfolgen

Wenn Sie über ein aktives IIS-Umschreibungsmodul verfügen, für das die Regeln auf Serverebene konfiguriert sind, die negative Auswirkungen auf Ihre App hätten, können Sie das IIS-Umschreibungsmodul für die App deaktivieren. Weitere Informationen finden Sie unter [Disabling IIS modules (Deaktivieren von IIS-Modulen)](xref:host-and-deploy/iis/modules#disabling-iis-modules). Nicht unterstützte Features Die im Lieferumfang von ASP.NET Core 2.x enthaltene Middleware unterstützt die folgenden Features des IIS-URL-Umschreibungsmoduls nicht:

### <a name="url-redirect-to-a-secure-endpoint"></a>Ausgehende Regeln

Benutzerdefinierte Servervariablen Platzhalter LogRewrittenUrl

* Unterstützte Servervariablen
* Die Middleware unterstützt die folgenden Servervariablen für das IIS-URL-Umschreibungsmodul:

CONTENT_LENGTH

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

CONTENT_TYPE HTTP_ACCEPT

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> HTTP_CONNECTION HTTP_COOKIE

HTTP_HOST HTTP_REFERER HTTP_URL HTTP_USER_AGENT

HTTPS

![LOCAL_ADDR](url-rewriting/_static/add_redirect_to_https.png)

QUERY_STRING

![REMOTE_ADDR](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a>REMOTE_PORT

REQUEST_FILENAME REQUEST_URI Sie können <xref:Microsoft.Extensions.FileProviders.IFileProvider> auch über <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> abrufen. Über diesen Ansatz können Sie flexibler einen Speicherort für die Dateien der Umschreibungsregeln auswählen.

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

Vergewissern Sie sich, dass die Dateien zu den Umschreibungsregeln auf dem Server unter dem von Ihnen angegebenen Pfad bereitgestellt werden.

![Methodenbasierte Regel](url-rewriting/_static/add_rewrite.png)

Verwenden Sie <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>, um Ihre eigene Regellogik in einer Methode zu implementieren.

`Add` macht <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> verfügbar, wodurch die Verwendung von <xref:Microsoft.AspNetCore.Http.HttpContext> in Ihrer Methode ermöglicht wird. [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) bestimmt, wie die zusätzliche Pipelineverarbeitung erfolgt.

| Legen Sie den Wert auf eines der <xref:Microsoft.AspNetCore.Rewrite.RuleResult>-Felder fest, die in der folgenden Tabelle beschrieben sind:                               | Aktion |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:   |
| `/my-cool-redirect-rule/1234/5678` | 'Blazor'   |
| `/anotherredirect-rule/1234/5678`  | 'Identity'   |

'Let's Encrypt' 'Razor'

| 'SignalR' uid:                              | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | 'Blazor'   |
| `/my-cool-rewrite-rule/1234/5678` | 'Identity'    |
| `/anotherrewrite-rule/1234/5678`  | 'Let's Encrypt'    |

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

> [!NOTE]
> 'Razor' 'SignalR' uid:
>
> * title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
> * 'Blazor'

### <a name="apache-mod_rewrite"></a>'Identity'

'Let's Encrypt' 'Razor' 'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

'Blazor' 'Identity'

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

'Let's Encrypt'

!['Razor'](url-rewriting/_static/add_apache_mod_redirect.png)

'SignalR' uid:

* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'

### <a name="iis-url-rewrite-module-rules"></a>'SignalR' uid:

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt' 'Razor'

'SignalR' uid:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

'Identity'

!['Let's Encrypt'](url-rewriting/_static/add_iis_url_rewrite.png)

'Razor' 'SignalR' uid:

#### <a name="unsupported-features"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:

#### <a name="supported-server-variables"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* ------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:

> [!NOTE]
> title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity'
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a>'Let's Encrypt'

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor'

| `RewriteContext.Result`              | 'Identity'                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| 'Let's Encrypt' | 'Razor'                                         |
| `RuleResult.EndResponse`             | 'SignalR' uid:                       |
| `RuleResult.SkipRemainingRules`      | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

'Blazor' 'Identity' 'Let's Encrypt' 'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor'

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

'Identity' 'Let's Encrypt' 'Razor'

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

'SignalR' uid:

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a>title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

'Blazor' 'Identity' 'Let's Encrypt'

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

'Razor' 'SignalR' uid: title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc: 'Blazor' 'Identity' 'Let's Encrypt'

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

'Razor'

!['SignalR' uid:](url-rewriting/_static/add_redirect_png_requests.png)

title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:

!['Blazor'](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a>'Identity'

| 'Let's Encrypt' | 'Razor'<br>'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:<br>'Blazor' |
| ---- | ------------------------------- | -------------------------------------- |
| 'Identity' | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| 'Let's Encrypt' | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| 'Razor' | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| 'SignalR' uid: | title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:<br>'Blazor'<br>'Identity' | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| 'Let's Encrypt' | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| 'Razor' | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a>'SignalR' uid:

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
* 'Let's Encrypt'
* 'Razor'
* 'SignalR' uid:
* title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:
* 'Blazor'
* 'Identity'
