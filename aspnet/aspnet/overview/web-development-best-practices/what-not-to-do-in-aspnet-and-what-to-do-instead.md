---
uid: aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
title: Vorgehensweise nicht in ASP.NET und empfohlene Vorgehensweisen | Microsoft-Dokumentation
author: Rick-Anderson
description: Dieses Thema beschreibt einige häufige Fehler, die Personen innerhalb von ASP.NET-Webprojekten vornehmen. Hier finden Sie Empfehlungen für was Sie tun sollten, um diese gemeinsame zu vermeiden...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: c39b9965-545c-4b04-8f55-21be7f28a9e5
msc.legacyurl: /aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
msc.type: authoredcontent
ms.openlocfilehash: 512d2e2b39467635390fa175546f79d8c9f89f4a
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667712"
---
# <a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a>Häufige Fehler bei ASP.NET und empfohlene Vorgehensweisen

> Dieses Thema beschreibt einige häufige Fehler, die Personen innerhalb von ASP.NET-Webprojekten vornehmen. Hier finden Sie Empfehlungen für was Sie tun sollten, um diese häufige Fehler zu vermeiden. Es basiert auf einer [Präsentation](http://vimeo.com/68390507) von **Damian Edwards** auf Norwegian Developers Conference.


## <a name="disclaimer"></a>Haftungsausschluss

In diesem Thema wird nicht als eine vollständige Anleitung sollen sicherstellen, dass Ihre Anwendung sicher und effizient ist. Sie müssen weiterhin bewährte Methoden für Sicherheit und Leistung befolgen, die nicht in diesem Thema beschrieben werden. Es schlägt nur vor, wie Sie häufige Fehler im Zusammenhang mit .NET-Klassen und Prozesse zu vermeiden.

## <a name="overview"></a>Übersicht

Dieses Thema enthält folgende Abschnitte:

- [Einhaltung von Standards](#standards)

    - [Steuerelementadapter](#adapters)
    - [Style-Eigenschaften für Steuerelemente](#styleprop)
    - [Seite "und" Control-Rückrufe](#callback)
    - [Erkennung des Webbrowsers-Funktion](#browsercap)
- [Sicherheit](#security)

    - [Request-Überprüfung](#validation)
    - [Formularauthentifizierung und Sitzung](#cookieless)
    - [EnableViewStateMac](#viewstatemac)
    - [Mittlere Vertrauenswürdigkeit](#medium)
    - [&lt;appSettings&gt;](#appsettings)
    - [UrlPathEncode](#urlpathencode)
- [Zuverlässigkeit und Leistung](#performance)

    - [PreSendRequestHeaders und PreSendRequestContent](#presend)
    - [Asynchrone Seitenereignisse mit Web Forms](#asyncevents)
    - [Fire-and-Forget-Aufgaben](#fire)
    - [Anforderungs-Einheitstextkörpers](#requestentity)
    - [Response.Redirect und Response.End](#redirect)
    - [EnableViewState und ViewStateMode](#viewstatemode)
    - [SqlMembershipProvider](#sqlprovider)
    - [Lange ausgeführten Anforderungen (> 110 Sekunden)](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a>Einhaltung von Standards

<a id="adapters"></a>

### <a name="control-adapters"></a>Steuerelementadapter

Empfehlung: Beenden Sie der Verwendung von Steuerelementadapter für adaptive Rendering, und verwenden Sie die CSS-Medienabfragen und standardkonforme HTML.

Steuerelemente Adapter wurden in .NET 2.0 Darstellungscode gerendert, die für verschiedene Geräte und Umgebungen angepasst wurde eingeführt. Jetzt kann dieses adaptive Rendering mit CSS und HTML-erreicht werden. Sie sollten nicht mehr verwenden Steuerelementadapter und konvertieren alle vorhandenen Adapter, CSS und HTML.

Weitere Informationen finden Sie unter [Medienabfragen](http://www.w3.org/TR/css3-mediaqueries/) und [so wird's gemacht: Hinzufügen mobiler Seiten zu Ihren ASP.NET Web Forms-/ MVC-Anwendung](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a>Style-Eigenschaften für Steuerelemente

Empfehlung: Beenden Sie die Einstellungswerte in Markup des Steuerelements, und stattdessen festlegen Sie Formatierungswerte in CSS-Stylesheets.

Webserver-Steuerelemente enthalten Dutzende von Eigenschaften, die mit dem Inline-Style-Eigenschaften festgelegt werden können. Beispielsweise legt die ForeColor-Eigenschaft, die Farbe des Texts für ein Steuerelement. Sie können diesen Effekt über CSS-Stylesheets effizienter ausführen. Stylesheets können Sie Stilwerte zentralisieren und zu vermeiden, diese Werte in der gesamten Anwendung festlegen.

Das folgende Beispiel zeigt einer CSS-Klasse den Text der Sätze in Rot.

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

Das nächste Beispiel zeigt, wie Sie die CSS-Klasse dynamisch anwenden.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a>Seiten- und Rückrufe

Empfehlung: Beenden Sie verwenden von Rückrufen für Seiten und-Steuerelementen zu, und verwenden Sie stattdessen eine der folgenden: AJAX, UpdatePanel, MVC Aktionsmethoden, Web-API und SignalR.

In früheren Versionen von ASP.NET aktiviert Seiten- und Steuerelementausgabe Rückrufmethoden Sie Teil der Webseite zu aktualisieren, ohne dass eine gesamte Seite aktualisiert. Erreichen Sie Partial-Page-Updates bis jetzt [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [Web-API-](../../../web-api/index.md) oder [SignalR](../../../signalr/index.md). Sie müssen die Verwendung von Rückrufmethoden, weil sie Probleme mit freundlichen URLs verursachen können, und routing beenden. Standardmäßig Steuerelemente Rückrufmethoden nicht aktivieren, aber wenn Sie diese Funktion in einem Steuerelement aktiviert haben, sollten Sie ihn deaktivieren.

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a>Erkennung des Webbrowsers-Funktion

Empfehlung: Beenden Sie der Verwendung von statischen Funktion Browsererkennung und verwenden Sie die Ermittlung von dynamischen Features.

In früheren Versionen von ASP.NET wurden die unterstützten Funktionen für jeden Browser in einer XML-Datei gespeichert. Erkennen von Feature-Unterstützung über die statische Suche ist nicht der beste Ansatz. Nun, Sie können dynamisch erkennen ein Browser unterstützten Funktionen des mithilfe einer Funktion zur Erkennung-Framework, wie z. B. [Modernizr](http://modernizr.com/). Ermittlung von Features bestimmt die Unterstützung von versuchen, eine Methode oder Eigenschaft zu verwenden, und klicken Sie dann überprüfen, um festzustellen, ob der Browser das gewünschte Ergebnis erzeugt. Standardmäßig ist Modernizr in die Web Application-Vorlagen enthalten.

<a id="security"></a>

## <a name="security"></a>Sicherheit

<a id="validation"></a>

### <a name="request-validation"></a>Request-Überprüfung

Empfehlung: Validieren von Benutzereingaben und Ausgabe von Benutzern zu codieren.

Anforderungsvalidierung ist ein Feature von ASP.NET an, die jeder Anforderung untersucht und die Anforderung beendet, wenn eine erkannte Bedrohung gefunden wurde. Hängen Sie nicht von Anforderungsvalidierung für Ihre Anwendung vor Cross-Site scripting-Angriffe zu schützen. Stattdessen überprüfen Sie aller Eingaben von Benutzern und codieren Sie die Ausgabe zu. Klicken Sie in manchen Fällen können Sie reguläre Ausdrücke zum Überprüfen der Eingabe, jedoch in komplexen Fällen, die Sie überprüfen sollten Benutzereingaben mithilfe von Klassen in .NET, die zu bestimmen, ob der Wert entspricht Werte zulässig.

Das folgende Beispiel zeigt, wie Sie eine statische Methode in der Uri-Klasse verwenden, um zu bestimmen, ob der Uri von einem Benutzer gültig ist.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

Jedoch um den Uri ausreichend zu überprüfen, Sie sollten auch prüfen, stellen Sie sicher, es gibt `http` oder `https`. Im folgenden Beispiel wird Instanzmethoden zur Verfügung, um sicherzustellen, dass der Uri gültig ist.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

Codieren Sie, bevor Sie Benutzereingaben als HTML-rendering oder einschließlich der Benutzereingabe in einer SQL-Abfrage, die Werte, um sicherzustellen, dass bösartiger Code nicht enthalten ist.

Können Sie HTML-Codierung für den Wert im Markup mit der &lt;%: %&gt; Syntax, wie unten dargestellt.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

In Razor-Syntax können Sie HTML codieren mit @, wie unten dargestellt.

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

Das folgende Beispiel zeigt eine Codierung wie in HTML einen Wert im Code-Behind.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

Um einen Wert für die SQL-Befehlen sicher zu codieren, verwenden Sie Parameter des Befehls wie z. B. die ["SqlParameter"](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx). <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a>Formularauthentifizierung und Sitzung

Empfehlung: Erfordern Sie Cookies.

Übergeben von Informationen für die Authentifizierung in der Abfragezeichenfolge ist nicht sicher. Erfordern Sie daher Cookies auf, wenn Ihre Anwendung Authentifizierung enthält. Wenn Ihr Cookie vertraulichen Informationen gespeichert werden, sollten Sie die Erzwingung von SSL für das Cookie.

Das folgende Beispiel zeigt, wie Sie in der Datei "Web.config" angeben, dass die Formularauthentifizierung einen Cookie erforderlich ist, der über SSL übertragen werden.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a>EnableViewStateMac

Empfehlung: Nie auf "false" festgelegt.

In der Standardeinstellung EnbableViewStateMac festgelegt ist auf "true". Auch wenn Ihre Anwendung nicht Ansichtszustand verwendet wird, legen Sie EnableViewStateMac nicht auf "false". Dieser Wert auf "false" festlegen, wird die Anwendung anfällig für Cross-Site-scripting machen.

ASP.NET 4.5.2 ab, die Laufzeit erzwingt **EnableViewStateMac = True**. Auch wenn Sie es auf "false" festlegen, wird die Laufzeit ignoriert diesen Wert und wird mit dem Wert "true". Weitere Informationen finden Sie unter [ASP.NET 4.5.2 und EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).

Das folgende Beispiel zeigt, wie Sie EnableViewStateMac auf "true" festlegen. Sie müssen nicht tatsächlich legen Sie diesen Wert auf "true", da sie standardmäßig "true" ist. Wenn Sie es auf "false" auf einer beliebigen Seite in Ihrer Anwendung festgelegt haben, müssen Sie diesen Wert sofort beheben.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a>Mittlere Vertrauenswürdigkeit

Empfehlung: Hängen Sie nicht von mittlere Vertrauensebene (oder einer anderen Ebene der Vertrauenswürdigkeit) als Sicherheitsgrenze.

Teilweiser Vertrauenswürdigkeit schützt Ihre Anwendung nicht ordnungsgemäß und sollte nicht verwendet werden. Verwenden Sie stattdessen voll vertrauenswürdig, und nicht vertrauenswürdige Anwendungen in separaten Anwendungspools zu isolieren. Führen Sie außerdem jeder Anwendungspool unter einer eindeutigen Identität. Weitere Informationen finden Sie unter [ASP.NET teilweiser Vertrauenswürdigkeit garantiert keine Anwendungsisolation](https://support.microsoft.com/kb/2698981).

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a>&lt;appSettings&gt;

Empfehlung: Deaktivieren Sie Sicherheitseinstellungen in nicht &lt;"appSettings"&gt; Element.

Das Element "appSettings" enthält viele Werte, die für die Sicherheitsupdates erforderlich sind. Sie sollten nicht ändern oder deaktivieren diese Werte. Wenn Sie diese Werte beim Bereitstellen eines Updates deaktivieren müssen, sofort wieder aktivieren Sie nach Abschluss der Bereitstellung.

Weitere Informationen finden Sie unter [ASP.NET AppSettings-Element](https://msdn.microsoft.com/library/hh975440.aspx).

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a>UrlPathEncode

Empfehlung: Verwendung [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) stattdessen.

.NET Framework, um eine ganz bestimmte Browser Problems "Anwendungskompatibilität" aufgelöst wurde die UrlPathEncode-Methode hinzugefügt. Er nicht ordnungsgemäß codiert eine URL und die Anwendung nicht vor Cross-Site-scripting schützt. Sie sollten nie in Ihrer Anwendung verwenden. Verwenden Sie stattdessen [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).

Das folgende Beispiel zeigt, wie eine codierte URL als ein Abfragezeichenfolgen-Parameter für ein Hyperlink-Steuerelement zu übergeben.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a>Zuverlässigkeit und Leistung

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a>PreSendRequestHeaders und PreSendRequestContent

Empfehlung: Verwenden Sie diese Ereignisse nicht mit verwalteten Module aus. Schreiben Sie stattdessen ein natives IIS-Modul, um die erforderliche Aufgabe ausführen. Finden Sie unter [Erstellen von systemeigenem Code HTTP-Modulen](https://msdn.microsoft.com/library/ms693629.aspx).

Sie können die [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) und [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) Ereignisse mit systemeigenen IIS-Modulen.
> [!WARNING]
> Verwenden Sie keine `PreSendRequestHeaders` und `PreSendRequestContent` mit verwalteten Modulen, die implementieren `IHttpModule`. Das Festlegen dieser Eigenschaften kann Probleme bei asynchronen Anforderungen verursachen. Die Kombination von der Anwendung angeforderte Routing (ARR) und Websockets kann zu Zugriffsverletzungsausnahmen führen, die w3wp zu einem Absturz führen können. Z. B. Iiscore! W3_CONTEXT_BASE::GetIsLastNotification + 68 in iiscore.dll hat eine Access-Verletzung-Ausnahme (0xC0000005) verursacht.

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a>Asynchrone Seitenereignisse mit Web forms

Empfehlung: In Web Forms zu vermeiden, Schreiben Async-void-Methoden für den Seitenlebenszyklus-Ereignisse und stattdessen [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) für asynchronen Code.

Wenn Sie ein Seitenereignis mit markieren **Async** und **"void"**, Sie können nicht bestimmen, wann der asynchrone Code beendet wurde. Verwenden Sie stattdessen Page.RegisterAsyncTask, um den asynchronen Code so ausführen, die Sie zu ihrem Abschluss verfolgen können.

Das folgende Beispiel zeigt eine Schaltfläche klicken Sie auf Handler, der asynchrone Code enthält. Dieses Beispiel enthält Lesen eines Zeichenfolgenwerts asynchron, die nur als ein vereinfachtes Beispiel einer asynchronen Aufgabe und nicht als empfohlene Vorgehensweise bereitgestellt wird.

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

Bei Verwendung asynchrone Aufgaben, legen Sie das Zielframework des Http-Laufzeit auf 4.5 (oder höher) in der Datei "Web.config". Durch Festlegen des Zielframeworks auf 4.5 auf dem neuen Synchronisierungskontext an, die wurde in .NET 4.5 hinzugefügt. Dieser Wert ist standardmäßig in neuen Projekten in Visual Studio festgelegt, aber es ist nicht festgelegt werden, wenn Sie ein vorhandenes Projekt arbeiten.

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a>Fire-and-forget-Aufgaben

Empfehlung: Bei der Behandlung einer Anforderung in ASP.NET zu vermeiden, Starten von Fire-and-forget-Arbeit (solche Aufrufen der Methode ThreadPool.QueueUserWorkItem oder erstellen einen Timer, der wiederholt einen Delegaten aufruft).

Wenn Ihre Anwendung Fire-and-forget-Aufgaben, die in ASP.NET ausgeführt wird, erhalten Ihre Anwendung nicht mehr synchron. Zu jedem Zeitpunkt kann die Anwendungsdomäne zerstört werden dies bedeutet, dass es sich bei Ihrem laufende Prozess den aktuellen Zustand der Anwendung möglicherweise nicht mehr überein.

Sie sollten diese Art von Arbeit außerhalb von ASP.NET verschieben. Sie können verwenden Sie einen Webauftrag, der Windows-Dienst oder eine workerrolle in Azure derzeit ausgeführte Arbeit ausführen, und führen Sie diesen Code aus einem anderen Prozess.

Wenn Sie diese Arbeit in ASP.NET ausführen müssen, können Sie die Nuget-Paket namens hinzufügen [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) um den Code auszuführen.

<a id="requestentity"></a>

### <a name="request-entity-body"></a>Anforderungs-einheitstextkörpers

Empfehlung: Lesen Request.Form oder Request.InputStream, vor dem Ausführen des Handlers des Ereignisses zu vermeiden.

Das früheste von Request.Form oder Request.InputStream gelesenen sollte ausführen während des Handlers Ereignis. In MVC wird der Controller ist der Handler, und das Execute-Ereignis ist, wenn die Aktionsmethode ausgeführt wird. In Web Forms die Seite wird der Handler, und das Execute-Ereignis ist, wenn das Page.Init-Ereignis auslöst. Wenn Sie die Anforderungs-einheitstextkörper vor der Execute-Ereignis lesen, stören Sie bei der Verarbeitung der Anforderung.

Wenn Sie die Anforderungs-einheitstextkörper vor der Execute-Ereignis finden müssen, verwenden Sie entweder [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) oder [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx). Bei Verwendung von GetBufferlessInputStream Sie den unformatierten Stream abrufen, aus der Anforderung und verantwortlich für die gesamte Anforderung zu verarbeiten. Nach dem Aufruf GetBufferlessInputStream, sind Request.Form und Request.InputStream nicht verfügbar, da sie von ASP.NET nicht aufgefüllt wurden. Wenn Sie GetBufferedInputStream verwenden, erhalten Sie eine Kopie des Datenstroms aus der Anforderung. Request.Form und Request.InputStream sind weiter unten in der Anforderung weiterhin verfügbar, da ASP.NET die andere Kopie füllt.

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a>Response.Redirect und Response.End

Empfehlung: Achten Sie Unterschiede in der Behandlung der Thread nach dem Aufruf [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).

Die [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) Methode ruft die Response.End-Methode. Innerhalb eines synchronen Prozesses durch den Aufruf von Request.Redirect der aktuelle Thread sofort abgebrochen. Allerdings in einem asynchronen Prozess Aufruf von Response.Redirect nicht den aktuellen Thread abgebrochen wird, damit die codeausführung für die Anforderung wird fortgesetzt. In einem asynchronen Prozess ist müssen Sie die Aufgabe von der Methode zum Beenden der Ausführung des Codes zurückgeben.

In einem MVC-Projekt dürfen Sie nicht Response.Redirect aufrufen. Geben Sie stattdessen ein RedirectResult zurück.

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a>EnableViewState und ViewStateMode

Empfehlung: Können Sie ViewStateMode, anstatt EnableViewState, geben Sie eine präzise Kontrolle über die Steuerelemente den Ansichtszustand verwenden.

Wenn Sie auf "false", in der Page-Direktive EnableViewState festlegen, werden Ansichtszustand ist für alle Steuerelemente auf der Seite deaktiviert und kann nicht aktiviert werden. Wenn Sie den Ansichtszustand für nur für bestimmte Steuerelemente auf der Seite zu aktivieren möchten, können Sie ViewStateMode auf deaktiviert festgelegt, für die Seite.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

Legen Sie Sie dann ViewStateMode aktiviert, auf nur die Steuerelemente, die tatsächlich Ansichtszustand benötigen.

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

Durch Aktivieren der Ansichtszustand für nur die Steuerelemente, die ihn benötigen, können Sie die Größe des Ansichtszustands für Ihre Webseiten verkleinern.

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a>SqlMembershipProvider

Empfehlung: Verwenden Sie universelle Anbieter.

In der aktuellen Projektvorlagen SqlMembershipProvider wurde ersetzt durch [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), die als NuGet-Paket verfügbar ist. Wenn Sie SqlMembershipProvider in einem Projekt verwenden, die mit einer früheren Version der Vorlagen erstellt wurde, sollten Sie für universelle Anbieter wechseln. Universal Providers funktionieren mit allen Datenbanken, die von Entity Framework unterstützt werden.

Weitere Informationen finden Sie unter [Einführung in ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a>Lang ausgeführte Anforderungen (> 110 Sekunden)

Empfehlung: Verwenden Sie [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) oder [SignalR](../../../signalr/index.md) für verbundene Clients, und verwenden asynchrone e/a-Vorgänge.

Lang ausgeführte Anforderungen kann zu unvorhersehbaren Ergebnissen und eine schlechte Leistung in Ihrer Webanwendung. Der standardmäßigen zeitlimiteinstellung für eine Anforderung ist 110 Sekunden. Bei Verwendung des Sitzungszustands mit einer lang andauernden-Anforderung wird ASP.NET die Sperre für das Sitzungsobjekt nach 110 Sekunden freigegeben. Jedoch kann die Anwendung gerade ein Vorgang für das Sitzungsobjekt sein, wenn die Sperre wird aufgehoben, und der Vorgang kann nicht erfolgreich abgeschlossen. Wenn eine zweite Anforderung des Benutzers blockiert wird, während die erste Anforderung ausgeführt wird, kann die zweite Anforderung das Sitzungsobjekt in einem inkonsistenten Zustand zugreifen.

Wenn Ihre Anwendung blockieren (oder synchrone) e/a-Vorgänge enthält, wird die Anwendung reagiert jedoch nicht.

Verwenden Sie zur Verbesserung der Leistung der asynchronen e/a-Vorgänge in .NET Framework. Außerdem verwenden Sie WebSockets oder SignalR, für die Verbindung von Clients an den Server. Diese Features dienen zum lang andauernder Anforderungen effizient zu verarbeiten.
