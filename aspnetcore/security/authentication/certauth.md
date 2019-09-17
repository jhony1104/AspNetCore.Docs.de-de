---
title: Konfigurieren der Zertifikat Authentifizierung in ASP.net Core
author: blowdart
description: Erfahren Sie, wie Sie die Zertifikat Authentifizierung in ASP.net Core für IIS und http. sys konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 08/19/2019
uid: security/authentication/certauth
ms.openlocfilehash: bb375cf380175daf2399f3b56f543819ee5692b8
ms.sourcegitcommit: 07cd66e367d080acb201c7296809541599c947d1
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71039241"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Konfigurieren der Zertifikat Authentifizierung in ASP.net Core

`Microsoft.AspNetCore.Authentication.Certificate`enthält eine-Implementierung, die der [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) für ASP.net Core ähnelt. Die Zertifikat Authentifizierung erfolgt auf TLS-Ebene, lange vor der ASP.net Core. Genauer ist dies ein Authentifizierungs Handler, der das Zertifikat überprüft und dann ein Ereignis liefert, bei dem Sie dieses Zertifikat in eine `ClaimsPrincipal`auflösen können. 

[Konfigurieren Sie Ihren Host für die](#configure-your-host-to-require-certificates) Zertifikat Authentifizierung, also IIS, Kestrel, Azure-Web-Apps oder andere Benutzer, die Sie verwenden.

## <a name="proxy-and-load-balancer-scenarios"></a>Proxy-und Load Balancer-Szenarios

Die Zertifikat Authentifizierung ist ein Zustands behaftetes Szenario, das hauptsächlich verwendet wird, wenn ein Proxy oder ein Load Balancer den Datenverkehr zwischen Clients und Servern nicht Wenn ein Proxy oder ein Lasten Ausgleichs Modul verwendet wird, funktioniert die Zertifikat Authentifizierung nur, wenn der Proxy oder der Load Balancer:

* Behandelt die-Authentifizierung.
* Übergibt die Benutzer Authentifizierungsinformationen an die APP (z. b. in einem Anforderungs Header), die die Authentifizierungsinformationen verarbeitet.

Eine Alternative zur Zertifikat Authentifizierung in Umgebungen, in denen Proxys und Lasten Ausgleichs Module verwendet werden, ist Active Directory Verbund Dienste (AD FS) mit OpenID Connect (oidc).

## <a name="get-started"></a>Erste Schritte

Erwerben Sie ein HTTPS-Zertifikat, wenden Sie es an, und [Konfigurieren Sie den Host](#configure-your-host-to-require-certificates) , um Zertifikate anzufordern.

Fügen Sie in Ihrer Web-App einen Verweis auf `Microsoft.AspNetCore.Authentication.Certificate` das Paket hinzu. Verwenden Sie dann `Startup.Configure` in der- `app.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).UseCertificateAuthentication(...);` Methode mit den Optionen, und geben Sie `OnCertificateValidated` einen Delegaten für an, um eine ergänzende Validierung für das mit Anforderungen gesendete Client Zertifikat durchzuführen. Diese Informationen werden in ein `ClaimsPrincipal` -Objekt umgewandelt und für `context.Principal` die-Eigenschaft festgelegt.

Wenn die Authentifizierung fehlschlägt, gibt dieser `403 (Forbidden)` Handler wie erwartet `401 (Unauthorized)`eine Antwort zurück. Der Grund dafür ist, dass die Authentifizierung während der anfänglichen TLS-Verbindung stattfinden soll. Bis zum Zeitpunkt, an dem der Handler erreicht wird, ist es zu spät. Es gibt keine Möglichkeit, die Verbindung von einer anonymen Verbindung mit einem Zertifikat zu aktualisieren.

Fügen Sie `app.UseAuthentication();` außerdem die `Startup.Configure` -Methode hinzu. Andernfalls wird HttpContext. User nicht auf `ClaimsPrincipal` aus dem Zertifikat erstellt festgelegt. Beispiel:

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

Im vorangehenden Beispiel wird die Standardmethode zum Hinzufügen der Zertifikat Authentifizierung veranschaulicht. Der Handler erstellt einen Benutzer Prinzipal mithilfe der allgemeinen Zertifikat Eigenschaften.

## <a name="configure-certificate-validation"></a>Konfigurieren der Zertifikat Überprüfung

Der `CertificateAuthenticationOptions` Handler verfügt über einige integrierte Validierungen, bei denen es sich um die minimalen Überprüfungen handelt, die Sie für ein Zertifikat ausführen sollten. Jede dieser Einstellungen ist standardmäßig aktiviert.

### <a name="allowedcertificatetypes--chained-selfsigned-or-all-chained--selfsigned"></a>"Verkewedcertifipeetypes = verkettet", "selfsigned" oder "alle" (verkettet | Selfsigned)

Mit dieser Überprüfung wird überprüft, ob nur der geeignete Zertifikattyp zulässig ist.

### <a name="validatecertificateuse"></a>ValidateCertificateUse

Mit dieser Überprüfung wird überprüft, ob das vom Client vorgelegte Zertifikat über die erweiterte Schlüssel Verwendung (EKU) der Client Authentifizierung oder über keine EKUs verfügt. Wie bei den Spezifikationen gesagt, werden alle EKUs als gültig eingestuft, wenn kein EKU angegeben wird.

### <a name="validatevalidityperiod"></a>Validatevalidityperiod

Diese Prüfung überprüft, ob das Zertifikat innerhalb seines Gültigkeits Zeitraums liegt. Bei jeder Anforderung stellt der Handler sicher, dass ein Zertifikat, das bei der Präsentation gültig war, während der aktuellen Sitzung nicht abgelaufen ist.

### <a name="revocationflag"></a>RevocationFlag

Ein Flag, das angibt, welche Zertifikate in der Kette auf die Sperrung geprüft werden.

Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.

### <a name="revocationmode"></a>RevocationMode

Ein Flag, das angibt, wie Sperr Überprüfungen durchgeführt werden.

Das Angeben einer Online Überprüfung kann zu einer langen Verzögerung führen, während die Zertifizierungsstelle kontaktiert wird.

Sperr Überprüfungen werden nur ausgeführt, wenn das Zertifikat mit einem Stamm Zertifikat verkettet ist.

### <a name="can-i-configure-my-app-to-require-a-certificate-only-on-certain-paths"></a>Kann ich meine APP so konfigurieren, dass ein Zertifikat nur für bestimmte Pfade erforderlich ist?

Dies ist nicht möglich. Merken Sie sich, dass der Zertifikat Austausch durchgeführt wurde, bis der Start der HTTPS-Konversation durch den Server erfolgt ist, bevor die erste Anforderung über diese Verbindung empfangen wird. Daher ist es nicht möglich, den Bereich auf der Grundlage von Anforderungs Feldern festzustellen.

## <a name="handler-events"></a>Handlerereignisse

Der Handler hat zwei Ereignisse:

* `OnAuthenticationFailed`&ndash; Wird aufgerufen, wenn während der Authentifizierung eine Ausnahme auftritt und Sie reagieren können.
* `OnCertificateValidated`&ndash; Wird aufgerufen, nachdem das Zertifikat überprüft wurde, die Überprüfung bestanden und ein Standard Prinzipal erstellt wurde. Dieses Ereignis ermöglicht es Ihnen, ihre eigene Validierung auszuführen und den Prinzipal zu erweitern oder zu ersetzen. Beispiele hierfür sind:
  * Ermitteln, ob das Zertifikat den Diensten bekannt ist.
  * Erstellen eines eigenen Prinzipals. Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:

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

Wenn Sie feststellen, dass das eingehende Zertifikat die zusätzliche über `context.Fail("failure reason")` Prüfung nicht erfüllt, können Sie mit einem Fehler Grund anrufen.

Für echte Funktionen möchten Sie wahrscheinlich einen Dienst aufzurufen, der in der Abhängigkeitsinjektion registriert ist, der eine Verbindung mit einer Datenbank oder einem anderen Benutzerspeicher herstellt. Greifen Sie auf den Dienst zu, indem Sie den Kontext verwenden, der in ihren Delegaten Betrachten Sie das folgende Beispiel in `Startup.ConfigureServices`:

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

Konzeptionell ist die Validierung des Zertifikats ein Autorisierungs Problem. Das Hinzufügen einer Überprüfung (z. b. eines Ausstellers oder Fingerabdrucks in einer Autorisierungs Richtlinie) und nicht innerhalb `OnCertificateValidated`von ist durchaus akzeptabel.

## <a name="configure-your-host-to-require-certificates"></a>Konfigurieren des Hosts, damit Zertifikate erforderlich sind

### <a name="kestrel"></a>Kestrel

Konfigurieren Sie in *Program.cs*Kestrel wie folgt:

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

Führen Sie im IIS-Manager die folgenden Schritte aus:

1. Wählen Sie auf der Registerkarte **Verbindungen** Ihre Website aus.
1. Doppelklicken Sie im Fenster " **Featureansicht** " auf die Option " **SSL-Einstellungen** ".
1. Aktivieren Sie das Kontrollkästchen **SSL erforderlich** , und aktivieren Sie im Abschnitt **Client Zertifikate** das Optionsfeld **erforderlich** .

![Client Zertifikat Einstellungen in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure und benutzerdefinierte Webproxys

Informationen zum Konfigurieren der Middleware für die Zertifikat Weiterleitung finden Sie in der [Dokumentation zu Host und](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) Bereitstellung.
