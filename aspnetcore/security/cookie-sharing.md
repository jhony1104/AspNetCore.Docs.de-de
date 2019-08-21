---
title: Freigeben von Authentifizierungs Cookies zwischen ASP.net-apps
author: rick-anderson
description: Erfahren Sie, wie Sie Authentifizierungs Cookies in ASP.NET 4. x-und ASP.net Core-apps freigeben.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: security/cookie-sharing
ms.openlocfilehash: 1650afce5c371d0830bb207618b9c1495f0ce587
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022386"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Freigeben von Authentifizierungs Cookies zwischen ASP.net-apps

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)

Websites bestehen häufig aus einzelnen Webanwendungen, die zusammenarbeiten. Um eine einmalige Anmeldung (Single Sign-on, SSO) zu ermöglichen, müssen Web-Apps innerhalb einer Website Authentifizierungs Cookies gemeinsam verwenden. Zur Unterstützung dieses Szenarios ermöglicht der Datenschutz Stapel die gemeinsame Verwendung von Katana-Cookie-Authentifizierung und ASP.net Core Cookie-Authentifizierungs Tickets.

In den folgenden Beispielen:

* Der Name des Authentifizierungs Cookies wird auf einen gemeinsamen Wert `.AspNet.SharedCookie`von festgelegt.
* Der `AuthenticationType` wird entweder explizit `Identity.Application` oder standardmäßig auf festgelegt.
* Ein allgemeiner App-Name wird verwendet, um dem Datenschutzsystem das Freigeben von Datenschutz`SharedCookieApp`Schlüsseln () zu ermöglichen.
* `Identity.Application`wird als Authentifizierungsschema verwendet. Welches Schema verwendet wird, muss konsistent *innerhalb und in* den freigegebenen Cookie-Apps verwendet werden, entweder als Standardschema oder explizit festgelegt. Das Schema wird beim Verschlüsseln und Entschlüsseln von Cookies verwendet. Daher muss ein konsistentes Schema zwischen Apps verwendet werden.
* Es wird ein allgemeiner Speicherort für den [Datenschutz Schlüssel](xref:security/data-protection/implementation/key-management) verwendet.
  * In ASP.net Core-apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> wird verwendet, um den Speicherort des Schlüssels festzulegen.
  * In .NET Framework-Apps verwendet die cookesauthentifizierungs-Middleware <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>eine Implementierung von. `DataProtectionProvider`stellt Datenschutzdienste zum Verschlüsseln und Entschlüsseln von Nutzungsdaten von Authentifizierungs Cookies bereit. Die `DataProtectionProvider` -Instanz ist von dem Datenschutzsystem isoliert, das von anderen Teilen der APP verwendet wird. [Dataschutzprovider. Create (System. IO. Director yinfo, Action\<idataschutzbuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert einen <xref:System.IO.DirectoryInfo> , um den Speicherort für den Datenschutz Schlüssel zu bestimmen.
* `DataProtectionProvider`erfordert das nuget-Paket " [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ":
  * Verweisen Sie in ASP.net Core 2. x-apps auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)".
  * Fügen Sie in .NET Framework-apps einen Paket Verweis auf [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)hinzu.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Legt den Namen der allgemeinen App fest.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Freigeben von Authentifizierungs Cookies zwischen ASP.net Core-apps

Wenn Sie ASP.net Core Identität verwenden:

* Datenschutz Schlüssel und der App-Name müssen von den apps gemeinsam genutzt werden. In den folgenden Beispielen wird die <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> -Methode mit einem allgemeinen Schlüssel Speicherort bereitgestellt. Verwenden <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Sie, um einen gemeinsamen freigegebenen`SharedCookieApp` APP-Namen (in den folgenden Beispielen) zu konfigurieren. Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.
* Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> die-Erweiterungsmethode, um den Datenschutz Dienst für Cookies einzurichten.
* Der Standard Authentifizierungstyp ist `Identity.Application`.

In `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

Wenn Sie Cookies direkt ohne ASP.net Core Identität verwenden, konfigurieren Sie den Datenschutz `Startup.ConfigureServices`und die Authentifizierung in. Im folgenden Beispiel wird der Authentifizierungstyp auf `Identity.Application`festgelegt:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

Geben Sie beim Hosting von Apps, die Cookies in Unterdomänen freigeben, eine allgemeine Domäne in der Eigenschaft [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) an. Geben `contoso.com` `first_subdomain.contoso.com` `second_subdomain.contoso.com` `.contoso.com`Sie zum Freigeben von Cookies für mehrere apps unter, wie z. b. und, Folgendes an: `Cookie.Domain`

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Verschlüsseln von Datenschutz Schlüsseln im Ruhezustand

Konfigurieren Sie für Produktions Bereitstellungen den `DataProtectionProvider` so, dass Schlüssel im Ruhezustand mit DPAPI oder einem X509Certificate verschlüsselt werden. Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>. Im folgenden Beispiel wird ein Zertifikat Fingerabdruck für <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>Folgendes bereitgestellt:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Freigeben von Authentifizierungs Cookies zwischen ASP.NET 4. x-und ASP.net Core-apps

ASP.NET 4. x-apps, die Katana-cookieauthentifizierungs-Middleware verwenden, können so konfiguriert werden, dass mit der ASP.net Core cookieauthentifizierungs-Middleware kompatible Authentifizierungs Cookies generiert werden. Dies ermöglicht die Aktualisierung der einzelnen apps eines großen Standorts in mehreren Schritten und bietet gleichzeitig eine reibungslose SSO-Funktion auf der Website.

Wenn eine APP eine Katana-Cookie-Authentifizierungs Middleware verwendet `UseCookieAuthentication` , ruft Sie in der *Startup.auth.cs* -Datei des Projekts auf. ASP.NET 4. x-Web-App-Projekte, die mit Visual Studio 2013 und höher erstellt wurden, verwenden standardmäßig die Katana-Cookie-Authentifizierungs Middleware. Obwohl `UseCookieAuthentication` veraltet ist und für ASP.net Core-apps nicht unterstützt `UseCookieAuthentication` wird, ist der Aufruf von in einer ASP.NET 4. x-APP, die Katana-cookeauthentifizierungs-Middleware verwendet, gültig

Eine ASP.NET 4. x-APP muss auf .NET Framework 4.5.1 oder höher ausgerichtet sein. Andernfalls können die erforderlichen nuget-Pakete nicht installiert werden.

Wenn Sie Authentifizierungs Cookies zwischen einer ASP.NET 4. x-APP und einer ASP.net Core-App freigeben möchten, konfigurieren Sie die ASP.net Core-App gemäß den Informationen im Abschnitt [Freigeben von Authentifizierungs Cookies unter ASP.net Core apps](#share-authentication-cookies-among-aspnet-core-apps) , und konfigurieren Sie dann die ASP.NET 4. x-APP wie folgt.

Vergewissern Sie sich, dass die Pakete der APP auf die neuesten Versionen aktualisiert werden. Installieren Sie das [Microsoft. owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4. x-app.

Suchen und ändern Sie den- `UseCookieAuthentication`Befehl an:

* Ändern Sie den Namen des Cookies so, dass er mit dem von der ASP.net Core cookenauthentifizierungs-Middleware (`.AspNet.SharedCookie` im Beispiel) verwendeten Namen identisch ist.
* Im folgenden Beispiel wird der Authentifizierungstyp auf `Identity.Application`festgelegt.
* Geben Sie eine Instanz von `DataProtectionProvider` an, die für den Common Data Protection Key-Speicherort initialisiert wurde.
* Vergewissern Sie sich, dass der Name der APP auf den allgemeinen APP-Namen festgelegt ist, der von`SharedCookieApp` allen Apps verwendet wird, die Authentifizierungs Cookies verwenden (im Beispiel).

Wenn Sie und `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`nicht festlegen, <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> legen Sie auf einen Anspruch fest, der eindeutige Benutzer unterscheidet.

*App_Start/Startup. auth. cs*:

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create({PATH TO COMMON KEY RING FOLDER},
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

Beim Erstellen einer Benutzeridentität muss`Identity.Application`der Authentifizierungstyp () mit dem Typ identisch sein, der in `AuthenticationType` Set with `UseCookieAuthentication` in *App_Start/Startup. auth. cs*definiert ist.

*Models/identitymodels. cs*:

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a>Verwenden einer allgemeinen Benutzerdatenbank

Wenn apps dasselbe Identitäts Schema verwenden (dieselbe Identitäts Version), vergewissern Sie sich, dass das Identitätssystem für jede APP auf dieselbe Benutzerdatenbank verweist. Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit, wenn versucht wird, die Informationen im Authentifizierungs Cookie mit den Informationen in der Datenbank abzugleichen.

Wenn das Identitäts Schema von apps abweicht, in der Regel, weil apps verschiedene Identitäts Versionen verwenden, ist die gemeinsame Nutzung einer gemeinsamen Datenbank auf der Grundlage der aktuellen Identitäts Version nicht möglich, ohne dass Spalten in den Identitäts Schemas anderer apps neu zugeordnet und hinzugefügt werden. Häufig ist es effizienter, die anderen apps so zu aktualisieren, dass Sie die neueste Identitäts Version verwenden, damit eine gemeinsame Datenbank von den apps gemeinsam genutzt werden kann.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:host-and-deploy/web-farm>
