---
title: Konfigurieren der Zertifikat Authentifizierung in ASP.net Core
author: blowdart
description: Erfahren Sie, wie Sie die Zertifikat Authentifizierung in ASP.net Core für IIS und http. sys konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: ce7bcdbfb8ce0f1febf34b49786e92c917be139c
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384859"
---
# <a name="overview"></a><span data-ttu-id="179e0-103">Übersicht</span><span class="sxs-lookup"><span data-stu-id="179e0-103">Overview</span></span>

<span data-ttu-id="179e0-104">`Microsoft.AspNetCore.Authentication.Certificate`enthält eine-Implementierung, die der [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) für ASP.net Core ähnelt.</span><span class="sxs-lookup"><span data-stu-id="179e0-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="179e0-105">Die Zertifikat Authentifizierung erfolgt auf TLS-Ebene, lange vor der ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="179e0-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="179e0-106">Genauer ist dies ein Authentifizierungs Handler, der das Zertifikat überprüft und dann ein Ereignis liefert, bei dem Sie dieses Zertifikat in eine `ClaimsPrincipal`auflösen können.</span><span class="sxs-lookup"><span data-stu-id="179e0-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="179e0-107">[Konfigurieren Sie Ihren Host für die](#configure-your-host-to-require-certificates) Zertifikat Authentifizierung, also IIS, Kestrel, Azure-Web-Apps oder andere Benutzer, die Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="179e0-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="179e0-108">Proxy-und Load Balancer-Szenarios</span><span class="sxs-lookup"><span data-stu-id="179e0-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="179e0-109">Die Zertifikat Authentifizierung ist ein Zustands behaftetes Szenario, das hauptsächlich verwendet wird, wenn ein Proxy oder ein Load Balancer den Datenverkehr zwischen Clients und Servern nicht</span><span class="sxs-lookup"><span data-stu-id="179e0-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="179e0-110">Wenn ein Proxy oder ein Lasten Ausgleichs Modul verwendet wird, funktioniert die Zertifikat Authentifizierung nur, wenn der Proxy oder der Load Balancer:</span><span class="sxs-lookup"><span data-stu-id="179e0-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="179e0-111">Behandelt die-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="179e0-111">Handles the authentication.</span></span>
* <span data-ttu-id="179e0-112">Übergibt die Benutzer Authentifizierungsinformationen an die APP (z. b. in einem Anforderungs Header), die die Authentifizierungsinformationen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="179e0-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="179e0-113">Eine Alternative zur Zertifikat Authentifizierung in Umgebungen, in denen Proxys und Lasten Ausgleichs Module verwendet werden, ist Active Directory Verbund Dienste (AD FS) mit OpenID Connect (oidc).</span><span class="sxs-lookup"><span data-stu-id="179e0-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="179e0-114">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="179e0-114">Get started</span></span>

<span data-ttu-id="179e0-115">Erwerben Sie ein HTTPS-Zertifikat, wenden Sie es an, und [Konfigurieren Sie den Host](#configure-your-host-to-require-certificates) , um Zertifikate anzufordern.</span><span class="sxs-lookup"><span data-stu-id="179e0-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="179e0-116">Fügen Sie in Ihrer Web-App einen Verweis auf `Microsoft.AspNetCore.Authentication.Certificate` das Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="179e0-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="179e0-117">Verwenden Sie dann `Startup.Configure` in der- `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` Methode mit den Optionen, und geben Sie `OnCertificateValidated` einen Delegaten für an, um eine ergänzende Validierung für das mit Anforderungen gesendete Client Zertifikat durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="179e0-117">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="179e0-118">Diese Informationen werden in ein `ClaimsPrincipal` -Objekt umgewandelt und für `context.Principal` die-Eigenschaft festgelegt.</span><span class="sxs-lookup"><span data-stu-id="179e0-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="179e0-119">Wenn die Authentifizierung fehlschlägt, gibt dieser `403 (Forbidden)` Handler wie erwartet `401 (Unauthorized)`eine Antwort zurück.</span><span class="sxs-lookup"><span data-stu-id="179e0-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="179e0-120">Der Grund dafür ist, dass die Authentifizierung während der anfänglichen TLS-Verbindung stattfinden soll.</span><span class="sxs-lookup"><span data-stu-id="179e0-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="179e0-121">Bis zum Zeitpunkt, an dem der Handler erreicht wird, ist es zu spät.</span><span class="sxs-lookup"><span data-stu-id="179e0-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="179e0-122">Es gibt keine Möglichkeit, die Verbindung von einer anonymen Verbindung mit einem Zertifikat zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="179e0-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="179e0-123">Fügen Sie `app.UseAuthentication();` außerdem die `Startup.Configure` -Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="179e0-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="179e0-124">Andernfalls wird HttpContext. User nicht auf `ClaimsPrincipal` aus dem Zertifikat erstellt festgelegt.</span><span class="sxs-lookup"><span data-stu-id="179e0-124">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="179e0-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="179e0-125">For example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(
        CertificateAuthenticationDefaults.AuthenticationScheme)
            .AddCertificate();
    // All the other service configuration.
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseAuthentication();

    // All the other app configuration.
}
```

<span data-ttu-id="179e0-126">Im vorangehenden Beispiel wird die Standardmethode zum Hinzufügen der Zertifikat Authentifizierung veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="179e0-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="179e0-127">Der Handler erstellt einen Benutzer Prinzipal mithilfe der allgemeinen Zertifikat Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="179e0-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="179e0-128">Konfigurieren der Zertifikat Überprüfung</span><span class="sxs-lookup"><span data-stu-id="179e0-128">Configure certificate validation</span></span>

<span data-ttu-id="179e0-129">Der `CertificateAuthenticationOptions` Handler verfügt über einige integrierte Validierungen, bei denen es sich um die minimalen Überprüfungen handelt, die Sie für ein Zertifikat ausführen sollten.</span><span class="sxs-lookup"><span data-stu-id="179e0-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="179e0-130">Jede dieser Einstellungen ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="179e0-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="179e0-131">"Verkewedcertifipeetypes = verkettet", "selfsigned" oder "alle" (verkettet | Selfsigned)</span><span class="sxs-lookup"><span data-stu-id="179e0-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="179e0-132">Mit dieser Überprüfung wird überprüft, ob nur der geeignete Zertifikattyp zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="179e0-132">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="179e0-133">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="179e0-133">ValidateCertificateUse</span></span>

<span data-ttu-id="179e0-134">Mit dieser Überprüfung wird überprüft, ob das vom Client vorgelegte Zertifikat über die erweiterte Schlüssel Verwendung (EKU) der Client Authentifizierung oder über keine EKUs verfügt.</span><span class="sxs-lookup"><span data-stu-id="179e0-134">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="179e0-135">Wie bei den Spezifikationen gesagt, werden alle EKUs als gültig eingestuft, wenn kein EKU angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="179e0-135">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="179e0-136">Validatevalidityperiod</span><span class="sxs-lookup"><span data-stu-id="179e0-136">ValidateValidityPeriod</span></span>

<span data-ttu-id="179e0-137">Diese Prüfung überprüft, ob das Zertifikat innerhalb seines Gültigkeits Zeitraums liegt.</span><span class="sxs-lookup"><span data-stu-id="179e0-137">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="179e0-138">Bei jeder Anforderung stellt der Handler sicher, dass ein Zertifikat, das bei der Präsentation gültig war, während der aktuellen Sitzung nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="179e0-138">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="179e0-139">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="179e0-139">RevocationFlag</span></span>

<span data-ttu-id="179e0-140">Ein Flag, das angibt, welche Zertifikate in der Kette auf die Sperrung geprüft werden.</span><span class="sxs-lookup"><span data-stu-id="179e0-140">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="179e0-141">Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.</span><span class="sxs-lookup"><span data-stu-id="179e0-141">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="179e0-142">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="179e0-142">RevocationMode</span></span>

<span data-ttu-id="179e0-143">Ein Flag, das angibt, wie Sperr Überprüfungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="179e0-143">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="179e0-144">Das Angeben einer Online Überprüfung kann zu einer langen Verzögerung führen, während die Zertifizierungsstelle kontaktiert wird.</span><span class="sxs-lookup"><span data-stu-id="179e0-144">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="179e0-145">Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.</span><span class="sxs-lookup"><span data-stu-id="179e0-145">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="179e0-146">Kann ich meine APP so konfigurieren, dass ein Zertifikat nur für bestimmte Pfade erforderlich ist?</span><span class="sxs-lookup"><span data-stu-id="179e0-146">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="179e0-147">Dies ist nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="179e0-147">This isn't possible.</span></span> <span data-ttu-id="179e0-148">Merken Sie sich, dass der Zertifikat Austausch durchgeführt wurde, bis der Start der HTTPS-Konversation durch den Server erfolgt ist, bevor die erste Anforderung über diese Verbindung empfangen wird. Daher ist es nicht möglich, den Bereich auf der Grundlage von Anforderungs Feldern festzustellen.</span><span class="sxs-lookup"><span data-stu-id="179e0-148">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="179e0-149">Handlerereignisse</span><span class="sxs-lookup"><span data-stu-id="179e0-149">Handler events</span></span>

<span data-ttu-id="179e0-150">Der Handler hat zwei Ereignisse:</span><span class="sxs-lookup"><span data-stu-id="179e0-150">The handler has two events:</span></span>

* <span data-ttu-id="179e0-151">`OnAuthenticationFailed`&ndash; Wird aufgerufen, wenn während der Authentifizierung eine Ausnahme auftritt und Sie reagieren können.</span><span class="sxs-lookup"><span data-stu-id="179e0-151">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="179e0-152">`OnCertificateValidated`&ndash; Wird aufgerufen, nachdem das Zertifikat überprüft wurde, die Überprüfung bestanden und ein Standard Prinzipal erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="179e0-152">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="179e0-153">Dieses Ereignis ermöglicht es Ihnen, ihre eigene Validierung auszuführen und den Prinzipal zu erweitern oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="179e0-153">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="179e0-154">Beispiele hierfür sind:</span><span class="sxs-lookup"><span data-stu-id="179e0-154">For examples include:</span></span>
  * <span data-ttu-id="179e0-155">Ermitteln, ob das Zertifikat den Diensten bekannt ist.</span><span class="sxs-lookup"><span data-stu-id="179e0-155">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="179e0-156">Erstellen eines eigenen Prinzipals.</span><span class="sxs-lookup"><span data-stu-id="179e0-156">Constructing your own principal.</span></span> <span data-ttu-id="179e0-157">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="179e0-157">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var claims = new[]
                {
                    new Claim(
                        ClaimTypes.NameIdentifier, 
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer),
                    new Claim(ClaimTypes.Name,
                        context.ClientCertificate.Subject,
                        ClaimValueTypes.String, 
                        context.Options.ClaimsIssuer)
                };

                context.Principal = new ClaimsPrincipal(
                    new ClaimsIdentity(claims, context.Scheme.Name));
                context.Success();

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="179e0-158">Wenn Sie feststellen, dass das eingehende Zertifikat die zusätzliche über `context.Fail("failure reason")` Prüfung nicht erfüllt, können Sie mit einem Fehler Grund anrufen.</span><span class="sxs-lookup"><span data-stu-id="179e0-158">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="179e0-159">Für echte Funktionen möchten Sie wahrscheinlich einen Dienst aufzurufen, der in der Abhängigkeitsinjektion registriert ist, der eine Verbindung mit einer Datenbank oder einem anderen Benutzerspeicher herstellt.</span><span class="sxs-lookup"><span data-stu-id="179e0-159">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="179e0-160">Greifen Sie auf den Dienst zu, indem Sie den Kontext verwenden, der in ihren Delegaten</span><span class="sxs-lookup"><span data-stu-id="179e0-160">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="179e0-161">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="179e0-161">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddAuthentication(
    CertificateAuthenticationDefaults.AuthenticationScheme)
    .AddCertificate(options =>
    {
        options.Events = new CertificateAuthenticationEvents
        {
            OnCertificateValidated = context =>
            {
                var validationService =
                    context.HttpContext.RequestServices
                        .GetService<ICertificateValidationService>();
                
                if (validationService.ValidateCertificate(
                    context.ClientCertificate))
                {
                    var claims = new[]
                    {
                        new Claim(
                            ClaimTypes.NameIdentifier, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer),
                        new Claim(
                            ClaimTypes.Name, 
                            context.ClientCertificate.Subject, 
                            ClaimValueTypes.String, 
                            context.Options.ClaimsIssuer)
                    };

                    context.Principal = new ClaimsPrincipal(
                        new ClaimsIdentity(claims, context.Scheme.Name));
                    context.Success();
                }                     

                return Task.CompletedTask;
            }
        };
    });
```

<span data-ttu-id="179e0-162">Konzeptionell ist die Validierung des Zertifikats ein Autorisierungs Problem.</span><span class="sxs-lookup"><span data-stu-id="179e0-162">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="179e0-163">Das Hinzufügen einer Überprüfung (z. b. eines Ausstellers oder Fingerabdrucks in einer Autorisierungs Richtlinie) und nicht innerhalb `OnCertificateValidated`von ist durchaus akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="179e0-163">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="179e0-164">Konfigurieren des Hosts, damit Zertifikate erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="179e0-164">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="179e0-165">Kestrel</span><span class="sxs-lookup"><span data-stu-id="179e0-165">Kestrel</span></span>

<span data-ttu-id="179e0-166">Konfigurieren Sie in *Program.cs*Kestrel wie folgt:</span><span class="sxs-lookup"><span data-stu-id="179e0-166">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static IWebHost BuildWebHost(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel(options =>
        {
            options.ConfigureHttpsDefaults(opt => 
                opt.ClientCertificateMode = 
                    ClientCertificateMode.RequireCertificate);
        })
        .Build();
```

### <a name="iis"></a><span data-ttu-id="179e0-167">IIS</span><span class="sxs-lookup"><span data-stu-id="179e0-167">IIS</span></span>

<span data-ttu-id="179e0-168">Führen Sie im IIS-Manager die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="179e0-168">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="179e0-169">Wählen Sie auf der Registerkarte **Verbindungen** Ihre Website aus.</span><span class="sxs-lookup"><span data-stu-id="179e0-169">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="179e0-170">Doppelklicken Sie im Fenster " **Featureansicht** " auf die Option " **SSL-Einstellungen** ".</span><span class="sxs-lookup"><span data-stu-id="179e0-170">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="179e0-171">Aktivieren Sie das Kontrollkästchen **SSL erforderlich** , und aktivieren Sie im Abschnitt **Client Zertifikate** das Optionsfeld **erforderlich** .</span><span class="sxs-lookup"><span data-stu-id="179e0-171">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Client Zertifikat Einstellungen in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="179e0-173">Azure und benutzerdefinierte Webproxys</span><span class="sxs-lookup"><span data-stu-id="179e0-173">Azure and custom web proxies</span></span>

<span data-ttu-id="179e0-174">Informationen zum Konfigurieren der Middleware für die Zertifikat Weiterleitung finden Sie in der [Dokumentation zu Host und](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="179e0-174">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
