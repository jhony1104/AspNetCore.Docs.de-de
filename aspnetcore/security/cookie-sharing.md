---
title: Freigeben von Authentifizierungs Cookies zwischen ASP.net-apps
author: rick-anderson
description: Erfahren Sie, wie Sie Authentifizierungs Cookies in ASP.NET 4. x-und ASP.net Core-apps freigeben.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7e29be22717f0b97fc115ac036cc54e333bed4e2
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651607"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Freigeben von Authentifizierungs Cookies zwischen ASP.net-apps

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

Websites bestehen häufig aus einzelnen Webanwendungen, die zusammenarbeiten. Zum Bereitstellen eines Single Sign-on (SSO) müssen Web-Apps innerhalb einer Website Authentifizierungs Cookies gemeinsam verwenden. Zur Unterstützung dieses Szenarios ermöglicht der Datenschutz Stapel die gemeinsame Verwendung von Katana-Cookie-Authentifizierung und ASP.net Core Cookie-Authentifizierungs Tickets.

In den folgenden Beispielen:

* Der Name des Authentifizierungs Cookies wird auf einen allgemeinen Wert `.AspNet.SharedCookie`festgelegt.
* Der `AuthenticationType` ist auf `Identity.Application` entweder explizit oder standardmäßig festgelegt.
* Ein allgemeiner App-Name wird verwendet, um dem Datenschutzsystem das Freigeben von Datenschutz Schlüsseln (`SharedCookieApp`) zu ermöglichen.
* `Identity.Application` wird als Authentifizierungsschema verwendet. Welches Schema verwendet wird, muss konsistent *innerhalb und in* den freigegebenen Cookie-Apps verwendet werden, entweder als Standardschema oder explizit festgelegt. Das Schema wird beim Verschlüsseln und Entschlüsseln von Cookies verwendet. Daher muss ein konsistentes Schema zwischen Apps verwendet werden.
* Es wird ein allgemeiner Speicherort für den [Datenschutz Schlüssel](xref:security/data-protection/implementation/key-management) verwendet.
  * In ASP.net Core-apps wird <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> verwendet, um den Schlüssel Speicherort festzulegen.
  * In .NET Framework-Apps verwendet die cookesauthentifizierungs-Middleware eine Implementierung von <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>. `DataProtectionProvider` stellt Datenschutzdienste für die Verschlüsselung und Entschlüsselung von Daten zu den Nutzlastdaten des Authentifizierungs Cookies bereit. Die `DataProtectionProvider` Instanz ist von dem Datenschutzsystem isoliert, das von anderen Teilen der APP verwendet wird. [Dataschutzprovider. Create (System. IO. Director yinfo, Action\<idataschutzbuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert einen <xref:System.IO.DirectoryInfo>, um den Speicherort für den Datenschutz Schlüssel anzugeben.
* `DataProtectionProvider` erfordert das nuget-Paket " [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ":
  * Verweisen Sie in ASP.net Core 2. x-apps auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)".
  * Fügen Sie in .NET Framework-apps einen Paket Verweis auf [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)hinzu.
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> legt den Namen der allgemeinen App fest.

## <a name="share-authentication-cookies-with-aspnet-core-identity"></a>Freigeben von Authentifizierungs Cookies mit ASP.net Core Identität

Wenn Sie ASP.net Core Identität verwenden:

* Datenschutz Schlüssel und der App-Name müssen von den apps gemeinsam genutzt werden. In den folgenden Beispielen wird ein allgemeiner Schlüssel Speicherort für die <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*>-Methode bereitgestellt. Verwenden Sie <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>, um einen gemeinsamen freigegebenen APP-Namen (`SharedCookieApp` in den folgenden Beispielen) zu konfigurieren. Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.
* Verwenden Sie die <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>-Erweiterungsmethode, um den Datenschutz Dienst für Cookies einzurichten.
* Der Standard Authentifizierungstyp ist `Identity.Application`.

In `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

## <a name="share-authentication-cookies-without-aspnet-core-identity"></a>Freigeben von Authentifizierungs Cookies ohne ASP.net Core Identität

Wenn Sie Cookies ohne ASP.net Core Identität direkt verwenden, konfigurieren Sie den Datenschutz und die Authentifizierung in `Startup.ConfigureServices`. Im folgenden Beispiel wird der Authentifizierungstyp auf `Identity.Application`festgelegt:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-cookies-across-different-base-paths"></a>Freigeben von Cookies über verschiedene Basis Pfade hinweg

Ein Authentifizierungs Cookie verwendet [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) als Standard [Cookie. Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path). Wenn das Cookie der APP über verschiedene Basis Pfade gemeinsam genutzt werden muss, muss `Path` überschrieben werden:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-cookies-across-subdomains"></a>Cookies in Unterdomänen freigeben

Geben Sie beim Hosting von apps, die Cookies in verschiedenen Unterdomänen teilen, eine gemeinsame Domäne in der [Cookiedomäne](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) an. Geben Sie zum Freigeben von Cookies für mehrere apps `contoso.com`wie `first_subdomain.contoso.com` und `second_subdomain.contoso.com`die `Cookie.Domain` als `.contoso.com`an:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Verschlüsseln von Datenschutz Schlüsseln im Ruhezustand

Konfigurieren Sie für Produktions Bereitstellungen die `DataProtectionProvider`, um Schlüssel im Ruhezustand mit DPAPI oder einem X509Certificate zu verschlüsseln. Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>. Im folgenden Beispiel wird ein Zertifikat Fingerabdruck für <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>bereitgestellt:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Freigeben von Authentifizierungs Cookies zwischen ASP.NET 4. x-und ASP.net Core-apps

ASP.NET 4. x-apps, die Katana-cookieauthentifizierungs-Middleware verwenden, können so konfiguriert werden, dass mit der ASP.net Core cookieauthentifizierungs-Middleware kompatible Authentifizierungs Cookies generiert werden. Dies ermöglicht die Aktualisierung der einzelnen apps eines großen Standorts in mehreren Schritten und bietet gleichzeitig eine reibungslose SSO-Funktion auf der Website.

Wenn eine APP eine Katana-Cookie-Authentifizierungs Middleware verwendet, ruft Sie `UseCookieAuthentication` in der *Startup.auth.cs* -Datei des Projekts auf. ASP.NET 4. x-Web-App-Projekte, die mit Visual Studio 2013 und höher erstellt wurden, verwenden standardmäßig die Katana-Cookie-Authentifizierungs Middleware. Obwohl `UseCookieAuthentication` veraltet und für ASP.net Core-apps nicht unterstützt wird, ist das Aufrufen von `UseCookieAuthentication` in einer ASP.NET 4. x-APP, die Katana-cookeauthentifizierungs-Middleware verwendet, gültig.

Eine ASP.NET 4. x-APP muss auf .NET Framework 4.5.1 oder höher ausgerichtet sein. Andernfalls können die erforderlichen nuget-Pakete nicht installiert werden.

Wenn Sie Authentifizierungs Cookies zwischen einer ASP.NET 4. x-APP und einer ASP.net Core-App freigeben möchten, konfigurieren Sie die ASP.net Core-App gemäß den Informationen im Abschnitt [Freigeben von Authentifizierungs Cookies unter ASP.net Core apps](#share-authentication-cookies-with-aspnet-core-identity) , und konfigurieren Sie dann die ASP.NET 4. x-APP wie folgt.

Vergewissern Sie sich, dass die Pakete der APP auf die neuesten Versionen aktualisiert werden. Installieren Sie das [Microsoft. owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4. x-app.

Suchen und ändern Sie den `UseCookieAuthentication`-Aufrufe:

* Ändern Sie den cooknamen so, dass er mit dem Namen der ASP.net Core cookenauthentifizierungs-Middleware (`.AspNet.SharedCookie` im Beispiel) identisch ist.
* Im folgenden Beispiel wird der Authentifizierungstyp auf `Identity.Application`festgelegt.
* Stellen Sie eine Instanz einer `DataProtectionProvider` bereit, die für den Common Data Protection Key-Speicherort initialisiert wurde.
* Vergewissern Sie sich, dass der Name der APP auf den allgemeinen APP-Namen festgelegt ist, der von allen Apps verwendet wird, die Authentifizierungs Cookies verwenden (im Beispiel`SharedCookieApp`).

Wenn Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` und `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`nicht festlegen, legen Sie <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> auf einen Anspruch fest, mit dem eindeutige Benutzer unterschieden werden.

*App_Start/Startup.auth.cs*:

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
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
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

Beim Erstellen einer Benutzeridentität muss der Authentifizierungstyp (`Identity.Application`) mit dem Typ identisch sein, der in `AuthenticationType` Set mit `UseCookieAuthentication` in *App_Start/Startup.auth.cs*definiert ist.

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
