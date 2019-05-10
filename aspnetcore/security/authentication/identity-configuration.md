---
title: Konfigurieren von ASP.NET Core-Identität
author: AdrienTorris
description: Verstehen Sie ASP.NET Core Identity-Standardwerte, und erfahren Sie, wie so konfigurieren Sie die Identitätseigenschaften, um benutzerdefinierte Werte verwenden.
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 823182bed2cb953e07f9374d135868aeb2be9c60
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892347"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="817e1-103">Konfigurieren von ASP.NET Core-Identität</span><span class="sxs-lookup"><span data-stu-id="817e1-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="817e1-104">ASP.NET Core Identity verwendet Standardwerte für Einstellungen wie z. B. Kennwortrichtlinien, kontosperrung und Cookie-Konfiguration.</span><span class="sxs-lookup"><span data-stu-id="817e1-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="817e1-105">Diese Einstellungen können überschrieben werden, der `Startup` Klasse.</span><span class="sxs-lookup"><span data-stu-id="817e1-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="817e1-106">Identity-Optionen</span><span class="sxs-lookup"><span data-stu-id="817e1-106">Identity options</span></span>

<span data-ttu-id="817e1-107">Die [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) -Klasse stellt die Optionen, die zum Konfigurieren des Identity-Systems verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="817e1-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="817e1-108">`IdentityOptions` nutno nastavit **nach** Aufrufen `AddIdentity` oder `AddDefaultIdentity`.</span><span class="sxs-lookup"><span data-stu-id="817e1-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="817e1-109">Anspruchsbasierte Identität</span><span class="sxs-lookup"><span data-stu-id="817e1-109">Claims Identity</span></span>

<span data-ttu-id="817e1-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) gibt an, die [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) mit den Eigenschaften, die in der folgenden Tabelle gezeigt.</span><span class="sxs-lookup"><span data-stu-id="817e1-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="817e1-111">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="817e1-111">Property</span></span> | <span data-ttu-id="817e1-112">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-112">Description</span></span> | <span data-ttu-id="817e1-113">Standard</span><span class="sxs-lookup"><span data-stu-id="817e1-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="817e1-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="817e1-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="817e1-115">Übernimmt oder bestimmt die für einen Rollenanspruch verwendeten Anspruchstyp.</span><span class="sxs-lookup"><span data-stu-id="817e1-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="817e1-116">ClaimTypes.Role</span><span class="sxs-lookup"><span data-stu-id="817e1-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="817e1-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="817e1-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="817e1-118">Übernimmt oder bestimmt die für den Stempel sicherheitsanspruch verwendeten Anspruchstyp.</span><span class="sxs-lookup"><span data-stu-id="817e1-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="817e1-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="817e1-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="817e1-120">Übernimmt oder bestimmt die für die Benutzer-ID-Anspruch verwendeten Anspruchstyp.</span><span class="sxs-lookup"><span data-stu-id="817e1-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="817e1-121">ClaimTypes.NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="817e1-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="817e1-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="817e1-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="817e1-123">Übernimmt oder bestimmt den Anspruchstyp für den Namensanspruch des Benutzers verwendet.</span><span class="sxs-lookup"><span data-stu-id="817e1-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="817e1-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="817e1-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="817e1-125">Kontosperrung</span><span class="sxs-lookup"><span data-stu-id="817e1-125">Lockout</span></span>

<span data-ttu-id="817e1-126">Sperre wird festgelegt, der [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) Methode:</span><span class="sxs-lookup"><span data-stu-id="817e1-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="817e1-127">Der vorangehende Code basiert auf der `Login` Identity-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="817e1-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="817e1-128">Sperre Optionen werden in festgelegt `StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="817e1-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="817e1-129">Im obigen Code wird die [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit Standardwerten.</span><span class="sxs-lookup"><span data-stu-id="817e1-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="817e1-130">Eine erfolgreiche Authentifizierung setzt die Anzahl der fehlgeschlagenen Zugriffe Versuche und setzt die Uhr.</span><span class="sxs-lookup"><span data-stu-id="817e1-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="817e1-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) gibt an, die [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) mit den Eigenschaften, die in der Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="817e1-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="817e1-132">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="817e1-132">Property</span></span> | <span data-ttu-id="817e1-133">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-133">Description</span></span> | <span data-ttu-id="817e1-134">Standard</span><span class="sxs-lookup"><span data-stu-id="817e1-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="817e1-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="817e1-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="817e1-136">Bestimmt, ob ein neuer Benutzer gesperrt werden kann.</span><span class="sxs-lookup"><span data-stu-id="817e1-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="817e1-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="817e1-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="817e1-138">Die Zeitspanne ist ein Benutzer gesperrt, wenn eine Sperre auftritt.</span><span class="sxs-lookup"><span data-stu-id="817e1-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="817e1-139">5 Minuten</span><span class="sxs-lookup"><span data-stu-id="817e1-139">5 minutes</span></span> |
| [<span data-ttu-id="817e1-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="817e1-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="817e1-141">Die Anzahl fehlerhafter Zugriffsversuche, bis ein Benutzer gesperrt ist, wenn die Sperre aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="817e1-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="817e1-142">5</span><span class="sxs-lookup"><span data-stu-id="817e1-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="817e1-143">Kennwort</span><span class="sxs-lookup"><span data-stu-id="817e1-143">Password</span></span>

<span data-ttu-id="817e1-144">Standardmäßig erfordert Identität an, dass die Kennwörter, einen Großbuchstaben, Kleinbuchstaben, eine Ziffer und ein nicht alphanumerisches Zeichen enthalten.</span><span class="sxs-lookup"><span data-stu-id="817e1-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="817e1-145">Kennwörter müssen mindestens sechs Zeichen lang sein.</span><span class="sxs-lookup"><span data-stu-id="817e1-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="817e1-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) kann festgelegt werden, `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="817e1-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="817e1-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) gibt an, die [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) mit den Eigenschaften, die in der Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="817e1-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="817e1-148">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="817e1-148">Property</span></span> | <span data-ttu-id="817e1-149">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-149">Description</span></span> | <span data-ttu-id="817e1-150">Standard</span><span class="sxs-lookup"><span data-stu-id="817e1-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="817e1-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="817e1-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="817e1-152">Erfordert eine Zahl zwischen 0-9, das Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="817e1-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="817e1-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="817e1-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="817e1-154">Die Mindestlänge des Kennworts.</span><span class="sxs-lookup"><span data-stu-id="817e1-154">The minimum length of the password.</span></span> | <span data-ttu-id="817e1-155">6</span><span class="sxs-lookup"><span data-stu-id="817e1-155">6</span></span> |
| [<span data-ttu-id="817e1-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="817e1-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="817e1-157">Erfordert ein Kleinbuchstabe, das Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="817e1-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="817e1-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="817e1-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="817e1-159">Ist ein nicht alphanumerisches Zeichen im Kennwort erforderlich.</span><span class="sxs-lookup"><span data-stu-id="817e1-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="817e1-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="817e1-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="817e1-161">Gilt nur für ASP.NET Core 2.0 oder höher aus.</span><span class="sxs-lookup"><span data-stu-id="817e1-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="817e1-162">Ist die Anzahl der unterschiedlichen Zeichen im Kennwort erforderlich.</span><span class="sxs-lookup"><span data-stu-id="817e1-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="817e1-163">1</span><span class="sxs-lookup"><span data-stu-id="817e1-163">1</span></span> |
| [<span data-ttu-id="817e1-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="817e1-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="817e1-165">Ist ein Großbuchstabe Zeichen im Kennwort erforderlich.</span><span class="sxs-lookup"><span data-stu-id="817e1-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="817e1-166">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="817e1-166">Property</span></span> | <span data-ttu-id="817e1-167">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-167">Description</span></span> | <span data-ttu-id="817e1-168">Standard</span><span class="sxs-lookup"><span data-stu-id="817e1-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="817e1-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="817e1-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="817e1-170">Erfordert eine Zahl zwischen 0-9, das Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="817e1-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="817e1-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="817e1-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="817e1-172">Die Mindestlänge des Kennworts.</span><span class="sxs-lookup"><span data-stu-id="817e1-172">The minimum length of the password.</span></span> | <span data-ttu-id="817e1-173">6</span><span class="sxs-lookup"><span data-stu-id="817e1-173">6</span></span> |
| [<span data-ttu-id="817e1-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="817e1-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="817e1-175">Erfordert ein Kleinbuchstabe, das Kennwort an.</span><span class="sxs-lookup"><span data-stu-id="817e1-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="817e1-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="817e1-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="817e1-177">Ist ein nicht alphanumerisches Zeichen im Kennwort erforderlich.</span><span class="sxs-lookup"><span data-stu-id="817e1-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="817e1-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="817e1-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="817e1-179">Ist ein Großbuchstabe Zeichen im Kennwort erforderlich.</span><span class="sxs-lookup"><span data-stu-id="817e1-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="817e1-180">Anmelden</span><span class="sxs-lookup"><span data-stu-id="817e1-180">Sign-in</span></span>

<span data-ttu-id="817e1-181">Im folgenden code wird `SignIn` Einstellungen (Standardwerte):</span><span class="sxs-lookup"><span data-stu-id="817e1-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="817e1-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) gibt an, die [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) mit den Eigenschaften, die in der Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="817e1-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="817e1-183">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="817e1-183">Property</span></span> | <span data-ttu-id="817e1-184">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-184">Description</span></span> | <span data-ttu-id="817e1-185">Standard</span><span class="sxs-lookup"><span data-stu-id="817e1-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="817e1-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="817e1-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="817e1-187">Erfordert eine bestätigte e-Mail-Adresse für die Anmeldung an.</span><span class="sxs-lookup"><span data-stu-id="817e1-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="817e1-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="817e1-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="817e1-189">Erfordert eine bestätigte Telefonnummer für die Anmeldung an.</span><span class="sxs-lookup"><span data-stu-id="817e1-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="817e1-190">tokens</span><span class="sxs-lookup"><span data-stu-id="817e1-190">Tokens</span></span>

<span data-ttu-id="817e1-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) gibt an, die [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) mit den Eigenschaften, die in der Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="817e1-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="817e1-192">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="817e1-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="817e1-193">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="817e1-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="817e1-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="817e1-195">Übernimmt oder bestimmt den `AuthenticatorTokenProvider` verwendet, um zwei-Faktor-Anmeldungen mit einem Authentifikator überprüfen.</span><span class="sxs-lookup"><span data-stu-id="817e1-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="817e1-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="817e1-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="817e1-197">Übernimmt oder bestimmt den `ChangeEmailTokenProvider` verwendet zum Generieren von Token, die in e-Mail-Adresse ändern Bestätigungs-e-Mails verwendet.</span><span class="sxs-lookup"><span data-stu-id="817e1-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="817e1-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="817e1-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="817e1-199">Übernimmt oder bestimmt die `ChangePhoneNumberTokenProvider` verwendet zum Generieren von Tokens verwendet, wenn die Telefonnummern zu ändern.</span><span class="sxs-lookup"><span data-stu-id="817e1-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="817e1-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="817e1-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="817e1-201">Übernimmt oder bestimmt den Tokenanbieter, der zum Generieren von Tokens, die im Konto Bestätigungs-e-Mails verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="817e1-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="817e1-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="817e1-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="817e1-203">Ruft ab oder legt die [IUserTwoFactorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) verwendet zum Generieren von Token, die im Kennwort zurücksetzen der e-Mail-Nachrichten verwendet.</span><span class="sxs-lookup"><span data-stu-id="817e1-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="817e1-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="817e1-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="817e1-205">Zum Erstellen einer [Benutzer Tokenanbieter](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) mit dem Schlüssel verwendet wird, wie der Name des Anbieters.</span><span class="sxs-lookup"><span data-stu-id="817e1-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="817e1-206">Benutzer</span><span class="sxs-lookup"><span data-stu-id="817e1-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="817e1-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) gibt an, die [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) mit den Eigenschaften, die in der Tabelle dargestellt.</span><span class="sxs-lookup"><span data-stu-id="817e1-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="817e1-208">Eigenschaft</span><span class="sxs-lookup"><span data-stu-id="817e1-208">Property</span></span> | <span data-ttu-id="817e1-209">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-209">Description</span></span> | <span data-ttu-id="817e1-210">Standard</span><span class="sxs-lookup"><span data-stu-id="817e1-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="817e1-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="817e1-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="817e1-212">Zulässige Zeichen des Benutzernamens.</span><span class="sxs-lookup"><span data-stu-id="817e1-212">Allowed characters in the username.</span></span> | <span data-ttu-id="817e1-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="817e1-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="817e1-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="817e1-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="817e1-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="817e1-215">0123456789</span></span><br><span data-ttu-id="817e1-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="817e1-216">-.\_@+</span></span> |
| [<span data-ttu-id="817e1-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="817e1-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="817e1-218">Müssen alle Benutzer eine eindeutige e-Mail-Adresse haben.</span><span class="sxs-lookup"><span data-stu-id="817e1-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="817e1-219">Cookie-Einstellungen</span><span class="sxs-lookup"><span data-stu-id="817e1-219">Cookie settings</span></span>

<span data-ttu-id="817e1-220">Konfigurieren Sie in der app-Cookie `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="817e1-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="817e1-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) muss aufgerufen werden, **nach** Aufrufen `AddIdentity` oder `AddDefaultIdentity`.</span><span class="sxs-lookup"><span data-stu-id="817e1-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="817e1-222">Weitere Informationen finden Sie unter [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span><span class="sxs-lookup"><span data-stu-id="817e1-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="817e1-223">Hasher-Kennwortoptionen</span><span class="sxs-lookup"><span data-stu-id="817e1-223">Password Hasher options</span></span>

<span data-ttu-id="817e1-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> Abrufen und Festlegen von Optionen für die Kennwort-hashing.</span><span class="sxs-lookup"><span data-stu-id="817e1-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="817e1-225">Option</span><span class="sxs-lookup"><span data-stu-id="817e1-225">Option</span></span> | <span data-ttu-id="817e1-226">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="817e1-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="817e1-227">Der Kompatibilitätsmodus verwendet, wenn neue Kennwörter hashing.</span><span class="sxs-lookup"><span data-stu-id="817e1-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="817e1-228">Wird standardmäßig auf <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3> festgelegt.</span><span class="sxs-lookup"><span data-stu-id="817e1-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="817e1-229">Das erste Byte ein Hashwert des Kennworts, bezeichnet einen *Format Marker*, gibt die Version des Hashalgorithmus verwendet, um die kennworthashsynchronisierung.</span><span class="sxs-lookup"><span data-stu-id="817e1-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="817e1-230">Bei der Überprüfung der ein Kennwort mit einem Hash, der <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> Methode wählt die korrekten Algorithmus basierend auf dem ersten Byte.</span><span class="sxs-lookup"><span data-stu-id="817e1-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="817e1-231">Ein Client ist, unabhängig davon, ob authentifizieren können, von denen wurde Version des Algorithmus verwendet, um die kennworthashsynchronisierung.</span><span class="sxs-lookup"><span data-stu-id="817e1-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="817e1-232">Einstellen des Kompatibilitätsmodus wirkt sich auf das hashing von *neue Kennwörter*.</span><span class="sxs-lookup"><span data-stu-id="817e1-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="817e1-233">Die Anzahl der Iterationen, die beim hashing von Kennwörtern mit PBKDF2 verwendet werden soll.</span><span class="sxs-lookup"><span data-stu-id="817e1-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="817e1-234">Dieser Wert wird nur verwendet, wenn die <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> nastaven NA hodnotu <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span><span class="sxs-lookup"><span data-stu-id="817e1-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="817e1-235">Der Wert muss eine positive ganze Zahl und der Standardwert ist `10000`.</span><span class="sxs-lookup"><span data-stu-id="817e1-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="817e1-236">Im folgenden Beispiel die <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> nastaven NA hodnotu `12000` in `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="817e1-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
