---
uid: web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
title: Behandeln von HTTP 405-Fehlern, nach der Veröffentlichung von Web-API-Anwendungen | Microsoft-Dokumentation
author: rmcmurray
description: In diesem Tutorial wird beschrieben, wie zur Problembehandlung von HTTP 405-Fehlern nach dem Veröffentlichen einer Web-API-Anwendung in einem Produktionswebserver wird.
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 07ec7d37-023f-43ea-b471-60b08ce338f7
msc.legacyurl: /web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
msc.type: authoredcontent
ms.openlocfilehash: ce5b617cc1032d190cc2450aa554b462ea6f6156
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667413"
---
# <a name="troubleshooting-http-405-errors-after-publishing-web-api-applications"></a>Problembehandlung bei HTTP 405-Fehlern, nach der Veröffentlichung von Web-API-Anwendungen

> In diesem Tutorial wird beschrieben, wie zur Problembehandlung von HTTP 405-Fehlern nach dem Veröffentlichen einer Web-API-Anwendung in einem Produktionswebserver wird.
> 
> ## <a name="software-used-in-this-tutorial"></a>In diesem Tutorial verwendete Software
> 
> 
> - [Internetinformationsdienste (Internet Information Services, IIS)](https://www.iis.net/) (Version 7 oder höher)
> - [Web-API](../../index.md) 


Web-API-Anwendungen verwenden in der Regel mehrere gängige HTTP-Verben: GET, POST, PUT, DELETE, und manchmal PATCH. Nichtsdestotrotz ist Entwickler in Situationen, in denen diese Verben werden durch ein anderes Modul von IIS auf ihrer Produktionsserver, was zu einer Situation führt ein Web-API-Controller, der in Visual Studio oder auf einem Entwicklungsserver ordnungsgemäß funktioniert, in denen zurück implementiert, führen möglicherweise eine HTTP 405 Fehler bei der Bereitstellung auf einem Produktionsserver. Glücklicherweise kann dieses Problem einfach behoben, aber die Lösung erfordert eine Erklärung, warum das Problem auftritt.

## <a name="what-causes-http-405-errors"></a>Was bewirkt, dass HTTP 405-Fehlern

Im ersten Schritt zum Lernen, wie Sie Problemen HTTP 405-Fehler ist, zu verstehen, welche HTTP-Fehler 405 eigentlich bedeutet. Dokumentieren Sie den primären leitenden für HTTP ist [RFC 2616](http://www.ietf.org/rfc/rfc2616.txt), das definiert, dass des Statuscode "HTTP 405" als ***Method Not Allowed***, und beschreibt Weitere, dieser Statuscode als eine Situation, in denen &quot;der-Methode im angegebenen die Anforderungszeile ist für die durch den Anforderungs-URI identifizierte Ressource nicht zulässig.&quot; Das heißt, ist das HTTP-Verb für den bestimmten URL nicht zulässig, die ein HTTP-Client angefordert hat.

Als eine kurze Überprüfung sind hier einige der am häufigsten verwendeten HTTP-Methoden, wie in RFC 2616, RFC 4918 und RFC 5789 definiert:

| HTTP-Methode | Beschreibung |
| --- | --- |
| **GET** | Diese Methode wird verwendet, um Daten aus einem URI, und es wahrscheinlich die am häufigsten verwendeten HTTP-Methode abzurufen. |
| **HEAD** | Diese Methode ist ähnlich wie die GET-Methode, mit dem Unterschied, dass sie nicht die Daten tatsächlich aus der Anforderungs-URI abrufen: sie ruft einfach den HTTP-Status ab. |
| **POST** | Diese Methode wird in der Regel verwendet, um neue Daten an den URI senden; POST wird häufig verwendet, um Daten zu übermitteln. |
| **PUT** | Diese Methode wird in der Regel verwendet, um Rohdaten an den URI senden; PUT wird häufig verwendet, um JSON- oder XML-Daten für Web-API-Anwendungen zu übermitteln. |
| **LÖSCHEN** | Diese Methode wird verwendet, um Daten aus einem URI zu entfernen. |
| **OPTIONS** | Diese Methode wird normalerweise verwendet, um die Liste der HTTP-Methoden abzurufen, die für einen URI unterstützt werden. |
| **KOPIEREN VERSCHIEBEN** | Diese beiden Methoden mit WebDAV verwendet werden, und ihr Zweck ist selbsterklärend. |
| **MKCOL** | Diese Methode wird verwendet, mit WebDAV, und es wird verwendet, um eine Sammlung (z. B. ein Verzeichnis) am angegebenen URI zu erstellen. |
| **PROPFIND PROPPATCH** | Diese beiden Methoden mit WebDAV verwendet werden, und sie werden zum Abfragen, oder legen Sie Eigenschaften für einen URI verwendet. |
| **SPERREN ENTSPERREN** | Diese beiden Methoden mit WebDAV verwendet werden, und sie werden verwendet, um die Ressource, die durch den Anforderungs-URI identifiziert wird, beim Erstellen von Sperren/Entsperren. |
| **PATCH** | Diese Methode wird verwendet, um eine vorhandene HTTP-Ressource zu ändern. |

Wenn eine der folgenden HTTP-Methoden für die Verwendung auf dem Server konfiguriert ist, antwortet der Server mit der HTTP-Status und andere Daten, die für die Anforderung geeignet ist. (Z. B. eine GET-Methode wird möglicherweise eine HTTP 200 ***OK*** Antwort und eine PUT-Methode wird möglicherweise eine HTTP 201 ***erstellt*** Antwort.)

Wenn die HTTP-Methode für die Verwendung auf dem Server nicht konfiguriert ist, der Server antwortet mit einer HTTP-501 ***nicht implementiert*** Fehler.

Aber wenn eine HTTP-Methode für die Verwendung auf dem Server konfiguriert ist, aber es für einen angegebenen URI deaktiviert wurde, der Server antwortet mit einer HTTP 405 ***Method Not Allowed*** Fehler.

## <a name="example-http-405-error"></a>Beispiel für HTTP-Fehler 405

Das folgende Beispiel-HTTP-Anforderung und Antwort veranschaulicht eine Situation, in denen ein HTTP-Client versucht, den Wert an eine Web-API-Anwendung auf einem Webserver zu PLATZIEREN, und der Server gibt einen HTTP-Fehler die Zustände, die die PUT-Methode nicht zulässig:


HTTP-Anforderung:


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample1.cmd)]


HTTP-Antwort:


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample2.cmd)]


In diesem Beispiel ist der HTTP-Client eine gültige JSON-Anforderung an die URL für eine Web-API-Anwendung auf einem Webserver gesendet, aber der Server hat eine HTTP 405-Fehlermeldung gibt an, dass die PUT-Methode für die URL nicht zugelassen wurde zurückgegeben. Im Gegensatz dazu, wenn der Anforderungs-URI eine Route für die Web-API-Anwendung nicht übereinstimmten, der Server würde zurückgeben HTTP 404-Antwort ***wurde nicht gefunden.*** Fehler.

## <a name="resolve-http-405-errors"></a>Beheben von HTTP 405-Fehlern

Es gibt verschiedene Gründe, warum ein bestimmtes HTTP-Verb unter Umständen nicht zulässig, aber es gibt ein wichtiges Szenario, das ist der Hauptgrund für diesen Fehler in IIS: mehrere Handler für die gleiche Verb/Methode definiert sind, und einem der Handler aus den erwarteten Handler blockiert wird Verarbeitung der Anforderung. Über Erklärung verarbeitet IIS Handler aus der ersten, letzten basierend auf den Order-Handler-Einträgen in den Dateien "applicationHost.config" und "Web.config", in dem die erste übereinstimmende Kombination von Pfad "," Verb "," Resource "," usw., zur Verarbeitung der Anforderung verwendet wird.

Im folgende Beispiel wird ein Auszug aus einer Datei "applicationHost.config" für einen IIS-Server, der HTTP-Fehler 405 zurückgegeben wurden, wenn Sie die PUT-Methode zum Senden von Daten an eine Web-API-Anwendung verwenden. In diesem Ausschnitt wird mehrere HTTP-Handler definiert sind, und jeder Handler verfügt über einen anderen Satz von HTTP-Methoden, die für die sie konfiguriert ist – der letzte Eintrag in der Liste ist der statische Inhaltshandler, der der Standardhandler bietet, die verwendet wird, nachdem die andere Handler eine Chanc generiert hat e, um die Anforderung zu überprüfen:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample3.xml)]

Im obigen Beispiel werden der WebDAV-Handler und der Erweiterung URL-Handler für ASP.NET (die für Web-API verwendet wird) eindeutig für separate Listen mit HTTP-Methoden definiert. Beachten Sie, dass für alle HTTP-Methoden, die ISAPI-DLL-Handler konfiguriert ist, auch wenn diese Konfiguration nicht unbedingt einen Fehler verursachen. Allerdings wie Einstellungen für diese müssen berücksichtigt werden, bei der Problembehandlung von HTTP 405-Fehlern.

Im obigen Beispiel war der ISAPI-DLL-Handler nicht das Problem. in der Tat das Problem wurde nicht definiert, in der Datei "applicationHost.config" für den IIS-Server – das Problem verursacht wurde, durch die Eingabe, die in der Datei "Web.config" vorgenommen wurde, wenn die Web-API-Anwendung in Visual Studio erstellt wurde. Der folgende Auszug aus der Datei web.config der Anwendung zeigt den Speicherort des Problems:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample4.xml)]

In diesem Ausschnitt wird der Erweiterung URL-Handler für ASP.NET neu definiert, um zusätzliche HTTP-Methoden enthalten, die mit der Web-API-Anwendung verwendet werden. Da eine ähnliche Reihe von HTTP-Methoden für die WebDAV-Handler definiert ist, tritt jedoch ein Konflikt auf. In diesem speziellen Fall wird der Handler für WebDAV definiert und von IIS geladen wird, auch wenn Sie WebDAV für die Website deaktiviert ist, die die Web-API-Anwendung enthält. Während der Verarbeitung einer HTTP PUT-Anforderung ruft IIS das WebDAV-Modul aus, da es für die PUT-Verb definiert ist. Wenn das WebDAV-Modul aufgerufen wird, wird es überprüft die Konfiguration und erkennt Sie, dass er deaktiviert ist, ein, damit wird eine HTTP 405 zurückgegeben ***Method Not Allowed*** Fehler für jede Anforderung, die eine WebDAV-Anforderung ähnelt. Um dieses Problem zu beheben, sollten Sie WebDAV aus der Liste der HTTP-Module für die Website entfernen, wenn Ihre Web-API-Anwendung definiert ist. Das folgende Beispiel zeigt was, aussehen könnte:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample5.xml)]

Dieses Szenario tritt häufig nach eine Anwendung aus einer Entwicklungsumgebung in einer produktionsumgebung veröffentlicht wird, und dies geschieht, weil die Liste der Handler/Modules Ihrer Umgebungen für Entwicklungs- und produktionsumgebung unterschiedlich ist. Beispielsweise bei Verwendung von Visual Studio 2012 oder höher, um eine Web-API-Anwendung erstellen ist die IIS Express der Standardwebserver für Tests. Dieser Development-Webserver ist eine reduzierte Version von der vollständigen Funktionalität von IIS, die eine Server-Produkts ausgeliefert wird, und diese Entwicklungswebserver enthält einige Änderungen, die für Entwicklungsszenarios hinzugefügt wurden. Beispielsweise ist das WebDAV-Modul oft installiert auf einem Produktions-Web-Server, auf der die Vollversion von IIS ausgeführt wird, obwohl er in der Praxis möglicherweise nicht. Die Entwicklungsversion von IIS (IIS Express), wird das WebDAV-Modul installiert, aber die Einträge für das WebDAV-Modul absichtlich auskommentiert werden, damit das WebDAV-Modul niemals unter IIS Express geladen wird, es sei denn, Sie insbesondere die IIS Express-Konfiguration ändern. Einstellungen, die WebDAV-Funktionen der IIS Express-Installation hinzufügen. Daher Ihrer Webanwendung möglicherweise ordnungsgemäß auf Ihrem Entwicklungscomputer ausgeführt, aber Sie können HTTP 405-Fehler auftreten, wenn Sie Ihre Web-API-Anwendung auf Ihren Webserver für die Produktion veröffentlichen.

## <a name="summary"></a>Zusammenfassung

HTTP 405 Fehler treten auf, wenn eine HTTP-Methode nicht von einem Webserver für eine angeforderte URL zulässig ist. Diese Bedingung wird häufig angezeigt, wenn ein bestimmter Handler für ein bestimmtes Verb definiert wurde, und dieser Handler wird den Handler, den Sie erwarten, beim Verarbeiten der Anforderung dass überschreiben.

Wenn Sie die Situation eintreten, in dem Sie eine HTTP 501, was bedeutet Fehlermeldung, dass die spezifische Funktionalität nicht auf dem Server implementiert wurde, häufig bedeutet, dass es ist kein Handler definiert, die in den IIS-Einstellungen die HTTP-Anforderung entspricht dem Wahrscheinlich gibt an, dass etwas nicht ordnungsgemäß auf Ihrem System installiert wurde oder etwas verfügt über die IIS-Einstellungen so geändert, dass es sind keine Handler die der bestimmten HTTP-Unterstützungsmethode definiert. Um dieses Problem zu beheben, müssten Sie jede Anwendung neu zu installieren, die versucht, eine HTTP-Methode verwenden, für die es keine entsprechende Modul oder Handler Definitionen verfügt.
