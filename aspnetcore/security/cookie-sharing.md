---
title: Freigeben von Cookies zwischen apps mit ASP.NET und ASP.NET Core
author: rick-anderson
description: Informationen zum Freigeben des Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core-apps.
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
uid: security/cookie-sharing
ms.openlocfilehash: 7a049ed8787808e228859afc051b8697a6261c21
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068309"
---
# <a name="share-cookies-among-apps-with-aspnet-and-aspnet-core"></a>Freigeben von Cookies zwischen apps mit ASP.NET und ASP.NET Core

Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)

Websites bestehen häufig aus einzelner Web-apps, die zusammenarbeiten. Um einen einmaligen Anmeldens (SSO) zu gewährleisten, müssen die Web-apps innerhalb eines Standorts Authentifizierungscookies freigeben. Zur Unterstützung dieses Szenarios kann die Stapel für den Schutz von Daten Freigeben von Katana-Cookie-Authentifizierung und ASP.NET Core-Cookie-Authentifizierungstickets.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispiel veranschaulicht die Cookies, die Freigabe über drei apps, die Cookie-Authentifizierung zu verwenden:

* ASP.NET Core 2.0-Razor-Seiten-app ohne [ASP.NET Core Identity](xref:security/authentication/identity)
* ASP.NET Core 2.0 MVC-app mit ASP.NET Core-Identität
* ASP.NET Framework 4.6.1-MVC-app mit ASP.NET Identity

In den folgenden Beispielen:

* Der Name des Cookies Authentifizierung festgelegt ist, um einen gemeinsamen Wert `.AspNet.SharedCookie`.
* Die `AuthenticationType` nastaven NA hodnotu `Identity.Application` entweder explizit oder standardmäßig.
* Ein allgemeine app-Name wird verwendet, aktivieren Sie das System zum Schutz von Daten zum Freigeben von Data Protection-Schlüssel (`SharedCookieApp`).
* `Identity.Application` wird als das Authentifizierungsschema verwendet. Alle Schema verwendet, müssen einheitlich verwendet werden *innerhalb und außerhalb von* die freigegebenen Cookie-apps als der Standard-Schema oder indem Sie ihn explizit festlegen. Das Schema wird verwendet, beim Ver- und Entschlüsslung von Cookies, damit ein konsistentes Schema für apps verwendet werden muss.
* Eine allgemeine [Data Protection-Schlüssels](xref:security/data-protection/implementation/key-management) Speicherort verwendet wird. Die Beispiel-app verwendet einen Ordner namens *Schlüsselbund* am Stamm der Projektmappe, die die Schlüssel zum Schutz von Daten enthalten soll.
* In den ASP.NET Core-apps [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) wird verwendet, um den Schlüsselspeicher Speicherort festzulegen. [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) wird verwendet, um einen allgemeinen Namen für die freigegebene app konfigurieren.
* In der .NET Framework-app verwendet die cookieauthentifizierungs-Middleware eine Implementierung des [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider). `DataProtectionProvider` enthält Data Protection-Diensten für die Ver- und Entschlüsselung von Authentifizierungsdaten Cookie-Nutzlast an. Die `DataProtectionProvider` Instanz wird isoliert vom System zum Schutz von Daten, die von anderen Teilen der app verwendet.
  * [DataProtectionProvider.Create (System.IO.DirectoryInfo, Aktion\<IDataProtectionBuilder >)](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider.create?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionProvider_Create_System_IO_DirectoryInfo_System_Action_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder__) akzeptiert eine [DirectoryInfo](/dotnet/api/system.io.directoryinfo) den Speicherort für die datenspeicherung für Schutz an. Die Beispiel-app stellt den Pfad zu der *Schlüsselbund* Ordner `DirectoryInfo`. [DataProtectionBuilderExtensions.SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname?view=aspnetcore-2.0#Microsoft_AspNetCore_DataProtection_DataProtectionBuilderExtensions_SetApplicationName_Microsoft_AspNetCore_DataProtection_IDataProtectionBuilder_System_String_) sets the common app name.
  * [DataProtectionProvider](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionprovider) erfordert die [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet-Paket. Um dieses Paket für ASP.NET Core 2.1 und höher apps zu erhalten, verweisen die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app). Wenn die .NET Framework abzielen, fügen Sie einen Paketverweis auf `Microsoft.AspNetCore.DataProtection.Extensions`.

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a>Authentifizierungscookies zwischen ASP.NET Core-apps freigeben

Bei Verwendung von ASP.NET Core Identity:

::: moniker range=">= aspnetcore-2.0"

In der `ConfigureServices` -Methode, mit der [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie) Erweiterungsmethode, um den Datenschutzdienst für Cookies einzurichten.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.Core/Startup.cs?name=snippet1)]

Data Protection-Schlüssel und app-Name müssen zwischen apps freigegeben werden. In den beispielapps `GetKeyRingDirInfo` gibt den allgemeinen Schlüssel Speicherort, der [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) Methode. Verwendung [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) so konfigurieren Sie einen allgemeinen Namen für die freigegebene app (`SharedCookieApp` im Beispiel). Weitere Informationen finden Sie unter [Konfigurieren von Data Protection](xref:security/data-protection/configuration/overview).

Beim Hosten von apps, die Cookies für Unterdomänen freigeben, geben Sie eine gemeinsame Domäne in der [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) Eigenschaft. Zum Freigeben von Cookies für apps im `contoso.com`, z. B. `first_subdomain.contoso.com` und `second_subdomain.contoso.com`, geben Sie die `Cookie.Domain` als `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

Finden Sie unter den *CookieAuthWithIdentity.Core* -Projekt in der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([herunterladen](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

In der `Configure` -Methode, mit der [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.builder.cookieauthenticationoptions) einrichten:

* Den Datenschutzdienst für Cookies.
* Die `AuthenticationScheme` entsprechend der ASP.NET 4.x.

```csharp
app.AddIdentity<ApplicationUser, IdentityRole>(options =>
{
    options.Cookies.ApplicationCookie.AuthenticationScheme = 
        "ApplicationCookie";

    var protectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"));

    options.Cookies.ApplicationCookie.DataProtectionProvider = 
        protectionProvider;

    options.Cookies.ApplicationCookie.TicketDataFormat = 
        new TicketDataFormat(protectionProvider.CreateProtector(
            "Microsoft.AspNetCore.Authentication.Cookies.CookieAuthenticationMiddleware", 
            "Cookies", 
            "v2"));
});
```

::: moniker-end

Wenn Sie Cookies direkt verwenden:

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](cookie-sharing/sample/CookieAuth.Core/Startup.cs?name=snippet1)]

Data Protection-Schlüssel und app-Name müssen zwischen apps freigegeben werden. In den beispielapps `GetKeyRingDirInfo` gibt den allgemeinen Schlüssel Speicherort, der [PersistKeysToFileSystem](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.persistkeystofilesystem) Methode. Verwendung [SetApplicationName](/dotnet/api/microsoft.aspnetcore.dataprotection.dataprotectionbuilderextensions.setapplicationname) so konfigurieren Sie einen allgemeinen Namen für die freigegebene app (`SharedCookieApp` im Beispiel). Weitere Informationen finden Sie unter [Konfigurieren von Data Protection](xref:security/data-protection/configuration/overview).

Beim Hosten von apps, die Cookies für Unterdomänen freigeben, geben Sie eine gemeinsame Domäne in der [Cookie.Domain](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.domain) Eigenschaft. Zum Freigeben von Cookies für apps im `contoso.com`, z. B. `first_subdomain.contoso.com` und `second_subdomain.contoso.com`, geben Sie die `Cookie.Domain` als `.contoso.com`:

```csharp
options.Cookie.Domain = ".contoso.com";
```

Finden Sie unter den *CookieAuth.Core* -Projekt in der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([herunterladen](xref:index#how-to-download-a-sample)).

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = 
        DataProtectionProvider.Create(
            new DirectoryInfo(@"PATH_TO_KEY_RING_FOLDER"))
});
```

::: moniker-end

## <a name="encrypting-data-protection-keys-at-rest"></a>Data Protection-Schlüssel im Ruhezustand verschlüsselt

Konfigurieren Sie bei produktionsbereitstellungen die `DataProtectionProvider` Schlüssel im Ruhezustand mit DPAPI oder ein X509Certificate verschlüsselt. Finden Sie unter [Schlüssel Verschlüsselung ruhender](xref:security/data-protection/implementation/key-encryption-at-rest) für Weitere Informationen.

::: moniker range=">= aspnetcore-2.0"

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("thumbprint");
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    DataProtectionProvider = DataProtectionProvider.Create(
        new DirectoryInfo(@"PATH_TO_KEY_RING"),
        configure =>
        {
            configure.ProtectKeysWithCertificate("thumbprint");
        })
});
```

::: moniker-end

## <a name="sharing-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a>Freigeben von Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core-apps

ASP.NET 4.x-apps, die Katana-Cookie-Authentifizierung-Middleware verwenden können konfiguriert werden, um Authentifizierungscookies zu generieren, die mit ASP.NET Core cookieauthentifizierungsmiddleware kompatibel sind. Dadurch wird eine große Site einzelne apps schrittweise und gleichzeitig eine reibungslos funktionierende Umgebung für SSO für den Standort aktualisieren.

Wenn eine app Katana cookieauthentifizierungs-Middleware verwendet wird, ruft er `UseCookieAuthentication` des Projekts *Startup.Auth.cs* Datei. ASP.NET 4.x-Web-app-Projekten mit Visual Studio 2013 erstellt und später die Katana cookieauthentifizierungs-Middleware standardmäßig verwendet wird. Obwohl `UseCookieAuthentication` ist veraltet und wird nicht unterstützt für ASP.NET Core-apps Aufrufen `UseCookieAuthentication` in einer ASP.NET 4.x-app, die Katana verwendet cookieauthentifizierungsmiddleware gültig ist.

Eine ASP.NET 4.x-app muss als Ziel .NET Framework 4.5.1 oder höher. Anderenfalls nicht die erforderlichen NuGet-Pakete installiert.

Wenn Authentifizierungscookies zwischen einer ASP.NET 4.x-app und einer ASP.NET Core-app freigeben möchten, konfigurieren Sie die ASP.NET Core-app aus, wie bereits erwähnt, und konfigurieren Sie die ASP.NET 4.x-app, indem Sie folgende Schritte:

1. Installieren Sie das Paket [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) in jeder ASP.NET 4.x-app.

2. In *Startup.Auth.cs*, suchen Sie den Aufruf von `UseCookieAuthentication` und ändern Sie sie wie folgt. Ändern Sie den Namen des Cookies mit dem Namen ein, die die ASP.NET Core cookieauthentifizierungs-Middleware übereinstimmen. Geben Sie eine Instanz von einer `DataProtectionProvider` mit den allgemeinen Schutz Key Datenspeicherort initialisiert. Stellen Sie sicher, dass der Name der app, auf die allgemeiner app-Name festgelegt ist, der alle Apps, die Cookies, freigeben `SharedCookieApp` in der Beispiel-app.

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/App_Start/Startup.Auth.cs?name=snippet1)]

Finden Sie unter den *CookieAuthWithIdentity.NETFramework* -Projekt in der [Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/cookie-sharing/sample/) ([herunterladen](xref:index#how-to-download-a-sample)).

Zur Erstellung eine Benutzeridentität muss der Authentifizierungstyp in definierten Typ übereinstimmen `AuthenticationType` festgelegt `UseCookieAuthentication`.

*Models/IdentityModels.cs*:

[!code-csharp[](cookie-sharing/sample/CookieAuthWithIdentity.NETFramework/CookieAuthWithIdentity.NETFramework/Models/IdentityModels.cs?name=snippet1)]

## <a name="use-a-common-user-database"></a>Verwenden Sie eine allgemeine Benutzerdatenbank

Vergewissern Sie sich, dass das Identitätssystem für jede app in der gleichen Benutzerdatenbank gezeigt wird. Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit auf, wenn er versucht, die Informationen in das Authentifizierungscookie anhand der Informationen in der Datenbank übereinstimmen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

<xref:host-and-deploy/web-farm>
