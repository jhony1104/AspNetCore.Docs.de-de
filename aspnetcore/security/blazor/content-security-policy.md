---
title: Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net CoreBlazor
author: guardrex
description: Erfahren Sie, wie Sie eine Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP) mit ASP.net Core- Blazor Apps verwenden, um Schutz vor Cross-Site Scripting (XSS)-Angriffen zu bieten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8615b199373ca856c252b9f843e3635770367e4a
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106389"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="ea7f7-103">Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net CoreBlazor</span><span class="sxs-lookup"><span data-stu-id="ea7f7-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="ea7f7-104">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ea7f7-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="ea7f7-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) ist ein Sicherheitsrisiko, bei dem ein Angreifer ein oder mehrere böswillige Client seitige Skripts in den gerenderten Inhalt einer APP platziert.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="ea7f7-106">Eine Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP) hilft beim Schutz vor XSS-Angriffen, indem der Browser von gültig informiert wird:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="ea7f7-107">Quellen für geladenen Inhalt, einschließlich Skripts, Stylesheets und Bilder.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="ea7f7-108">Von einer Seite ausgeführte Aktionen, die zulässige URL-Ziele von Formularen angeben.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="ea7f7-109">Plug-ins, die geladen werden können.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="ea7f7-110">Zum Anwenden eines CSP auf eine APP gibt der Entwickler mehrere CSP-Content-Security- *Direktiven* in einem oder mehreren `Content-Security-Policy` Headern oder `<meta>` Tags an.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="ea7f7-111">Die Richtlinien werden vom Browser ausgewertet, während eine Seite geladen wird.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="ea7f7-112">Der Browser überprüft die Quellen der Seite und bestimmt, ob Sie die Anforderungen der Inhalts Sicherheits Direktiven erfüllen.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="ea7f7-113">Wenn Richtlinien Anweisungen für eine Ressource nicht erfüllt werden, lädt der Browser die Ressource nicht.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="ea7f7-114">Nehmen Sie beispielsweise eine Richtlinie, die keine Skripts von Drittanbietern zulässt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="ea7f7-115">Wenn eine Seite ein `<script>` Tag mit einem Ursprung eines Drittanbieters im- `src` Attribut enthält, verhindert der Browser das Laden des Skripts.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="ea7f7-116">CSP wird in den meisten modernen Desktop-und mobilen Browsern unterstützt, einschließlich Chrome, Edge, Firefox, Opera und Safari.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="ea7f7-117">CSP wird für- Blazor apps empfohlen.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="ea7f7-118">Richtlinien Direktiven</span><span class="sxs-lookup"><span data-stu-id="ea7f7-118">Policy directives</span></span>

<span data-ttu-id="ea7f7-119">Legen Sie mindestens die folgenden Direktiven und Quellen für Blazor apps fest.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="ea7f7-120">Fügen Sie nach Bedarf weitere Direktiven und Quellen hinzu.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="ea7f7-121">Die folgenden Direktiven werden im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dieses Artikels verwendet, in dem Beispiel Sicherheitsrichtlinien für Blazor Webassembly und Blazor Server bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="ea7f7-122">[Base-URI](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): schränkt die URLs für das Tag einer Seite ein `<base>` .</span><span class="sxs-lookup"><span data-stu-id="ea7f7-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="ea7f7-123">Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="ea7f7-124">[Block-all-Mixed-Content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): verhindert das Laden von gemischtem http-und HTTPS-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="ea7f7-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): gibt ein Fall Back für Quell Direktiven an, die nicht explizit durch die Richtlinie angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="ea7f7-126">Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="ea7f7-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): gibt gültige Quellen für Images an.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="ea7f7-128">Geben `data:` Sie an, um das Laden von Bildern aus `data:` URLs zuzulassen</span><span class="sxs-lookup"><span data-stu-id="ea7f7-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="ea7f7-129">Geben `https:` Sie an, um das Laden von Bildern von HTTPS-Endpunkten zuzulassen</span><span class="sxs-lookup"><span data-stu-id="ea7f7-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="ea7f7-130">[Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): gibt gültige Quellen für die `<object>` `<embed>` Tags, und an `<applet>` .</span><span class="sxs-lookup"><span data-stu-id="ea7f7-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="ea7f7-131">Geben `none` Sie an, um alle URL-Quellen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="ea7f7-132">[Script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): gibt gültige Quellen für Skripts an.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="ea7f7-133">Geben Sie die `https://stackpath.bootstrapcdn.com/` Host Quelle für Bootstrap-Skripts an.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="ea7f7-134">Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="ea7f7-135">In einer Blazor Webassembly-App:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="ea7f7-136">Geben Sie die folgenden Hashwerte an, um zuzulassen, dass die erforderlichen Blazor Webassembly-Inline Skripts geladen werden:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="ea7f7-137">Gibt `unsafe-eval` an, `eval()` dass die Methoden und zum Erstellen von Code aus Zeichen folgen verwenden.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="ea7f7-138">Geben Sie in einer Blazor Server-App den `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` Hash für das Inline Skript an, das die Fall Back Erkennung für Stylesheets ausführt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="ea7f7-139">[Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): gibt gültige Quellen für Stylesheets an.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="ea7f7-140">Geben Sie die `https://stackpath.bootstrapcdn.com/` Host Quelle für Bootstrap-Stylesheets an.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="ea7f7-141">Geben `self` Sie an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="ea7f7-142">Geben `unsafe-inline` Sie an, um die Verwendung von Inline Stilen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="ea7f7-143">Die Inline Deklaration ist erforderlich, damit die Benutzeroberfläche in Blazor Server-apps nach der anfänglichen Anforderung erneut eine Verbindung mit dem Client und dem Server herstellen kann.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="ea7f7-144">In einer zukünftigen Version kann die Inline Formatierung entfernt werden, sodass Sie `unsafe-inline` nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="ea7f7-145">[Upgrade-unsicher-Requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): gibt an, dass Inhalts-URLs aus unsicheren (http) Quellen sicher über HTTPS abgerufen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="ea7f7-146">Die vorangehenden Anweisungen werden von allen Browsern mit Ausnahme von Microsoft Internet Explorer unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="ea7f7-147">So erhalten Sie SHA-Hashes für zusätzliche Inline Skripts:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="ea7f7-148">Wenden Sie den im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) gezeigten CSP an.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="ea7f7-149">Greifen Sie auf die Developer Tools-Konsole des Browsers zu, während Sie die APP lokal ausführen.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="ea7f7-150">Der Browser berechnet Hashes für blockierte Skripts und zeigt diese an, wenn ein CSP-Header oder- `meta` Tag vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="ea7f7-151">Kopieren Sie die Hashes, die vom Browser bereitgestellt werden, in die `script-src` Quellen.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="ea7f7-152">Verwenden Sie einfache Anführungszeichen um jeden Hash.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="ea7f7-153">Eine Browser Unterstützungs Matrix für Content Security Policy Level 2 finden Sie unter [kann ich verwenden: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="ea7f7-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="ea7f7-154">Anwenden der Richtlinie</span><span class="sxs-lookup"><span data-stu-id="ea7f7-154">Apply the policy</span></span>

<span data-ttu-id="ea7f7-155">Verwenden `<meta>` Sie ein Tag, um die Richtlinie anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="ea7f7-156">Legen Sie den Wert des `http-equiv` Attributs auf fest `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="ea7f7-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="ea7f7-157">Platzieren Sie die Direktiven im `content` Attribut Wert.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="ea7f7-158">Trennen Sie-Direktiven mit einem Semikolon ( `;` ).</span><span class="sxs-lookup"><span data-stu-id="ea7f7-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="ea7f7-159">Platzieren Sie das `meta` Tag immer im `<head>` Inhalt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="ea7f7-160">In den folgenden Abschnitten werden Beispiel Richtlinien für Blazor Webassembly und Blazor Server gezeigt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="ea7f7-161">Diese Beispiele sind für jede Version von mit diesem Artikel versioniert Blazor .</span><span class="sxs-lookup"><span data-stu-id="ea7f7-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="ea7f7-162">Wenn Sie eine für Ihr Release geeignete Version verwenden möchten, wählen Sie auf dieser Webseite die Dokument Version mit der Dropdown Auswahl für die **Version** aus.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="ea7f7-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ea7f7-163"> WebAssembly</span></span>

<span data-ttu-id="ea7f7-164">Wenden Sie im `<head>` Inhalt der *wwwroot/Index.html* -Host Seite die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="ea7f7-165"> Server</span><span class="sxs-lookup"><span data-stu-id="ea7f7-165"> Server</span></span>

<span data-ttu-id="ea7f7-166">Wenden Sie im `<head>` Inhalt der Seite *pages/_Host. cshtml-* Host die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="ea7f7-167">Meta-Tag-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="ea7f7-167">Meta tag limitations</span></span>

<span data-ttu-id="ea7f7-168">`<meta>`Die folgenden Direktiven werden von einer tagrichtlinie nicht unterstützt:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="ea7f7-169">Frame-Vorgänger</span><span class="sxs-lookup"><span data-stu-id="ea7f7-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="ea7f7-170">Bericht zu</span><span class="sxs-lookup"><span data-stu-id="ea7f7-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="ea7f7-171">Report-URI</span><span class="sxs-lookup"><span data-stu-id="ea7f7-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="ea7f7-172">Sandbox</span><span class="sxs-lookup"><span data-stu-id="ea7f7-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="ea7f7-173">Verwenden Sie zum unterstützen der vorangehenden Direktiven einen Header mit dem Namen `Content-Security-Policy` .</span><span class="sxs-lookup"><span data-stu-id="ea7f7-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="ea7f7-174">Die direktivenzeichenfolge ist der Wert des Headers.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="ea7f7-175">Testen einer Richtlinie und empfangen von Verletzungs Berichten</span><span class="sxs-lookup"><span data-stu-id="ea7f7-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="ea7f7-176">Durch Tests können Sie sicherstellen, dass Skripts von Drittanbietern nicht versehentlich blockiert werden, wenn Sie eine anfängliche Richtlinie</span><span class="sxs-lookup"><span data-stu-id="ea7f7-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="ea7f7-177">Wenn Sie eine Richtlinie über einen Zeitraum testen möchten, ohne die Richtlinien Direktiven zu erzwingen, legen `<meta>` Sie das- `http-equiv` Attribut oder den Header Namen einer Header basierten Richtlinie auf fest `Content-Security-Policy-Report-Only` .</span><span class="sxs-lookup"><span data-stu-id="ea7f7-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="ea7f7-178">Fehlerberichte werden als JSON-Dokumente an eine angegebene URL gesendet.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="ea7f7-179">Weitere Informationen finden Sie unter [MDN-Webdokumentation: Content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="ea7f7-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="ea7f7-180">Informationen zur Berichterstattung bei Verstößen, während eine Richtlinie aktiv ist, finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="ea7f7-181">Bericht zu</span><span class="sxs-lookup"><span data-stu-id="ea7f7-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="ea7f7-182">Report-URI</span><span class="sxs-lookup"><span data-stu-id="ea7f7-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="ea7f7-183">Obwohl `report-uri` nicht mehr zur Verwendung empfohlen wird, sollten beide Direktiven verwendet werden, bis `report-to` von allen wichtigen Browsern unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="ea7f7-184">Verwenden Sie nicht exklusiv `report-uri` , da die Unterstützung für jederzeit `report-uri` von Browsern gelöscht wird. *at any time*</span><span class="sxs-lookup"><span data-stu-id="ea7f7-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="ea7f7-185">Entfernen Sie Unterstützung für `report-uri` in ihren Richtlinien, wenn `report-to` vollständig unterstützt wird</span><span class="sxs-lookup"><span data-stu-id="ea7f7-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="ea7f7-186">Informationen zum Nachverfolgen der Übernahme von finden Sie unter `report-to` [kann ich verwenden: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="ea7f7-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="ea7f7-187">Testen und aktualisieren Sie die Richtlinie einer APP alle Releases.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="ea7f7-188">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="ea7f7-188">Troubleshoot</span></span>

* <span data-ttu-id="ea7f7-189">Fehler werden in der Developer Tools-Konsole des Browsers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="ea7f7-190">Browser stellen Informationen zu folgenden Informationen bereit:</span><span class="sxs-lookup"><span data-stu-id="ea7f7-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="ea7f7-191">Elemente, die die Richtlinie nicht einhalten.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="ea7f7-192">Vorgehensweise beim Ändern der Richtlinie, um ein blockiertes Element zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="ea7f7-193">Eine Richtlinie ist nur vollständig wirksam, wenn der Client Browser alle enthaltenen Direktiven unterstützt.</span><span class="sxs-lookup"><span data-stu-id="ea7f7-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="ea7f7-194">Eine aktuelle Browser Unterstützungs Matrix finden Sie unter [kann ich verwenden: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="ea7f7-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ea7f7-195">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ea7f7-195">Additional resources</span></span>

* [<span data-ttu-id="ea7f7-196">MDN-Webdokumentation: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="ea7f7-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="ea7f7-197">Ebene 2 der Inhalts Sicherheitsrichtlinie</span><span class="sxs-lookup"><span data-stu-id="ea7f7-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="ea7f7-198">Google CSP-Evaluator</span><span class="sxs-lookup"><span data-stu-id="ea7f7-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
