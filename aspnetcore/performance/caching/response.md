---
title: Zwischenspeichern von Antworten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie die Zwischenspeicherung von Antworten verwenden können, um die Bandbreitenanforderungen zu senken und die Leistung von ASP.NET Core-Apps zu steigern.
ms.author: riande
ms.date: 01/07/2018
uid: performance/caching/response
ms.openlocfilehash: 5fbcaddff6e53d01a19ba8a7455c719feb614326
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098947"
---
# <a name="response-caching-in-aspnet-core"></a>Zwischenspeichern von Antworten in ASP.NET Core

Durch [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), und [Luke Latham](https://github.com/guardrex)

> [!NOTE]
> Zwischenspeichern von Antworten in Razor Pages ist in ASP.NET Core 2.1 oder höher verfügbar.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zwischenspeichern von Antworten reduziert die Anzahl der Anforderungen, die ein Client oder Proxy an einen Webserver sendet. Zwischenspeichern von Antworten auch verringert die Menge der Arbeit der Webserver ausgeführt werden, um eine Antwort zu generieren. Zwischenspeichern von Antworten wird durch Header gesteuert werden, die angeben, wie Sie Client und Proxy-Middleware zum Zwischenspeichern von Antworten soll.

Die [ResponseCache-Attribut](#responsecache-attribute) beim Festlegen der Antwort-Header, welche Clients möglicherweise, beim Zwischenspeichern von Antworten berücksichtigt Zwischenspeichern teilnimmt. [Zwischenspeichern von Antworten-Middleware](xref:performance/caching/middleware) kann zum Zwischenspeichern von Antworten auf dem Server verwendet werden. Die Middleware können `ResponseCache` Attributeigenschaften serverseitige Verhalten beim Zwischenspeichern zu beeinflussen.

## <a name="http-based-response-caching"></a>Zwischenspeichern von Antworten HTTP-basierte

Die [Zwischenspeichern von HTTP 1.1-Spezifikation](https://tools.ietf.org/html/rfc7234) beschrieben, wie internetcaches Verhalten soll. Der primäre HTTP-Header für das caching verwendet [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), dient zum Angeben der Cache *Direktiven*. Die Anweisungen Steuern des zwischenspeicherverhaltens Anforderungen ihren Weg von den Clients an Server senden und Antworten an Clients ihren Weg von Servern machen. Verschieben von Anforderungen und Antworten über Proxyserver und webanwendungsproxy-Server müssen auch der Zwischenspeicherung von HTTP 1.1-Spezifikation entsprechen.

Allgemeine `Cache-Control` Direktiven werden in der folgenden Tabelle angezeigt.

| Direktive                                                       | Aktion |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Ein Cache kann die Antwort speichern. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Die Antwort muss nicht von einem freigegebenen Cache gespeichert werden. Ein privater Cache möglicherweise speichern und Wiederverwenden von der Antwort. |
| [Max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Der Client wird nicht akzeptiert, eine Antwort, deren Alter größer als die angegebene Anzahl von Sekunden ist. Beispiele: `max-age=60` (60 Sekunden), `max-age=2592000` (1 Monat) |
| [ohne-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Für Anforderungen**: Ein Cache darf keine gespeicherte Antwort verwenden, zum Erfüllen der Anforderung. Hinweis: Der Ursprungsserver die Antwort erneut für den Client generiert und die Middleware aktualisiert die gespeicherte Antwort im jeweiligen Cache.<br><br>**Für Antworten**: Die Antwort darf nicht für eine nachfolgende Anforderung ohne Überprüfung auf dem Ursprungsserver verwendet werden. |
| [ohne-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Für Anforderungen**: Ein Cache muss die Anforderung nicht speichern.<br><br>**Für Antworten**: Ein Cache muss einen beliebigen Teil der Antwort nicht speichern. |

Andere Cacheheader, die Zwischenspeichern eine Rolle spielen, werden in der folgenden Tabelle angezeigt.

| Header                                                     | Funktion |
| ---------------------------------------------------------- | -------- |
| [ALTER](https://tools.ietf.org/html/rfc7234#section-5.1)     | Eine Schätzung der die Zeitspanne in Sekunden seit der Antwort generiert oder erfolgreich auf dem Ursprungsserver überprüft wurde. |
| [Läuft ab](https://tools.ietf.org/html/rfc7234#section-5.3) | Das Datum und Uhrzeit, nach dem die Antwort als veraltet angesehen wird. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Vorhanden ist, für die Abwärtskompatibilität mit HTTP/1.0 für die Einstellung speichert `no-cache` Verhalten. Wenn die `Cache-Control` Header vorhanden ist, ist die `Pragma` Header wird ignoriert. |
| [Variieren](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Gibt an, dass eine zwischengespeicherte Antwort nicht, wenn alle gesendet werden muss von der `Vary` Headerfelder in der zwischengespeicherten Antwort zur ursprünglichen Anforderung und die neue Anforderung zu entsprechen. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Zwischenspeichern Hinsicht HTTP-basierte Anforderung cachesteuerungsdirektiven

Die [Zwischenspeichern von HTTP 1.1-Spezifikation für den Cache-Control-Header](https://tools.ietf.org/html/rfc7234#section-5.2) erfordert einen Cache, der einen gültigen berücksichtigt `Cache-Control` Header, die vom Client gesendet werden. Ein Client kann Anforderungen mit stellen eine `no-cache` Headerwert und erzwingen Sie den Server aus, um eine neue Antwort für jede Anforderung zu generieren.

Berücksichtigt immer Client `Cache-Control` Anforderungsheader ist sinnvoll, wenn Sie das Ziel der HTTP-Zwischenspeicherung in Betracht ziehen. In der offiziellen Spezifikation Zwischenspeicherung dient zum Reduzieren des Latenz und Aufwand der Erfüllung der Anforderungen in einem Netzwerk von Clients, Proxys und Servern. Es ist nicht unbedingt eine Möglichkeit zum Steuern der Last auf einem Ursprungsserver.

Ist kein Entwickler-Steuerelement über dieses Verhalten beim Zwischenspeichern bei Verwendung der [Antworten Zwischenspeichern Middleware](xref:performance/caching/middleware) , da die Middleware der offiziellen zwischenspeicherungsspezifikation entspricht. [Verbesserungen an die Middleware geplant](https://github.com/aspnet/AspNetCore/issues/2612) sind eine gute Gelegenheit zum Konfigurieren der Middleware zum Ignorieren von einer Anforderung `Cache-Control` Header, die bei der Entscheidung, um eine zwischengespeicherte Antwort zu verarbeiten. Geplante-Erweiterungen bieten die Möglichkeit, eine bessere Kontrolle serverauslastung.

## <a name="other-caching-technology-in-aspnet-core"></a>Andere cachetechnologie in ASP.NET Core

### <a name="in-memory-caching"></a>Zwischenspeicherung im Arbeitsspeicher

Zwischenspeicherung im Arbeitsspeicher verwendet Server-Speicher zum Speichern von zwischengespeicherter Daten. Diese Form des Cachings eignet sich für einen einzelnen oder mehrerer Server mithilfe von *persistente Sitzungen*. Persistente Sitzungen bedeutet, dass die Anforderungen von einem Client immer auf dem gleichen Server zur Verarbeitung weitergeleitet werden.

Weitere Informationen finden Sie unter [in-Memory-Cache](xref:performance/caching/memory).

### <a name="distributed-cache"></a>Verteilter Cache

Verwenden Sie einen verteilten Cache zum Speichern von Daten im Arbeitsspeicher, wenn die app in einer Cloud oder Server-Farm gehostet wird. Der Cache wird auf allen Servern gemeinsam genutzt, die Anforderungen verarbeiten. Ein Client kann eine Anforderung übermitteln, die von einem beliebigen Server in der Gruppe verarbeitet wird, wenn zwischengespeicherte Daten für den Client verfügbar sind. ASP.NET Core bietet SQL Server und verteilt Redis-Caches.

Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Cache-Taghilfsprogramm

Sie können den Inhalt von einem MVC-Ansicht oder Razor-Seite mit Cache-Taghilfsprogramms Zwischenspeichern. Das Cache-Taghilfsprogramm verwendet speicherinternes caching, um Daten zu speichern.

Weitere Informationen finden Sie unter [Cache-Taghilfsprogramm im ASP.NET Core MVC](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).

### <a name="distributed-cache-tag-helper"></a>Taghilfsprogramm für verteilten Cache

Sie können den Inhalt von einem MVC-Ansicht oder Razor-Seite in verteilten Cloud oder Webfarm-Szenarios mit dem Taghilfsprogramm für verteilten Cache zwischenspeichern. Das Taghilfsprogramm für verteilten Cache werden SQL Server oder Redis verwendet, um Daten zu speichern.

Weitere Informationen finden Sie unter [Taghilfsprogramm für verteilten Cache](xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper).

## <a name="responsecache-attribute"></a>ResponseCache-Attribut

Die [ResponseCacheAttribute](/dotnet/api/Microsoft.AspNetCore.Mvc.ResponseCacheAttribute) gibt die Parameter zum Festlegen der entsprechenden Header in das Zwischenspeichern von Antworten erforderlich sind.

> [!WARNING]
> Deaktivieren Sie die Zwischenspeicherung für Inhalte, die Informationen für authentifizierte Clients enthält. Zwischenspeichern von sollte nur aktiviert werden, für Inhalte, die sich nicht ändert, die anhand eines Benutzers Identität oder, ob ein Benutzer angemeldet ist.

[VaryByQueryKeys](/dotnet/api/microsoft.aspnetcore.mvc.responsecacheattribute.varybyquerykeys) hängt von der gespeicherten Antwort nach den Werten der angegebenen Liste von Abfrage-Schlüssel. Wenn ein einzelner Wert des `*` angegeben wird, hängt von die Middleware Antworten von allen anfordern, Abfragezeichenfolgen-Parameter. `VaryByQueryKeys` erfordert ASP.NET Core 1.1 oder höher.

[Antworten Zwischenspeichern Middleware](xref:performance/caching/middleware) muss aktiviert sein, um festzulegen der `VaryByQueryKeys` Eigenschaft ist; andernfalls wird eine Laufzeitausnahme ausgelöst. Es gibt keine entsprechenden HTTP-Header für die `VaryByQueryKeys` Eigenschaft. Die Eigenschaft ist eine HTTP-Funktion, die von der Antwort-Caching-Middleware verarbeitet. Für die Middleware, um eine zwischengespeicherte Antwort zu verarbeiten müssen der Abfragezeichenfolge und den Wert der Abfragezeichenfolge eine vorherige Anforderung übereinstimmen. Betrachten Sie beispielsweise die Reihenfolge der Anforderungen und Ergebnissen, die in der folgenden Tabelle gezeigt.

| Anforderung                          | Ergebnis                   |
| -------------------------------- | ------------------------ |
| `http://example.com?key1=value1` | Vom Server zurückgegebenen     |
| `http://example.com?key1=value1` | Zurückgegeben von middleware |
| `http://example.com?key1=value2` | Vom Server zurückgegebenen     |

Die erste Anforderung wird vom Server zurückgegebenen und im Middleware zwischengespeichert. Die zweite Anforderung wird von Middleware zurückgegeben, da die Abfragezeichenfolge die vorherige Anforderung übereinstimmt. Die dritte Anforderung ist nicht im Cache Middleware, dass Abfragezeichenfolgen-Werts nicht mit eine vorherige Anforderung übereinstimmt. 

Die `ResponseCacheAttribute` dient zum Konfigurieren und erstellen Sie (über `IFilterFactory`) eine [ResponseCacheFilter](/dotnet/api/microsoft.aspnetcore.mvc.internal.responsecachefilter). Die `ResponseCacheFilter` die Arbeit der Aktualisierung des entsprechenden HTTP-Header und Funktionen der Antwort durchführt. Der Filter:

* Entfernt alle vorhandenen Header für `Vary`, `Cache-Control`, und `Pragma`. 
* Schreibt die entsprechenden Header auf Grundlage der Eigenschaften legen Sie in der `ResponseCacheAttribute`. 
* Aktualisiert die Antwort Zwischenspeichern von HTTP-Funktion, wenn `VaryByQueryKeys` festgelegt ist.

### <a name="vary"></a>Variieren

Dieser Header wird nur geschrieben, wenn die `VaryByHeader` festgelegt wird. Festgelegt auf die `Vary` Eigenschaftswert. Das folgende Beispiel verwendet die `VaryByHeader` Eigenschaft:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_VaryByHeader&highlight=1)]

::: moniker-end

Sie können die Antwortheadern mit Ihres Browsers Netzwerktools anzeigen. Die folgende Abbildung zeigt die Ausgabe auf Microsoft Edge-F12 die **Netzwerk** Registerkarte, wenn die `About2` Aktionsmethode wird aktualisiert:

![Microsoft Edge F12-Ausgabe auf der Registerkarte "Netzwerk", wenn die About2 Aktionsmethode aufgerufen wird](response/_static/vary.png)

### <a name="nostore-and-locationnone"></a>NoStore und Location.None

`NoStore` überschreibt die meisten anderen Eigenschaften. Wenn diese Eigenschaft auf festgelegt ist `true`, `Cache-Control` Header nastaven NA hodnotu `no-store`. Wenn `Location` nastaven NA hodnotu `None`:

* Für `Cache-Control` ist `no-store,no-cache` festgelegt.
* Für `Pragma` ist `no-cache` festgelegt.

Wenn `NoStore` ist `false` und `Location` ist `None`, `Cache-Control` und `Pragma` festgelegt `no-cache`.

Legen Sie Sie in der Regel `NoStore` zu `true` auf Fehler. Zum Beispiel:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet1&highlight=1)]

::: moniker-end

Dies ergibt die folgenden Header:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Position und Dauer

Zum Aktivieren der Zwischenspeicherung, `Duration` muss auf einen positiven Wert festgelegt werden und `Location` muss `Any` (Standard) oder `Client`. In diesem Fall die `Cache-Control` Header wird festgelegt, um den Speicherort angeben, gefolgt von der `max-age` der Antwort.

> [!NOTE]
> `Location`die Optionen der `Any` und `Client` übersetzen in `Cache-Control` Headerwerte `public` und `private`bzw. Wie bereits erwähnt, festlegen `Location` zu `None` legt sowohl `Cache-Control` und `Pragma` Header `no-cache`.

Im folgenden wird ein Beispiel für die Header erstellt, durch Festlegen von `Duration` und verlassen die Standardeinstellung `Location` Wert:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_duration&highlight=1)]

::: moniker-end

Dies erzeugt die folgende Kopfzeile:

```
Cache-Control: public,max-age=60
```

### <a name="cache-profiles"></a>Cacheprofile

Anstelle von duplizieren `ResponseCache` auf viele Aktion Attributen für Controller, Cacheprofilen konfiguriert werden können als Optionen beim Einrichten von MVC in die `ConfigureServices` -Methode in der `Startup`. In einer referenzierten Cacheprofil, gefundenen Werte werden verwendet, die standardmäßig von der `ResponseCache` Attribut, und werden überschrieben, nach beliebigen Eigenschaften des Attributs angegeben.

Das Einrichten eines Cacheprofils:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Startup.cs?name=snippet1)]

::: moniker-end

Verweisen auf ein Cacheprofil aus:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](response/samples/1.x/ResponseCacheSample/Controllers/HomeController.cs?name=snippet_controller&highlight=1,4)]

::: moniker-end

Die `ResponseCache` sowohl Aktionen (Methoden) und Controller (Klassen) können Attribute angewendet werden. Auf Methodenebene Attribute überschreiben die Einstellungen, die in den auf Klassenebene Attribute angegeben.

Im obigen Beispiel gibt ein Attributs auf Klassenebene eine Dauer von 30 Sekunden, während ein Attributs auf Methodenebene. ein Cacheprofil mit einer Dauer auf 60 Sekunden festgelegt verweist.

Die resultierende Header:

```
Cache-Control: public,max-age=60
```

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Das Speichern von Antworten in Caches](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
