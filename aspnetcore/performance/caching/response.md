---
title: Zwischenspeichern von Antworten in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie die Zwischenspeicherung von Antworten verwenden können, um die Bandbreitenanforderungen zu senken und die Leistung von ASP.NET Core-Apps zu steigern.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/04/2019
uid: performance/caching/response
ms.openlocfilehash: ab5d1414ae72edade81ab55aef6b0fa5af30f0f4
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2020
ms.locfileid: "76971977"
---
# <a name="response-caching-in-aspnet-core"></a>Zwischenspeichern von Antworten in ASP.NET Core

Durch [John Luo](https://github.com/JunTaoLuo), [Rick Anderson](https://twitter.com/RickAndMSFT), [Steve Smith](https://ardalis.com/), und [Luke Latham](https://github.com/guardrex)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Zwischenspeichern von Antworten reduziert die Anzahl der Anforderungen, die ein Client oder Proxy an einen Webserver sendet. Zwischenspeichern von Antworten auch verringert die Menge der Arbeit der Webserver ausgeführt werden, um eine Antwort zu generieren. Zwischenspeichern von Antworten wird durch Header gesteuert werden, die angeben, wie Sie Client und Proxy-Middleware zum Zwischenspeichern von Antworten soll.

Das [responsecache-Attribut](#responsecache-attribute) nimmt an der Einstellung von Cache Headern für Antworten Teil. Clients und zwischen Proxys sollten die Header zum Zwischenspeichern von Antworten unter der [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234)berücksichtigen.

Verwenden Sie für die serverseitige Zwischenspeicherung, die der HTTP 1,1-Cache Spezifikation folgt, die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware). Die Middleware kann die <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> Eigenschaften verwenden, um das Verhalten der serverseitigen Zwischenspeicherung zu beeinflussen.

## <a name="http-based-response-caching"></a>Zwischenspeichern von Antworten HTTP-basierte

Die [Zwischenspeichern von HTTP 1.1-Spezifikation](https://tools.ietf.org/html/rfc7234) beschrieben, wie internetcaches Verhalten soll. Der primäre HTTP-Header für das caching verwendet [Cache-Control](https://tools.ietf.org/html/rfc7234#section-5.2), dient zum Angeben der Cache *Direktiven*. Die Direktiven steuern das zwischen Speicherungs Verhalten, da Anforderungen von Clients auf Server und Antworten von Servern an Clients zurückgeben. Verschieben von Anforderungen und Antworten über Proxyserver und webanwendungsproxy-Server müssen auch der Zwischenspeicherung von HTTP 1.1-Spezifikation entsprechen.

Allgemeine `Cache-Control` Direktiven werden in der folgenden Tabelle angezeigt.

| Directive                                                       | -Aktion |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | Ein Cache kann die Antwort speichern. |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | Die Antwort muss nicht von einem freigegebenen Cache gespeichert werden. Ein privater Cache möglicherweise speichern und Wiederverwenden von der Antwort. |
| [Max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | Der Client akzeptiert keine Antwort, deren Alter größer ist als die angegebene Anzahl von Sekunden. Beispiele: `max-age=60` (60 Sekunden), `max-age=2592000` (1 Monat) |
| [ohne-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **Für Anforderungen**: ein Cache gespeicherte Antwort zum Erfüllen der Anforderung muss nicht verwendet werden. Der Ursprungsserver generiert die Antwort für den Client erneut, und die Middleware aktualisiert die gespeicherte Antwort im Cache.<br><br>**Für Antworten**: die Antwort darf nicht für eine nachfolgende Anforderung ohne Überprüfung auf dem Ursprungsserver verwendet werden. |
| [ohne-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **Für Anforderungen**: ein Cache muss die Anforderung nicht speichern.<br><br>**Für Antworten**: ein Cache muss einen beliebigen Teil der Antwort nicht speichern. |

Andere Cacheheader, die Zwischenspeichern eine Rolle spielen, werden in der folgenden Tabelle angezeigt.

| Header                                                     | Funktion |
| ---------------------------------------------------------- | -------- |
| [ALTER](https://tools.ietf.org/html/rfc7234#section-5.1)     | Eine Schätzung der die Zeitspanne in Sekunden seit der Antwort generiert oder erfolgreich auf dem Ursprungsserver überprüft wurde. |
| [Läuft ab](https://tools.ietf.org/html/rfc7234#section-5.3) | Die Zeit, nach der die Antwort als veraltet eingestuft wird. |
| [Pragma](https://tools.ietf.org/html/rfc7234#section-5.4)  | Vorhanden ist, für die Abwärtskompatibilität mit HTTP/1.0 für die Einstellung speichert `no-cache` Verhalten. Wenn die `Cache-Control` Header vorhanden ist, ist die `Pragma` Header wird ignoriert. |
| [Variieren](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | Gibt an, dass eine zwischengespeicherte Antwort nicht, wenn alle gesendet werden muss von der `Vary` Headerfelder in der zwischengespeicherten Antwort zur ursprünglichen Anforderung und die neue Anforderung zu entsprechen. |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>Zwischenspeichern Hinsicht HTTP-basierte Anforderung cachesteuerungsdirektiven

Die [Zwischenspeichern von HTTP 1.1-Spezifikation für den Cache-Control-Header](https://tools.ietf.org/html/rfc7234#section-5.2) erfordert einen Cache, der einen gültigen berücksichtigt `Cache-Control` Header, die vom Client gesendet werden. Ein Client kann Anforderungen mit stellen eine `no-cache` Headerwert und erzwingen Sie den Server aus, um eine neue Antwort für jede Anforderung zu generieren.

Berücksichtigt immer Client `Cache-Control` Anforderungsheader ist sinnvoll, wenn Sie das Ziel der HTTP-Zwischenspeicherung in Betracht ziehen. In der offiziellen Spezifikation Zwischenspeicherung dient zum Reduzieren des Latenz und Aufwand der Erfüllung der Anforderungen in einem Netzwerk von Clients, Proxys und Servern. Es ist nicht unbedingt eine Möglichkeit zum Steuern der Last auf einem Ursprungsserver.

Es gibt keine Entwickler Kontrolle über dieses zwischen Speicherungs Verhalten, wenn die [Middleware](xref:performance/caching/middleware) zum Zwischenspeichern von Antworten verwendet wird, da die Middleware die offizielle cachingspezifikation befolgt. [Geplante Verbesserungen an der Middleware](https://github.com/dotnet/AspNetCore/issues/2612) sind die Möglichkeit, die Middleware so zu konfigurieren, dass Sie den `Cache-Control`-Header einer Anforderung ignoriert, wenn Sie sich für eine zwischengespeicherte Antwort entscheiden. Geplante Erweiterungen bieten die Möglichkeit, die Serverlast besser zu steuern.

## <a name="other-caching-technology-in-aspnet-core"></a>Andere cachetechnologie in ASP.NET Core

### <a name="in-memory-caching"></a>Zwischenspeicherung im Arbeitsspeicher

Zwischenspeicherung im Arbeitsspeicher verwendet Server-Speicher zum Speichern von zwischengespeicherter Daten. Diese Form des Cachings eignet sich für einen einzelnen oder mehrerer Server mithilfe von *persistente Sitzungen*. Persistente Sitzungen bedeutet, dass die Anforderungen von einem Client immer auf dem gleichen Server zur Verarbeitung weitergeleitet werden.

Weitere Informationen finden Sie unter <xref:performance/caching/memory>.

### <a name="distributed-cache"></a>Verteilter Cache

Verwenden Sie einen verteilten Cache zum Speichern von Daten im Arbeitsspeicher, wenn die app in einer Cloud oder Server-Farm gehostet wird. Der Cache wird auf allen Servern gemeinsam genutzt, die Anforderungen verarbeiten. Ein Client kann eine Anforderung übermitteln, die von einem beliebigen Server in der Gruppe verarbeitet wird, wenn zwischengespeicherte Daten für den Client verfügbar sind. ASP.net Core funktioniert mit verteilten Caches SQL Server, [redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)und [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) .

Weitere Informationen finden Sie unter <xref:performance/caching/distributed>.

### <a name="cache-tag-helper"></a>Cache-Taghilfsprogramm

Zwischenspeichern des Inhalts aus einer MVC-Ansicht oder-Razor Page mit dem cachetaghilfsprogramm. Das Cache-Taghilfsprogramm verwendet speicherinternes caching, um Daten zu speichern.

Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>.

### <a name="distributed-cache-tag-helper"></a>Taghilfsprogramm für verteilten Cache

Speichern Sie den Inhalt aus einer MVC-Ansicht oder einer Razor page in verteilten Cloud-oder Webfarm Szenarios mit dem taghilfsprogramm für verteilte Caches. Das taghilfsprogramm für verteilte Caches verwendet SQL Server, [redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)oder [NCache](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/) zum Speichern von Daten.

Weitere Informationen finden Sie unter <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>.

## <a name="responsecache-attribute"></a>ResponseCache-Attribut

Der <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> gibt die Parameter an, die zum Festlegen geeigneter Header beim Zwischenspeichern von Antworten erforderlich sind.

> [!WARNING]
> Deaktivieren Sie die Zwischenspeicherung für Inhalte, die Informationen für authentifizierte Clients enthält. Zwischenspeichern von sollte nur aktiviert werden, für Inhalte, die sich nicht ändert, die anhand eines Benutzers Identität oder, ob ein Benutzer angemeldet ist.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> variiert die gespeicherte Antwort anhand der Werte der angegebenen Liste von Abfrage Schlüsseln. Wenn ein einzelner Wert des `*` angegeben wird, hängt von die Middleware Antworten von allen anfordern, Abfragezeichenfolgen-Parameter.

Die [Middleware zum Zwischenspeichern](xref:performance/caching/middleware) muss aktiviert sein, um die <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>-Eigenschaft festzulegen. Andernfalls wird eine Lauf Zeit Ausnahme ausgelöst. Es gibt keine entsprechenden HTTP-Header für die <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> Eigenschaft. Die-Eigenschaft ist eine HTTP-Funktion, die von der zwischenware zum Zwischenspeichern von Antworten Für die Middleware, um eine zwischengespeicherte Antwort zu verarbeiten müssen der Abfragezeichenfolge und den Wert der Abfragezeichenfolge eine vorherige Anforderung übereinstimmen. Betrachten Sie beispielsweise die Reihenfolge der Anforderungen und Ergebnissen, die in der folgenden Tabelle gezeigt.

| Anforderung                          | Ergebnis                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | Vom Server zurückgegeben. |
| `http://example.com?key1=value1` | Von der Middleware zurückgegeben. |
| `http://example.com?key1=value2` | Vom Server zurückgegeben. |

Die erste Anforderung wird vom Server zurückgegebenen und im Middleware zwischengespeichert. Die zweite Anforderung wird von Middleware zurückgegeben, da die Abfragezeichenfolge die vorherige Anforderung übereinstimmt. Die dritte Anforderung ist nicht im Cache Middleware, dass Abfragezeichenfolgen-Werts nicht mit eine vorherige Anforderung übereinstimmt.

Der <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> wird verwendet, um eine `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`zu konfigurieren und zu erstellen (über <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>). Die `ResponseCacheFilter` die Arbeit der Aktualisierung des entsprechenden HTTP-Header und Funktionen der Antwort durchführt. Der Filter:

* Entfernt alle vorhandenen Header für `Vary`, `Cache-Control`, und `Pragma`.
* Schreibt die entsprechenden Header auf Grundlage der Eigenschaften legen Sie in der <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>.
* Aktualisiert die Antwort Zwischenspeichern von HTTP-Funktion, wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> festgelegt ist.

### <a name="vary"></a>Variieren

Dieser Header wird nur geschrieben, wenn die <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> festgelegt wird. Die Eigenschaft, die auf den Wert der `Vary` Eigenschaft festgelegt ist. Das folgende Beispiel verwendet die <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> Eigenschaft:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

Verwenden Sie die Beispiel-APP, um die Antwortheader mit den Netzwerk Tools des Browsers anzuzeigen. Die folgenden Antwortheader werden mit der Cache1-Seiten Antwort gesendet:

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore und Location.None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> überschreibt die meisten anderen Eigenschaften. Wenn diese Eigenschaft auf festgelegt ist `true`, `Cache-Control` Header nastaven NA hodnotu `no-store`. Wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> nastaven NA hodnotu `None`:

* Für `Cache-Control` ist `no-store,no-cache` festgelegt.
* Für `Pragma` ist `no-cache` festgelegt.

Wenn <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> `false` und <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> `None`ist, `Cache-Control`und `Pragma` auf `no-cache`festgelegt sind.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> wird in der Regel auf `true` für Fehlerseiten festgelegt. Die Cache2-Seite in der Beispiel-app erzeugt Antwortheader, die den Client anweisen, die Antwort nicht zu speichern.

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

Die Beispiel-App gibt die Cache2-Seite mit den folgenden Headern zurück:

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>Position und Dauer

Zum Aktivieren der Zwischenspeicherung, <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> muss auf einen positiven Wert festgelegt werden und <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> muss `Any` (Standard) oder `Client`. Das Framework legt den `Cache-Control`-Header auf den Speicherort Wert fest, gefolgt vom `max-age` der Antwort.

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>die Optionen der `Any` und `Client` übersetzen in `Cache-Control` Headerwerte `public` und `private`bzw. Wie im Abschnitt [NoStore und Location. None](#nostore-and-locationnone) vermerkt, werden beim Festlegen von <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> auf `None` sowohl `Cache-Control`-als auch `Pragma`-Header auf `no-cache`festgelegt.

`Location.Any` (`Cache-Control` auf `public`festgelegt) gibt an, dass der Wert des *Clients oder eines beliebigen zwischen Proxys zwischen* gespeichert werden kann, einschließlich der Zwischenspeicherung von zwischen [Speichern](xref:performance/caching/middleware)

`Location.Client` (`Cache-Control` auf `private`festgelegt) gibt an, dass der Wert *nur vom Client* zwischengespeichert werden darf. Der Wert kann nicht zwischengespeichert werden, einschließlich der [Middleware](xref:performance/caching/middleware)zum Zwischenspeichern von Antworten.

Cache Steuerungs Header bieten lediglich Anleitungen für Clients und Vermittler Proxys, wann und wie Antworten zwischengespeichert werden. Es gibt keine Garantie dafür, dass Clients und Proxys die [http 1,1-cachingspezifikation](https://tools.ietf.org/html/rfc7234)berücksichtigen. Die [Middleware zum Zwischenspeichern von Antworten](xref:performance/caching/middleware) folgt immer den durch die Spezifikation vorgegebenen Cache Regeln.

Das folgende Beispiel zeigt das Cache3 Page Model aus der Beispiel-APP und die durch Festlegen von <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> erstellten Header und die standardmäßige <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> Werts:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

Die Beispiel-App gibt die Cache3-Seite mit dem folgenden Header zurück:

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>Cacheprofile

Anstatt die Einstellungen für den Antwort Cache für viele Controller Aktions Attribute zu duplizieren, können Cache Profile beim Einrichten von MVC/Razor pages in `Startup.ConfigureServices`als Optionen konfiguriert werden. Werte, die in einem Cache Profil gefunden werden, auf das verwiesen wird, werden vom <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> als Standardwerte verwendet und von allen Eigenschaften überschrieben, die für das Attribut angegeben werden.

Einrichten eines Cache Profils. Das folgende Beispiel zeigt ein Cache Profil mit 30 Sekunden im `Startup.ConfigureServices`der Beispiel-App:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

Das Cache4-Seiten Modell der Beispiel-App verweist auf das `Default30` Cache Profil:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

Der <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> kann angewendet werden auf:

* Razor Page Handler (Klassen) &ndash; Attribute können nicht auf Handlermethoden angewendet werden.
* MVC-Controller (Klassen).
* MVC-Aktionen (Methoden) &ndash; Attribute auf Methoden Ebene überschreiben die in Attributen auf Klassenebene angegebenen Einstellungen.

Der resultierende Header, der für die Antwort auf die Cache4-Seite vom `Default30` Cache Profil übernommen wird:

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>Weitere Ressourcen

* [Das Speichern von Antworten in Caches](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
