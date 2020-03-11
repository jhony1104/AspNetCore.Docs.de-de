---
title: Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie die QR-Codegenerierung für TOTP Authenticator-Apps aktivieren, die mit ASP.net Core zweistufigen Authentifizierung funktionieren.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: a7fdc86b3fe94e714e5147c89a32fce13757d1c1
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654157"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="8485e-103">Aktivieren der QR-Code Generierung für TOTP Authenticator-apps in ASP.net Core</span><span class="sxs-lookup"><span data-stu-id="8485e-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="8485e-104">Für QR-Codes ist ASP.net Core 2,0 oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="8485e-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8485e-105">ASP.net Core wird mit Unterstützung für authentifizierungsanwendungen für die individuelle Authentifizierung ausgeliefert.</span><span class="sxs-lookup"><span data-stu-id="8485e-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="8485e-106">Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, verwenden eine zeitbasierte Einmalkennwort Kennwort Algorithmus (TOTP), sind der empfohlene Ansatz für 2FA Branche.</span><span class="sxs-lookup"><span data-stu-id="8485e-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="8485e-107">2FA TOTP mit SMS 2FA vorzuziehen ist.</span><span class="sxs-lookup"><span data-stu-id="8485e-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="8485e-108">Eine Authentifikator-App bietet einen 6-bis achtstelligen Code, den Benutzer nach der Bestätigung Ihres Benutzernamens und Kennworts eingeben müssen.</span><span class="sxs-lookup"><span data-stu-id="8485e-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="8485e-109">In der Regel wird eine Authentifikator-App auf einem Smartphone installiert.</span><span class="sxs-lookup"><span data-stu-id="8485e-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="8485e-110">Die ASP.net Core Web-App-Vorlagen unterstützen Authentifikatoren, bieten jedoch keine Unterstützung für die QRCode-Generierung.</span><span class="sxs-lookup"><span data-stu-id="8485e-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="8485e-111">QRCode-Generatoren vereinfachen das Einrichten von 2FA.</span><span class="sxs-lookup"><span data-stu-id="8485e-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="8485e-112">Dieses Dokument führt Sie durch das Hinzufügen der [QR-Code](https://wikipedia.org/wiki/QR_code) Generierung zur Seite "2FA-Konfiguration".</span><span class="sxs-lookup"><span data-stu-id="8485e-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="8485e-113">Die zweistufige Authentifizierung erfolgt nicht mithilfe eines externen Authentifizierungs Anbieters, z. b. [Google](xref:security/authentication/google-logins) oder [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="8485e-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="8485e-114">Externe Anmeldungen werden durch einen beliebigen Mechanismus geschützt, den der externe Anmelde Anbieter bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="8485e-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="8485e-115">Beachten Sie, dass der [Microsoft](xref:security/authentication/microsoft-logins) -Authentifizierungs Anbieter beispielsweise einen Hardwareschlüssel oder einen anderen 2FA-Ansatz erfordert.</span><span class="sxs-lookup"><span data-stu-id="8485e-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="8485e-116">Wenn die Standardvorlagen "local" 2FA erzwingen, müssten Benutzer zwei 2FA-Ansätze erfüllen. Dies ist kein häufig verwendetes Szenario.</span><span class="sxs-lookup"><span data-stu-id="8485e-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="8485e-117">Hinzufügen von QR-Codes zur Seite "2FA-Konfiguration"</span><span class="sxs-lookup"><span data-stu-id="8485e-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="8485e-118">In diesen Anweisungen wird *QRCode. js* aus dem https://davidshimjs.github.io/qrcodejs/-Repository verwendet.</span><span class="sxs-lookup"><span data-stu-id="8485e-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="8485e-119">Laden Sie die [JavaScript-Bibliothek QRCode. js](https://davidshimjs.github.io/qrcodejs/) in den Ordner `wwwroot\lib` in Ihrem Projekt herunter.</span><span class="sxs-lookup"><span data-stu-id="8485e-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="8485e-120">Befolgen Sie die Anweisungen unter [Gerüst Identität](xref:security/authentication/scaffold-identity) , um */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*zu generieren.</span><span class="sxs-lookup"><span data-stu-id="8485e-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="8485e-121">Suchen Sie in */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*den `Scripts` Abschnitt am Ende der Datei:</span><span class="sxs-lookup"><span data-stu-id="8485e-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="8485e-122">Suchen Sie in *pages/Account/Manage/enableauthenticator. cshtml* (Razor Pages) oder *views/Manage/enableauthenticator. cshtml* (MVC) den Abschnitt `Scripts` am Ende der Datei:</span><span class="sxs-lookup"><span data-stu-id="8485e-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="8485e-123">Aktualisieren Sie den `Scripts` Abschnitt, um einen Verweis auf die hinzugefügte `qrcodejs` Bibliothek und einen Befehl zum Generieren des QR-Codes hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="8485e-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="8485e-124">Dies sollte folgendermaßen aussehen:</span><span class="sxs-lookup"><span data-stu-id="8485e-124">It should look as follows:</span></span>

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")

    <script type="text/javascript" src="~/lib/qrcode.js"></script>
    <script type="text/javascript">
        new QRCode(document.getElementById("qrCode"),
            {
                text: "@Html.Raw(Model.AuthenticatorUri)",
                width: 150,
                height: 150
            });
    </script>
}
```

* <span data-ttu-id="8485e-125">Löschen Sie den Absatz, der Sie mit diesen Anweisungen verknüpft.</span><span class="sxs-lookup"><span data-stu-id="8485e-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="8485e-126">Führen Sie Ihre APP aus, und stellen Sie sicher, dass Sie den QR-Code Scannen und den Code validieren können, den der Authentifikator</span><span class="sxs-lookup"><span data-stu-id="8485e-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="8485e-127">Ändern des Website namens im QR-Code</span><span class="sxs-lookup"><span data-stu-id="8485e-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="8485e-128">Der Name der Website im QR-Code stammt aus dem Projektnamen, den Sie beim ersten Erstellen des Projekts auswählen.</span><span class="sxs-lookup"><span data-stu-id="8485e-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="8485e-129">Sie können dies ändern, indem Sie in der */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*-Methode nach der `GenerateQrCodeUri(string email, string unformattedKey)`-Methode suchen.</span><span class="sxs-lookup"><span data-stu-id="8485e-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="8485e-130">Der Name der Website im QR-Code stammt aus dem Projektnamen, den Sie beim ersten Erstellen des Projekts auswählen.</span><span class="sxs-lookup"><span data-stu-id="8485e-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="8485e-131">Sie können dies ändern, indem Sie die `GenerateQrCodeUri(string email, string unformattedKey)`-Methode in der Datei *pages/Account/Manage/enableauthenticator. cshtml. cs* (Razor Pages) oder *Controller/managecontroller. cs* (MVC) suchen.</span><span class="sxs-lookup"><span data-stu-id="8485e-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="8485e-132">Der Standard Code aus der Vorlage sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="8485e-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenticatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="8485e-133">Der zweite Parameter im `string.Format` aufrufen ist Ihr Website Name, der aus dem Projektmappennamen entnommen wurde.</span><span class="sxs-lookup"><span data-stu-id="8485e-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="8485e-134">Sie kann in einen beliebigen Wert geändert werden, muss jedoch immer URL-codiert sein.</span><span class="sxs-lookup"><span data-stu-id="8485e-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="8485e-135">Verwenden einer anderen QR-Code Bibliothek</span><span class="sxs-lookup"><span data-stu-id="8485e-135">Using a different QR Code library</span></span>

<span data-ttu-id="8485e-136">Sie können die QR-Code Bibliothek durch Ihre bevorzugte Bibliothek ersetzen.</span><span class="sxs-lookup"><span data-stu-id="8485e-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="8485e-137">Der HTML-Code enthält ein `qrCode` Element, in das Sie einen QR-Code mit jedem Mechanismus platzieren können, den Ihre Bibliothek bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="8485e-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="8485e-138">Die korrekt formatierte URL für den QR-Code ist in verfügbar:</span><span class="sxs-lookup"><span data-stu-id="8485e-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="8485e-139">`AuthenticatorUri` Eigenschaft des Modells.</span><span class="sxs-lookup"><span data-stu-id="8485e-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="8485e-140">`data-url`-Eigenschaft im `qrCodeData`-Element.</span><span class="sxs-lookup"><span data-stu-id="8485e-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="8485e-141">TOTP-Client-und Serverzeit Abweichung</span><span class="sxs-lookup"><span data-stu-id="8485e-141">TOTP client and server time skew</span></span>

<span data-ttu-id="8485e-142">TOTP (zeitbasierte einmalige Kenn Wort Authentifizierung) hängt vom Server-und Authentifikator-Gerät ab, das über eine genaue Zeit verfügt.</span><span class="sxs-lookup"><span data-stu-id="8485e-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="8485e-143">Token sind nur 30 Sekunden lang.</span><span class="sxs-lookup"><span data-stu-id="8485e-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="8485e-144">Wenn TOTP 2FA-Anmeldungen fehlschlagen, überprüfen Sie, ob die Serverzeit genau ist und vorzugsweise mit einem genauen NTP-Dienst synchronisiert wird.</span><span class="sxs-lookup"><span data-stu-id="8485e-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
