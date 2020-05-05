---
title: Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net CoreBlazor
author: guardrex
description: Erfahren Sie, wie Sie eine Inhalts Sicherheitsrichtlinie (Content Security Policy Blazor , CSP) mit ASP.net Core-Apps verwenden, um Schutz vor Cross-Site Scripting (XSS)-Angriffen zu bieten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8c5e1c5dd2d41efade91a612bea2855569a61fee
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775582"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net CoreBlazor

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) ist ein Sicherheitsrisiko, bei dem ein Angreifer ein oder mehrere böswillige Client seitige Skripts in den gerenderten Inhalt einer APP platziert. Eine Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP) hilft beim Schutz vor XSS-Angriffen, indem der Browser von gültig informiert wird:

* Quellen für geladenen Inhalt, einschließlich Skripts, Stylesheets und Bilder.
* Von einer Seite ausgeführte Aktionen, die zulässige URL-Ziele von Formularen angeben.
* Plug-ins, die geladen werden können.

Zum Anwenden eines CSP auf eine APP gibt der Entwickler mehrere CSP-Content-Security- *Direktiven* in `Content-Security-Policy` einem oder `<meta>` mehreren Headern oder Tags an.

Die Richtlinien werden vom Browser ausgewertet, während eine Seite geladen wird. Der Browser überprüft die Quellen der Seite und bestimmt, ob Sie die Anforderungen der Inhalts Sicherheits Direktiven erfüllen. Wenn Richtlinien Anweisungen für eine Ressource nicht erfüllt werden, lädt der Browser die Ressource nicht. Nehmen Sie beispielsweise eine Richtlinie, die keine Skripts von Drittanbietern zulässt. Wenn eine Seite ein `<script>` Tag mit einem Ursprung eines Drittanbieters im- `src` Attribut enthält, verhindert der Browser das Laden des Skripts.

CSP wird in den meisten modernen Desktop-und mobilen Browsern unterstützt, einschließlich Chrome, Edge, Firefox, Opera und Safari. CSP wird für Blazor -apps empfohlen.

## <a name="policy-directives"></a>Richtlinien Direktiven

Legen Sie mindestens die folgenden Direktiven und Quellen Blazor für apps fest. Fügen Sie nach Bedarf weitere Direktiven und Quellen hinzu. Die folgenden Direktiven werden im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dieses Artikels verwendet, in dem Beispiel Sicherheitsrichtlinien Blazor für Webassembly Blazor und Server bereitgestellt werden:

* der [Basis-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; schränkt die URLs für das `<base>` Tag einer Seite ein. Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
* [Block-all-Mixed-Content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; verhindert das Laden von gemischtem http-und HTTPS-Inhalt.
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; gibt ein Fall Back für Quell Direktiven an, die nicht explizit durch die Richtlinie angegeben werden. Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; gibt gültige Quellen für Images an.
  * Geben `data:` Sie an, um das `data:` Laden von Bildern aus URLs zuzulassen
  * Geben `https:` Sie an, um das Laden von Bildern von HTTPS-Endpunkten zuzulassen
* [Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; gibt gültige Quellen für die `<object>`Tags `<embed>`, und `<applet>` an. Geben `none` Sie an, um alle URL-Quellen zu verhindern.
* [Script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; gibt gültige Quellen für Skripts an.
  * Geben Sie `https://stackpath.bootstrapcdn.com/` die Host Quelle für Bootstrap-Skripts an.
  * Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
  * In einer Blazor Webassembly-App:
    * Geben Sie die folgenden Hashwerte an, um Blazor zuzulassen, dass die erforderlichen Webassembly-Inline Skripts geladen werden:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Gibt `unsafe-eval` an, `eval()` dass die Methoden und zum Erstellen von Code aus Zeichen folgen verwenden.
  * Geben Sie Blazor in einer Server-APP `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` den Hash für das Inline Skript an, das die Fall Back Erkennung für Stylesheets ausführt.
* [Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; gibt gültige Quellen für Stylesheets an.
  * Geben Sie `https://stackpath.bootstrapcdn.com/` die Host Quelle für Bootstrap-Stylesheets an.
  * Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
  * Geben `unsafe-inline` Sie an, um die Verwendung von Inline Stilen zuzulassen. Die Inline Deklaration ist erforderlich, damit die Blazor Benutzeroberfläche in Server-apps nach der anfänglichen Anforderung erneut eine Verbindung mit dem Client und dem Server herstellen kann. In einer zukünftigen Version kann die `unsafe-inline` Inline Formatierung entfernt werden, sodass Sie nicht mehr benötigt wird.
* [Upgrade-unsicher-Anforderungen](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; gibt an, dass Inhalts-URLs aus unsicheren (http) Quellen sicher über HTTPS abgerufen werden sollen.

Die vorangehenden Anweisungen werden von allen Browsern mit Ausnahme von Microsoft Internet Explorer unterstützt.

So erhalten Sie SHA-Hashes für zusätzliche Inline Skripts:

* Wenden Sie den im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) gezeigten CSP an.
* Greifen Sie auf die Developer Tools-Konsole des Browsers zu, während Sie die APP lokal ausführen. Der Browser berechnet Hashes für blockierte Skripts und zeigt diese an, wenn `meta` ein CSP-Header oder-Tag vorhanden ist.
* Kopieren Sie die Hashes, die vom Browser bereit `script-src` gestellt werden, in die Quellen. Verwenden Sie einfache Anführungszeichen um jeden Hash.

Eine Browser Unterstützungs Matrix für Content Security Policy Level 2 finden Sie unter [kann ich verwenden: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Anwenden der Richtlinie

Verwenden Sie `<meta>` ein Tag, um die Richtlinie anzuwenden:

* Legen Sie den Wert des `http-equiv` Attributs `Content-Security-Policy`auf fest.
* Platzieren Sie die Direktiven `content` im Attribut Wert. Trennen Sie-Direktiven mit einem`;`Semikolon ().
* Platzieren Sie das `meta` Tag immer im `<head>` Inhalt.

In den folgenden Abschnitten werden Beispiel Richtlinien Blazor für Webassembly Blazor und Server gezeigt. Diese Beispiele sind für jede Version von Blazormit diesem Artikel versioniert. Wenn Sie eine für Ihr Release geeignete Version verwenden möchten, wählen Sie auf dieser Webseite die Dokument Version mit der Dropdown Auswahl für die **Version** aus.

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Wenden Sie `<head>` im Inhalt der *wwwroot/Index.html* -Host Seite die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a>Blazor Server

Wenden Sie `<head>` im Inhalt der Seite *pages/_Host. cshtml-* Host die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>Meta-Tag-Einschränkungen

Die `<meta>` folgenden Direktiven werden von einer tagrichtlinie nicht unterstützt:

* [Frame-Vorgänger](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [Bericht zu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [Report-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [Sandbox](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Verwenden Sie zum unterstützen der vorangehenden Direktiven `Content-Security-Policy`einen Header mit dem Namen. Die direktivenzeichenfolge ist der Wert des Headers.

## <a name="test-a-policy-and-receive-violation-reports"></a>Testen einer Richtlinie und empfangen von Verletzungs Berichten

Durch Tests können Sie sicherstellen, dass Skripts von Drittanbietern nicht versehentlich blockiert werden, wenn Sie eine anfängliche Richtlinie

Wenn Sie eine Richtlinie über einen Zeitraum testen möchten, ohne die Richtlinien Direktiven zu `<meta>` erzwingen, `http-equiv` legen Sie das-Attribut oder den Header Namen einer Header `Content-Security-Policy-Report-Only`basierten Richtlinie auf fest. Fehlerberichte werden als JSON-Dokumente an eine angegebene URL gesendet. Weitere Informationen finden Sie unter [MDN-Webdokumentation: Content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Informationen zur Berichterstattung bei Verstößen, während eine Richtlinie aktiv ist, finden Sie in den folgenden Artikeln:

* [Bericht zu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [Report-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Obwohl `report-uri` nicht mehr zur Verwendung empfohlen wird, sollten beide Direktiven verwendet werden `report-to` , bis von allen wichtigen Browsern unterstützt wird. Verwenden `report-uri` Sie nicht exklusiv, da `report-uri` die Unterstützung für jederzeit *von Browsern* gelöscht wird. Entfernen Sie unter `report-uri` Stützung für in Ihren `report-to` Richtlinien, wenn vollständig unterstützt wird Informationen zum Nachverfolgen `report-to`der Übernahme von finden Sie unter [kann ich verwenden: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

Testen und aktualisieren Sie die Richtlinie einer APP alle Releases.

## <a name="troubleshoot"></a>Problembehandlung

* Fehler werden in der Developer Tools-Konsole des Browsers angezeigt. Browser stellen Informationen zu folgenden Informationen bereit:
  * Elemente, die die Richtlinie nicht einhalten.
  * Vorgehensweise beim Ändern der Richtlinie, um ein blockiertes Element zuzulassen.
* Eine Richtlinie ist nur vollständig wirksam, wenn der Client Browser alle enthaltenen Direktiven unterstützt. Eine aktuelle Browser Unterstützungs Matrix finden Sie unter [kann ich verwenden: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [MDN-Webdokumentation: Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [Ebene 2 der Inhalts Sicherheitsrichtlinie](https://www.w3.org/TR/CSP2/)
* [Google CSP-Evaluator](https://csp-evaluator.withgoogle.com/)
