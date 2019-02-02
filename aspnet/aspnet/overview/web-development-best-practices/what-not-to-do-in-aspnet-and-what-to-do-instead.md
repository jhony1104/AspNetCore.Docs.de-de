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
# <a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a><span data-ttu-id="2c601-104">Häufige Fehler bei ASP.NET und empfohlene Vorgehensweisen</span><span class="sxs-lookup"><span data-stu-id="2c601-104">What not to do in ASP.NET, and what to do instead</span></span>

> <span data-ttu-id="2c601-105">Dieses Thema beschreibt einige häufige Fehler, die Personen innerhalb von ASP.NET-Webprojekten vornehmen.</span><span class="sxs-lookup"><span data-stu-id="2c601-105">This topic describes several common mistakes people make within ASP.NET web projects.</span></span> <span data-ttu-id="2c601-106">Hier finden Sie Empfehlungen für was Sie tun sollten, um diese häufige Fehler zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="2c601-106">It provides recommendations for what you should do to avoid these common mistakes.</span></span> <span data-ttu-id="2c601-107">Es basiert auf einer [Präsentation](http://vimeo.com/68390507) von **Damian Edwards** auf Norwegian Developers Conference.</span><span class="sxs-lookup"><span data-stu-id="2c601-107">It is based on a [presentation](http://vimeo.com/68390507) by **Damian Edwards** at Norwegian Developers Conference.</span></span>


## <a name="disclaimer"></a><span data-ttu-id="2c601-108">Haftungsausschluss</span><span class="sxs-lookup"><span data-stu-id="2c601-108">Disclaimer</span></span>

<span data-ttu-id="2c601-109">In diesem Thema wird nicht als eine vollständige Anleitung sollen sicherstellen, dass Ihre Anwendung sicher und effizient ist.</span><span class="sxs-lookup"><span data-stu-id="2c601-109">This topic is not intended as a complete guide to ensure your application is secure and efficient.</span></span> <span data-ttu-id="2c601-110">Sie müssen weiterhin bewährte Methoden für Sicherheit und Leistung befolgen, die nicht in diesem Thema beschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="2c601-110">You still need to follow best practices for security and performance that are not outlined in this topic.</span></span> <span data-ttu-id="2c601-111">Es schlägt nur vor, wie Sie häufige Fehler im Zusammenhang mit .NET-Klassen und Prozesse zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="2c601-111">It only suggests how to avoid common mistakes related to .NET classes and processes.</span></span>

## <a name="overview"></a><span data-ttu-id="2c601-112">Übersicht</span><span class="sxs-lookup"><span data-stu-id="2c601-112">Overview</span></span>

<span data-ttu-id="2c601-113">Dieses Thema enthält folgende Abschnitte:</span><span class="sxs-lookup"><span data-stu-id="2c601-113">This topic contains the following sections:</span></span>

- [<span data-ttu-id="2c601-114">Einhaltung von Standards</span><span class="sxs-lookup"><span data-stu-id="2c601-114">Standards Compliance</span></span>](#standards)

    - [<span data-ttu-id="2c601-115">Steuerelementadapter</span><span class="sxs-lookup"><span data-stu-id="2c601-115">Control Adapters</span></span>](#adapters)
    - [<span data-ttu-id="2c601-116">Style-Eigenschaften für Steuerelemente</span><span class="sxs-lookup"><span data-stu-id="2c601-116">Style Properties on Controls</span></span>](#styleprop)
    - [<span data-ttu-id="2c601-117">Seite "und" Control-Rückrufe</span><span class="sxs-lookup"><span data-stu-id="2c601-117">Page and Control Callbacks</span></span>](#callback)
    - [<span data-ttu-id="2c601-118">Erkennung des Webbrowsers-Funktion</span><span class="sxs-lookup"><span data-stu-id="2c601-118">Browser Capability Detection</span></span>](#browsercap)
- [<span data-ttu-id="2c601-119">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="2c601-119">Security</span></span>](#security)

    - [<span data-ttu-id="2c601-120">Request-Überprüfung</span><span class="sxs-lookup"><span data-stu-id="2c601-120">Request Validation</span></span>](#validation)
    - [<span data-ttu-id="2c601-121">Formularauthentifizierung und Sitzung</span><span class="sxs-lookup"><span data-stu-id="2c601-121">Cookieless Forms Authentication and Session</span></span>](#cookieless)
    - [<span data-ttu-id="2c601-122">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="2c601-122">EnableViewStateMac</span></span>](#viewstatemac)
    - [<span data-ttu-id="2c601-123">Mittlere Vertrauenswürdigkeit</span><span class="sxs-lookup"><span data-stu-id="2c601-123">Medium Trust</span></span>](#medium)
    - [<span data-ttu-id="2c601-124">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="2c601-124">&lt;appSettings&gt;</span></span>](#appsettings)
    - [<span data-ttu-id="2c601-125">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="2c601-125">UrlPathEncode</span></span>](#urlpathencode)
- [<span data-ttu-id="2c601-126">Zuverlässigkeit und Leistung</span><span class="sxs-lookup"><span data-stu-id="2c601-126">Reliability and Performance</span></span>](#performance)

    - [<span data-ttu-id="2c601-127">PreSendRequestHeaders und PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="2c601-127">PreSendRequestHeaders and PreSendRequestContent</span></span>](#presend)
    - [<span data-ttu-id="2c601-128">Asynchrone Seitenereignisse mit Web Forms</span><span class="sxs-lookup"><span data-stu-id="2c601-128">Asynchronous Page Events with Web Forms</span></span>](#asyncevents)
    - [<span data-ttu-id="2c601-129">Fire-and-Forget-Aufgaben</span><span class="sxs-lookup"><span data-stu-id="2c601-129">Fire-and-Forget Work</span></span>](#fire)
    - [<span data-ttu-id="2c601-130">Anforderungs-Einheitstextkörpers</span><span class="sxs-lookup"><span data-stu-id="2c601-130">Request Entity Body</span></span>](#requestentity)
    - [<span data-ttu-id="2c601-131">Response.Redirect und Response.End</span><span class="sxs-lookup"><span data-stu-id="2c601-131">Response.Redirect and Response.End</span></span>](#redirect)
    - [<span data-ttu-id="2c601-132">EnableViewState und ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="2c601-132">EnableViewState and ViewStateMode</span></span>](#viewstatemode)
    - [<span data-ttu-id="2c601-133">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="2c601-133">SqlMembershipProvider</span></span>](#sqlprovider)
    - [<span data-ttu-id="2c601-134">Lange ausgeführten Anforderungen (> 110 Sekunden)</span><span class="sxs-lookup"><span data-stu-id="2c601-134">Long Running Requests (>110 seconds)</span></span>](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a><span data-ttu-id="2c601-135">Einhaltung von Standards</span><span class="sxs-lookup"><span data-stu-id="2c601-135">Standards Compliance</span></span>

<a id="adapters"></a>

### <a name="control-adapters"></a><span data-ttu-id="2c601-136">Steuerelementadapter</span><span class="sxs-lookup"><span data-stu-id="2c601-136">Control adapters</span></span>

<span data-ttu-id="2c601-137">Empfehlung: Beenden Sie der Verwendung von Steuerelementadapter für adaptive Rendering, und verwenden Sie die CSS-Medienabfragen und standardkonforme HTML.</span><span class="sxs-lookup"><span data-stu-id="2c601-137">Recommendation: Stop using control adapters for adaptive rendering, and instead use CSS media queries and standards-compliant HTML.</span></span>

<span data-ttu-id="2c601-138">Steuerelemente Adapter wurden in .NET 2.0 Darstellungscode gerendert, die für verschiedene Geräte und Umgebungen angepasst wurde eingeführt.</span><span class="sxs-lookup"><span data-stu-id="2c601-138">Controls Adapters were introduced in .NET 2.0 to render presentation code that was customized for different devices and environments.</span></span> <span data-ttu-id="2c601-139">Jetzt kann dieses adaptive Rendering mit CSS und HTML-erreicht werden.</span><span class="sxs-lookup"><span data-stu-id="2c601-139">Now, this adaptive rendering can be accomplished with CSS and HTML.</span></span> <span data-ttu-id="2c601-140">Sie sollten nicht mehr verwenden Steuerelementadapter und konvertieren alle vorhandenen Adapter, CSS und HTML.</span><span class="sxs-lookup"><span data-stu-id="2c601-140">You should stop using Control Adapters and convert any existing adapters to CSS and HTML.</span></span>

<span data-ttu-id="2c601-141">Weitere Informationen finden Sie unter [Medienabfragen](http://www.w3.org/TR/css3-mediaqueries/) und [so wird's gemacht: Hinzufügen mobiler Seiten zu Ihren ASP.NET Web Forms-/ MVC-Anwendung](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).</span><span class="sxs-lookup"><span data-stu-id="2c601-141">For more information, see [Media Queries](http://www.w3.org/TR/css3-mediaqueries/) and [How To: Add Mobile Pages to Your ASP.NET Web Forms / MVC Application](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).</span></span>

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a><span data-ttu-id="2c601-142">Style-Eigenschaften für Steuerelemente</span><span class="sxs-lookup"><span data-stu-id="2c601-142">Style properties on controls</span></span>

<span data-ttu-id="2c601-143">Empfehlung: Beenden Sie die Einstellungswerte in Markup des Steuerelements, und stattdessen festlegen Sie Formatierungswerte in CSS-Stylesheets.</span><span class="sxs-lookup"><span data-stu-id="2c601-143">Recommendation: Stop setting style values in the control markup, and instead set formatting values in CSS stylesheets.</span></span>

<span data-ttu-id="2c601-144">Webserver-Steuerelemente enthalten Dutzende von Eigenschaften, die mit dem Inline-Style-Eigenschaften festgelegt werden können.</span><span class="sxs-lookup"><span data-stu-id="2c601-144">Web server controls contain dozens of properties which can be used to set in-line style properties.</span></span> <span data-ttu-id="2c601-145">Beispielsweise legt die ForeColor-Eigenschaft, die Farbe des Texts für ein Steuerelement.</span><span class="sxs-lookup"><span data-stu-id="2c601-145">For example, the ForeColor property sets the color of the text for a control.</span></span> <span data-ttu-id="2c601-146">Sie können diesen Effekt über CSS-Stylesheets effizienter ausführen.</span><span class="sxs-lookup"><span data-stu-id="2c601-146">You can accomplish this same effect more efficiently through CSS stylesheets.</span></span> <span data-ttu-id="2c601-147">Stylesheets können Sie Stilwerte zentralisieren und zu vermeiden, diese Werte in der gesamten Anwendung festlegen.</span><span class="sxs-lookup"><span data-stu-id="2c601-147">Stylesheets enable you to centralize style values and avoid setting these values throughout your application.</span></span>

<span data-ttu-id="2c601-148">Das folgende Beispiel zeigt einer CSS-Klasse den Text der Sätze in Rot.</span><span class="sxs-lookup"><span data-stu-id="2c601-148">The following example shows a CSS class the sets text to red.</span></span>

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

<span data-ttu-id="2c601-149">Das nächste Beispiel zeigt, wie Sie die CSS-Klasse dynamisch anwenden.</span><span class="sxs-lookup"><span data-stu-id="2c601-149">The next example shows how to dynamically apply the CSS class.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a><span data-ttu-id="2c601-150">Seiten- und Rückrufe</span><span class="sxs-lookup"><span data-stu-id="2c601-150">Page and control callbacks</span></span>

<span data-ttu-id="2c601-151">Empfehlung: Beenden Sie verwenden von Rückrufen für Seiten und-Steuerelementen zu, und verwenden Sie stattdessen eine der folgenden: AJAX, UpdatePanel, MVC Aktionsmethoden, Web-API und SignalR.</span><span class="sxs-lookup"><span data-stu-id="2c601-151">Recommendation: Stop using page and control callbacks, and instead use any of the following: AJAX, UpdatePanel, MVC action methods, Web API, or SignalR.</span></span>

<span data-ttu-id="2c601-152">In früheren Versionen von ASP.NET aktiviert Seiten- und Steuerelementausgabe Rückrufmethoden Sie Teil der Webseite zu aktualisieren, ohne dass eine gesamte Seite aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="2c601-152">In earlier versions of ASP.NET, Page and Control callback methods enabled you to update part of the web page without refreshing an entire page.</span></span> <span data-ttu-id="2c601-153">Erreichen Sie Partial-Page-Updates bis jetzt [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [Web-API-](../../../web-api/index.md) oder [SignalR](../../../signalr/index.md).</span><span class="sxs-lookup"><span data-stu-id="2c601-153">You can now accomplish partial-page updates through [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [Web API](../../../web-api/index.md) or [SignalR](../../../signalr/index.md).</span></span> <span data-ttu-id="2c601-154">Sie müssen die Verwendung von Rückrufmethoden, weil sie Probleme mit freundlichen URLs verursachen können, und routing beenden.</span><span class="sxs-lookup"><span data-stu-id="2c601-154">You should stop using callback methods because they can cause issues with friendly URLs and routing.</span></span> <span data-ttu-id="2c601-155">Standardmäßig Steuerelemente Rückrufmethoden nicht aktivieren, aber wenn Sie diese Funktion in einem Steuerelement aktiviert haben, sollten Sie ihn deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="2c601-155">By default, controls do not enable callback methods, but if you enabled this feature in a control, you should disable it.</span></span>

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a><span data-ttu-id="2c601-156">Erkennung des Webbrowsers-Funktion</span><span class="sxs-lookup"><span data-stu-id="2c601-156">Browser capability detection</span></span>

<span data-ttu-id="2c601-157">Empfehlung: Beenden Sie der Verwendung von statischen Funktion Browsererkennung und verwenden Sie die Ermittlung von dynamischen Features.</span><span class="sxs-lookup"><span data-stu-id="2c601-157">Recommendation: Stop using static browser capability detection, and instead use dynamic feature detection.</span></span>

<span data-ttu-id="2c601-158">In früheren Versionen von ASP.NET wurden die unterstützten Funktionen für jeden Browser in einer XML-Datei gespeichert.</span><span class="sxs-lookup"><span data-stu-id="2c601-158">In earlier versions of ASP.NET, the supported features for each browser were stored in an XML file.</span></span> <span data-ttu-id="2c601-159">Erkennen von Feature-Unterstützung über die statische Suche ist nicht der beste Ansatz.</span><span class="sxs-lookup"><span data-stu-id="2c601-159">Detecting feature support through a static lookup is not the best approach.</span></span> <span data-ttu-id="2c601-160">Nun, Sie können dynamisch erkennen ein Browser unterstützten Funktionen des mithilfe einer Funktion zur Erkennung-Framework, wie z. B. [Modernizr](http://modernizr.com/).</span><span class="sxs-lookup"><span data-stu-id="2c601-160">Now, you can dynamically detect a browser's supported features by using a feature detection framework, such as [Modernizr](http://modernizr.com/).</span></span> <span data-ttu-id="2c601-161">Ermittlung von Features bestimmt die Unterstützung von versuchen, eine Methode oder Eigenschaft zu verwenden, und klicken Sie dann überprüfen, um festzustellen, ob der Browser das gewünschte Ergebnis erzeugt.</span><span class="sxs-lookup"><span data-stu-id="2c601-161">Feature detection determines support by attempting to use a method or property and then checking to see if the browser produced the desired result.</span></span> <span data-ttu-id="2c601-162">Standardmäßig ist Modernizr in die Web Application-Vorlagen enthalten.</span><span class="sxs-lookup"><span data-stu-id="2c601-162">By default, Modernizr is included in the Web application templates.</span></span>

<a id="security"></a>

## <a name="security"></a><span data-ttu-id="2c601-163">Sicherheit</span><span class="sxs-lookup"><span data-stu-id="2c601-163">Security</span></span>

<a id="validation"></a>

### <a name="request-validation"></a><span data-ttu-id="2c601-164">Request-Überprüfung</span><span class="sxs-lookup"><span data-stu-id="2c601-164">Request validation</span></span>

<span data-ttu-id="2c601-165">Empfehlung: Validieren von Benutzereingaben und Ausgabe von Benutzern zu codieren.</span><span class="sxs-lookup"><span data-stu-id="2c601-165">Recommendation: Validate user input, and encode output from users.</span></span>

<span data-ttu-id="2c601-166">Anforderungsvalidierung ist ein Feature von ASP.NET an, die jeder Anforderung untersucht und die Anforderung beendet, wenn eine erkannte Bedrohung gefunden wurde.</span><span class="sxs-lookup"><span data-stu-id="2c601-166">Request validation is a feature of ASP.NET that inspects each request and stops the request if a perceived threat is found.</span></span> <span data-ttu-id="2c601-167">Hängen Sie nicht von Anforderungsvalidierung für Ihre Anwendung vor Cross-Site scripting-Angriffe zu schützen.</span><span class="sxs-lookup"><span data-stu-id="2c601-167">Do not depend on request validation for securing your application against cross-site scripting attacks.</span></span> <span data-ttu-id="2c601-168">Stattdessen überprüfen Sie aller Eingaben von Benutzern und codieren Sie die Ausgabe zu.</span><span class="sxs-lookup"><span data-stu-id="2c601-168">Instead, validate all input from users and encode the output.</span></span> <span data-ttu-id="2c601-169">Klicken Sie in manchen Fällen können Sie reguläre Ausdrücke zum Überprüfen der Eingabe, jedoch in komplexen Fällen, die Sie überprüfen sollten Benutzereingaben mithilfe von Klassen in .NET, die zu bestimmen, ob der Wert entspricht Werte zulässig.</span><span class="sxs-lookup"><span data-stu-id="2c601-169">In some limited cases, you can use regular expressions to validate the input, but in more complicated cases you should validate user input by using .NET classes that determine if the value matches allowed values.</span></span>

<span data-ttu-id="2c601-170">Das folgende Beispiel zeigt, wie Sie eine statische Methode in der Uri-Klasse verwenden, um zu bestimmen, ob der Uri von einem Benutzer gültig ist.</span><span class="sxs-lookup"><span data-stu-id="2c601-170">The following example shows how to use a static method in the Uri class to determine whether the Uri provided by a user is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

<span data-ttu-id="2c601-171">Jedoch um den Uri ausreichend zu überprüfen, Sie sollten auch prüfen, stellen Sie sicher, es gibt `http` oder `https`.</span><span class="sxs-lookup"><span data-stu-id="2c601-171">However, to sufficiently verify the Uri, you should also check to make sure it specifies `http` or `https`.</span></span> <span data-ttu-id="2c601-172">Im folgenden Beispiel wird Instanzmethoden zur Verfügung, um sicherzustellen, dass der Uri gültig ist.</span><span class="sxs-lookup"><span data-stu-id="2c601-172">The following example uses instance methods to verify that the Uri is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

<span data-ttu-id="2c601-173">Codieren Sie, bevor Sie Benutzereingaben als HTML-rendering oder einschließlich der Benutzereingabe in einer SQL-Abfrage, die Werte, um sicherzustellen, dass bösartiger Code nicht enthalten ist.</span><span class="sxs-lookup"><span data-stu-id="2c601-173">Before rendering user input as HTML or including user input in a SQL query, encode the values to ensure malicious code is not included.</span></span>

<span data-ttu-id="2c601-174">Können Sie HTML-Codierung für den Wert im Markup mit der &lt;%: %&gt; Syntax, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="2c601-174">You can HTML encode the value in markup with the &lt;%: %&gt; syntax, as shown below.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

<span data-ttu-id="2c601-175">In Razor-Syntax können Sie HTML codieren mit @, wie unten dargestellt.</span><span class="sxs-lookup"><span data-stu-id="2c601-175">Or, in Razor syntax, you can HTML encode with @, as shown below.</span></span>

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

<span data-ttu-id="2c601-176">Das folgende Beispiel zeigt eine Codierung wie in HTML einen Wert im Code-Behind.</span><span class="sxs-lookup"><span data-stu-id="2c601-176">The next example shows how to HTML encode a value in code-behind.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

<span data-ttu-id="2c601-177">Um einen Wert für die SQL-Befehlen sicher zu codieren, verwenden Sie Parameter des Befehls wie z. B. die ["SqlParameter"](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-177">To safely encode a value for SQL commands, use command parameters such as the [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx).</span></span> <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a><span data-ttu-id="2c601-178">Formularauthentifizierung und Sitzung</span><span class="sxs-lookup"><span data-stu-id="2c601-178">Cookieless forms authentication and session</span></span>

<span data-ttu-id="2c601-179">Empfehlung: Erfordern Sie Cookies.</span><span class="sxs-lookup"><span data-stu-id="2c601-179">Recommendation: Require cookies.</span></span>

<span data-ttu-id="2c601-180">Übergeben von Informationen für die Authentifizierung in der Abfragezeichenfolge ist nicht sicher.</span><span class="sxs-lookup"><span data-stu-id="2c601-180">Passing authentication information in the query string is not secure.</span></span> <span data-ttu-id="2c601-181">Erfordern Sie daher Cookies auf, wenn Ihre Anwendung Authentifizierung enthält.</span><span class="sxs-lookup"><span data-stu-id="2c601-181">Therefore, require cookies when your application includes authentication.</span></span> <span data-ttu-id="2c601-182">Wenn Ihr Cookie vertraulichen Informationen gespeichert werden, sollten Sie die Erzwingung von SSL für das Cookie.</span><span class="sxs-lookup"><span data-stu-id="2c601-182">If your cookie stores sensitive information, consider requiring SSL for the cookie.</span></span>

<span data-ttu-id="2c601-183">Das folgende Beispiel zeigt, wie Sie in der Datei "Web.config" angeben, dass die Formularauthentifizierung einen Cookie erforderlich ist, der über SSL übertragen werden.</span><span class="sxs-lookup"><span data-stu-id="2c601-183">The following example shows how to specify in the Web.config file that Forms Authentication requires a cookie that is transmitted over SSL.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a><span data-ttu-id="2c601-184">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="2c601-184">EnableViewStateMac</span></span>

<span data-ttu-id="2c601-185">Empfehlung: Nie auf "false" festgelegt.</span><span class="sxs-lookup"><span data-stu-id="2c601-185">Recommendation: Never set to false.</span></span>

<span data-ttu-id="2c601-186">In der Standardeinstellung EnbableViewStateMac festgelegt ist auf "true".</span><span class="sxs-lookup"><span data-stu-id="2c601-186">By default, EnbableViewStateMac is set to true.</span></span> <span data-ttu-id="2c601-187">Auch wenn Ihre Anwendung nicht Ansichtszustand verwendet wird, legen Sie EnableViewStateMac nicht auf "false".</span><span class="sxs-lookup"><span data-stu-id="2c601-187">Even if your application is not using view state, do not set EnableViewStateMac to false.</span></span> <span data-ttu-id="2c601-188">Dieser Wert auf "false" festlegen, wird die Anwendung anfällig für Cross-Site-scripting machen.</span><span class="sxs-lookup"><span data-stu-id="2c601-188">Setting this value to false will make your application vulnerable to cross-site scripting.</span></span>

<span data-ttu-id="2c601-189">ASP.NET 4.5.2 ab, die Laufzeit erzwingt **EnableViewStateMac = True**.</span><span class="sxs-lookup"><span data-stu-id="2c601-189">Starting with ASP.NET 4.5.2, the runtime enforces **EnableViewStateMac=true**.</span></span> <span data-ttu-id="2c601-190">Auch wenn Sie es auf "false" festlegen, wird die Laufzeit ignoriert diesen Wert und wird mit dem Wert "true".</span><span class="sxs-lookup"><span data-stu-id="2c601-190">Even if you set it to false, the runtime ignores this value and proceeds with the value set to true.</span></span> <span data-ttu-id="2c601-191">Weitere Informationen finden Sie unter [ASP.NET 4.5.2 und EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-191">For more information, see [ASP.NET 4.5.2 and EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).</span></span>

<span data-ttu-id="2c601-192">Das folgende Beispiel zeigt, wie Sie EnableViewStateMac auf "true" festlegen.</span><span class="sxs-lookup"><span data-stu-id="2c601-192">The following example shows how to set EnableViewStateMac to true.</span></span> <span data-ttu-id="2c601-193">Sie müssen nicht tatsächlich legen Sie diesen Wert auf "true", da sie standardmäßig "true" ist.</span><span class="sxs-lookup"><span data-stu-id="2c601-193">You do not need to actually set this value to true because it is true by default.</span></span> <span data-ttu-id="2c601-194">Wenn Sie es auf "false" auf einer beliebigen Seite in Ihrer Anwendung festgelegt haben, müssen Sie diesen Wert sofort beheben.</span><span class="sxs-lookup"><span data-stu-id="2c601-194">However, if you have set it to false on any page in your application, you must immediately correct this value.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a><span data-ttu-id="2c601-195">Mittlere Vertrauenswürdigkeit</span><span class="sxs-lookup"><span data-stu-id="2c601-195">Medium trust</span></span>

<span data-ttu-id="2c601-196">Empfehlung: Hängen Sie nicht von mittlere Vertrauensebene (oder einer anderen Ebene der Vertrauenswürdigkeit) als Sicherheitsgrenze.</span><span class="sxs-lookup"><span data-stu-id="2c601-196">Recommendation: Do not depend on Medium Trust (or any other trust level) as a security boundary.</span></span>

<span data-ttu-id="2c601-197">Teilweiser Vertrauenswürdigkeit schützt Ihre Anwendung nicht ordnungsgemäß und sollte nicht verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2c601-197">Partial trust does not adequately protect your application and should not be used.</span></span> <span data-ttu-id="2c601-198">Verwenden Sie stattdessen voll vertrauenswürdig, und nicht vertrauenswürdige Anwendungen in separaten Anwendungspools zu isolieren.</span><span class="sxs-lookup"><span data-stu-id="2c601-198">Instead, use Full Trust, and isolate untrusted applications in separate application pools.</span></span> <span data-ttu-id="2c601-199">Führen Sie außerdem jeder Anwendungspool unter einer eindeutigen Identität.</span><span class="sxs-lookup"><span data-stu-id="2c601-199">Also, run each application pool under a unique identity.</span></span> <span data-ttu-id="2c601-200">Weitere Informationen finden Sie unter [ASP.NET teilweiser Vertrauenswürdigkeit garantiert keine Anwendungsisolation](https://support.microsoft.com/kb/2698981).</span><span class="sxs-lookup"><span data-stu-id="2c601-200">For more information, see [ASP.NET Partial Trust does not guarantee application isolation](https://support.microsoft.com/kb/2698981).</span></span>

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a><span data-ttu-id="2c601-201">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="2c601-201">&lt;appSettings&gt;</span></span>

<span data-ttu-id="2c601-202">Empfehlung: Deaktivieren Sie Sicherheitseinstellungen in nicht &lt;"appSettings"&gt; Element.</span><span class="sxs-lookup"><span data-stu-id="2c601-202">Recommendation: Do not disable security settings in &lt;appSettings&gt; element.</span></span>

<span data-ttu-id="2c601-203">Das Element "appSettings" enthält viele Werte, die für die Sicherheitsupdates erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="2c601-203">The appSettings element contains many values which are required for security updates.</span></span> <span data-ttu-id="2c601-204">Sie sollten nicht ändern oder deaktivieren diese Werte.</span><span class="sxs-lookup"><span data-stu-id="2c601-204">You should not change or disable these values.</span></span> <span data-ttu-id="2c601-205">Wenn Sie diese Werte beim Bereitstellen eines Updates deaktivieren müssen, sofort wieder aktivieren Sie nach Abschluss der Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="2c601-205">If you must disable these values when deploying an update, immediately re-enable after completing deployment.</span></span>

<span data-ttu-id="2c601-206">Weitere Informationen finden Sie unter [ASP.NET AppSettings-Element](https://msdn.microsoft.com/library/hh975440.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-206">For details, see [ASP.NET appSettings Element](https://msdn.microsoft.com/library/hh975440.aspx).</span></span>

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a><span data-ttu-id="2c601-207">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="2c601-207">UrlPathEncode</span></span>

<span data-ttu-id="2c601-208">Empfehlung: Verwendung [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) stattdessen.</span><span class="sxs-lookup"><span data-stu-id="2c601-208">Recommendation: Use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) instead.</span></span>

<span data-ttu-id="2c601-209">.NET Framework, um eine ganz bestimmte Browser Problems "Anwendungskompatibilität" aufgelöst wurde die UrlPathEncode-Methode hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2c601-209">The UrlPathEncode method was added to the .NET Framework to resolve a very specific browser compatibility problem.</span></span> <span data-ttu-id="2c601-210">Er nicht ordnungsgemäß codiert eine URL und die Anwendung nicht vor Cross-Site-scripting schützt.</span><span class="sxs-lookup"><span data-stu-id="2c601-210">It does not adequately encode a URL, and does not protect your application from cross-site scripting.</span></span> <span data-ttu-id="2c601-211">Sie sollten nie in Ihrer Anwendung verwenden.</span><span class="sxs-lookup"><span data-stu-id="2c601-211">You should never use it in your application.</span></span> <span data-ttu-id="2c601-212">Verwenden Sie stattdessen [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-212">Instead, use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).</span></span>

<span data-ttu-id="2c601-213">Das folgende Beispiel zeigt, wie eine codierte URL als ein Abfragezeichenfolgen-Parameter für ein Hyperlink-Steuerelement zu übergeben.</span><span class="sxs-lookup"><span data-stu-id="2c601-213">The following example shows how to pass an encoded URL as a query string parameter for a hyperlink control.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a><span data-ttu-id="2c601-214">Zuverlässigkeit und Leistung</span><span class="sxs-lookup"><span data-stu-id="2c601-214">Reliability and performance</span></span>

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a><span data-ttu-id="2c601-215">PreSendRequestHeaders und PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="2c601-215">PreSendRequestHeaders and PreSendRequestContent</span></span>

<span data-ttu-id="2c601-216">Empfehlung: Verwenden Sie diese Ereignisse nicht mit verwalteten Module aus.</span><span class="sxs-lookup"><span data-stu-id="2c601-216">Recommendation: Do not use these events with managed modules.</span></span> <span data-ttu-id="2c601-217">Schreiben Sie stattdessen ein natives IIS-Modul, um die erforderliche Aufgabe ausführen.</span><span class="sxs-lookup"><span data-stu-id="2c601-217">Instead, write a native IIS module to perform the required task.</span></span> <span data-ttu-id="2c601-218">Finden Sie unter [Erstellen von systemeigenem Code HTTP-Modulen](https://msdn.microsoft.com/library/ms693629.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-218">See [Creating Native-Code HTTP Modules](https://msdn.microsoft.com/library/ms693629.aspx).</span></span>

<span data-ttu-id="2c601-219">Sie können die [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) und [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) Ereignisse mit systemeigenen IIS-Modulen.</span><span class="sxs-lookup"><span data-stu-id="2c601-219">You can use the [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) and [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) events with native IIS modules.</span></span>
> [!WARNING]
> <span data-ttu-id="2c601-220">Verwenden Sie keine `PreSendRequestHeaders` und `PreSendRequestContent` mit verwalteten Modulen, die implementieren `IHttpModule`.</span><span class="sxs-lookup"><span data-stu-id="2c601-220">Do not use `PreSendRequestHeaders` and `PreSendRequestContent` with managed modules that implement `IHttpModule`.</span></span> <span data-ttu-id="2c601-221">Das Festlegen dieser Eigenschaften kann Probleme bei asynchronen Anforderungen verursachen.</span><span class="sxs-lookup"><span data-stu-id="2c601-221">Setting these properties can cause issues with asynchronous requests.</span></span> <span data-ttu-id="2c601-222">Die Kombination von der Anwendung angeforderte Routing (ARR) und Websockets kann zu Zugriffsverletzungsausnahmen führen, die w3wp zu einem Absturz führen können.</span><span class="sxs-lookup"><span data-stu-id="2c601-222">The combination of Application Requested Routing (ARR) and websockets might lead to access violation exceptions that can cause w3wp to crash.</span></span> <span data-ttu-id="2c601-223">Z. B. Iiscore! W3_CONTEXT_BASE::GetIsLastNotification + 68 in iiscore.dll hat eine Access-Verletzung-Ausnahme (0xC0000005) verursacht.</span><span class="sxs-lookup"><span data-stu-id="2c601-223">For example, iiscore!W3_CONTEXT_BASE::GetIsLastNotification+68 in iiscore.dll has caused an access violation exception (0xC0000005).</span></span>

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a><span data-ttu-id="2c601-224">Asynchrone Seitenereignisse mit Web forms</span><span class="sxs-lookup"><span data-stu-id="2c601-224">Asynchronous page events with web forms</span></span>

<span data-ttu-id="2c601-225">Empfehlung: In Web Forms zu vermeiden, Schreiben Async-void-Methoden für den Seitenlebenszyklus-Ereignisse und stattdessen [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) für asynchronen Code.</span><span class="sxs-lookup"><span data-stu-id="2c601-225">Recommendation: In Web Forms, avoid writing async void methods for Page lifecycle events, and instead use [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) for asynchronous code.</span></span>

<span data-ttu-id="2c601-226">Wenn Sie ein Seitenereignis mit markieren **Async** und **"void"**, Sie können nicht bestimmen, wann der asynchrone Code beendet wurde.</span><span class="sxs-lookup"><span data-stu-id="2c601-226">When you mark a page event with **async** and **void**, you cannot determine when the asynchronous code has finished.</span></span> <span data-ttu-id="2c601-227">Verwenden Sie stattdessen Page.RegisterAsyncTask, um den asynchronen Code so ausführen, die Sie zu ihrem Abschluss verfolgen können.</span><span class="sxs-lookup"><span data-stu-id="2c601-227">Instead, use Page.RegisterAsyncTask to run the asynchronous code in a way that enables you to track its completion.</span></span>

<span data-ttu-id="2c601-228">Das folgende Beispiel zeigt eine Schaltfläche klicken Sie auf Handler, der asynchrone Code enthält.</span><span class="sxs-lookup"><span data-stu-id="2c601-228">The following example shows a button click handler that contains asynchronous code.</span></span> <span data-ttu-id="2c601-229">Dieses Beispiel enthält Lesen eines Zeichenfolgenwerts asynchron, die nur als ein vereinfachtes Beispiel einer asynchronen Aufgabe und nicht als empfohlene Vorgehensweise bereitgestellt wird.</span><span class="sxs-lookup"><span data-stu-id="2c601-229">This example includes reading a string value asynchronously, which is provided only as a simplified example of an asynchronous task and not as a recommended practice.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

<span data-ttu-id="2c601-230">Bei Verwendung asynchrone Aufgaben, legen Sie das Zielframework des Http-Laufzeit auf 4.5 (oder höher) in der Datei "Web.config".</span><span class="sxs-lookup"><span data-stu-id="2c601-230">If you are using asynchronous Tasks, set the Http runtime target framework to 4.5 (or later) in the Web.config file.</span></span> <span data-ttu-id="2c601-231">Durch Festlegen des Zielframeworks auf 4.5 auf dem neuen Synchronisierungskontext an, die wurde in .NET 4.5 hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="2c601-231">Setting the target framework to 4.5 turns on the new synchronization context that was added in .NET 4.5.</span></span> <span data-ttu-id="2c601-232">Dieser Wert ist standardmäßig in neuen Projekten in Visual Studio festgelegt, aber es ist nicht festgelegt werden, wenn Sie ein vorhandenes Projekt arbeiten.</span><span class="sxs-lookup"><span data-stu-id="2c601-232">This value is set by default in new projects in Visual Studio, but is not be set if you are working with an existing project.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a><span data-ttu-id="2c601-233">Fire-and-forget-Aufgaben</span><span class="sxs-lookup"><span data-stu-id="2c601-233">Fire-and-forget work</span></span>

<span data-ttu-id="2c601-234">Empfehlung: Bei der Behandlung einer Anforderung in ASP.NET zu vermeiden, Starten von Fire-and-forget-Arbeit (solche Aufrufen der Methode ThreadPool.QueueUserWorkItem oder erstellen einen Timer, der wiederholt einen Delegaten aufruft).</span><span class="sxs-lookup"><span data-stu-id="2c601-234">Recommendation: When handling a request within ASP.NET, avoid launching fire-and-forget work (such calling the ThreadPool.QueueUserWorkItem method or creating a timer that repeatedly calls a delegate).</span></span>

<span data-ttu-id="2c601-235">Wenn Ihre Anwendung Fire-and-forget-Aufgaben, die in ASP.NET ausgeführt wird, erhalten Ihre Anwendung nicht mehr synchron. Zu jedem Zeitpunkt kann die Anwendungsdomäne zerstört werden dies bedeutet, dass es sich bei Ihrem laufende Prozess den aktuellen Zustand der Anwendung möglicherweise nicht mehr überein.</span><span class="sxs-lookup"><span data-stu-id="2c601-235">If your application has fire-and-forget work that runs within ASP.NET, your application can get out of sync. At any time, the app domain can be destroyed which means your ongoing process may no longer match the current state of the application.</span></span>

<span data-ttu-id="2c601-236">Sie sollten diese Art von Arbeit außerhalb von ASP.NET verschieben.</span><span class="sxs-lookup"><span data-stu-id="2c601-236">You should move this type of work outside of ASP.NET.</span></span> <span data-ttu-id="2c601-237">Sie können verwenden Sie einen Webauftrag, der Windows-Dienst oder eine workerrolle in Azure derzeit ausgeführte Arbeit ausführen, und führen Sie diesen Code aus einem anderen Prozess.</span><span class="sxs-lookup"><span data-stu-id="2c601-237">You can use a Web Jobs, Windows Service or a Worker role in Azure to perform ongoing work, and run that code from another process.</span></span>

<span data-ttu-id="2c601-238">Wenn Sie diese Arbeit in ASP.NET ausführen müssen, können Sie die Nuget-Paket namens hinzufügen [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) um den Code auszuführen.</span><span class="sxs-lookup"><span data-stu-id="2c601-238">If you must perform this work within ASP.NET, you can add the Nuget package called [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) to run the code.</span></span>

<a id="requestentity"></a>

### <a name="request-entity-body"></a><span data-ttu-id="2c601-239">Anforderungs-einheitstextkörpers</span><span class="sxs-lookup"><span data-stu-id="2c601-239">Request entity body</span></span>

<span data-ttu-id="2c601-240">Empfehlung: Lesen Request.Form oder Request.InputStream, vor dem Ausführen des Handlers des Ereignisses zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="2c601-240">Recommendation: Avoid reading Request.Form or Request.InputStream before the handler's execute event.</span></span>

<span data-ttu-id="2c601-241">Das früheste von Request.Form oder Request.InputStream gelesenen sollte ausführen während des Handlers Ereignis.</span><span class="sxs-lookup"><span data-stu-id="2c601-241">The earliest you should read from Request.Form or Request.InputStream is during the handler's execute event.</span></span> <span data-ttu-id="2c601-242">In MVC wird der Controller ist der Handler, und das Execute-Ereignis ist, wenn die Aktionsmethode ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="2c601-242">In MVC, the Controller is the handler and the execute event is when the action method runs.</span></span> <span data-ttu-id="2c601-243">In Web Forms die Seite wird der Handler, und das Execute-Ereignis ist, wenn das Page.Init-Ereignis auslöst.</span><span class="sxs-lookup"><span data-stu-id="2c601-243">In Web Forms, the Page is the handler and the execute event is when the Page.Init event fires.</span></span> <span data-ttu-id="2c601-244">Wenn Sie die Anforderungs-einheitstextkörper vor der Execute-Ereignis lesen, stören Sie bei der Verarbeitung der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="2c601-244">If you read the request entity body earlier than the execute event, you interfere with the processing of the request.</span></span>

<span data-ttu-id="2c601-245">Wenn Sie die Anforderungs-einheitstextkörper vor der Execute-Ereignis finden müssen, verwenden Sie entweder [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) oder [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-245">If you need to read the request entity body before the execute event, use either [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) or [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx).</span></span> <span data-ttu-id="2c601-246">Bei Verwendung von GetBufferlessInputStream Sie den unformatierten Stream abrufen, aus der Anforderung und verantwortlich für die gesamte Anforderung zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="2c601-246">When you use GetBufferlessInputStream, you get the raw stream from the request, and assume responsibility for processing the entire request.</span></span> <span data-ttu-id="2c601-247">Nach dem Aufruf GetBufferlessInputStream, sind Request.Form und Request.InputStream nicht verfügbar, da sie von ASP.NET nicht aufgefüllt wurden.</span><span class="sxs-lookup"><span data-stu-id="2c601-247">After calling GetBufferlessInputStream, Request.Form and Request.InputStream are not available because they have not been populated by ASP.NET.</span></span> <span data-ttu-id="2c601-248">Wenn Sie GetBufferedInputStream verwenden, erhalten Sie eine Kopie des Datenstroms aus der Anforderung.</span><span class="sxs-lookup"><span data-stu-id="2c601-248">When you use GetBufferedInputStream, you get a copy of the stream from the request.</span></span> <span data-ttu-id="2c601-249">Request.Form und Request.InputStream sind weiter unten in der Anforderung weiterhin verfügbar, da ASP.NET die andere Kopie füllt.</span><span class="sxs-lookup"><span data-stu-id="2c601-249">Request.Form and Request.InputStream are still available later in the request because ASP.NET populates the other copy.</span></span>

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a><span data-ttu-id="2c601-250">Response.Redirect und Response.End</span><span class="sxs-lookup"><span data-stu-id="2c601-250">Response.Redirect and Response.End</span></span>

<span data-ttu-id="2c601-251">Empfehlung: Achten Sie Unterschiede in der Behandlung der Thread nach dem Aufruf [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-251">Recommendation: Be aware of differences in how thread is handled after calling [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).</span></span>

<span data-ttu-id="2c601-252">Die [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) Methode ruft die Response.End-Methode.</span><span class="sxs-lookup"><span data-stu-id="2c601-252">The [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) method calls the Response.End method.</span></span> <span data-ttu-id="2c601-253">Innerhalb eines synchronen Prozesses durch den Aufruf von Request.Redirect der aktuelle Thread sofort abgebrochen.</span><span class="sxs-lookup"><span data-stu-id="2c601-253">In a synchronous process, calling Request.Redirect causes the current thread to immediately abort.</span></span> <span data-ttu-id="2c601-254">Allerdings in einem asynchronen Prozess Aufruf von Response.Redirect nicht den aktuellen Thread abgebrochen wird, damit die codeausführung für die Anforderung wird fortgesetzt.</span><span class="sxs-lookup"><span data-stu-id="2c601-254">However, in an asynchronous process, calling Response.Redirect does not abort the current thread, so code execution continues for the request.</span></span> <span data-ttu-id="2c601-255">In einem asynchronen Prozess ist müssen Sie die Aufgabe von der Methode zum Beenden der Ausführung des Codes zurückgeben.</span><span class="sxs-lookup"><span data-stu-id="2c601-255">In an asynchronous process, you must return the Task from the method to stop the code execution.</span></span>

<span data-ttu-id="2c601-256">In einem MVC-Projekt dürfen Sie nicht Response.Redirect aufrufen.</span><span class="sxs-lookup"><span data-stu-id="2c601-256">In an MVC project, you should not call Response.Redirect.</span></span> <span data-ttu-id="2c601-257">Geben Sie stattdessen ein RedirectResult zurück.</span><span class="sxs-lookup"><span data-stu-id="2c601-257">Instead, return a RedirectResult.</span></span>

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a><span data-ttu-id="2c601-258">EnableViewState und ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="2c601-258">EnableViewState and ViewStateMode</span></span>

<span data-ttu-id="2c601-259">Empfehlung: Können Sie ViewStateMode, anstatt EnableViewState, geben Sie eine präzise Kontrolle über die Steuerelemente den Ansichtszustand verwenden.</span><span class="sxs-lookup"><span data-stu-id="2c601-259">Recommendation: Use ViewStateMode, instead of EnableViewState, to provide granular control over which controls use view state.</span></span>

<span data-ttu-id="2c601-260">Wenn Sie auf "false", in der Page-Direktive EnableViewState festlegen, werden Ansichtszustand ist für alle Steuerelemente auf der Seite deaktiviert und kann nicht aktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="2c601-260">When you set EnableViewState to false in the Page directive, view state is disabled for all controls within the page and cannot be enabled.</span></span> <span data-ttu-id="2c601-261">Wenn Sie den Ansichtszustand für nur für bestimmte Steuerelemente auf der Seite zu aktivieren möchten, können Sie ViewStateMode auf deaktiviert festgelegt, für die Seite.</span><span class="sxs-lookup"><span data-stu-id="2c601-261">If you want to enable view state for only certain controls in your page, set ViewStateMode to Disabled for the Page.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

<span data-ttu-id="2c601-262">Legen Sie Sie dann ViewStateMode aktiviert, auf nur die Steuerelemente, die tatsächlich Ansichtszustand benötigen.</span><span class="sxs-lookup"><span data-stu-id="2c601-262">Then, set ViewStateMode to Enabled on only the controls that actually need view state.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

<span data-ttu-id="2c601-263">Durch Aktivieren der Ansichtszustand für nur die Steuerelemente, die ihn benötigen, können Sie die Größe des Ansichtszustands für Ihre Webseiten verkleinern.</span><span class="sxs-lookup"><span data-stu-id="2c601-263">By enabling view state for only the controls that need it, you can shrink the size of the view state for your web pages.</span></span>

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a><span data-ttu-id="2c601-264">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="2c601-264">SqlMembershipProvider</span></span>

<span data-ttu-id="2c601-265">Empfehlung: Verwenden Sie universelle Anbieter.</span><span class="sxs-lookup"><span data-stu-id="2c601-265">Recommendation: Use Universal Providers.</span></span>

<span data-ttu-id="2c601-266">In der aktuellen Projektvorlagen SqlMembershipProvider wurde ersetzt durch [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), die als NuGet-Paket verfügbar ist.</span><span class="sxs-lookup"><span data-stu-id="2c601-266">In the current project templates, SqlMembershipProvider has been replaced by [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), which is available as a NuGet package.</span></span> <span data-ttu-id="2c601-267">Wenn Sie SqlMembershipProvider in einem Projekt verwenden, die mit einer früheren Version der Vorlagen erstellt wurde, sollten Sie für universelle Anbieter wechseln.</span><span class="sxs-lookup"><span data-stu-id="2c601-267">If you are using SqlMembershipProvider in a project that was built with an earlier version of the templates, you should switch to Universal Providers.</span></span> <span data-ttu-id="2c601-268">Universal Providers funktionieren mit allen Datenbanken, die von Entity Framework unterstützt werden.</span><span class="sxs-lookup"><span data-stu-id="2c601-268">The Universal Providers work with all databases that are supported by Entity Framework.</span></span>

<span data-ttu-id="2c601-269">Weitere Informationen finden Sie unter [Einführung in ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).</span><span class="sxs-lookup"><span data-stu-id="2c601-269">For more information, see [Introducing ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).</span></span>

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a><span data-ttu-id="2c601-270">Lang ausgeführte Anforderungen (> 110 Sekunden)</span><span class="sxs-lookup"><span data-stu-id="2c601-270">Long-running requests (>110 seconds)</span></span>

<span data-ttu-id="2c601-271">Empfehlung: Verwenden Sie [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) oder [SignalR](../../../signalr/index.md) für verbundene Clients, und verwenden asynchrone e/a-Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="2c601-271">Recommendation: Use [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) or [SignalR](../../../signalr/index.md) for connected clients, and use asynchronous I/O operations.</span></span>

<span data-ttu-id="2c601-272">Lang ausgeführte Anforderungen kann zu unvorhersehbaren Ergebnissen und eine schlechte Leistung in Ihrer Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="2c601-272">Long-running requests can cause unpredictable results and poor performance in your web application.</span></span> <span data-ttu-id="2c601-273">Der standardmäßigen zeitlimiteinstellung für eine Anforderung ist 110 Sekunden.</span><span class="sxs-lookup"><span data-stu-id="2c601-273">The default timeout setting for a request is 110 seconds.</span></span> <span data-ttu-id="2c601-274">Bei Verwendung des Sitzungszustands mit einer lang andauernden-Anforderung wird ASP.NET die Sperre für das Sitzungsobjekt nach 110 Sekunden freigegeben.</span><span class="sxs-lookup"><span data-stu-id="2c601-274">If you are using session state with a long-running request, ASP.NET will release the lock on the Session object after 110 seconds.</span></span> <span data-ttu-id="2c601-275">Jedoch kann die Anwendung gerade ein Vorgang für das Sitzungsobjekt sein, wenn die Sperre wird aufgehoben, und der Vorgang kann nicht erfolgreich abgeschlossen.</span><span class="sxs-lookup"><span data-stu-id="2c601-275">However, your application might be in the middle of an operation on the Session object when the lock is released, and the operation might not complete successfully.</span></span> <span data-ttu-id="2c601-276">Wenn eine zweite Anforderung des Benutzers blockiert wird, während die erste Anforderung ausgeführt wird, kann die zweite Anforderung das Sitzungsobjekt in einem inkonsistenten Zustand zugreifen.</span><span class="sxs-lookup"><span data-stu-id="2c601-276">If a second request from the user is blocked while the first request is running, the second request might access the Session object in an inconsistent state.</span></span>

<span data-ttu-id="2c601-277">Wenn Ihre Anwendung blockieren (oder synchrone) e/a-Vorgänge enthält, wird die Anwendung reagiert jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="2c601-277">If your application includes blocking (or synchronous) I/O operations, the application will be unresponsive.</span></span>

<span data-ttu-id="2c601-278">Verwenden Sie zur Verbesserung der Leistung der asynchronen e/a-Vorgänge in .NET Framework.</span><span class="sxs-lookup"><span data-stu-id="2c601-278">To improve performance, use the asynchronous I/O operations in the .NET Framework.</span></span> <span data-ttu-id="2c601-279">Außerdem verwenden Sie WebSockets oder SignalR, für die Verbindung von Clients an den Server.</span><span class="sxs-lookup"><span data-stu-id="2c601-279">Also, use WebSockets or SignalR for connecting clients to the server.</span></span> <span data-ttu-id="2c601-280">Diese Features dienen zum lang andauernder Anforderungen effizient zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="2c601-280">These features are designed to efficiently handle long-running requests.</span></span>
