---
title: Konfigurieren der Zertifikat Authentifizierung in ASP.net Core
author: blowdart
description: Erfahren Sie, wie Sie die Zertifikat Authentifizierung in ASP.net Core für IIS und http. sys konfigurieren.
monikerRange: '>= aspnetcore-3.0'
ms.author: bdorrans
ms.date: 11/14/2019
uid: security/authentication/certauth
ms.openlocfilehash: 2ed3e88adf3bdb7528f47492b6eb5792f99f20d8
ms.sourcegitcommit: f91d322f790123d41ec3271fa084ae20ed9f89a6
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155006"
---
# <a name="configure-certificate-authentication-in-aspnet-core"></a>Konfigurieren der Zertifikat Authentifizierung in ASP.net Core

`Microsoft.AspNetCore.Authentication.Certificate` enthält eine-Implementierung, die der [Zertifikat Authentifizierung](https://tools.ietf.org/html/rfc5246#section-7.4.4) für ASP.net Core ähnelt. Die Zertifikat Authentifizierung erfolgt auf TLS-Ebene, lange vor der ASP.net Core. Genauer ist dies ein Authentifizierungs Handler, der das Zertifikat überprüft und dann ein Ereignis liefert, bei dem Sie dieses Zertifikat in eine `ClaimsPrincipal`auflösen können. 

[Konfigurieren Sie Ihren Host für die](#configure-your-host-to-require-certificates) Zertifikat Authentifizierung, also IIS, Kestrel, Azure-Web-Apps oder andere Benutzer, die Sie verwenden.

## <a name="proxy-and-load-balancer-scenarios"></a>Proxy-und Load Balancer-Szenarios

Die Zertifikat Authentifizierung ist ein Zustands behaftetes Szenario, das hauptsächlich verwendet wird, wenn ein Proxy oder ein Load Balancer den Datenverkehr zwischen Clients und Servern nicht Wenn ein Proxy oder ein Lasten Ausgleichs Modul verwendet wird, funktioniert die Zertifikat Authentifizierung nur, wenn der Proxy oder der Load Balancer:

* Behandelt die-Authentifizierung.
* Übergibt die Benutzer Authentifizierungsinformationen an die APP (z. b. in einem Anforderungs Header), die die Authentifizierungsinformationen verarbeitet.

Eine Alternative zur Zertifikat Authentifizierung in Umgebungen, in denen Proxys und Lasten Ausgleichs Module verwendet werden, ist Active Directory Verbund Dienste (AD FS) mit OpenID Connect (oidc).

## <a name="get-started"></a>Erste Schritte

Erwerben Sie ein HTTPS-Zertifikat, wenden Sie es an, und [Konfigurieren Sie den Host](#configure-your-host-to-require-certificates) , um Zertifikate anzufordern.

Fügen Sie in Ihrer Web-App einen Verweis auf das `Microsoft.AspNetCore.Authentication.Certificate`-Paket hinzu. Nennen Sie dann in der `Startup.ConfigureServices`-Methode `services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate(...);` mit den Optionen, und geben Sie einen Delegaten für `OnCertificateValidated` an, um eine ergänzende Validierung für das mit Anforderungen gesendete Client Zertifikat durchzuführen. Wandeln Sie diese Informationen in eine `ClaimsPrincipal` um, und legen Sie Sie für die `context.Principal`-Eigenschaft fest.

Wenn bei der Authentifizierung ein Fehler auftritt, gibt dieser Handler wie erwartet eine `403 (Forbidden)` Antwort zurück, die `401 (Unauthorized)`. Der Grund dafür ist, dass die Authentifizierung während der anfänglichen TLS-Verbindung stattfinden soll. Bis zum Zeitpunkt, an dem der Handler erreicht wird, ist es zu spät. Es gibt keine Möglichkeit, die Verbindung von einer anonymen Verbindung mit einem Zertifikat zu aktualisieren.

Fügen Sie außerdem `app.UseAuthentication();` in der `Startup.Configure`-Methode hinzu. Andernfalls wird der `HttpContext.User` nicht auf `ClaimsPrincipal`, der aus dem Zertifikat erstellt wurde, festgelegt. Beispiel:

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

### <a name="validatecertificateuse"></a>Validatecertifi| euse

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

* `OnAuthenticationFailed` &ndash; aufgerufen, wenn eine Ausnahme während der Authentifizierung auftritt und es Ihnen ermöglicht, zu reagieren.
* `OnCertificateValidated` &ndash; aufgerufen, nachdem das Zertifikat überprüft wurde, wird die Überprüfung bestanden, und ein Standard Prinzipal wurde erstellt. Dieses Ereignis ermöglicht es Ihnen, ihre eigene Validierung auszuführen und den Prinzipal zu erweitern oder zu ersetzen. Beispiele hierfür sind:
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

Wenn Sie feststellen, dass das eingehende Zertifikat die zusätzliche Überprüfung nicht erfüllt, wenden Sie `context.Fail("failure reason")` mit einem Fehler Grund an.

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

Konzeptionell ist die Validierung des Zertifikats ein Autorisierungs Problem. Das Hinzufügen einer Überprüfung (z. b. eines Ausstellers oder Fingerabdrucks in einer Autorisierungs Richtlinie) und nicht innerhalb `OnCertificateValidated`ist durchaus akzeptabel.

## <a name="configure-your-host-to-require-certificates"></a>Konfigurieren des Hosts, damit Zertifikate erforderlich sind

### <a name="kestrel"></a>Kestrel

Konfigurieren Sie in *Program.cs*Kestrel wie folgt:

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
                        o.ConfigureHttpsDefaults(o => o.ClientCertificateMode = ClientCertificateMode.RequireCertificate);
                    });
                });
}
```

> [!NOTE]
> Endpunkte, die durch <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Aufrufen von erstellt wurden, **bevor** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> aufgerufen werden, werden nicht auf die Standardwerte angewendet

### <a name="iis"></a>IIS

Führen Sie im IIS-Manager die folgenden Schritte aus:

1. Wählen Sie auf der Registerkarte **Verbindungen** Ihre Website aus.
1. Doppelklicken Sie im Fenster " **Featureansicht** " auf die Option " **SSL-Einstellungen** ".
1. Aktivieren Sie das Kontrollkästchen **SSL erforderlich** , und aktivieren Sie im Abschnitt **Client Zertifikate** das Optionsfeld **erforderlich** .

![Client Zertifikat Einstellungen in IIS](README-IISConfig.png)

### <a name="azure-and-custom-web-proxies"></a>Azure und benutzerdefinierte Webproxys

Informationen zum Konfigurieren der Middleware für die Zertifikat Weiterleitung finden Sie in der [Dokumentation zu Host und](xref:host-and-deploy/proxy-load-balancer#certificate-forwarding) Bereitstellung.

### <a name="use-certificate-authentication-in-azure-web-apps"></a>Verwenden der Zertifikat Authentifizierung in Azure-Web-Apps

Die `AddCertificateForwarding`-Methode wird verwendet, um Folgendes anzugeben:

* Der Name des Client Headers.
* Wie das Zertifikat geladen werden soll (mit der `HeaderConverter`-Eigenschaft).

In Azure-Web-Apps wird das Zertifikat als benutzerdefinierter Anforderungs Header mit dem Namen "`X-ARR-ClientCert`" übermittelt. Konfigurieren Sie die Zertifikat Weiterleitung in `Startup.ConfigureServices`, um Sie zu verwenden:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...
    
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
        bytes[i / 2] = Convert.ToByte(hex.Substring(i, 2), 16);
    return bytes;
}
```

Die `Startup.Configure`-Methode fügt dann die Middleware hinzu. `UseCertificateForwarding` wird vor den Aufrufen von `UseAuthentication` und `UseAuthorization`aufgerufen:

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

Eine separate Klasse kann verwendet werden, um Validierungs Logik zu implementieren. Da in diesem Beispiel dasselbe selbst signierte Zertifikat verwendet wird, sollten Sie sicherstellen, dass nur Ihr Zertifikat verwendet werden kann. Überprüfen Sie, ob die Fingerabdrücke sowohl des Client Zertifikats als auch des Serverzertifikats Stimmen. andernfalls können alle Zertifikate verwendet werden, die für die Authentifizierung ausreichen. Dies wird in der `AddCertificate`-Methode verwendet. Sie können den Betreff oder den Aussteller hier auch überprüfen, wenn Sie zwischengeschaltete oder untergeordnete Zertifikate verwenden.

```csharp
using System.IO;
using System.Security.Cryptography.X509Certificates;

namespace AspNetCoreCertificateAuthApi
{
    public class MyCertificateValidationService
    {
        public bool ValidateCertificate(X509Certificate2 clientCertificate)
        {
            // Do not hardcode passwords in production code, use thumbprint or key vault
            var cert = new X509Certificate2(Path.Combine("sts_dev_cert.pfx"), "1234");
            if (clientCertificate.Thumbprint == cert.Thumbprint)
            {
                return true;
            }

            return false;
        }
    }
}
```

#### <a name="implement-an-httpclient-using-a-certificate"></a>Implementieren eines httpclient mithilfe eines Zertifikats

Der Web-API-Client verwendet eine `HttpClient`, die mithilfe einer `IHttpClientFactory` Instanz erstellt wurde. Dies bietet keine Möglichkeit, einen Handler für die `HttpClient`zu definieren. verwenden Sie daher eine `HttpRequestMessage`, um das Zertifikat dem `X-ARR-ClientCert`-Anforderungs Header hinzuzufügen. Das Zertifikat wird mithilfe der `GetRawCertDataString`-Methode als Zeichenfolge hinzugefügt. 

```csharp
private async Task<JsonDocument> GetApiDataAsync()
{
    try
    {
        // Do not hardcode passwords in production code, use thumbprint or key vault
        var cert = new X509Certificate2(Path.Combine(_environment.ContentRootPath, "sts_dev_cert.pfx"), "1234");

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

        throw new ApplicationException($"Status code: {response.StatusCode}, Error: {response.ReasonPhrase}");
    }
    catch (Exception e)
    {
        throw new ApplicationException($"Exception {e}");
    }
}
```

Wenn das richtige Zertifikat an den Server gesendet wird, werden die Daten zurückgegeben. Wenn kein Zertifikat oder falsches Zertifikat gesendet wird, wird ein HTTP 403-Statuscode zurückgegeben.

### <a name="create-certificates-in-powershell"></a>Erstellen von Zertifikaten in PowerShell

Das Erstellen der Zertifikate ist der schwierigste Teil beim Einrichten dieses Flows. Ein Stamm Zertifikat kann mit dem `New-SelfSignedCertificate` PowerShell-Cmdlet erstellt werden. Verwenden Sie beim Erstellen des Zertifikats ein sicheres Kennwort. Es ist wichtig, den `KeyUsageProperty`-Parameter und den `KeyUsage`-Parameter wie gezeigt hinzuzufügen.

#### <a name="create-root-ca"></a>Stamm Zertifizierungsstelle erstellen

```powershell
New-SelfSignedCertificate -DnsName "root_ca_dev_damienbod.com", "root_ca_dev_damienbod.com" -CertStoreLocation "cert:\LocalMachine\My" -NotAfter (Get-Date).AddYears(20) -FriendlyName "root_ca_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\root_ca_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath root_ca_dev_damienbod.crt
```

#### <a name="install-in-the-trusted-root"></a>Installieren des vertrauenswürdigen Stamms

Das Stamm Zertifikat muss auf Ihrem Host System als vertrauenswürdig eingestuft werden. Ein Stamm Zertifikat, das nicht von einer Zertifizierungsstelle erstellt wurde, ist standardmäßig nicht vertrauenswürdig. Der folgende Link erläutert, wie dies unter Windows erreicht werden kann:

https://social.msdn.microsoft.com/Forums/SqlServer/5ed119ef-1704-4be4-8a4f-ef11de7c8f34/a-certificate-chain-processed-but-terminated-in-a-root-certificate-which-is-not-trusted-by-the

#### <a name="intermediate-certificate"></a>Zwischen Zertifikat

Ein zwischen Zertifikat kann nun aus dem Stamm Zertifikat erstellt werden. Dies ist für alle Anwendungsfälle nicht erforderlich, aber Sie müssen möglicherweise viele Zertifikate erstellen oder Gruppen von Zertifikaten aktivieren bzw. deaktivieren. Der `TextExtension`-Parameter ist erforderlich, um die Pfadlänge in den grundeinschränkungen des Zertifikats festzulegen.

Das zwischen Zertifikat kann dann dem vertrauenswürdigen zwischen Zertifikat im Windows-Host System hinzugefügt werden.

```powershell
$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint of the root..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "intermediate_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "intermediate_dev_damienbod.com" -KeyUsageProperty All -KeyUsage CertSign, CRLSign, DigitalSignature -TextExtension @("2.5.29.19={text}CA=1&pathlength=1")

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\intermediate_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath intermediate_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-intermediate-certificate"></a>Untergeordnetes Zertifikat aus zwischen Zertifikat erstellen

Ein untergeordnetes Zertifikat kann aus dem zwischen Zertifikat erstellt werden. Dies ist die Endentität, und es müssen nicht mehr untergeordnete Zertifikate erstellt werden.

```powershell
$parentcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the Intermediate certificate..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $parentcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="create-child-certificate-from-root-certificate"></a>Erstellen des untergeordneten Zertifikats aus dem Stamm Zertifikat

Ein untergeordnetes Zertifikat kann auch direkt aus dem Stamm Zertifikat erstellt werden. 

```powershell
$rootcert = ( Get-ChildItem -Path cert:\LocalMachine\My\"The thumbprint from the root cert..." )

New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "child_a_dev_damienbod.com" -Signer $rootcert -NotAfter (Get-Date).AddYears(20) -FriendlyName "child_a_dev_damienbod.com"

$mypwd = ConvertTo-SecureString -String "1234" -Force -AsPlainText

Get-ChildItem -Path cert:\localMachine\my\"The thumbprint..." | Export-PfxCertificate -FilePath C:\git\AspNetCoreCertificateAuth\Certs\child_a_dev_damienbod.pfx -Password $mypwd

Export-Certificate -Cert cert:\localMachine\my\"The thumbprint..." -FilePath child_a_dev_damienbod.crt
```

#### <a name="example-root---intermediate-certificate---certificate"></a>Beispiel für ein root-zwischen Zertifikat-Zertifikat

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

Wenn Sie die Stamm-, zwischen-oder untergeordneten Zertifikate verwenden, können die Zertifikate nach Bedarf mithilfe des Fingerabdrucks oder PublicKey überprüft werden.

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
