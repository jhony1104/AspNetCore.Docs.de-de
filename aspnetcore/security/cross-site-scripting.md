---
title: Verhindern von Cross-Site Scripting (XSS) in ASP.net Core
author: rick-anderson
description: Erfahren Sie mehr über Cross-Site Scripting (XSS) und Techniken zum Beheben dieses Sicherheitsrisikos in einer ASP.net Core-app.
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1d6f605dc336d8768b8a47e4995f119d198a61af
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172633"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>Verhindern von Cross-Site Scripting (XSS) in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Cross-Site Scripting (XSS) ist ein Sicherheitsrisiko, das es Angreifern ermöglicht, Client seitige Skripts (normalerweise JavaScript) in Webseiten zu platzieren. Wenn andere Benutzer betroffene Seiten laden, werden die Skripts des Angreifers ausgeführt, sodass der Angreifer Cookies und Sitzungs Token stehlen, den Inhalt der Webseite durch DOM-Manipulation ändern oder den Browser an eine andere Seite umleiten kann. XSS-Sicherheitsrisiken treten in der Regel auf, wenn eine Anwendung Benutzereingaben annimmt und Sie an eine Seite ausgibt, ohne Sie zu validieren, zu codieren oder zu

## <a name="protecting-your-application-against-xss"></a>Schutz Ihrer Anwendung vor XSS

XSS funktioniert auf der Basisebene, indem Sie die Anwendung in das Einfügen eines `<script>`-Tags in die gerenderte Seite einfügen, oder indem Sie ein `On*` Ereignis in ein Element einfügen. Entwickler sollten die folgenden Präventionsmaßnahmen ergreifen, um die Einführung von XSS in Ihre Anwendung zu vermeiden.

1. Fügen Sie niemals nicht vertrauenswürdige Daten in Ihre HTML-Eingabe ein, es sei denn, Sie führen die restlichen Schritte aus. Bei nicht vertrauenswürdigen Daten handelt es sich um Daten, die von einem Angreifer gesteuert werden können, HTML-Formular Eingaben, Abfrage Zeichenfolgen, HTTP-Header, und auch Daten, die aus einer Datenbank als Angreifer stammen, können möglicherweise die Datenbank verletzen, auch wenn Sie Ihre Anwendung nicht verletzen können.

2. Vor dem Einfügen nicht vertrauenswürdiger Daten in ein HTML-Element stellen Sie sicher, dass die HTML-Codierung vorliegt Die HTML-Codierung nimmt Zeichen wie z. b. &lt; auf und ändert Sie in eine sichere Form &amp;lt;

3. Bevor nicht vertrauenswürdige Daten in ein HTML-Attribut gesetzt werden, stellen Sie sicher, dass die HTML-Codierung Die HTML-Attribut Codierung ist eine supermenge der HTML-Codierung und codiert zusätzliche Zeichen wie z. b. "and".

4. Vor dem Einfügen nicht vertrauenswürdiger Daten in JavaScript platzieren Sie die Daten in einem HTML-Element, dessen Inhalt Sie zur Laufzeit abrufen. Wenn dies nicht möglich ist, stellen Sie sicher, dass die Daten JavaScript-codiert sind. Die JavaScript-Codierung erfordert gefährliche Zeichen für JavaScript und ersetzt Sie durch ihre hexadezimal Zeichen, z. b. &lt; als `\u003C`codiert werden.

5. Vor dem Einfügen von nicht vertrauenswürdigen Daten in eine URL-Abfrage Zeichenfolge stellen Sie sicher, dass die URL

## <a name="html-encoding-using-razor"></a>HTML-Codierung mit Razor

Die Razor-Engine, die in MVC verwendet wird, codiert automatisch alle Ausgaben aus Variablen, es sei denn, Sie arbeiten wirklich hart, um dies zu verhindern. Sie verwendet die Codierungsregeln für HTML-Attribute, wenn Sie die *@* -Direktive verwenden Da die HTML-Attribut Codierung eine supermenge der HTML-Codierung ist, bedeutet dies, dass Sie sich nicht darum kümmern müssen, ob Sie HTML-Codierung oder HTML-Attribut Codierung verwenden sollten. Sie müssen sicherstellen, dass Sie @ nur in einem HTML-Kontext verwenden, nicht, wenn Sie versuchen, nicht vertrauenswürdige Eingaben direkt in JavaScript einzufügen. Taghilfsprogramme codieren auch Eingaben, die Sie in tagparametern verwenden.

Nehmen Sie die folgende Razor-Ansicht:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

In dieser Ansicht wird der Inhalt der Variablen *untreudinput ausgegeben* . Diese Variable enthält einige Zeichen, die in XSS-Angriffen verwendet werden, nämlich &lt;, "und &gt;. Beim Untersuchen der Quelle wird die gerenderte Ausgabe angezeigt, die als

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.net Core MVC stellt eine `HtmlString` Klasse bereit, die bei der Ausgabe nicht automatisch codiert wird. Dies sollte nie in Kombination mit nicht vertrauenswürdigen Eingaben verwendet werden, da dadurch ein XSS-Sicherheitsrisiko verfügbar gemacht wird.

## <a name="javascript-encoding-using-razor"></a>JavaScript-Codierung mithilfe von Razor

Es kann vorkommen, dass Sie einen Wert in JavaScript einfügen möchten, um Ihre Ansicht zu verarbeiten. Es gibt hierbei zwei Möglichkeiten. Die sicherste Möglichkeit zum Einfügen von Werten besteht darin, den Wert in einem Daten Attribut eines Tags zu platzieren und ihn in JavaScript abzurufen. Beispiel:

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

Dadurch wird der folgende HTML-Code erstellt.

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

Bei der Ausführung von wird Folgendes gerrennt:

```
<"123">
   <"123">
```

Sie können den JavaScript-Encoder auch direkt aufzurufen:

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
> Verketten Sie nicht vertrauenswürdige Eingaben in JavaScript, um DOM-Elemente zu erstellen. Sie sollten `createElement()` verwenden und Eigenschaftswerte entsprechend zuweisen, z. b. `node.TextContent=`, oder `element.SetAttribute()`/verwenden `element[attribute]=` andernfalls machen Sie sich für DOM-basiertes XSS verfügbar.

## <a name="accessing-encoders-in-code"></a>Aufrufen von Encodern im Code

Die HTML-, JavaScript-und URL-Encoder sind für den Code auf zwei Arten verfügbar. Sie können Sie über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) einfügen, oder Sie können die im `System.Text.Encodings.Web`-Namespace enthaltenen Standard Encoder verwenden. Wenn Sie die Standard Encoder verwenden, werden alle, die Sie auf Zeichen Bereiche angewendet haben, als sicher behandelt werden, nicht wirksam. die Standard Codierungen verwenden die sichersten Codierungsregeln.

Um die konfigurierbaren Encoder über di verwenden zu können, sollten die Konstruktoren nach Bedarf einen *htmlencoder*-, *javascriptencoder* -und *urlencoder* -Parameter verwenden. Beispiel:

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

## <a name="encoding-url-parameters"></a>URL-Codierungs Parameter

Wenn Sie eine URL-Abfrage Zeichenfolge mit nicht vertrauenswürdiger Eingabe als Wert erstellen möchten, verwenden Sie die `UrlEncoder`, um den Wert zu codieren. Beispiel:

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

Nach der Codierung enthält die encodvalue-Variable `%22Quoted%20Value%20with%20spaces%20and%20%26%22`. Leerzeichen, Anführungszeichen, Interpunktions Zeichen und andere unsichere Zeichen werden in den hexadezimalen Wert Prozent codiert, z. b. wird ein Leerzeichen zu %20.

>[!WARNING]
> Verwenden Sie nicht vertrauenswürdige Eingaben nicht als Teil eines URL-Pfads. Übergeben Sie immer nicht vertrauenswürdige Eingaben als Abfrage Zeichenfolgen-Wert.

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>Anpassen der Encoder

Standardmäßig verwenden Encoder eine sichere Liste, die auf den grundlegenden lateinischen Unicode-Bereich beschränkt ist, und Codieren alle Zeichen außerhalb dieses Bereichs als Zeichencode Entsprechungen. Dieses Verhalten wirkt sich auch auf Razor-taghelper und htmlhelper-Rendering aus, da es die Encoder zum Ausgeben der Zeichen folgen verwendet.

Die dahinter liegende Argumentation besteht darin, vor unbekannten oder zukünftigen Browserfehlern zu schützen (vorherige Browser Fehler haben die Analysierung basierend auf der Verarbeitung von nicht englischen Zeichen abgeglichen). Wenn Ihre Website nicht lateinische Zeichen wie Chinesisch, Kyrillisch oder andere verwendet, ist dies wahrscheinlich nicht das gewünschte Verhalten.

Sie können die Liste der Encoder-sichere Listen so anpassen, dass Sie beim Start für Ihre Anwendung geeignete Unicode-Bereiche in `ConfigureServices()`enthalten.

Beispielsweise können Sie mit der Standardkonfiguration ein Razor-htmlhelper wie folgt verwenden.

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

Wenn Sie die Quelle der Webseite anzeigen, sehen Sie, dass Sie wie folgt gerendert wurde, wobei der chinesische Text codiert ist.

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

Um die vom Encoder als sicher behandelten Zeichen zu erweitern, fügen Sie die folgende Zeile in die `ConfigureServices()`-Methode in `startup.cs`ein.

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

In diesem Beispiel wird die sichere Liste so erweitert, dass Sie den Unicode-Bereich cjkunifedideographs einschließt. Die gerenderte Ausgabe wird jetzt

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

Sichere Listen Bereiche werden als Unicode-Code Diagramme, nicht als Sprachen angegeben. Der [Unicode-Standard](https://unicode.org/) enthält eine Liste von [Code Diagrammen](https://www.unicode.org/charts/index.html) , die Sie verwenden können, um das Diagramm mit ihren Zeichen zu suchen. Jeder Encoder, HTML, JavaScript und jede URL muss separat konfiguriert werden.

> [!NOTE]
> Die Anpassung der sicheren Liste wirkt sich nur auf Encoder aus, die über di übertragen werden. Wenn Sie über `System.Text.Encodings.Web.*Encoder.Default` direkt auf einen Encoder zugreifen, wird die standardmäßige lateinische Standard SafeList verwendet.

## <a name="where-should-encoding-take-place"></a>Wo sollte die Codierung erfolgen?

Die allgemein akzeptierte Übung besteht darin, dass die Codierung an der Stelle der Ausgabe stattfindet und codierte Werte niemals in einer Datenbank gespeichert werden sollen. Die Codierung an der Stelle der Ausgabe ermöglicht es Ihnen, die Verwendung von Daten zu ändern, z. b. von HTML in einen Abfrage Zeichen folgen Wert. Außerdem können Sie Ihre Daten problemlos durchsuchen, ohne Werte vor der Suche codieren zu müssen, und Sie können alle Änderungen oder Fehlerbehebungen von Encodern nutzen.

## <a name="validation-as-an-xss-prevention-technique"></a>Validierung als XSS-Präventionsmethode

Die Validierung kann ein nützliches Tool zum Einschränken von XSS-Angriffen sein. Beispielsweise löst eine numerische Zeichenfolge, die nur die Zeichen 0-9 enthält, keinen XSS-Angriff aus. Die Validierung wird komplizierter, wenn HTML in Benutzereingaben akzeptiert wird. Das Auswerten von HTML-Eingaben ist schwierig, wenn nicht unmöglich. Markdown ist mit einem Parser gekoppelt, der eingebettete HTML-Code entfernt, ist eine sicherere Option zum akzeptieren umfassender Eingaben. Verlassen Sie sich niemals ausschließlich auf die Validierung. Codieren Sie vor der Ausgabe immer nicht vertrauenswürdige Eingaben, unabhängig davon, welche Validierung oder Bereinigung durchgeführt wurde.
