---
title: Zu verhindern, dass Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core
author: steve-smith
description: Erfahren Sie, wie ein, um Angriffe auf Web-apps zu verhindern, in dem eine schädliche Website die Interaktion zwischen einem Clientbrowser und die app auswirken kann.
ms.author: riande
ms.custom: mvc
ms.date: 10/11/2018
uid: security/anti-request-forgery
ms.openlocfilehash: 4498bd88e7188ea6c0063aeec054bc20e13ed5f1
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086439"
---
# <a name="prevent-cross-site-request-forgery-xsrfcsrf-attacks-in-aspnet-core"></a>Zu verhindern, dass Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core

Durch [Steve Smith](https://ardalis.com/), [Fiyaz Hasan](https://twitter.com/FiyazBinHasan), und [Rick Anderson](https://twitter.com/RickAndMSFT)

Websiteübergreifende anforderungsfälschung (auch bekannt als XSRF oder CSRF-Angriffen ausgesprochen *finden Sie unter ausspricht*) ist ein Angriff gegen gehostete Web-apps, die eine böswilligen Web-app kann bei dem die Interaktion zwischen einem Clientbrowser und einer Web-app, die vertraut, die beeinflussen Browser. Diese Angriffe sind möglich, da Webbrowser einige Arten von Authentifizierungstoken automatisch mit jeder Anforderung an eine Website gesendet werden. Diese Form der Exploit ist auch bekannt als eine *One-Click-Angriffs* oder *Sitzung Vorderkante* , da der Angriff nutzt die der Benutzer zuvor Sitzung authentifiziert.

Ein Beispiel eines CSRF-Angriffs:

1. Ein Benutzer meldet sich in `www.good-banking-site.com` mit Formularauthentifizierung. Der Server authentifiziert den Benutzer und gibt eine Antwort, die ein Authentifizierungscookie enthält. Der Standort ist anfällig für Angriffe, da es sich um alle Anforderungen vertraut, die sie mit der ein gültiges Authentifizierungscookie erhält.
1. Der Benutzer besucht eine schädliche Website, `www.bad-crook-site.com`.

   Die bösartige Website, `www.bad-crook-site.com`, enthält ein HTML-Formular, das dem folgenden ähnelt:

   ```html
   <h1>Congratulations! You're a Winner!</h1>
   <form action="http://good-banking-site.com/api/account" method="post">
       <input type="hidden" name="Transaction" value="withdraw">
       <input type="hidden" name="Amount" value="1000000">
       <input type="submit" value="Click to collect your prize!">
   </form>
   ```

   Beachten Sie, dass des Formulars `action` Beiträge an anfälligen Website und nicht an die bösartige Website. Dies ist der Teil von "Cross-Site" für CSRF.

1. Der Benutzer wählt die Schaltfläche "Senden". Der Browser sendet die Anforderung und schließt automatisch das Authentifizierungscookie für die angeforderte Domäne `www.good-banking-site.com`.
1. Die Anforderung ausgeführt wird, auf die `www.good-banking-site.com` Server mit dem Kontext des Benutzers Authentifizierung und können Aktionen, die ein authentifizierter Benutzer ausführen darf.

Zusätzlich zu dem Szenario, in dem sich der Benutzer auf die Schaltfläche zum Senden des Formulars auswählt, können die bösartige Website:

* Führen Sie ein Skript, das automatisch auf das Formular übermittelt.
* Senden Sie die Übermittlung des Formulars wird als eine AJAX-Anforderung.
* Blenden Sie das Formular mit CSS.

Diese alternativen Szenarien erforderlich keine Aktion oder die Eingabe des Benutzers als zunächst auf die bösartige Website.

Mithilfe von HTTPS nicht verhindert, dass eine CSRF-Angriffe. Kann die bösartige Website senden ein `https://www.good-banking-site.com/` Anforderung so einfach wie eine unsichere Anforderung senden kann.

Einige Angriffe Zielen Endpunkte, die auf den GET-Anforderungen, Antworten in diesem Fall ein Bildtag dienen zum Ausführen der Aktion. Diese Form des Angriffs ist üblich, auf der Forum-Websites, die Bilder zulässig, aber Hiermit blockieren Sie JavaScript. Apps, die statusänderung zu GET-Anforderungen, bei dem Variablen oder Ressourcen geändert werden, sind anfällig für Angriffe. **GET-Anforderungen, die Status ändern, sind unsicher. Eine bewährte Methode besteht darin Zustand für eine GET-Anforderung nie zu ändern.**

CSRF-Angriffe sind für Web-apps, die Cookies für die Authentifizierung zu verwenden, da möglich:

* Speichern Browser die Cookies, die von einer Web-app ausgestellt.
* Gespeicherten Cookies enthalten die Sitzungscookies für authentifizierte Benutzer.
* Browser senden, dass alle Cookies mit einer Domäne zur Web-app verknüpft ist jede Anforderung unabhängig davon, wie die Anforderung an die app im Browser generiert wurde.

Allerdings CSRF-Angriffe sind nicht beschränkt auf Cookies ausnutzen. Z. B. Basis-und Digestauthentifizierung sind auch anfällig. Nachdem ein Benutzer mit der Standard oder Digest-Authentifizierung anmeldet, sendet der Browser die Anmeldeinformationen automatisch bis die Sitzung&dagger; endet.

&dagger;In diesem Kontext *Sitzung* bezieht sich auf die clientseitige Sitzung während der der Benutzer wird authentifiziert. Es ist, die nicht mit einem serverseitigen Sitzungen oder [ASP.NET Core-Sitzungsmiddleware](xref:fundamentals/app-state).

Benutzer können sich gegen CSRF-Schwachstellen durch Vorsichtsmaßnahmen schützen:

* Melden Sie sich auf die Web-apps nach deren Verwendung.
* Deaktivieren der Browsercookies in regelmäßigen Abständen.

CSRF-Sicherheitsrisiken sind jedoch im Grunde ein Problem mit der Web-app nicht der Endbenutzer.

## <a name="authentication-fundamentals"></a>Grundlagen der Authentifizierung

Cookie-basierte Authentifizierung ist eine gängige Form der Authentifizierung. Tokenbasierte Authentifizierungssysteme wachsen Beliebtheit, insbesondere für Single Page Applications (SPAs).

### <a name="cookie-based-authentication"></a>Cookie-basierte Authentifizierung

Wenn ein Benutzer authentifiziert, mit ihrem Benutzernamen und Kennwort, sind sie ausgestellt ein Token, mit der ein Authentifizierungsticket, die für die Authentifizierung und Autorisierung verwendet werden kann. Das Token wird gespeichert, da ein Cookie an, die mit jeder Anforderung des Clients ist. Erstellen und überprüfen dieses Cookie wird durch die Cookieauthentifizierungs-Middleware ausgeführt. Die [Middleware](xref:fundamentals/middleware/index) einen Benutzerprinzipal in einem verschlüsselten Cookie serialisiert. Bei nachfolgenden Anforderungen, die Middleware überprüft das Cookie, erstellt den Prinzipal aus, und weist den Prinzipal, der [Benutzer](/dotnet/api/microsoft.aspnetcore.http.httpcontext.user) Eigenschaft ["HttpContext"](/dotnet/api/microsoft.aspnetcore.http.httpcontext).

### <a name="token-based-authentication"></a>Tokenbasierte Authentifizierung

Wenn ein Benutzer authentifiziert ist, können sie ein Token (kein Antifälschungstoken) ausgegeben. Das Token enthält Benutzerinformationen in Form von [Ansprüche](/dotnet/framework/security/claims-based-identity-model) oder ein Verweistoken, die die app auf Benutzerstatus, die in der app verwaltet verweist. Wenn ein Benutzer versucht, die Zugriff auf eine Ressource, die eine Authentifizierung erforderlich, wird das Token an die app mit einer zusätzlichen Authorization-Header in Form von Trägertoken, das gesendet. Dadurch wird die app zustandslos. In jeder nachfolgenden Anforderung wird das Token für die serverseitige Validierung in der Anforderung übergeben. Dieses Token ist nicht *verschlüsselte*; es *codiert*. Auf dem Server wird das Token decodiert, für den Zugriff auf ihre Informationen. Um das Token bei nachfolgenden Anforderungen zu senden, müssen Sie das Token im lokalen Speicher des Browsers gespeichert. Werden Sie keine Bedenken bezüglich eines CSRF-Sicherheitsrisiko, wenn das Token im lokalen Speicher des Browsers gespeichert ist. CSRF ist ein Problem auf, wenn das Token in einem Cookie gespeichert wird.

### <a name="multiple-apps-hosted-at-one-domain"></a>Mehrere apps, die in einer Domäne gehostet werden.

Freigegebene Hostingumgebungen sind anfällig für Session Hijacking, CSRF-Anmeldung und andere Angriffe.

Obwohl `example1.contoso.net` und `example2.contoso.net` sind verschiedene Hosts besteht ein implizites Vertrauensverhältnis zwischen Hosts in der `*.contoso.net` Domäne. Diese implizite Vertrauensstellung kann potenziell nicht vertrauenswürdige Hosts die Cookies des jeweils anderen auswirken (die gleichen Ursprungs-Richtlinien, die steuern, AJAX-Anforderungen unbedingt gelten nicht für HTTP-Cookies).

Angriffe, die vertrauenswürdige Cookies zwischen apps, die in der gleichen Domäne gehostet ausnutzen können verhindert werden, durch die Domänen nicht freigegeben wird. Wenn jede app in einer eigenen Domäne gehostet wird, besteht keine Vertrauensstellung für implizite Cookie ausnutzen.

## <a name="aspnet-core-antiforgery-configuration"></a>ASP.NET Core-antiforgery-Konfiguration

> [!WARNING]
> ASP.NET Core implementiert antiforgery mit [ASP.NET Core-Datenschutz](xref:security/data-protection/introduction). Stapel für den Schutz von Daten muss konfiguriert werden, um in einer Serverfarm zu arbeiten. Finden Sie unter [konfigurieren Sie den Schutz von Daten von](xref:security/data-protection/configuration/overview) für Weitere Informationen.

In ASP.NET Core 2.0 oder höher der [FormTagHelper](xref:mvc/views/working-with-forms#the-form-tag-helper) fälschungssicherheitstoken in HTML-Formularelemente einfügt. Das folgende Markup in einer Razor-Datei wird automatisch fälschungssicherheitstoken generiert:

```cshtml
<form method="post">
    ...
</form>
```

Auf ähnliche Weise [IHtmlHelper.BeginForm](/dotnet/api/microsoft.aspnetcore.mvc.rendering.ihtmlhelper.beginform) fälschungssicherheitstoken standardmäßig generiert, wenn die-Methode des Formulars GET nicht ist.

Erfolgt die automatische Generierung von fälschungssicherheitstoken für HTML-Formularelemente bei der `<form>` -Tag enthält den `method="post"` -Attribut und eine der folgenden erfüllt sind:

* Das Action-Attribut ist leer (`action=""`).
* Das Action-Attribut ist nicht angegeben (`<form method="post">`).

Automatische Generierung von fälschungssicherheitstoken für HTML-Formularelemente kann deaktiviert werden:

* Deaktivieren Sie explizit die antiforgery Token mit den `asp-antiforgery` Attribut:

  ```cshtml
  <form method="post" asp-antiforgery="false">
      ...
  </form>
  ```

* Das Formularelement ist entschieden-Out-of Taghilfsprogramme mit dem Taghilfsprogramm [! melden Sie sich Symbol](xref:mvc/views/tag-helpers/intro#opt-out):

  ```cshtml
  <!form method="post">
      ...
  </!form>
  ```

* Entfernen Sie die `FormTagHelper` aus der Sicht. Die `FormTagHelper` können aus einer Sicht entfernt werden, indem Sie die Razor-Ansicht die folgende Anweisung hinzufügen:

  ```cshtml
  @removeTagHelper Microsoft.AspNetCore.Mvc.TagHelpers.FormTagHelper, Microsoft.AspNetCore.Mvc.TagHelpers
  ```

> [!NOTE]
> [Razor-Seiten](xref:razor-pages/index) vor XSRF/CSRF automatisch geschützt sind. Weitere Informationen finden Sie unter [XSRF/CSRF und Razor-Seiten](xref:razor-pages/index#xsrf).

Die am häufigsten verwendete Ansatz für die Verteidigung gegen CSRF-Angriffe ist die Verwendung der *für die Domänensynchronisierung Tokenmuster* (STP). STP wird verwendet, wenn der Benutzer eine Seite mit den Formulardaten anfordert:

1. Der Server sendet ein Token, die denen des aktuellen Benutzers Identität an den Client zugeordnet ist.
1. Der Client sendet das Token zurück an den Server für die Überprüfung.
1. Wenn der Server ein Token, die Identität des authentifizierten Benutzers entsprechen nicht erhält, wird die Anforderung zurückgewiesen.

Das Token ist eindeutig und nicht vorhersagbar. Das Token kann auch verwendet werden, um sicherzustellen, dass ordnungsgemäße Sequenzierung einer Reihe von Anforderungen (z. B. Sicherstellen der anforderungssequenz des: Seite 1 &ndash; Seite 2 &ndash; Seite 3). Alle Formulare in ASP.NET Core MVC und Razor Pages-Vorlagen generiert fälschungssicherheitstoken. Die folgenden zwei Beispiele anzeigen generieren fälschungssicherheitstoken:

```cshtml
<form asp-controller="Manage" asp-action="ChangePassword" method="post">
    ...
</form>

@using (Html.BeginForm("ChangePassword", "Manage"))
{
    ...
}
```

Explizit hinzufügen ein Antifälschungstoken an einen `<form>` -Element ohne das HTML-Hilfsobjekt mit Taghilfsprogrammen [ @Html.AntiForgeryToken ](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.htmlhelper.antiforgerytoken):

```cshtml
<form action="/" method="post">
    @Html.AntiForgeryToken()
</form>
```

In allen genannten Fällen fügt ASP.NET Core ein ausgeblendetes Formularfeld, die etwa wie folgt hinzu:

```cshtml
<input name="__RequestVerificationToken" type="hidden" value="CfDJ8NrAkS ... s2-m9Yw">
```

ASP.NET Core enthält drei [Filter](xref:mvc/controllers/filters) für die Verwendung von fälschungssicherheitstoken:

* [ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute)
* [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute)
* [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute)

## <a name="antiforgery-options"></a>Antiforgery-Optionen

Anpassen von [antiforgery Optionen](/dotnet/api/Microsoft.AspNetCore.Antiforgery.AntiforgeryOptions) in `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    // Set Cookie properties using CookieBuilder properties†.
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.SuppressXFrameOptionsHeader = false;
});
```

&dagger;Legen Sie die antifälschungsunterstützung `Cookie` Eigenschaften, die mithilfe der Eigenschaften der [CookieBuilder](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder) Klasse.

| Option | Beschreibung |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Bestimmt die Einstellungen verwendet, um die antiforgery Cookies zu erstellen. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Der Name des ausgeblendeten Formularfelds mit denen vom System antiforgery fälschungssicherheitstoken in Ansichten gerendert werden soll. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Der Name des Headers der antiforgery-System verwendet werden soll. Wenn `null`, das System berücksichtigt nur die Formulardaten. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Gibt an, ob die Erstellung von Unterdrücken der `X-Frame-Options` Header. Standardmäßig wird der Header mit einem Wert von "SAMEORIGIN" generiert. Wird standardmäßig auf `false` festgelegt. |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
services.AddAntiforgery(options => 
{
    options.CookieDomain = "contoso.com";
    options.CookieName = "X-CSRF-TOKEN-COOKIENAME";
    options.CookiePath = "Path";
    options.FormFieldName = "AntiforgeryFieldname";
    options.HeaderName = "X-CSRF-TOKEN-HEADERNAME";
    options.RequireSsl = false;
    options.SuppressXFrameOptionsHeader = false;
});
```

| Option | Beschreibung |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookie) | Bestimmt die Einstellungen verwendet, um die antiforgery Cookies zu erstellen. |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiedomain) | Die Domäne des Cookies. Wird standardmäßig auf `null` festgelegt. Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative ist die Cookie.Domain. |
| [CookieName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiename) | Der Name des Cookies. Wenn nicht festgelegt ist, generiert das System einen eindeutigen Namen ab, mit der [DefaultCookiePrefix](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.defaultcookieprefix) (". AspNetCore.Antiforgery."). Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative ist die Cookie.Name. |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.cookiepath) | Der Pfad für das Cookie festgelegt. Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative ist die Cookie.Path. |
| [FormFieldName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.formfieldname) | Der Name des ausgeblendeten Formularfelds mit denen vom System antiforgery fälschungssicherheitstoken in Ansichten gerendert werden soll. |
| [HeaderName](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.headername) | Der Name des Headers der antiforgery-System verwendet werden soll. Wenn `null`, das System berücksichtigt nur die Formulardaten. |
| [RequireSsl](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.requiressl) | Gibt an, ob das System antiforgery HTTPS erforderlich ist. Wenn `true`, nicht-HTTPS-Anforderungen schlagen fehl. Wird standardmäßig auf `false` festgelegt. Diese Eigenschaft ist veraltet und wird in einer zukünftigen Version entfernt. Die empfohlene Alternative ist Cookie.SecurePolicy festlegen. |
| [SuppressXFrameOptionsHeader](/dotnet/api/microsoft.aspnetcore.antiforgery.antiforgeryoptions.suppressxframeoptionsheader) | Gibt an, ob die Erstellung von Unterdrücken der `X-Frame-Options` Header. Standardmäßig wird der Header mit einem Wert von "SAMEORIGIN" generiert. Wird standardmäßig auf `false` festgelegt. |

::: moniker-end

Weitere Informationen finden Sie unter [CookieAuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.CookieAuthenticationOptions).

## <a name="configure-antiforgery-features-with-iantiforgery"></a>Konfigurieren antiforgery-Features mit IAntiforgery

[IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) stellt die API zum Konfigurieren der antiforgery Features bereit. `IAntiforgery` können angefordert werden, der `Configure` Methode der `Startup` Klasse. Im folgenden Beispiel wird die Middleware auf der Startseite der app eine antiforgery Token generieren und senden sie in der Antwort als Cookie (mithilfe von die standardmäßige Angular Benennungskonvention weiter unten in diesem Thema beschrieben):

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}
```

### <a name="require-antiforgery-validation"></a>Antiforgery-Überprüfung

[ValidateAntiForgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.validateantiforgerytokenattribute) ist ein Aktionsfilter, die mit einer einzelnen Aktion, einen Controller angewendet werden kann oder Global. Anforderungen an Aktionen, die dieser Filter angewendet haben werden blockiert, es sei denn, der die Anforderung ein gültiges antiforgery Token enthält.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<IActionResult> RemoveLogin(RemoveLoginViewModel account)
{
    ManageMessageId? message = ManageMessageId.Error;
    var user = await GetCurrentUserAsync();

    if (user != null)
    {
        var result = 
            await _userManager.RemoveLoginAsync(
                user, account.LoginProvider, account.ProviderKey);

        if (result.Succeeded)
        {
            await _signInManager.SignInAsync(user, isPersistent: false);
            message = ManageMessageId.RemoveLoginSuccess;
        }
    }

    return RedirectToAction(nameof(ManageLogins), new { Message = message });
}
```

Die `ValidateAntiForgeryToken` Attribut erfordert ein Token für Anforderungen an die Aktionsmethoden, die es ausstattet, einschließlich HTTP GET-Anforderungen. Wenn die `ValidateAntiForgeryToken` Attribut angewendet wird, auf der app Controller, sie kann überschrieben werden, mit der `IgnoreAntiforgeryToken` Attribut.

> [!NOTE]
> ASP.NET Core unterstützt keine GET-Anforderungen automatisch antiforgery Token hinzugefügt werden.

### <a name="automatically-validate-antiforgery-tokens-for-unsafe-http-methods-only"></a>Überprüfen Sie fälschungssicherheitstoken für unsichere HTTP-Methoden nur automatisch

ASP.NET Core-apps erstellen keine fälschungssicherheitstoken für sichere HTTP-Methoden (GET, HEAD, Optionen und ABLAUFVERFOLGUNG). Anstelle von Allgemein Anwenden der `ValidateAntiForgeryToken` -Attribut, und überschreiben Sie dann mit `IgnoreAntiforgeryToken` Attribute, die [AutoValidateAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.autovalidateantiforgerytokenattribute) Attribut kann verwendet werden. Dieses Attribut funktioniert genauso wie die `ValidateAntiForgeryToken` Attribut, außer dass es keine Token für Anforderungen mit den folgenden HTTP-Methoden erfordert:

* GET
* HEAD
* OPTIONEN
* TRACE

Wir empfehlen die Verwendung von `AutoValidateAntiforgeryToken` Allgemein für nicht-API-Szenarien. Dadurch wird sichergestellt, dass standardmäßig die POST-Aktionen geschützt sind. Die Alternative ist standardmäßig fälschungssicherheitstoken ignoriert werden sollen, es sei denn, `ValidateAntiForgeryToken` auf einzelne Aktionsmethoden angewendet wird. Verlassen die app anfällig für CSRF-Angriffe ungeschützten versehen, eher er in diesem Szenario für eine POST-Aktion-Methode, die verbleiben. Alle Beiträge, sollte das Antifälschungstoken senden.

APIs verfügen nicht über einen automatischen Mechanismus zum Senden des nicht-Cookie-Teils des Tokens. Die Implementierung hängt wahrscheinlich die Client-Code-Implementierung. Nachfolgend einige Beispiele:

Auf Klassenebene-Beispiel:

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
```

Globale Beispiel:

```csharp
services.AddMvc(options => 
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));
```

### <a name="override-global-or-controller-antiforgery-attributes"></a>Globale außer Kraft setzen oder antiforgery Attributen für controller

Die [IgnoreAntiforgeryToken](/dotnet/api/microsoft.aspnetcore.mvc.ignoreantiforgerytokenattribute) Filter wird verwendet, um das Antifälschungstoken für eine bestimmte Aktion (oder Controller) erforderlich. Wenn angewendet wird, überschreibt dieser Filter `ValidateAntiForgeryToken` und `AutoValidateAntiforgeryToken` angegebenen Filtern auf einer höheren Ebene (Global oder auf einem Domänencontroller).

```csharp
[Authorize]
[AutoValidateAntiforgeryToken]
public class ManageController : Controller
{
    [HttpPost]
    [IgnoreAntiforgeryToken]
    public async Task<IActionResult> DoSomethingSafe(SomeViewModel model)
    {
        // no antiforgery token required
    }
}
```

## <a name="refresh-tokens-after-authentication"></a>Aktualisierungstoken nach der Authentifizierung

Token sollte aktualisiert werden, nachdem der Benutzer authentifiziert ist, durch Weiterleiten des Benutzers auf eine Sicht oder Razor Pages-Seite.

## <a name="javascript-ajax-and-spas"></a>JavaScript, AJAX und SPAs

In herkömmlichen HTML-basierte apps werden fälschungssicherheitstoken mit ausgeblendeten Formularfeldern an den Server übergeben. In modernen apps mit JavaScript-basierten und SPAs werden viele Anforderungen programmgesteuert vorgenommen. Diese AJAX-Anforderungen möglicherweise andere Techniken (z.B. Anforderungsheader oder Cookies) verwenden, um das Token senden zu können.

Wenn Cookies zum Speichern von Authentifizierungstoken und zum Authentifizieren der API-Anforderungen auf dem Server verwendet werden, ist CSRF ein potenzielles Problem. Wenn lokaler Speicher verwendet wird, um das Token zu speichern, kann CSRF-Sicherheitsrisiko verringert werden, da die Werte aus dem lokalen Speicher automatisch mit jeder Anforderung an den Server gesendet werden nicht. Daher verwenden lokalen Speicher zum Speichern der Antifälschungstoken auf dem Client und Senden des Tokens, wie ein Anforderungsheader ein empfohlenes Verfahren ist.

### <a name="javascript"></a>JavaScript

Verwenden von JavaScript mit Sichten, kann das Token erstellt werden mithilfe eines Diensts aus, in der Ansicht. Einfügen der [Microsoft.AspNetCore.Antiforgery.IAntiforgery](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery) Dienst in der Ansicht, und rufen [GetAndStoreTokens](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgery.getandstoretokens):

[!code-csharp[](anti-request-forgery/sample/MvcSample/Views/Home/Ajax.cshtml?highlight=4-10,12-13,35-36)]

Dadurch entfällt das arbeiten direkt mit Cookies vom Server festlegen, oder sie vom Client gelesen werden.

Im vorherige Beispiel verwendet JavaScript zum Wert des ausgeblendeten Felds für den AJAX-POST-Header zu lesen.

JavaScript kann auch Zugriffstoken in Cookies und Inhalt für das Cookie Header mit dem Wert von des Tokens zu erstellen.

```csharp
context.Response.Cookies.Append("CSRF-TOKEN", tokens.RequestToken, 
    new Microsoft.AspNetCore.Http.CookieOptions { HttpOnly = false });
```

Vorausgesetzt, das Skript fordert zum Senden von Token in einer Kopfzeile namens `X-CSRF-TOKEN`, konfigurieren Sie den antiforgery-Dienst auf die Suche nach der `X-CSRF-TOKEN` Header:

```csharp
services.AddAntiforgery(options => options.HeaderName = "X-CSRF-TOKEN");
```

Im folgenden Beispiel wird JavaScript, um eine AJAX-Anforderung mit den entsprechenden Header zu machen:

```javascript
function getCookie(cname) {
    var name = cname + "=";
    var decodedCookie = decodeURIComponent(document.cookie);
    var ca = decodedCookie.split(';');
    for(var i = 0; i <ca.length; i++) {
        var c = ca[i];
        while (c.charAt(0) == ' ') {
            c = c.substring(1);
        }
        if (c.indexOf(name) == 0) {
            return c.substring(name.length, c.length);
        }
    }
    return "";
}

var csrfToken = getCookie("CSRF-TOKEN");

var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
    if (xhttp.readyState == XMLHttpRequest.DONE) {
        if (xhttp.status == 200) {
            alert(xhttp.responseText);
        } else {
            alert('There was an error processing the AJAX request.');
        }
    }
};
xhttp.open('POST', '/api/password/changepassword', true);
xhttp.setRequestHeader("Content-type", "application/json");
xhttp.setRequestHeader("X-CSRF-TOKEN", csrfToken);
xhttp.send(JSON.stringify({ "newPassword": "ReallySecurePassword999$$$" }));
```

### <a name="angularjs"></a>AngularJS

AngularJS verwendet eine Konvention gilt CSRF-Adresse. Wenn der Server ein Cookie mit dem Namen sendet `XSRF-TOKEN`, die AngularJS `$http` Service eines Headers den Cookiewert hinzufügt, wenn er eine Anforderung an den Server sendet. Dieser Prozess erfolgt automatisch. Der Header muss nicht explizit im Client festgelegt werden. Der Headername ist `X-XSRF-TOKEN`. Der Server sollte dieser Header zu erkennen und seinen Inhalt zu überprüfen.

Für ASP.NET Core-API diese Konvention in Ihrem Start der Anwendung funktioniert:

* Konfigurieren Ihrer app für ein Token in einem Cookie namens bereitstellen `XSRF-TOKEN`.
* Konfigurieren den antiforgery-Dienst einen Header mit Namen gesucht `X-XSRF-TOKEN`.

```csharp
public void Configure(IApplicationBuilder app, IAntiforgery antiforgery)
{
    app.Use(next => context =>
    {
        string path = context.Request.Path.Value;

        if (
            string.Equals(path, "/", StringComparison.OrdinalIgnoreCase) ||
            string.Equals(path, "/index.html", StringComparison.OrdinalIgnoreCase))
        {
            // The request token can be sent as a JavaScript-readable cookie, 
            // and Angular uses it by default.
            var tokens = antiforgery.GetAndStoreTokens(context);
            context.Response.Cookies.Append("XSRF-TOKEN", tokens.RequestToken, 
                new CookieOptions() { HttpOnly = false });
        }

        return next(context);
    });
}

public void ConfigureServices(IServiceCollection services)
{
    // Angular's default header name for sending the XSRF token.
    services.AddAntiforgery(options => options.HeaderName = "X-XSRF-TOKEN");
}
```

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/anti-request-forgery/sample/AngularSample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="extend-antiforgery"></a>Erweitern Sie antifälschungsunterstützung

Die [IAntiForgeryAdditionalDataProvider](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider) Typ ermöglicht Entwicklern, die das Verhalten des Anti-CSRF-Systems durch zusätzliche Round-Tripping-Daten in jedem Token zu erweitern. Die [GetAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.getadditionaldata) Methode jedes Mal aufgerufen, wird ein Token generiert und der zurückgegebene Wert in das generierte Token eingebettet ist. Eine Implementierung kann einen Zeitstempel, eine Nonce oder einen anderen Wert zurück, und rufen dann [ValidateAdditionalData](/dotnet/api/microsoft.aspnetcore.antiforgery.iantiforgeryadditionaldataprovider.validateadditionaldata) auf diese Daten zu überprüfen, wenn das Token überprüft wird. Den Benutzernamen des Clients ist bereits in das generierte Token, eingebettet, daher keine Notwendigkeit besteht, diese Informationen umfassen. Wenn ein Token, ergänzende Daten, aber keine enthält `IAntiForgeryAdditionalDataProvider` wird konfiguriert, die ergänzenden Daten wird nicht überprüft.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [CSRF](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) auf [Öffnen der Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP).
* <xref:host-and-deploy/web-farm>
