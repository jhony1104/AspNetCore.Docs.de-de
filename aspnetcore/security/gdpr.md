---
title: Datenschutz-Grundverordnung (DSGVO)-Unterstützung in ASP.NET Core
author: rick-anderson
description: Erfahren Sie, wie Sie auf die DSGVO-Erweiterungspunkte in einer ASP.NET Core-Web-app zugreifen.
ms.author: riande
ms.custom: mvc
ms.date: 07/11/2019
uid: security/gdpr
ms.openlocfilehash: 01d2f8943c0995c1400122b89c4ca7c459a85279
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724563"
---
# <a name="eu-general-data-protection-regulation-gdpr-support-in-aspnet-core"></a>Europa Allgemein Datenschutz-Grundverordnung (DSGVO)-Unterstützung in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core bietet APIs und Vorlagen, um einige der erfüllen die [Europa Allgemein Datenschutz-Grundverordnung (DSGVO)](https://www.eugdpr.org/) Anforderungen:

::: moniker range=">= aspnetcore-3.0"

* Die Projektvorlagen enthalten Erweiterungspunkte und liegenden ereignisfelds Markup, das Sie durch den Datenschutz und Cookies verwenden von Richtlinien ersetzen können.
* Die *Pages/Privacy.cshtml* Seite oder *Views/Home/Privacy.cshtml* Ansicht bietet eine Seite Ihrer Website-Datenschutzrichtlinie ausführlich beschrieben.

Um die Cookie-Zustimmung Standardfeature, zu aktivieren, die in die ASP.NET Core-2.2-Vorlagen in einer ASP.NET Core 3.0 generierte Vorlage app gefunden werden:

* Hinzufügen [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) zu `Startup.ConfigureServices` und [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) zu `Startup.Configure`:

  [!code-csharp[Main](gdpr/sample/RP3.0/Startup.cs?name=snippet1&highlight=12-19,38)]

* Fügen Sie die Zustimmung teilweise Cookie, um die *"_Layout.cshtml"* Datei:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_Layout.cshtml?name=snippet&highlight=4)]

* Hinzufügen der  *\_CookieConsentPartial.cshtml* Datei zum Projekt:

  [!code-cshtml[Main](gdpr/sample/RP3.0/Pages/Shared/_CookieConsentPartial.cshtml)]

* Wählen Sie die ASP.NET Core-2.2-Version dieses Artikels zum Feature Zustimmung Cookie gelesen.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* Die Projektvorlagen enthalten Erweiterungspunkte und liegenden ereignisfelds Markup, das Sie durch den Datenschutz und Cookies verwenden von Richtlinien ersetzen können.
* Ein Cookie Zustimmung-Feature können Sie Ihre Zustimmung anfordern (und Nachverfolgen) von Ihren Benutzern zum Speichern von persönlichen Informationen. Wenn ein Benutzer noch nicht, um die Datensammlung zugestimmt und die app verfügt über [CheckConsentNeeded](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions.checkconsentneeded) festgelegt `true`, nicht unbedingt Cookies werden nicht an den Browser gesendet.
* Cookies können als wichtig markiert werden. Wichtige Cookies werden an den Browser gesendet, selbst wenn der Benutzer noch nicht zugestimmt hat und nachverfolgung ist deaktiviert.
* [TempData und Sitzungscookies](#tempdata) nicht funktionsfähig sind, bei der Überwachung deaktiviert ist.
* Die [identitätsverwaltung](#pd) Seite enthält einen Link zum Herunterladen und Löschen von Daten des Benutzers.

Die [Beispiel-app](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) können Sie testen, die meisten der DSGVO Erweiterungspunkte und APIs hinzugefügt, um die Vorlagen für ASP.NET Core 2.1. Finden Sie unter den [Infodatei](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) -Datei zum Testen von Anweisungen.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/gdpr/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="aspnet-core-gdpr-support-in-template-generated-code"></a>ASP.NET Core DSGVO unterstützen, in der Vorlage generierten Codes

Razor-Seiten und MVC-Projekte erstellt, die mit den Projektvorlagen enthalten die folgende DSGVO-Unterstützung:

* [CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) und [UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) in festgelegt sind das `Startup` Klasse.
* Die  *\_CookieConsentPartial.cshtml* [Teilansicht](xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper). Ein **Accept** Schaltfläche befindet sich in dieser Datei. Klickt der Benutzer die **Accept** Schaltfläche, die zum Speichern von Cookies zustimmen wird bereitgestellt.
* Die *Pages/Privacy.cshtml* Seite oder *Views/Home/Privacy.cshtml* Ansicht bietet eine Seite Ihrer Website-Datenschutzrichtlinie ausführlich beschrieben. Die  *\_CookieConsentPartial.cshtml* -Datei generiert einen Link zu die "Datenschutz".
* Für apps, die mit individuellen Benutzerkonten erstellt werden, bietet der Seite "verwalten" Links zum Herunterladen und Löschen von [persönliche Benutzerdaten](#pd).

### <a name="cookiepolicyoptions-and-usecookiepolicy"></a>CookiePolicyOptions und UseCookiePolicy

[CookiePolicyOptions](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyoptions) initialisiert werden `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/Startup.cs?name=snippet1&highlight=14-20)]

[UseCookiePolicy](/dotnet/api/microsoft.aspnetcore.builder.cookiepolicyappbuilderextensions.usecookiepolicy) unter "lautet" `Startup.Configure`:

[!code-csharp[](gdpr/sample/Startup.cs?name=snippet1&highlight=51)]

### <a name="cookieconsentpartialcshtml-partial-view"></a>\_CookieConsentPartial.cshtml Teilansicht

Die  *\_CookieConsentPartial.cshtml* Teilansicht:

[!code-html[](gdpr/sample/RP2.2/Pages/Shared/_CookieConsentPartial.cshtml)]

Diese partielle:

* Ruft den Status der Überwachung für den Benutzer ab. Wenn die app konfiguriert ist, um die Zustimmung erforderlich ist, muss der Benutzer zustimmen, bevor Cookies nachverfolgt werden können. Wenn Zustimmung erforderlich ist, das Cookie Zustimmung der Bereiche am oberen Rand der Navigationsleiste von erstellten fixiert ist die  *\_Layout.cshtml* Datei.
* Stellt eine HTML `<p>` Element, um den Datenschutz und Cookies zusammenzufassen mithilfe der Gruppenrichtlinie.
* Enthält einen Link zu "Datenschutz" oder Ansicht, in denen Sie Ihrer Website-Datenschutzrichtlinie beschreiben können.

## <a name="essential-cookies"></a>Wichtige cookies

Wenn Zustimmung zum Speichern von Cookies wurde nicht angegeben wurde, nur Cookies, die als wichtig markiert werden an den Browser gesendet. Im folgenden Code werden einen Cookie wichtig:

[!code-csharp[Main](gdpr/sample/RP2.2/Pages/Cookie.cshtml.cs?name=snippet1&highlight=5)]

<a name="tempdata"></a>

### <a name="tempdata-provider-and-session-state-cookies-arent-essential"></a>TempData-Anbieter und -Status-Sitzungscookies haben keine Bedeutung

Die [TempData-Anbieters](xref:fundamentals/app-state#tempdata) Cookie nicht wichtig ist. Wenn die Überwachung deaktiviert ist, nicht der TempData-Anbieter. Um der TempData-Anbieter aktivieren, wenn die nachverfolgung deaktiviert ist, Markieren des TempData-Cookies als wesentlich `Startup.ConfigureServices`:

[!code-csharp[Main](gdpr/sample/RP2.2/Startup.cs?name=snippet1)]

[Sitzungsstatus](xref:fundamentals/app-state) Cookies sind nicht wichtig. Sitzungsstatus nicht funktionsfähig, wenn die nachverfolgung deaktiviert ist. Im folgenden Code werden die Sitzungscookies wichtig:

[!code-csharp[](gdpr/sample/RP2.2/Startup.cs?name=snippet2)]

<a name="pd"></a>

## <a name="personal-data"></a>Personenbezogene Daten

Mit einzelnen Benutzerkonten erstellten ASP.NET Core-apps enthalten Code zum Herunterladen und Löschen von personenbezogenen Daten.

Wählen Sie den Benutzernamen ein, und wählen Sie dann **personenbezogene Daten**:

![Verwalten personenbezogener Daten (Seite)](gdpr/_static/pd.png)

Notizen:

* Zum Generieren der `Account/Manage` programmieren, finden Sie unter [Gerüst Identität](xref:security/authentication/scaffold-identity).
* Die **löschen** und **herunterladen** Links dienen nur auf den Standard-Identity-Daten. Apps, die benutzerdefinierten Daten zu erstellen, müssen zum Löschen und die benutzerdefinierten Benutzerdaten herunterladen erweitert werden. Weitere Informationen finden Sie unter [hinzufügen, herunterladen und Löschen von benutzerdefinierten Daten Identität](xref:security/authentication/add-user-data).
* Gespeicherte Token für den Benutzer, die in der Identity-Datenbanktabelle gespeichert sind `AspNetUserTokens` werden gelöscht, wenn der Benutzer, über das kaskadierende Delete Verhalten aufgrund gelöscht wird der [Fremdschlüssel](https://github.com/aspnet/Identity/blob/release/2.1/src/EF/IdentityUserContext.cs#L152).
* [Authentifizierung des externen Anbieter](xref:security/authentication/social/index), z. B. Facebook und Google, nicht zur Verfügung, bevor die Cookierichtlinie akzeptiert wird.

::: moniker-end

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten

Einige Datenbanken und andere Speichermechanismen ermöglichen die Verschlüsselung im Ruhezustand. Verschlüsselung ruhender Daten:

* Gespeicherte Daten verschlüsselt automatisch.
* Verschlüsselt, ohne Konfiguration, Programmierung oder andere Aufgaben für die Software, die die Daten zugreift.
* Ist die einfachste und sicherste Option.
* Kann die Datenbank Verwalten von Schlüsseln und Verschlüsselung.

Zum Beispiel:

* Bereitstellen von Microsoft SQL und Azure SQL [Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE).
* [SQL Azure werden die Datenbank standardmäßig verschlüsselt.](https://azure.microsoft.com/updates/newly-created-azure-sql-databases-encrypted-by-default/)
* [Azure-Blobs, Dateien, Table und Queue Storage standardmäßig verschlüsselt](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).

Für Datenbanken, die integrierte Verschlüsselung ruhender Daten nicht bereitstellen, können Sie datenträgerverschlüsselung zu verwenden, um den gleichen Schutz bereitstellen können. Zum Beispiel:

* [BitLocker für WindowsServer](/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
* Linux:
  * [eCryptfs](https://launchpad.net/ecryptfs)
  * [EncFS](https://github.com/vgough/encfs).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Microsoft.com/GDPR](https://www.microsoft.com/trustcenter/Privacy/GDPR)
