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
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="f35c4-103">Freigeben von Authentifizierungscookies für die mehrere ASP.NET-Anwendungen</span><span class="sxs-lookup"><span data-stu-id="f35c4-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="f35c4-104">Durch [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f35c4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f35c4-105">Websites bestehen häufig aus einzelner Web-apps, die zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="f35c4-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="f35c4-106">Um einen einmaligen Anmeldens (SSO) zu gewährleisten, müssen die Web-apps innerhalb eines Standorts Authentifizierungscookies freigeben.</span><span class="sxs-lookup"><span data-stu-id="f35c4-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="f35c4-107">Zur Unterstützung dieses Szenarios kann die Stapel für den Schutz von Daten Freigeben von Katana-Cookie-Authentifizierung und ASP.NET Core-Cookie-Authentifizierungstickets.</span><span class="sxs-lookup"><span data-stu-id="f35c4-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="f35c4-108">In den folgenden Beispielen:</span><span class="sxs-lookup"><span data-stu-id="f35c4-108">In the examples that follow:</span></span>

* <span data-ttu-id="f35c4-109">Der Name des Cookies Authentifizierung festgelegt ist, um einen gemeinsamen Wert `.AspNet.SharedCookie`.</span><span class="sxs-lookup"><span data-stu-id="f35c4-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="f35c4-110">Die `AuthenticationType` nastaven NA hodnotu `Identity.Application` entweder explizit oder standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="f35c4-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="f35c4-111">Ein allgemeine app-Name wird verwendet, aktivieren Sie das System zum Schutz von Daten zum Freigeben von Data Protection-Schlüssel (`SharedCookieApp`).</span><span class="sxs-lookup"><span data-stu-id="f35c4-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="f35c4-112">`Identity.Application` wird als das Authentifizierungsschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="f35c4-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="f35c4-113">Alle Schema verwendet, müssen einheitlich verwendet werden *innerhalb und außerhalb von* die freigegebenen Cookie-apps als der Standard-Schema oder indem Sie ihn explizit festlegen.</span><span class="sxs-lookup"><span data-stu-id="f35c4-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="f35c4-114">Das Schema wird verwendet, beim Ver- und Entschlüsslung von Cookies, damit ein konsistentes Schema für apps verwendet werden muss.</span><span class="sxs-lookup"><span data-stu-id="f35c4-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="f35c4-115">Eine allgemeine [Data Protection-Schlüssels](xref:security/data-protection/implementation/key-management) Speicherort verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f35c4-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="f35c4-116">In ASP.NET Core-apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> wird verwendet, um den Schlüsselspeicher Speicherort festzulegen.</span><span class="sxs-lookup"><span data-stu-id="f35c4-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="f35c4-117">In .NET Framework-apps Cookieauthentifizierungs-Middleware verwendet eine Implementierung des <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span><span class="sxs-lookup"><span data-stu-id="f35c4-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="f35c4-118">`DataProtectionProvider` enthält Data Protection-Diensten für die Ver- und Entschlüsselung von Authentifizierungsdaten Cookie-Nutzlast an.</span><span class="sxs-lookup"><span data-stu-id="f35c4-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="f35c4-119">Die `DataProtectionProvider` Instanz wird isoliert vom System zum Schutz von Daten, die von anderen Teilen der app verwendet.</span><span class="sxs-lookup"><span data-stu-id="f35c4-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="f35c4-120">[DataProtectionProvider.Create (System.IO.DirectoryInfo, Aktion\<IDataProtectionBuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert eine <xref:System.IO.DirectoryInfo> den Speicherort für die datenspeicherung für Schutz an.</span><span class="sxs-lookup"><span data-stu-id="f35c4-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="f35c4-121">`DataProtectionProvider` erfordert die [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet-Paket:</span><span class="sxs-lookup"><span data-stu-id="f35c4-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="f35c4-122">In ASP.NET Core 2.x-apps verweisen die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="f35c4-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="f35c4-123">In .NET Framework-apps, fügen Sie einen Paketverweis auf [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span><span class="sxs-lookup"><span data-stu-id="f35c4-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="f35c4-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Legt fest, den allgemeine Name der app.</span><span class="sxs-lookup"><span data-stu-id="f35c4-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="f35c4-125">Authentifizierungscookies zwischen ASP.NET Core-apps freigeben</span><span class="sxs-lookup"><span data-stu-id="f35c4-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="f35c4-126">Bei Verwendung von ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="f35c4-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f35c4-127">Data Protection-Schlüssel und app-Name müssen zwischen apps freigegeben werden.</span><span class="sxs-lookup"><span data-stu-id="f35c4-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="f35c4-128">Ein allgemeinen Speicherort für den Schlüsselspeicher wird bereitgestellt, um die <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> -Methode in den folgenden Beispielen.</span><span class="sxs-lookup"><span data-stu-id="f35c4-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="f35c4-129">Verwendung <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> so konfigurieren Sie einen allgemeinen Namen für die freigegebene app (`SharedCookieApp` in den folgenden Beispielen).</span><span class="sxs-lookup"><span data-stu-id="f35c4-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="f35c4-130">Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="f35c4-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="f35c4-131">Verwenden der <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> Erweiterungsmethode, um den Datenschutzdienst für Cookies einzurichten.</span><span class="sxs-lookup"><span data-stu-id="f35c4-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="f35c4-132">Im folgenden Beispiel wird der Authentifizierungstyp zu festgelegt `Identity.Application` standardmäßig.</span><span class="sxs-lookup"><span data-stu-id="f35c4-132">In the following example, the authentication type is set to `Identity.Application` by default.</span></span>

<span data-ttu-id="f35c4-133">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="f35c4-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="f35c4-134">Bei Verwendung von Cookies ohne ASP.NET Core Identity direkt, konfigurieren Sie den Schutz von Daten und Authentifizierung in `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="f35c4-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="f35c4-135">Im folgenden Beispiel wird der Authentifizierungstyp zu festgelegt `Identity.Application`:</span><span class="sxs-lookup"><span data-stu-id="f35c4-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="f35c4-136">Beim Hosten von apps, die Cookies für Unterdomänen freigeben, geben Sie eine gemeinsame Domäne in der [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="f35c4-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="f35c4-137">Zum Freigeben von Cookies für apps im `contoso.com`, z. B. `first_subdomain.contoso.com` und `second_subdomain.contoso.com`, geben Sie die `Cookie.Domain` als `.contoso.com`:</span><span class="sxs-lookup"><span data-stu-id="f35c4-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="f35c4-138">Data Protection-Schlüssel im Ruhezustand verschlüsselt</span><span class="sxs-lookup"><span data-stu-id="f35c4-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="f35c4-139">Konfigurieren Sie bei produktionsbereitstellungen die `DataProtectionProvider` Schlüssel im Ruhezustand mit DPAPI oder ein X509Certificate verschlüsselt.</span><span class="sxs-lookup"><span data-stu-id="f35c4-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="f35c4-140">Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="f35c4-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="f35c4-141">Im folgenden Beispiel wird ein Fingerabdruck des Zertifikats bereitgestellt, um <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span><span class="sxs-lookup"><span data-stu-id="f35c4-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="f35c4-142">Freigeben des Authentifizierungscookies zwischen ASP.NET 4.x und ASP.NET Core-apps</span><span class="sxs-lookup"><span data-stu-id="f35c4-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="f35c4-143">ASP.NET 4.x-apps, mit dem Katana-Cookie-Authentifizierung-Middleware können für die um Authentifizierungs-Cookies zu generieren, die mit ASP.NET Core Cookieauthentifizierungsmiddleware kompatibel sind konfiguriert werden.</span><span class="sxs-lookup"><span data-stu-id="f35c4-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="f35c4-144">Dadurch wird eine große Site einzelne apps in mehreren Schritten und gleichzeitig eine reibungslos funktionierende Umgebung für SSO für den Standort aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="f35c4-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="f35c4-145">Wenn eine app Katana Cookieauthentifizierungs-Middleware verwendet wird, ruft er `UseCookieAuthentication` des Projekts *Startup.Auth.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="f35c4-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="f35c4-146">ASP.NET 4.x-Web-app-Projekten mit Visual Studio 2013 erstellt und später die Katana Cookieauthentifizierungs-Middleware standardmäßig verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="f35c4-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="f35c4-147">Obwohl `UseCookieAuthentication` ist veraltet und wird nicht unterstützt für ASP.NET Core-apps Aufrufen `UseCookieAuthentication` in einer ASP.NET 4.x-app, die Katana Cookieauthentifizierungs-Middleware verwendet gültig ist.</span><span class="sxs-lookup"><span data-stu-id="f35c4-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="f35c4-148">Eine ASP.NET 4.x-app muss als Ziel .NET Framework 4.5.1 oder höher.</span><span class="sxs-lookup"><span data-stu-id="f35c4-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="f35c4-149">Anderenfalls nicht die erforderlichen NuGet-Pakete installiert.</span><span class="sxs-lookup"><span data-stu-id="f35c4-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="f35c4-150">Authentifizierungscookies zwischen einer ASP.NET 4.x-app und einer ASP.NET Core-app freigeben möchten, konfigurieren Sie die ASP.NET Core-app gemäß dem [Authentifizierungscookies zwischen ASP.NET Core-apps freigeben](#share-authentication-cookies-among-aspnet-core-apps) Abschnitt, und konfigurieren Sie die ASP.NET 4.x-app als folgt.</span><span class="sxs-lookup"><span data-stu-id="f35c4-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="f35c4-151">Vergewissern Sie sich, dass die app Pakete auf die neuesten Versionen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="f35c4-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="f35c4-152">Installieren Sie die [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4.x-app.</span><span class="sxs-lookup"><span data-stu-id="f35c4-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="f35c4-153">Suchen und ändern Sie den Aufruf von `UseCookieAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="f35c4-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="f35c4-154">Ändern Sie den Namen des Cookies mit dem Namen ein, die die ASP.NET Core Cookieauthentifizierungs-Middleware übereinstimmen (`.AspNet.SharedCookie` im Beispiel).</span><span class="sxs-lookup"><span data-stu-id="f35c4-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="f35c4-155">Im folgenden Beispiel wird der Authentifizierungstyp zu festgelegt `Identity.Application`.</span><span class="sxs-lookup"><span data-stu-id="f35c4-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="f35c4-156">Geben Sie eine Instanz von einer `DataProtectionProvider` mit den allgemeinen Schutz Key Datenspeicherort initialisiert.</span><span class="sxs-lookup"><span data-stu-id="f35c4-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="f35c4-157">Vergewissern Sie sich, dass der Name der app, auf die allgemeiner app-Name festgelegt ist, der alle Apps, die gemeinsam von Authentifizierungs-Cookies (`SharedCookieApp` im Beispiel).</span><span class="sxs-lookup"><span data-stu-id="f35c4-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="f35c4-158">Wenn kein einrichten `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` und `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`legen <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> , einen Anspruch, der eindeutigen Benutzer unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="f35c4-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="f35c4-159">*App_Start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="f35c4-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="f35c4-160">Beim Generieren einer Benutzeridentität, der den Authentifizierungstyp (`Identity.Application`) muss dem Typ im definierten entsprechen `AuthenticationType` festgelegt `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span><span class="sxs-lookup"><span data-stu-id="f35c4-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="f35c4-161">*Models/IdentityModels.cs*:</span><span class="sxs-lookup"><span data-stu-id="f35c4-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="f35c4-162">Verwenden Sie eine allgemeine Benutzerdatenbank</span><span class="sxs-lookup"><span data-stu-id="f35c4-162">Use a common user database</span></span>

<span data-ttu-id="f35c4-163">Wenn apps verwenden die gleiche Identität Schema (dieselbe Version der Identität), vergewissern Sie sich, dass das Identitätssystem für jede app in der gleichen Benutzerdatenbank gezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="f35c4-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="f35c4-164">Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit auf, wenn er versucht, die Informationen in das Authentifizierungscookie anhand der Informationen in der Datenbank übereinstimmen.</span><span class="sxs-lookup"><span data-stu-id="f35c4-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="f35c4-165">Wenn das Schema für die Identität zwischen apps unterscheidet, da apps verschiedene Identity-Versionen verwenden, ist nicht in der Regel gemeinsame Nutzung einer allgemeinen Datenbank basierend auf die neueste Version der Identität möglich ohne neuzuordnung und Hinzufügen von Spalten in anderen app Identität Schemas.</span><span class="sxs-lookup"><span data-stu-id="f35c4-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="f35c4-166">Häufig ist es effizienter, aktualisieren die anderen apps, um die neueste Version der Identität zu verwenden, sodass eine allgemeine Datenbank von den apps gemeinsam genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="f35c4-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f35c4-167">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="f35c4-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
