---
title: Konfigurieren Sie zertifikatbasierte Authentifizierung in ASP.NET Core
author: blowdart
description: Informationen Sie zum Konfigurieren der Zertifikatauthentifizierung in ASP.NET Core für IIS und HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 8609c58265340da1d618135795915d6c49e750a3
ms.sourcegitcommit: 0b9e767a09beaaaa4301915cdda9ef69daaf3ff2
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538722"
---
# <a name="overview"></a><span data-ttu-id="81afa-103">Übersicht</span><span class="sxs-lookup"><span data-stu-id="81afa-103">Overview</span></span>

<span data-ttu-id="81afa-104">`Microsoft.AspNetCore.Authentication.Certificate` enthält eine Implementierung, die ähnlich wie [Zertifikatauthentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) für ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="81afa-104">`Microsoft.AspNetCore.Authentication.Certificate` contains an implementation similar to [Certificate Authentication](https://tools.ietf.org/html/rfc5246#section-7.4.4) for ASP.NET Core.</span></span> <span data-ttu-id="81afa-105">Zertifikatauthentifizierung erfolgt auf lange TLS-Ebene, bevor es je zu ASP.NET Core wird.</span><span class="sxs-lookup"><span data-stu-id="81afa-105">Certificate authentication happens at the TLS level, long before it ever gets to ASP.NET Core.</span></span> <span data-ttu-id="81afa-106">Genauer gesagt, dies ist ein authentifizierungshandler, die das Zertifikat überprüft und gibt Ihnen danach ein Ereignis, in dem Sie das Zertifikat zu beheben können, eine `ClaimsPrincipal`.</span><span class="sxs-lookup"><span data-stu-id="81afa-106">More accurately, this is an authentication handler that validates the certificate and then gives you an event where you can resolve that certificate to a `ClaimsPrincipal`.</span></span> 

<span data-ttu-id="81afa-107">[Konfigurieren Sie Ihren Host](#configure-your-host-to-require-certificates) für die Zertifikatauthentifizierung, sei es IIS, Kestrel, Azure-Web-Apps oder was auch immer Sie verwenden.</span><span class="sxs-lookup"><span data-stu-id="81afa-107">[Configure your host](#configure-your-host-to-require-certificates) for certificate authentication, be it IIS, Kestrel, Azure Web Apps, or whatever else you're using.</span></span>

## <a name="get-started"></a><span data-ttu-id="81afa-108">Erste Schritte</span><span class="sxs-lookup"><span data-stu-id="81afa-108">Get started</span></span>

<span data-ttu-id="81afa-109">Ein HTTPS-Zertifikat erwerben, wenden Sie es, und [konfigurieren Sie Ihren Host](#configure-your-host-to-require-certificates) auf Zertifikate erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="81afa-109">Acquire an HTTPS certificate, apply it, and [configure your host](#configure-your-host-to-require-certificates) to require certificates.</span></span>

<span data-ttu-id="81afa-110">Fügen Sie in Ihrer Web-app einen Verweis auf die `Microsoft.AspNetCore.Authentication.Certificate` Paket.</span><span class="sxs-lookup"><span data-stu-id="81afa-110">In your web app, add a reference to the `Microsoft.AspNetCore.Authentication.Certificate` package.</span></span> <span data-ttu-id="81afa-111">Klicken Sie dann in der `Startup.Configure` -Methode, rufen `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` über Ihre weiteren Möglichkeiten, einen Delegaten für die Bereitstellung von `OnCertificateValidated` Sie keine zusätzliche Überprüfung des Clientzertifikats mit Anforderungen gesendet.</span><span class="sxs-lookup"><span data-stu-id="81afa-111">Then in the `Startup.Configure` method, call `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` with your options, providing a delegate for `OnCertificateValidated` to do any supplementary validation on the client certificate sent with requests.</span></span> <span data-ttu-id="81afa-112">Aktivieren Sie die Daten in einem `ClaimsPrincipal` und legen Sie es auf die `context.Principal` Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="81afa-112">Turn that information into a `ClaimsPrincipal` and set it on the `context.Principal` property.</span></span>

<span data-ttu-id="81afa-113">Wenn Authentifizierung fehlschlägt, wird dieser Handler gibt eine `403 (Forbidden)` Antwort eher eine `401 (Unauthorized)`, wie zu erwarten.</span><span class="sxs-lookup"><span data-stu-id="81afa-113">If authentication fails, this handler returns a `403 (Forbidden)` response rather a `401 (Unauthorized)`, as you might expect.</span></span> <span data-ttu-id="81afa-114">Der Grund dafür ist, dass die Authentifizierung während der anfänglichen TLS-Verbindung erfolgen soll.</span><span class="sxs-lookup"><span data-stu-id="81afa-114">The reasoning is that the authentication should happen during the initial TLS connection.</span></span> <span data-ttu-id="81afa-115">Durch die Zeit, die sie den Handler erreicht, ist es zu spät.</span><span class="sxs-lookup"><span data-stu-id="81afa-115">By the time it reaches the handler, it's too late.</span></span> <span data-ttu-id="81afa-116">Es gibt keine Möglichkeit die Verbindung über eine anonyme Verbindung auf eine mit einem Zertifikat zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="81afa-116">There's no way to upgrade the connection from an anonymous connection to one with a certificate.</span></span>

<span data-ttu-id="81afa-117">Fügen Sie auch `app.UseAuthentication();` in die `Startup.Configure` Methode.</span><span class="sxs-lookup"><span data-stu-id="81afa-117">Also add `app.UseAuthentication();` in the `Startup.Configure` method.</span></span> <span data-ttu-id="81afa-118">Andernfalls die HttpContext.User wird nicht festgelegt, um `ClaimsPrincipal` aus dem Zertifikat erstellt.</span><span class="sxs-lookup"><span data-stu-id="81afa-118">Otherwise, the HttpContext.User will not be set to `ClaimsPrincipal` created from the certificate.</span></span> <span data-ttu-id="81afa-119">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="81afa-119">For example:</span></span>

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

<span data-ttu-id="81afa-120">Das vorausgehende Beispiel veranschaulicht die Standardmethode zum Hinzufügen der Authentifizierung per Clientzertifikat.</span><span class="sxs-lookup"><span data-stu-id="81afa-120">The preceding example demonstrates the default way to add certificate authentication.</span></span> <span data-ttu-id="81afa-121">Der Handler erstellt einen Benutzerprinzipal, der über die allgemeinen Zertifikateigenschaften.</span><span class="sxs-lookup"><span data-stu-id="81afa-121">The handler constructs a user principal using the common certificate properties.</span></span>

## <a name="configure-certificate-validation"></a><span data-ttu-id="81afa-122">Konfigurieren der Überprüfung des Zertifikats</span><span class="sxs-lookup"><span data-stu-id="81afa-122">Configure certificate validation</span></span>

<span data-ttu-id="81afa-123">Die `CertificateAuthenticationOptions` Handler verfügt über einige integrierte Überprüfungen, die die minimale Überprüfungen wurden, sollten Sie für ein Zertifikat ausführen.</span><span class="sxs-lookup"><span data-stu-id="81afa-123">The `CertificateAuthenticationOptions` handler has some built-in validations that are the minimum validations you should perform on a certificate.</span></span> <span data-ttu-id="81afa-124">Jede dieser Einstellungen ist standardmäßig aktiviert.</span><span class="sxs-lookup"><span data-stu-id="81afa-124">Each of these settings is enabled by default.</span></span>

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a><span data-ttu-id="81afa-125">AllowedCertificateTypes = verkettet, SelfSigned oder allen (verketteten | SelfSigned)</span><span class="sxs-lookup"><span data-stu-id="81afa-125">AllowedCertificateTypes = Chained, SelfSigned, or All (Chained | SelfSigned)</span></span>

<span data-ttu-id="81afa-126">Diese Überprüfung wird überprüft, dass nur der entsprechende Zertifikatstyp zulässig ist.</span><span class="sxs-lookup"><span data-stu-id="81afa-126">This check validates that only the appropriate certificate type is allowed.</span></span>

### <a name="validatecertificateuse"></a><span data-ttu-id="81afa-127">ValidateCertificateUse</span><span class="sxs-lookup"><span data-stu-id="81afa-127">ValidateCertificateUse</span></span>

<span data-ttu-id="81afa-128">Diese Überprüfung überprüft, ob das vom Client vorgelegte Zertifikat die Clientauthentifizierung, die erweiterte Schlüssel verwenden (EKU) oder keine EKUs zu hat.</span><span class="sxs-lookup"><span data-stu-id="81afa-128">This check validates that the certificate presented by the client has the Client Authentication extended key use (EKU), or no EKUs at all.</span></span> <span data-ttu-id="81afa-129">Wenn die Spezifikationen beispielsweise, wenn keine EKU angegeben wird, und klicken Sie dann alle EKUs als gültig erachtet werden.</span><span class="sxs-lookup"><span data-stu-id="81afa-129">As the specifications say, if no EKU is specified, then all EKUs are deemed valid.</span></span>

### <a name="validatevalidityperiod"></a><span data-ttu-id="81afa-130">ValidateValidityPeriod</span><span class="sxs-lookup"><span data-stu-id="81afa-130">ValidateValidityPeriod</span></span>

<span data-ttu-id="81afa-131">Diese Überprüfung überprüft, dass das Zertifikat in seinem Gültigkeitszeitraum liegen.</span><span class="sxs-lookup"><span data-stu-id="81afa-131">This check validates that the certificate is within its validity period.</span></span> <span data-ttu-id="81afa-132">Bei jeder Anforderung wird der Handler für sichergestellt, dass ein Zertifikat, das war ungültig, wenn es angezeigt wurde nicht während der aktuellen Sitzung abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="81afa-132">On each request, the handler ensures that a certificate that was valid when it was presented hasn't expired during its current session.</span></span>

### <a name="revocationflag"></a><span data-ttu-id="81afa-133">RevocationFlag</span><span class="sxs-lookup"><span data-stu-id="81afa-133">RevocationFlag</span></span>

<span data-ttu-id="81afa-134">Ein Flag, das angibt, welche Zertifikate in der Kette auf Sperrungen überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="81afa-134">A flag that specifies which certificates in the chain are checked for revocation.</span></span>

<span data-ttu-id="81afa-135">Sperrprüfungen werden nur durchgeführt, wenn das Zertifikat mit einem Stammzertifikat verkettet ist.</span><span class="sxs-lookup"><span data-stu-id="81afa-135">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="revocationmode"></a><span data-ttu-id="81afa-136">RevocationMode</span><span class="sxs-lookup"><span data-stu-id="81afa-136">RevocationMode</span></span>

<span data-ttu-id="81afa-137">Ein Flag, das angibt, wie sperrüberprüfungen ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="81afa-137">A flag that specifies how revocation checks are performed.</span></span>

<span data-ttu-id="81afa-138">Eine onlineüberprüfung Angabe kann in einer langen Verzögerung führen, während die Verbindung mit die Zertifizierungsstelle hergestellt wird.</span><span class="sxs-lookup"><span data-stu-id="81afa-138">Specifying an online check can result in a long delay while the certificate authority is contacted.</span></span>

<span data-ttu-id="81afa-139">Sperrprüfungen werden nur durchgeführt, wenn das Zertifikat mit einem Stammzertifikat verkettet ist.</span><span class="sxs-lookup"><span data-stu-id="81afa-139">Revocation checks are only performed when the certificate is chained to a root certificate.</span></span>

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a><span data-ttu-id="81afa-140">Kann ich meine app aus, um ein Zertifikat nur für bestimmte Pfade erfordern konfigurieren?</span><span class="sxs-lookup"><span data-stu-id="81afa-140">Can I configure my app to require a certificate only on certain paths?</span></span>

<span data-ttu-id="81afa-141">Dies ist nicht möglich.</span><span class="sxs-lookup"><span data-stu-id="81afa-141">This isn't possible.</span></span> <span data-ttu-id="81afa-142">Denken Sie daran, dass der zertifikataustausch erfolgt ist, dass der Start der Konversation HTTPS, es vom Server abgeschlossen ist, bevor die erste Anforderung für die Verbindung empfangen wird, daher es nicht möglich, den Umfang je nach Anforderung Felder ist.</span><span class="sxs-lookup"><span data-stu-id="81afa-142">Remember the certificate exchange is done that the start of the HTTPS conversation, it's done by the server before the first request is received on that connection so it's not possible to scope based on any request fields.</span></span>

## <a name="handler-events"></a><span data-ttu-id="81afa-143">Handler-Ereignisse</span><span class="sxs-lookup"><span data-stu-id="81afa-143">Handler events</span></span>

<span data-ttu-id="81afa-144">Der Handler verfügt über zwei Ereignisse:</span><span class="sxs-lookup"><span data-stu-id="81afa-144">The handler has two events:</span></span>

* <span data-ttu-id="81afa-145">`OnAuthenticationFailed` &ndash; Wird aufgerufen, wenn eine Ausnahme während der Authentifizierung erfolgt und ermöglicht es Ihnen, zu reagieren.</span><span class="sxs-lookup"><span data-stu-id="81afa-145">`OnAuthenticationFailed` &ndash; Called if an exception happens during authentication and allows you to react.</span></span>
* <span data-ttu-id="81afa-146">`OnCertificateValidated` &ndash; Wird aufgerufen, nachdem das Zertifikat überprüft wurde, war erfolgreich und ein Standard-Prinzipals erstellt wurde.</span><span class="sxs-lookup"><span data-stu-id="81afa-146">`OnCertificateValidated` &ndash; Called after the certificate has been validated, passed validation and a default principal has been created.</span></span> <span data-ttu-id="81afa-147">Mit diesem Ereignis können Sie eine eigene Überprüfung durchführen und zu erweitern oder Ersetzen Sie den Prinzipal.</span><span class="sxs-lookup"><span data-stu-id="81afa-147">This event allows you to perform your own validation and augment or replace the principal.</span></span> <span data-ttu-id="81afa-148">Beispiele umfassen:</span><span class="sxs-lookup"><span data-stu-id="81afa-148">For examples include:</span></span>
  * <span data-ttu-id="81afa-149">Bestimmen, ob das Zertifikat für Ihre Dienste bekannt ist.</span><span class="sxs-lookup"><span data-stu-id="81afa-149">Determining if the certificate is known to your services.</span></span>
  * <span data-ttu-id="81afa-150">Erstellen Ihre eigenen dienstprinzipals.</span><span class="sxs-lookup"><span data-stu-id="81afa-150">Constructing your own principal.</span></span> <span data-ttu-id="81afa-151">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="81afa-151">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="81afa-152">Wenn Sie das eingehende Zertifikat erfüllt nicht die zusätzliche Überprüfung finden, rufen Sie `context.Fail("failure reason")` mit einem Fehlergrund für.</span><span class="sxs-lookup"><span data-stu-id="81afa-152">If you find the inbound certificate doesn't meet your extra validation, call `context.Fail("failure reason")` with a failure reason.</span></span>

<span data-ttu-id="81afa-153">Für real Funktionalität, Sie sollten zum Aufrufen eines Diensts in Abhängigkeitsinjektion die Verbindung mit einer Datenbank oder andere Art von Speicher des Benutzers registriert.</span><span class="sxs-lookup"><span data-stu-id="81afa-153">For real functionality, you'll probably want to call a service registered in dependency injection that connects to a database or other type of user store.</span></span> <span data-ttu-id="81afa-154">Zugriff auf Ihren Dienst unter Verwendung des Kontexts an Ihren Delegaten übergeben.</span><span class="sxs-lookup"><span data-stu-id="81afa-154">Access your service by using the context passed into your delegate.</span></span> <span data-ttu-id="81afa-155">Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="81afa-155">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

<span data-ttu-id="81afa-156">Grundsätzlich ist die Überprüfung des Zertifikats ein Aspekt Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="81afa-156">Conceptually, the validation of the certificate is an authorization concern.</span></span> <span data-ttu-id="81afa-157">Eine Überprüfung auf, z. B. hinzufügen, Aussteller oder Fingerabdruck in einer Autorisierungsrichtlinie, anstatt in `OnCertificateValidated`, ist durchaus akzeptabel.</span><span class="sxs-lookup"><span data-stu-id="81afa-157">Adding a check on, for example, an issuer or thumbprint in an authorization policy, rather than inside `OnCertificateValidated`, is perfectly acceptable.</span></span>

## <a name="configure-your-host-to-require-certificates"></a><span data-ttu-id="81afa-158">Konfigurieren Sie Ihren Host für Zertifikate erforderlich sind</span><span class="sxs-lookup"><span data-stu-id="81afa-158">Configure your host to require certificates</span></span>

### <a name="kestrel"></a><span data-ttu-id="81afa-159">Kestrel</span><span class="sxs-lookup"><span data-stu-id="81afa-159">Kestrel</span></span>

<span data-ttu-id="81afa-160">In *"Program.cs"* , konfigurieren Sie Kestrel wie folgt:</span><span class="sxs-lookup"><span data-stu-id="81afa-160">In *Program.cs*, configure Kestrel as follows:</span></span>

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

### <a name="iis"></a><span data-ttu-id="81afa-161">IIS</span><span class="sxs-lookup"><span data-stu-id="81afa-161">IIS</span></span>

<span data-ttu-id="81afa-162">Führen Sie die folgenden Schritte im IIS-Manager:</span><span class="sxs-lookup"><span data-stu-id="81afa-162">Complete the following steps In IIS Manager:</span></span>

1. <span data-ttu-id="81afa-163">Wählen Sie Ihre Website aus der **Verbindungen** Registerkarte.</span><span class="sxs-lookup"><span data-stu-id="81afa-163">Select your site from the **Connections** tab.</span></span>
1. <span data-ttu-id="81afa-164">Doppelklicken Sie auf die **SSL-Einstellungen** option die **Ansicht "Features"** Fenster.</span><span class="sxs-lookup"><span data-stu-id="81afa-164">Double-click the **SSL Settings** option in the **Features View** window.</span></span>
1. <span data-ttu-id="81afa-165">Überprüfen der **SSL erforderlich** Kontrollkästchen, und wählen Sie die **erfordern** Optionsfeld in der **Clientzertifikate** Abschnitt.</span><span class="sxs-lookup"><span data-stu-id="81afa-165">Check the **Require SSL** checkbox, and select the **Require** radio button in the **Client certificates** section.</span></span>

![Einstellungen für Client-Zertifikat in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a><span data-ttu-id="81afa-167">Azure und benutzerdefinierte Web-Proxys</span><span class="sxs-lookup"><span data-stu-id="81afa-167">Azure and custom web proxies</span></span>

<span data-ttu-id="81afa-168">Finden Sie unter den [hosten und Bereitstellen von Dokumentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) Informationen zum Konfigurieren des verwaltungszertifikats Middleware weitergeleitet.</span><span class="sxs-lookup"><span data-stu-id="81afa-168">See the [host and deploy documentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) for how to configure the certificate forwarding middleware.</span></span>
