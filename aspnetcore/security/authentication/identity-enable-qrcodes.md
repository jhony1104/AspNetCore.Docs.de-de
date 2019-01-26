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
# <a name="enable-qr-code-generation-for-totp-authenticator-apps-in-aspnet-core"></a>Aktivieren der QR-Code-Generierung für Authentifikator-apps in ASP.NET Core TOTP

::: moniker range="<= aspnetcore-2.0"

Qr-Codes ist ASP.NET Core 2.0 oder höher erforderlich.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Im Lieferumfang von ASP.NET Core ist Support für Authenticator-Anwendungen für die einzelne Authentifizierung. Zwei Faktor-Authentifizierung (2FA) Authentifikator-apps, verwenden eine zeitbasierte Einmalkennwort Kennwort Algorithmus (TOTP), sind der empfohlene Ansatz für 2FA Branche. 2FA TOTP mit SMS 2FA vorzuziehen ist. Eine Authentifikator-app bietet einen 6 bis 8 Ziffern-Code, der die Benutzer nach der Bestätigung ihres Benutzernamens und Kennworts eingeben müssen. In der Regel wird eine Authentifikator-app auf einem Smartphone installiert.

Die ASP.NET Core-Web-app-Vorlagen unterstützen von Authentifikatoren, aber Sie bieten keine Unterstützung für QRCode Generation. QRCode Generatoren vereinfachen die Einrichtung der 2FA. Dieses Dokument führt Sie durch Hinzufügen von [QR-Code](https://wikipedia.org/wiki/QR_code) Generierung auf der Konfigurationsseite 2FA.

Zweistufige Authentifizierung erfolgt nicht mithilfe eines externen Authentifizierungsanbieters, z. B. [Google](xref:security/authentication/google-logins) oder [Facebook](xref:security/authentication/facebook-logins). Externe Anmeldungen sind geschützt durch Mechanismus der externen Anmeldeanbieter bietet. Betrachten Sie z. B. die [Microsoft](xref:security/authentication/microsoft-logins) Authentication-Anbieter erfordert einen Hardwareschlüssel oder ein anderer 2FA Ansatz. Wenn die Standardvorlagen "local" 2FA erzwungen ist Benutzer erforderlich sein, erfüllt zwei 2FA Ansätze, die nicht auf ein häufig verwendetes Szenario ist.

## <a name="adding-qr-codes-to-the-2fa-configuration-page"></a>Hinzufügen von QR-Codes auf der Konfigurationsseite für 2FA

Verwenden Sie diese Anweisungen *qrcode.js* aus der https://davidshimjs.github.io/qrcodejs/ Repository.

* Herunterladen der [qrcode.js Javascript-Bibliothek](https://davidshimjs.github.io/qrcodejs/) auf die `wwwroot\lib` -Ordner des Projekts.

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* Befolgen Sie die Anweisungen in [Gerüst Identität](xref:security/authentication/scaffold-identity) generieren */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.
* In */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*, suchen Sie die `Scripts` Abschnitt am Ende der Datei:

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* In *Pages/Account/Manage/EnableAuthenticator.cshtml* (Razor Pages) oder *Views/Manage/EnableAuthenticator.cshtml* (MVC), suchen Sie die `Scripts` Abschnitt am Ende der Datei:

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

```cshtml
@section Scripts {
    @await Html.PartialAsync("_ValidationScriptsPartial")
}
```

* Update der `Scripts` Abschnitt aus, um das Hinzufügen eines Verweises auf die `qrcodejs` Bibliothek, die Sie hinzugefügt haben und ein Aufruf zum Generieren des QR-Codes. Es sollte wie folgt aussehen:

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

* Löschen Sie den Absatz, der Sie mit diesen Anweisungen verknüpft ist.

Führen Sie die app aus, und stellen Sie sicher, dass Sie können den QR-Code Scannen und überprüfen den Code, den der Authentifikator beweist.

## <a name="change-the-site-name-in-the-qr-code"></a>Ändern Sie den Namen der Website in den QR-Code

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

Der Websitename in den QR-Code stammt aus den Namen des Projekts, die Sie auswählen, wenn das Projekt zuerst erstellen. Sie können ändern, indem Sie die Suche nach der `GenerateQrCodeUri(string email, string unformattedKey)` -Methode in der die */Areas/Identity/Pages/Account/Manage/EnableAuthenticator.cshtml*.

::: moniker-end

::: moniker range="= aspnetcore-2.0"

Der Websitename in den QR-Code stammt aus den Namen des Projekts, die Sie auswählen, wenn das Projekt zuerst erstellen. Können Sie Sie ändern, indem Sie die Suche nach der `GenerateQrCodeUri(string email, string unformattedKey)` -Methode in der die *Pages/Account/Manage/EnableAuthenticator.cshtml.cs* (Razor Pages)-Datei oder das *Controllers/ManageController.cs* (MVC)-Datei.

::: moniker-end

::: moniker range=">= aspnetcore-2.0"

Der Standardcode aus der Vorlage sieht wie folgt aus:

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

Der zweite Parameter im Aufruf von `string.Format` der Standortname, der den Namen der Projektmappe entnommen wird. Sie können auf einen beliebigen Wert geändert werden, aber es muss immer ein URL-codiert sein.

## <a name="using-a-different-qr-code-library"></a>Verwenden einer anderen QR-Code-Bibliothek

Sie können den QR-Code-Bibliothek mit Ihrer bevorzugten Bibliothek ersetzen. Der HTML-Code enthält eine `qrCode` Element in der Sie einen QR-Code, indem Mechanismus platzieren können Ihrer Bibliothek enthält.

Die korrekt formatierte URL für den QR-Code finden Sie in der:

* `AuthenticatorUri` die Eigenschaft des Modells.
* `data-url` Eigenschaft in der `qrCodeData` Element.

## <a name="totp-client-and-server-time-skew"></a>TOTP-Client und Server zeitabweichung auf.

TOTP (zeitbasierte Einmalkennwort) Authentifizierung hängt von dem Server und dem Authentifikator Gerät müssen die genaue Uhrzeit ab. Token, nur 30 Sekunden dauern. TOTP 2FA Anmeldungen fehlschlagen, sicher, dass die Serverzeit genaue und vorzugsweise mit einer genauen NTP-Dienst synchronisiert wird.

::: moniker-end
