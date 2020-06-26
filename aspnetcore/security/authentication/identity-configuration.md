---
title: Konfigurieren von ASP.net CoreIdentity
author: AdrienTorris
description: Verstehen Identity Sie ASP.net Core Standardwerte, und erfahren Sie, wie Sie Identity Eigenschaften für die Verwendung von benutzerdefinierten Werten konfigurieren.
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: 95c19b671602b45ba217dcb551110854cbbee359
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408967"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="dcd8b-103">Konfigurieren von ASP.net CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="dcd8b-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="dcd8b-104">In ASP.net Core werden Identity Standardwerte für Einstellungen verwendet, z. b. Kenn Wort Richtlinie, Sperrung und Konfiguration von Cookies.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="dcd8b-105">Diese Einstellungen können in der-Klasse überschrieben werden `Startup` .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a>Identity<span data-ttu-id="dcd8b-106">Optionen</span><span class="sxs-lookup"><span data-stu-id="dcd8b-106"> options</span></span>

<span data-ttu-id="dcd8b-107">Die [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) -Klasse stellt die Optionen dar, die verwendet werden können, um das System zu konfigurieren Identity .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="dcd8b-108">`IdentityOptions`muss **nach** dem Aufrufen von oder festgelegt werden `AddIdentity` `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="dcd8b-109">ClaimsIdentity</span><span class="sxs-lookup"><span data-stu-id="dcd8b-109">Claims Identity</span></span>

<span data-ttu-id="dcd8b-110">" [Identityoptions. ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) " gibt die " [claimsidentityoptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) " mit den Eigenschaften an, die in der folgenden Tabelle aufgeführt sind.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="dcd8b-111">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="dcd8b-111">Property</span></span> | <span data-ttu-id="dcd8b-112">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-112">Description</span></span> | <span data-ttu-id="dcd8b-113">Standard</span><span class="sxs-lookup"><span data-stu-id="dcd8b-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="dcd8b-114">Roleclaimtype</span><span class="sxs-lookup"><span data-stu-id="dcd8b-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="dcd8b-115">Ruft den für einen Rollen Anspruch verwendeten Anspruchstyp ab oder legt ihn fest.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="dcd8b-116">ClaimTypes. Role</span><span class="sxs-lookup"><span data-stu-id="dcd8b-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="dcd8b-117">Securitystampclaimtype</span><span class="sxs-lookup"><span data-stu-id="dcd8b-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="dcd8b-118">Ruft den für den sicherheitstokenanspruch verwendeten Anspruchstyp ab oder legt ihn fest.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="dcd8b-119">Useridclaimtype</span><span class="sxs-lookup"><span data-stu-id="dcd8b-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="dcd8b-120">Ruft den für den benutzerbezeichneranspruch verwendeten Anspruchstyp ab oder legt ihn fest.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="dcd8b-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="dcd8b-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="dcd8b-122">Usernameclaimtype</span><span class="sxs-lookup"><span data-stu-id="dcd8b-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="dcd8b-123">Ruft den für den Benutzernamens Anspruch verwendeten Anspruchstyp ab oder legt ihn fest.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="dcd8b-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="dcd8b-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="dcd8b-125">Sperrung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-125">Lockout</span></span>

<span data-ttu-id="dcd8b-126">Die Sperre wird in der [passwordsigninasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) -Methode festgelegt:</span><span class="sxs-lookup"><span data-stu-id="dcd8b-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="dcd8b-127">Der vorangehende Code basiert auf der `Login` Identity Vorlage.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="dcd8b-128">Sperrungs Optionen werden in festgelegt `StartUp.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dcd8b-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="dcd8b-129">Der vorangehende Code legt die [identityoptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) - [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den Standardwerten fest.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="dcd8b-130">Bei erfolgreicher Authentifizierung wird die Anzahl fehlerhafter Zugriffsversuche zurückgesetzt, und die Uhr wird zurückgesetzt.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="dcd8b-131">[Identityoptions. Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) gibt die [lockoutoptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den in der Tabelle angezeigten Eigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="dcd8b-132">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="dcd8b-132">Property</span></span> | <span data-ttu-id="dcd8b-133">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-133">Description</span></span> | <span data-ttu-id="dcd8b-134">Standard</span><span class="sxs-lookup"><span data-stu-id="dcd8b-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="dcd8b-135">"Zuweisen"</span><span class="sxs-lookup"><span data-stu-id="dcd8b-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="dcd8b-136">Bestimmt, ob ein neuer Benutzer gesperrt werden kann.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="dcd8b-137">Defaultlockouttimespan</span><span class="sxs-lookup"><span data-stu-id="dcd8b-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="dcd8b-138">Die Zeitspanne, für die ein Benutzer gesperrt wird, wenn eine Sperre auftritt.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="dcd8b-139">5 Minuten</span><span class="sxs-lookup"><span data-stu-id="dcd8b-139">5 minutes</span></span> |
| [<span data-ttu-id="dcd8b-140">Maxfailedaccessversuchs</span><span class="sxs-lookup"><span data-stu-id="dcd8b-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="dcd8b-141">Die Anzahl der fehlgeschlagenen Zugriffsversuche, bis ein Benutzer gesperrt ist, wenn die Sperre aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="dcd8b-142">5</span><span class="sxs-lookup"><span data-stu-id="dcd8b-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="dcd8b-143">Kennwort</span><span class="sxs-lookup"><span data-stu-id="dcd8b-143">Password</span></span>

<span data-ttu-id="dcd8b-144">Standardmäßig Identity erfordert, dass Kenn Wörter einen Großbuchstaben, einen Kleinbuchstaben, eine Ziffer und ein nicht alphanumerisches Zeichen enthalten.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="dcd8b-145">Kenn Wörter müssen mindestens sechs Zeichen lang sein.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="dcd8b-146">[Passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) kann in festgelegt werden `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="dcd8b-147">[Identityoptions. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) gibt die [passwordoptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) -Eigenschaft mit den Eigenschaften an, die in der Tabelle angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="dcd8b-148">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="dcd8b-148">Property</span></span> | <span data-ttu-id="dcd8b-149">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-149">Description</span></span> | <span data-ttu-id="dcd8b-150">Standard</span><span class="sxs-lookup"><span data-stu-id="dcd8b-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="dcd8b-151">Requirements-Ziffer</span><span class="sxs-lookup"><span data-stu-id="dcd8b-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="dcd8b-152">Erfordert eine Zahl zwischen 0-9 und dem Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="dcd8b-153">Requirements dlength</span><span class="sxs-lookup"><span data-stu-id="dcd8b-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="dcd8b-154">Die Mindestlänge des Kennworts.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-154">The minimum length of the password.</span></span> | <span data-ttu-id="dcd8b-155">6</span><span class="sxs-lookup"><span data-stu-id="dcd8b-155">6</span></span> |
| [<span data-ttu-id="dcd8b-156">Requirements lowercase</span><span class="sxs-lookup"><span data-stu-id="dcd8b-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="dcd8b-157">Erfordert einen Kleinbuchstaben im Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="dcd8b-158">Anforderungs alphanumerisch</span><span class="sxs-lookup"><span data-stu-id="dcd8b-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="dcd8b-159">Erfordert ein nicht alphanumerisches Zeichen im Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="dcd8b-160">Requirements duniquechars</span><span class="sxs-lookup"><span data-stu-id="dcd8b-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="dcd8b-161">Gilt nur für ASP.net Core 2,0 oder höher.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="dcd8b-162">Erfordert die Anzahl der unterschiedlichen Zeichen im Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="dcd8b-163">1</span><span class="sxs-lookup"><span data-stu-id="dcd8b-163">1</span></span> |
| [<span data-ttu-id="dcd8b-164">Erforderlich (Großbuchstaben)</span><span class="sxs-lookup"><span data-stu-id="dcd8b-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="dcd8b-165">Erfordert ein Großbuchstaben im Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="dcd8b-166">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="dcd8b-166">Property</span></span> | <span data-ttu-id="dcd8b-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-167">Description</span></span> | <span data-ttu-id="dcd8b-168">Standard</span><span class="sxs-lookup"><span data-stu-id="dcd8b-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="dcd8b-169">Requirements-Ziffer</span><span class="sxs-lookup"><span data-stu-id="dcd8b-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="dcd8b-170">Erfordert eine Zahl zwischen 0-9 und dem Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="dcd8b-171">Requirements dlength</span><span class="sxs-lookup"><span data-stu-id="dcd8b-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="dcd8b-172">Die Mindestlänge des Kennworts.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-172">The minimum length of the password.</span></span> | <span data-ttu-id="dcd8b-173">6</span><span class="sxs-lookup"><span data-stu-id="dcd8b-173">6</span></span> |
| [<span data-ttu-id="dcd8b-174">Requirements lowercase</span><span class="sxs-lookup"><span data-stu-id="dcd8b-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="dcd8b-175">Erfordert einen Kleinbuchstaben im Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="dcd8b-176">Anforderungs alphanumerisch</span><span class="sxs-lookup"><span data-stu-id="dcd8b-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="dcd8b-177">Erfordert ein nicht alphanumerisches Zeichen im Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="dcd8b-178">Erforderlich (Großbuchstaben)</span><span class="sxs-lookup"><span data-stu-id="dcd8b-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="dcd8b-179">Erfordert ein Großbuchstaben im Kennwort.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="dcd8b-180">Anmeldung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-180">Sign-in</span></span>

<span data-ttu-id="dcd8b-181">Mit dem folgenden Code werden Einstellungen festgelegt `SignIn` (auf Standardwerte):</span><span class="sxs-lookup"><span data-stu-id="dcd8b-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="dcd8b-182">[Identityoptions. SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) gibt die [signinoptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) -Eigenschaft mit den in der Tabelle angezeigten Eigenschaften an.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="dcd8b-183">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="dcd8b-183">Property</span></span> | <span data-ttu-id="dcd8b-184">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-184">Description</span></span> | <span data-ttu-id="dcd8b-185">Standard</span><span class="sxs-lookup"><span data-stu-id="dcd8b-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="dcd8b-186">"Requirements confirmede Mail"</span><span class="sxs-lookup"><span data-stu-id="dcd8b-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="dcd8b-187">Erfordert eine bestätigte e-Mail-Adresse für die Anmeldung.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="dcd8b-188">Requirements confirmedphonenumber</span><span class="sxs-lookup"><span data-stu-id="dcd8b-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="dcd8b-189">Erfordert eine bestätigte Telefonnummer, um sich anzumelden.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="dcd8b-190">Token</span><span class="sxs-lookup"><span data-stu-id="dcd8b-190">Tokens</span></span>

<span data-ttu-id="dcd8b-191">[Identityoptions. Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) gibt die [tokenoptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) mit den Eigenschaften an, die in der Tabelle angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="dcd8b-192">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="dcd8b-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="dcd8b-193">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="dcd8b-194">Authenticenortokenprovider</span><span class="sxs-lookup"><span data-stu-id="dcd8b-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="dcd8b-195">Ruft den ab `AuthenticatorTokenProvider` , der zum Überprüfen von zweistufigen Anmeldungen mit einem Authentifikator verwendet wird, oder legt diesen fest.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="dcd8b-196">Changeemailstokenprovider</span><span class="sxs-lookup"><span data-stu-id="dcd8b-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="dcd8b-197">Ruft den `ChangeEmailTokenProvider` zum Generieren von Token ab, die in e-Mail-Bestätigungs-e-Mails verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="dcd8b-198">Changephonenumberumkenprovider</span><span class="sxs-lookup"><span data-stu-id="dcd8b-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="dcd8b-199">Ruft den ab `ChangePhoneNumberTokenProvider` , der zum Generieren von Token verwendet wird, die beim Ändern von Telefonnummern verwendet werden</span><span class="sxs-lookup"><span data-stu-id="dcd8b-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="dcd8b-200">Emailconfirmationstokenprovider</span><span class="sxs-lookup"><span data-stu-id="dcd8b-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="dcd8b-201">Ruft den Tokenanbieter ab, der zum Generieren von Token verwendet wird, die in e-Mail-Konto Bestätigungs</span><span class="sxs-lookup"><span data-stu-id="dcd8b-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="dcd8b-202">Passwordresetstokenprovider</span><span class="sxs-lookup"><span data-stu-id="dcd8b-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="dcd8b-203">Ruft den [iusertwofaktortokenprovider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) ab, der zum Generieren von Token verwendet wird, die in e-Mails zur Kenn Wort Zurücksetzung verwendet werden</span><span class="sxs-lookup"><span data-stu-id="dcd8b-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="dcd8b-204">Providermap</span><span class="sxs-lookup"><span data-stu-id="dcd8b-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="dcd8b-205">Wird verwendet, um einen [benutzertokenanbieter](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) mit dem Schlüssel zu erstellen, der als Name des Anbieters verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="dcd8b-206">Benutzer</span><span class="sxs-lookup"><span data-stu-id="dcd8b-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="dcd8b-207">[Identityoptions. User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) gibt die [User Options](/dotnet/api/microsoft.aspnetcore.identity.useroptions) mit den Eigenschaften an, die in der Tabelle angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="dcd8b-208">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="dcd8b-208">Property</span></span> | <span data-ttu-id="dcd8b-209">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="dcd8b-209">Description</span></span> | <span data-ttu-id="dcd8b-210">Standard</span><span class="sxs-lookup"><span data-stu-id="dcd8b-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="dcd8b-211">Zuordnung von "Zuweisung"</span><span class="sxs-lookup"><span data-stu-id="dcd8b-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="dcd8b-212">Zulässige Zeichen im Benutzernamen.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-212">Allowed characters in the username.</span></span> | <span data-ttu-id="dcd8b-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="dcd8b-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="dcd8b-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="dcd8b-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="dcd8b-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="dcd8b-215">0123456789</span></span><br><span data-ttu-id="dcd8b-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="dcd8b-216">-.\_@+</span></span> |
| [<span data-ttu-id="dcd8b-217">Requirements-e-Mail</span><span class="sxs-lookup"><span data-stu-id="dcd8b-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="dcd8b-218">Erfordert, dass jeder Benutzer eine eindeutige e-Mail-Adresse hat.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="dcd8b-219">Cookie-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="dcd8b-219">Cookie settings</span></span>

<span data-ttu-id="dcd8b-220">Konfigurieren Sie das Cookie der app in `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dcd8b-221">" [Applireapplicationcookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) " muss **nach** dem Aufrufen von oder aufgerufen werden `AddIdentity` `AddDefaultIdentity` .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="dcd8b-222">Weitere Informationen finden Sie unter [cookieauthenticationoptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="dcd8b-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="dcd8b-223">Optionen für Kennwort Hasher</span><span class="sxs-lookup"><span data-stu-id="dcd8b-223">Password Hasher options</span></span>

<span data-ttu-id="dcd8b-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>Ruft Optionen für Kenn Wort Hashwert ab und legt Sie fest.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="dcd8b-225">Option</span><span class="sxs-lookup"><span data-stu-id="dcd8b-225">Option</span></span> | <span data-ttu-id="dcd8b-226">BESCHREIBUNG</span><span class="sxs-lookup"><span data-stu-id="dcd8b-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="dcd8b-227">Der beim hashup neuer Kenn Wörter verwendete Kompatibilitätsmodus.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="dcd8b-228">Wird standardmäßig auf <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="dcd8b-229">Das erste Byte eines Hash Kennworts, das als *Format Marker*bezeichnet wird, gibt die Version des Hash Algorithmus an, mit dem das Kennwort als Hash verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="dcd8b-230">Wenn ein Kennwort mit einem Hash überprüft wird, <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> wählt die Methode den richtigen Algorithmus basierend auf dem ersten Byte aus.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="dcd8b-231">Ein Client kann authentifiziert werden, unabhängig davon, welche Version des Algorithmus zum Hashen des Kennworts verwendet wurde.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="dcd8b-232">Das Festlegen des Kompatibilitätsmodus wirkt sich auf das hashup *neuer Kenn Wörter*aus.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="dcd8b-233">Die Anzahl der Iterationen, die beim Hashwert von Kenn Wörtern mithilfe von PBKDF2 verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="dcd8b-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="dcd8b-234">Dieser Wert wird nur verwendet, wenn <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> auf festgelegt ist <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="dcd8b-235">Der Wert muss eine positive ganze Zahl sein, und der Standardwert ist `10000` .</span><span class="sxs-lookup"><span data-stu-id="dcd8b-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="dcd8b-236">Im folgenden Beispiel <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> wird auf in festgelegt `12000` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dcd8b-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
