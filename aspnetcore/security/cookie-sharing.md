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
# <a name="share-authentication-cookies-among-aspnet-apps"></a><span data-ttu-id="1d4a9-103">Freigeben von Authentifizierungs Cookies zwischen ASP.net-apps</span><span class="sxs-lookup"><span data-stu-id="1d4a9-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="1d4a9-104">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1d4a9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="1d4a9-105">Websites bestehen häufig aus einzelnen Webanwendungen, die zusammenarbeiten.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="1d4a9-106">Um eine einmalige Anmeldung (Single Sign-on, SSO) zu ermöglichen, müssen Web-Apps innerhalb einer Website Authentifizierungs Cookies gemeinsam verwenden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="1d4a9-107">Zur Unterstützung dieses Szenarios ermöglicht der Datenschutz Stapel die gemeinsame Verwendung von Katana-Cookie-Authentifizierung und ASP.net Core Cookie-Authentifizierungs Tickets.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="1d4a9-108">In den folgenden Beispielen:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-108">In the examples that follow:</span></span>

* <span data-ttu-id="1d4a9-109">Der Name des Authentifizierungs Cookies wird auf einen gemeinsamen Wert `.AspNet.SharedCookie`von festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="1d4a9-110">Der `AuthenticationType` wird entweder explizit `Identity.Application` oder standardmäßig auf festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="1d4a9-111">Ein allgemeiner App-Name wird verwendet, um dem Datenschutzsystem das Freigeben von Datenschutz`SharedCookieApp`Schlüsseln () zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="1d4a9-112">`Identity.Application`wird als Authentifizierungsschema verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="1d4a9-113">Welches Schema verwendet wird, muss konsistent *innerhalb und in* den freigegebenen Cookie-Apps verwendet werden, entweder als Standardschema oder explizit festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="1d4a9-114">Das Schema wird beim Verschlüsseln und Entschlüsseln von Cookies verwendet. Daher muss ein konsistentes Schema zwischen Apps verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="1d4a9-115">Es wird ein allgemeiner Speicherort für den [Datenschutz Schlüssel](xref:security/data-protection/implementation/key-management) verwendet.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="1d4a9-116">In ASP.net Core-apps <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> wird verwendet, um den Speicherort des Schlüssels festzulegen.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="1d4a9-117">In .NET Framework-Apps verwendet die cookesauthentifizierungs-Middleware <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>eine Implementierung von.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="1d4a9-118">`DataProtectionProvider`stellt Datenschutzdienste zum Verschlüsseln und Entschlüsseln von Nutzungsdaten von Authentifizierungs Cookies bereit.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="1d4a9-119">Die `DataProtectionProvider` -Instanz ist von dem Datenschutzsystem isoliert, das von anderen Teilen der APP verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="1d4a9-120">[Dataschutzprovider. Create (System. IO. Director yinfo, Action\<idataschutzbuilder >)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) akzeptiert einen <xref:System.IO.DirectoryInfo> , um den Speicherort für den Datenschutz Schlüssel zu bestimmen.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="1d4a9-121">`DataProtectionProvider`erfordert das nuget-Paket " [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) ":</span><span class="sxs-lookup"><span data-stu-id="1d4a9-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="1d4a9-122">Verweisen Sie in ASP.net Core 2. x-apps auf das [Metapaket "Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)".</span><span class="sxs-lookup"><span data-stu-id="1d4a9-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="1d4a9-123">Fügen Sie in .NET Framework-apps einen Paket Verweis auf [Microsoft. aspnetcore. dataprotection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)hinzu.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="1d4a9-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Legt den Namen der allgemeinen App fest.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-cookies-among-aspnet-core-apps"></a><span data-ttu-id="1d4a9-125">Freigeben von Authentifizierungs Cookies zwischen ASP.net Core-apps</span><span class="sxs-lookup"><span data-stu-id="1d4a9-125">Share authentication cookies among ASP.NET Core apps</span></span>

<span data-ttu-id="1d4a9-126">Wenn Sie ASP.net Core Identität verwenden:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="1d4a9-127">Datenschutz Schlüssel und der App-Name müssen von den apps gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="1d4a9-128">In den folgenden Beispielen wird die <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> -Methode mit einem allgemeinen Schlüssel Speicherort bereitgestellt.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="1d4a9-129">Verwenden <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> Sie, um einen gemeinsamen freigegebenen`SharedCookieApp` APP-Namen (in den folgenden Beispielen) zu konfigurieren.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="1d4a9-130">Weitere Informationen finden Sie unter <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="1d4a9-131">Verwenden Sie <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> die-Erweiterungsmethode, um den Datenschutz Dienst für Cookies einzurichten.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="1d4a9-132">Der Standard Authentifizierungstyp ist `Identity.Application`.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="1d4a9-133">In `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem({PATH TO COMMON KEY RING FOLDER})
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="1d4a9-134">Wenn Sie Cookies direkt ohne ASP.net Core Identität verwenden, konfigurieren Sie den Datenschutz `Startup.ConfigureServices`und die Authentifizierung in.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-134">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="1d4a9-135">Im folgenden Beispiel wird der Authentifizierungstyp auf `Identity.Application`festgelegt:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-135">In the following example, the authentication type is set to `Identity.Application`:</span></span>

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

<span data-ttu-id="1d4a9-136">Geben Sie beim Hosting von Apps, die Cookies in Unterdomänen freigeben, eine allgemeine Domäne in der Eigenschaft [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) an.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-136">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="1d4a9-137">Geben `contoso.com` `first_subdomain.contoso.com` `second_subdomain.contoso.com` `.contoso.com`Sie zum Freigeben von Cookies für mehrere apps unter, wie z. b. und, Folgendes an: `Cookie.Domain`</span><span class="sxs-lookup"><span data-stu-id="1d4a9-137">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="1d4a9-138">Verschlüsseln von Datenschutz Schlüsseln im Ruhezustand</span><span class="sxs-lookup"><span data-stu-id="1d4a9-138">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="1d4a9-139">Konfigurieren Sie für Produktions Bereitstellungen den `DataProtectionProvider` so, dass Schlüssel im Ruhezustand mit DPAPI oder einem X509Certificate verschlüsselt werden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-139">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="1d4a9-140">Weitere Informationen finden Sie unter <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-140">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="1d4a9-141">Im folgenden Beispiel wird ein Zertifikat Fingerabdruck für <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>Folgendes bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-141">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-cookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="1d4a9-142">Freigeben von Authentifizierungs Cookies zwischen ASP.NET 4. x-und ASP.net Core-apps</span><span class="sxs-lookup"><span data-stu-id="1d4a9-142">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="1d4a9-143">ASP.NET 4. x-apps, die Katana-cookieauthentifizierungs-Middleware verwenden, können so konfiguriert werden, dass mit der ASP.net Core cookieauthentifizierungs-Middleware kompatible Authentifizierungs Cookies generiert werden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-143">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="1d4a9-144">Dies ermöglicht die Aktualisierung der einzelnen apps eines großen Standorts in mehreren Schritten und bietet gleichzeitig eine reibungslose SSO-Funktion auf der Website.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-144">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="1d4a9-145">Wenn eine APP eine Katana-Cookie-Authentifizierungs Middleware verwendet `UseCookieAuthentication` , ruft Sie in der *Startup.auth.cs* -Datei des Projekts auf.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-145">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="1d4a9-146">ASP.NET 4. x-Web-App-Projekte, die mit Visual Studio 2013 und höher erstellt wurden, verwenden standardmäßig die Katana-Cookie-Authentifizierungs Middleware.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-146">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="1d4a9-147">Obwohl `UseCookieAuthentication` veraltet ist und für ASP.net Core-apps nicht unterstützt `UseCookieAuthentication` wird, ist der Aufruf von in einer ASP.NET 4. x-APP, die Katana-cookeauthentifizierungs-Middleware verwendet, gültig</span><span class="sxs-lookup"><span data-stu-id="1d4a9-147">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="1d4a9-148">Eine ASP.NET 4. x-APP muss auf .NET Framework 4.5.1 oder höher ausgerichtet sein.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-148">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="1d4a9-149">Andernfalls können die erforderlichen nuget-Pakete nicht installiert werden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-149">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="1d4a9-150">Wenn Sie Authentifizierungs Cookies zwischen einer ASP.NET 4. x-APP und einer ASP.net Core-App freigeben möchten, konfigurieren Sie die ASP.net Core-App gemäß den Informationen im Abschnitt [Freigeben von Authentifizierungs Cookies unter ASP.net Core apps](#share-authentication-cookies-among-aspnet-core-apps) , und konfigurieren Sie dann die ASP.NET 4. x-APP wie folgt.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-150">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-among-aspnet-core-apps) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="1d4a9-151">Vergewissern Sie sich, dass die Pakete der APP auf die neuesten Versionen aktualisiert werden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-151">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="1d4a9-152">Installieren Sie das [Microsoft. owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) -Paket in jeder ASP.NET 4. x-app.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-152">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="1d4a9-153">Suchen und ändern Sie den- `UseCookieAuthentication`Befehl an:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-153">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="1d4a9-154">Ändern Sie den Namen des Cookies so, dass er mit dem von der ASP.net Core cookenauthentifizierungs-Middleware (`.AspNet.SharedCookie` im Beispiel) verwendeten Namen identisch ist.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-154">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="1d4a9-155">Im folgenden Beispiel wird der Authentifizierungstyp auf `Identity.Application`festgelegt.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-155">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="1d4a9-156">Geben Sie eine Instanz von `DataProtectionProvider` an, die für den Common Data Protection Key-Speicherort initialisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-156">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="1d4a9-157">Vergewissern Sie sich, dass der Name der APP auf den allgemeinen APP-Namen festgelegt ist, der von`SharedCookieApp` allen Apps verwendet wird, die Authentifizierungs Cookies verwenden (im Beispiel).</span><span class="sxs-lookup"><span data-stu-id="1d4a9-157">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="1d4a9-158">Wenn Sie und `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`nicht festlegen, <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> legen Sie auf einen Anspruch fest, der eindeutige Benutzer unterscheidet.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-158">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="1d4a9-159">*App_Start/Startup. auth. cs*:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-159">*App_Start/Startup.Auth.cs*:</span></span>

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

<span data-ttu-id="1d4a9-160">Beim Erstellen einer Benutzeridentität muss`Identity.Application`der Authentifizierungstyp () mit dem Typ identisch sein, der in `AuthenticationType` Set with `UseCookieAuthentication` in *App_Start/Startup. auth. cs*definiert ist.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-160">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="1d4a9-161">*Models/identitymodels. cs*:</span><span class="sxs-lookup"><span data-stu-id="1d4a9-161">*Models/IdentityModels.cs*:</span></span>

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

## <a name="use-a-common-user-database"></a><span data-ttu-id="1d4a9-162">Verwenden einer allgemeinen Benutzerdatenbank</span><span class="sxs-lookup"><span data-stu-id="1d4a9-162">Use a common user database</span></span>

<span data-ttu-id="1d4a9-163">Wenn apps dasselbe Identitäts Schema verwenden (dieselbe Identitäts Version), vergewissern Sie sich, dass das Identitätssystem für jede APP auf dieselbe Benutzerdatenbank verweist.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-163">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="1d4a9-164">Andernfalls erzeugt das Identitätssystem Fehler zur Laufzeit, wenn versucht wird, die Informationen im Authentifizierungs Cookie mit den Informationen in der Datenbank abzugleichen.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-164">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="1d4a9-165">Wenn das Identitäts Schema von apps abweicht, in der Regel, weil apps verschiedene Identitäts Versionen verwenden, ist die gemeinsame Nutzung einer gemeinsamen Datenbank auf der Grundlage der aktuellen Identitäts Version nicht möglich, ohne dass Spalten in den Identitäts Schemas anderer apps neu zugeordnet und hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-165">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="1d4a9-166">Häufig ist es effizienter, die anderen apps so zu aktualisieren, dass Sie die neueste Identitäts Version verwenden, damit eine gemeinsame Datenbank von den apps gemeinsam genutzt werden kann.</span><span class="sxs-lookup"><span data-stu-id="1d4a9-166">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1d4a9-167">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="1d4a9-167">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
