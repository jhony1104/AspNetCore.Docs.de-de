---
title: Globalisierung und Lokalisierung in ASP.NET Core Blazor
author: guardrex
description: In diesem Artikel erfahren Sie, wie Sie Razor-Komponenten Benutzern mit verschiedenen Kulturen und Sprachen zur Verfügung stellen.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- Blazor
- SignalR
uid: blazor/globalization-localization
ms.openlocfilehash: 1b0db66b23c0caffc6b7c4e4af723c020609612a
ms.sourcegitcommit: d5d45d84fe488427d418de770000f7df44a08370
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81539660"
---
# <a name="aspnet-core-opno-locblazor-globalization-and-localization"></a><span data-ttu-id="ae1e9-103">Globalisierung und Lokalisierung in ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="ae1e9-103">ASP.NET Core Blazor globalization and localization</span></span>

<span data-ttu-id="ae1e9-104">Von [Luke Latham](https://github.com/guardrex) und [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="ae1e9-104">By [Luke Latham](https://github.com/guardrex) and [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="ae1e9-105">Razor-Komponenten können Benutzern mit verschiedenen Kulturen und Sprachen zur Verfügung gestellt werden.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-105">Razor components can be made accessible to users in multiple cultures and languages.</span></span> <span data-ttu-id="ae1e9-106">Die folgenden Szenarios zur Globalisierung und Lokalisierung von .NET sind verfügbar:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-106">The following .NET globalization and localization scenarios are available:</span></span>

* <span data-ttu-id="ae1e9-107">.NET-Ressourcensystem</span><span class="sxs-lookup"><span data-stu-id="ae1e9-107">.NET's resources system</span></span>
* <span data-ttu-id="ae1e9-108">Kulturspezifische Zahlen- und Datumsformatierung</span><span class="sxs-lookup"><span data-stu-id="ae1e9-108">Culture-specific number and date formatting</span></span>

<span data-ttu-id="ae1e9-109">Derzeit werden Lokalisierungsszenarios für ASP.NET Core eingeschränkt unterstützt:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-109">A limited set of ASP.NET Core's localization scenarios are currently supported:</span></span>

* <span data-ttu-id="ae1e9-110">`IStringLocalizer<>` in Blazor-Apps *wird unterstützt*</span><span class="sxs-lookup"><span data-stu-id="ae1e9-110">`IStringLocalizer<>` *is supported* in Blazor apps.</span></span>
* <span data-ttu-id="ae1e9-111">`IHtmlLocalizer<>`, `IViewLocalizer<>` und die Lokalisierung von Datenanmerkungen sind ASP.NET Core MVC-Szenarios und **werden nicht in Blazor-Apps unterstützt**.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-111">`IHtmlLocalizer<>`, `IViewLocalizer<>`, and Data Annotations localization are ASP.NET Core MVC scenarios and **not supported** in Blazor apps.</span></span>

<span data-ttu-id="ae1e9-112">Weitere Informationen finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-112">For more information, see <xref:fundamentals/localization>.</span></span>

## <a name="globalization"></a><span data-ttu-id="ae1e9-113">Globalisierung</span><span class="sxs-lookup"><span data-stu-id="ae1e9-113">Globalization</span></span>

<span data-ttu-id="ae1e9-114">Die `@bind`-Funktionalität von Blazor führt Formate durch und analysiert Werte zur Anzeige basierend auf der aktuellen Kultur des Benutzers.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-114">Blazor's `@bind` functionality performs formats and parses values for display based on the user's current culture.</span></span>

<span data-ttu-id="ae1e9-115">Der Zugriff auf die aktuelle Kultur kann über die <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>-Eigenschaft erfolgen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-115">The current culture can be accessed from the <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> property.</span></span>

<span data-ttu-id="ae1e9-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) wird für die folgenden Feldtypen (`<input type="{TYPE}" />`) verwendet:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-116">[CultureInfo.InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture) is used for the following field types (`<input type="{TYPE}" />`):</span></span>

* `date`
* `number`

<span data-ttu-id="ae1e9-117">Diese Feldtypen:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-117">The preceding field types:</span></span>

* <span data-ttu-id="ae1e9-118">werden basierend auf ihren entsprechenden browserbasierten Formatierungsregeln angezeigt.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-118">Are displayed using their appropriate browser-based formatting rules.</span></span>
* <span data-ttu-id="ae1e9-119">können keinen Freiformtext enthalten.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-119">Can't contain free-form text.</span></span>
* <span data-ttu-id="ae1e9-120">bieten Benutzerinteraktionsmerkmale basierend auf der Implementierung des Browsers.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-120">Provide user interaction characteristics based on the browser's implementation.</span></span>

<span data-ttu-id="ae1e9-121">Die folgenden Feldtypen verfügen über spezifische Formatierungsanforderungen und werden derzeit nicht von Blazor unterstützt, weil sie von keinem der gängigen Browser unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-121">The following field types have specific formatting requirements and aren't currently supported by Blazor because they aren't supported by all major browsers:</span></span>

* `datetime-local`
* `month`
* `week`

<span data-ttu-id="ae1e9-122">`@bind` unterstützt den `@bind:culture`-Parameter, um eine <xref:System.Globalization.CultureInfo?displayProperty=fullName>-Klasse zum Analysieren und Formatieren eines Werts.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-122">`@bind` supports the `@bind:culture` parameter to provide a <xref:System.Globalization.CultureInfo?displayProperty=fullName> for parsing and formatting a value.</span></span> <span data-ttu-id="ae1e9-123">Vom Festlegen einer Kultur wird abgeraten, wenn die Feldtypen `date` und `number` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-123">Specifying a culture isn't recommended when using the `date` and `number` field types.</span></span> <span data-ttu-id="ae1e9-124">`date` und `number` verfügen über integrierte Blazor-Unterstützung, die die erforderliche Kultur bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-124">`date` and `number` have built-in Blazor support that provides the required culture.</span></span>

## <a name="localization"></a><span data-ttu-id="ae1e9-125">Lokalisierung</span><span class="sxs-lookup"><span data-stu-id="ae1e9-125">Localization</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="ae1e9-126"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="ae1e9-126"> WebAssembly</span></span>

Blazor<span data-ttu-id="ae1e9-127">-WebAssembly-Apps legen die Kultur anhand der [Spracheinstellungen](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages) des Benutzers fest.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-127"> WebAssembly apps set the culture using the user's [language preference](https://developer.mozilla.org/docs/Web/API/NavigatorLanguage/languages).</span></span>

<span data-ttu-id="ae1e9-128">Legen Sie in `Program.Main` `CultureInfo.DefaultThreadCurrentCulture` und `CultureInfo.DefaultThreadCurrentUICulture` fest, um die Kultur explizit zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-128">To explicitly configure the culture, set `CultureInfo.DefaultThreadCurrentCulture` and `CultureInfo.DefaultThreadCurrentUICulture` in `Program.Main`.</span></span>

<span data-ttu-id="ae1e9-129">Die Blazor-Linkerkonfiguration für Blazor-WebAssembly-Apps entfernt standardmäßig Internationalisierungsinformationen, mit Ausnahme von explizit angeforderten Gebietsschemas.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-129">By default, Blazor's linker configuration for Blazor WebAssembly apps strips out internationalization information except for locales explicitly requested.</span></span> <span data-ttu-id="ae1e9-130">Weitere Informationen und Anleitungen zum Steuern des Verhaltens des Linkers finden Sie unter <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-130">For more information and guidance on controlling the linker's behavior, see <xref:host-and-deploy/blazor/configure-linker#configure-the-linker-for-internationalization>.</span></span>

<span data-ttu-id="ae1e9-131">Auch wenn für die meisten Benutzer die standardmäßig von Blazor ausgewählte Kultur möglicherweise ausreichend ist, ziehen Sie in Betracht, Benutzern die Möglichkeit zu geben, ihr bevorzugtes Gebietsschema anzugeben.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-131">While the culture that Blazor selects by default might be sufficient for most users, consider offering a way for users to specify their preferred locale.</span></span> <span data-ttu-id="ae1e9-132">Eine Blazor-WebAssembly-Beispiel-App mit Kulturauswahl finden Sie in der [LocSample](https://github.com/pranavkm/LocSample)-Lokalisierungs-Beispiel-App.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-132">For a Blazor WebAssembly sample app with a culture picker, see the [LocSample](https://github.com/pranavkm/LocSample) localization sample app.</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="ae1e9-133"> Server</span><span class="sxs-lookup"><span data-stu-id="ae1e9-133"> Server</span></span>

Blazor<span data-ttu-id="ae1e9-134"> Server-Apps werden mit [Lokalisierungsmiddleware](xref:fundamentals/localization#localization-middleware) lokalisiert.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-134"> Server apps are localized using [Localization Middleware](xref:fundamentals/localization#localization-middleware).</span></span> <span data-ttu-id="ae1e9-135">Die Middleware wählt die entsprechende Kultur für Benutzer aus, die Ressourcen von der App anfordern.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-135">The middleware selects the appropriate culture for users requesting resources from the app.</span></span>

<span data-ttu-id="ae1e9-136">Die Kultur kann mit einem der folgenden Ansätze festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-136">The culture can be set using one of the following approaches:</span></span>

* [<span data-ttu-id="ae1e9-137">Cookies</span><span class="sxs-lookup"><span data-stu-id="ae1e9-137">Cookies</span></span>](#cookies)
* [<span data-ttu-id="ae1e9-138">Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="ae1e9-138">Provide UI to choose the culture</span></span>](#provide-ui-to-choose-the-culture)

<span data-ttu-id="ae1e9-139">Weitere Informationen und Beispiele finden Sie unter <xref:fundamentals/localization>.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-139">For more information and examples, see <xref:fundamentals/localization>.</span></span>

#### <a name="cookies"></a><span data-ttu-id="ae1e9-140">Cookies</span><span class="sxs-lookup"><span data-stu-id="ae1e9-140">Cookies</span></span>

<span data-ttu-id="ae1e9-141">Ein Cookie für die Lokalisierungskultur kann die Kultur des Benutzers beibehalten.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-141">A localization culture cookie can persist the user's culture.</span></span> <span data-ttu-id="ae1e9-142">Das Cookie wird von der `OnGet`-Methode der Hostseite der App erstellt (*Pages/Host.cshtml.cs*).</span><span class="sxs-lookup"><span data-stu-id="ae1e9-142">The cookie is created by the `OnGet` method of the app's host page (*Pages/Host.cshtml.cs*).</span></span> <span data-ttu-id="ae1e9-143">Die Lokalisierungsmiddleware liest das Cookie bei aufeinanderfolgenden Anforderungen, um die Kultur des Benutzers festzulegen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-143">The Localization Middleware reads the cookie on subsequent requests to set the user's culture.</span></span> 

<span data-ttu-id="ae1e9-144">Durch Verwendung eines Cookies wird sichergestellt, wird sichergestellt, dass die WebSocket-Verbindung die Kultur ordnungsgemäß weitergeben kann.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-144">Use of a cookie ensures that the WebSocket connection can correctly propagate the culture.</span></span> <span data-ttu-id="ae1e9-145">Wenn die Lokalisierungsschemas auf dem URL-Pfad oder der Abfragezeichenfolge basieren, kann das Schema möglicherweise nicht mit WebSockets funktionieren, wodurch das Beibehalten der Kultur fehlschlägt.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-145">If localization schemes are based on the URL path or query string, the scheme might not be able to work with WebSockets, thus fail to persist the culture.</span></span> <span data-ttu-id="ae1e9-146">Daher wird die Verwendung eines Cookies für die Lokalisierungskultur empfohlen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-146">Therefore, use of a localization culture cookie is the recommended approach.</span></span>

<span data-ttu-id="ae1e9-147">Alle Vorgehensweisen können zum Zuweisen einer Kultur verwendet werden, wenn die Kultur in einem Lokalisierungscookie beibehalten wird.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-147">Any technique can be used to assign a culture if the culture is persisted in a localization cookie.</span></span> <span data-ttu-id="ae1e9-148">Wenn die App bereits über ein Lokalisierungsschema für serverseitiges ASP.NET Core verfügt, können Sie die vorhandene Lokalisierungsinfrastruktur der App weiterhin verwenden und das Lokalisierungskulturcookie innerhalb des Schemas der App festlegen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-148">If the app already has an established localization scheme for server-side ASP.NET Core, continue to use the app's existing localization infrastructure and set the localization culture cookie within the app's scheme.</span></span>

<span data-ttu-id="ae1e9-149">Im folgenden Beispiel wird veranschaulicht, wie die aktuelle Kultur in einem Cookie festgelegt werden kann, das von der Lokalisierungsmiddleware gelesen werden kann.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-149">The following example shows how to set the current culture in a cookie that can be read by the Localization Middleware.</span></span> <span data-ttu-id="ae1e9-150">Erstellen Sie eine Datei *Pages/_Host.cshtml.cs* mit dem folgenden Inhalt in der Blazor-Server-App:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-150">Create a *Pages/_Host.cshtml.cs* file with the following contents in the Blazor Server app:</span></span>

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

<span data-ttu-id="ae1e9-151">Die Lokalisierung wird mit der folgenden Ereignissequenz von der App verarbeitet:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-151">Localization is handled by the app in the following sequence of events:</span></span>

1. <span data-ttu-id="ae1e9-152">Der Browser sendet zunächst eine HTTP-Anforderung an die App.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-152">The browser sends an initial HTTP request to the app.</span></span>
1. <span data-ttu-id="ae1e9-153">Die Kultur wird von der Lokalisierungsmiddleware zugewiesen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-153">The culture is assigned by the Localization Middleware.</span></span>
1. <span data-ttu-id="ae1e9-154">Die `OnGet`-Methode in *_Host.cshtml.cs* speichert die Kultur im Rahmen der Reaktion in einem Cookie.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-154">The `OnGet` method in *_Host.cshtml.cs* persists the culture in a cookie as part of the response.</span></span>
1. <span data-ttu-id="ae1e9-155">Der Browser stellt eine WebSocket-Verbindung her, um einer interaktive Blazor-Serversitzung zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-155">The browser opens a WebSocket connection to create an interactive Blazor Server session.</span></span>
1. <span data-ttu-id="ae1e9-156">Die Lokalisierungsmiddleware liest das Cookie und weist die Kultur zu.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-156">The Localization Middleware reads the cookie and assigns the culture.</span></span>
1. <span data-ttu-id="ae1e9-157">Die Blazor-Serversitzung beginnt mit der richtigen Kultur.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-157">The Blazor Server session begins with the correct culture.</span></span>

#### <a name="provide-ui-to-choose-the-culture"></a><span data-ttu-id="ae1e9-158">Bereitstellen einer Benutzeroberfläche zum Auswählen der Kultur</span><span class="sxs-lookup"><span data-stu-id="ae1e9-158">Provide UI to choose the culture</span></span>

<span data-ttu-id="ae1e9-159">Zum Bereitstellen einer Benutzeroberfläche, um Benutzern das Auswählen einer Kultur zu ermöglichen, wird ein *Ansatz auf Grundlage von Umleitungen* empfohlen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-159">To provide UI to allow a user to select a culture, a *redirect-based approach* is recommended.</span></span> <span data-ttu-id="ae1e9-160">Dieses Verfahren ähnelt dem, was in einer Web-App passiert, wenn ein Benutzer versucht, auf eine sichere Ressource zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-160">The process is similar to what happens in a web app when a user attempts to access a secure resource.</span></span> <span data-ttu-id="ae1e9-161">Der Benutzer wird auf eine Anmeldeseite umgeleitet und dann zurück zur ursprünglichen Ressource.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-161">The user is redirected to a sign-in page and then redirected back to the original resource.</span></span> 

<span data-ttu-id="ae1e9-162">Die App behält die vom Benutzer ausgewählte Kultur mithilfe einer Umleitung an einen Controller bei.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-162">The app persists the user's selected culture via a redirect to a controller.</span></span> <span data-ttu-id="ae1e9-163">Der Controller legt die ausgewählte Kultur des Benutzers mit einem Cookie fest und leitet den Benutzer wieder an den ursprünglichen URI weiter.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-163">The controller sets the user's selected culture into a cookie and redirects the user back to the original URI.</span></span>

<span data-ttu-id="ae1e9-164">Erstellen Sie einen HTTP-Endpunkt auf dem Server zum Festlegen der ausgewählten Kultur eines Benutzers in einem Cookie, und leiten Sie ihn dann wieder an den ursprünglichen URI weiter:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-164">Establish an HTTP endpoint on the server to set the user's selected culture in a cookie and perform the redirect back to the original URI:</span></span>

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> <span data-ttu-id="ae1e9-165">Verwenden Sie das Ergebnis der `LocalRedirect`-Aktion, um Angriffe durch offene Umleitungen zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-165">Use the `LocalRedirect` action result to prevent open redirect attacks.</span></span> <span data-ttu-id="ae1e9-166">Weitere Informationen finden Sie unter <xref:security/preventing-open-redirects>.</span><span class="sxs-lookup"><span data-stu-id="ae1e9-166">For more information, see <xref:security/preventing-open-redirects>.</span></span>

<span data-ttu-id="ae1e9-167">Im folgenden Beispiel wird das Durchführen einer Umleitung veranschaulicht, wenn der Benutzer eine Kultur auswählt:</span><span class="sxs-lookup"><span data-stu-id="ae1e9-167">The following component shows an example of how to perform the initial redirection when the user selects a culture:</span></span>

```razor
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri)
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="ae1e9-168">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="ae1e9-168">Additional resources</span></span>

* <xref:fundamentals/localization>
