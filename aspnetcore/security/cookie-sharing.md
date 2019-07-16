---
title: Freigeben von Authentifizierungscookies für die mehrere ASP.NET-Anwendungen
author: rick-anderson
description: Informationen zum Freigeben des Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core-apps.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/15/2019
uid: security/cookie-sharing
ms.openlocfilehash: b2f906ac97fe79b2a66a5ab709bcbcb03ab8cc39
ms.sourcegitcommit: 1bf80f4acd62151ff8cce517f03f6fa891136409
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2019
ms.locfileid: "68223920"
---
# <a name="share-authentication-cookies-among-aspnet-apps"></a>Freigeben von Authentifizierungscookies für die mehrere ASP.NET-Anwendungen

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)

Websites bestehen häufig aus einzelner Web-apps, die zusammenarbeiten. Um einen einmaligen Anmeldens (SSO) zu gewährleisten, müssen die Web-apps innerhalb eines Standorts Authentifizierungscookies freigeben. Zur Unterstützung dieses Szenarios kann die Stapel für den Schutz von Daten Freigeben von Katana-Cookie-Authentifizierung und ASP.NET Core-Cookie-Authentifizierungstickets.

In den folgenden Beispielen:

* Der Name des Cookies Authentifizierung festgelegt ist, um einen gemeinsamen Wert `.AspNet.SharedCookie`.
* Die `AuthenticationType` nastaven NA hodnotu `Identity.Application` entweder explizit oder standardmäßig.
* Ein allgemeine app-Name wird verwendet, aktivieren Sie das System zum Schutz von Daten zum Freigeben von Data Protection-Schlüssel (`SharedCookieApp`).
* `Identity.Application` wird als das Authentifizierungsschema verwendet. Alle Schema verwendet, müssen einheitlich verwendet werden *innerhalb und außerhalb von* die freigegebenen Cookie-apps als der Standard-Schema oder indem Sie ihn explizit festlegen. Das Schema wird verwendet, beim Ver- und Entschlüsslung von Cookies, damit ein konsistentes Schema für apps verwendet werden muss.
* Eine allgemeine [Data Protection-Schlüssels](xref:security/data-protection/implementation/key-management) Speicherort verwendet wird.
  * In ASP.NET Core-apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> wird verwendet, um den Schlüsselspeicher Speicherort festzulegen.
  * In .NET Framework-apps Cookieauthentifizierungs-Middleware verwendet eine Implementierung des <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>. `DataProtectionProvider` enthält Data Protection-Diensten für die Ver- und Entschlüsselung von Authentifizierungsdaten Cookie-Nutzlast an. Die `DataProtectionProvider` Instanz wird isoliert vom System zum Schutz von Daten, die von anderen Teilen der app verwendet. [DataProtectionProvider.Create (System.IO.DirectoryInfo, Aktion\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert eine <xref:System.IO.DirectoryInfo> den Speicherort für die datenspeicherung für Schutz an.
* `DataProtectionProvider` erfordert die [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet-Paket:
  * In ASP.NET Core 2.x-apps verweisen die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).
  * In .NET Framework-apps, fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).
* <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Legt fest, den allgemeine Name der app.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Authentifizierungscookies zwischen ASP.NET Core-apps freigeben

Bei Verwendung von ASP.NET Core Identity:

* Data Protection-Schlüssel und app-Name müssen zwischen apps freigegeben werden. Ein allgemeinen Speicherort für den Schlüsselspeicher wird bereitgestellt, um die <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> -Methode in den folgenden Beispielen. Verwendung <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> so konfigurieren Sie einen allgemeinen Namen für die freigegebene app (`SharedCookieApp` in den folgenden Beispielen). Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.
* Verwenden der <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> Erweiterungsmethode, um den Datenschutzdienst für Cookies einzurichten.
* Im folgenden Beispiel wird der Authentifizierungstyp zu festgelegt `Identity.Application` standardmäßig.

In `Startup.ConfigureServices`:

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

Bei Verwendung von Cookies ohne ASP.NET Core Identity direkt, konfigurieren Sie den Schutz von Daten und Authentifizierung in `Startup.ConfigureServices`. Im folgenden Beispiel wird der Authentifizierungstyp zu festgelegt `Identity.Application`:

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

Beim Hosten von apps, die Cookies für Unterdomänen freigeben, geben Sie eine gemeinsame Domäne in der [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) Eigenschaft. Zum Freigeben von Cookies für apps im `contoso.com`, z. B. `first_subdomain.contoso.com` und `second_subdomain.contoso.com`, geben Sie die `Cookie.Domain` als `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a>Data Protection-Schlüssel im Ruhezustand verschlüsselt

Konfigurieren Sie bei produktionsbereitstellungen die `DataProtectionProvider` Schlüssel im Ruhezustand mit DPAPI oder ein X509Certificate verschlüsselt. Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>. Im folgenden Beispiel wird ein Fingerabdruck des Zertifikats bereitgestellt, um <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Freigeben des Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core-apps

ASP.NET 4.x-apps, mit dem Katana-Cookie-Authentifizierung-Middleware können für die um Authentifizierungs-Cookies zu generieren, die mit ASP.NET Core Cookieauthentifizierungsmiddleware kompatibel sind konfiguriert werden. Dadurch wird eine große Site einzelne apps in mehreren Schritten und gleichzeitig eine reibungslos funktionierende Umgebung für SSO für den Standort aktualisieren.

Wenn eine app Katana Cookieauthentifizierungs-Middleware verwendet wird, ruft er `UseCookieAuthentication` des Projekts *Startup.Auth.cs* Datei. ASP.NET 4.x-Web-app-Projekten mit Visual Studio 2013 erstellt und später die Katana Cookieauthentifizierungs-Middleware standardmäßig verwendet wird. Obwohl `UseCookieAuthentication` ist veraltet und wird nicht unterstützt für ASP.NET Core-apps Aufrufen `UseCookieAuthentication` in einer ASP.NET 4.x-app, die Katana Cookieauthentifizierungs-Middleware verwendet gültig ist.

Eine ASP.NET 4.x-app muss als Ziel .NET Framework 4.5.1 oder höher. Anderenfalls nicht die erforderlichen NuGet-Pakete installiert.

Authentifizierungscookies zwischen einer ASP.NET 4.x-app und einer ASP.NET Core-app freigeben möchten, konfigurieren Sie die ASP.NET Core-app gemäß dem [Authentifizierungscookies zwischen ASP.NET Core-apps freigeben](#share-authentication-cookies-among-aspnet-core-apps) Abschnitt, und konfigurieren Sie die ASP.NET 4.x-app als folgt.

Vergewissern Sie sich, dass die app Pakete auf die neuesten Versionen aktualisiert werden. Installieren Sie die [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4.x-app.

Suchen und ändern Sie den Aufruf von `UseCookieAuthentication`:

* Ändern Sie den Namen des Cookies mit dem Namen ein, die die ASP.NET Core Cookieauthentifizierungs-Middleware übereinstimmen (`.AspNet.SharedCookie` im Beispiel).
* Im folgenden Beispiel wird der Authentifizierungstyp zu festgelegt `Identity.Application`.
* Geben Sie eine Instanz von einer `DataProtectionProvider` mit den allgemeinen Schutz Key Datenspeicherort initialisiert.
* Vergewissern Sie sich, dass der Name der app, auf die allgemeiner app-Name festgelegt ist, der alle Apps, die gemeinsam von Authentifizierungs-Cookies (`SharedCookieApp` im Beispiel).

Wenn kein einrichten `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` und `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`legen <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> , einen Anspruch, der eindeutigen Benutzer unterscheidet.

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

Beim Generieren einer Benutzeridentität, der den Authentifizierungstyp (`Identity.Application`) muss dem Typ im definierten entsprechen `AuthenticationType` festgelegt `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.

*Models/IdentityModels.cs*:

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

## <a name="use-a-common-user-database"></a>Verwenden Sie eine allgemeine Benutzerdatenbank

Wenn apps verwenden die gleiche Identität Schema (dieselbe Version der Identität), vergewissern Sie sich, dass das Identitätssystem für jede app in der gleichen Benutzerdatenbank gezeigt wird. Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit auf, wenn er versucht, die Informationen in das Authentifizierungscookie anhand der Informationen in der Datenbank übereinstimmen.

Wenn das Schema für die Identität zwischen apps unterscheidet, da apps verschiedene Identity-Versionen verwenden, ist nicht in der Regel gemeinsame Nutzung einer allgemeinen Datenbank basierend auf die neueste Version der Identität möglich ohne neuzuordnung und Hinzufügen von Spalten in anderen app Identität Schemas. Häufig ist es effizienter, aktualisieren die anderen apps, um die neueste Version der Identität zu verwenden, sodass eine allgemeine Datenbank von den apps gemeinsam genutzt werden kann.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:host-and-deploy/web-farm>
