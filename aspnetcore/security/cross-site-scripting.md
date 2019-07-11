---
title: Verhindern von Cross-Site Scripting (XSS) in ASP.NET Core
author: rick-anderson
description: Informationen Sie zu Cross-Site Scripting (XSS) und Techniken zur Behebung dieses Sicherheitsrisiko in ASP.NET Core-Apps.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1e9e988be68313cfd493832519c1be89335d6e48
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815212"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Verhindern von Cross-Site Scripting (XSS) in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Cross-Site Scripting (XSS) ist ein Sicherheitsrisiko, wodurch einen Angreifer, clientseitige Skripts (in der Regel "JavaScript") in Webseiten zu platzieren. Wenn andere Benutzer betroffene Seiten der Angreifer Skripts ausführen laden, aktivieren Angreifer zum Diebstahl von Cookies und Sitzungstoken, ändern Sie den Inhalt der Webseite durch die DOM-Manipulation oder umleiten Sie Browser zu einer anderen Seite. XSS-Sicherheitsrisiken treten im Allgemeinen als eine Anwendung Benutzereingaben ohne Validierung, Codierung entkommen auf einer Seite ausgegeben.

## <a name="protecting-your-application-against-xss"></a>Zum Schützen Ihrer Anwendung vor XSS

An einer grundlegenden Ebene XSS funktioniert, indem Sie Ihre Anwendung in das Einfügen von Konten eine `<script>` Tag in die gerenderte Seite oder durch Einfügen einer `On*` Ereignis in ein Element. Entwickler sollten die folgenden Schritte für die Verhinderung von verwenden, um zu vermeiden, die Einführung von XSS in ihrer Anwendung.

1. Nicht vertrauenswürdige Daten abgelegt nie Ihrer HTML-Eingaben, es sei denn, Sie die restlichen Schritte befolgen. Nicht vertrauenswürdige Daten ist keine Daten, die von Angreifern, HTML-Formulareingaben, Abfragezeichenfolgen, HTTP-Header selbst Daten stammen aus einer Datenbank, wie ein Angreifer möglicherweise auf Ihre Datenbank zu verletzen, auch wenn sie Ihre Anwendung verletzen können nicht gesteuert werden können.

2. Stellen Sie sicher, dass es sich um HTML-codiert ist, vor dem Einfügen von nicht vertrauenswürdiger Daten innerhalb eines HTML-Elements. HTML-Codierung die Zeichen wie z. B. dauert &lt; und ändert diese in eine sichere Form wie &amp;Lt;

3. Vor nicht vertrauenswürdigen Daten in ein HTML-Attribut sicher, dass sie HTML-codiert. HTML-attributcodierung eine Obermenge der HTML-Codierung codiert und zusätzliche Zeichen wie z. B. "und".

4. Platzieren Sie die Daten vor dem Einfügen von nicht vertrauenswürdiger Daten in JavaScript in einem HTML-Element, dessen Inhalt Sie zur Laufzeit abrufen. Ist dies nicht möglich, dann sicher, dass die Daten codiert JavaScript. JavaScript-Codierung nimmt gefährliche Zeichen für JavaScript und z. B. durch ihre hexadezimal ersetzt &lt; codiert werden sollen, als `\u003C`.

5. Stellen Sie sicher, dass er URL-codiert ist, ehe Sie nicht vertrauenswürdige Daten in eine URL-Abfragezeichenfolge.

## <a name="html-encoding-using-razor"></a>HTML-Codierung, die mithilfe von Razor

Die Razor-Engine, die automatisch verwendet, die in MVC codiert alle Ausgabe stammt von Variablen auf, es sei denn, Sie hart arbeiten, um zu verhindern, dass sie auf diese Weise. Bei jeder Verwendung von HTML-Attribut Codierungsregeln verwendet die *@* Richtlinie. Als HTML ist das attributcodierung eine Obermenge der HTML-Codierung, die Dies bedeutet, dass Sie nicht darum kümmern, selbst, ob Sie HTML-Codierung oder HTML-attributcodierung verwenden sollten. Achten Sie darauf, nur @ in einem HTML-Kontext nicht verwendet werden, wenn versucht wird, nicht vertrauenswürdige Eingaben direkt in JavaScript einzufügen. Taghilfsprogramme werden auch Eingabe codieren, die Sie Tag-Parametern verwenden.

Führen Sie die folgende Razor-Ansicht:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

Diese Sicht gibt den Inhalt der *UntrustedInput* Variable. Diese Variable enthält einige Zeichen, die in der XSS-Angriffen, d. h. verwendet werden &lt;, "und &gt;. Untersuchen die Quelle zeigt die gerenderte Ausgabe als codiert:

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC bietet eine `HtmlString` Klasse, die automatisch bei Ausgabe codiert ist nicht. Dies sollte nie in Kombination mit nicht vertrauenswürdigen Eingaben verwendet werden, wie diese eine XSS-Schwachstelle verfügbar macht.

## <a name="javascript-encoding-using-razor"></a>JavaScript Codierung Razor

Möglicherweise gibt es Zeiten, die zum Einfügen eines Werts in JavaScript in der Ansicht verarbeitet werden sollen. Hierfür gibt es zwei Möglichkeiten. Die sicherste Möglichkeit zum Einfügen von Werten ist den Wert in einem Datenattribut eines Tags und in JavaScript abrufen. Beispiel:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Dadurch wird der folgenden HTML-Code generiert.

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

Die bei seiner Ausführung wird Folgendes dargestellt:

```none
<"123">
   <"123">
   ```

Sie können den JavaScript-Encoder auch direkt aufrufen:

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
   ```

Dies wird im Browser wie folgt dargestellt:

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> Verketten Sie keine nicht vertrauenswürdige Eingaben in JavaScript zum Erstellen von DOM-Elementen. Verwenden Sie `createElement()` und Zuweisen von Eigenschaftswerten entsprechend z. B. `node.TextContent=`, oder verwenden Sie `element.SetAttribute()` / `element[attribute]=` machen sich andernfalls DOM-basierte XSS.

## <a name="accessing-encoders-in-code"></a>Beim Zugriff auf den Encoder in code

Die HTML, JavaScript und URL-Encoder für Ihren Code auf zwei Arten verfügbar sind, fügen Sie sie über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) oder Sie können die Standard-Encoder, die innerhalb der `System.Text.Encodings.Web` Namespace. Wenn Sie die Standard-Encoder verwenden, und klicken Sie dann auf Sie angewendet Zeichenbereiche, die als sicher betrachtet wird nicht wirksam: die Standard-Encoder verwenden, die am sichersten Codierungsregeln möglich.

Die konfigurierbaren Encoder mithilfe von DI verwenden Ihre Konstruktoren dauert ein *HtmlEncoder*, *JavaScriptEncoder* und *UrlEncoder* Parameter nach Bedarf. Beispiel:

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>Codierung von URL-Parametern

Wenn Sie eine URL-Abfragezeichenfolge mit nicht vertrauenswürdigen Eingaben als Wert Verwendung erstellen möchten, die `UrlEncoder` zum Codieren des Werts. Ein auf ein Objekt angewendeter

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Nach der Codierung der EncodedValue Variable enthält `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Leerzeichen, Anführungszeichen, Interpunktionszeichen und andere unsichere Zeichen Prozent in ihrer hexadezimalen Wert codiert, z. B. wird ein Leerzeichen % 20.

>[!WARNING]
> Verwenden Sie nicht vertrauenswürdige Eingaben nicht als Teil eines URL-Pfads an. Übergeben Sie nicht vertrauenswürdige Eingaben werden immer als Wert einer Abfragezeichenfolge.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Anpassen der Encoder

Standard-Encoder verwenden Sie eine Sicherungsliste auf den Basic Latin Unicode-Bereich beschränkt, und codieren Sie aller Zeichen außerhalb dieses Bereichs wie ihre entsprechenden Code. Dieses Verhalten wirkt sich auch auf die Razor-TagHelper und HtmlHelper-Rendering aus, da es die Encoder verwendet wird, um Zeichenfolgen auszugeben.

Die Überlegung dahinter ist zum Schutz unbekannt oder zukünftige Browserfehler (vorherige Browserfehler haben gebracht Analyse basierend auf die Verarbeitung von nicht-englische Zeichen). Wenn Ihre Website intensiven Gebrauch von nicht-lateinische Zeichen wie Chinesisch, macht ist Kyrillisch oder andere Personen dies wahrscheinlich nicht das gewünschte Verhalten.

Sie können anpassen, dass die Encoder-Sicherheitslisten Unicode enthält Bereiche, die für Ihre Anwendung während des Starts in entsprechende `ConfigureServices()`.

Z. B. mit der Konfiguration können Sie eine Razor-HtmlHelper wie.

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Wenn Sie die Quelle der Webseite anzeigen sehen Sie sich, dass es wie folgt mit dem chinesische Text codiert gerendert wurde.

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Erweitern Sie die Zeichen, die als behandelt sicher vom Encoder Sie würde fügen die folgende Zeile in der `ConfigureServices()` -Methode in der `startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

In diesem Beispiel wird die Sicherungsliste, um die Unicode-Bereich CjkUnifiedIdeographs einzuschließen. Die gerenderte Ausgabe wäre nun werden.

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Liste mit sicheren Bereiche werden als Unicode-codeübersichten, nicht die Sprachen angegeben. Die [Unicode-Standard](https://unicode.org/) enthält eine Liste der [code Diagramme](https://www.unicode.org/charts/index.html) können Sie das Diagramm, das mit Ihrer Zeichen gefunden. Jeder Encoder, Html, JavaScript und die Url muss separat konfiguriert werden.

> [!NOTE]
> Anpassung der Liste der sicheren wirkt sich nur Encodern, die mithilfe von DI stammen. Wenn Sie direkt einen Encoder über zugreifen `System.Text.Encodings.Web.*Encoder.Default` klicken Sie dann den Standardwert lateinischen nur Listen sicherer Adressen verwendet werden.

## <a name="where-should-encoding-take-place"></a>Wo soll Codierung stattfinden?

Die allgemeinen akzeptiert, dass empfiehlt es sich, dass die Codierung wird zum Zeitpunkt der Ausgabe und die codierte Werte sollten nie in einer Datenbank gespeichert werden. Zum Zeitpunkt der Ausgabe-Codierung, können Sie die Verwendung von Daten, z. B. von HTML in einen Zeichenfolgenwert für die Abfrage zu ändern. Außerdem können Sie Ihre Daten ganz einfach suchen, ohne Werte vor der Suche nach zu codieren und ermöglicht es Ihnen, Änderungen oder Fehlerbehebungen, die versucht, den Encoder zu nutzen.

## <a name="validation-as-an-xss-prevention-technique"></a>Überprüfung als eine XSS-Prevention-Methode

Überprüfung kann ein hilfreiches Tool bei XSS-Angriffen eingeschränkt sein. Beispielsweise wird keine numerische Zeichenfolge, die nur die Zeichen 0-9 enthält einen XSS-Angriff auslösen. Überprüfung wird komplizierter, wenn HTML Benutzereingaben akzeptieren. Analysieren von HTML-Eingaben ist schwierig, wenn nicht sogar unmöglich. Markdown, einen Parser, der eingebetteten HTML-Code, entfernt gekoppelt ist eine sicherere Option für umfangreiche Eingaben angenommen. Niemals zur Validierung von allein verlassen. Immer codieren Sie nicht vertrauenswürdige Eingaben vor der Ausgabe, unabhängig davon, welche eingabeüberprüfungs- oder bereinigungsuntersystem durchgeführt wurde.
