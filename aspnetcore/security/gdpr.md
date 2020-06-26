---
title: Unterstützung für Datenschutz-Grundverordnung (dsgvo) in ASP.net Core
author: rick-anderson
description: Erfahren Sie, wie Sie in einer ASP.net Core Web-App auf die dsgvo-Erweiterungs Punkte zugreifen.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/gdpr
ms.openlocfilehash: 0fef14388f83d8b4c708194954f2b75b2164b746
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404495"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Unterstützung der EU-Datenschutz-Grundverordnung (dsgvo) in ASP.net Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.net Core bietet APIs und Vorlagen, mit denen einige der Anforderungen der [EU-Datenschutz-Grundverordnung (](https://ec.europa.eu/info/law/law-topic/data-protection/reform/what-does-general-data-protection-regulation-gdpr-govern_en) dsgvo) erfüllt werden können:

::: moniker range=">= aspnetcore-3.0"

* Die Projektvorlagen enthalten Erweiterungs Punkte und Stub-Markup, die Sie durch Ihre Datenschutz-und Cookie-Nutzungsrichtlinie ersetzen können.
* Die Seite *pages/Privacy. cshtml* oder *views/Home/Privacy. cshtml* enthält eine Seite, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.

So aktivieren Sie das standardmäßige Cookie-Zustimmungs Feature wie das in den ASP.net Core 2,2-Vorlagen in einer von ASP.net Core 3,0-Vorlagen generierten App:

* Fügen Sie `using Microsoft.AspNetCore.Http` der Liste der using-Direktiven hinzu.
* Fügen Sie [cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) zu `Startup.ConfigureServices` und [usecookiepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) hinzu `Startup.Configure` :

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Fügen Sie die Cookie-Zustimmung partiell der Datei *_Layout. cshtml* hinzu:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Fügen Sie dem Projekt die Datei * \_ cookiegenehmipartial. cshtml* hinzu:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Wählen Sie die ASP.net Core 2,2-Version dieses Artikels aus, um Informationen über die Cookie-Zustimmungs Funktion zu erhalten.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Die Projektvorlagen enthalten Erweiterungs Punkte und Stub-Markup, die Sie durch Ihre Datenschutz-und Cookie-Nutzungsrichtlinie ersetzen können.
* Mit der Funktion "Cookie-Zustimmung" können Sie Ihre Benutzer für die Speicherung persönlicher Informationen auffordern (und nachverfolgen). Wenn ein Benutzer der Datensammlung nicht zugestimmt hat und für die APP [checkgenehmigbar](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) auf festgelegt ist `true` , werden nicht erforderliche Cookies nicht an den Browser gesendet.
* Cookies können als unverzichtbar gekennzeichnet werden. Wichtige Cookies werden auch dann an den Browser gesendet, wenn der Benutzer nicht zugestimmt hat und die Nachverfolgung deaktiviert ist.
* [TempData-und Sitzungs Cookies](#tempdata) sind nicht funktionsfähig, wenn die Nachverfolgung deaktiviert ist.
* Die Seite [ Identity Verwalten](#pd) bietet einen Link zum herunterladen und Löschen von Benutzerdaten.

Mit der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) können Sie die meisten dsgvo-Erweiterungs Punkte und APIs testen, die den ASP.net Core 2,1-Vorlagen hinzugefügt wurden. Weitere Informationen finden Sie [in der](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) Infodatei.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.net Core dsgvo-Unterstützung in Vorlagen generiertem Code

RazorSeiten und MVC-Projekte, die mit den Projektvorlagen erstellt wurden, enthalten die folgende dsgvo-Unterstützung:

* [Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) und [usecookiepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) werden in der- `Startup` Klasse festgelegt.
* Die [Teilansicht](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper) * \_ cookiegenehmipartial. cshtml* . Eine **Accept** -Schaltfläche ist in dieser Datei enthalten. Wenn der Benutzer auf die Schaltfläche **annehmen** klickt, wird die Zustimmung zum Speichern von Cookies bereitgestellt.
* Die Seite *pages/Privacy. cshtml* oder *views/Home/Privacy. cshtml* enthält eine Seite, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können. Die * \_ cookiegenehmipartial. cshtml* -Datei generiert einen Link zur Datenschutzseite.
* Für apps, die mit einzelnen Benutzerkonten erstellt wurden, stellt die Seite verwalten Links zum herunterladen und Löschen [persönlicher Benutzerdaten](#pd)bereit.

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>Cookiepolicyoptions und usecookiepolicy

[Cookiepolicyoptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) werden in initialisiert `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[Usecookiepolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) wird in aufgerufen `Startup.Configure` :

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="_cookieconsentpartialcshtml-partial-view"></a>\_Cookieeinvernehmliche partielle. cshtml-Teilansicht

Die Teilansicht * \_ cookiegenehmipartial. cshtml* :

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Diese partielle:

* Ruft den Status der Nachverfolgung für den Benutzer ab. Wenn die APP so konfiguriert ist, dass Sie Zustimmung erfordert, muss der Benutzer zustimmen, bevor Cookies nachverfolgt werden können. Wenn die Zustimmung erforderlich ist, wird der Bereich für die Cookie-Zustimmung am oberen Rand der Navigationsleiste, die von der Datei " * \_ Layout. cshtml* " erstellt wurde, korrigiert.
* Stellt ein HTML `<p>` -Element bereit, um Ihre Datenschutz-und Richtlinien zur Verwendung von Cookies
* Enthält einen Link zur Datenschutzseite oder-Ansicht, auf der Sie die Datenschutzrichtlinie Ihrer Website detailliert erläutern können.

## <a name="essential-cookies"></a>Wichtige Cookies

Wenn die Zustimmung zum Speichern von Cookies nicht angegeben wurde, werden nur Cookies, die als unverzichtbar gekennzeichnet sind, an den Browser gesendet. Mit dem folgenden Code wird ein Cookie benötigt:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData-Anbieter und Sitzungs Zustands Cookies sind nicht zwingend erforderlich.

Das Cookie des [TempData-Anbieters](xref:fundamentals/app-state#tempdata) ist nicht zwingend erforderlich. Wenn die Nachverfolgung deaktiviert ist, ist der TempData-Anbieter nicht funktionsfähig. Um den TempData-Anbieter zu aktivieren, wenn die Nachverfolgung deaktiviert ist, markieren Sie den TempData-Cookie in `Startup.ConfigureServices` :

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Sitzungs Zustands](xref:fundamentals/app-state) Cookies sind nicht zwingend erforderlich. Der Sitzungszustand ist nicht funktionsfähig, wenn die Überwachung deaktiviert ist. Der folgende Code macht Sitzungs Cookies unverzichtbar:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Personenbezogene Daten

ASP.net Core apps, die mit einzelnen Benutzerkonten erstellt wurden, enthalten Code zum herunterladen und Löschen von persönlichen Daten.

Wählen Sie den Benutzernamen aus, und wählen Sie dann **persönliche Daten**aus:

![Seite "persönliche Daten verwalten"](gdpr/_static/pd.png)

Notizen:

* Informationen zum Generieren des `Account/Manage` Codes finden Sie unter [ Identity Gerüstbau ](xref:security/authentication/scaffold-identity).
* Die Links zum **Löschen** und **herunterladen** wirken sich nur auf die Standard Identitätsdaten aus. Apps, die benutzerdefinierte Benutzerdaten erstellen, müssen erweitert werden, um die benutzerdefinierten Benutzerdaten zu löschen/herunterzuladen. Weitere Informationen finden Sie unter [hinzufügen, herunterladen und Löschen von benutzerdefinierten Identity Benutzerdaten in ](xref:security/authentication/add-user-data).
* Gespeicherte Token für den Benutzer, die in der Identity Datenbanktabelle gespeichert sind, `AspNetUserTokens` werden gelöscht, wenn der Benutzer aufgrund des [Fremd Schlüssels](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152)über das kaskadierende Lösch Verhalten gelöscht wird.
* Die [Authentifizierung externer Anbieter](xref:security/authentication/social/index), wie Facebook und Google, ist nicht verfügbar, bevor die Cookie-Richtlinie akzeptiert wird.

::: moniker-end

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Einige Datenbanken und Speicher Mechanismen ermöglichen die Verschlüsselung ruhender Daten. Verschlüsselung ruhender Daten:

* Verschlüsselte Daten werden automatisch verschlüsselt.
* Verschlüsselt, ohne Konfiguration, Programmierung oder andere Aufgaben für die Software, die auf die Daten zugreift.
* Ist die einfachste und sicherste Option.
* Ermöglicht der Datenbank die Verwaltung von Schlüsseln und Verschlüsselung.

Zum Beispiel:

* Microsoft SQL und Azure SQL bieten [transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [Die Datenbank wird von SQL Azure standardmäßig verschlüsselt.](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Azure-blobdateien,-Dateien,-Tabellen und-Queue Storage werden standardmäßig verschlüsselt](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Bei Datenbanken, die keine integrierte Verschlüsselung im Ruhezustand bereitstellen, können Sie die Datenträger Verschlüsselung möglicherweise verwenden, um denselben Schutz zu gewährleisten. Zum Beispiel:

* [BitLocker für Windows Server](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [ecryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs)Ein.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
* [Dsgvo: Hinzufügen einer Schaltfläche zum Widerrufen der Zustimmung in ASP.net Core](https://www.joeaudette.com/blog/2018/08/28/gdpr---adding-a-revoke-consent-button-in-aspnet-core)
