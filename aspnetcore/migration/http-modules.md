---
title: Migrieren von HTTP-Handler und Module zu ASP.NET Core-middleware
author: rick-anderson
description: ''
ms.author: tdykstra
ms.date: 12/07/2016
uid: migration/http-modules
ms.openlocfilehash: 516230a66ee3edba986c91d79684256aa8e4c994
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65087016"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a>Migrieren von HTTP-Handler und Module zu ASP.NET Core-middleware

Durch [Matt Perdeck](https://www.linkedin.com/in/mattperdeck)

In diesem Artikel zeigt, wie Sie vorhandene ASP.NET migrieren [HTTP-Module und Handler in "System.Webserver"](/iis/configuration/system.webserver/) zu ASP.NET Core [Middleware](xref:fundamentals/middleware/index).

## <a name="modules-and-handlers-revisited"></a>Module und Handler revisited

Bevor Sie fortfahren, um ASP.NET Core-Middleware, zunächst betrachten wir wie HTTP-Module und Handler verwendet werden:

![Module-Handler](http-modules/_static/moduleshandlers.png)

**Handler sind:**

* Klassen, in denen [IHttpHandler](/dotnet/api/system.web.ihttphandler)

* Verwendet, um Anforderungen mit einem angegebenen Dateinamen oder die Erweiterung, z. B. behandeln *berichtsserverprojekten*

* [Konfiguriert](/iis/configuration/system.webserver/handlers/) in *"Web.config"*

**Module sind:**

* Klassen, in denen ["IHttpModule"](/dotnet/api/system.web.ihttpmodule)

* Für jede Anforderung aufgerufen

* (Weitere Verarbeitung einer Anforderung beenden) kurzschließen können

* Hinzufügen der HTTP-Antwort, oder ihre eigenen erstellen können

* [Konfiguriert](/iis/configuration/system.webserver/modules/) in *"Web.config"*

**Die Reihenfolge, in der Module eingehenden Anforderungen verarbeiten, wird durch bestimmt:**

1. Die [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx), dies ist eine Reihe-Ereignisse, die von ASP.NET ausgelöst: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Jedes Modul kann es sich um einen Handler für ein oder mehrere Ereignisse erstellen.

2. Für das gleiche Ereignis, die Reihenfolge, in dem sie in konfiguriert sind *"Web.config"*.

Neben der Module, können Sie Handler für die Lebenszyklus-Ereignisse zum Hinzufügen Ihrer *"Global.asax.cs"* Datei. Diese Handler führen nach dem Handler in der konfigurierten Module.

## <a name="from-handlers-and-modules-to-middleware"></a>Vom Handler und Module zu middleware

**Middleware sind einfacher als HTTP-Module und Handler:**

* Module, Handler *"Global.asax.cs"*, *"Web.config"* (mit Ausnahme von IIS-Konfiguration) und der Lebenszyklus der Anwendung nicht mehr vorhanden sind

* Die Rollen von Module und Handler haben von Middleware übernommen wurden

* Middleware konfiguriert sind, mithilfe von Code statt im *"Web.config"*

* [Pipeline Verzweigen](xref:fundamentals/middleware/index#use-run-and-map) Sie Anforderungen an bestimmte Middleware senden, basierend auf nicht nur die URL, sondern auch von Anforderungsheadern, Abfragezeichenfolgen usw. ermöglicht.

**Middleware sind Module sehr ähnlich:**

* Im Prinzip für jede Anforderung aufgerufen

* Kann eine Anforderung kurzschließen, indem [nicht die Anforderung an die nächste Middleware übergeben](#http-modules-shortcircuiting-middleware)

* Erstellen Sie ihre eigenen HTTP-Antwort

**Middleware und Module werden in einer anderen Reihenfolge verarbeitet:**

* Reihenfolge der Middleware basiert auf der Reihenfolge, in der sie in die Anforderungspipeline, eingefügt sind während die Reihenfolge der Module vor allem auf basiert [Anwendungslebenszyklus](https://msdn.microsoft.com/library/ms227673.aspx) Ereignisse

* Reihenfolge der Middleware für Antworten ist das Gegenteil von dem für Anforderungen, während die Reihenfolge der Module, die für Anforderungen und Antworten identisch ist

* Finden Sie unter [erstellen eine middlewarepipeline mit IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)

![Middleware](http-modules/_static/middleware.png)

Beachten Sie, wie in der Abbildung oben die authentifizierungsmiddleware die Anforderung kurzgeschlossen.

## <a name="migrating-module-code-to-middleware"></a>Migrieren von Modulcode zu middleware

Ein vorhandenes HTTP-Modul sieht etwa wie folgt:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

Siehe die [Middleware](xref:fundamentals/middleware/index) Seite eine ASP.NET Core-Middleware ist eine Klasse, die verfügbar gemacht ein `Invoke` Methode aufnehmen ein `HttpContext` und Zurückgeben einer `Task`. Ihre neue Middleware wird wie folgt aussehen:

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

Die vorherige Middleware Vorlage stammt aus dem Abschnitt für [Schreiben von Middleware](xref:fundamentals/middleware/write).

Die *MyMiddlewareExtensions* Hilfsklasse erleichtert das Konfigurieren Ihrer Middleware in Ihre `Startup` Klasse. Die `UseMyMiddleware` Methode fügt Ihrer Middleware-Klasse, zu der Anforderungspipeline. Die Middleware erforderlich sind, erhalten in den Konstruktor der Middleware eingefügt.

<a name="http-modules-shortcircuiting-middleware"></a>

Ihr Modul kann eine Anforderung, z. B. wenn der Benutzer nicht autorisiert ist, beenden:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

Eine Middleware verarbeitet dies durch den Aufruf nicht `Invoke` auf die nächste Middleware in der Pipeline. Beachten Sie, dass dadurch die Anforderung, vollständig beendet, nicht behalten Sie, da es sich bei vorherigen Middlewares weiterhin aufgerufen wird, wenn die Antwort die Möglichkeit, über die Pipeline zurückgegeben wird.

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

Beim Migrieren des Moduls Funktionen zu Ihrer neuen Middleware können möglicherweise den Code Kompilieren nicht, da die `HttpContext` Klasse in ASP.NET Core deutlich geändert hat. [Später](#migrating-to-the-new-httpcontext), sehen Sie, wie Sie in der neuen ASP.NET Core "HttpContext" migrieren.

## <a name="migrating-module-insertion-into-the-request-pipeline"></a>Migrieren von Modul einfügen in die Anforderungspipeline

HTTP-Module werden in der Regel hinzugefügt, um die Anforderungspipeline mithilfe *"Web.config"*:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

Konvertieren von [Hinzufügen Ihrer neuen Middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) zu der Anforderungspipeline in Ihre `Startup` Klasse:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

Die genaue Stelle in der Pipeline, in dem Sie Ihre neue Middleware einfügen, hängt das Ereignis, das sie als Modul verarbeitet (`BeginRequest`, `EndRequest`usw.) und die Reihenfolge, in der Liste mit den Modulen in *"Web.config"*.

Wie bereits erwähnt, besteht keine Anwendungslebenszyklus in ASP.NET Core und die Reihenfolge, in der Antworten von Middleware verarbeitet werden, die von der Reihenfolge von Modulen verwendeten unterscheidet sich. Dadurch kann Ihre Bestellungen Entscheidung schwieriger.

Wenn Sortierung ein Problem aufgetreten ist, können Sie Ihr Modul in mehreren middlewarekomponenten aufteilen, die unabhängig voneinander sortiert werden können.

## <a name="migrating-handler-code-to-middleware"></a>Migrieren von Handlercode zu middleware

Ein HTTP-Handler sieht etwa folgendermaßen aus:

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

Im ASP.NET Core-Projekt würden Sie dies eine Middleware, die etwa wie folgt übersetzt:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

Diese Middleware ist sehr ähnlich ist, an die Middleware für Module. Der einzige wirkliche Unterschied besteht darin, die hier gibt es erfolgt kein Aufruf von `_next.Invoke(context)`. Das ist sinnvoll, da der Handler am Ende der Pipeline, ist daher gibt es keine nächste Middleware aufrufen.

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a>Migrieren von Handler einfügen in die Anforderungspipeline

Konfigurieren eines HTTP-Handlers erfolgt in *"Web.config"* und sieht etwa folgendermaßen aus:

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

Sie können dies konvertieren, indem die Anforderungspipeline in Ihrer neuen Handler Middleware hinzugefügt Ihre `Startup` -Klasse, ähnlich wie Middleware, die aus Modulen konvertiert. Das Problem mit diesem Ansatz ist, dass sie alle Anforderungen an Ihre neuen Handler Middleware senden würde. Sie möchten jedoch nur Anforderungen mit einer bestimmten Erweiterung für Ihre Middleware zu erreichen. Die Sie die gleiche Funktionalität erhalten würde, die Sie mit der HTTP-Handler hatten.

Eine Lösung besteht darin, Branchen die Pipeline für Anforderungen mit einer angegebenen Erweiterung, mit der `MapWhen` -Erweiterungsmethode. Sie dazu in der gleichen `Configure` Methode, die in dem Sie die andere Middleware hinzufügen:

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

`MapWhen` werden diese Parameter verwendet:

1. Ein Lambda-Ausdruck, der akzeptiert die `HttpContext` und gibt `true` , wenn Sie den Branch die Anforderung gesendet werden soll. Dies bedeutet, dass Sie die Anforderungen nicht nur basierend auf ihrer Erweiterung, sondern auch auf die Anforderungsheader, Abfrageparameter usw. verzweigen können.

2. Ein Lambda-Ausdruck, der akzeptiert eine `IApplicationBuilder` und fügt die gesamte Middleware für die Verzweigung. Dies bedeutet, dass die Verzweigung vor Ihrer Middleware Handler weiteren Middleware hinzugefügt werden können.

Die Middleware zur Pipeline hinzugefügt werden, bevor der Branch für alle Anforderungen aufgerufen werden soll; der Branch wird keine Auswirkungen darauf haben.

## <a name="loading-middleware-options-using-the-options-pattern"></a>Laden die Verwendung des optionsmusters-middlewareoptionen.

Einige Module und Handler verfügen über Konfigurationsoptionen, die in gespeichert werden *"Web.config"*. In ASP.NET Core ist ein neues Konfigurationsmodell jedoch anstelle des verwendet *"Web.config"*.

Die neue [Konfigurationssystem](xref:fundamentals/configuration/index) bietet Ihnen diese Optionen, um dieses Problem zu beheben:

* Die Optionen an die Middleware, direkt einfügen, siehe die [nächsten Abschnitt](#loading-middleware-options-through-direct-injection).

* Verwenden der [optionsmuster](xref:fundamentals/configuration/options):

1. Erstellen einer Klasse zum halten Ihre middlewareoptionen, z.B.:

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. Die Optionswerte Store

   Das Konfigurationssystem können Sie Werte an einer beliebigen Stelle zu speichern, die Sie möchten. Allerdings verwenden die meisten Websites *"appSettings.JSON"*, sodass wir dieses Ansatzes eingehen werde:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   *MyMiddlewareOptionsSection* hier ist ein Name des Abschnitts. Sie müssen nicht den Namen der Optionsklasse identisch sein.

3. Ordnen Sie die Werte der Optionsklasse

    Das optionsmuster stellt anhand des ASP.NET Core Dependency Injection-Framework Optionstyp zuordnen (wie z. B. `MyMiddlewareOptions`) mit einem `MyMiddlewareOptions` Objekt, das die tatsächlichen Optionen verfügt.

    Aktualisieren Ihrer `Startup` Klasse:

   1. Bei Verwendung von *"appSettings.JSON"*, fügen Sie es an den konfigurationsbuilder in die `Startup` Konstruktor:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. Konfigurieren Sie den optionendienst an:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. Ordnen Sie die Optionen der Options-Klasse:

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. Fügen Sie die Optionen in Ihrer middlewarekonstruktor. Dies entspricht dem injizieren von Optionen in einen Controller.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   Die [UseMiddleware](#http-modules-usemiddleware) Erweiterungsmethode, die Ihre Middleware, fügt die `IApplicationBuilder` übernimmt der Abhängigkeitsinjektion.

   Dies ist nicht beschränkt auf `IOptions` Objekte. Jedes andere Objekt, das Ihrer Middleware ist erforderlich, kann auf diese Weise eingefügt werden.

## <a name="loading-middleware-options-through-direct-injection"></a>Laden über direkte Injection-middlewareoptionen.

Das optionsmuster hat es sich um den Vorteil, den lose Kopplung von Optionswerten und seinem Consumer erstellt wird. Sobald Sie eine Options-Klasse die tatsächlichen Optionswerte zugeordnet haben, kann jede beliebige andere Klasse mit den Optionen, über die Dependency Injection-Framework zugreifen. Es ist nicht erforderlich, um Optionswerte zu übergeben.

Dies wird jedoch, wenn Sie dieselbe Middleware zweimal mit verschiedenen Optionen verwenden möchten. Zum Beispiel eine autorisierungsmiddleware in anderen Branches, sodass verschiedene Rollen verwendet. Die für eine Optionsklasse können keine zwei unterschiedliche Optionsobjekte zugeordnet werden.

Die Lösung besteht darin, die Optionen Objekte durch die tatsächlichen Optionswerte in Ihrem `Startup` Klasse, und übergeben Sie diese direkt auf jede Instanz Ihrer Middleware.

1. Fügen Sie einen zweiten Schlüssel um *"appSettings.JSON"*

   Um einen zweiten Satz von Optionen zum Hinzufügen der *"appSettings.JSON"* Datei, verwenden Sie einen neuen Schlüssel, um eindeutig zu identifizieren:

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. Abrufen von Optionswerten, und diese an die Middleware übergeben. Die `Use...` Erweiterungsmethode (die Ihre Middleware zur Pipeline hinzugefügt wird) ist ein logischer Ansatzpunkt zum Übergeben der Werte: 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. Aktiviert die Middleware auszuführende Options-Parameter. Stellen Sie eine Überladung von der `Use...` Erweiterungsmethode (, die die Options-Parameter akzeptiert und übergibt es an `UseMiddleware`). Wenn `UseMiddleware` heißt mit Parametern, die Parameter an Ihre middlewarekonstruktor übergeben beim Instanziieren der Middleware-Objekt.

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   Beachten Sie, wie dies in der Options-Objekt dient als Wrapper für ein `OptionsWrapper` Objekt. Dadurch wird implementiert `IOptions`, wie von der middlewarekonstruktor erwartet.

## <a name="migrating-to-the-new-httpcontext"></a>Migration zu neuen "HttpContext"

Sie haben weiter oben gesehen, die die `Invoke` -Methode in Ihrer Middleware nimmt einen Parameter vom Typ `HttpContext`:

```csharp
public async Task Invoke(HttpContext context)
```

`HttpContext` in ASP.NET Core wurde erheblich geändert werden. In diesem Abschnitt wird gezeigt, wie die am häufigsten verwendeten Eigenschaften der übersetzen [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) mit dem neuen `Microsoft.AspNetCore.Http.HttpContext`.

### <a name="httpcontext"></a>HttpContext

**"HttpContext.Items"** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

**Eindeutige Anforderungs-ID (keine Entsprechung System.Web.HttpContext)**

Erhalten Sie eine eindeutige Id für jede Anforderung. Sehr nützlich in Ihre Protokolle eingeschlossen werden sollen.

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a>HttpContext.Request

**HttpContext.Request.HttpMethod** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

**HttpContext.Request.QueryString** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

**HttpContext.Request.Url** und **HttpContext.Request.RawUrl** übersetzen in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

**HttpContext.Request.IsSecureConnection** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

**HttpContext.Request.UserHostAddress** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

**HttpContext.Request.Cookies** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

**HttpContext.Request.RequestContext.RouteData** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

**HttpContext.Request.Headers** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

**HttpContext.Request.UserAgent** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

**HttpContext.Request.UrlReferrer** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

**HttpContext.Request.ContentType** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

**HttpContext.Request.Form** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> Lesen Formularwerte, nur dann, wenn der Inhalt Sub-Typ ist *X-www-form-urlencoded* oder *Formulardaten*.

**HttpContext.Request.InputStream** translates to:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> Verwenden Sie diesen Code nur in einem Handler für Typ-Middleware, am Ende einer Pipeline.
>
>Sie können die unformatierten Text lesen, wie oben nur einmal pro Anforderung. Middleware, die versuchen, die zum Lesen des Texts nach dem ersten Lesen liest aus einem leeren Textkörper.
>
>Dies gilt nicht zum Lesen eines Formulars wie zuvor gezeigt, da, die aus einem Puffer ausgeführt wird.

### <a name="httpcontextresponse"></a>HttpContext.Response

**HttpContext.Response.Status** und **HttpContext.Response.StatusDescription** übersetzen in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

**HttpContext.Response.ContentEncoding** und **HttpContext.Response.ContentType** übersetzen in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

**HttpContext.Response.ContentType** auf eigene auch übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

**HttpContext.Response.Output** übersetzt in:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

**HttpContext.Response.TransmitFile**

Erstellen einer Datei wird erläutert [hier](../fundamentals/request-features.md#middleware-and-request-features).

**HttpContext.Response.Headers**

Senden der Antwortheader wird durch die Tatsache verkompliziert, dass wenn Sie festlegen, nachdem alles in den Antworttext geschrieben wurde, sie wird nicht gesendet.

Die Lösung besteht darin, eine Callback-Methode festgelegt wird, die vor dem Schreiben auf die Antwort beginnt rechts aufgerufen werden. Dies erfolgt am besten am Anfang der `Invoke` -Methode in Ihrer Middleware. Es ist diese Callback-Methode, die Ihre Antwortheader festlegt.

Im folgenden Code wird eine Rückrufmethode namens `SetHeaders`:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Die `SetHeaders` Callback-Methode sieht wie folgt:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

**HttpContext.Response.Cookies**

Übertragen Sie Cookies im Browser in einer *Set-Cookie* -Antwortheader. Senden Cookies erfordert daher demselben Rückruf verwendet zum Senden der Antwortheader:

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

Die `SetCookies` Callback-Methode würde wie folgt aussehen:

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [HTTP-Handler und HTTP-Module (Übersicht)](/iis/configuration/system.webserver/)
* [Konfiguration](xref:fundamentals/configuration/index)
* [Application Startup (Starten von Anwendungen)](xref:fundamentals/startup)
* [Middleware](xref:fundamentals/middleware/index)
