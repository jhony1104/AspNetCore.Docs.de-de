---
title: ASP.net Core 2,1 MVC SameSite-Cookie-Beispiel
author: rick-anderson
description: ASP.net Core 2,1 MVC SameSite-Cookie-Beispiel
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: security/samesite/mvc21
ms.openlocfilehash: 14f3d3d27d5740519e1ba57529d5694b4cdeb9ec
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654991"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="34a43-103">ASP.net Core 2,1 MVC SameSite-Cookie-Beispiel</span><span class="sxs-lookup"><span data-stu-id="34a43-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="34a43-104">ASP.net Core 2,1 verfügt über eine integrierte Unterstützung für das [SameSite](https://www.owasp.org/index.php/SameSite) -Attribut, wurde aber in den ursprünglichen Standard geschrieben.</span><span class="sxs-lookup"><span data-stu-id="34a43-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="34a43-105">Das [gepatchte Verhalten](https://github.com/dotnet/aspnetcore/issues/8212) änderte die Bedeutung von `SameSite.None`, um das SameSite-Attribut mit dem Wert `None`auszugeben, anstatt den Wert überhaupt auszugeben.</span><span class="sxs-lookup"><span data-stu-id="34a43-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="34a43-106">Wenn Sie den Wert nicht ausgeben möchten, können Sie die `SameSite`-Eigenschaft für ein Cookie auf-1 festlegen.</span><span class="sxs-lookup"><span data-stu-id="34a43-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="sampleCode"></a><span data-ttu-id="34a43-107">Schreiben des SameSite-Attributs</span><span class="sxs-lookup"><span data-stu-id="34a43-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="34a43-108">Im folgenden finden Sie ein Beispiel für das Schreiben eines SameSite-Attributs für ein Cookie:</span><span class="sxs-lookup"><span data-stu-id="34a43-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="34a43-109">Festlegen von Cookieauthentifizierung und Sitzungs Zustands Cookies</span><span class="sxs-lookup"><span data-stu-id="34a43-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="34a43-110">Cookie-Authentifizierung, Sitzungs Status und [verschiedene andere Komponenten](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) legen ihre SameSite-Optionen über Cookie-Optionen fest, z. b.</span><span class="sxs-lookup"><span data-stu-id="34a43-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="34a43-111">Im vorangehenden Code legen sowohl die Cookie-als auch der Sitzungs Status Ihr SameSite-Attribut auf `None`fest, wobei das-Attribut mit einem `None` Wert ausgegeben wird, und legen das Secure-Attribut auf "true" fest.</span><span class="sxs-lookup"><span data-stu-id="34a43-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="34a43-112">Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="34a43-112">Run the sample</span></span>

<span data-ttu-id="34a43-113">Wenn Sie das [Beispiel Projekt](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)ausführen, laden Sie den Browser Debugger auf der Anfangs Seite, und verwenden Sie ihn, um die Cookie-Sammlung für die Website anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="34a43-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="34a43-114">Wenn Sie dies in Edge und Chrome tun möchten `F12` klicken Sie dann auf die Registerkarte `Application`, und klicken Sie im Abschnitt `Storage` unter der Option `Cookies` auf die Website-URL.</span><span class="sxs-lookup"><span data-stu-id="34a43-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Liste der Browser Debugger-Cookies](BrowserDebugger.png)

<span data-ttu-id="34a43-116">In der obigen Abbildung sehen Sie, dass das Cookie, das durch das Beispiel erstellt wird, wenn Sie auf die Schaltfläche "SameSite Cookie erstellen" klicken, einen SameSite-Attribut Wert `Lax`hat, der mit dem im [Beispielcode](#sampleCode)festgelegten Wert übereinstimmt.</span><span class="sxs-lookup"><span data-stu-id="34a43-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="interception"></a><span data-ttu-id="34a43-117">Abfangen von Cookies</span><span class="sxs-lookup"><span data-stu-id="34a43-117">Intercepting cookies</span></span>

<span data-ttu-id="34a43-118">Zum Abfangen von Cookies müssen Sie die `CookiePolicy` Middleware verwenden, um den Wert None gemäß seiner Unterstützung im Browser-Agent des Benutzers anzupassen.</span><span class="sxs-lookup"><span data-stu-id="34a43-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="34a43-119">Dies muss **vor** allen Komponenten, die Cookies schreiben und innerhalb `ConfigureServices()`konfiguriert werden, in der HTTP-Anforderungs Pipeline platziert werden.</span><span class="sxs-lookup"><span data-stu-id="34a43-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="34a43-120">Verwenden Sie `app.UseCookiePolicy()` in der `Configure(IApplicationBuilder, IHostingEnvironment)`-Methode in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs), um Sie in die Pipeline einzufügen.</span><span class="sxs-lookup"><span data-stu-id="34a43-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="34a43-121">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="34a43-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="34a43-122">Konfigurieren Sie dann im `ConfigureServices(IServiceCollection services)` die cookierichtlinie so zu konfigurieren, dass eine Hilfsklasse aufgerufen wird, wenn Cookies angehängt oder gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="34a43-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="34a43-123">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="34a43-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="34a43-124">Die `CheckSameSite(HttpContext, CookieOptions)`der Hilfsfunktion:</span><span class="sxs-lookup"><span data-stu-id="34a43-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="34a43-125">Wird aufgerufen, wenn Cookies an die Anforderung angefügt oder aus der Anforderung gelöscht werden.</span><span class="sxs-lookup"><span data-stu-id="34a43-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="34a43-126">Überprüft, ob die `SameSite`-Eigenschaft auf `None`festgelegt ist.</span><span class="sxs-lookup"><span data-stu-id="34a43-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="34a43-127">Wenn `SameSite` auf `None` festgelegt ist und der aktuelle Benutzer-Agent bekanntermaßen nicht den Attribut Wert "None" unterstützt.</span><span class="sxs-lookup"><span data-stu-id="34a43-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="34a43-128">Die Überprüfung erfolgt mithilfe der [samesitesupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) -Klasse:</span><span class="sxs-lookup"><span data-stu-id="34a43-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="34a43-129">Legt `SameSite` fest, um den Wert nicht auszugeben, indem die-Eigenschaft auf festgelegt wird `(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="34a43-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="34a43-130">Ziel .NET Framework</span><span class="sxs-lookup"><span data-stu-id="34a43-130">Targeting .NET Framework</span></span>

<span data-ttu-id="34a43-131">ASP.net Core und System. Web (ASP.net Classic) verfügen über unabhängige Implementierungen von "SameSite".</span><span class="sxs-lookup"><span data-stu-id="34a43-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="34a43-132">Die SameSite-KB-Patches für .NET Framework sind nicht erforderlich, wenn Sie ASP.net Core oder die System. Web SameSite-Mindestanforderungen (.NET 4.7.2) auf ASP.net Core anwenden.</span><span class="sxs-lookup"><span data-stu-id="34a43-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="34a43-133">ASP.net Core auf .net erfordert das Aktualisieren von nuget-Paketabhängigkeiten, um die entsprechenden Korrekturen zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="34a43-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="34a43-134">Um die ASP.net Core Änderungen für .NET Framework zu erhalten, stellen Sie sicher, dass Sie über einen direkten Verweis auf die gepatchten Pakete und Versionen (2.1.14 oder höher 2,1 Versionen) verfügen.</span><span class="sxs-lookup"><span data-stu-id="34a43-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="34a43-135">Weitere Informationen</span><span class="sxs-lookup"><span data-stu-id="34a43-135">More Information</span></span>
 
<span data-ttu-id="34a43-136">[Chrome-Updates](https://www.chromium.org/updates/same-site)
[ASP.net Core SameSite-Dokumentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.net Core 2,1 SameSite-Änderungs Ankündigung](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="34a43-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>