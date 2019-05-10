---
title: Neuerungen in ASP.NET Core 2.1
author: isaac2004
description: Informationen zu den neuen Features in ASP.NET Core 2.1.
ms.author: riande
ms.custom: mvc
ms.date: 04/30/2019
uid: aspnetcore-2.1
ms.openlocfilehash: 359f961db768b9048427c8ab296ee3e035879408
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65086394"
---
# <a name="whats-new-in-aspnet-core-21"></a><span data-ttu-id="a618f-103">Neuerungen in ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="a618f-103">What's new in ASP.NET Core 2.1</span></span>

<span data-ttu-id="a618f-104">In diesem Artikel werden die wichtigsten Änderungen in ASP.NET Core 2.1 hervorgehoben und Links zu relevanter Dokumentation bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="a618f-104">This article highlights the most significant changes in ASP.NET Core 2.1, with links to relevant documentation.</span></span>

## <a name="signalr"></a><span data-ttu-id="a618f-105">SignalR</span><span class="sxs-lookup"><span data-stu-id="a618f-105">SignalR</span></span>

<span data-ttu-id="a618f-106">SignalR wurde für ASP.NET Core 2.1 umgeschrieben.</span><span class="sxs-lookup"><span data-stu-id="a618f-106">SignalR has been rewritten for ASP.NET Core 2.1.</span></span> <span data-ttu-id="a618f-107">ASP.NET Core SignalR umfasst eine Reihe von Verbesserungen:</span><span class="sxs-lookup"><span data-stu-id="a618f-107">ASP.NET Core SignalR includes a number of improvements:</span></span>

* <span data-ttu-id="a618f-108">Ein vereinfachtes Modell für horizontale Skalierung.</span><span class="sxs-lookup"><span data-stu-id="a618f-108">A simplified scale-out model.</span></span>
* <span data-ttu-id="a618f-109">Einen neuen JavaScript-Client ohne jQuery-Abhängigkeit.</span><span class="sxs-lookup"><span data-stu-id="a618f-109">A new JavaScript client with no jQuery dependency.</span></span>
* <span data-ttu-id="a618f-110">Ein neues kompaktes, binäres Protokoll, das auf MessagePack basiert.</span><span class="sxs-lookup"><span data-stu-id="a618f-110">A new compact binary protocol based on MessagePack.</span></span>
* <span data-ttu-id="a618f-111">Unterstützung für benutzerdefinierte Protokolle.</span><span class="sxs-lookup"><span data-stu-id="a618f-111">Support for custom protocols.</span></span>
* <span data-ttu-id="a618f-112">Ein neues Streaming-Antwortmodell.</span><span class="sxs-lookup"><span data-stu-id="a618f-112">A new streaming response model.</span></span>
* <span data-ttu-id="a618f-113">Unterstützung für Clients basierend auf Bare-WebSockets.</span><span class="sxs-lookup"><span data-stu-id="a618f-113">Support for clients based on bare WebSockets.</span></span>

<span data-ttu-id="a618f-114">Weitere Informationen finden Sie unter [ASP.NET Core SignalR](xref:signalr/introduction).</span><span class="sxs-lookup"><span data-stu-id="a618f-114">For more information, see [ASP.NET Core SignalR](xref:signalr/introduction).</span></span>

## <a name="razor-class-libraries"></a><span data-ttu-id="a618f-115">Razor-Klassenbibliotheken</span><span class="sxs-lookup"><span data-stu-id="a618f-115">Razor class libraries</span></span>

<span data-ttu-id="a618f-116">ASP.NET Core 2.1 erleichtert die Erstellung und Einbeziehung einer Razor-basierten Benutzeroberfläche in einer Bibliothek sowie die mehrere Projekte übergreifende Freigabe.</span><span class="sxs-lookup"><span data-stu-id="a618f-116">ASP.NET Core 2.1 makes it easier to build and include Razor-based UI in a library and share it across multiple projects.</span></span> <span data-ttu-id="a618f-117">Mit dem neuen Razor SDK können Razor-Dateien in einem Klassenbibliotheksprojekt erstellt werden, das in ein NuGet-Paket gepackt werden kann.</span><span class="sxs-lookup"><span data-stu-id="a618f-117">The new Razor SDK enables building Razor files into a class library project that can be packaged into a NuGet package.</span></span> <span data-ttu-id="a618f-118">Ansichten und Seiten in Bibliotheken werden automatisch ermittelt und können von der App überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="a618f-118">Views and pages in libraries are automatically discovered and can be overridden by the app.</span></span> <span data-ttu-id="a618f-119">Folgen der Integration der Razor-Kompilierung in den Build:</span><span class="sxs-lookup"><span data-stu-id="a618f-119">By integrating Razor compilation into the build:</span></span>

* <span data-ttu-id="a618f-120">Die App wird deutlich schneller gestartet.</span><span class="sxs-lookup"><span data-stu-id="a618f-120">The app startup time is significantly faster.</span></span>
* <span data-ttu-id="a618f-121">Schnelle Updates von Razor-Ansichten und -Seiten zur Laufzeit sind weiterhin als Bestandteil eines iterativen Entwicklungsworkflows verfügbar.</span><span class="sxs-lookup"><span data-stu-id="a618f-121">Fast updates to Razor views and pages at runtime are still available as part of an iterative development workflow.</span></span>

<span data-ttu-id="a618f-122">Weitere Informationen finden Sie unter [Erstellen einer wiederverwendbaren Benutzeroberfläche mithilfe des Razor-Klassenbibliotheksprojekts](xref:razor-pages/ui-class).</span><span class="sxs-lookup"><span data-stu-id="a618f-122">For more information, see [Create reusable UI using the Razor Class Library project](xref:razor-pages/ui-class).</span></span>

## <a name="identity-ui-library--scaffolding"></a><span data-ttu-id="a618f-123">Bibliothek „Identität“ der Benutzeroberfläche und Gerüst</span><span class="sxs-lookup"><span data-stu-id="a618f-123">Identity UI library & scaffolding</span></span>

<span data-ttu-id="a618f-124">ASP.NET Core 2.1 stellt [ASP.NET Core-Identität](xref:security/authentication/identity) als [Razor-Klassenbibliothek](xref:razor-pages/ui-class) bereit.</span><span class="sxs-lookup"><span data-stu-id="a618f-124">ASP.NET Core 2.1 provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a618f-125">Apps, die über die Bibliothek „Identität“ verfügen, können das neue Gerüst „Identität“ anwenden, um die in der Razor-Klassenbibliothek „Identität“ enthaltenen Quellcode selektiv hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a618f-125">Apps that include Identity can apply the new Identity scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="a618f-126">Sie sollten Quellcode generieren, um den Code und das Verhalten ändern zu können.</span><span class="sxs-lookup"><span data-stu-id="a618f-126">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="a618f-127">Sie können das Gerüst beispielsweise anweisen, den bei der Registrierung verwendeten Code zu generieren.</span><span class="sxs-lookup"><span data-stu-id="a618f-127">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="a618f-128">Generierter Code hat Vorrang vor dem gleichen Code in der Razor-Klassenbibliothek „Identität“.</span><span class="sxs-lookup"><span data-stu-id="a618f-128">Generated code takes precedence over the same code in the Identity RCL.</span></span>

<span data-ttu-id="a618f-129">Apps **ohne** Authentifizierung können das Gerüst „Identität“ anwenden, um das Paket der Razor-Klassenbibliothek „Identität“ hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="a618f-129">Apps that do **not** include authentication can apply the Identity scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="a618f-130">Sie können Code der Klassenbibliothek „Identität“ auswählen, der generiert werden soll.</span><span class="sxs-lookup"><span data-stu-id="a618f-130">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="a618f-131">Weitere Informationen finden Sie unter [Gerüst „Identität“in ASP.NET Core Projekten](xref:security/authentication/scaffold-identity).</span><span class="sxs-lookup"><span data-stu-id="a618f-131">For more information, see [Scaffold Identity in ASP.NET Core projects](xref:security/authentication/scaffold-identity).</span></span>

## <a name="https"></a><span data-ttu-id="a618f-132">HTTPS</span><span class="sxs-lookup"><span data-stu-id="a618f-132">HTTPS</span></span>

<span data-ttu-id="a618f-133">Durch die verstärkte Ausrichtung auf Sicherheit und Datenschutz ist die Aktivierung von HTTPS für Web-Apps von großer Bedeutung.</span><span class="sxs-lookup"><span data-stu-id="a618f-133">With the increased focus on security and privacy, enabling HTTPS for web apps is important.</span></span> <span data-ttu-id="a618f-134">Die Erzwingung von HTTPS ist im Web zunehmend strenger.</span><span class="sxs-lookup"><span data-stu-id="a618f-134">HTTPS enforcement is becoming increasingly strict on the web.</span></span> <span data-ttu-id="a618f-135">Websites, auf denen HTTPS nicht verwendet wird, gelten als unsicher.</span><span class="sxs-lookup"><span data-stu-id="a618f-135">Sites that don’t use HTTPS are considered insecure.</span></span> <span data-ttu-id="a618f-136">Browser (Chromium, Mozilla) beginnen zu erzwingen, dass Web-Features über einen sicheren Kontext verwendet werden müssen.</span><span class="sxs-lookup"><span data-stu-id="a618f-136">Browsers (Chromium, Mozilla) are starting to enforce that web features must be used from a secure context.</span></span> <span data-ttu-id="a618f-137">Für die [DSGVO](xref:security/gdpr) ist die Verwendung von HTTPS aus Datenschutzgründen erforderlich.</span><span class="sxs-lookup"><span data-stu-id="a618f-137">[GDPR](xref:security/gdpr) requires the use of HTTPS to protect user privacy.</span></span> <span data-ttu-id="a618f-138">Während die Verwendung von HTTPS in der Produktion wichtig ist, können durch die Verwendung von HTTPS in der Entwicklung Probleme bei der Bereitstellung verhindert werden (z.B. unsichere Links).</span><span class="sxs-lookup"><span data-stu-id="a618f-138">While using HTTPS in production is critical, using HTTPS in development can help prevent issues in deployment (for example, insecure links).</span></span> <span data-ttu-id="a618f-139">ASP.NET Core 2.1 umfasst eine Reihe von Verbesserungen, die die Verwendung von HTTPS in der Entwicklung und die Konfiguration von HTTPS in der Produktion erleichtern.</span><span class="sxs-lookup"><span data-stu-id="a618f-139">ASP.NET Core 2.1 includes a number of improvements that make it easier to use HTTPS in development and to configure HTTPS in production.</span></span> <span data-ttu-id="a618f-140">Weitere Informationen finden Sie unter [Erzwingen von HTTPS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="a618f-140">For more information, see [Enforce HTTPS](xref:security/enforcing-ssl).</span></span>

### <a name="on-by-default"></a><span data-ttu-id="a618f-141">Standardmäßig aktiviert</span><span class="sxs-lookup"><span data-stu-id="a618f-141">On by default</span></span>

<span data-ttu-id="a618f-142">HTTPS ist jetzt standardmäßig aktiviert, um die Entwicklung sicherer Websites zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="a618f-142">To facilitate secure website development, HTTPS  is now enabled by default.</span></span> <span data-ttu-id="a618f-143">Seit Version 2.1 lauscht Kestrel unter `https://localhost:5001`, wenn ein lokales Entwicklungszertifikat vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="a618f-143">Starting in 2.1, Kestrel listens on `https://localhost:5001` when a local development certificate is present.</span></span> <span data-ttu-id="a618f-144">Ein Entwicklungszertifikat wird erstellt:</span><span class="sxs-lookup"><span data-stu-id="a618f-144">A development certificate is created:</span></span>

* <span data-ttu-id="a618f-145">Im Rahmen des Eindrucks des .NET Core SDK beim ersten Ausführen, wenn Sie das SDK zum ersten Mal verwenden.</span><span class="sxs-lookup"><span data-stu-id="a618f-145">As part of the .NET Core SDK first-run experience, when you use the SDK for the first time.</span></span>
* <span data-ttu-id="a618f-146">Manuell mithilfe des neuen `dev-certs`-Tools.</span><span class="sxs-lookup"><span data-stu-id="a618f-146">Manually using the new `dev-certs` tool.</span></span>

<span data-ttu-id="a618f-147">Führen Sie `dotnet dev-certs https --trust` aus, um den Zertifikat vertrauen zu können.</span><span class="sxs-lookup"><span data-stu-id="a618f-147">Run `dotnet dev-certs https --trust` to trust the certificate.</span></span>

### <a name="https-redirection-and-enforcement"></a><span data-ttu-id="a618f-148">HTTPS-Umleitung und -Erzwingung</span><span class="sxs-lookup"><span data-stu-id="a618f-148">HTTPS redirection and enforcement</span></span>

<span data-ttu-id="a618f-149">Web-Apps müssen in der Regel HTTP und HTTPS lauschen, leiten dann aber sämtlichen HTTP-Verkehr an HTTPS um.</span><span class="sxs-lookup"><span data-stu-id="a618f-149">Web apps typically need to listen on both HTTP and HTTPS, but then redirect all HTTP traffic to HTTPS.</span></span> <span data-ttu-id="a618f-150">In Version 2.1 wurde eine spezielle Middleware für die HTTPS-Umleitung eingeführt, die Umleitungen auf intelligente Weise basierend auf dem Vorhandensein einer Konfiguration oder gebundener Server-Ports durchführt.</span><span class="sxs-lookup"><span data-stu-id="a618f-150">In 2.1, specialized HTTPS redirection middleware that intelligently redirects based on the presence of configuration or bound server ports has been introduced.</span></span>

<span data-ttu-id="a618f-151">Darüber hinaus kann die Verwendung von HTTPS mit dem [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) erzwungen werden.</span><span class="sxs-lookup"><span data-stu-id="a618f-151">Use of HTTPS can be further enforced using [HTTP Strict Transport Security Protocol (HSTS)](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="a618f-152">Das HSTS gibt Browsern die Anweisung, immer über HTTPS auf die Website zuzugreifen.</span><span class="sxs-lookup"><span data-stu-id="a618f-152">HSTS instructs browsers to always access the site via HTTPS.</span></span> <span data-ttu-id="a618f-153">ASP.NET Core 2.1 fügt HSTS-Middleware hinzu, die Optionen für das max. Alter, Unterdomänen und die Preload-Liste des HSTS unterstützt.</span><span class="sxs-lookup"><span data-stu-id="a618f-153">ASP.NET Core 2.1 adds HSTS middleware that supports options for max age, subdomains, and the HSTS preload list.</span></span>

### <a name="configuration-for-production"></a><span data-ttu-id="a618f-154">Konfiguration für die Produktion</span><span class="sxs-lookup"><span data-stu-id="a618f-154">Configuration for production</span></span>

<span data-ttu-id="a618f-155">In der Produktion muss HTTPS explizit konfiguriert sein.</span><span class="sxs-lookup"><span data-stu-id="a618f-155">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="a618f-156">In Version 2.1 wurde ein Konfigurationsschema für die Konfiguration von HTTPS für Kestrel hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="a618f-156">In 2.1, default configuration schema for configuring HTTPS for Kestrel has been added.</span></span> <span data-ttu-id="a618f-157">Apps können für folgende Verwendung konfiguriert werden:</span><span class="sxs-lookup"><span data-stu-id="a618f-157">Apps can be configured to use:</span></span>

* <span data-ttu-id="a618f-158">Mehrere Endpunkte einschließlich der URLs.</span><span class="sxs-lookup"><span data-stu-id="a618f-158">Multiple endpoints including the URLs.</span></span> <span data-ttu-id="a618f-159">Weitere Informationen finden Sie unter [Kestrel web server implementation: Endpoint configuration (Kestrel: Endpunktkonfiguration)](xref:fundamentals/servers/kestrel#endpoint-configuration).</span><span class="sxs-lookup"><span data-stu-id="a618f-159">For more information, see [Kestrel web server implementation: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>
* <span data-ttu-id="a618f-160">Das für HTTPS zu verwendende Zertifikat aus einer Datei auf dem Datenträger oder aus einem Zertifikatspeicher.</span><span class="sxs-lookup"><span data-stu-id="a618f-160">The certificate to use for HTTPS either from a file on disk or from a certificate store.</span></span>

## <a name="gdpr"></a><span data-ttu-id="a618f-161">DSGVO</span><span class="sxs-lookup"><span data-stu-id="a618f-161">GDPR</span></span>

<span data-ttu-id="a618f-162">ASP.NET Core stellt APIs und Vorlagen für die Erfüllung einiger der Anforderungen der [Datenschutz-Grundverordnung (DSGV=)](https://www.eugdpr.org/) bereit.</span><span class="sxs-lookup"><span data-stu-id="a618f-162">ASP.NET Core provides APIs and templates to help meet some of the [EU General Data Protection Regulation (GDPR)](https://www.eugdpr.org/) requirements.</span></span> <span data-ttu-id="a618f-163">Weitere Informationen finden Sie unter [DSGVO-Unterstützung in ASP.NET Core](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="a618f-163">For more information, see [GDPR support in ASP.NET Core](xref:security/gdpr).</span></span> <span data-ttu-id="a618f-164">Eine [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) veranschaulicht, wie die meisten DSGVO-Erweiterungspunkte verwendet und getestet werden können. Ferner enthält sie zu den Vorlagen von ASP.NET Core 2.1 hinzugefügte APIs.</span><span class="sxs-lookup"><span data-stu-id="a618f-164">A [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) shows how to use and lets you test most of the GDPR extension points and APIs added to the ASP.NET Core 2.1 templates.</span></span>

## <a name="integration-tests"></a><span data-ttu-id="a618f-165">Integrationstests</span><span class="sxs-lookup"><span data-stu-id="a618f-165">Integration tests</span></span>

<span data-ttu-id="a618f-166">Es wurde ein neues Paket eingeführt, durch das die Testerstellung und -ausführung optimiert wird.</span><span class="sxs-lookup"><span data-stu-id="a618f-166">A new package is introduced that streamlines test creation and execution.</span></span> <span data-ttu-id="a618f-167">Das Paket [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) behandelt folgende Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="a618f-167">The [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/) package handles the following tasks:</span></span>

* <span data-ttu-id="a618f-168">Es kopiert die Abhängigkeitsdatei (*\*.deps*) aus der getesteten App in den Ordner *bin* des Testprojekts.</span><span class="sxs-lookup"><span data-stu-id="a618f-168">Copies the dependency file (*\*.deps*) from the tested app into the test project's *bin* folder.</span></span>
* <span data-ttu-id="a618f-169">Es legt das Inhaltsstammelement auf das Projektstammelement der App fest, damit statische Dateien und Seiten/Ansichten bei der Ausführung des Tests gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="a618f-169">Sets the content root to the tested app's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="a618f-170">Es stellt die Klasse [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) zur Optimierung des Bootstrappings der getesteten App mit [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) bereit.</span><span class="sxs-lookup"><span data-stu-id="a618f-170">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the tested app with [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span>

<span data-ttu-id="a618f-171">Im folgenden Test wird mithilfe von [xUnit](https://xunit.github.io/) überprüft, ob die Indexseite mit einem erfolgreichen Statuscode und mit dem richtigen Content-Type-Header geladen wird:</span><span class="sxs-lookup"><span data-stu-id="a618f-171">The following test uses [xUnit](https://xunit.github.io/) to check that the Index page loads with a success status code and with the correct Content-Type header:</span></span>

```csharp
public class BasicTests
    : IClassFixture<WebApplicationFactory<RazorPagesProject.Startup>>
{
    private readonly HttpClient _client;

    public BasicTests(WebApplicationFactory<RazorPagesProject.Startup> factory)
    {
        _client = factory.CreateClient();
    }

    [Fact]
    public async Task GetHomePage()
    {
        // Act
        var response = await _client.GetAsync("/");

        // Assert
        response.EnsureSuccessStatusCode(); // Status Code 200-299
        Assert.Equal("text/html; charset=utf-8",
            response.Content.Headers.ContentType.ToString());
    }
}
```

<span data-ttu-id="a618f-172">Weitere Informationen finden Sie im Thema [Integrationstests](xref:test/integration-tests).</span><span class="sxs-lookup"><span data-stu-id="a618f-172">For more information, see the [Integration tests](xref:test/integration-tests) topic.</span></span>

## <a name="apicontroller-actionresultt"></a><span data-ttu-id="a618f-173">[ApiController], ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="a618f-173">[ApiController], ActionResult\<T></span></span>

<span data-ttu-id="a618f-174">In ASP.NET Core 2.1 werden neue Programmierungskonventionen hinzugefügt, die eine Erstellung bereinigter und beschreibender Web-APIs vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="a618f-174">ASP.NET Core 2.1 adds new programming conventions that make it easier to build clean and descriptive web APIs.</span></span> <span data-ttu-id="a618f-175">`ActionResult<T>` ist ein neuer Typ, der hinzugefügt wurde, damit eine App einen Antworttyp oder ein anderes Aktionsergebnis zurückgeben kann (vergleichbar mit IActionResult), während der Antworttyp weiterhin angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="a618f-175">`ActionResult<T>` is a new type added to allow an app to return either a response type or any other action result (similar to IActionResult), while still indicating the response type.</span></span> <span data-ttu-id="a618f-176">Das Attribut `[ApiController]` wurde auch hinzugefügt, um Web-API-spezifischen Konventionen und Verhaltensweisen zuzustimmen.</span><span class="sxs-lookup"><span data-stu-id="a618f-176">The `[ApiController]` attribute has also been added as the way to opt in to Web API-specific conventions and behaviors.</span></span>

<span data-ttu-id="a618f-177">Weitere Informationen finden Sie unter [Erstellen von Web-APIs mit ASP.NET Core](xref:web-api/index).</span><span class="sxs-lookup"><span data-stu-id="a618f-177">For more information, see [Build Web APIs with ASP.NET Core](xref:web-api/index).</span></span>

## <a name="ihttpclientfactory"></a><span data-ttu-id="a618f-178">IHttpClientFactory</span><span class="sxs-lookup"><span data-stu-id="a618f-178">IHttpClientFactory</span></span>

<span data-ttu-id="a618f-179">ASP.NET Core 2.1 umfasst einen neuen `IHttpClientFactory`-Dienst, durch den die Konfiguration und der Verbrauch von `HttpClient`-Instanzen in Apps vereinfacht wird.</span><span class="sxs-lookup"><span data-stu-id="a618f-179">ASP.NET Core 2.1 includes a new `IHttpClientFactory` service that makes it easier to configure and consume instances of `HttpClient` in apps.</span></span> <span data-ttu-id="a618f-180">`HttpClient` enthält bereits das Konzept, Handler zu delegieren, die für ausgehende HTTP-Anforderungen miteinander verknüpft werden könnten.</span><span class="sxs-lookup"><span data-stu-id="a618f-180">`HttpClient` already has the concept of delegating handlers that could be linked together for outgoing HTTP requests.</span></span> <span data-ttu-id="a618f-181">Die Factory:</span><span class="sxs-lookup"><span data-stu-id="a618f-181">The factory:</span></span>

* <span data-ttu-id="a618f-182">Sie macht die Registrierung von `HttpClient`-Instanzen pro benanntem Client intuitiver.</span><span class="sxs-lookup"><span data-stu-id="a618f-182">Makes registering of instances of `HttpClient` per named client more intuitive.</span></span>
* <span data-ttu-id="a618f-183">Sie implementiert einen Polly-Handler, der zulässt, dass Polly-Richtlinien für „Retry“, „CircuitBreakers“ usw. verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="a618f-183">Implements a Polly handler that allows Polly policies to be used for Retry, CircuitBreakers, etc.</span></span>

<span data-ttu-id="a618f-184">Weitere Informationen finden Sie unter [Initiieren von HTTP-Anforderungen](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="a618f-184">For more information, see [Initiate HTTP Requests](xref:fundamentals/http-requests).</span></span>

## <a name="kestrel-transport-configuration"></a><span data-ttu-id="a618f-185">Kestrel-Transportkonfiguration</span><span class="sxs-lookup"><span data-stu-id="a618f-185">Kestrel transport configuration</span></span>

<span data-ttu-id="a618f-186">Mit dem Release von ASP.NET Core 2.1 basiert der Standardtransport von Kestrel nicht mehr auf Libuv, sondern auf verwalteten Sockets.</span><span class="sxs-lookup"><span data-stu-id="a618f-186">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="a618f-187">Weitere Informationen finden Sie unter [Kestrel web server implementation: Transport configuration (Kestrel: Transportkonfiguration)](xref:fundamentals/servers/kestrel#transport-configuration).</span><span class="sxs-lookup"><span data-stu-id="a618f-187">For more information, see [Kestrel web server implementation: Transport configuration](xref:fundamentals/servers/kestrel#transport-configuration).</span></span>

## <a name="generic-host-builder"></a><span data-ttu-id="a618f-188">Generator für generische Hosts</span><span class="sxs-lookup"><span data-stu-id="a618f-188">Generic host builder</span></span>

<span data-ttu-id="a618f-189">Der Generator für generische Hosts (`HostBuilder`) wurde eingeführt.</span><span class="sxs-lookup"><span data-stu-id="a618f-189">The Generic Host Builder (`HostBuilder`) has been introduced.</span></span> <span data-ttu-id="a618f-190">Dieser Generator kann für Apps verwendet werden, die keine HTTP-Anforderungen verarbeiten (Messaging, Hintergrundaufgaben usw.).</span><span class="sxs-lookup"><span data-stu-id="a618f-190">This builder can be used for apps that don't process HTTP requests (Messaging, background tasks, etc.).</span></span>

<span data-ttu-id="a618f-191">Weitere Informationen finden Sie unter [Generischer .NET-Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="a618f-191">For more information, see [.NET Generic Host](xref:fundamentals/host/generic-host).</span></span>

## <a name="updated-spa-templates"></a><span data-ttu-id="a618f-192">Aktualisierte SPA-Vorlagen</span><span class="sxs-lookup"><span data-stu-id="a618f-192">Updated SPA templates</span></span>

<span data-ttu-id="a618f-193">Die Vorlagen der Single-Page-Webanwendung für Angular, React und React & Redux werden aktualisiert, damit die Standardprojektstrukturen und Buildsysteme für die einzelnen Frameworks verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="a618f-193">The Single Page Application templates for Angular, React, and React with Redux are updated to use the standard project structures and build systems for each framework.</span></span>

<span data-ttu-id="a618f-194">Die Angular-Vorlage basiert auf der Angular-CLI, während die React-Vorlagen auf „create-react-app“ basieren.</span><span class="sxs-lookup"><span data-stu-id="a618f-194">The Angular template is based on the Angular CLI, and the React templates are based on create-react-app.</span></span>

<span data-ttu-id="a618f-195">Weitere Informationen finden Sie unter:</span><span class="sxs-lookup"><span data-stu-id="a618f-195">For more information, see:</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:spa/react-with-redux>

## <a name="razor-pages-search-for-razor-assets"></a><span data-ttu-id="a618f-196">Razor Pages-Suche nach Razor-Objekten</span><span class="sxs-lookup"><span data-stu-id="a618f-196">Razor Pages search for Razor assets</span></span>

<span data-ttu-id="a618f-197">In Version 2.1 sucht Razor Pages in den folgenden Verzeichnissen in der aufgeführten Reihenfolge nach Razor-Objekten (z.B. Layouts und Teilausführungen):</span><span class="sxs-lookup"><span data-stu-id="a618f-197">In 2.1, Razor Pages search for Razor assets (such as layouts and partials) in the following directories in the listed order:</span></span>

1. <span data-ttu-id="a618f-198">Im aktuellen Ordner „Pages“.</span><span class="sxs-lookup"><span data-stu-id="a618f-198">Current Pages folder.</span></span>
1. <span data-ttu-id="a618f-199">*/Pages/Shared/*</span><span class="sxs-lookup"><span data-stu-id="a618f-199">*/Pages/Shared/*</span></span>
1. <span data-ttu-id="a618f-200">*/Views/Shared/*</span><span class="sxs-lookup"><span data-stu-id="a618f-200">*/Views/Shared/*</span></span>

## <a name="razor-pages-in-an-area"></a><span data-ttu-id="a618f-201">Razor Pages in einem Bereich</span><span class="sxs-lookup"><span data-stu-id="a618f-201">Razor Pages in an area</span></span>

<span data-ttu-id="a618f-202">Razor Pages unterstützt jetzt [Bereiche](xref:mvc/controllers/areas).</span><span class="sxs-lookup"><span data-stu-id="a618f-202">Razor Pages now support [areas](xref:mvc/controllers/areas).</span></span> <span data-ttu-id="a618f-203">Erstellen Sie für die Anzeige eines Beispiels für Bereiche eine neue Razor Pages-Web-App mit individuellen Benutzerkonten.</span><span class="sxs-lookup"><span data-stu-id="a618f-203">To see an example of areas, create a new Razor Pages web app with individual user accounts.</span></span> <span data-ttu-id="a618f-204">Eine Razor Pages-Web-App mit individuellen Benutzerkonten enthält */Areas/Identity/Pages*.</span><span class="sxs-lookup"><span data-stu-id="a618f-204">A Razor Pages web app with individual user accounts includes */Areas/Identity/Pages*.</span></span>

## <a name="mvc-compatibility-version"></a><span data-ttu-id="a618f-205">MVC-Kompatibilitätsversion</span><span class="sxs-lookup"><span data-stu-id="a618f-205">MVC compatibility version</span></span>

<span data-ttu-id="a618f-206">Durch die Methode <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> kann eine App Änderungen im Verhalten annehmen oder ablehnen, die in ASP.NET Core MVC 2.1 und höher eingeführt werden und potentiell Fehler verursachen.</span><span class="sxs-lookup"><span data-stu-id="a618f-206">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="a618f-207">Weitere Informationen finden Sie unter <xref:mvc/compatibility-version>.</span><span class="sxs-lookup"><span data-stu-id="a618f-207">For more information, see <xref:mvc/compatibility-version>.</span></span>

## <a name="migrate-from-20-to-21"></a><span data-ttu-id="a618f-208">Migrieren von 2.0 zu 2.1</span><span class="sxs-lookup"><span data-stu-id="a618f-208">Migrate from 2.0 to 2.1</span></span>

<span data-ttu-id="a618f-209">Siehe [Migrieren von ASP.NET Core 2.0 zu 2.1](xref:migration/20_21).</span><span class="sxs-lookup"><span data-stu-id="a618f-209">See [Migrate from ASP.NET Core 2.0 to 2.1](xref:migration/20_21).</span></span>

## <a name="additional-information"></a><span data-ttu-id="a618f-210">Zusätzliche Informationen</span><span class="sxs-lookup"><span data-stu-id="a618f-210">Additional information</span></span>

<span data-ttu-id="a618f-211">Die vollständige Liste der Änderungen finden Sie unter [ASP.NET Core 2.1 – Anmerkungen zu dieser Version](https://github.com/aspnet/Home/releases/tag/2.1.0).</span><span class="sxs-lookup"><span data-stu-id="a618f-211">For the complete list of changes, see the [ASP.NET Core 2.1 Release Notes](https://github.com/aspnet/Home/releases/tag/2.1.0).</span></span>
