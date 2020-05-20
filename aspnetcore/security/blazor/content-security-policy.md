---
Title: ' Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net Core Blazor ' Author: Description: ' erfahren Sie, wie Sie eine Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP) mit ASP.net Core- Blazor Apps verwenden, um Schutz vor Cross-Site Scripting (XSS)-Angriffen zu bieten.
monikerrange: ms. Author: ms. Custom: ms. Date: NO-LOC:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' UID: 

---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net CoreBlazor

Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)

[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) ist ein Sicherheitsrisiko, bei dem ein Angreifer ein oder mehrere böswillige Client seitige Skripts in den gerenderten Inhalt einer APP platziert. Eine Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP) hilft beim Schutz vor XSS-Angriffen, indem der Browser von gültig informiert wird:

* Quellen für geladenen Inhalt, einschließlich Skripts, Stylesheets und Bilder.
* Von einer Seite ausgeführte Aktionen, die zulässige URL-Ziele von Formularen angeben.
* Plug-ins, die geladen werden können.

Zum Anwenden eines CSP auf eine APP gibt der Entwickler mehrere CSP-Content-Security- *Direktiven* in einem oder mehreren `Content-Security-Policy` Headern oder `<meta>` Tags an.

Die Richtlinien werden vom Browser ausgewertet, während eine Seite geladen wird. Der Browser überprüft die Quellen der Seite und bestimmt, ob Sie die Anforderungen der Inhalts Sicherheits Direktiven erfüllen. Wenn Richtlinien Anweisungen für eine Ressource nicht erfüllt werden, lädt der Browser die Ressource nicht. Nehmen Sie beispielsweise eine Richtlinie, die keine Skripts von Drittanbietern zulässt. Wenn eine Seite ein `<script>` Tag mit einem Ursprung eines Drittanbieters im- `src` Attribut enthält, verhindert der Browser das Laden des Skripts.

CSP wird in den meisten modernen Desktop-und mobilen Browsern unterstützt, einschließlich Chrome, Edge, Firefox, Opera und Safari. CSP wird für- Blazor apps empfohlen.

## <a name="policy-directives"></a>Richtlinien Direktiven

Legen Sie mindestens die folgenden Direktiven und Quellen für Blazor apps fest. Fügen Sie nach Bedarf weitere Direktiven und Quellen hinzu. Die folgenden Direktiven werden im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dieses Artikels verwendet, in dem Beispiel Sicherheitsrichtlinien für Blazor Webassembly und Blazor Server bereitgestellt werden:

* [Basis-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Schränkt die URLs für das Tag einer Seite ein `<base>` . Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
* [Block-alle-gemischt-Inhalt](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Verhindert das Laden gemischter http-und HTTPS-Inhalte.
* [Standard-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Gibt einen Fall Back für Quell Direktiven an, die nicht explizit durch die Richtlinie angegeben werden. Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Gibt gültige Quellen für Images an.
  * Geben `data:` Sie an, um das Laden von Bildern aus `data:` URLs zuzulassen
  * Geben `https:` Sie an, um das Laden von Bildern von HTTPS-Endpunkten zuzulassen
* [Objekt-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Gibt gültige Quellen für die `<object>` `<embed>` Tags, und an `<applet>` . Geben `none` Sie an, um alle URL-Quellen zu verhindern.
* [Skript-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Gibt gültige Quellen für Skripts an.
  * Geben Sie die `https://stackpath.bootstrapcdn.com/` Host Quelle für Bootstrap-Skripts an.
  * Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
  * In einer Blazor Webassembly-App:
    * Geben Sie die folgenden Hashwerte an, um zuzulassen, dass die erforderlichen Blazor Webassembly-Inline Skripts geladen werden:
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * Gibt `unsafe-eval` an, `eval()` dass die Methoden und zum Erstellen von Code aus Zeichen folgen verwenden.
  * Geben Sie in einer Blazor Server-App den `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` Hash für das Inline Skript an, das die Fall Back Erkennung für Stylesheets ausführt.
* [Stil-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Gibt gültige Quellen für Stylesheets an.
  * Geben Sie die `https://stackpath.bootstrapcdn.com/` Host Quelle für Bootstrap-Stylesheets an.
  * Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.
  * Geben `unsafe-inline` Sie an, um die Verwendung von Inline Stilen zuzulassen. Die Inline Deklaration ist erforderlich, damit die Benutzeroberfläche in Blazor Server-apps nach der anfänglichen Anforderung erneut eine Verbindung mit dem Client und dem Server herstellen kann. In einer zukünftigen Version kann die Inline Formatierung entfernt werden, sodass Sie `unsafe-inline` nicht mehr benötigt wird.
* [Upgrade-unsichere Anforderungen](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Gibt an, dass Inhalts-URLs aus unsicheren (http) Quellen sicher über HTTPS abgerufen werden sollen.

Die vorangehenden Anweisungen werden von allen Browsern mit Ausnahme von Microsoft Internet Explorer unterstützt.

So erhalten Sie SHA-Hashes für zusätzliche Inline Skripts:

* Wenden Sie den im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) gezeigten CSP an.
* Greifen Sie auf die Developer Tools-Konsole des Browsers zu, während Sie die APP lokal ausführen. Der Browser berechnet Hashes für blockierte Skripts und zeigt diese an, wenn ein CSP-Header oder- `meta` Tag vorhanden ist.
* Kopieren Sie die Hashes, die vom Browser bereitgestellt werden, in die `script-src` Quellen. Verwenden Sie einfache Anführungszeichen um jeden Hash.

Eine Browser Unterstützungs Matrix für Content Security Policy Level 2 finden Sie unter [kann ich verwenden: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).

## <a name="apply-the-policy"></a>Anwenden der Richtlinie

Verwenden `<meta>` Sie ein Tag, um die Richtlinie anzuwenden:

* Legen Sie den Wert des `http-equiv` Attributs auf fest `Content-Security-Policy` .
* Platzieren Sie die Direktiven im `content` Attribut Wert. Trennen Sie-Direktiven mit einem Semikolon ( `;` ).
* Platzieren Sie das `meta` Tag immer im `<head>` Inhalt.

In den folgenden Abschnitten werden Beispiel Richtlinien für Blazor Webassembly und Blazor Server gezeigt. Diese Beispiele sind für jede Version von mit diesem Artikel versioniert Blazor . Wenn Sie eine für Ihr Release geeignete Version verwenden möchten, wählen Sie auf dieser Webseite die Dokument Version mit der Dropdown Auswahl für die **Version** aus.

### <a name="blazor-webassembly"></a>Blazor WebAssembly

Wenden Sie im `<head>` Inhalt der *wwwroot/Index.html* -Host Seite die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:

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

Wenden Sie im `<head>` Inhalt der Seite *pages/_Host. cshtml-* Host die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:

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

`<meta>`Die folgenden Direktiven werden von einer tagrichtlinie nicht unterstützt:

* [Frame-Vorgänger](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [Bericht zu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [Report-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [Sandbox](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

Verwenden Sie zum unterstützen der vorangehenden Direktiven einen Header mit dem Namen `Content-Security-Policy` . Die direktivenzeichenfolge ist der Wert des Headers.

## <a name="test-a-policy-and-receive-violation-reports"></a>Testen einer Richtlinie und empfangen von Verletzungs Berichten

Durch Tests können Sie sicherstellen, dass Skripts von Drittanbietern nicht versehentlich blockiert werden, wenn Sie eine anfängliche Richtlinie

Wenn Sie eine Richtlinie über einen Zeitraum testen möchten, ohne die Richtlinien Direktiven zu erzwingen, legen `<meta>` Sie das- `http-equiv` Attribut oder den Header Namen einer Header basierten Richtlinie auf fest `Content-Security-Policy-Report-Only` . Fehlerberichte werden als JSON-Dokumente an eine angegebene URL gesendet. Weitere Informationen finden Sie unter [MDN-Webdokumentation: Content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).

Informationen zur Berichterstattung bei Verstößen, während eine Richtlinie aktiv ist, finden Sie in den folgenden Artikeln:

* [Bericht zu](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [Report-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

Obwohl `report-uri` nicht mehr zur Verwendung empfohlen wird, sollten beide Direktiven verwendet werden, bis `report-to` von allen wichtigen Browsern unterstützt wird. Verwenden Sie nicht exklusiv `report-uri` , da die Unterstützung für jederzeit `report-uri` von Browsern gelöscht wird. *at any time* Entfernen Sie Unterstützung für `report-uri` in ihren Richtlinien, wenn `report-to` vollständig unterstützt wird Informationen zum Nachverfolgen der Übernahme von finden Sie unter `report-to` [kann ich verwenden: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).

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
