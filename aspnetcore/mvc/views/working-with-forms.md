---
title: Taghilfsprogramme in Formularen in ASP.NET Core
author: rick-anderson
description: Beschreibt die integrierten Taghilfsprogramme, die mit Formularen verwendet werden.
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: mvc/views/working-with-forms
ms.openlocfilehash: 5af532db35b858d157f61a6aca30f55d15e9ff1e
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/16/2020
ms.locfileid: "79416197"
---
# <a name="tag-helpers-in-forms-in-aspnet-core"></a><span data-ttu-id="d5c9a-103">Taghilfsprogramme in Formularen in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d5c9a-103">Tag Helpers in forms in ASP.NET Core</span></span>

<span data-ttu-id="d5c9a-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette) und [Jerrie Pelser](https://github.com/jerriep)</span><span class="sxs-lookup"><span data-stu-id="d5c9a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [N. Taylor Mullen](https://github.com/NTaylorMullen), [Dave Paquette](https://twitter.com/Dave_Paquette), and [Jerrie Pelser](https://github.com/jerriep)</span></span>

<span data-ttu-id="d5c9a-105">In diesem Dokument wird das Arbeiten mit Formularen und mit den HTML-Elementen, die häufig in Formularen verwendet werden, veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-105">This document demonstrates working with Forms and the HTML elements commonly used on a Form.</span></span> <span data-ttu-id="d5c9a-106">Das HTML-Element [Form](https://www.w3.org/TR/html401/interact/forms.html) stellt den primären Mechanismus bereit, den Web-Apps zum Bereitstellen von Daten an den Server verwenden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-106">The HTML [Form](https://www.w3.org/TR/html401/interact/forms.html) element provides the primary mechanism web apps use to post back data to the server.</span></span> <span data-ttu-id="d5c9a-107">In diesem Dokument werden überwiegend [Taghilfsprogramme](tag-helpers/intro.md) beschrieben sowie deren Einsatzmöglichkeiten zum produktiven Erstellen von robusten HTML-Formularen.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-107">Most of this document describes [Tag Helpers](tag-helpers/intro.md) and how they can help you productively create robust HTML forms.</span></span> <span data-ttu-id="d5c9a-108">Es wird empfohlen, [Introduction to Tag Helpers (Einführung in Taghilfsprogramme)](tag-helpers/intro.md) vor diesem Dokument zu lesen.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-108">We recommend you read [Introduction to Tag Helpers](tag-helpers/intro.md) before you read this document.</span></span>

<span data-ttu-id="d5c9a-109">Häufig stellen HTML-Hilfsprogramme eine Alternative zu einem bestimmten Taghilfsprogramm dar. Allerdings ersetzen Taghilfsprogramme HTML-Hilfsprogramme nicht, und es gibt nicht für jedes HTML-Hilfsprogramm ein Taghilfsprogramm.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-109">In many cases, HTML Helpers provide an alternative approach to a specific Tag Helper, but it's important to recognize that Tag Helpers don't replace HTML Helpers and there's not a Tag Helper for each HTML Helper.</span></span> <span data-ttu-id="d5c9a-110">Wenn es ein alternatives HTML-Hilfsprogramm gibt, wird dies erwähnt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-110">When an HTML Helper alternative exists, it's mentioned.</span></span>

<a name="my-asp-route-param-ref-label"></a>

## <a name="the-form-tag-helper"></a><span data-ttu-id="d5c9a-111">Das Taghilfsprogramm für Formulare</span><span class="sxs-lookup"><span data-stu-id="d5c9a-111">The Form Tag Helper</span></span>

<span data-ttu-id="d5c9a-112">Das Taghilfsprogramm für [Formulare](https://www.w3.org/TR/html401/interact/forms.html):</span><span class="sxs-lookup"><span data-stu-id="d5c9a-112">The [Form](https://www.w3.org/TR/html401/interact/forms.html) Tag Helper:</span></span>

* <span data-ttu-id="d5c9a-113">Generiert den HTML-Attributwert [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` für eine MVC-Controlleraktion oder eine benannte Route</span><span class="sxs-lookup"><span data-stu-id="d5c9a-113">Generates the HTML [\<FORM>](https://www.w3.org/TR/html401/interact/forms.html) `action` attribute value for a MVC controller action or named route</span></span>

* <span data-ttu-id="d5c9a-114">Generiert ein ausgeblendetes [Token für die Anforderungsüberprüfung](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages), um (bei Verwendung mit dem `[ValidateAntiForgeryToken]`-Attribut in der nachfolgenden HTML-Aktionsmethode) websiteübergreifende Anforderungsfälschung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-114">Generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method)</span></span>

* <span data-ttu-id="d5c9a-115">Stellt das `asp-route-<Parameter Name>`-Attribut bereit, bei dem `<Parameter Name>` zu den Routenwerten hinzugefügt wird.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-115">Provides the `asp-route-<Parameter Name>` attribute, where `<Parameter Name>` is added to the route values.</span></span> <span data-ttu-id="d5c9a-116">Die `routeValues`-Parameter von `Html.BeginForm` und `Html.BeginRouteForm` bieten eine ähnliche Funktionalität.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-116">The  `routeValues` parameters to `Html.BeginForm` and `Html.BeginRouteForm` provide similar functionality.</span></span>

* <span data-ttu-id="d5c9a-117">Verfügt über die alternativen HTML-Hilfsprogramme `Html.BeginForm` und `Html.BeginRouteForm`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-117">Has an HTML Helper alternative `Html.BeginForm` and `Html.BeginRouteForm`</span></span>

<span data-ttu-id="d5c9a-118">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-118">Sample:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterFormOnly.cshtml)]

<span data-ttu-id="d5c9a-119">Das zuvor erwähnte Taghilfsprogramm für Formulare generiert folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-119">The Form Tag Helper above generates the following HTML:</span></span>

```html
<form method="post" action="/Demo/Register">
    <!-- Input and Submit elements -->
    <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

<span data-ttu-id="d5c9a-120">Die MVC-Runtime generiert den `action`-Attributwert aus den Attributen `asp-controller` und `asp-action` des Taghilfsprogramms für Formulare.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-120">The MVC runtime generates the `action` attribute value from the Form Tag Helper attributes `asp-controller` and `asp-action`.</span></span> <span data-ttu-id="d5c9a-121">Das Taghilfsprogramm für Formulare generiert ebenfalls ein ausgeblendetes [Token für die Anforderungsüberprüfung](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages), um (bei Verwendung mit dem `[ValidateAntiForgeryToken]`-Attribut in der nachfolgenden HTML-Aktionsmethode) websiteübergreifende Anforderungsfälschung zu verhindern.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-121">The Form Tag Helper also generates a hidden [Request Verification Token](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) to prevent cross-site request forgery (when used with the `[ValidateAntiForgeryToken]` attribute in the HTTP Post action method).</span></span> <span data-ttu-id="d5c9a-122">Es ist schwierig, ein reines HTML-Formular vor websiteübergreifender Anforderungsfälschung zu schützen. Das Taghilfsprogramm für Formulare stellt diesen Dienst für Sie bereit.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-122">Protecting a pure HTML Form from cross-site request forgery is difficult, the Form Tag Helper provides this service for you.</span></span>

### <a name="using-a-named-route"></a><span data-ttu-id="d5c9a-123">Verwenden einer benannte Route</span><span class="sxs-lookup"><span data-stu-id="d5c9a-123">Using a named route</span></span>

<span data-ttu-id="d5c9a-124">Das `asp-route`-Attribut für Taghilfsprogramme kann ebenfalls ein Markup für das HTML-Attribut `action` generieren.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-124">The `asp-route` Tag Helper attribute can also generate markup for the HTML `action` attribute.</span></span> <span data-ttu-id="d5c9a-125">Eine App mit einer [Route](../../fundamentals/routing.md) namens `register` kann folgendes Markup für die Registrierungsseite verwenden:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-125">An app with a [route](../../fundamentals/routing.md)  named `register` could use the following markup for the registration page:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterRoute.cshtml)]

<span data-ttu-id="d5c9a-126">Viele der Ansichten im Ordner *Views/Account* (dieser wird generiert, wenn Sie eine neue Web-App mit *einzelnen Benutzerkonten* erstellen) enthalten das Attribut [asp-route-returnurl](xref:mvc/views/working-with-forms):</span><span class="sxs-lookup"><span data-stu-id="d5c9a-126">Many of the views in the *Views/Account* folder (generated when you create a new web app with *Individual User Accounts*) contain the [asp-route-returnurl](xref:mvc/views/working-with-forms) attribute:</span></span>

```cshtml
<form asp-controller="Account" asp-action="Login"
     asp-route-returnurl="@ViewData["ReturnUrl"]"
     method="post" class="form-horizontal" role="form">
```

>[!NOTE]
><span data-ttu-id="d5c9a-127">Durch die integrierten Vorlagen wird `returnUrl` nur automatisch aufgefüllt, wenn Sie versuchen, auf eine autorisierte Ressource zuzugreifen, aber nicht authentifiziert oder autorisiert sind.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-127">With the built in templates, `returnUrl` is only populated automatically when you try to access an authorized resource but are not authenticated or authorized.</span></span> <span data-ttu-id="d5c9a-128">Wenn Sie versuchen, einen nicht autorisierten Zugriff durchzuführen, leitet die Sicherheitsmiddleware Sie zur Anmeldeseite weiter, bei der `returnUrl` festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-128">When you attempt an unauthorized access, the security middleware redirects you to the login page with the `returnUrl` set.</span></span>

## <a name="the-form-action-tag-helper"></a><span data-ttu-id="d5c9a-129">Das Hilfsprogramm für Formularaktionen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-129">The Form Action Tag Helper</span></span>

<span data-ttu-id="d5c9a-130">Das Hilfsprogramm für Formularaktionen generiert das `formaction`-Attribut auf dem generierten `<button ...>`- oder `<input type="image" ...>`-Tag.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-130">The Form Action Tag Helper generates the `formaction` attribute on the generated `<button ...>` or `<input type="image" ...>` tag.</span></span> <span data-ttu-id="d5c9a-131">Das `formaction`-Attribut legt fest, wohin ein Formular seine Daten übermittelt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-131">The `formaction` attribute controls where a form submits its data.</span></span> <span data-ttu-id="d5c9a-132">Es stellt eine Bindung mit den [\<Eingabe>](https://www.w3.org/wiki/HTML/Elements/input)-Elementen des Typs `image` und den [\<Schaltfläche>](https://www.w3.org/wiki/HTML/Elements/button)-Elementen her.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-132">It binds to [\<input>](https://www.w3.org/wiki/HTML/Elements/input) elements of type `image` and [\<button>](https://www.w3.org/wiki/HTML/Elements/button) elements.</span></span> <span data-ttu-id="d5c9a-133">Das Hilfsprogramm für Formularaktionen ermöglicht die Verwendung von mehreren [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-`-Attributen, die steuern, welcher `formaction`-Link für das entsprechende Element generiert wird.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-133">The Form Action Tag Helper enables the usage of several [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) `asp-` attributes to control what `formaction` link is generated for the corresponding element.</span></span>

<span data-ttu-id="d5c9a-134">Folgende [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper)-Attribute werden zum Steuern des Werts von `formaction` unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-134">Supported [AnchorTagHelper](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper) attributes to control the value of `formaction`:</span></span>

|<span data-ttu-id="d5c9a-135">attribute</span><span class="sxs-lookup"><span data-stu-id="d5c9a-135">Attribute</span></span>|<span data-ttu-id="d5c9a-136">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="d5c9a-136">Description</span></span>|
|---|---|
|[<span data-ttu-id="d5c9a-137">asp-controller</span><span class="sxs-lookup"><span data-stu-id="d5c9a-137">asp-controller</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-controller)|<span data-ttu-id="d5c9a-138">Der Name des Controllers.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-138">The name of the controller.</span></span>|
|[<span data-ttu-id="d5c9a-139">asp-action</span><span class="sxs-lookup"><span data-stu-id="d5c9a-139">asp-action</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-action)|<span data-ttu-id="d5c9a-140">Der Name der Aktionsmethode.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-140">The name of the action method.</span></span>|
|[<span data-ttu-id="d5c9a-141">asp-area</span><span class="sxs-lookup"><span data-stu-id="d5c9a-141">asp-area</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-area)|<span data-ttu-id="d5c9a-142">Der Name des Bereichs.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-142">The name of the area.</span></span>|
|[<span data-ttu-id="d5c9a-143">asp-page</span><span class="sxs-lookup"><span data-stu-id="d5c9a-143">asp-page</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page)|<span data-ttu-id="d5c9a-144">Der Name der Razor Page.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-144">The name of the Razor page.</span></span>|
|[<span data-ttu-id="d5c9a-145">asp-page-handler</span><span class="sxs-lookup"><span data-stu-id="d5c9a-145">asp-page-handler</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-page-handler)|<span data-ttu-id="d5c9a-146">Der Name des Razor Page-Handlers.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-146">The name of the Razor page handler.</span></span>|
|[<span data-ttu-id="d5c9a-147">asp-route</span><span class="sxs-lookup"><span data-stu-id="d5c9a-147">asp-route</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route)|<span data-ttu-id="d5c9a-148">Der Name der Route.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-148">The name of the route.</span></span>|
|[<span data-ttu-id="d5c9a-149">asp-route-{value}</span><span class="sxs-lookup"><span data-stu-id="d5c9a-149">asp-route-{value}</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-route-value)|<span data-ttu-id="d5c9a-150">Ein einzelner URL-Routenwert.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-150">A single URL route value.</span></span> <span data-ttu-id="d5c9a-151">Beispiel: `asp-route-id="1234"`.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-151">For example, `asp-route-id="1234"`.</span></span>|
|[<span data-ttu-id="d5c9a-152">asp-all-route-data</span><span class="sxs-lookup"><span data-stu-id="d5c9a-152">asp-all-route-data</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-all-route-data)|<span data-ttu-id="d5c9a-153">Alle Routenwerte.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-153">All route values.</span></span>|
|[<span data-ttu-id="d5c9a-154">asp-fragment</span><span class="sxs-lookup"><span data-stu-id="d5c9a-154">asp-fragment</span></span>](xref:mvc/views/tag-helpers/builtin-th/anchor-tag-helper#asp-fragment)|<span data-ttu-id="d5c9a-155">Das URL-Fragment.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-155">The URL fragment.</span></span>|

### <a name="submit-to-controller-example"></a><span data-ttu-id="d5c9a-156">Beispiel des Übermittelns an den Controller</span><span class="sxs-lookup"><span data-stu-id="d5c9a-156">Submit to controller example</span></span>

<span data-ttu-id="d5c9a-157">Das folgende Markup sendet das Formular bei Auswahl der Eingabe oder der Schaltfläche an die `Index`-Aktion von `HomeController`:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-157">The following markup submits the form to the `Index` action of `HomeController` when the input or button are selected:</span></span>

```cshtml
<form method="post">
    <button asp-controller="Home" asp-action="Index">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-controller="Home" 
                                asp-action="Index">
</form>
```

<span data-ttu-id="d5c9a-158">Das bisherige Markup generiert folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-158">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home">
</form>
```

### <a name="submit-to-page-example"></a><span data-ttu-id="d5c9a-159">Beispiel des Übermittelns an die Seite</span><span class="sxs-lookup"><span data-stu-id="d5c9a-159">Submit to page example</span></span>

<span data-ttu-id="d5c9a-160">Das folgende Markup sendet das Formular an die `About`-Razor-Seite:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-160">The following markup submits the form to the `About` Razor Page:</span></span>

```cshtml
<form method="post">
    <button asp-page="About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-page="About">
</form>
```

<span data-ttu-id="d5c9a-161">Das bisherige Markup generiert folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-161">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/About">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/About">
</form>
```

### <a name="submit-to-route-example"></a><span data-ttu-id="d5c9a-162">Beispiel des Übermittelns an die Route</span><span class="sxs-lookup"><span data-stu-id="d5c9a-162">Submit to route example</span></span>

<span data-ttu-id="d5c9a-163">Betrachten Sie den `/Home/Test`-Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-163">Consider the `/Home/Test` endpoint:</span></span>

```csharp
public class HomeController : Controller
{
    [Route("/Home/Test", Name = "Custom")]
    public string Test()
    {
        return "This is the test page";
    }
}
```

<span data-ttu-id="d5c9a-164">Das folgende Markup sendet das Formular an den `/Home/Test`-Endpunkt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-164">The following markup submits the form to the `/Home/Test` endpoint.</span></span>

```cshtml
<form method="post">
    <button asp-route="Custom">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" asp-route="Custom">
</form>
```

<span data-ttu-id="d5c9a-165">Das bisherige Markup generiert folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-165">The previous markup generates following HTML:</span></span>

```html
<form method="post">
    <button formaction="/Home/Test">Click Me</button>
    <input type="image" src="..." alt="Or Click Me" formaction="/Home/Test">
</form>
```

## <a name="the-input-tag-helper"></a><span data-ttu-id="d5c9a-166">Das Taghilfsprogramm für die Eingabe</span><span class="sxs-lookup"><span data-stu-id="d5c9a-166">The Input Tag Helper</span></span>

<span data-ttu-id="d5c9a-167">Das Taghilfsprogramm für die Eingabe bindet ein HTML-Element [\<input>](https://www.w3.org/wiki/HTML/Elements/input) an einen Modellausdruck in Ihrer Razor-Ansicht.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-167">The Input Tag Helper binds an HTML [\<input>](https://www.w3.org/wiki/HTML/Elements/input) element to a model expression in your razor view.</span></span>

<span data-ttu-id="d5c9a-168">Syntax:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-168">Syntax:</span></span>

```cshtml
<input asp-for="<Expression Name>">
```

<span data-ttu-id="d5c9a-169">Das Taghilfsprogramm für die Eingabe:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-169">The Input Tag Helper:</span></span>

* <span data-ttu-id="d5c9a-170">Generiert die HTML-Attribute `id` und `name` für den Ausdrucksnamen, der im `asp-for`-Attribut angegeben ist.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-170">Generates the `id` and `name` HTML attributes for the expression name specified in the `asp-for` attribute.</span></span> <span data-ttu-id="d5c9a-171">`asp-for="Property1.Property2"` entspricht `m => m.Property1.Property2`.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-171">`asp-for="Property1.Property2"` is equivalent to `m => m.Property1.Property2`.</span></span> <span data-ttu-id="d5c9a-172">Der Name des Ausdrucks wird für den `asp-for`-Attributwert verwendet.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-172">The name of the expression is what is used for the `asp-for` attribute value.</span></span> <span data-ttu-id="d5c9a-173">Weitere Informationen finden Sie im Abschnitt [Ausdrucksnamen](#expression-names).</span><span class="sxs-lookup"><span data-stu-id="d5c9a-173">See the [Expression names](#expression-names) section for additional information.</span></span>

* <span data-ttu-id="d5c9a-174">Legt den HTML-Attributwert `type` basierend auf dem Modelltyp und den Attributen für die [Datenanmerkung](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) fest, die auf die Modelleigenschaft angewendet werden</span><span class="sxs-lookup"><span data-stu-id="d5c9a-174">Sets the HTML `type` attribute value based on the model type and  [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to the model property</span></span>

* <span data-ttu-id="d5c9a-175">Überschreibt den HTML-Attributwert `type` nicht, wenn ein solcher festgelegt ist</span><span class="sxs-lookup"><span data-stu-id="d5c9a-175">Won't overwrite the HTML `type` attribute value when one is specified</span></span>

* <span data-ttu-id="d5c9a-176">Generiert [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)-Validierungsattribute aus den Attributen für die [Datenanmerkung](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter), die auf Modelleigenschaften angewendet werden</span><span class="sxs-lookup"><span data-stu-id="d5c9a-176">Generates [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  validation attributes from [data annotation](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes applied to model properties</span></span>

* <span data-ttu-id="d5c9a-177">Verfügt über eine Überschneidung der HTML-Hilfsprogrammfeatures mit `Html.TextBoxFor` und `Html.EditorFor`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-177">Has an HTML Helper feature overlap with `Html.TextBoxFor` and `Html.EditorFor`.</span></span> <span data-ttu-id="d5c9a-178">Weitere Informationen finden Sie im Abschnitt **Alternative HTML-Hilfsprogramme für Taghilfsprogramme für die Eingabe**.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-178">See the **HTML Helper alternatives to Input Tag Helper** section for details.</span></span>

* <span data-ttu-id="d5c9a-179">Stellt die starke Typisierung bereit</span><span class="sxs-lookup"><span data-stu-id="d5c9a-179">Provides strong typing.</span></span> <span data-ttu-id="d5c9a-180">Wenn der Name der Eigenschaft geändert wird und Sie das Taghilfsprogramm nicht aktualisieren, wird Ihnen eine Fehlermeldung ähnlich der Folgenden angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-180">If the name of the property changes and you don't update the Tag Helper you'll get an error similar to the following:</span></span>

```
An error occurred during the compilation of a resource required to process
this request. Please review the following specific error details and modify
your source code appropriately.

Type expected
 'RegisterViewModel' does not contain a definition for 'Email' and no
 extension method 'Email' accepting a first argument of type 'RegisterViewModel'
 could be found (are you missing a using directive or an assembly reference?)
```

<span data-ttu-id="d5c9a-181">Das `Input`-Taghilfsprogramm legt das HTML-Attribut `type` basierend auf dem .NET-Typ fest.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-181">The `Input` Tag Helper sets the HTML `type` attribute based on the .NET type.</span></span> <span data-ttu-id="d5c9a-182">In der folgenden Tabelle werden einige (jedoch nicht alle) allgemeine .NET-Typen sowie generierte HTML-Typen aufgeführt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-182">The following table lists some common .NET types and generated HTML type (not every .NET type is listed).</span></span>

|<span data-ttu-id="d5c9a-183">.NET-Typ</span><span class="sxs-lookup"><span data-stu-id="d5c9a-183">.NET type</span></span>|<span data-ttu-id="d5c9a-184">Eingabetyp</span><span class="sxs-lookup"><span data-stu-id="d5c9a-184">Input Type</span></span>|
|---|---|
|<span data-ttu-id="d5c9a-185">Bool</span><span class="sxs-lookup"><span data-stu-id="d5c9a-185">Bool</span></span>|<span data-ttu-id="d5c9a-186">type="checkbox"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-186">type="checkbox"</span></span>|
|<span data-ttu-id="d5c9a-187">String</span><span class="sxs-lookup"><span data-stu-id="d5c9a-187">String</span></span>|<span data-ttu-id="d5c9a-188">type="text"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-188">type="text"</span></span>|
|<span data-ttu-id="d5c9a-189">Datetime</span><span class="sxs-lookup"><span data-stu-id="d5c9a-189">DateTime</span></span>|<span data-ttu-id="d5c9a-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span><span class="sxs-lookup"><span data-stu-id="d5c9a-190">type=["datetime-local"](https://developer.mozilla.org/docs/Web/HTML/Element/input/datetime-local)</span></span>|
|<span data-ttu-id="d5c9a-191">Byte</span><span class="sxs-lookup"><span data-stu-id="d5c9a-191">Byte</span></span>|<span data-ttu-id="d5c9a-192">type="number"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-192">type="number"</span></span>|
|<span data-ttu-id="d5c9a-193">Int</span><span class="sxs-lookup"><span data-stu-id="d5c9a-193">Int</span></span>|<span data-ttu-id="d5c9a-194">type="number"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-194">type="number"</span></span>|
|<span data-ttu-id="d5c9a-195">Single, Double</span><span class="sxs-lookup"><span data-stu-id="d5c9a-195">Single, Double</span></span>|<span data-ttu-id="d5c9a-196">type="number"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-196">type="number"</span></span>|

<span data-ttu-id="d5c9a-197">In der folgenden Tabelle werden einige allgemeine Attribute für die [Datenanmerkung](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) aufgeführt, die das Taghilfsprogramm für die Eingabe bestimmten Eingabetypen zuordnet (nicht jedes Validierungsattribut wird aufgeführt):</span><span class="sxs-lookup"><span data-stu-id="d5c9a-197">The following table shows some common [data annotations](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.iattributeadapter) attributes that the input tag helper will map to specific input types (not every validation attribute is listed):</span></span>

|<span data-ttu-id="d5c9a-198">attribute</span><span class="sxs-lookup"><span data-stu-id="d5c9a-198">Attribute</span></span>|<span data-ttu-id="d5c9a-199">Eingabetyp</span><span class="sxs-lookup"><span data-stu-id="d5c9a-199">Input Type</span></span>|
|---|---|
|<span data-ttu-id="d5c9a-200">[EmailAddress]</span><span class="sxs-lookup"><span data-stu-id="d5c9a-200">[EmailAddress]</span></span>|<span data-ttu-id="d5c9a-201">type="email"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-201">type="email"</span></span>|
|<span data-ttu-id="d5c9a-202">[Url]</span><span class="sxs-lookup"><span data-stu-id="d5c9a-202">[Url]</span></span>|<span data-ttu-id="d5c9a-203">type="url"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-203">type="url"</span></span>|
|<span data-ttu-id="d5c9a-204">[HiddenInput]</span><span class="sxs-lookup"><span data-stu-id="d5c9a-204">[HiddenInput]</span></span>|<span data-ttu-id="d5c9a-205">type="hidden"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-205">type="hidden"</span></span>|
|<span data-ttu-id="d5c9a-206">[Phone]</span><span class="sxs-lookup"><span data-stu-id="d5c9a-206">[Phone]</span></span>|<span data-ttu-id="d5c9a-207">type="tel"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-207">type="tel"</span></span>|
|<span data-ttu-id="d5c9a-208">[DataType(DataType.Password)]</span><span class="sxs-lookup"><span data-stu-id="d5c9a-208">[DataType(DataType.Password)]</span></span>|<span data-ttu-id="d5c9a-209">type="password"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-209">type="password"</span></span>|
|<span data-ttu-id="d5c9a-210">[DataType(DataType.Date)]</span><span class="sxs-lookup"><span data-stu-id="d5c9a-210">[DataType(DataType.Date)]</span></span>|<span data-ttu-id="d5c9a-211">type="date"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-211">type="date"</span></span>|
|<span data-ttu-id="d5c9a-212">[DataType(DataType.Time)]</span><span class="sxs-lookup"><span data-stu-id="d5c9a-212">[DataType(DataType.Time)]</span></span>|<span data-ttu-id="d5c9a-213">type="time"</span><span class="sxs-lookup"><span data-stu-id="d5c9a-213">type="time"</span></span>|

<span data-ttu-id="d5c9a-214">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-214">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](working-with-forms/sample/final/Views/Demo/RegisterInput.cshtml)]

<span data-ttu-id="d5c9a-215">Der oben stehende Code generiert folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-215">The code above generates the following HTML:</span></span>

```html
  <form method="post" action="/Demo/RegisterInput">
      Email:
      <input type="email" data-val="true"
             data-val-email="The Email Address field is not a valid email address."
             data-val-required="The Email Address field is required."
             id="Email" name="Email" value=""><br>
      Password:
      <input type="password" data-val="true"
             data-val-required="The Password field is required."
             id="Password" name="Password"><br>
      <button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

<span data-ttu-id="d5c9a-216">Die Datenanmerkungen, die auf die `Email`- und `Password`-Eigenschaft angewendet werden, generieren Metadaten im Modell.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-216">The data annotations applied to the `Email` and `Password` properties generate metadata on the model.</span></span> <span data-ttu-id="d5c9a-217">Das Taghilfsprogramm für die Eingabe nutzt die Metadaten des Modells und erzeugt [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)-`data-val-*`-Attribute (siehe [Modellvalidierung](../models/validation.md)).</span><span class="sxs-lookup"><span data-stu-id="d5c9a-217">The Input Tag Helper consumes the model metadata and produces [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5) `data-val-*` attributes (see [Model Validation](../models/validation.md)).</span></span> <span data-ttu-id="d5c9a-218">Diese Attribute beschreiben das Validierungssteuerelement, das den Eingabefeldern angefügt werden soll.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-218">These attributes describe the validators to attach to the input fields.</span></span> <span data-ttu-id="d5c9a-219">Dadurch wird die unaufdringliche Validierung für HTML5 und [jQuery](https://jquery.com/) bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-219">This provides unobtrusive HTML5 and [jQuery](https://jquery.com/) validation.</span></span> <span data-ttu-id="d5c9a-220">Die unaufdringlichen Attribute haben das Format `data-val-rule="Error Message"`, wobei Rule der Name der Validierungs Regel ist (z. b. `data-val-required`, `data-val-email`, `data-val-maxlength`usw.). Wenn eine Fehlermeldung im-Attribut bereitgestellt wird, wird Sie als Wert für das `data-val-rule`-Attribut angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-220">The unobtrusive attributes have the format `data-val-rule="Error Message"`, where rule is the name of the validation rule (such as `data-val-required`, `data-val-email`, `data-val-maxlength`, etc.) If an error message is provided in the attribute, it's displayed as the value for the `data-val-rule` attribute.</span></span> <span data-ttu-id="d5c9a-221">Es gibt ebenfalls Attribute im Format `data-val-ruleName-argumentName="argumentValue"`, die zusätzliche Details zur Regel bereitstellen, z.B. `data-val-maxlength-max="1024"`.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-221">There are also attributes of the form `data-val-ruleName-argumentName="argumentValue"` that provide additional details about the rule, for example, `data-val-maxlength-max="1024"` .</span></span>

### <a name="html-helper-alternatives-to-input-tag-helper"></a><span data-ttu-id="d5c9a-222">Alternative HTML-Hilfsprogramme zum Taghilfsprogramm für die Eingabe</span><span class="sxs-lookup"><span data-stu-id="d5c9a-222">HTML Helper alternatives to Input Tag Helper</span></span>

<span data-ttu-id="d5c9a-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` und `Html.EditorFor` verfügen über sich überschneidende Features mit dem Taghilfsprogramm für die Eingabe.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-223">`Html.TextBox`, `Html.TextBoxFor`, `Html.Editor` and `Html.EditorFor` have overlapping features with the Input Tag Helper.</span></span> <span data-ttu-id="d5c9a-224">Das Taghilfsprogramm für die Eingabe legt das `type`-Attribut automatisch fest, `Html.TextBox` und `Html.TextBoxFor` jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-224">The Input Tag Helper will automatically set the `type` attribute; `Html.TextBox` and `Html.TextBoxFor` won't.</span></span> <span data-ttu-id="d5c9a-225">`Html.Editor` und `Html.EditorFor` verarbeiten Auflistungen, komplexe Objekte und Vorlagen, das Taghilfsprogramm für die Eingabe jedoch nicht.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-225">`Html.Editor` and `Html.EditorFor` handle collections, complex objects and templates; the Input Tag Helper doesn't.</span></span> <span data-ttu-id="d5c9a-226">Das Taghilfsprogramm für die Eingabe, `Html.EditorFor` und `Html.TextBoxFor` sind stark typisiert (sie verwenden Lambdaausdrücke), `Html.TextBox` und `Html.Editor` jedoch nicht (sie verwenden Ausdrucksnamen).</span><span class="sxs-lookup"><span data-stu-id="d5c9a-226">The Input Tag Helper, `Html.EditorFor`  and  `Html.TextBoxFor` are strongly typed (they use lambda expressions); `Html.TextBox` and `Html.Editor` are not (they use expression names).</span></span>

### <a name="htmlattributes"></a><span data-ttu-id="d5c9a-227">HtmlAttributes</span><span class="sxs-lookup"><span data-stu-id="d5c9a-227">HtmlAttributes</span></span>

<span data-ttu-id="d5c9a-228">`@Html.Editor()` und `@Html.EditorFor()` verwenden einen speziellen `ViewDataDictionary`-Eintrag namens `htmlAttributes`, wenn die Standardvorlagen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-228">`@Html.Editor()` and `@Html.EditorFor()` use a special `ViewDataDictionary` entry named `htmlAttributes` when executing their default templates.</span></span> <span data-ttu-id="d5c9a-229">Dieses Verhalten kann optional mithilfe des `additionalViewData`-Parameters erweitert werden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-229">This behavior is optionally augmented using `additionalViewData` parameters.</span></span> <span data-ttu-id="d5c9a-230">Der Schlüssel „htmlAttributes“ berücksichtigt die Groß-/Kleinschreibung nicht.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-230">The key "htmlAttributes" is case-insensitive.</span></span> <span data-ttu-id="d5c9a-231">Der Schlüssel „htmlAttributes“ wird ähnlich wie das `htmlAttributes`-Objekt behandelt, das an Hilfsprogramme für die Eingabe wie `@Html.TextBox()` übergeben wird.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-231">The key "htmlAttributes" is handled similarly to the `htmlAttributes` object passed to input helpers like `@Html.TextBox()`.</span></span>

```cshtml
@Html.EditorFor(model => model.YourProperty, 
  new { htmlAttributes = new { @class="myCssClass", style="Width:100px" } })
```

### <a name="expression-names"></a><span data-ttu-id="d5c9a-232">Ausdrucksnamen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-232">Expression names</span></span>

<span data-ttu-id="d5c9a-233">Der `asp-for`-Attributwert stellt eine `ModelExpression`-Klasse sowie die rechte Seite eines Lambdaausdrucks dar.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-233">The `asp-for` attribute value is a `ModelExpression` and the right hand side of a lambda expression.</span></span> <span data-ttu-id="d5c9a-234">Somit wird `asp-for="Property1"` im generierten Code zu `m => m.Property1`. Dadurch benötigen Sie das Präfix `Model` nicht.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-234">Therefore, `asp-for="Property1"` becomes `m => m.Property1` in the generated code which is why you don't need to prefix with `Model`.</span></span> <span data-ttu-id="d5c9a-235">Sie können das \@-Zeichen verwenden, um einen Inlineausdruck zu starten und diesen vor `m.` zu verschieben:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-235">You can use the "\@" character to start an inline expression and move before the `m.`:</span></span>

```cshtml
@{
  var joe = "Joe";
}

<input asp-for="@joe">
```

<span data-ttu-id="d5c9a-236">Folgendes wird generiert:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-236">Generates the following:</span></span>

```html
<input type="text" id="joe" name="joe" value="Joe">
```

<span data-ttu-id="d5c9a-237">Mit den Auflistungseigenschaften generiert `asp-for="CollectionProperty[23].Member"` den gleichen Namen wie `asp-for="CollectionProperty[i].Member"`, wenn `i` den Wert `23` besitzt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-237">With collection properties, `asp-for="CollectionProperty[23].Member"` generates the same name as `asp-for="CollectionProperty[i].Member"` when `i` has the value `23`.</span></span>

<span data-ttu-id="d5c9a-238">Wenn ASP.NET Core MVC den Wert von `ModelExpression` berechnet, werden verschiedene Quellen untersucht, unter anderem `ModelState`.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-238">When ASP.NET Core MVC calculates the value of `ModelExpression`, it inspects several sources, including `ModelState`.</span></span> <span data-ttu-id="d5c9a-239">Gehen Sie von `<input type="text" asp-for="@Name">` aus.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-239">Consider `<input type="text" asp-for="@Name">`.</span></span> <span data-ttu-id="d5c9a-240">Das berechnete `value`-Attribut entspricht dem ersten der folgenden Werte, der nicht NULL ist:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-240">The calculated `value` attribute is the first non-null value from:</span></span>

* <span data-ttu-id="d5c9a-241">`ModelState`-Eintrag mit dem Schlüssel „Name“</span><span class="sxs-lookup"><span data-stu-id="d5c9a-241">`ModelState` entry with key "Name".</span></span>
* <span data-ttu-id="d5c9a-242">Ergebnis des Ausdrucks `Model.Name`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-242">Result of the expression `Model.Name`.</span></span>

### <a name="navigating-child-properties"></a><span data-ttu-id="d5c9a-243">Navigieren zu untergeordneten Eigenschaften</span><span class="sxs-lookup"><span data-stu-id="d5c9a-243">Navigating child properties</span></span>

<span data-ttu-id="d5c9a-244">Sie können ebenfalls mithilfe des Eigenschaftenpfads des Ansichtsmodells zu untergeordneten Eigenschaften navigieren.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-244">You can also navigate to child properties using the property path of the view model.</span></span> <span data-ttu-id="d5c9a-245">Betrachten Sie eine komplexere Modellklasse, die eine untergeordnete `Address`-Eigenschaft enthält.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-245">Consider a more complex model class that contains a child `Address` property.</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/AddressViewModel.cs?highlight=1,2,3,4&range=5-8)]

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/RegisterAddressViewModel.cs?highlight=8&range=5-13)]

<span data-ttu-id="d5c9a-246">In der Ansicht wird eine Bindung an `Address.AddressLine1` erstellt:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-246">In the view, we bind to `Address.AddressLine1`:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterAddress.cshtml?highlight=6)]

<span data-ttu-id="d5c9a-247">Folgender HTML-Code wird für `Address.AddressLine1` generiert:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-247">The following HTML is generated for `Address.AddressLine1`:</span></span>

```html
<input type="text" id="Address_AddressLine1" name="Address.AddressLine1" value="">
```

### <a name="expression-names-and-collections"></a><span data-ttu-id="d5c9a-248">Ausdrucksnamen und Auflistungen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-248">Expression names and Collections</span></span>

<span data-ttu-id="d5c9a-249">Beispiel für ein Modell, das ein Array von `Colors` enthält:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-249">Sample, a model containing an array of `Colors`:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/Person.cs?highlight=3&range=5-10)]

<span data-ttu-id="d5c9a-250">Die Aktionsmethode:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-250">The action method:</span></span>

```csharp
public IActionResult Edit(int id, int colorIndex)
{
    ViewData["Index"] = colorIndex;
    return View(GetPerson(id));
}
```

<span data-ttu-id="d5c9a-251">Die folgende Razor-Syntax veranschaulicht den Zugriff auf ein bestimmtes `Color`-Element:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-251">The following Razor shows how you access a specific `Color` element:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/EditColor.cshtml)]

<span data-ttu-id="d5c9a-252">Die Vorlage *Views/Shared/EditorTemplates/String.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-252">The *Views/Shared/EditorTemplates/String.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/String.cshtml)]

<span data-ttu-id="d5c9a-253">Beispiel mithilfe von `List<T>`:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-253">Sample using `List<T>`:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/ToDoItem.cs?range=3-8)]

<span data-ttu-id="d5c9a-254">Die folgende Razor-Syntax veranschaulicht das Durchlaufen einer Auflistung:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-254">The following Razor shows how to iterate over a collection:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Demo/Edit.cshtml)]

<span data-ttu-id="d5c9a-255">Die Vorlage *Views/Shared/EditorTemplates/ToDoItem.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-255">The *Views/Shared/EditorTemplates/ToDoItem.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/ToDoItem.cshtml)]

<span data-ttu-id="d5c9a-256">Wenn der Wert in einem Kontext wie `foreach` oder `asp-for` ausgeführt wird, sollte nach Möglichkeit `Html.DisplayFor` verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-256">`foreach` should be used if possible when the value is going to be used in an `asp-for` or `Html.DisplayFor` equivalent context.</span></span> <span data-ttu-id="d5c9a-257">Allgemein ist `for` besser als `foreach` (sofern das Szenario dies zulässt), weil kein Enumerator zugewiesen werden muss. Die Auswertung eines Indexers in einem LINQ-Ausdruck kann jedoch teuer sein und sollte auf ein Mindestmaß reduziert werden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-257">In general, `for` is better than `foreach` (if the scenario allows it) because it doesn't need to allocate an enumerator; however, evaluating an indexer in a LINQ expression can be expensive and should be minimized.</span></span>

&nbsp;

>[!NOTE]
><span data-ttu-id="d5c9a-258">Der oben stehende kommentierte Beispielcode veranschaulicht das Ersetzen eines Lambdaausdrucks mit dem `@`-Operator, um auf jedes `ToDoItem`-Objekt in der Liste zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-258">The commented sample code above shows how you would replace the lambda expression with the `@` operator to access each `ToDoItem` in the list.</span></span>

## <a name="the-textarea-tag-helper"></a><span data-ttu-id="d5c9a-259">Das Taghilfsprogramm für Textbereiche</span><span class="sxs-lookup"><span data-stu-id="d5c9a-259">The Textarea Tag Helper</span></span>

<span data-ttu-id="d5c9a-260">Das `Textarea Tag Helper`-Taghilfsprogramm ähnelt dem Taghilfsprogramm für die Eingabe.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-260">The `Textarea Tag Helper` tag helper is  similar to the Input Tag Helper.</span></span>

* <span data-ttu-id="d5c9a-261">Generiert die `id`- und `name`-Attribute sowie die Attribute für die Datenvalidierung aus dem Modell für ein [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea)-Element.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-261">Generates the `id` and `name` attributes, and the data validation attributes from the model for a [\<textarea>](https://www.w3.org/wiki/HTML/Elements/textarea) element.</span></span>

* <span data-ttu-id="d5c9a-262">Stellt die starke Typisierung bereit</span><span class="sxs-lookup"><span data-stu-id="d5c9a-262">Provides strong typing.</span></span>

* <span data-ttu-id="d5c9a-263">Alternatives HTML-Hilfsprogramm: `Html.TextAreaFor`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-263">HTML Helper alternative: `Html.TextAreaFor`</span></span>

<span data-ttu-id="d5c9a-264">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-264">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/DescriptionViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterTextArea.cshtml?highlight=4)]

<span data-ttu-id="d5c9a-265">Folgender HTML-Code wird generiert:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-265">The following HTML is generated:</span></span>

```html
<form method="post" action="/Demo/RegisterTextArea">
  <textarea data-val="true"
   data-val-maxlength="The field Description must be a string or array type with a maximum length of &#x27;1024&#x27;."
   data-val-maxlength-max="1024"
   data-val-minlength="The field Description must be a string or array type with a minimum length of &#x27;5&#x27;."
   data-val-minlength-min="5"
   id="Description" name="Description">
  </textarea>
  <button type="submit">Test</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-label-tag-helper"></a><span data-ttu-id="d5c9a-266">Das Taghilfsprogramm für Bezeichnungen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-266">The Label Tag Helper</span></span>

* <span data-ttu-id="d5c9a-267">generiert den Titel der Beschriftung und das `for`-Attribut in einem [\<label>](https://www.w3.org/wiki/HTML/Elements/label)-Element für einen Ausdrucksnamen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-267">Generates the label caption and `for` attribute on a [\<label>](https://www.w3.org/wiki/HTML/Elements/label) element for an expression name</span></span>

* <span data-ttu-id="d5c9a-268">Alternatives HTML-Hilfsprogramm: `Html.LabelFor`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-268">HTML Helper alternative: `Html.LabelFor`.</span></span>

<span data-ttu-id="d5c9a-269">`Label Tag Helper` bietet folgende Vorteile gegenüber einem reinen HTML-Bezeichnungselement:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-269">The `Label Tag Helper`  provides the following benefits over a pure HTML label element:</span></span>

* <span data-ttu-id="d5c9a-270">Sie erhalten den aussagekräftigen Bezeichnungswert aus dem `Display`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-270">You automatically get the descriptive label value from the `Display` attribute.</span></span> <span data-ttu-id="d5c9a-271">Der gewünschte Anzeigename kann sich mit der Zeit ändern, und die Kombination des `Display`-Attributs und des Taghilfsprogramms für Bezeichnungen wendet `Display` überall dort an, wo es verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-271">The intended display name might change over time, and the combination of `Display` attribute and Label Tag Helper will apply the `Display` everywhere it's used.</span></span>

* <span data-ttu-id="d5c9a-272">Weniger Markup im Quellcode</span><span class="sxs-lookup"><span data-stu-id="d5c9a-272">Less markup in source code</span></span>

* <span data-ttu-id="d5c9a-273">Starke Typisierung mit der Modelleigenschaft</span><span class="sxs-lookup"><span data-stu-id="d5c9a-273">Strong typing with the model property.</span></span>

<span data-ttu-id="d5c9a-274">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-274">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/SimpleViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterLabel.cshtml?highlight=4)]

<span data-ttu-id="d5c9a-275">Folgender HTML-Code wird für das `<label>`-Element generiert:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-275">The following HTML is generated for the `<label>` element:</span></span>

```html
<label for="Email">Email Address</label>
```

<span data-ttu-id="d5c9a-276">Das Taghilfsprogramm für Bezeichnungen hat den `for`-Attributwert „Email“ generiert, bei dem es sich um die ID handelt, die dem `<input>`-Element zugeordnet wird.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-276">The Label Tag Helper generated the `for` attribute value of "Email", which is the ID associated with the `<input>` element.</span></span> <span data-ttu-id="d5c9a-277">Die Taghilfsprogramme generieren konsistente `id`- und `for`-Elemente, sodass diese ordnungsgemäß zugeordnet werden können.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-277">The Tag Helpers generate consistent `id` and `for` elements so they can be correctly associated.</span></span> <span data-ttu-id="d5c9a-278">Die Beschriftung in diesem Beispiel ergibt sich aus dem `Display`-Attribut.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-278">The caption in this sample comes from the `Display` attribute.</span></span> <span data-ttu-id="d5c9a-279">Wenn das Modell kein `Display`-Attribut enthalten hat, würde die Beschriftung dem Eigenschaftsname des Ausdrucks entsprechen.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-279">If the model didn't contain a `Display` attribute, the caption would be the property name of the expression.</span></span>

## <a name="the-validation-tag-helpers"></a><span data-ttu-id="d5c9a-280">Die Taghilfsprogramme für die Validierung</span><span class="sxs-lookup"><span data-stu-id="d5c9a-280">The Validation Tag Helpers</span></span>

<span data-ttu-id="d5c9a-281">Es gibt zwei Taghilfsprogramme für die Validierung.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-281">There are two Validation Tag Helpers.</span></span> <span data-ttu-id="d5c9a-282">`Validation Message Tag Helper` (zeigt eine Validierungsmeldung für eine einzelne Eigenschaft im Modell an) und `Validation Summary Tag Helper` (zeigt eine Zusammenfassung der Validierungsfehler an).</span><span class="sxs-lookup"><span data-stu-id="d5c9a-282">The `Validation Message Tag Helper` (which displays a validation message for a single property on your model), and the `Validation Summary Tag Helper` (which displays a summary of validation errors).</span></span> <span data-ttu-id="d5c9a-283">`Input Tag Helper` fügt clientseitige HTML5-Validierungsattribute zu Eingabeelementen hinzu, die auf den Attributen für die Datenanmerkung Ihrer Modellklassen basieren.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-283">The `Input Tag Helper` adds HTML5 client side validation attributes to input elements based on data annotation attributes on your model classes.</span></span> <span data-ttu-id="d5c9a-284">Die Validierung wird ebenfalls auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-284">Validation is also performed on the server.</span></span> <span data-ttu-id="d5c9a-285">Das Taghilfsprogramm für die Validierung zeigt diese Fehlermeldungen an, wenn ein Validierungsfehler auftritt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-285">The Validation Tag Helper displays these error messages when a validation error occurs.</span></span>

### <a name="the-validation-message-tag-helper"></a><span data-ttu-id="d5c9a-286">Das Taghilfsprogramm für Validierungsmeldungen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-286">The Validation Message Tag Helper</span></span>

* <span data-ttu-id="d5c9a-287">Fügt das [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)-`data-valmsg-for="property"`-Attribut zum [span](https://developer.mozilla.org/docs/Web/HTML/Element/span)-Element hinzu, wodurch die Validierungsfehlermeldung an das Eingabefeld der angegebenen Modelleigenschaft angefügt wird.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-287">Adds the [HTML5](https://developer.mozilla.org/docs/Web/Guide/HTML/HTML5)  `data-valmsg-for="property"` attribute to the [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element, which attaches the validation error messages on the input field of the specified model property.</span></span> <span data-ttu-id="d5c9a-288">Wenn ein clientseitiger Validierungsfehler auftritt, zeigt [jQuery](https://jquery.com/) die Fehlermeldung im `<span>`-Element an.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-288">When a client side validation error occurs, [jQuery](https://jquery.com/) displays the error message in the `<span>` element.</span></span>

* <span data-ttu-id="d5c9a-289">Die Validierung wird ebenfalls auf dem Server ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-289">Validation also takes place on the server.</span></span> <span data-ttu-id="d5c9a-290">JavaScript ist auf Clients möglicherweise deaktiviert, und einige Validierungen können nur auf Serverseite ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-290">Clients may have JavaScript disabled and some validation can only be done on the server side.</span></span>

* <span data-ttu-id="d5c9a-291">Alternatives HTML-Hilfsprogramm: `Html.ValidationMessageFor`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-291">HTML Helper alternative: `Html.ValidationMessageFor`</span></span>

<span data-ttu-id="d5c9a-292">`Validation Message Tag Helper` wird mit dem `asp-validation-for`-Attribut eines [span](https://developer.mozilla.org/docs/Web/HTML/Element/span)-HTML-Elements verwendet.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-292">The `Validation Message Tag Helper`  is used with the `asp-validation-for` attribute on a HTML [span](https://developer.mozilla.org/docs/Web/HTML/Element/span) element.</span></span>

```cshtml
<span asp-validation-for="Email"></span>
```

<span data-ttu-id="d5c9a-293">Das Taghilfsprogramm für Validierungsmeldungen generiert folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-293">The Validation Message Tag Helper will generate the following HTML:</span></span>

```html
<span class="field-validation-valid"
  data-valmsg-for="Email"
  data-valmsg-replace="true"></span>
```

<span data-ttu-id="d5c9a-294">Im Allgemeinen verwenden Sie `Validation Message Tag Helper` nach einem `Input`-Taghilfsprogramm für die gleiche Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-294">You generally use the `Validation Message Tag Helper`  after an `Input` Tag Helper for the same property.</span></span> <span data-ttu-id="d5c9a-295">Dadurch werden alle Validierungsfehlermeldungen in der Nähe der Eingabe angezeigt, die den Fehler verursacht hat.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-295">Doing so displays any validation error messages near the input that caused the error.</span></span>

> [!NOTE]
> <span data-ttu-id="d5c9a-296">Für die clientseitige Validierung müssen Sie über eine Ansicht mit den richtigen JavaScript- und [jQuery](https://jquery.com/)-Skriptverweisen verfügen.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-296">You must have a view with the correct JavaScript and [jQuery](https://jquery.com/) script references in place for client side validation.</span></span> <span data-ttu-id="d5c9a-297">Weitere Informationen finden Sie unter [Modellvalidierung](../models/validation.md).</span><span class="sxs-lookup"><span data-stu-id="d5c9a-297">See [Model Validation](../models/validation.md) for more information.</span></span>

<span data-ttu-id="d5c9a-298">Wenn ein serverseitiger Validierungsfehler auftritt (wenn Sie z.B. eine benutzerdefinierte serverseitige Validierung durchführen oder die clientseitige Validierung deaktiviert ist), legt MVC diese Fehlermeldung als Text des `<span>`-Elements fest.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-298">When a server side validation error occurs (for example when you have custom server side validation or client-side validation is disabled), MVC places that error message as the body of the `<span>` element.</span></span>

```html
<span class="field-validation-error" data-valmsg-for="Email"
            data-valmsg-replace="true">
   The Email Address field is required.
</span>
```

### <a name="the-validation-summary-tag-helper"></a><span data-ttu-id="d5c9a-299">Das Taghilfsprogramm für Validierungszusammenfassungen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-299">The Validation Summary Tag Helper</span></span>

* <span data-ttu-id="d5c9a-300">Zielt `<div>`-Elemente mit dem `asp-validation-summary`-Attribut an</span><span class="sxs-lookup"><span data-stu-id="d5c9a-300">Targets `<div>` elements with the `asp-validation-summary` attribute</span></span>

* <span data-ttu-id="d5c9a-301">Alternatives HTML-Hilfsprogramm: `@Html.ValidationSummary`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-301">HTML Helper alternative: `@Html.ValidationSummary`</span></span>

<span data-ttu-id="d5c9a-302">`Validation Summary Tag Helper` wird verwendet, um eine Zusammenfassung der Validierungsmeldungen anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-302">The `Validation Summary Tag Helper`  is used to display a summary of validation messages.</span></span> <span data-ttu-id="d5c9a-303">Der `asp-validation-summary`-Attributwert kann Folgendem entsprechen:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-303">The `asp-validation-summary` attribute value can be any of the following:</span></span>

|<span data-ttu-id="d5c9a-304">asp-validation-summary</span><span class="sxs-lookup"><span data-stu-id="d5c9a-304">asp-validation-summary</span></span>|<span data-ttu-id="d5c9a-305">Validierungsmeldungen werden angezeigt</span><span class="sxs-lookup"><span data-stu-id="d5c9a-305">Validation messages displayed</span></span>|
|--- |--- |
|<span data-ttu-id="d5c9a-306">ValidationSummary.All</span><span class="sxs-lookup"><span data-stu-id="d5c9a-306">ValidationSummary.All</span></span>|<span data-ttu-id="d5c9a-307">Eigenschaften- und Modellebene</span><span class="sxs-lookup"><span data-stu-id="d5c9a-307">Property and model level</span></span>|
|<span data-ttu-id="d5c9a-308">ValidationSummary.ModelOnly</span><span class="sxs-lookup"><span data-stu-id="d5c9a-308">ValidationSummary.ModelOnly</span></span>|<span data-ttu-id="d5c9a-309">Modell</span><span class="sxs-lookup"><span data-stu-id="d5c9a-309">Model</span></span>|
|<span data-ttu-id="d5c9a-310">ValidationSummary.None</span><span class="sxs-lookup"><span data-stu-id="d5c9a-310">ValidationSummary.None</span></span>|<span data-ttu-id="d5c9a-311">Keine</span><span class="sxs-lookup"><span data-stu-id="d5c9a-311">None</span></span>|

### <a name="sample"></a><span data-ttu-id="d5c9a-312">Beispiel</span><span class="sxs-lookup"><span data-stu-id="d5c9a-312">Sample</span></span>

<span data-ttu-id="d5c9a-313">Im folgenden Beispiel enthält das Datenmodell `DataAnnotation`-Attribute, wodurch Validierungsfehlermeldungen im `<input>`-Element generiert werden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-313">In the following example, the data model has `DataAnnotation` attributes, which generates validation error messages on the `<input>` element.</span></span>  <span data-ttu-id="d5c9a-314">Das Taghilfsprogramm für die Validierung zeigt diese Fehlermeldung an, wenn ein Validierungsfehler auftritt:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-314">When a validation error occurs, the Validation Tag Helper displays the error message:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/RegisterViewModel.cs)]

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Demo/RegisterValidation.cshtml?highlight=4,6,8&range=1-10)]

<span data-ttu-id="d5c9a-315">Der generierte HTML-Code (wenn das Modell gültig ist):</span><span class="sxs-lookup"><span data-stu-id="d5c9a-315">The generated HTML (when the model is valid):</span></span>

```html
<form action="/DemoReg/Register" method="post">
  <div class="validation-summary-valid" data-valmsg-summary="true">
  <ul><li style="display:none"></li></ul></div>
  Email:  <input name="Email" id="Email" type="email" value=""
   data-val-required="The Email field is required."
   data-val-email="The Email field is not a valid email address."
   data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Email"></span><br>
  Password: <input name="Password" id="Password" type="password"
   data-val-required="The Password field is required." data-val="true"><br>
  <span class="field-validation-valid" data-valmsg-replace="true"
   data-valmsg-for="Password"></span><br>
  <button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

## <a name="the-select-tag-helper"></a><span data-ttu-id="d5c9a-316">Das Taghilfsprogramm für die Auswahl</span><span class="sxs-lookup"><span data-stu-id="d5c9a-316">The Select Tag Helper</span></span>

* <span data-ttu-id="d5c9a-317">Generiert das [select](https://www.w3.org/wiki/HTML/Elements/select)-Element und die zugehörigen [option](https://www.w3.org/wiki/HTML/Elements/option)-Elemente für die Eigenschaften des Modells.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-317">Generates [select](https://www.w3.org/wiki/HTML/Elements/select) and associated [option](https://www.w3.org/wiki/HTML/Elements/option) elements for properties of your model.</span></span>

* <span data-ttu-id="d5c9a-318">Verfügt über die alternativen HTML-Hilfsprogramme `Html.DropDownListFor` und `Html.ListBoxFor`</span><span class="sxs-lookup"><span data-stu-id="d5c9a-318">Has an HTML Helper alternative `Html.DropDownListFor` and `Html.ListBoxFor`</span></span>

<span data-ttu-id="d5c9a-319">Das `Select Tag Helper` `asp-for` gibt den Namen der Modelleigenschaft für das [select](https://www.w3.org/wiki/HTML/Elements/select)-Element an, und `asp-items` legt die [option](https://www.w3.org/wiki/HTML/Elements/option)-Elemente fest.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-319">The `Select Tag Helper` `asp-for` specifies the model property  name for the [select](https://www.w3.org/wiki/HTML/Elements/select) element  and `asp-items` specifies the [option](https://www.w3.org/wiki/HTML/Elements/option) elements.</span></span>  <span data-ttu-id="d5c9a-320">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-320">For example:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

<span data-ttu-id="d5c9a-321">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-321">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryViewModel.cs)]

<span data-ttu-id="d5c9a-322">Die `Index`-Methode initialisiert `CountryViewModel`, legt das ausgewählte Land fest und übergibt dieses an die `Index`-Ansicht.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-322">The `Index` method initializes the `CountryViewModel`, sets the selected country and passes it to the `Index` view.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=8-13)]

<span data-ttu-id="d5c9a-323">Die HTTP-POST-Methode `Index` zeigt die Auswahl an:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-323">The HTTP POST `Index` method displays the selection:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=15-27)]

<span data-ttu-id="d5c9a-324">Die Ansicht `Index`:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-324">The `Index` view:</span></span>

[!code-cshtml[](working-with-forms/sample/final/Views/Home/Index.cshtml?highlight=4)]

<span data-ttu-id="d5c9a-325">Diese generiert folgenden HTML-Code (wenn „CA“ ausgewählt ist):</span><span class="sxs-lookup"><span data-stu-id="d5c9a-325">Which generates the following HTML (with "CA" selected):</span></span>

```html
<form method="post" action="/">
     <select id="Country" name="Country">
       <option value="MX">Mexico</option>
       <option selected="selected" value="CA">Canada</option>
       <option value="US">USA</option>
     </select>
       <br /><button type="submit">Register</button>
     <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
   </form>
```

> [!NOTE]
> <span data-ttu-id="d5c9a-326">Es wird nicht empfohlen, `ViewBag` oder `ViewData` mit dem Taghilfsprogramm für die Auswahl zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-326">We don't recommend using `ViewBag` or `ViewData` with the Select Tag Helper.</span></span> <span data-ttu-id="d5c9a-327">Ein Ansichtsmodell kann MVC-Metadaten stabiler bereitstellen und ist in der Regel weniger problematisch.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-327">A view model is more robust at providing MVC metadata and generally less problematic.</span></span>

<span data-ttu-id="d5c9a-328">Beim `asp-for`-Attributwert handelt es sich um einen Sonderfall, bei dem kein `Model`-Präfix erforderlich ist. Für andere Attribute von Taghilfsprogrammen (z.B. `asp-items`) ist dies jedoch erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-328">The `asp-for` attribute value is a special case and doesn't require a `Model` prefix, the other Tag Helper attributes do (such as `asp-items`)</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Home/Index.cshtml?range=4)]

### <a name="enum-binding"></a><span data-ttu-id="d5c9a-329">Enumerationsbindung</span><span class="sxs-lookup"><span data-stu-id="d5c9a-329">Enum binding</span></span>

<span data-ttu-id="d5c9a-330">Häufig ist es sinnvoll, `<select>` mit einer `enum`-Eigenschaft zu verwenden und das `SelectListItem`-Element aus den `enum`-Werten zu generieren.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-330">It's often convenient to use `<select>` with an `enum` property and generate the `SelectListItem` elements from the `enum` values.</span></span>

<span data-ttu-id="d5c9a-331">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-331">Sample:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnumViewModel.cs?range=3-7)]

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs)]

<span data-ttu-id="d5c9a-332">Die `GetEnumSelectList`-Methode generiert ein `SelectList`-Objekt für eine Enumeration.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-332">The `GetEnumSelectList` method generates a `SelectList` object for an enum.</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEnum.cshtml?highlight=5)]

<span data-ttu-id="d5c9a-333">Sie können die Enumeratorliste mit dem `Display`-Attribut markieren, um eine umfangreichere Benutzeroberfläche zu erhalten:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-333">You can mark your enumerator list with the `Display` attribute to get a richer UI:</span></span>

[!code-csharp[](working-with-forms/sample/final/ViewModels/CountryEnum.cs?highlight=5,7)]

<span data-ttu-id="d5c9a-334">Folgender HTML-Code wird generiert:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-334">The following HTML is generated:</span></span>

```html
  <form method="post" action="/Home/IndexEnum">
         <select data-val="true" data-val-required="The EnumCountry field is required."
                 id="EnumCountry" name="EnumCountry">
             <option value="0">United Mexican States</option>
             <option value="1">United States of America</option>
             <option value="2">Canada</option>
             <option value="3">France</option>
             <option value="4">Germany</option>
             <option selected="selected" value="5">Spain</option>
         </select>
         <br /><button type="submit">Register</button>
         <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
    </form>
```

### <a name="option-group"></a><span data-ttu-id="d5c9a-335">Optionsgruppe</span><span class="sxs-lookup"><span data-stu-id="d5c9a-335">Option Group</span></span>

<span data-ttu-id="d5c9a-336">Das HTML-Element [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) wird generiert, wenn das Ansichtsmodell mindestens `SelectListGroup`-Objekte enthält.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-336">The HTML  [\<optgroup>](https://www.w3.org/wiki/HTML/Elements/optgroup) element is generated when the view model contains one or more `SelectListGroup` objects.</span></span>

<span data-ttu-id="d5c9a-337">`CountryViewModelGroup` teilt die `SelectListItem`-Elemente den Gruppen „North America“ (Nordamerika) und „Europe“ (Europa) zu:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-337">The `CountryViewModelGroup` groups the `SelectListItem` elements into the "North America" and "Europe" groups:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelGroup.cs?highlight=5,6,14,20,26,32,38,44&range=6-56)]

<span data-ttu-id="d5c9a-338">Die beiden Gruppen werden im Folgenden dargestellt:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-338">The two groups are shown below:</span></span>

![Beispiel für Optionsgruppen](working-with-forms/_static/grp.png)

<span data-ttu-id="d5c9a-340">Der generierte HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-340">The generated HTML:</span></span>

```html
 <form method="post" action="/Home/IndexGroup">
      <select id="Country" name="Country">
          <optgroup label="North America">
              <option value="MEX">Mexico</option>
              <option value="CAN">Canada</option>
              <option value="US">USA</option>
          </optgroup>
          <optgroup label="Europe">
              <option value="FR">France</option>
              <option value="ES">Spain</option>
              <option value="DE">Germany</option>
          </optgroup>
      </select>
      <br /><button type="submit">Register</button>
      <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
```

### <a name="multiple-select"></a><span data-ttu-id="d5c9a-341">Mehrfachauswahl</span><span class="sxs-lookup"><span data-stu-id="d5c9a-341">Multiple select</span></span>

<span data-ttu-id="d5c9a-342">Das Taghilfsprogramm für die Auswahl generiert automatisch das Attribut [multiple = "multiple"](https://w3c.github.io/html-reference/select.html), wenn die Eigenschaft, die im `asp-for`-Attribut angegeben wird, eine `IEnumerable`-Schnittstelle ist.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-342">The Select Tag Helper  will automatically generate the [multiple = "multiple"](https://w3c.github.io/html-reference/select.html)  attribute if the property specified in the `asp-for` attribute is an `IEnumerable`.</span></span> <span data-ttu-id="d5c9a-343">Betrachten Sie beispielsweise folgendes Modell:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-343">For example, given the following model:</span></span>

[!code-csharp[](../../mvc/views/working-with-forms/sample/final/ViewModels/CountryViewModelIEnumerable.cs?highlight=6)]

<span data-ttu-id="d5c9a-344">Mit folgender Ansicht</span><span class="sxs-lookup"><span data-stu-id="d5c9a-344">With the following view:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexMultiSelect.cshtml?highlight=4)]

<span data-ttu-id="d5c9a-345">generiert den folgenden HTML-Code:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-345">Generates the following HTML:</span></span>

```html
<form method="post" action="/Home/IndexMultiSelect">
    <select id="CountryCodes"
    multiple="multiple"
    name="CountryCodes"><option value="MX">Mexico</option>
<option value="CA">Canada</option>
<option value="US">USA</option>
<option value="FR">France</option>
<option value="ES">Spain</option>
<option value="DE">Germany</option>
</select>
    <br /><button type="submit">Register</button>
  <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
</form>
```

### <a name="no-selection"></a><span data-ttu-id="d5c9a-346">Keine Auswahl</span><span class="sxs-lookup"><span data-stu-id="d5c9a-346">No selection</span></span>

<span data-ttu-id="d5c9a-347">Wenn Sie die Option „not specified“ auf mehreren Seiten verwenden, können Sie eine Vorlage erstellen, um Wiederholungen aus dem HTML-Code zu entfernen:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-347">If you find yourself using the "not specified" option in multiple pages, you can create a template to eliminate repeating the HTML:</span></span>

[!code-HTML[](../../mvc/views/working-with-forms/sample/final/Views/Home/IndexEmptyTemplate.cshtml?highlight=5)]

<span data-ttu-id="d5c9a-348">Die Vorlage *Views/Shared/EditorTemplates/CountryViewModel.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-348">The *Views/Shared/EditorTemplates/CountryViewModel.cshtml* template:</span></span>

[!code-HTML[](working-with-forms/sample/final/Views/Shared/EditorTemplates/CountryViewModel.cshtml)]

<span data-ttu-id="d5c9a-349">Das Hinzufügen des HTML-Elements [\<option>](https://www.w3.org/wiki/HTML/Elements/option) ist nicht auf den Fall *Keine Auswahl* beschränkt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-349">Adding HTML [\<option>](https://www.w3.org/wiki/HTML/Elements/option) elements isn't limited to the *No selection* case.</span></span> <span data-ttu-id="d5c9a-350">Durch die folgende Ansichts- und Aktionsmethode wird beispielsweise HTML-Code generiert, der dem oben stehenden Code ähnelt:</span><span class="sxs-lookup"><span data-stu-id="d5c9a-350">For example, the following view and action method will generate HTML similar to the code above:</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?name=snippetNone)]

[!code-HTML[](working-with-forms/sample/final/Views/Home/IndexOption.cshtml)]

<span data-ttu-id="d5c9a-351">Das richtige `<option>`-Element (enthält das `selected="selected"`-Attribut) wird abhängig vom aktuellen `Country`-Wert ausgewählt.</span><span class="sxs-lookup"><span data-stu-id="d5c9a-351">The correct `<option>` element will be selected ( contain the `selected="selected"` attribute) depending on the current `Country` value.</span></span>

[!code-csharp[](working-with-forms/sample/final/Controllers/HomeController.cs?range=114-119)]

```html
 <form method="post" action="/Home/IndexEmpty">
      <select id="Country" name="Country">
          <option value="">&lt;none&gt;</option>
          <option value="MX">Mexico</option>
          <option value="CA" selected="selected">Canada</option>
          <option value="US">USA</option>
      </select>
      <br /><button type="submit">Register</button>
   <input name="__RequestVerificationToken" type="hidden" value="<removed for brevity>">
 </form>
 ```

## <a name="additional-resources"></a><span data-ttu-id="d5c9a-352">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d5c9a-352">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* [<span data-ttu-id="d5c9a-353">HTML-Formularelement</span><span class="sxs-lookup"><span data-stu-id="d5c9a-353">HTML Form element</span></span>](https://www.w3.org/TR/html401/interact/forms.html)
* [<span data-ttu-id="d5c9a-354">Token für die Anforderungsüberprüfung</span><span class="sxs-lookup"><span data-stu-id="d5c9a-354">Request Verification Token</span></span>](/aspnet/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages)
* <xref:mvc/models/model-binding>
* <xref:mvc/models/validation>
* [<span data-ttu-id="d5c9a-355">IAttributeAdapter-Schnittstelle</span><span class="sxs-lookup"><span data-stu-id="d5c9a-355">IAttributeAdapter Interface</span></span>](/dotnet/api/Microsoft.AspNetCore.Mvc.DataAnnotations.IAttributeAdapter)
* [<span data-ttu-id="d5c9a-356">Codeauschnitte für dieses Dokument</span><span class="sxs-lookup"><span data-stu-id="d5c9a-356">Code snippets for this document</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/working-with-forms/sample/final)
