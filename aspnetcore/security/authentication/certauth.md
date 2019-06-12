---
title: Konfigurieren Sie zertifikatbasierte Authentifizierung in ASP.NET Core
author: blowdart
description: Informationen Sie zum Konfigurieren der Zertifikatauthentifizierung in ASP.NET Core für IIS und HTTP.sys.
monikerRange: '>= aspnetcore-3.0'
ms.author: barry.dorrans
ms.date: 06/11/2019
uid: security/authentication/certauth
ms.openlocfilehash: 37567128531187bfe7dd6c9f5aa990226e70f35f
ms.sourcegitcommit: 1bb3f3f1905b4e7d4ca1b314f2ce6ee5dd8be75f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2019
ms.locfileid: "66837537"
---
# <a name="overview"></a>Übersicht

`Microsoft.AspNetCore.Authentication.Certificate` enthält eine Implementierung, die ähnlich wie [Zertifikatauthentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) für ASP.NET Core. Zertifikatauthentifizierung erfolgt auf lange TLS-Ebene, bevor es je zu ASP.NET Core wird. Genauer gesagt, dies ist ein authentifizierungshandler, die das Zertifikat überprüft und gibt Ihnen danach ein Ereignis, in dem Sie das Zertifikat zu beheben können, eine `ClaimsPrincipal`. 

[Konfigurieren Sie Ihren Host](#configure-your-host-to-require-certificates) für die Zertifikatauthentifizierung, sei es IIS, Kestrel, Azure-Web-Apps oder was auch immer Sie verwenden.

## <a name="get-started"></a>Erste Schritte

Ein HTTPS-Zertifikat erwerben, wenden Sie es, und [konfigurieren Sie Ihren Host](#configure-your-host-to-require-certificates) auf Zertifikate erforderlich sind.

Fügen Sie in Ihrer Web-app einen Verweis auf die `Microsoft.AspNetCore.Authentication.Certificate` Paket. Klicken Sie dann in der `Startup.Configure` -Methode, rufen `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` über Ihre weiteren Möglichkeiten, einen Delegaten für die Bereitstellung von `OnCertificateValidated` Sie keine zusätzliche Überprüfung des Clientzertifikats mit Anforderungen gesendet. Aktivieren Sie die Daten in einem `ClaimsPrincipal` und legen Sie es auf die `context.Principal` Eigenschaft.

Wenn Authentifizierung fehlschlägt, wird dieser Handler gibt eine `403 (Forbidden)` Antwort eher eine `401 (Unauthorized)`, wie zu erwarten. Der Grund dafür ist, dass die Authentifizierung während der anfänglichen TLS-Verbindung erfolgen soll. Durch die Zeit, die sie den Handler erreicht, ist es zu spät. Es gibt keine Möglichkeit die Verbindung über eine anonyme Verbindung auf eine mit einem Zertifikat zu aktualisieren.

Fügen Sie auch `app.UseAuthentication();` in die `Startup.Configure` Methode. Andernfalls die HttpContext.User wird nicht festgelegt, um `ClaimsPrincipal` aus dem Zertifikat erstellt. Zum Beispiel:

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

Das vorausgehende Beispiel veranschaulicht die Standardmethode zum Hinzufügen der Authentifizierung per Clientzertifikat. Der Handler erstellt einen Benutzerprinzipal, der über die allgemeinen Zertifikateigenschaften.

## <a name="configure-certificate-validation"></a>Konfigurieren der Überprüfung des Zertifikats

Die `CertificateAuthenticationOptions` Handler verfügt über einige integrierte Überprüfungen, die die minimale Überprüfungen wurden, sollten Sie für ein Zertifikat ausführen. Jede dieser Einstellungen ist standardmäßig aktiviert.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>AllowedCertificateTypes = verkettet, SelfSigned oder allen (verketteten | SelfSigned)

Diese Überprüfung wird überprüft, dass nur der entsprechende Zertifikatstyp zulässig ist.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Diese Überprüfung überprüft, ob das vom Client vorgelegte Zertifikat die Clientauthentifizierung, die erweiterte Schlüssel verwenden (EKU) oder keine EKUs zu hat. Wenn die Spezifikationen beispielsweise, wenn keine EKU angegeben wird, und klicken Sie dann alle EKUs als gültig erachtet werden.

### <a name="validatevalidityperiod"></a>ValidateValidityPeriod

Diese Überprüfung überprüft, dass das Zertifikat in seinem Gültigkeitszeitraum liegen. Bei jeder Anforderung wird der Handler für sichergestellt, dass ein Zertifikat, das war ungültig, wenn es angezeigt wurde nicht während der aktuellen Sitzung abgelaufen ist.

### <a name="revocationflag"></a>RevocationFlag

Ein Flag, das angibt, welche Zertifikate in der Kette auf Sperrungen überprüft werden.

Sperrprüfungen werden nur durchgeführt, wenn das Zertifikat mit einem Stammzertifikat verkettet ist.

### <a name="revocationmode"></a>RevocationMode

Ein Flag, das angibt, wie sperrüberprüfungen ausgeführt werden.

Eine onlineüberprüfung Angabe kann in einer langen Verzögerung führen, während die Verbindung mit die Zertifizierungsstelle hergestellt wird.

Sperrprüfungen werden nur durchgeführt, wenn das Zertifikat mit einem Stammzertifikat verkettet ist.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Kann ich meine app aus, um ein Zertifikat nur für bestimmte Pfade erfordern konfigurieren?

Dies ist nicht möglich. Denken Sie daran, dass der zertifikataustausch erfolgt ist, dass der Start der Konversation HTTPS, es vom Server abgeschlossen ist, bevor die erste Anforderung für die Verbindung empfangen wird, daher es nicht möglich, den Umfang je nach Anforderung Felder ist.

## <a name="handler-events"></a>Handler-Ereignisse

Der Handler verfügt über zwei Ereignisse:

* `OnAuthenticationFailed` &ndash; Wird aufgerufen, wenn eine Ausnahme während der Authentifizierung erfolgt und ermöglicht es Ihnen, zu reagieren.
* `OnCertificateValidated` &ndash; Wird aufgerufen, nachdem das Zertifikat überprüft wurde, war erfolgreich und ein Standard-Prinzipals erstellt wurde. Mit diesem Ereignis können Sie eine eigene Überprüfung durchführen und zu erweitern oder Ersetzen Sie den Prinzipal. Beispiele umfassen:
  * Bestimmen, ob das Zertifikat für Ihre Dienste bekannt ist.
  * Erstellen Ihre eigenen dienstprinzipals. Im folgenden Beispiel in `Startup.ConfigureServices`:

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

Wenn Sie das eingehende Zertifikat erfüllt nicht die zusätzliche Überprüfung finden, rufen Sie `context.Fail("failure reason")` mit einem Fehlergrund für.

Für real Funktionalität, Sie sollten zum Aufrufen eines Diensts in Abhängigkeitsinjektion die Verbindung mit einer Datenbank oder andere Art von Speicher des Benutzers registriert. Zugriff auf Ihren Dienst unter Verwendung des Kontexts an Ihren Delegaten übergeben. Im folgenden Beispiel in `Startup.ConfigureServices`:

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

Grundsätzlich ist die Überprüfung des Zertifikats ein Aspekt Autorisierung. Eine Überprüfung auf, z. B. hinzufügen, Aussteller oder Fingerabdruck in einer Autorisierungsrichtlinie, anstatt in `OnCertificateValidated`, ist durchaus akzeptabel.

## <a name="configure-your-host-to-require-certificates"></a>Konfigurieren Sie Ihren Host für Zertifikate erforderlich sind

### <a name="kestrel"></a>Kestrel

In *"Program.cs"* , konfigurieren Sie Kestrel wie folgt:

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

### <a name="iis"></a>IIS

Führen Sie die folgenden Schritte im IIS-Manager:

1. Wählen Sie Ihre Website aus der **Verbindungen** Registerkarte.
1. Doppelklicken Sie auf die **SSL-Einstellungen** option die **Ansicht "Features"** Fenster.
1. Überprüfen der **SSL erforderlich** Kontrollkästchen, und wählen Sie die **erfordern** Optionsfeld in der **Clientzertifikate** Abschnitt.

![Einstellungen für Client-Zertifikat in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure und benutzerdefinierte Web-Proxys

Finden Sie unter den [hosten und Bereitstellen von Dokumentation](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) Informationen zum Konfigurieren des verwaltungszertifikats Middleware weitergeleitet.
