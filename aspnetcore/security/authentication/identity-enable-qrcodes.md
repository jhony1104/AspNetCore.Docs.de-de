---
title: Aktivieren der QR-Code-Generierung für Authentifikator-apps in ASP.NET Core TOTP
author: rick-anderson
description: Erfahren Sie, wie QR-codegenerierung für TOTP-Authentifikator-apps zu aktivieren, die mit ASP.NET Core-zwei-Faktor-Authentifizierung verwendet.
ms.author: riande
ms.date: 08/14/2018
uid: security/authentication/identity-enable-qrcodes
ms.openlocfilehash: cf99cc21a7a1bb4d01c7cc092106d23375a1a76f
ms.sourcegitcommit: ca5f03210bedc61c6639a734ae5674bfe095dee8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55073126"
---
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a><span data-ttu-id="45a6b-103">Aktivieren der QR-Code-Generierung für Authentifikator-apps in ASP.NET Core TOTP</span><span class="sxs-lookup"><span data-stu-id="45a6b-103">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="45a6b-104">Qr-Codes ist ASP.NET Core 2.0 oder höher erforderlich.</span><span class="sxs-lookup"><span data-stu-id="45a6b-104">QR Codes requires ASP.NET Core 2.0 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="45a6b-105">Im Lieferumfang von ASP.NET Core ist Support für Authenticator-Anwendungen für die einzelne Authentifizierung.</span><span class="sxs-lookup"><span data-stu-id="45a6b-105">ASP.NET Core ships with support for authenticator applications for individual authentication.</span></span> <span data-ttu-id="45a6b-106">Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, verwenden eine zeitbasierte Einmalkennwort Kennwort Algorithmus (TOTP), sind der empfohlene Ansatz für 2FA Branche.</span><span class="sxs-lookup"><span data-stu-id="45a6b-106">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="45a6b-107">2FA TOTP mit SMS 2FA vorzuziehen ist.</span><span class="sxs-lookup"><span data-stu-id="45a6b-107">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="45a6b-108">Eine Authentifikator-app bietet einen 6 bis 8 Ziffern-Code, der die Benutzer nach der Bestätigung ihres Benutzernamens und Kennworts eingeben müssen.</span><span class="sxs-lookup"><span data-stu-id="45a6b-108">An authenticator app provides a 6 to 8 digit code which users must enter after confirming their username and password.</span></span> <span data-ttu-id="45a6b-109">In der Regel wird eine Authentifikator-app auf einem Smartphone installiert.</span><span class="sxs-lookup"><span data-stu-id="45a6b-109">Typically an authenticator app is installed on a smart phone.</span></span>

<span data-ttu-id="45a6b-110">Die ASP.NET Core-Web-app-Vorlagen unterstützen von Authentifikatoren, aber Sie bieten keine Unterstützung für QRCode Generation.</span><span class="sxs-lookup"><span data-stu-id="45a6b-110">The ASP.NET Core web app templates support authenticators, but don't provide support for QRCode generation.</span></span> <span data-ttu-id="45a6b-111">QRCode Generatoren vereinfachen die Einrichtung der 2FA.</span><span class="sxs-lookup"><span data-stu-id="45a6b-111">QRCode generators ease the setup of 2FA.</span></span> <span data-ttu-id="45a6b-112">Dieses Dokument führt Sie durch Hinzufügen von [QR-Code](https://wikipedia.org/wiki/QR_code) Generierung auf der Konfigurationsseite 2FA.</span><span class="sxs-lookup"><span data-stu-id="45a6b-112">This document will guide you through adding [QR Code](https://wikipedia.org/wiki/QR_code) generation to the 2FA configuration page.</span></span>

<span data-ttu-id="45a6b-113">Zweistufige Authentifizierung erfolgt nicht mithilfe eines externen Authentifizierungsanbieters, z. B. [Google](xref:security/authentication/google-logins) oder [Facebook](xref:security/authentication/facebook-logins).</span><span class="sxs-lookup"><span data-stu-id="45a6b-113">Two factor authentication does not happen using an external authentication provider, such as [Google](xref:security/authentication/google-logins) or [Facebook](xref:security/authentication/facebook-logins).</span></span> <span data-ttu-id="45a6b-114">Externe Anmeldungen sind geschützt durch Mechanismus der externen Anmeldeanbieter bietet.</span><span class="sxs-lookup"><span data-stu-id="45a6b-114">External logins are protected by whatever mechanism the external login provider provides.</span></span> <span data-ttu-id="45a6b-115">Betrachten Sie z. B. die [Microsoft](xref:security/authentication/microsoft-logins) Authentication-Anbieter erfordert einen Hardwareschlüssel oder ein anderer 2FA Ansatz.</span><span class="sxs-lookup"><span data-stu-id="45a6b-115">Consider, for example, the [Microsoft](xref:security/authentication/microsoft-logins) authentication provider requires a hardware key or another 2FA approach.</span></span> <span data-ttu-id="45a6b-116">Wenn die Standardvorlagen "local" 2FA erzwungen ist Benutzer erforderlich sein, erfüllt zwei 2FA Ansätze, die nicht auf ein häufig verwendetes Szenario ist.</span><span class="sxs-lookup"><span data-stu-id="45a6b-116">If the default templates enforced "local" 2FA then users would be required to satisfy two 2FA approaches, which is not a commonly used scenario.</span></span>

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a><span data-ttu-id="45a6b-117">Hinzufügen von QR-Codes auf der Konfigurationsseite für 2FA</span><span class="sxs-lookup"><span data-stu-id="45a6b-117">Adding QR Codes to the 2FA configuration page</span></span>

<span data-ttu-id="45a6b-118">Verwenden Sie diese Anweisungen *qrcode.js* aus der https://davidshimjs.github.io/qrcodejs/ Repository.</span><span class="sxs-lookup"><span data-stu-id="45a6b-118">These instructions use *qrcode.js* from the https://davidshimjs.github.io/qrcodejs/ repo.</span></span>

* <span data-ttu-id="45a6b-119">Herunterladen der [qrcode.js Javascript-Bibliothek](https://davidshimjs.github.io/qrcodejs/) auf die `wwwroot\lib` -Ordner des Projekts.</span><span class="sxs-lookup"><span data-stu-id="45a6b-119">Download the [qrcode.js javascript library](https://davidshimjs.github.io/qrcodejs/) to the `wwwroot\lib` folder in your project.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="45a6b-120">Befolgen Sie die Anweisungen in [Gerüst Identität](xref:security/authentication/scaffold-identity) generieren */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45a6b-120">Follow the instructions in [Scaffold Identity](xref:security/authentication/scaffold-identity) to generate */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>
* <span data-ttu-id="45a6b-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, suchen Sie die `Scripts` Abschnitt am Ende der Datei:</span><span class="sxs-lookup"><span data-stu-id="45a6b-121">In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="45a6b-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) oder *Views/Manage/EnableAuthenticator.cshtml* (MVC), suchen Sie die `Scripts` Abschnitt am Ende der Datei:</span><span class="sxs-lookup"><span data-stu-id="45a6b-122">In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) or *Views/Manage/EnableAuthenticator.cshtml* (MVC), locate the `Scripts` section at the end of the file:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* <span data-ttu-id="45a6b-123">Update der `Scripts` Abschnitt aus, um das Hinzufügen eines Verweises auf die `qrcodejs` Bibliothek, die Sie hinzugefügt haben und ein Aufruf zum Generieren des QR-Codes.</span><span class="sxs-lookup"><span data-stu-id="45a6b-123">Update the `Scripts` section to add a reference to the `qrcodejs` library you added and a call to generate the QR Code.</span></span> <span data-ttu-id="45a6b-124">Es sollte wie folgt aussehen:</span><span class="sxs-lookup"><span data-stu-id="45a6b-124">It should look as follows:</span></span>

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

* <span data-ttu-id="45a6b-125">Löschen Sie den Absatz, der Sie mit diesen Anweisungen verknüpft ist.</span><span class="sxs-lookup"><span data-stu-id="45a6b-125">Delete the paragraph which links you to these instructions.</span></span>

<span data-ttu-id="45a6b-126">Führen Sie die app aus, und stellen Sie sicher, dass Sie können den QR-Code Scannen und überprüfen den Code, den der Authentifikator beweist.</span><span class="sxs-lookup"><span data-stu-id="45a6b-126">Run your app and ensure that you can scan the QR code and validate the code the authenticator proves.</span></span>

## <a name="change-the-site-name-in-the-qr-code"></a><span data-ttu-id="45a6b-127">Ändern Sie den Namen der Website in den QR-Code</span><span class="sxs-lookup"><span data-stu-id="45a6b-127">Change the site name in the QR Code</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="45a6b-128">Der Websitename in den QR-Code stammt aus den Namen des Projekts, die Sie auswählen, wenn das Projekt zuerst erstellen.</span><span class="sxs-lookup"><span data-stu-id="45a6b-128">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="45a6b-129">Sie können ändern, indem Sie die Suche nach der `GenerateQrCodeUri(string email, string unformattedKey)` -Methode in der die */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="45a6b-129">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="45a6b-130">Der Websitename in den QR-Code stammt aus den Namen des Projekts, die Sie auswählen, wenn das Projekt zuerst erstellen.</span><span class="sxs-lookup"><span data-stu-id="45a6b-130">The site name in the QR Code is taken from the project name you choose when initially creating your project.</span></span> <span data-ttu-id="45a6b-131">Können Sie Sie ändern, indem Sie die Suche nach der `GenerateQrCodeUri(string email, string unformattedKey)` -Methode in der die *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages)-Datei oder das *Controllers/ManageController.cs* (MVC)-Datei.</span><span class="sxs-lookup"><span data-stu-id="45a6b-131">You can change it by looking for the `GenerateQrCodeUri(string email, string unformattedKey)` method in the *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages) file or the *Controllers/ManageController.cs* (MVC) file.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="45a6b-132">Der Standardcode aus der Vorlage sieht wie folgt aus:</span><span class="sxs-lookup"><span data-stu-id="45a6b-132">The default code from the template looks as follows:</span></span>

```csharp
private string GenerateQrCodeUri(string email, string unformattedKey)
{
    return string.Format(
        AuthenicatorUriFormat,
        _urlEncoder.Encode("Razor Pages"),
        _urlEncoder.Encode(email),
        unformattedKey);
}
```

<span data-ttu-id="45a6b-133">Der zweite Parameter im Aufruf von `string.Format` der Standortname, der den Namen der Projektmappe entnommen wird.</span><span class="sxs-lookup"><span data-stu-id="45a6b-133">The second parameter in the call to `string.Format` is your site name, taken from your solution name.</span></span> <span data-ttu-id="45a6b-134">Sie können auf einen beliebigen Wert geändert werden, aber es muss immer ein URL-codiert sein.</span><span class="sxs-lookup"><span data-stu-id="45a6b-134">It can be changed to any value, but it must always be URL encoded.</span></span>

## <a name="using-a-different-qr-code-library"></a><span data-ttu-id="45a6b-135">Verwenden einer anderen QR-Code-Bibliothek</span><span class="sxs-lookup"><span data-stu-id="45a6b-135">Using a different QR Code library</span></span>

<span data-ttu-id="45a6b-136">Sie können den QR-Code-Bibliothek mit Ihrer bevorzugten Bibliothek ersetzen.</span><span class="sxs-lookup"><span data-stu-id="45a6b-136">You can replace the QR Code library with your preferred library.</span></span> <span data-ttu-id="45a6b-137">Der HTML-Code enthält eine `qrCode` Element in der Sie einen QR-Code, indem Mechanismus platzieren können Ihrer Bibliothek enthält.</span><span class="sxs-lookup"><span data-stu-id="45a6b-137">The HTML contains a `qrCode` element into which you can place a QR Code by whatever mechanism your library provides.</span></span>

<span data-ttu-id="45a6b-138">Die korrekt formatierte URL für den QR-Code finden Sie in der:</span><span class="sxs-lookup"><span data-stu-id="45a6b-138">The correctly formatted URL for the QR Code is available in the:</span></span>

* <span data-ttu-id="45a6b-139">`AuthenticatorUri` die Eigenschaft des Modells.</span><span class="sxs-lookup"><span data-stu-id="45a6b-139">`AuthenticatorUri` property of the model.</span></span>
* <span data-ttu-id="45a6b-140">`data-url` Eigenschaft in der `qrCodeData` Element.</span><span class="sxs-lookup"><span data-stu-id="45a6b-140">`data-url` property in the `qrCodeData` element.</span></span>

## <a name="totp-client-and-server-time-skew"></a><span data-ttu-id="45a6b-141">TOTP-Client und Server zeitabweichung auf.</span><span class="sxs-lookup"><span data-stu-id="45a6b-141">TOTP client and server time skew</span></span>

<span data-ttu-id="45a6b-142">TOTP (zeitbasierte Einmalkennwort) Authentifizierung hängt von dem Server und dem Authentifikator Gerät müssen die genaue Uhrzeit ab.</span><span class="sxs-lookup"><span data-stu-id="45a6b-142">TOTP (Time-based One-Time Password) authentication depends on both the server and authenticator device having an accurate time.</span></span> <span data-ttu-id="45a6b-143">Token, nur 30 Sekunden dauern.</span><span class="sxs-lookup"><span data-stu-id="45a6b-143">Tokens only last for 30 seconds.</span></span> <span data-ttu-id="45a6b-144">TOTP 2FA Anmeldungen fehlschlagen, sicher, dass die Serverzeit genaue und vorzugsweise mit einer genauen NTP-Dienst synchronisiert wird.</span><span class="sxs-lookup"><span data-stu-id="45a6b-144">If TOTP 2FA logins are failing, check that the server time is accurate, and preferably synchronized to an accurate NTP service.</span></span>

::: moniker-end
