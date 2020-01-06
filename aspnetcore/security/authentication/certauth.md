---
title: Konfigurieren der Zertifikat Authentifizierung in ASP.net Core
author: blowdart
description: Erfahren Sie, wie Sie die Zertifikat Authentifizierung in ASP.net Core für IIS und http. sys konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 01/02/2020
uid: security/authentication/certauth
ms.openlocfilehash: 9c175439c0313d62c75898f1af097774b06f353a
ms.sourcegitcommit: e7d4fe6727d423f905faaeaa312f6c25ef844047
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75608144"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a><span data-ttu-id="72d34-103">Konfigurieren der Zertifikat Authentifizierung in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="72d34-103">Configure certificate authentication in ASP.NET Core</span></span>

<span data-ttu-id="72d34-104">`Microsoft.AspNetCore.Authentication.Certificate` enthält eine-Implementierung, die der [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) für ASP.net Core ähnelt.</span><span class="sxs-lookup"><span data-stu-id="72d34-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="72d34-105">Die Zertifikat Authentifizierung erfolgt auf TLS-Ebene, lange vor der ASP.net Core.</span><span class="sxs-lookup"><span data-stu-id="72d34-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="72d34-106">Genauer ist dies ein Authentifizierungs Handler, der das Zertifikat überprüft und dann ein Ereignis liefert, bei dem Sie dieses Zertifikat in eine `ClaimsPrincipal`auflösen können.</span><span class="sxs-lookup"><span data-stu-id="72d34-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="72d34-107">[Konfigurieren Sie Ihren Host für die](#configure-your-host-to-require-certificates) Zertifikat Authentifizierung, also IIS, Kestrel, Azure-Web-Apps oder andere Benutzer, die Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="72d34-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="proxy-and-load-balancer-scenarios"></a><span data-ttu-id="72d34-108">Proxy-und Load Balancer-Szenarios</span><span class="sxs-lookup"><span data-stu-id="72d34-108">Proxy and load balancer scenarios</span></span>

<span data-ttu-id="72d34-109">Die Zertifikat Authentifizierung ist ein Zustands behaftetes Szenario, das hauptsächlich verwendet wird, wenn ein Proxy oder ein Load Balancer den Datenverkehr zwischen Clients und Servern nicht</span><span class="sxs-lookup"><span data-stu-id="72d34-109">Certificate authentication is a stateful scenario primarily used where a proxy or load balancer doesn't handle traffic between clients and servers.</span></span> <span data-ttu-id="72d34-110">Wenn ein Proxy oder ein Lasten Ausgleichs Modul verwendet wird, funktioniert die Zertifikat Authentifizierung nur, wenn der Proxy oder der Load Balancer:</span><span class="sxs-lookup"><span data-stu-id="72d34-110">If a proxy or load balancer is used, certificate authentication only works if the proxy or load balancer:</span></span>

* <span data-ttu-id="72d34-111">Behandelt die-Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="72d34-111">Handles the authentication.</span></span>
* <span data-ttu-id="72d34-112">Übergibt die Benutzer Authentifizierungsinformationen an die APP (z. b. in einem Anforderungs Header), die die Authentifizierungsinformationen verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="72d34-112">Passes the user authentication information to the app (for example, in a request header), which acts on the authentication information.</span></span>

<span data-ttu-id="72d34-113">Eine Alternative zur Zertifikat Authentifizierung in Umgebungen, in denen Proxys und Lasten Ausgleichs Module verwendet werden, ist Active Directory Verbund Dienste (AD FS) mit OpenID Connect (oidc).</span><span class="sxs-lookup"><span data-stu-id="72d34-113">An alternative to certificate authentication in environments where proxies and load balancers are used is Active Directory Federated Services (ADFS) with OpenID Connect (OIDC).</span></span>

## <a name="get-started"></a><span data-ttu-id="72d34-114">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="72d34-114">Get started</span></span>

<span data-ttu-id="72d34-115">Erwerben Sie ein HTTPS-Zertifikat, wenden Sie es an, und [Konfigurieren Sie den Host](#configure-your-host-to-require-certificates) , um Zertifikate anzufordern.</span><span class="sxs-lookup"><span data-stu-id="72d34-115">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="72d34-116">Fügen Sie in Ihrer Web-App einen Verweis auf das `Microsoft.AspNetCore.Authentication.Certificate`-Paket hinzu.</span><span class="sxs-lookup"><span data-stu-id="72d34-116">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="72d34-117">Nennen Sie dann in der `Startup.ConfigureServices`-Methode `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` mit den Optionen, und geben Sie einen Delegaten für `OnCertificateValidated` an, um eine ergänzende Validierung für das mit Anforderungen gesendete Client Zertifikat durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="72d34-117">Then in the `Startup.ConfigureServices` method, call `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="72d34-118">Wandeln Sie diese Informationen in eine `ClaimsPrincipal` um, und legen Sie Sie für die `context.Principal`-Eigenschaft fest.</span><span class="sxs-lookup"><span data-stu-id="72d34-118">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="72d34-119">Wenn bei der Authentifizierung ein Fehler auftritt, gibt dieser Handler wie erwartet eine `403 (Forbidden)` Antwort zurück, die `401 (Unauthorized)`.</span><span class="sxs-lookup"><span data-stu-id="72d34-119">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="72d34-120">Der Grund dafür ist, dass die Authentifizierung während der anfänglichen TLS-Verbindung stattfinden soll.</span><span class="sxs-lookup"><span data-stu-id="72d34-120">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="72d34-121">Bis zum Zeitpunkt, an dem der Handler erreicht wird, ist es zu spät.</span><span class="sxs-lookup"><span data-stu-id="72d34-121">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="72d34-122">Es gibt keine Möglichkeit, die Verbindung von einer anonymen Verbindung mit einem Zertifikat zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="72d34-122">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="72d34-123">Fügen Sie außerdem `app.UseAuthentication();` in der `Startup.Configure`-Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="72d34-123">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="72d34-124">Andernfalls wird der `HttpContext.User` nicht auf `ClaimsPrincipal`, der aus dem Zertifikat erstellt wurde, festgelegt.</span><span class="sxs-lookup"><span data-stu-id="72d34-124">Otherwise, the `HttpContext.User` will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="72d34-125">Beispiel:</span><span class="sxs-lookup"><span data-stu-id="72d34-125">For example:</span></span>

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

<span data-ttu-id="72d34-126">Im vorangehenden Beispiel wird die Standardmethode zum Hinzufügen der Zertifikat Authentifizierung veranschaulicht.</span><span class="sxs-lookup"><span data-stu-id="72d34-126">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="72d34-127">Der Handler erstellt einen Benutzer Prinzipal mithilfe der allgemeinen Zertifikat Eigenschaften.</span><span class="sxs-lookup"><span data-stu-id="72d34-127">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="72d34-128">Konfigurieren der Zertifikat Überprüfung</span><span class="sxs-lookup"><span data-stu-id="72d34-128">Configure certificate validation</span></span>

<span data-ttu-id="72d34-129">Der `CertificateAuthenticationOptions` Handler verfügt über einige integrierte Validierungen, bei denen es sich um die minimalen Überprüfungen handelt, die Sie für ein Zertifikat ausführen sollten.</span><span class="sxs-lookup"><span data-stu-id="72d34-129">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="72d34-130">Jede dieser Einstellungen ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="72d34-130">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="72d34-131">"Verkewedcertifipeetypes = verkettet", "selfsigned" oder "alle" (verkettet | Selfsigned)</span><span class="sxs-lookup"><span data-stu-id="72d34-131">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="72d34-132">Standardwert: `CertificateTypes.Chained`</span><span class="sxs-lookup"><span data-stu-id="72d34-132">Default value: `CertificateTypes.Chained`</span></span>

<span data-ttu-id="72d34-133">Mit dieser Überprüfung wird überprüft, ob nur der geeignete Zertifikattyp zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="72d34-133">This check validates that only the appropriate certificate type is allowed.</span></span> <span data-ttu-id="72d34-134">Wenn die APP selbst signierte Zertifikate verwendet, muss diese Option auf `CertificateTypes.All` oder `CertificateTypes.SelfSigned`festgelegt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-134">If the app is using self-signed certificates, this option needs to be set to `CertificateTypes.All` or `CertificateTypes.SelfSigned`.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="72d34-135">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="72d34-135">ValidateCertificateUse</span></span>

<span data-ttu-id="72d34-136">Standardwert: `true`</span><span class="sxs-lookup"><span data-stu-id="72d34-136">Default value: `true`</span></span>

<span data-ttu-id="72d34-137">Mit dieser Überprüfung wird überprüft, ob das vom Client vorgelegte Zertifikat über die erweiterte Schlüssel Verwendung (EKU) der Client Authentifizierung oder über keine EKUs verfügt.</span><span class="sxs-lookup"><span data-stu-id="72d34-137">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="72d34-138">Wie bei den Spezifikationen gesagt, werden alle EKUs als gültig eingestuft, wenn kein EKU angegeben wird.</span><span class="sxs-lookup"><span data-stu-id="72d34-138">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="72d34-139">Validatevalidityperiod</span><span class="sxs-lookup"><span data-stu-id="72d34-139">ValidateValidityPeriod</span></span>

<span data-ttu-id="72d34-140">Standardwert: `true`</span><span class="sxs-lookup"><span data-stu-id="72d34-140">Default value: `true`</span></span>

<span data-ttu-id="72d34-141">Diese Prüfung überprüft, ob das Zertifikat innerhalb seines Gültigkeits Zeitraums liegt.</span><span class="sxs-lookup"><span data-stu-id="72d34-141">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="72d34-142">Bei jeder Anforderung stellt der Handler sicher, dass ein Zertifikat, das bei der Präsentation gültig war, während der aktuellen Sitzung nicht abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="72d34-142">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="72d34-143">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="72d34-143">RevocationFlag</span></span>

<span data-ttu-id="72d34-144">Standardwert: `X509RevocationFlag.ExcludeRoot`</span><span class="sxs-lookup"><span data-stu-id="72d34-144">Default value: `X509RevocationFlag.ExcludeRoot`</span></span>

<span data-ttu-id="72d34-145">Ein Flag, das angibt, welche Zertifikate in der Kette auf die Sperrung geprüft werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-145">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="72d34-146">Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.</span><span class="sxs-lookup"><span data-stu-id="72d34-146">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="72d34-147">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="72d34-147">RevocationMode</span></span>

<span data-ttu-id="72d34-148">Standardwert: `X509RevocationMode.Online`</span><span class="sxs-lookup"><span data-stu-id="72d34-148">Default value: `X509RevocationMode.Online`</span></span>

<span data-ttu-id="72d34-149">Ein Flag, das angibt, wie Sperr Überprüfungen durchgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-149">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="72d34-150">Das Angeben einer Online Überprüfung kann zu einer langen Verzögerung führen, während die Zertifizierungsstelle kontaktiert wird.</span><span class="sxs-lookup"><span data-stu-id="72d34-150">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="72d34-151">Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.</span><span class="sxs-lookup"><span data-stu-id="72d34-151">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="72d34-152">Kann ich meine APP so konfigurieren, dass ein Zertifikat nur für bestimmte Pfade erforderlich ist?</span><span class="sxs-lookup"><span data-stu-id="72d34-152">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="72d34-153">Dies ist nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="72d34-153">This isn't possible.</span></span> <span data-ttu-id="72d34-154">Merken Sie sich, dass der Zertifikat Austausch durchgeführt wurde, bis der Start der HTTPS-Konversation durch den Server erfolgt ist, bevor die erste Anforderung über diese Verbindung empfangen wird. Daher ist es nicht möglich, den Bereich auf der Grundlage von Anforderungs Feldern festzustellen.</span><span class="sxs-lookup"><span data-stu-id="72d34-154">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="72d34-155">Handlerereignisse</span><span class="sxs-lookup"><span data-stu-id="72d34-155">Handler events</span></span>

<span data-ttu-id="72d34-156">Der Handler hat zwei Ereignisse:</span><span class="sxs-lookup"><span data-stu-id="72d34-156">The handler has two events:</span></span>

* <span data-ttu-id="72d34-157">`OnAuthenticationFailed` &ndash; aufgerufen, wenn eine Ausnahme während der Authentifizierung auftritt und es Ihnen ermöglicht, zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="72d34-157">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="72d34-158">`OnCertificateValidated` &ndash; aufgerufen, nachdem das Zertifikat überprüft wurde, wird die Überprüfung bestanden, und ein Standard Prinzipal wurde erstellt.</span><span class="sxs-lookup"><span data-stu-id="72d34-158">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="72d34-159">Dieses Ereignis ermöglicht es Ihnen, ihre eigene Validierung auszuführen und den Prinzipal zu erweitern oder zu ersetzen.</span><span class="sxs-lookup"><span data-stu-id="72d34-159">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="72d34-160">Beispiele hierfür sind:</span><span class="sxs-lookup"><span data-stu-id="72d34-160">For examples include:</span></span>
  * <span data-ttu-id="72d34-161">Ermitteln, ob das Zertifikat den Diensten bekannt ist.</span><span class="sxs-lookup"><span data-stu-id="72d34-161">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="72d34-162">Erstellen eines eigenen Prinzipals.</span><span class="sxs-lookup"><span data-stu-id="72d34-162">Constructing your own principal.</span></span> <span data-ttu-id="72d34-163">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="72d34-163">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="72d34-164">Wenn Sie feststellen, dass das eingehende Zertifikat die zusätzliche Überprüfung nicht erfüllt, wenden Sie `context.Fail("failure reason")` mit einem Fehler Grund an.</span><span class="sxs-lookup"><span data-stu-id="72d34-164">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="72d34-165">Für echte Funktionen möchten Sie wahrscheinlich einen Dienst aufzurufen, der in der Abhängigkeitsinjektion registriert ist, der eine Verbindung mit einer Datenbank oder einem anderen Benutzerspeicher herstellt.</span><span class="sxs-lookup"><span data-stu-id="72d34-165">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="72d34-166">Greifen Sie auf den Dienst zu, indem Sie den Kontext verwenden, der in ihren Delegaten</span><span class="sxs-lookup"><span data-stu-id="72d34-166">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="72d34-167">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="72d34-167">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="72d34-168">Konzeptionell ist die Validierung des Zertifikats ein Autorisierungs Problem.</span><span class="sxs-lookup"><span data-stu-id="72d34-168">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="72d34-169">Das Hinzufügen einer Überprüfung (z. b. eines Ausstellers oder Fingerabdrucks in einer Autorisierungs Richtlinie) und nicht innerhalb `OnCertificateValidated`ist durchaus akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="72d34-169">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="72d34-170">Konfigurieren des Hosts, damit Zertifikate erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="72d34-170">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="72d34-171">Kestrel</span><span class="sxs-lookup"><span data-stu-id="72d34-171">Kestrel</span></span>

<span data-ttu-id="72d34-172">Konfigurieren Sie in *Program.cs*Kestrel wie folgt:</span><span class="sxs-lookup"><span data-stu-id="72d34-172">In *Program.cs*, configure Kestrel as follows:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateHostBuilder(args).Build().Run();
}

public static IHostBuilder CreateHostBuilder(string[] args)
{
    return Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
            webBuilder.ConfigureKestrel(o =>
            {
                o.ConfigureHttpsDefaults(o => 
            o.ClientCertificateMode = 
                ClientCertificateMode.RequireCertificate);
            });
        });
}
```

> [!NOTE]
> <span data-ttu-id="72d34-173">Endpunkte, die durch <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Aufrufen von erstellt wurden, **bevor** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> aufgerufen werden, werden nicht auf die Standardwerte angewendet</span><span class="sxs-lookup"><span data-stu-id="72d34-173">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="iis"></a><span data-ttu-id="72d34-174">IIS</span><span class="sxs-lookup"><span data-stu-id="72d34-174">IIS</span></span>

<span data-ttu-id="72d34-175">Führen Sie im IIS-Manager die folgenden Schritte aus:</span><span class="sxs-lookup"><span data-stu-id="72d34-175">Complete the following steps in IIS Manager:</span></span>

1. <span data-ttu-id="72d34-176">Wählen Sie auf der Registerkarte **Verbindungen** Ihre Website aus.</span><span class="sxs-lookup"><span data-stu-id="72d34-176">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="72d34-177">Doppelklicken Sie im Fenster " **Featureansicht** " auf die Option " **SSL-Einstellungen** ".</span><span class="sxs-lookup"><span data-stu-id="72d34-177">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="72d34-178">Aktivieren Sie das Kontrollkästchen **SSL erforderlich** , und aktivieren Sie im Abschnitt **Client Zertifikate** das Optionsfeld **erforderlich** .</span><span class="sxs-lookup"><span data-stu-id="72d34-178">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Client Zertifikat Einstellungen in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="72d34-180">Azure und benutzerdefinierte Webproxys</span><span class="sxs-lookup"><span data-stu-id="72d34-180">Azure and custom web proxies</span></span>

<span data-ttu-id="72d34-181">Informationen zum Konfigurieren der Middleware für die Zertifikat Weiterleitung finden Sie in der [Dokumentation zu Host und](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) Bereitstellung.</span><span class="sxs-lookup"><span data-stu-id="72d34-181">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>

### <a name="use-certificate-authentication-in-azure-web-apps"></a><span data-ttu-id="72d34-182">Verwenden der Zertifikat Authentifizierung in Azure-Web-Apps</span><span class="sxs-lookup"><span data-stu-id="72d34-182">Use certificate authentication in Azure Web Apps</span></span>

<span data-ttu-id="72d34-183">Die `AddCertificateForwarding`-Methode wird verwendet, um Folgendes anzugeben:</span><span class="sxs-lookup"><span data-stu-id="72d34-183">The `AddCertificateForwarding` method is used to specify:</span></span>

* <span data-ttu-id="72d34-184">Der Name des Client Headers.</span><span class="sxs-lookup"><span data-stu-id="72d34-184">The client header name.</span></span>
* <span data-ttu-id="72d34-185">Wie das Zertifikat geladen werden soll (mit der `HeaderConverter`-Eigenschaft).</span><span class="sxs-lookup"><span data-stu-id="72d34-185">How the certificate is to be loaded (using the `HeaderConverter` property).</span></span>

<span data-ttu-id="72d34-186">In Azure-Web-Apps wird das Zertifikat als benutzerdefinierter Anforderungs Header mit dem Namen "`X-ARR-ClientCert`" übermittelt.</span><span class="sxs-lookup"><span data-stu-id="72d34-186">In Azure Web Apps, the certificate is passed as a custom request header named `X-ARR-ClientCert`.</span></span> <span data-ttu-id="72d34-187">Konfigurieren Sie die Zertifikat Weiterleitung in `Startup.ConfigureServices`, um Sie zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="72d34-187">To use it, configure certificate forwarding in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCertificateForwarding(options =>
    {
        options.CertificateHeader = "X-ARR-ClientCert";
        options.HeaderConverter = (headerValue) =>
        {
            X509Certificate2 clientCertificate = null;
        
            if(!string.IsNullOrWhiteSpace(headerValue))
            {
                byte[] bytes = StringToByteArray(headerValue);
                clientCertificate = new X509Certificate2(bytes);
            }

            return clientCertificate;
        };
    });
}

private static byte[] StringToByteArray(string hex)
{
    int NumberChars = hex.Length;
    byte[] bytes = new byte[NumberChars / 2];

    for (int i = 0; i < NumberChars; i += 2)
    {
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    }

    return bytes;
}
```

<span data-ttu-id="72d34-188">Die `Startup.Configure`-Methode fügt dann die Middleware hinzu.</span><span class="sxs-lookup"><span data-stu-id="72d34-188">The `Startup.Configure` method then adds the middleware.</span></span> <span data-ttu-id="72d34-189">`UseCertificateForwarding` wird vor den Aufrufen von `UseAuthentication` und `UseAuthorization`aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="72d34-189">`UseCertificateForwarding` is called before the calls to `UseAuthentication` and `UseAuthorization`:</span></span>

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseRouting();

    app.UseCertificateForwarding();
    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

<span data-ttu-id="72d34-190">Eine separate Klasse kann verwendet werden, um Validierungs Logik zu implementieren.</span><span class="sxs-lookup"><span data-stu-id="72d34-190">A separate class can be used to implement validation logic.</span></span> <span data-ttu-id="72d34-191">Da in diesem Beispiel dasselbe selbst signierte Zertifikat verwendet wird, sollten Sie sicherstellen, dass nur Ihr Zertifikat verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="72d34-191">Because the same self-signed certificate is used in this example, ensure that only your certificate can be used.</span></span> <span data-ttu-id="72d34-192">Überprüfen Sie, ob die Fingerabdrücke sowohl des Client Zertifikats als auch des Serverzertifikats Stimmen. andernfalls können alle Zertifikate verwendet werden, die für die Authentifizierung ausreichen.</span><span class="sxs-lookup"><span data-stu-id="72d34-192">Validate that the thumbprints of both the client certificate and the server certificate match, otherwise any certificate can be used and will be enough to authenticate.</span></span> <span data-ttu-id="72d34-193">Dies wird in der `AddCertificate`-Methode verwendet.</span><span class="sxs-lookup"><span data-stu-id="72d34-193">This would be used inside the `AddCertificate` method.</span></span> <span data-ttu-id="72d34-194">Sie können den Betreff oder den Aussteller hier auch überprüfen, wenn Sie zwischengeschaltete oder untergeordnete Zertifikate verwenden.</span><span class="sxs-lookup"><span data-stu-id="72d34-194">You could also validate the subject or the issuer here if you're using intermediate or child certificates.</span></span>

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code
            // Use thumbprint or key vault
            var cert = new X509Certificate2(
                Path.Combine("sts_dev_cert.pfx"), "1234");

            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate"></a><span data-ttu-id="72d34-195">Implementieren eines httpclient mithilfe eines Zertifikats</span><span class="sxs-lookup"><span data-stu-id="72d34-195">Implement an HttpClient using a certificate</span></span>

<span data-ttu-id="72d34-196">Der Web-API-Client verwendet eine `HttpClient`, die mithilfe einer `IHttpClientFactory` Instanz erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="72d34-196">The web API client uses an `HttpClient`, which was created using an `IHttpClientFactory` instance.</span></span> <span data-ttu-id="72d34-197">Dies bietet keine Möglichkeit, einen Handler für die `HttpClient`zu definieren. verwenden Sie daher eine `HttpRequestMessage`, um das Zertifikat dem `X-ARR-ClientCert`-Anforderungs Header hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="72d34-197">This doesn't provide a way to define a handler for the `HttpClient`, so use an `HttpRequestMessage` to add the certificate to the `X-ARR-ClientCert` request header.</span></span> <span data-ttu-id="72d34-198">Das Zertifikat wird mithilfe der `GetRawCertDataString`-Methode als Zeichenfolge hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="72d34-198">The certificate is added as a string using the `GetRawCertDataString` method.</span></span> 

```csharp
private async Task<JsonDocument> GetApiDataAsync()
{
    try
    {
        // Do not hardcode passwords in production code
        // Use thumbprint or key vault
        var cert = new X509Certificate2(
            Path.Combine(_environment.ContentRootPath, 
                "sts_dev_cert.pfx"), "1234");
        var client = _clientFactory.CreateClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = new Uri("https://localhost:44379/api/values"),
            Method = HttpMethod.Get,
        };

        request.Headers.Add("X-ARR-ClientCert", cert.GetRawCertDataString());
        var response = await client.SendAsync(request);

        if (response.IsSuccessStatusCode)
        {
            var responseContent = await response.Content.ReadAsStringAsync();
            var data = JsonDocument.Parse(responseContent);

            return data;
        }

        throw new ApplicationException(
            $"Status code: {response.StatusCode}, " +
            $"Error: {response.ReasonPhrase}");
    }
    catch (Exception e)
    {
        throw new ApplicationException($"Exception {e}");
    }
}
```

<span data-ttu-id="72d34-199">Wenn das richtige Zertifikat an den Server gesendet wird, werden die Daten zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="72d34-199">If the correct certificate is sent to the server, the data is returned.</span></span> <span data-ttu-id="72d34-200">Wenn kein Zertifikat oder falsches Zertifikat gesendet wird, wird ein HTTP 403-Statuscode zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="72d34-200">If no certificate or the wrong certificate is sent, an HTTP 403 status code is returned.</span></span>

### <a name="create-certificates-in-powershell"></a><span data-ttu-id="72d34-201">Erstellen von Zertifikaten in PowerShell</span><span class="sxs-lookup"><span data-stu-id="72d34-201">Create certificates in PowerShell</span></span>

<span data-ttu-id="72d34-202">Das Erstellen der Zertifikate ist der schwierigste Teil beim Einrichten dieses Flows.</span><span class="sxs-lookup"><span data-stu-id="72d34-202">Creating the certificates is the hardest part in setting up this flow.</span></span> <span data-ttu-id="72d34-203">Ein Stamm Zertifikat kann mit dem `New-SelfSignedCertificate` PowerShell-Cmdlet erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-203">A root certificate can be created using the `New-SelfSignedCertificate` PowerShell cmdlet.</span></span> <span data-ttu-id="72d34-204">Verwenden Sie beim Erstellen des Zertifikats ein sicheres Kennwort.</span><span class="sxs-lookup"><span data-stu-id="72d34-204">When creating the certificate, use a strong password.</span></span> <span data-ttu-id="72d34-205">Es ist wichtig, den `KeyUsageProperty`-Parameter und den `KeyUsage`-Parameter wie gezeigt hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="72d34-205">It's important to add the `KeyUsageProperty` parameter and the `KeyUsage` parameter as shown.</span></span>

#### <a name="create-root-ca"></a><span data-ttu-id="72d34-206">Stamm Zertifizierungsstelle erstellen</span><span class="sxs-lookup"><span data-stu-id="72d34-206">Create root CA</span></span>

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

> [!NOTE]
> <span data-ttu-id="72d34-207">Der Wert des `-DnsName`-Parameters muss mit dem Bereitstellungs Ziel der APP identisch sein.</span><span class="sxs-lookup"><span data-stu-id="72d34-207">The `-DnsName` parameter value must match the deployment target of the app.</span></span> <span data-ttu-id="72d34-208">Beispiel: "localhost" für die Entwicklung.</span><span class="sxs-lookup"><span data-stu-id="72d34-208">For example, "localhost" for development.</span></span>

#### <a name="install-in-the-trusted-root"></a><span data-ttu-id="72d34-209">Installieren des vertrauenswürdigen Stamms</span><span class="sxs-lookup"><span data-stu-id="72d34-209">Install in the trusted root</span></span>

<span data-ttu-id="72d34-210">Das Stamm Zertifikat muss auf Ihrem Host System als vertrauenswürdig eingestuft werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-210">The root certificate needs to be trusted on your host system.</span></span> <span data-ttu-id="72d34-211">Ein Stamm Zertifikat, das nicht von einer Zertifizierungsstelle erstellt wurde, ist standardmäßig nicht vertrauenswürdig.</span><span class="sxs-lookup"><span data-stu-id="72d34-211">A root certificate which was not created by a certificate authority won't be trusted by default.</span></span> <span data-ttu-id="72d34-212">Der folgende Link erläutert, wie dies unter Windows erreicht werden kann:</span><span class="sxs-lookup"><span data-stu-id="72d34-212">The following link explains how this can be accomplished on Windows:</span></span>

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a><span data-ttu-id="72d34-213">Zwischenzertifikat</span><span class="sxs-lookup"><span data-stu-id="72d34-213">Intermediate certificate</span></span>

<span data-ttu-id="72d34-214">Ein zwischen Zertifikat kann nun aus dem Stamm Zertifikat erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-214">An intermediate certificate can now be created from the root certificate.</span></span> <span data-ttu-id="72d34-215">Dies ist für alle Anwendungsfälle nicht erforderlich, aber Sie müssen möglicherweise viele Zertifikate erstellen oder Gruppen von Zertifikaten aktivieren bzw. deaktivieren.</span><span class="sxs-lookup"><span data-stu-id="72d34-215">This isn't required for all use cases, but you might need to create many certificates or need to activate or disable groups of certificates.</span></span> <span data-ttu-id="72d34-216">Der `TextExtension`-Parameter ist erforderlich, um die Pfadlänge in den grundeinschränkungen des Zertifikats festzulegen.</span><span class="sxs-lookup"><span data-stu-id="72d34-216">The `TextExtension` parameter is required to set the path length in the basic constraints of the certificate.</span></span>

<span data-ttu-id="72d34-217">Das zwischen Zertifikat kann dann dem vertrauenswürdigen zwischen Zertifikat im Windows-Host System hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-217">The intermediate certificate can then be added to the trusted intermediate certificate in the Windows host system.</span></span>

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a><span data-ttu-id="72d34-218">Untergeordnetes Zertifikat aus zwischen Zertifikat erstellen</span><span class="sxs-lookup"><span data-stu-id="72d34-218">Create child certificate from intermediate certificate</span></span>

<span data-ttu-id="72d34-219">Ein untergeordnetes Zertifikat kann aus dem zwischen Zertifikat erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-219">A child certificate can be created from the intermediate certificate.</span></span> <span data-ttu-id="72d34-220">Dies ist die Endentität, und es müssen nicht mehr untergeordnete Zertifikate erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-220">This is the end entity and doesn't need to create more child certificates.</span></span>

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a><span data-ttu-id="72d34-221">Erstellen des untergeordneten Zertifikats aus dem Stamm Zertifikat</span><span class="sxs-lookup"><span data-stu-id="72d34-221">Create child certificate from root certificate</span></span>

<span data-ttu-id="72d34-222">Ein untergeordnetes Zertifikat kann auch direkt aus dem Stamm Zertifikat erstellt werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-222">A child certificate can also be created from the root certificate directly.</span></span> 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a><span data-ttu-id="72d34-223">Beispiel für ein root-zwischen Zertifikat-Zertifikat</span><span class="sxs-lookup"><span data-stu-id="72d34-223">Example root - intermediate certificate - certificate</span></span>

```powershell
$mypwdroot = ConvertTo-SecureString -String "1234" -Force -AsPlainText
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

Get-ChildItem -Path cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwdroot

Export-Certificate -Cert cert:\localMachine\my\0C89639E4E2998A93E423F919B36D4009A0F9991 -FilePath root_ca_dev_damienbod.crt

$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\0C89639E4E2998A93E423F919B36D4009A0F9991 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\BA9BF91ED35538A01375EFC212A2F46104B33A44 -FilePath child_a_dev_damienbod.crt

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\BA9BF91ED35538A01375EFC212A2F46104B33A44 )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_b_from_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_b_from_a_dev_damienbod.com" 

Get-ChildItem -Path cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_b_from_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\141594A0AE38CBBECED7AF680F7945CD51D8F28A -FilePath child_b_from_a_dev_damienbod.crt
```

<span data-ttu-id="72d34-224">Wenn Sie die Stamm-, zwischen-oder untergeordneten Zertifikate verwenden, können die Zertifikate nach Bedarf mithilfe des Fingerabdrucks oder PublicKey überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="72d34-224">When using the root, intermediate, or child certificates, the certificates can be validated using the Thumbprint or PublicKey as required.</span></span>

```csharp
using System.Collections.Generic;
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService 
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            return CheckIfThumbprintIsValid(clientCertificate);
        }

        private bool CheckIfThumbprintIsValid(X509Certificate2 clientCertificate)
        {
            var listOfValidThumbprints = new List<string>
            {
                "141594A0AE38CBBECED7AF680F7945CD51D8F28A",
                "0C89639E4E2998A93E423F919B36D4009A0F9991",
                "BA9BF91ED35538A01375EFC212A2F46104B33A44"
            };

            if (listOfValidThumbprints.Contains(clientCertificate.Thumbprint))
            {
                return true;
            }

            return false;
        }
    }
}
```
