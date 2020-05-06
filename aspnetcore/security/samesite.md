---
title: Arbeiten mit SameSite-Cookies in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie mit SameSite-Cookies in ASP.net Core
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
- Electron
uid: security/samesite
ms.openlocfilehash: 43d5a3dbc5e202688e006355e0b105a86d721460
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775101"
---
# <a name="work-with-samesite-cookies-in-aspnet-core"></a><span data-ttu-id="6e577-103">Arbeiten mit SameSite-Cookies in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="6e577-103">Work with SameSite cookies in ASP.NET Core</span></span>

<span data-ttu-id="6e577-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6e577-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6e577-105">SameSite ist ein [IETF](https://ietf.org/about/) -Entwurfs Standard, der Schutz vor Cross-Site Request fälschungstoken (CSRF) bietet.</span><span class="sxs-lookup"><span data-stu-id="6e577-105">SameSite is an [IETF](https://ietf.org/about/) draft standard designed to provide some protection against cross-site request forgery (CSRF) attacks.</span></span> <span data-ttu-id="6e577-106">Ursprünglich in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07)entworfen wurde, wurde der Entwurfs Standard in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00)aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="6e577-106">Originally drafted in [2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07), the draft standard was updated in [2019](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00).</span></span> <span data-ttu-id="6e577-107">Der aktualisierte Standard ist nicht abwärts kompatibel mit dem vorherigen Standard, und es gibt folgende Unterschiede:</span><span class="sxs-lookup"><span data-stu-id="6e577-107">The updated standard is not backward compatible with the previous standard, with the following being the most noticeable differences:</span></span>

* <span data-ttu-id="6e577-108">Cookies ohne SameSite-Header werden Standard `SameSite=Lax` mäßig als behandelt.</span><span class="sxs-lookup"><span data-stu-id="6e577-108">Cookies without SameSite header are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="6e577-109">`SameSite=None`muss verwendet werden, um eine standortübergreifende Cookie-Verwendung zuzulassen.</span><span class="sxs-lookup"><span data-stu-id="6e577-109">`SameSite=None` must be used to allow cross-site cookie use.</span></span>
* <span data-ttu-id="6e577-110">Cookies, die `SameSite=None` bestätigen, müssen auch als `Secure`markiert werden.</span><span class="sxs-lookup"><span data-stu-id="6e577-110">Cookies that assert `SameSite=None` must also be marked as `Secure`.</span></span>
* <span data-ttu-id="6e577-111">Bei Anwendungen, [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) die verwenden, treten `sameSite=Lax` möglich `sameSite=Strict` erweise Probleme `<iframe>` mit-oder-Cookies auf, da als standortübergreifende Szenarien behandelt wird.</span><span class="sxs-lookup"><span data-stu-id="6e577-111">Applications that use [`<iframe>`](https://developer.mozilla.org/docs/Web/HTML/Element/iframe) may experience issues with `sameSite=Lax` or `sameSite=Strict` cookies because `<iframe>` is treated as cross-site scenarios.</span></span>
* <span data-ttu-id="6e577-112">Der Wert `SameSite=None` ist vom [2016-Standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) nicht zulässig und bewirkt, dass einige Implementierungen solche Cookies als `SameSite=Strict`behandeln.</span><span class="sxs-lookup"><span data-stu-id="6e577-112">The value `SameSite=None` is not allowed by the [2016 standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07) and causes some implementations to treat such cookies as `SameSite=Strict`.</span></span> <span data-ttu-id="6e577-113">Siehe [Unterstützung älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="6e577-113">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="6e577-114">Die `SameSite=Lax` Einstellung funktioniert für die meisten Anwendungs Cookies.</span><span class="sxs-lookup"><span data-stu-id="6e577-114">The `SameSite=Lax` setting works for most application cookies.</span></span> <span data-ttu-id="6e577-115">Für einige Formen der Authentifizierung wie [OpenID Connect](https://openid.net/connect/) (oidc) und [WS-](https://auth0.com/docs/protocols/ws-fed) Verbund werden standardmäßig Post basierte Umleitungen bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="6e577-115">Some forms of authentication like [OpenID Connect](https://openid.net/connect/) (OIDC) and [WS-Federation](https://auth0.com/docs/protocols/ws-fed) default to POST based redirects.</span></span> <span data-ttu-id="6e577-116">Die Post basierten Umleitungen veranlassen den SameSite-Browserschutz, sodass SameSite für diese Komponenten deaktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="6e577-116">The POST based redirects trigger the SameSite browser protections, so SameSite is disabled for these components.</span></span> <span data-ttu-id="6e577-117">Die meisten [OAuth](https://oauth.net/) -Anmeldungen sind aufgrund von Unterschieden in der Art der Anforderungs Abläufe nicht betroffen.</span><span class="sxs-lookup"><span data-stu-id="6e577-117">Most [OAuth](https://oauth.net/) logins are not affected due to differences in how the request flows.</span></span>

<span data-ttu-id="6e577-118">Jede ASP.net Core Komponente, die Cookies ausgibt, muss entscheiden, ob SameSite geeignet ist.</span><span class="sxs-lookup"><span data-stu-id="6e577-118">Each ASP.NET Core component that emits cookies needs to decide if SameSite is appropriate.</span></span>

## <a name="samesite-test-sample-code"></a><span data-ttu-id="6e577-119">SameSite-Testbeispiel Code</span><span class="sxs-lookup"><span data-stu-id="6e577-119">SameSite test sample code</span></span>

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="6e577-120">Die folgenden Beispiele können heruntergeladen und getestet werden:</span><span class="sxs-lookup"><span data-stu-id="6e577-120">The following samples can be downloaded and tested:</span></span>

| <span data-ttu-id="6e577-121">Beispiel</span><span class="sxs-lookup"><span data-stu-id="6e577-121">Sample</span></span>               | <span data-ttu-id="6e577-122">Dokument</span><span class="sxs-lookup"><span data-stu-id="6e577-122">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="6e577-123">.Net Core MVC</span><span class="sxs-lookup"><span data-stu-id="6e577-123">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="6e577-124">.Net Core-Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6e577-124">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6e577-125">Das folgende Beispiel kann heruntergeladen und getestet werden:</span><span class="sxs-lookup"><span data-stu-id="6e577-125">The following sample can be downloaded and tested:</span></span>


| <span data-ttu-id="6e577-126">Beispiel</span><span class="sxs-lookup"><span data-stu-id="6e577-126">Sample</span></span>               | <span data-ttu-id="6e577-127">Dokument</span><span class="sxs-lookup"><span data-stu-id="6e577-127">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="6e577-128">.Net Core-Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6e577-128">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)  | <xref:security/samesite/rp31> |

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="net-core-support-for-the-samesite-attribute"></a><span data-ttu-id="6e577-129">.Net Core-Unterstützung für das SameSite-Attribut</span><span class="sxs-lookup"><span data-stu-id="6e577-129">.NET Core support for the sameSite attribute</span></span>

<span data-ttu-id="6e577-130">.Net Core 2,2 unterstützt seit der Veröffentlichung von Updates im Dezember 2019 den 2019-Entwurfs Standard für SameSite.</span><span class="sxs-lookup"><span data-stu-id="6e577-130">.NET Core 2.2 supports the 2019 draft standard for SameSite since the release of updates in December 2019.</span></span> <span data-ttu-id="6e577-131">Entwickler können den Wert des SameSite-Attributs Programm gesteuert mithilfe der `HttpCookie.SameSite` -Eigenschaft steuern.</span><span class="sxs-lookup"><span data-stu-id="6e577-131">Developers are able to programmatically control the value of the sameSite attribute using the `HttpCookie.SameSite` property.</span></span> <span data-ttu-id="6e577-132">Wenn die `SameSite` Eigenschaft auf Strict, Lax oder None festgelegt wird, werden diese Werte mit dem Cookie im Netzwerk geschrieben.</span><span class="sxs-lookup"><span data-stu-id="6e577-132">Setting the `SameSite` property to Strict, Lax, or None results in those values being written on the network with the cookie.</span></span> <span data-ttu-id="6e577-133">Wenn Sie es auf (samesitemode) (-1) festlegen, wird angegeben, dass kein SameSite-Attribut im Netzwerk mit dem Cookie enthalten sein soll.</span><span class="sxs-lookup"><span data-stu-id="6e577-133">Setting it equal to (SameSiteMode)(-1) indicates that no sameSite attribute should be included on the network with the cookie</span></span>

[!code-csharp[](samesite/snippets/Privacy.cshtml.cs?name=snippet)]

<span data-ttu-id="6e577-134">.Net Core 3,0 unterstützt die aktualisierten SameSite-Werte und fügt der `SameSiteMode.Unspecified` `SameSiteMode` Enumeration einen zusätzlichen Enumerationswert hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e577-134">.NET Core 3.0 supports the updated SameSite values and adds an extra enum value, `SameSiteMode.Unspecified` to the `SameSiteMode` enum.</span></span>
<span data-ttu-id="6e577-135">Dieser neue Wert gibt an, dass keine SameSite mit dem Cookie gesendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="6e577-135">This new value indicates no sameSite should be sent with the cookie.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

## <a name="december-patch-behavior-changes"></a><span data-ttu-id="6e577-136">Änderungen im Dezember-patchverhalten</span><span class="sxs-lookup"><span data-stu-id="6e577-136">December patch behavior changes</span></span>

<span data-ttu-id="6e577-137">Die spezifische Behavior Change für .NET Framework und .net Core 2,1 gibt an, `SameSite` wie die- `None` Eigenschaft den Wert interpretiert.</span><span class="sxs-lookup"><span data-stu-id="6e577-137">The specific behavior change for .NET Framework and .NET Core 2.1 is how the `SameSite` property interprets the `None` value.</span></span> <span data-ttu-id="6e577-138">Vor dem Patch `None` bedeutete der Wert "geben Sie das Attribut überhaupt nicht aus". nach dem Patch bedeutet dies, dass das Attribut mit dem Wert `None`"" ausgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6e577-138">Before the patch a value of `None` meant "Do not emit the attribute at all", after the patch it means "Emit the attribute with a value of `None`".</span></span> <span data-ttu-id="6e577-139">Nach dem Patch `(SameSiteMode)(-1)` bewirkt `SameSite` der Wert, dass das Attribut nicht ausgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="6e577-139">After the patch a `SameSite` value of `(SameSiteMode)(-1)` causes the attribute not to be emitted.</span></span>

<span data-ttu-id="6e577-140">Der Standardwert von SameSite für die Formular Authentifizierung und Sitzungs Zustands Cookies wurde `None` von `Lax`in geändert.</span><span class="sxs-lookup"><span data-stu-id="6e577-140">The default SameSite value for forms authentication and session state cookies was changed from `None` to `Lax`.</span></span>

::: moniker-end

## <a name="api-usage-with-samesite"></a><span data-ttu-id="6e577-141">API-Nutzung mit SameSite</span><span class="sxs-lookup"><span data-stu-id="6e577-141">API usage with SameSite</span></span>

<span data-ttu-id="6e577-142">[HttpContext. Response. Cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) ist Standard `Unspecified`mäßig auf festgelegt. Dies bedeutet, dass kein SameSite-Attribut, das dem Cookie hinzugefügt wurde, und der Client sein Standardverhalten verwendet (LAX für neue Browser, keine für die alten).</span><span class="sxs-lookup"><span data-stu-id="6e577-142">[HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) defaults to `Unspecified`, meaning no SameSite attribute added to the cookie and the client will use its default behavior (Lax for new browsers, None for old ones).</span></span> <span data-ttu-id="6e577-143">Der folgende Code zeigt, wie der Wert des Cookie-SameSite `SameSiteMode.Lax`-Werts in geändert wird:</span><span class="sxs-lookup"><span data-stu-id="6e577-143">The following code shows how to change the cookie SameSite value to `SameSiteMode.Lax`:</span></span>

[!code-csharp[](samesite/sample/Pages/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="6e577-144">Alle ASP.net Core Komponenten, die Cookies ausgeben, überschreiben die oben genannten Standardeinstellungen mit den Einstellungen, die für Ihre Szenarios</span><span class="sxs-lookup"><span data-stu-id="6e577-144">All ASP.NET Core components that emit cookies override the preceding defaults with settings appropriate for their scenarios.</span></span> <span data-ttu-id="6e577-145">Die überschriebenen vorangehenden Standardwerte haben sich nicht geändert.</span><span class="sxs-lookup"><span data-stu-id="6e577-145">The overridden preceding default values haven't changed.</span></span>

| <span data-ttu-id="6e577-146">Komponente</span><span class="sxs-lookup"><span data-stu-id="6e577-146">Component</span></span> | <span data-ttu-id="6e577-147">Cookie</span><span class="sxs-lookup"><span data-stu-id="6e577-147">cookie</span></span> | <span data-ttu-id="6e577-148">Standard</span><span class="sxs-lookup"><span data-stu-id="6e577-148">Default</span></span> |
| ------------- | ------------- |
| <xref:Microsoft.AspNetCore.Http.CookieBuilder> | <xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite> | `Unspecified` |
| <xref:Microsoft.AspNetCore.Http.HttpContext.Session>  | [<span data-ttu-id="6e577-149">SessionOptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="6e577-149">SessionOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.SessionOptions.Cookie) |`Lax` |
| <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.CookieTempDataProvider>  | [<span data-ttu-id="6e577-150">Cookietempdataprovideroptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="6e577-150">CookieTempDataProviderOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Mvc.CookieTempDataProviderOptions.Cookie) | `Lax` |
| <xref:Microsoft.AspNetCore.Antiforgery.IAntiforgery> | [<span data-ttu-id="6e577-151">Antiforgeryoptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="6e577-151">AntiforgeryOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions.Cookie)| `Strict` |
| [<span data-ttu-id="6e577-152">Cookie-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="6e577-152">Cookie Authentication</span></span>](xref:Microsoft.Extensions.DependencyInjection.CookieExtensions.AddCookie*) | [<span data-ttu-id="6e577-153">Cookieauthenticationoptions. Cookie</span><span class="sxs-lookup"><span data-stu-id="6e577-153">CookieAuthenticationOptions.Cookie</span></span>](xref:Microsoft.AspNetCore.Builder.CookieAuthenticationOptions.CookieName) | `Lax` |
| <xref:Microsoft.Extensions.DependencyInjection.TwitterExtensions.AddTwitter*> | [<span data-ttu-id="6e577-154">Twitteroptions. statecookie</span><span class="sxs-lookup"><span data-stu-id="6e577-154">TwitterOptions.StateCookie </span></span>](xref:Microsoft.AspNetCore.Authentication.Twitter.TwitterOptions.StateCookie) | `Lax`  |
| <span data-ttu-id="6e577-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [Remoteauthenticationoptions. correlationcookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span><span class="sxs-lookup"><span data-stu-id="6e577-155"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationHandler`1> | [RemoteAuthenticationOptions.CorrelationCookie](xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.CorrelationCookie)  | `None`</span></span> |
| <xref:Microsoft.Extensions.DependencyInjection.OpenIdConnectExtensions.AddOpenIdConnect*> | [<span data-ttu-id="6e577-156">Openidconnectoptions. noncecookie</span><span class="sxs-lookup"><span data-stu-id="6e577-156">OpenIdConnectOptions.NonceCookie</span></span>](xref:Microsoft.AspNetCore.Authentication.OpenIdConnect.OpenIdConnectOptions.NonceCookie)| `None` |
| [<span data-ttu-id="6e577-157">HttpContext. Response. Cookies. Append</span><span class="sxs-lookup"><span data-stu-id="6e577-157">HttpContext.Response.Cookies.Append</span></span>](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) | <xref:Microsoft.AspNetCore.Http.CookieOptions> | `Unspecified` |

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="6e577-158">ASP.net Core 3,1 und höher bietet die folgende SameSite-Unterstützung:</span><span class="sxs-lookup"><span data-stu-id="6e577-158">ASP.NET Core 3.1 and later provides the following SameSite support:</span></span>

* <span data-ttu-id="6e577-159">Definiert das auszugebende Verhalten `SameSiteMode.None` von neu.`SameSite=None`</span><span class="sxs-lookup"><span data-stu-id="6e577-159">Redefines the behavior of `SameSiteMode.None` to emit `SameSite=None`</span></span>
* <span data-ttu-id="6e577-160">Fügt einen neuen Wert `SameSiteMode.Unspecified` zum Weglassen des SameSite-Attributs hinzu.</span><span class="sxs-lookup"><span data-stu-id="6e577-160">Adds a new value `SameSiteMode.Unspecified` to omit the SameSite attribute.</span></span>
* <span data-ttu-id="6e577-161">Alle Cookies-APIs werden `Unspecified`standardmäßig auf eingestellt.</span><span class="sxs-lookup"><span data-stu-id="6e577-161">All cookies APIs default to `Unspecified`.</span></span> <span data-ttu-id="6e577-162">Einige Komponenten, die Cookies verwenden, legen die Werte spezifischer für Ihre Szenarios fest.</span><span class="sxs-lookup"><span data-stu-id="6e577-162">Some components that use cookies set values more specific to their scenarios.</span></span> <span data-ttu-id="6e577-163">Beispiele finden Sie in der obigen Tabelle.</span><span class="sxs-lookup"><span data-stu-id="6e577-163">See the table above for examples.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6e577-164">In ASP.net Core 3,0 und höher wurden die SameSite-Standardwerte geändert, um Konflikte mit inkonsistenten Client Standardwerten zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="6e577-164">In ASP.NET Core 3.0 and later the SameSite defaults were changed to avoid conflicting with inconsistent client defaults.</span></span> <span data-ttu-id="6e577-165">Die folgenden APIs haben den Standardwert von `SameSiteMode.Lax ` in `-1` geändert, um zu vermeiden, dass ein SameSite-Attribut für diese Cookies ausgegeben wird:</span><span class="sxs-lookup"><span data-stu-id="6e577-165">The following APIs have changed the default from `SameSiteMode.Lax ` to `-1` to avoid emitting a SameSite attribute for these cookies:</span></span>

* <span data-ttu-id="6e577-166"><xref:Microsoft.AspNetCore.Http.CookieOptions>wird mit " [HttpContext. Response. Cookies. Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*) " verwendet.</span><span class="sxs-lookup"><span data-stu-id="6e577-166"><xref:Microsoft.AspNetCore.Http.CookieOptions> used with [HttpContext.Response.Cookies.Append](xref:Microsoft.AspNetCore.Http.IResponseCookies.Append*)</span></span>
* <span data-ttu-id="6e577-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>wird als Factory für`CookieOptions`</span><span class="sxs-lookup"><span data-stu-id="6e577-167"><xref:Microsoft.AspNetCore.Http.CookieBuilder>  used as a factory for `CookieOptions`</span></span>
* [<span data-ttu-id="6e577-168">Cookiepolicyoptions. minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="6e577-168">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)

::: moniker-end

## <a name="history-and-changes"></a><span data-ttu-id="6e577-169">Verlauf und Änderungen</span><span class="sxs-lookup"><span data-stu-id="6e577-169">History and changes</span></span>

<span data-ttu-id="6e577-170">Die SameSite-Unterstützung wurde zunächst in ASP.net Core in 2,0 mithilfe des [Entwurfs Standards 2016](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1)implementiert.</span><span class="sxs-lookup"><span data-stu-id="6e577-170">SameSite support was first implemented in ASP.NET Core in 2.0 using the [2016 draft standard](https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1).</span></span> <span data-ttu-id="6e577-171">Der 2016-Standard war Opt-in.</span><span class="sxs-lookup"><span data-stu-id="6e577-171">The 2016 standard was opt-in.</span></span> <span data-ttu-id="6e577-172">ASP.net Core durch Festlegen mehrerer Cookies `Lax` standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="6e577-172">ASP.NET Core opted-in by setting several cookies to `Lax` by default.</span></span> <span data-ttu-id="6e577-173">Nachdem mehrere Authentifizierungs [Probleme](https://github.com/aspnet/Announcements/issues/318) aufgetreten sind, wurde die meisten SameSite-Nutzung [deaktiviert](https://github.com/aspnet/Announcements/issues/348).</span><span class="sxs-lookup"><span data-stu-id="6e577-173">After encountering several [issues](https://github.com/aspnet/Announcements/issues/318) with authentication, most SameSite usage was [disabled](https://github.com/aspnet/Announcements/issues/348).</span></span>

<span data-ttu-id="6e577-174">Im November 2019 wurden [Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) zum Aktualisieren von der 2016-Standard-auf den 2019-Standard ausgegeben.</span><span class="sxs-lookup"><span data-stu-id="6e577-174">[Patches](https://devblogs.microsoft.com/dotnet/net-core-November-2019/) were issued in November 2019 to update from the 2016 standard to the 2019 standard.</span></span> <span data-ttu-id="6e577-175">Der [2019-Entwurf der SameSite-Spezifikation](https://github.com/aspnet/Announcements/issues/390):</span><span class="sxs-lookup"><span data-stu-id="6e577-175">The [2019 draft of the SameSite specification](https://github.com/aspnet/Announcements/issues/390):</span></span>

* <span data-ttu-id="6e577-176">Ist **nicht** abwärts kompatibel mit dem 2016-Entwurf.</span><span class="sxs-lookup"><span data-stu-id="6e577-176">Is **not** backwards compatible with the 2016 draft.</span></span> <span data-ttu-id="6e577-177">Weitere Informationen finden Sie [unter unterstützen älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="6e577-177">For more information, see [Supporting older browsers](#sob) in this document.</span></span>
* <span data-ttu-id="6e577-178">Gibt an, dass Cookies `SameSite=Lax` standardmäßig als behandelt werden.</span><span class="sxs-lookup"><span data-stu-id="6e577-178">Specifies cookies are treated as `SameSite=Lax` by default.</span></span>
* <span data-ttu-id="6e577-179">Gibt Cookies an, die `SameSite=None` explizit bestätigt werden, um die standortübergreifende Übermittlung zu aktivieren, `Secure`sollte als gekennzeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="6e577-179">Specifies cookies that explicitly assert `SameSite=None` in order to enable cross-site delivery should be marked as `Secure`.</span></span> <span data-ttu-id="6e577-180">`None`ist ein neuer Eintrag zum ablehnen.</span><span class="sxs-lookup"><span data-stu-id="6e577-180">`None` is a new entry to opt out.</span></span>
* <span data-ttu-id="6e577-181">Wird von Patches unterstützt, die für ASP.net Core 2,1, 2,2 und 3,0 ausgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="6e577-181">Is supported by patches issued for ASP.NET Core 2.1, 2.2, and 3.0.</span></span> <span data-ttu-id="6e577-182">ASP.net Core 3,1 bietet zusätzliche Unterstützung für SameSite.</span><span class="sxs-lookup"><span data-stu-id="6e577-182">ASP.NET Core 3.1 has additional SameSite support.</span></span>
* <span data-ttu-id="6e577-183">Ist für die standardmäßige Aktivierung durch [Chrome](https://chromestatus.com/feature/5088147346030592) in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)vorgesehen.</span><span class="sxs-lookup"><span data-stu-id="6e577-183">Is scheduled to be enabled by [Chrome](https://chromestatus.com/feature/5088147346030592) by default in [Feb 2020](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html).</span></span> <span data-ttu-id="6e577-184">Die Umstellung auf diesen Standard in 2019 wurde gestartet.</span><span class="sxs-lookup"><span data-stu-id="6e577-184">Browsers started moving to this standard in 2019.</span></span>

## <a name="apis-impacted-by-the-change-from-the-2016-samesite-draft-standard-to-the-2019-draft-standard"></a><span data-ttu-id="6e577-185">Von der Änderung von 2016 SameSite Draft Standard zum 2019 Draft Standard betroffene APIs</span><span class="sxs-lookup"><span data-stu-id="6e577-185">APIs impacted by the change from the 2016 SameSite draft standard to the 2019 draft standard</span></span>

* [<span data-ttu-id="6e577-186">Http. samesitemode</span><span class="sxs-lookup"><span data-stu-id="6e577-186">Http.SameSiteMode</span></span>](xref:Microsoft.AspNetCore.Http.SameSiteMode)
* [<span data-ttu-id="6e577-187">Cookieoptions. SameSite</span><span class="sxs-lookup"><span data-stu-id="6e577-187">CookieOptions.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieOptions.SameSite)
* [<span data-ttu-id="6e577-188">Cookiebuilder. SameSite</span><span class="sxs-lookup"><span data-stu-id="6e577-188">CookieBuilder.SameSite</span></span>](xref:Microsoft.AspNetCore.Http.CookieBuilder.SameSite)
* [<span data-ttu-id="6e577-189">Cookiepolicyoptions. minimumsamesitepolicy</span><span class="sxs-lookup"><span data-stu-id="6e577-189">CookiePolicyOptions.MinimumSameSitePolicy</span></span>](xref:Microsoft.AspNetCore.Builder.CookiePolicyOptions.MinimumSameSitePolicy)
* <xref:Microsoft.Net.Http.Headers.SameSiteMode?displayProperty=fullName>
* <xref:Microsoft.Net.Http.Headers.SetCookieHeaderValue.SameSite?displayProperty=fullName>

<a name="sob"></a>

## <a name="supporting-older-browsers"></a><span data-ttu-id="6e577-190">Unterstützung älterer Browser</span><span class="sxs-lookup"><span data-stu-id="6e577-190">Supporting older browsers</span></span>

<span data-ttu-id="6e577-191">Der 2016 SameSite-Standard hat festgestellt, dass unbekannte Werte `SameSite=Strict` als Werte behandelt werden müssen.</span><span class="sxs-lookup"><span data-stu-id="6e577-191">The 2016 SameSite standard mandated that unknown values must be treated as `SameSite=Strict` values.</span></span> <span data-ttu-id="6e577-192">Apps, auf die von älteren Browsern zugegriffen wird, die den 2016 SameSite-Standard unterstützen, können unterbrechen, wenn Sie `None`eine SameSite-Eigenschaft mit dem Wert erhalten.</span><span class="sxs-lookup"><span data-stu-id="6e577-192">Apps accessed from older browsers which support the 2016 SameSite standard may break when they get a SameSite property with a value of `None`.</span></span> <span data-ttu-id="6e577-193">Web-Apps müssen die Browser Erkennung implementieren, wenn Sie ältere Browser unterstützen möchten.</span><span class="sxs-lookup"><span data-stu-id="6e577-193">Web apps must implement browser detection if they intend to support older browsers.</span></span> <span data-ttu-id="6e577-194">ASP.net Core implementiert die Browser Erkennung nicht, da die Werte von Benutzer-Agents stark flüchtig sind und häufig geändert werden.</span><span class="sxs-lookup"><span data-stu-id="6e577-194">ASP.NET Core doesn't implement browser detection because User-Agents values are highly volatile and change frequently.</span></span> <span data-ttu-id="6e577-195">Ein Erweiterungs Punkt in <xref:Microsoft.AspNetCore.CookiePolicy> ermöglicht das Plug in der Benutzer-Agent-spezifischen Logik.</span><span class="sxs-lookup"><span data-stu-id="6e577-195">An extension point in <xref:Microsoft.AspNetCore.CookiePolicy> allows plugging in User-Agent specific logic.</span></span>

<span data-ttu-id="6e577-196">Fügen `Startup.Configure`Sie in Code hinzu, <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> der aufruft <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> , bevor aufgerufen wird, oder *eine* Methode, die Cookies schreibt:</span><span class="sxs-lookup"><span data-stu-id="6e577-196">In `Startup.Configure`, add code that calls <xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*> before calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> or *any* method that writes cookies:</span></span>

[!code-csharp[](samesite/sample/Startup.cs?name=snippet5&highlight=18-19)]

<span data-ttu-id="6e577-197">Fügen `Startup.ConfigureServices`Sie in Code ähnlich dem folgenden hinzu:</span><span class="sxs-lookup"><span data-stu-id="6e577-197">In `Startup.ConfigureServices`, add code similar to the following:</span></span>

::: moniker range="= aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet)]

::: moniker-end

::: moniker range="< aspnetcore-3.1"

[!code-csharp[](samesite/sample/Startup.cs?name=snippet)]

::: moniker-end

<span data-ttu-id="6e577-198">Im vorherigen Beispiel `MyUserAgentDetectionLib.DisallowsSameSiteNone` ist eine vom Benutzer bereitgestellte Bibliothek, die erkennt, ob der Benutzer-Agent SameSite `None`nicht unterstützt:</span><span class="sxs-lookup"><span data-stu-id="6e577-198">In the preceding sample, `MyUserAgentDetectionLib.DisallowsSameSiteNone` is a user supplied library that detects if the user agent doesn't support SameSite `None`:</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippet2)]

<span data-ttu-id="6e577-199">Der folgende Code zeigt eine Beispiel `DisallowsSameSiteNone` Methode:</span><span class="sxs-lookup"><span data-stu-id="6e577-199">The following code shows a sample `DisallowsSameSiteNone` method:</span></span>

> [!WARNING]
> <span data-ttu-id="6e577-200">Der folgende Code dient nur zu Demonstrationszwecken:</span><span class="sxs-lookup"><span data-stu-id="6e577-200">The following code is for demonstration only:</span></span>
> * <span data-ttu-id="6e577-201">Er sollte nicht als "Fertig" betrachtet werden.</span><span class="sxs-lookup"><span data-stu-id="6e577-201">It should not be considered complete.</span></span>
> * <span data-ttu-id="6e577-202">Er wird nicht verwaltet oder unterstützt.</span><span class="sxs-lookup"><span data-stu-id="6e577-202">It is not maintained or supported.</span></span>

[!code-csharp[](samesite/sample/Startup31.cs?name=snippetX)]

## <a name="test-apps-for-samesite-problems"></a><span data-ttu-id="6e577-203">Testen von Apps für SameSite-Probleme</span><span class="sxs-lookup"><span data-stu-id="6e577-203">Test apps for SameSite problems</span></span>

<span data-ttu-id="6e577-204">Apps, die mit Remote Standorten interagieren, z. b. durch die Anmeldung von Drittanbietern, benötigen Folgendes:</span><span class="sxs-lookup"><span data-stu-id="6e577-204">Apps that interact with remote sites such as through third-party login need to:</span></span>

* <span data-ttu-id="6e577-205">Testen Sie die Interaktion in mehreren Browsern.</span><span class="sxs-lookup"><span data-stu-id="6e577-205">Test the interaction on multiple browsers.</span></span>
* <span data-ttu-id="6e577-206">Wenden Sie die in diesem Dokument erörterten [Erkennung und Entschärfung von cookiepolicy-Browsern](#sob) an.</span><span class="sxs-lookup"><span data-stu-id="6e577-206">Apply the [CookiePolicy browser detection and mitigation](#sob) discussed in this document.</span></span>

<span data-ttu-id="6e577-207">Testen Sie Web-Apps mithilfe einer Client Version, die das neue SameSite-Verhalten abonnieren kann.</span><span class="sxs-lookup"><span data-stu-id="6e577-207">Test web apps using a client version that can opt-in to the new SameSite behavior.</span></span> <span data-ttu-id="6e577-208">Chrome, Firefox und Chromium Edge verfügen über neue Feature-Flags, die für Tests verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="6e577-208">Chrome, Firefox, and Chromium Edge all have new opt-in feature flags that can be used for testing.</span></span> <span data-ttu-id="6e577-209">Nachdem Ihre APP die SameSite-Patches angewendet hat, testen Sie Sie mit älteren Client Versionen, insbesondere Safari.</span><span class="sxs-lookup"><span data-stu-id="6e577-209">After your app applies the SameSite patches, test it with older client versions, especially Safari.</span></span> <span data-ttu-id="6e577-210">Weitere Informationen finden Sie [unter unterstützen älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="6e577-210">For more information, see [Supporting older browsers](#sob) in this document.</span></span>

### <a name="test-with-chrome"></a><span data-ttu-id="6e577-211">Testen mit Chrome</span><span class="sxs-lookup"><span data-stu-id="6e577-211">Test with Chrome</span></span>

<span data-ttu-id="6e577-212">Chrome 78 + liefert irreführende Ergebnisse, da eine vorübergehende Entschärfung vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="6e577-212">Chrome 78+ gives misleading results because it has a temporary mitigation in place.</span></span> <span data-ttu-id="6e577-213">Die temporäre Chrome-Minderung von 78 und höher ermöglicht Cookies, die weniger als zwei Minuten alt sind.</span><span class="sxs-lookup"><span data-stu-id="6e577-213">The Chrome 78+ temporary mitigation allows cookies less than two minutes old.</span></span> <span data-ttu-id="6e577-214">Chrome 76 oder 77 mit aktivierten geeigneten testflags bietet genauere Ergebnisse.</span><span class="sxs-lookup"><span data-stu-id="6e577-214">Chrome 76 or 77 with the appropriate test flags enabled provides more accurate results.</span></span> <span data-ttu-id="6e577-215">Zum Testen des neuen SameSite-Verhaltens umschalten `chrome://flags/#same-site-by-default-cookies` in **aktiviert**.</span><span class="sxs-lookup"><span data-stu-id="6e577-215">To test the new SameSite behavior toggle `chrome://flags/#same-site-by-default-cookies` to **Enabled**.</span></span> <span data-ttu-id="6e577-216">Ältere Versionen von Chrome (75 und niedriger) werden mit der neuen `None` Einstellung gemeldet.</span><span class="sxs-lookup"><span data-stu-id="6e577-216">Older versions of Chrome (75 and below) are reported to fail with the new `None` setting.</span></span> <span data-ttu-id="6e577-217">Siehe [Unterstützung älterer Browser](#sob) in diesem Dokument.</span><span class="sxs-lookup"><span data-stu-id="6e577-217">See [Supporting older browsers](#sob) in this document.</span></span>

<span data-ttu-id="6e577-218">Google stellt keine älteren Chrome-Versionen zur Verfügung.</span><span class="sxs-lookup"><span data-stu-id="6e577-218">Google does not make older chrome versions available.</span></span> <span data-ttu-id="6e577-219">Befolgen Sie die Anweisungen unter [Herunterladen von Chrom](https://www.chromium.org/getting-involved/download-chromium) , um ältere Versionen von Chrome zu testen.</span><span class="sxs-lookup"><span data-stu-id="6e577-219">Follow the instructions at [Download Chromium](https://www.chromium.org/getting-involved/download-chromium) to test older versions of Chrome.</span></span> <span data-ttu-id="6e577-220">Laden Sie Chrome **nicht** von den von Ihnen bereitgestellten Links herunter, indem Sie nach älteren Versionen von Chrome suchen.</span><span class="sxs-lookup"><span data-stu-id="6e577-220">Do **not** download Chrome from links provided by searching for older versions of chrome.</span></span>

* [<span data-ttu-id="6e577-221">Chromium 76 Win64</span><span class="sxs-lookup"><span data-stu-id="6e577-221">Chromium 76 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/664998/)
* [<span data-ttu-id="6e577-222">Chromium 74 Win64</span><span class="sxs-lookup"><span data-stu-id="6e577-222">Chromium 74 Win64</span></span>](https://commondatastorage.googleapis.com/chromium-browser-snapshots/index.html?prefix=Win_x64/638880/)

<span data-ttu-id="6e577-223">Beginnend mit der Canary `80.0.3975.0`-Version kann die vorübergehende Entschärfung von Lax und Post zu Testzwecken deaktiviert werden. `--enable-features=SameSiteDefaultChecksMethodRigorously` dazu wird das neue Flag verwendet, um das Testen von Websites und Diensten im Endzustand des Features zuzulassen, bei dem die Entschärfung entfernt wurde.</span><span class="sxs-lookup"><span data-stu-id="6e577-223">Starting in Canary version `80.0.3975.0`, the Lax+POST temporary mitigation can be disabled for testing purposes using the new flag `--enable-features=SameSiteDefaultChecksMethodRigorously` to allow testing of sites and services in the eventual end state of the feature where the mitigation has been removed.</span></span> <span data-ttu-id="6e577-224">Weitere Informationen finden Sie in den Informationen zu den [Software Update](https://www.chromium.org/updates/same-site) -Projekten in der Projektwebsite.</span><span class="sxs-lookup"><span data-stu-id="6e577-224">For more information, see The Chromium Projects [SameSite Updates](https://www.chromium.org/updates/same-site)</span></span>

### <a name="test-with-safari"></a><span data-ttu-id="6e577-225">Testen mit Safari</span><span class="sxs-lookup"><span data-stu-id="6e577-225">Test with Safari</span></span>

<span data-ttu-id="6e577-226">Safari 12 hat den vorherigen Entwurf streng implementiert und schlägt fehl, `None` wenn der neue Wert in einem Cookie liegt.</span><span class="sxs-lookup"><span data-stu-id="6e577-226">Safari 12 strictly implemented the prior draft and fails when the new `None` value is in a cookie.</span></span> <span data-ttu-id="6e577-227">`None`wird über den Browser Erkennungs Code, der ältere Browser in diesem Dokument [unterstützt](#sob) , vermieden.</span><span class="sxs-lookup"><span data-stu-id="6e577-227">`None` is avoided via the browser detection code [Supporting older browsers](#sob) in this document.</span></span> <span data-ttu-id="6e577-228">Testen Sie Safari 12-, Safari 13-und WebKit-basierte Anmeldungen im Betriebssystem Format unter Verwendung von msal, Adal oder einer beliebigen Bibliothek, die Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="6e577-228">Test Safari 12, Safari 13, and WebKit based OS style logins using MSAL, ADAL or whatever library you are using.</span></span> <span data-ttu-id="6e577-229">Das Problem hängt von der zugrunde liegenden Betriebssystemversion ab.</span><span class="sxs-lookup"><span data-stu-id="6e577-229">The problem is dependent on the underlying OS version.</span></span> <span data-ttu-id="6e577-230">OSX-mujave (10,14) und IOS 12 haben bekanntermaßen Kompatibilitätsprobleme mit dem neuen SameSite-Verhalten.</span><span class="sxs-lookup"><span data-stu-id="6e577-230">OSX Mojave (10.14) and iOS 12 are known to have compatibility problems with the new SameSite behavior.</span></span> <span data-ttu-id="6e577-231">Durch ein Upgrade des Betriebssystems auf OSX Catalina (10,15) oder IOS 13 wird das Problem behoben.</span><span class="sxs-lookup"><span data-stu-id="6e577-231">Upgrading the OS to OSX Catalina (10.15) or iOS 13 fixes the problem.</span></span> <span data-ttu-id="6e577-232">Safari verfügt derzeit nicht über ein Opt-in-Flag zum Testen des neuen Spezifikations Verhaltens.</span><span class="sxs-lookup"><span data-stu-id="6e577-232">Safari does not currently have an opt-in flag for testing the new spec behavior.</span></span>

### <a name="test-with-firefox"></a><span data-ttu-id="6e577-233">Testen mit Firefox</span><span class="sxs-lookup"><span data-stu-id="6e577-233">Test with Firefox</span></span>

<span data-ttu-id="6e577-234">Die Firefox-Unterstützung für den neuen Standard kann auf Version 68 + getestet werden, indem `about:config` Sie sich auf der Seite `network.cookie.sameSite.laxByDefault`mit dem Feature-Flag anmelden.</span><span class="sxs-lookup"><span data-stu-id="6e577-234">Firefox support for the new standard can be tested on version 68+ by opting in on the `about:config` page with the feature flag `network.cookie.sameSite.laxByDefault`.</span></span> <span data-ttu-id="6e577-235">Es gab keine Berichte über Kompatibilitätsprobleme mit älteren Versionen von Firefox.</span><span class="sxs-lookup"><span data-stu-id="6e577-235">There haven't been reports of compatibility issues with older versions of Firefox.</span></span>

### <a name="test-with-edge-browser"></a><span data-ttu-id="6e577-236">Testen mit dem Edge-Browser</span><span class="sxs-lookup"><span data-stu-id="6e577-236">Test with Edge browser</span></span>

<span data-ttu-id="6e577-237">Edge unterstützt den alten SameSite-Standard.</span><span class="sxs-lookup"><span data-stu-id="6e577-237">Edge supports the old SameSite standard.</span></span> <span data-ttu-id="6e577-238">Edge Version 44 hat keine bekannten Kompatibilitätsprobleme mit dem neuen Standard.</span><span class="sxs-lookup"><span data-stu-id="6e577-238">Edge version 44 doesn't have any known compatibility problems with the new standard.</span></span>

### <a name="test-with-edge-chromium"></a><span data-ttu-id="6e577-239">Testen mit Edge (Chrom)</span><span class="sxs-lookup"><span data-stu-id="6e577-239">Test with Edge (Chromium)</span></span>

<span data-ttu-id="6e577-240">SameSite-Flags werden auf der `edge://flags/#same-site-by-default-cookies` Seite festgelegt.</span><span class="sxs-lookup"><span data-stu-id="6e577-240">SameSite flags are set on the `edge://flags/#same-site-by-default-cookies` page.</span></span> <span data-ttu-id="6e577-241">Es wurden keine Kompatibilitätsprobleme mit Edge-Chrom erkannt.</span><span class="sxs-lookup"><span data-stu-id="6e577-241">No compatibility issues were discovered with Edge Chromium.</span></span>

### <a name="test-with-electron"></a><span data-ttu-id="6e577-242">Testen mit einem Elektron</span><span class="sxs-lookup"><span data-stu-id="6e577-242">Test with Electron</span></span>

<span data-ttu-id="6e577-243">Zu den Electron-Versionen zählen ältere Versionen von Chromium.</span><span class="sxs-lookup"><span data-stu-id="6e577-243">Versions of Electron include older versions of Chromium.</span></span> <span data-ttu-id="6e577-244">Beispielsweise ist die Version des von Teams verwendeten Elektrons Chrom 66, das das ältere Verhalten zeigt.</span><span class="sxs-lookup"><span data-stu-id="6e577-244">For example, the version of Electron used by Teams is Chromium 66, which exhibits the older behavior.</span></span> <span data-ttu-id="6e577-245">Sie müssen ihre eigenen Kompatibilitätstests mit der Version des von Ihrem Produkt verwendeten-Elektronen durchführen.</span><span class="sxs-lookup"><span data-stu-id="6e577-245">You must perform your own compatibility testing with the version of Electron your product uses.</span></span> <span data-ttu-id="6e577-246">Weitere Informationen finden Sie [unter Unterstützung älterer Browser](#sob) im folgenden Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="6e577-246">See [Supporting older browsers](#sob) in the following section.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6e577-247">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="6e577-247">Additional resources</span></span>

* [<span data-ttu-id="6e577-248">Chromium-Blog: Entwickler: machen Sie sich bereit für neue SameSite = None; Sichere Cookie-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="6e577-248">Chromium Blog:Developers: Get Ready for New SameSite=None; Secure Cookie Settings</span></span>](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html)
* [<span data-ttu-id="6e577-249">Erläuterte SameSite-Cookies</span><span class="sxs-lookup"><span data-stu-id="6e577-249">SameSite cookies explained</span></span>](https://web.dev/samesite-cookies-explained/)
* [<span data-ttu-id="6e577-250">Patches vom November 2019</span><span class="sxs-lookup"><span data-stu-id="6e577-250">November 2019 Patches</span></span>](https://devblogs.microsoft.com/dotnet/net-core-November-2019/)

 ::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

| <span data-ttu-id="6e577-251">Beispiel</span><span class="sxs-lookup"><span data-stu-id="6e577-251">Sample</span></span>               | <span data-ttu-id="6e577-252">Dokument</span><span class="sxs-lookup"><span data-stu-id="6e577-252">Document</span></span> |
| ----------------- | ------------ |
| [<span data-ttu-id="6e577-253">.Net Core MVC</span><span class="sxs-lookup"><span data-stu-id="6e577-253">.NET Core MVC</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)  | <xref:security/samesite/mvc21> |
| [<span data-ttu-id="6e577-254">.Net Core-Razor Pages</span><span class="sxs-lookup"><span data-stu-id="6e577-254">.NET Core Razor Pages</span></span>](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21RazorPages)  | <xref:security/samesite/rp21> |

::: moniker-end

 ::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="6e577-255">Beispiel</span><span class="sxs-lookup"><span data-stu-id="6e577-255">Sample</span></span>               | <span data-ttu-id="6e577-256">Dokument</span><span class="sxs-lookup"><span data-stu-id="6e577-256">Document</span></span> |
| ----------------- | ------------ |
| <span data-ttu-id="6e577-257">[.Net Core Razor -Seiten](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span><span class="sxs-lookup"><span data-stu-id="6e577-257">[.NET Core Razor Pages](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore31RazorPages)</span></span>  | <xref:security/samesite/rp31> |

::: moniker-end
