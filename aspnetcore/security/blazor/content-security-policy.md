---
title: Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net Core Blazor
author: guardrex
description: Erfahren Sie, wie Sie eine Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP) mit ASP.net Core Blazor-Apps verwenden, um Schutz vor Cross-Site Scripting (XSS)-Angriffen zu bieten.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 1cfebf7b3d3bbb98a671b6f2db7c6518cda74b65
ms.sourcegitcommit: 51c86c003ab5436598dbc42f26ea4a83a795fd6e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78964535"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-opno-locblazor"></a><span data-ttu-id="c14a0-103">Erzwingen einer Inhalts Sicherheitsrichtlinie für ASP.net Core Blazor</span><span class="sxs-lookup"><span data-stu-id="c14a0-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="c14a0-104">Von [Javier calvarro Nelson](https://github.com/javiercn) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c14a0-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="c14a0-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) ist ein Sicherheitsrisiko, bei dem ein Angreifer ein oder mehrere böswillige Client seitige Skripts in den gerenderten Inhalt einer APP platziert.</span><span class="sxs-lookup"><span data-stu-id="c14a0-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="c14a0-106">Eine Inhalts Sicherheitsrichtlinie (Content Security Policy, CSP) hilft beim Schutz vor XSS-Angriffen, indem der Browser von gültig informiert wird:</span><span class="sxs-lookup"><span data-stu-id="c14a0-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="c14a0-107">Quellen für geladenen Inhalt, einschließlich Skripts, Stylesheets und Bilder.</span><span class="sxs-lookup"><span data-stu-id="c14a0-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="c14a0-108">Von einer Seite ausgeführte Aktionen, die zulässige URL-Ziele von Formularen angeben.</span><span class="sxs-lookup"><span data-stu-id="c14a0-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="c14a0-109">Plug-ins, die geladen werden können.</span><span class="sxs-lookup"><span data-stu-id="c14a0-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="c14a0-110">Zum Anwenden eines CSP auf eine APP gibt der Entwickler in mindestens einer `Content-Security-Policy` Kopfzeile oder `<meta>` Tags mehrere CSP-Inhalts Sicherheits *Direktiven* an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="c14a0-111">Die Richtlinien werden vom Browser ausgewertet, während eine Seite geladen wird.</span><span class="sxs-lookup"><span data-stu-id="c14a0-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="c14a0-112">Der Browser überprüft die Quellen der Seite und bestimmt, ob Sie die Anforderungen der Inhalts Sicherheits Direktiven erfüllen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="c14a0-113">Wenn Richtlinien Anweisungen für eine Ressource nicht erfüllt werden, lädt der Browser die Ressource nicht.</span><span class="sxs-lookup"><span data-stu-id="c14a0-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="c14a0-114">Nehmen Sie beispielsweise eine Richtlinie, die keine Skripts von Drittanbietern zulässt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="c14a0-115">Wenn eine Seite ein `<script>`-Tag mit einem Ursprungs Drittanbieter im `src`-Attribut enthält, verhindert der Browser das Laden des Skripts.</span><span class="sxs-lookup"><span data-stu-id="c14a0-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="c14a0-116">CSP wird in den meisten modernen Desktop-und mobilen Browsern unterstützt, einschließlich Chrome, Edge, Firefox, Opera und Safari.</span><span class="sxs-lookup"><span data-stu-id="c14a0-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="c14a0-117">CSP wird für Blazor-apps empfohlen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="c14a0-118">Richtlinien Direktiven</span><span class="sxs-lookup"><span data-stu-id="c14a0-118">Policy directives</span></span>

<span data-ttu-id="c14a0-119">Geben Sie mindestens die folgenden Direktiven und Quellen für Blazor-apps an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="c14a0-120">Fügen Sie nach Bedarf weitere Direktiven und Quellen hinzu.</span><span class="sxs-lookup"><span data-stu-id="c14a0-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="c14a0-121">Die folgenden Direktiven werden im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) dieses Artikels verwendet, in dem Beispiel Sicherheitsrichtlinien für Blazor Webassembly und Blazor Server bereitgestellt werden:</span><span class="sxs-lookup"><span data-stu-id="c14a0-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="c14a0-122">der [Basis-URI-](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; schränkt die URLs für das `<base>`-Tag einer Seite ein.</span><span class="sxs-lookup"><span data-stu-id="c14a0-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="c14a0-123">Geben Sie `self` an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c14a0-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="c14a0-124">[Block-all-Mixed-Content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; verhindert das Laden von gemischtem http-und HTTPS-Inhalt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="c14a0-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; gibt ein Fall Back für Quell Direktiven an, die nicht explizit durch die Richtlinie angegeben werden.</span><span class="sxs-lookup"><span data-stu-id="c14a0-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="c14a0-126">Geben Sie `self` an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c14a0-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="c14a0-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; gibt gültige Quellen für Images an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Indicates valid sources for images.</span></span>
  * <span data-ttu-id="c14a0-128">Geben Sie `data:` an, um das Laden von Bildern aus `data:` URLs zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="c14a0-129">Gibt `https:` an, um das Laden von Bildern von HTTPS-Endpunkten zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="c14a0-130">[Object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; gibt gültige Quellen für die `<object>`-, `<embed>`-und `<applet>`-Tags an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="c14a0-131">Gibt `none` an, um alle URL-Quellen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="c14a0-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="c14a0-132">[Skript-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; gibt gültige Quellen für Skripts an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="c14a0-133">Geben Sie die `https://stackpath.bootstrapcdn.com/` Host Quelle für Bootstrap-Skripts an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="c14a0-134">Geben Sie `self` an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c14a0-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="c14a0-135">In einer Blazor Webassembly-App:</span><span class="sxs-lookup"><span data-stu-id="c14a0-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="c14a0-136">Geben Sie die folgenden Hashes an, damit die erforderlichen Blazor Webassembly-Inline Skripts geladen werden können:</span><span class="sxs-lookup"><span data-stu-id="c14a0-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="c14a0-137">Geben Sie `unsafe-eval` an, um `eval()` und Methoden zum Erstellen von Code aus Zeichen folgen zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="c14a0-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="c14a0-138">Geben Sie in einer Blazor Server-App den `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` Hash für das Inline Skript an, das die Fall Back Erkennung für Stylesheets ausführt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="c14a0-139">[Style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; gibt gültige Quellen für Stylesheets an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="c14a0-140">Geben Sie die `https://stackpath.bootstrapcdn.com/` Host Quelle für Bootstrap-Stylesheets an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="c14a0-141">Geben Sie `self` an, um anzugeben, dass der Ursprung der APP, einschließlich des Schemas und der Portnummer, eine gültige Quelle ist.</span><span class="sxs-lookup"><span data-stu-id="c14a0-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="c14a0-142">Geben Sie `unsafe-inline` an, um die Verwendung von Inline Stilen zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="c14a0-143">Die Inline Deklaration ist erforderlich, damit die Benutzeroberfläche in Blazor Server-apps nach der anfänglichen Anforderung erneut eine Verbindung mit dem Client und dem Server herstellt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="c14a0-144">In einer zukünftigen Version kann die Inline Formatierung entfernt werden, sodass `unsafe-inline` nicht mehr benötigt wird.</span><span class="sxs-lookup"><span data-stu-id="c14a0-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="c14a0-145">[Upgrade-unsicher-Anforderungen](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; gibt an, dass Inhalts-URLs aus unsicheren (http) Quellen sicher über HTTPS abgerufen werden sollen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="c14a0-146">Die vorangehenden Anweisungen werden von allen Browsern mit Ausnahme von Microsoft Internet Explorer unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="c14a0-147">So erhalten Sie SHA-Hashes für zusätzliche Inline Skripts:</span><span class="sxs-lookup"><span data-stu-id="c14a0-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="c14a0-148">Wenden Sie den im Abschnitt [Anwenden der Richtlinie](#apply-the-policy) gezeigten CSP an.</span><span class="sxs-lookup"><span data-stu-id="c14a0-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="c14a0-149">Greifen Sie auf die Developer Tools-Konsole des Browsers zu, während Sie die APP lokal ausführen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="c14a0-150">Der Browser berechnet Hashes für blockierte Skripts und zeigt diese an, wenn ein CSP-Header oder `meta`-Tag vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c14a0-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="c14a0-151">Kopieren Sie die Hashes, die vom Browser bereitgestellt werden, in die `script-src` Quellen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="c14a0-152">Verwenden Sie einfache Anführungszeichen um jeden Hash.</span><span class="sxs-lookup"><span data-stu-id="c14a0-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="c14a0-153">Eine Browser Unterstützungs Matrix für Content Security Policy Level 2 finden Sie unter [kann ich verwenden: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span><span class="sxs-lookup"><span data-stu-id="c14a0-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="c14a0-154">Anwenden der Richtlinie</span><span class="sxs-lookup"><span data-stu-id="c14a0-154">Apply the policy</span></span>

<span data-ttu-id="c14a0-155">Verwenden Sie ein `<meta>`-Tag, um die Richtlinie anzuwenden:</span><span class="sxs-lookup"><span data-stu-id="c14a0-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="c14a0-156">Legen Sie den Wert des `http-equiv` Attributs auf `Content-Security-Policy`fest.</span><span class="sxs-lookup"><span data-stu-id="c14a0-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="c14a0-157">Platzieren Sie die-Direktiven in den `content`-Attribut Wert.</span><span class="sxs-lookup"><span data-stu-id="c14a0-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="c14a0-158">Trennen Sie-Direktiven mit einem Semikolon (`;`).</span><span class="sxs-lookup"><span data-stu-id="c14a0-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="c14a0-159">Platzieren Sie das `meta`-Tag immer im `<head>` Inhalt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="c14a0-160">In den folgenden Abschnitten werden Beispiel Richtlinien für Blazor Webassembly und Blazor Server gezeigt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="c14a0-161">Diese Beispiele sind für jede Version von Blazormit diesem Artikel versioniert.</span><span class="sxs-lookup"><span data-stu-id="c14a0-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="c14a0-162">Wenn Sie eine für Ihr Release geeignete Version verwenden möchten, wählen Sie auf dieser Webseite die Dokument Version mit der Dropdown Auswahl für die **Version** aus.</span><span class="sxs-lookup"><span data-stu-id="c14a0-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="c14a0-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="c14a0-163"> WebAssembly</span></span>

<span data-ttu-id="c14a0-164">Wenden Sie im `<head>` Inhalt der *wwwroot/Index.html* -Host Seite die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:</span><span class="sxs-lookup"><span data-stu-id="c14a0-164">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="c14a0-165"> Server</span><span class="sxs-lookup"><span data-stu-id="c14a0-165"> Server</span></span>

<span data-ttu-id="c14a0-166">Wenden Sie im `<head>` Inhalt der Seite *pages/_Host. cshtml-* Host die im Abschnitt Richtlinien [Direktiven](#policy-directives) beschriebenen Anweisungen an:</span><span class="sxs-lookup"><span data-stu-id="c14a0-166">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="c14a0-167">Meta-Tag-Einschränkungen</span><span class="sxs-lookup"><span data-stu-id="c14a0-167">Meta tag limitations</span></span>

<span data-ttu-id="c14a0-168">Die folgenden Direktiven werden von einer `<meta>`-tagrichtlinie nicht unterstützt:</span><span class="sxs-lookup"><span data-stu-id="c14a0-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="c14a0-169">Frame-Vorgänger</span><span class="sxs-lookup"><span data-stu-id="c14a0-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="c14a0-170">Bericht zu</span><span class="sxs-lookup"><span data-stu-id="c14a0-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="c14a0-171">Report-URI</span><span class="sxs-lookup"><span data-stu-id="c14a0-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="c14a0-172">Sandbox</span><span class="sxs-lookup"><span data-stu-id="c14a0-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="c14a0-173">Verwenden Sie einen Header mit dem Namen `Content-Security-Policy`, um die vorangehenden Anweisungen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="c14a0-174">Die direktivenzeichenfolge ist der Wert des Headers.</span><span class="sxs-lookup"><span data-stu-id="c14a0-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="c14a0-175">Testen einer Richtlinie und empfangen von Verletzungs Berichten</span><span class="sxs-lookup"><span data-stu-id="c14a0-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="c14a0-176">Durch Tests können Sie sicherstellen, dass Skripts von Drittanbietern nicht versehentlich blockiert werden, wenn Sie eine anfängliche Richtlinie</span><span class="sxs-lookup"><span data-stu-id="c14a0-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="c14a0-177">Wenn Sie eine Richtlinie über einen Zeitraum testen möchten, ohne die Richtlinien Direktiven zu erzwingen, legen Sie das `http-equiv` Attribut oder den Header Namen einer Header basierten Richtlinie für das `<meta>` Tag auf `Content-Security-Policy-Report-Only`fest.</span><span class="sxs-lookup"><span data-stu-id="c14a0-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="c14a0-178">Fehlerberichte werden als JSON-Dokumente an eine angegebene URL gesendet.</span><span class="sxs-lookup"><span data-stu-id="c14a0-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="c14a0-179">Weitere Informationen finden Sie unter [MDN-Webdokumentation: Content-Security-Policy-Report-only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span><span class="sxs-lookup"><span data-stu-id="c14a0-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="c14a0-180">Informationen zur Berichterstattung bei Verstößen, während eine Richtlinie aktiv ist, finden Sie in den folgenden Artikeln:</span><span class="sxs-lookup"><span data-stu-id="c14a0-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="c14a0-181">Bericht zu</span><span class="sxs-lookup"><span data-stu-id="c14a0-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="c14a0-182">Report-URI</span><span class="sxs-lookup"><span data-stu-id="c14a0-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="c14a0-183">Obwohl `report-uri` nicht mehr zur Verwendung empfohlen wird, sollten beide Direktiven verwendet werden, bis `report-to` von allen wichtigen Browsern unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c14a0-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="c14a0-184">Verwenden Sie `report-uri` nicht exklusiv, da die Unterstützung für `report-uri` jederzeit *von Browsern* gelöscht wird.</span><span class="sxs-lookup"><span data-stu-id="c14a0-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="c14a0-185">Entfernen Sie Unterstützung für `report-uri` in ihren Richtlinien, wenn `report-to` vollständig unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="c14a0-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="c14a0-186">Informationen zum Nachverfolgen der Übernahme von `report-to`finden Sie unter [kann ich verwenden: Report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span><span class="sxs-lookup"><span data-stu-id="c14a0-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="c14a0-187">Testen und aktualisieren Sie die Richtlinie einer APP alle Releases.</span><span class="sxs-lookup"><span data-stu-id="c14a0-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="c14a0-188">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="c14a0-188">Troubleshoot</span></span>

* <span data-ttu-id="c14a0-189">Fehler werden in der Developer Tools-Konsole des Browsers angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="c14a0-190">Browser stellen Informationen zu folgenden Informationen bereit:</span><span class="sxs-lookup"><span data-stu-id="c14a0-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="c14a0-191">Elemente, die die Richtlinie nicht einhalten.</span><span class="sxs-lookup"><span data-stu-id="c14a0-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="c14a0-192">Vorgehensweise beim Ändern der Richtlinie, um ein blockiertes Element zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="c14a0-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="c14a0-193">Eine Richtlinie ist nur vollständig wirksam, wenn der Client Browser alle enthaltenen Direktiven unterstützt.</span><span class="sxs-lookup"><span data-stu-id="c14a0-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="c14a0-194">Eine aktuelle Browser Unterstützungs Matrix finden Sie unter [kann ich verwenden: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span><span class="sxs-lookup"><span data-stu-id="c14a0-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c14a0-195">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="c14a0-195">Additional resources</span></span>

* [<span data-ttu-id="c14a0-196">MDN-Webdokumentation: Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="c14a0-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="c14a0-197">Ebene 2 der Inhalts Sicherheitsrichtlinie</span><span class="sxs-lookup"><span data-stu-id="c14a0-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="c14a0-198">Google CSP-Evaluator</span><span class="sxs-lookup"><span data-stu-id="c14a0-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
