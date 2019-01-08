---
uid: web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
title: Grundlegendes zu Teilupdates für Seiten mit ASP.NET-AJAX aktualisiert | Microsoft-Dokumentation
author: scottcate
description: Das sichtbarste Feature von ASP.NET AJAX Extensions ist vielleicht die Möglichkeit, eine Seite teilweise oder inkrementelle Updates vorzunehmen, ohne ein vollständiges Postback in t...
ms.author: riande
ms.date: 03/28/2008
ms.assetid: 54d9df99-1161-4899-b4e8-2679c85915e7
msc.legacyurl: /web-forms/overview/older-versions-getting-started/aspnet-ajax/understanding-partial-page-updates-with-asp-net-ajax
msc.type: authoredcontent
ms.openlocfilehash: 4883046aa16d5e67b7f0c92e15c897ef1a933b67
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098934"
---
<a name="understanding-partial-page-updates-with-aspnet-ajax"></a>Grundlegendes zu Teilupdates für Seiten aktualisiert mit ASP.NET-AJAX
====================
durch [Scott Cate](https://github.com/scottcate)

[PDF herunterladen](http://download.microsoft.com/download/C/1/9/C19A3451-1D14-477C-B703-54EF22E197EE/AJAX_tutorial01_Partial_Page_Updates_cs.pdf)

> Das sichtbarste Feature von ASP.NET AJAX Extensions ist vielleicht die Möglichkeit, eine Seite teilweise oder inkrementelle Updates vorzunehmen, ohne ein vollständiges Postback an den Server, ohne Änderungen am Code und mit minimalen Markupänderungen. Die Vorteile sind umfangreiche – der Status Ihrer multimedialen Inhalte (wie Adobe Flash, oder klicken Sie mit der Windows-Medien) unverändert, anfallenden Bandbreitenkosten werden reduziert und der Client treten keine das Flimmern in Verbindung mit dem ein Postback.


## <a name="introduction"></a>Einführung

Die Technologie von Microsoft ASP.NET bietet ein Programmiermodell, das objektorientierte und ereignisgesteuerte und vereinen sie die Vorteile des kompilierten Codes. Das Modell für die serverseitige Verarbeitung hat jedoch einige Nachteile, die sich die Technologie:

- Seitenupdates erfordern einen Roundtrip zum Server, der eine seitenaktualisierung erforderlich ist.
- Roundtrips bleiben keine Auswirkungen, die von Javascript oder andere clientseitige-Technologie (z. B. Adobe Flash) generierten nicht erhalten werden.
- Während des Postbacks unterstützen Browser als Microsoft Internet Explorer keine automatische Wiederherstellung die Bildlaufposition. Und auch in Internet Explorer ein Flimmern wie die Seite aktualisiert wird.
- Postbacks können zur Folge haben einer hohe Menge an Bandbreite, als die \_ \_VIEWSTATE-Formularfeld anwachsen, insbesondere bei Steuerelemente wie GridView-Steuerelement oder Repeater.
- Es gibt kein einheitliches Modell für den Zugriff auf Webdiensten über JavaScript oder andere clientseitige Technologie.

Geben Sie den Microsoft ASP.NET AJAX-Erweiterungen. AJAX ist das steht für **ein** synchrone **J** AvaScript **ein** Nd **X** ML ist ein integriertes Gerüst für die Bereitstellung von inkrementellen Seite Updates über die plattformübergreifende JavaScript, bestehend aus serverseitigem Code, einschließlich der Microsoft AJAX-Framework und eine Skriptkomponente wird aufgerufen, die Microsoft AJAX-Skriptbibliothek. Die ASP.NET AJAX-Erweiterungen bieten auch plattformübergreifende Unterstützung für den Zugriff auf ASP.NET-Webdienste über JavaScript.

In diesem Whitepaper untersucht die Funktionalität von ASP.NET AJAX Extensions, die umfasst das ScriptManager-Komponente, das UpdatePanel-Steuerelement und das UpdateProgress-Steuerelement und Szenarien, in denen sie soll, oder sollte nicht, berücksichtigt teilupdates für Seiten-updates verwendet.

In diesem Whitepaper basiert auf der Beta-2-Version von Visual Studio 2008 und .NET Framework 3.5, die ASP.NET AJAX Extensions, die in der Basisklassenbibliothek integriert ist (er zuvor eine Add-On-Komponente, die für ASP.NET 2.0 verfügbar war). In diesem Whitepaper wird vorausgesetzt, dass Sie Visual Studio 2008 und nicht Visual Web Developer Express Edition verwenden; Einige Projektvorlagen, auf die verwiesen wird, werden möglicherweise nicht für Visual Web Developer Express-Benutzer verfügbar.

## <a name="partial-page-updates"></a>Teilupdates für Seiten

Das sichtbarste Feature von ASP.NET AJAX Extensions ist vielleicht die Möglichkeit, eine Seite teilweise oder inkrementelle Updates vorzunehmen, ohne ein vollständiges Postback an den Server, ohne Änderungen am Code und mit minimalen Markupänderungen. Die Vorteile sind umfangreiche – der Status Ihrer multimedialen Inhalte (wie Adobe Flash, oder klicken Sie mit der Windows-Medien) unverändert, Netzwerkbandbreite werden verringert, und treten des Clients keine das Flimmern in Verbindung mit dem ein Postback.

Die Fähigkeit zum Integrieren von Teilrendering von Seiten ist in ASP.NET mit minimalen Änderungen in Ihr Projekt integriert.

## <a name="walkthrough-integrating-partial-rendering-into-an-existing-project"></a>Exemplarische Vorgehensweise: Partielles Rendering integrieren in einem vorhandenen Projekt


1. Erstellen Sie in Microsoft Visual Studio 2008 ein neues Projekt für die ASP.NET Web Site, indem Sie <em>Datei</em>  <em>- &gt; neu</em>  <em>- &gt; Website</em> , und wählen im Dialogfeld ASP.NET-Website. Sie können sie einen beliebigen Namen, und Sie können es entweder im Dateisystem oder in Internet Information Services (IIS) installieren.
2. Sie werden die leere Standardseite mit grundlegenden ASP.NET-Markup angezeigt (ein serverseitiges Formular und einem `@Page` Richtlinie). Löschen Sie eine Bezeichnung namens `Label1` und eine Schaltfläche namens `Button1` auf die Seite in Form-Elements. Die Eigenschaften von Text kann beliebig festgelegt werden.
3. Doppelklicken Sie in der Entwurfsansicht auf `Button1` um einen Code-Behind-Ereignishandler zu generieren. Legen Sie in diesem Ereignishandler `Label1.Text` , klicken Sie auf die Schaltfläche! sein.

**Codebeispiel 1: Markup für "default.aspx", bevor das Teilrendering aktiviert ist**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample1.aspx)]

**Codebeispiel 2: (Hier gekürzt), in der Datei default.aspx.cs CodeBehind**

[!code-csharp[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample2.cs)]

1. Drücken Sie F5, um Ihre Website zu starten. Visual Studio fordert Sie zum Hinzufügen einer Datei "Web.config", um Debuggen zu aktivieren; Wenn Sie dies tun. Wenn Sie die Schaltfläche klicken, beachten Sie, dass die Seite aktualisiert, zum Ändern des Texts in der Bezeichnung wird und eine kurze Flimmern vorhanden ist, wie Sie die Seite neu gezeichnet wird.
2. Nach dem Schließen Ihres Browserfensters, zurück zu Visual Studio, und klicken Sie auf der Seite "Markup". Scrollen Sie in der Visual Studio-Toolbox, und suchen Sie die Registerkarte mit der Bezeichnung AJAX-Erweiterungen. (Wenn Sie nicht über diese Registerkarte verfügen, da Sie eine ältere Version von AJAX oder Atlas-Erweiterungen verwenden, finden Sie in der exemplarischen Vorgehensweise zum Registrieren von AJAX-Erweiterungen Toolboxelemente weiter unten in diesem Whitepaper, oder installieren Sie die aktuelle Version mit dem Windows Installer zum Herunterladen von der Website).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image2.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image1.png)

([Klicken Sie, um das Bild in voller Größe anzeigen](understanding-partial-page-updates-with-asp-net-ajax/_static/image3.png))


1. <em>Bekanntes Problem:</em>bei der Installation von Visual Studio 2008 auf einem Computer, dem bereits Visual Studio 2005, die mit ASP.NET 2.0 AJAX Extensions installiert ist, wird Visual Studio 2008 Toolboxelemente AJAX-Erweiterungen importiert. Sie können feststellen, ob dies der Fall ist, mithilfe der QuickInfo der Komponenten; Sie sollten Version 3.5.0.0 angenommen. Wenn sie Version 2.0.0.0 b. z. dann Ihrer alten Toolboxelemente importiert haben, und müssen manuell mit das Dialogfeld "Toolboxelemente auswählen" in Visual Studio importieren. Sie werden zum Hinzufügen von Version 2-Steuerelementen über den Designer.

2. Bevor Sie die `<asp:Label>` Tag beginnt, erstellen Sie eine Zeile aus Leerzeichen, und doppelklicken Sie auf das UpdatePanel-Steuerelement in der Toolbox. Beachten Sie, dass ein neues `@Register` Richtlinie befindet sich am oberen Rand der Seite gibt an, dass Steuerelemente innerhalb des System.Web.UI-Namespace mit importiert werden soll die `asp:` Präfix.
3. Ziehen Sie das schließende `</asp:UpdatePanel>` tag nach dem Ende des Button-Element, sodass das Element mit der Bezeichnung und Schaltflächen-Steuerelemente, die umschlossen wohlgeformt ist.
4. Nach dem öffnenden `<asp:UpdatePanel>` markieren, öffnen ein neues Tag zu beginnen. Beachten Sie, dass IntelliSense mit den beiden Optionen aufgefordert werden. In diesem Fall erstellen Sie eine `<ContentTemplate>` Tag. Achten Sie darauf, dass Sie dieses Tag für Ihre Bezeichnung und eine Schaltfläche zu wrappen, sodass das Markup wohlgeformt ist.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image5.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image4.png)

([Klicken Sie, um das Bild in voller Größe anzeigen](understanding-partial-page-updates-with-asp-net-ajax/_static/image6.png))


1. An einer beliebigen Stelle innerhalb der `<form>` Element umfassen ein ScriptManager-Steuerelement durch Doppelklicken auf die `ScriptManager` Element in der Toolbox.
2. Bearbeiten der `<asp:ScriptManager>` kennzeichnen, sodass sie das Attribut enthält `EnablePartialRendering= true`.

**Codebeispiel 3: Markup für "default.aspx", bei partiellem Rendering aktiviert**

[!code-aspx[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample3.aspx)]

1. Öffnen Sie die Datei "Web.config". Beachten Sie, dass Visual Studio automatisch ein Verweis Kompilierung System.Web.Extensions.dll hinzugefügt wurde.

1. Was ist neu in Visual Studio 2008: Die Datei "Web.config", die mit der ASP.NET Web Site Projektvorlagen automatisch alle erforderlichen Verweise auf die ASP.NET AJAX-Erweiterungen enthält, und enthält stammen kommentierten Abschnitten von Konfigurationsinformationen, die zusätzliche aktivieren nicht kommentiert werden können die Funktionalität. Visual Studio 2005 mussten ähnliche Vorlagen aus, wenn ASP.NET 2.0 AJAX Extensions installiert wurden. Die AJAX-Erweiterungen sind jedoch in Visual Studio 2008, melden Sie sich in der Standardeinstellung (d. h. sie wird standardmäßig verwiesen, aber als Verweise entfernt werden kann).


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image8.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image7.png)

([Klicken Sie, um das Bild in voller Größe anzeigen](understanding-partial-page-updates-with-asp-net-ajax/_static/image9.png))


1. Drücken Sie F5, um Ihre Website zu starten. Beachten Sie, das keine Änderungen am Quellcode wurden zur Unterstützung von partielle Seitenerzeugung erforderlich – nur Markup wurde geändert.

Wenn Sie Ihre Website starten, sehen Sie sich, dass ein partielles Rendering ist jetzt aktiviert, da beim Klicken auf die Schaltfläche es keine Flimmern werden, noch wird jede Änderung in der Bildlaufposition Seite (in diesem Beispiel wird nicht, die veranschaulicht) vorhanden sein. Würden Sie die gerenderte Quellcode der Seite betrachten, nach dem Klicken auf die Schaltfläche, wird es zu bestätigen, dass in der Tat ein Postback ist nicht aufgetreten: der ursprüngliche Bezeichnungstext weiterhin Teil Quellmarkup, und die Bezeichnung über JavaScript geändert hat.

Visual Studio 2008 wird nicht angezeigt, mit einer vordefinierten Vorlage für ein ASP.NET AJAX-aktivierten Websites stammen. Allerdings gab es eine solche Vorlage in Visual Studio 2005 Visual Studio 2005 und ASP.NET 2.0 AJAX Extensions installiert wurden. Folglich werden Konfigurieren einer Website ein, und starten mit der Vorlage für AJAX-Website sogar noch einfacher, Sie wahrscheinlich, wie die Vorlage eine voll funktionsfähige web.config-Datei, die (unterstützen alle ASP.NET AJAX-Erweiterungen, einschließlich des Zugriffs für Webdienste enthalten soll und JSON-Serialisierung - JavaScript Object Notation) und ein UpdatePanel und ContentTemplate innerhalb der Web Forms-Hauptseite standardmäßig enthalten. Aktivieren des partiellen Renderings mit diese Standardseite ist so einfach wie unter neuen Gesichtspunkten Schritt 10 dieser exemplarischen Vorgehensweise und Steuerelemente auf der Seite.

## <a name="the-scriptmanager-control"></a>Das ScriptManager-Steuerelement

## <a name="scriptmanager-control-reference"></a>ScriptManager-Steuerelement-Referenz

Markup-fähigen Eigenschaften:

| **Eigenschaftenname** | **Type** | **Beschreibung** |
| --- | --- | --- |
| AllowCustomErrors-Umleitung | Bool | Gibt an, ob der benutzerdefinierte Fehlerabschnitt der Datei "Web.config" zu verwenden, um Fehler zu behandeln. |
| AsyncPostBackError-Nachricht | Zeichenfolge | Übernimmt oder bestimmt die Fehlermeldung, die an den Client gesendet werden, wenn ein Fehler ausgelöst wird. |
| AsyncPostBack-Timeout | Int32 | Ruft ab, oder Festlegen der Standarddauer, die eine Zeitdauer, die ein Client, für die asynchrone Anforderung zum Abschließen warten soll. |
| EnableScript-Globalization | Bool | Ruft ab oder legt fest, ob die Skript-Globalisierung aktiviert ist. |
| EnableScript-Localization | Bool | Ruft ab oder legt fest, ob aktiviert ist. |
| ScriptLoadTimeout | Int32 | Bestimmt die Anzahl von Sekunden zum Laden von Skripts in den Client zulässig |
| ScriptMode | Enum (Auto, Debug, Release, erben) | Ruft ab oder legt fest, ob zum Rendern von Veröffentlichungsversionen von Skripts |
| ScriptPath | Zeichenfolge | Übernimmt oder bestimmt den Root-Pfad zum Speicherort der Skriptdateien, die an den Client gesendet werden. |

Nur Eigenschaften:

| **Eigenschaftenname** | **Type** | **Beschreibung** |
| --- | --- | --- |
| AuthenticationService | AuthenticationService-Manager | Ruft Details zu den ASP.NET-Authentifizierungsdienst-Proxy, der an den Client gesendet wird. |
| IsDebuggingEnabled | Bool | Ruft ab, ob Skripts aus, und Debuggen von Code aktiviert ist. |
| IsInAsyncPostback | Bool | Ruft ab, ob die Seite derzeit in einer asynchronen Postback-Anforderung ist. |
| ProfileService | ProfileService-Manager | Ruft Details zu den ASP.NET Profilerstellung Dienstproxy, der an den Client gesendet werden. |
| Skripts | Auflistung&lt;Skriptverweis&gt; | Ruft eine Auflistung von Skriptverweisen, die an den Client gesendet werden. |
| Dienste | Auflistung&lt;Dienstverweis&gt; | Ruft eine Auflistung von Web Service Proxyverweisen, die an den Client gesendet werden. |
| SupportsPartialRendering | Bool | Ruft ab, ob der aktuelle Client partielles Rendering unterstützt. Wenn diese Eigenschaft gibt **"false"**, dann alle Seitenanforderungen standard Postbacks. |

Öffentliche Methoden:

| **Methodenname** | **Type** | **Beschreibung** |
| --- | --- | --- |
| SetFocus(string) | Void | Legt den Fokus des Clients zu einem bestimmten Steuerelement fest, wenn die Anforderung abgeschlossen wurde. |

Nachfolger von Markup:

| **Tag** | **Beschreibung** |
| --- | --- |
| &lt;AuthenticationService&gt; | Enthält Details über den Proxy mit dem Authentifizierungsdienst ASP.NET. |
| &lt;ProfileService&gt; | Enthält Details zu den Proxy, um den Dienst für die ASP.NET-PROFILERSTELLUNG. |
| &lt;Skripts&gt; | Stellt zusätzliche Skriptverweise bereit. |
| &lt;asp:ScriptReference&gt; | Gibt einen bestimmten Skriptverweis an. |
| &lt;Dienst&gt; | Enthält zusätzliche Web Service-Verweise die Webdienstproxy-Klassen generiert hat. |
| &lt;asp:ServiceReference&gt; | Gibt einen bestimmten Webdienst-Verweis an. |

Das ScriptManager-Steuerelement ist der Kern des essential ASP.NET AJAX Extensions. Es ermöglicht den Zugriff auf die Skriptbibliothek (einschließlich des Typsystems umfangreichen Client-seitige Skript), partielles Rendering unterstützt und bietet umfangreiche Unterstützung für zusätzliche ASP.NET-Dienste (z. B. Authentifizierung und die profilerstellung, sondern auch andere Webdienste). Das ScriptManager-Steuerelement bietet auch Unterstützung von Globalisierung und Lokalisierung für die Clientskripts.

## <a name="providing-alternative-and-supplemental-scripts"></a>Bereitstellen von alternativen und zusätzliche Skripts

Während Microsoft ASP.NET 2.0 AJAX Extensions den gesamten Skriptcode in sowohl für Debug-umfassen und release Editionen als Ressourcen in den verwiesenen Assemblys eingebettet, können sich Entwickler uneingeschränkt Umleiten von ScriptManager auf benutzerdefinierte Skripts als auch registrieren zusätzliche erforderlichen Skripts.

Um die standardbindung für die Skripts in der Regel enthalten (z. B. die Sys.WebForms-Namespace und den benutzerdefinierten typisierungssystem unterstützen) zu überschreiben, können Sie für die Registrieren der `ResolveScriptReference` Ereignis des ScriptManager-Klasse. Wenn diese Methode aufgerufen wird, hat der Ereignishandler die Möglichkeit, den Pfad zu der fraglichen Skriptdatei zu ändern; der Skript-Manager sendet dann eine andere oder benutzerdefinierte Kopie der Skripts an den Client.

Darüber hinaus die Skriptverweise (dargestellt durch die `ScriptReference` Klasse) können programmgesteuert oder über das Markup eingefügt werden. Zu diesem Zweck entweder programmgesteuert ändern der `ScriptManager.Scripts` -Auflistung, oder schließen Sie `<asp:ScriptReference>` -Tags unter der `<Scripts>` -Tag, das das ScriptManager-Steuerelement auf oberster Ebene untergeordnet ist.

## <a name="custom-error-handling-for-updatepanels"></a>Benutzerdefinierte Fehlerbehandlung für UpdatePanels

Obwohl Updates durch Trigger, die anhand des UpdatePanel-Steuerelemente behandelt werden, wird die Unterstützung für die Fehlerbehandlung und benutzerdefinierten Fehlermeldungen von Instanz einer Seite ScriptManager-Steuerelements behandelt. Dies erfolgt durch Verfügbarmachen eines Ereignisses `AsyncPostBackError`, auf die Seite kann dann benutzerdefinierte Ausnahmebehandlung Logik bereitstellen.

Durch die Nutzung des Ereignisses AsyncPostBackError, Sie können angeben, die `AsyncPostBackErrorMessage` -Eigenschaft, die klicken Sie dann einem Warnfeld, nach dem Abschluss des Rückrufs ausgelöst wird.

Der clientseitigen Anpassung kann auch anstelle der standardmäßigen Warnfeld; Sie möchten z. B. eine benutzerdefinierte anzeigen `<div>` -Element, anstatt das modale Dialogfeld des Standard-Browser. In diesem Fall können Sie den Fehler im Clientskript behandeln:

**Codebeispiel 5: Client-seitige Skript zum Anzeigen der benutzerdefinierte Fehler**

[!code-html[Main](understanding-partial-page-updates-with-asp-net-ajax/samples/sample4.html)]

Das obige Skript registriert einen Rückruf ganz einfach mit der clientseitigen AJAX-Laufzeit für, wenn die asynchrone Anforderung abgeschlossen wurde. Es wird dann überprüft, ob ein Fehler gemeldet wurde, und wenn dies der Fall ist, verarbeitet die Details des Zertifikats, schließlich, der angibt, an die Laufzeit, dass der Fehler in benutzerdefinierten Skripts behandelt wurde.

## <a name="globalization-and-localization-support"></a>Globalisierung und Lokalisierungsunterstützung

Das ScriptManager-Steuerelement bietet umfangreiche Unterstützung für die Lokalisierung von Skriptzeichenfolgen und Komponenten der Benutzeroberfläche; Allerdings ist dieses Thema nicht im Rahmen dieses Whitepapers. Weitere Informationen finden Sie im Whitepaper, Globalisierungsunterstützung in ASP.NET AJAX-Erweiterungen.

## <a name="the-updatepanel-control"></a>Das UpdatePanel-Steuerelement

## <a name="updatepanel-control-reference"></a>UpdatePanel-Steuerelement-Referenz

Markup-fähigen Eigenschaften:

| **Eigenschaftenname** | **Type** | **Beschreibung** |
| --- | --- | --- |
| ChildrenAsTriggers | bool | Gibt an, ob untergeordnete Steuerelemente automatisch aktualisieren beim Postback aufrufen. |
| RenderMode | Enum ("Block", "Inline") | Gibt an, wie der Inhalt visuell dargestellt wird. |
| UpdateMode | Enum ("immer", "Conditional") | Gibt an, ob das UpdatePanel immer während einer partiellen Rendering aktualisiert wird oder es erst aktualisiert werden, wenn ein Trigger erreicht wird. |

Nur Eigenschaften:

| **Eigenschaftenname** | **Type** | **Beschreibung** |
| --- | --- | --- |
| IsInPartialRendering | bool | Ruft ab, ob das UpdatePanel partielles Rendering für die aktuelle Anforderung unterstützt werden. |
| ContentTemplate | ITemplate | Ruft das markupvorlage für die updateanforderung ab. |
| ContentTemplateContainer | Steuerelement | Ruft die programmgesteuerte Vorlage für die updateanforderung ab. |
| Trigger | UpdatePanel - TriggerCollection | Ruft die Liste von Triggern, die mit der aktuellen UpdatePanel-Steuerelement verknüpft ist. |

Öffentliche Methoden:

| **Methodenname** | **Type** | **Beschreibung** |
| --- | --- | --- |
| Update() | Void | Der angegebene UpdatePanel aktualisiert programmgesteuert. Ermöglicht es eine Anforderung einer partiellen Rendering von UpdatePanel andernfalls ohne Trigger auslösen. |

Nachfolger von Markup:

| **Tag** | **Beschreibung** |
| --- | --- |
| &lt;ContentTemplate&gt; | Gibt das Markup verwendet werden, um das Ergebnis für partielles Rendering zu rendern. Untergeordnetes Element des &lt;Asp: UpdatePanel&gt;. |
| &lt;Trigger&gt; | Gibt eine Auflistung von *n* Kontrollen hinsichtlich dieser UpdatePanel aktualisiert. Untergeordnetes Element des &lt;Asp: UpdatePanel&gt;. |
| &lt;asp:AsyncPostBackTrigger&gt; | Gibt an, einen Trigger, der eine partielle Rendering für das angegebene UpdatePanel aufruft. Dies kann oder möglicherweise ein Steuerelement ein untergeordnetes Element des betreffenden UpdatePanel nicht. Präzise, mit dem Ereignisnamen. Untergeordnetes Element des &lt;Trigger&gt;. |
| &lt;asp:PostBackTrigger&gt; | Gibt ein Steuerelement, das bewirkt, die gesamte Seite dass aktualisieren. Dies kann oder möglicherweise ein Steuerelement ein untergeordnetes Element des betreffenden UpdatePanel nicht. Präzise, auf das Objekt. Untergeordnetes Element des &lt;Trigger&gt;. |

Die `UpdatePanel` ist das Steuerelement, das den Inhalt des serverseitigen begrenzt, die in die partielle Seitenerzeugung-Funktionalität von den AJAX-Erweiterungen einbezogen wird. Es gibt keine Beschränkung der Anzahl der UpdatePanel-Steuerelemente, die auf einer Seite sein können, und können geschachtelt werden. Jede UpdatePanel ist isoliert, sodass jeweils unabhängig voneinander arbeiten kann (Sie können zwei UpdatePanels die gleichzeitige Ausführung, Rendern verschiedene Teile der Seite unabhängig von der Postback der Seite haben).

UpdatePanel steuern in erster Linie die Angebote mit Control-Trigger – standardmäßig alle enthaltenen ein UpdatePanel Steuerelement `ContentTemplate` einen Postback erstellt, die als Trigger für das UpdatePanel registriert ist. Dies bedeutet, dass UpdatePanel kann mit der standardmäßigen datengebundene Steuerelemente (z. B. GridView) mit Benutzersteuerelementen arbeiten, und sie im Skript so programmiert werden können.

Standardmäßig können Sie, wenn ein renderobjekt Teilseite ausgelöst wird, alle UpdatePanel-Steuerelemente auf der Seite aktualisiert werden, unabhängig davon, ob das UpdatePanel Steuerelemente Trigger für eine solche Aktion definiert. Beispielsweise werden, wenn ein UpdatePanel ein Schaltflächen-Steuerelement definiert, und dieses Schaltflächen-Steuerelement geklickt wird, alle UpdatePanel-Steuerelemente auf dieser Seite wird standardmäßig aktualisiert. Dies liegt daran, in der Standardeinstellung die `UpdateMode` von UpdatePanel-Eigenschaftensatz auf `Always`. Alternativ können Sie die UpdateMode-Eigenschaft festlegen, um `Conditional`, was bedeutet, dass das UpdatePanel wird nur aktualisiert werden, wenn Sie ein bestimmten Trigger erreicht wird.

## <a name="custom-control-notes"></a>Benutzerdefiniertes Steuerelement Anmerkungen zu dieser Version

Einem UpdatePanel-Steuerelement kann Benutzersteuerelement oder ein benutzerdefiniertes Steuerelement hinzugefügt werden; Allerdings muss die Seite, auf denen diese Steuerelemente enthalten sind, auch ein ScriptManager-Steuerelement, mit der Eigenschaft EnablePartialRendering auf enthalten **"true"**.

Eine Möglichkeit, in denen Sie möglicherweise berücksichtigen für diese beim Verwenden von benutzerdefinierten Websteuerelementen besteht die geschützte überschreiben `CreateChildControls()` Methode der `CompositeControl` Klasse. Auf diese Weise können Sie zwischen der untergeordneten Elemente des Steuerelements und der Außenwelt einem UpdatePanel-Steuerelement einfügen, wenn Sie feststellen, dass die Seite mit partiellem Rendering unterstützt; Andernfalls können Sie die untergeordneten Steuerelemente einfach zusätzliche, in einem Container `Control` Instanz.

## <a name="updatepanel-considerations"></a>UpdatePanel-Überlegungen

UpdatePanel arbeitet als eine Blackbox, ASP.NET-Postbacks innerhalb des Kontexts von einem JavaScript-XMLHttpRequest umschließen. Es gibt jedoch wichtige Leistungsaspekte zu, sowohl im Hinblick auf Verhalten und die Geschwindigkeit berücksichtigen. Zum Verständnis der Funktionsweise von UpdatePanel, also am besten können Sie entscheiden, wann die Verwendung geeignet ist, sollten Sie den Austausch von AJAX untersuchen. Im folgenden Beispiel wird eine vorhandene Website und Mozilla Firefox mit der Firebug-Erweiterung (Firebug erfasst XMLHttpRequest-Daten).

Erwägen Sie ein Formular, deren, u. a. ein Textfeld für die Postleitzahl ein Feld für Stadt und Bundesstaat in einem Formular oder Steuerelement Auffüllen soll. Dieses Formular erfasst letztlich Informationen zur Gruppenmitgliedschaft, einschließlich Name, Adresse und Kontaktinformationen des Benutzers. Es gibt viele Überlegungen zum Entwurf, basierend auf den Anforderungen eines bestimmten Projekts berücksichtigen.


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image11.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image10.png)

([Klicken Sie, um das Bild in voller Größe anzeigen](understanding-partial-page-updates-with-asp-net-ajax/_static/image12.png))


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image14.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image13.png)

([Klicken Sie, um das Bild in voller Größe anzeigen](understanding-partial-page-updates-with-asp-net-ajax/_static/image15.png))


In der ursprünglichen Iteration dieser Anwendung wurde ein Steuerelement erstellt, die die Registrierung Benutzerdaten, einschließlich die PLZ, Stadt und der Status während des gesamten Entwicklungsprozesses aufgenommen. Das gesamte Steuerelement wurde in einem UpdatePanel-Steuerelement umschlossen und ein Webformular abgelegt. Wenn die Postleitzahl, die vom Benutzer eingegeben wird, erkennt das UpdatePanel das Ereignis (das entsprechende TextChanged-Ereignis in das Back-End, entweder durch Angeben von Triggern oder mit die ChildrenAsTriggers-Eigenschaft auf "true" festgelegt). AJAX sendet alle Felder in UpdatePanel, was, wie Sie von FireBug erfasst (Siehe das Diagramm auf der rechten Seite).

Wie in die Bildschirmaufnahme angegeben, werden die Werte jedes innerhalb von UpdatePanel-Steuerelement übermittelt (in diesem Fall sie sind alle leer), sowie das Feld Ansichtszustand. Alles in allem ist mehr als 9 kb Daten gesendet, wenn tatsächlich nur fünf Byte Daten für diese bestimmte Anforderung erforderlich waren. Die Antwort ist sogar noch sehr groß: 57kb erhält insgesamt an den Client einfach, um ein Textfeld und einer Dropdown-Feld zu aktualisieren.

Es kann auch sein, von Interesse sind, um festzustellen, wie die Darstellung von ASP.NET AJAX aktualisiert. Das UpdatePanel Anforderung zum Aktualisieren der Antwortbereich wird in der Konsole Firebug auf der linken Seite angezeigt; Es ist eine speziell formuliert senkrechte Striche getrennte Zeichenfolge, die durch das Clientskript unterteilt ist, und klicken Sie dann auf der Seite wieder zusammengefügt werden. Insbesondere ASP.NET AJAX legt die *InnerHTML* -Eigenschaft des HTML-Elements auf dem Client, der Ihre UpdatePanel darstellt. Der Browser das DOM erneut generieren, besteht eine geringfügige Verzögerung kommen, abhängig vom Umfang der Informationen, die verarbeitet werden muss.

Die erneute Generierung des DOM löst eine Reihe von zusätzlichen Problemen:


[![](understanding-partial-page-updates-with-asp-net-ajax/_static/image17.png)](understanding-partial-page-updates-with-asp-net-ajax/_static/image16.png)

([Klicken Sie, um das Bild in voller Größe anzeigen](understanding-partial-page-updates-with-asp-net-ajax/_static/image18.png))


- Wenn das HTML-Element in UpdatePanel, was ist, verloren den Fokus. Also für Benutzer, die drücken die Tab-Taste, um das Textfeld für die Postleitzahl zu beenden, hätten ihre nächste Ziel im Textfeld "City". Jedoch nach der Anzeige von UpdatePanel aktualisiert werden, die Form den Fokus nicht mehr wären, und Tab-Taste drücken hätte begonnen markieren die Elemente des Ziels (z. B. Links).
- Wenn alle Arten von benutzerdefinierten Client-seitige Skript verwendet wird, dass greift auf DOM-Elemente, die Verweise von den Funktionen beibehalten kann nach der ein partielles Postback außer Kraft.

UpdatePanels sind nicht an die Catch-All-Lösungen gedacht. Stattdessen sie eine schnelle Lösung für bestimmte Situationen, die zum Erstellen von Prototypen, kleine Steuern von Updates, einschließlich bereitzustellen und eine vertraute Schnittstelle für ASP.NET-Entwickler, die werden können, mit dem Objektmodell von .NET vertraut sind, aber es ist daher weniger mit dem DOM Es gibt eine Reihe von alternativen, die eine bessere Leistung, je nach Anwendungsszenario führen kann:

- Erwägen Sie die Verwendung von PageMethods und JSON (JavaScript Object Notation) ermöglicht dem Entwickler, statische Methoden auf einer Seite aufzurufen, als ob ein Aufruf des Webdiensts war aufgerufen wird. Da die Methoden statisch sind, ist kein Zustand erforderlich; der Aufrufer Skript stellt die Parameter, und das Ergebnis wird asynchron zurückgegeben.
- Wenn ein einzelnes Steuerelement muss an mehreren Stellen innerhalb einer Anwendung verwendet werden, sollten Sie einen Webdienst und JSON in Betracht ziehen. Dies erneut ist sehr spezielle unkompliziert und asynchron ausgeführt.

Integrieren der Funktionen, die über Webdienste oder Seitenmethoden hat auch Nachteile. Zuallererst tendenziell ASP.NET-Entwickler in der Regel kleine Komponenten der Funktionalität in Benutzersteuerelemente (ASCX-Dateien) zu erstellen. Seitenmethoden können nicht in diesen Dateien gehostet werden. Sie müssen innerhalb der eigentlichen ASPX-Seite-Klasse gehostet werden. Webdienste müssen innerhalb der ASMX-Klasse auf ähnliche Weise gehostet werden. Abhängig von der Anwendung diese Architektur dem Prinzip der einzigen Verantwortung, verstößt gegen, da die Funktionalität für eine einzelne Komponente jetzt auf mindestens zwei physische Komponenten verteilt wird, die nur wenig oder keine zusammenhängende Ties haben kann.

Wenn eine Anwendung erfordert, dass UpdatePanels verwendet werden, sollte schließlich die folgenden Richtlinien bei der Problembehandlung und Wartung unterstützen.

- **Schachteln Sie UpdatePanels, so wenig wie möglich ist, nicht nur in-Einheiten, sondern auch in Einheiten von Code.** Beispielsweise ist die mit einem UpdatePanel-Steuerelement auf einer Seite, die ein Steuerelement umschließt, während das Steuerelement auch ein UpdatePanel, die ein anderes Steuerelement, die einem UpdatePanel-Steuerelement enthält enthält enthält, Cross-Einheit Schachtelung. Dadurch wird zu deaktivieren, welche Elemente aktualisiert werden soll, und verhindert, dass unerwartete Aktualisierungen an untergeordneten UpdatePanels.
- **Behalten Sie die *ChildrenAsTriggers* Eigenschaft auf "false" festgelegt und explizit festlegen, Auslösen von Ereignissen.** Nutzung der `<Triggers>` Auflistung ist ein viel klarer Weg zum Behandeln von Ereignissen, und verhindern möglicherweise unerwartetes Verhalten, hilft Ihnen bei der Wartungstasks, und erzwingen ein Entwickler nach einem Ereignis teilnehmen.
- **Verwenden Sie die kleinste mögliche Einheit, um die Funktionalität zu erzielen.** Wie bereits erwähnt, dass nur das absolute Minimum auf dem Server, insgesamt Verarbeitungs- und der Speicherbedarf der Exchange-Client / Server erforderliche Zeit reduziert wird, in der Beschreibung des Diensts postal Code umschließen, Verbessern der Leistung.

## <a name="the-updateprogress-control"></a>Das UpdateProgress-Steuerelement

## <a name="updateprogress-control-reference"></a>Referenz für Steuerelement "UpdateProgress"

Markup-fähigen Eigenschaften:

| **Eigenschaftenname** | **Type** | **Beschreibung** |
| --- | --- | --- |
| AssociatedUpdate-PanelID | Zeichenfolge | Gibt die ID von UpdatePanel, die auf diesem UpdateProgress gemeldet werden sollen. |
| DisplayAfter | Int | Gibt das Timeout in Millisekunden, bevor dieses Steuerelement angezeigt wird, nach dem Beginn der asynchronen Anforderung. |
| DynamicLayout | bool | Gibt an, ob der Status dynamisch gerendert wird. |

Nachfolger von Markup:

| **Tag** | **Beschreibung** |
| --- | --- |
| &lt;ProgressTemplate&gt; | Enthält die Steuerelementvorlage festlegen, die für den Inhalt, der mit diesem Steuerelement angezeigt wird. |

Das UpdateProgress-Steuerelement bietet Ihnen ein Maß für Feedback Ihrer Benutzer relevante zu halten, während die notwendigen Schritte zum Transport mit dem Server ausführen. Dies kann helfen, Ihre Benutzer wissen, dass Sie etwas tun, obwohl es offensichtlich, möglicherweise nicht besonders, da die meisten Benutzer die Seite aktualisieren, und sehen die Statusleiste markieren verwendet werden.

Bemerkt können UpdateProgress-Steuerelementen an einer beliebigen Stelle auf einer Seitenhierarchie angezeigt. Aber postbacks in Fällen, in denen ein partielles Postback initiiert wird (wobei einem UpdatePanel-Steuerelement in ein anderes UpdatePanel geschachtelt ist), von einem untergeordneten UpdatePanel, diesen Trigger das untergeordnete Element UpdatePanel UpdateProgress Vorlagen angezeigt werden für das untergeordnete Element führt dazu, dass UpdatePanel als auch das übergeordnete Element UpdatePanel. Aber wenn der Trigger direkt untergeordnetes Element des übergeordneten Elements UpdatePanel ist, und klicken Sie dann nur die UpdateProgress-Vorlagen, die mit dem übergeordneten Element angezeigt werden.

## <a name="summary"></a>Zusammenfassung

Die Microsoft ASP.NET AJAX-Erweiterungen sind komplexe Produkte entwickelt, um bei der Web-Inhalte leichter zugänglich machen und um eine umfangreichere Benutzeroberfläche für Ihre Webanwendungen bereitzustellen. Als Teil des ASP.NET AJAX Extensions, das Rendern von Steuerelementen teilupdates für Seiten, sind einschließlich der ScriptManager, dem UpdatePanel- und UpdateProgress-Steuerelementen einige der besten Komponenten des Toolkits.

Die ScriptManager-Komponente integriert ist die Bereitstellung des JavaScript-Clients für die Erweiterungen sowie können die verschiedenen Server- und clientseitigen Komponenten zusammen mit der Investition in die minimale Entwicklung funktioniert.

Das UpdatePanel-Steuerelement ist das offensichtlich Magic-Feld - Markup in UpdatePanel, was serverseitige Codebehind haben und eine seitenaktualisierung nicht auslösen kann. UpdatePanel-Steuerelemente können geschachtelt werden, und Sie können Steuerelemente in anderen UpdatePanels abhängig sein. Standardmäßig behandeln UpdatePanels Postbacks aufgerufen, indem die untergeordneten Steuerelemente, obwohl diese Funktionalität fein, entweder deklarativ oder programmgesteuert optimiert werden kann.

Wenn Sie das UpdatePanel-Steuerelement zu verwenden, sollten Entwickler die Auswirkungen auf die Leistung bewusst sein, die möglicherweise auftreten können. Mögliche Alternativen enthalten Webdienste und Seitenmethoden, obwohl der Entwurf der Anwendung berücksichtigt werden sollten.

Die UpdateProgress, die Steuerelement dem Benutzer ermöglicht wissen, dass sie oder er nicht ignoriert wird wird, und dass die Anforderung im Hintergrund bei der Seite vor sich geht andernfalls nicht im Leerlauf zum Reagieren auf Benutzereingabe. Darüber hinaus die Möglichkeit, Ergebnisse für partielles Rendering abgebrochen.

Diese Tools zusammen unterstützen, erstellen eine umfassende und nahtlose benutzererfahrung funktioniert für den Benutzer weniger offensichtlich und weniger Unterbrechen von Workflows.

## <a name="bio"></a>Biografie

Scott Cate arbeitet mit Microsoft-Web-Technologien seit 1997 und ist Vorsitzender der myKB.com ([www.myKB.com](http://www.myKB.com)), in dem er spezialisiert sich auf das Schreiben von ASP.NET basierende Anwendungen, die mit dem Schwerpunkt Knowledge Base-softwarelösungen. Scott hergestellt werden kann, per e-Mail unter [ scott.cate@myKB.com ](mailto:scott.cate@myKB.com) oder seinen Blog unter [ScottCate.com](http://ScottCate.com)

> [!div class="step-by-step"]
> [Nächste](understanding-asp-net-ajax-updatepanel-triggers.md)
