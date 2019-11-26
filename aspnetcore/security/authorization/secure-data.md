---
title: Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind
author: rick-anderson
description: Informationen Sie zum Erstellen einer Razor-Seiten-app mit Benutzerdaten, die durch Autorisierung geschützt sind. Enthält, HTTPS, Authentifizierung und Sicherheit, ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 65c72d4dd457f85451796c5713bedebafec7a7de
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239833"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

Informationen zur ASP.net Core MVC-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) . Die ASP.net Core 1,1-Version dieses Tutorials finden Sie in [diesem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner. Das 1,1-ASP.net Core Beispiel finden Sie in den [Beispielen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind. Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben. Es gibt drei Sicherheitsgruppen:

* **Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.
* **Manager** können Kontaktdaten genehmigen oder ablehnen. Nur genehmigte Kontakte für Benutzer sichtbar sind.
* **Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.

Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.

In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet. Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** . Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an. Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.

In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

Der Administrator muss alle Berechtigungen. Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.

Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Das Beispiel enthält die folgenden Handler für die Autorisierung:

* `ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.
* `ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.
* `ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.

## <a name="prerequisites"></a>Vorraussetzungen

In diesem Tutorial wird verschoben. Sie sollten mit vertraut sein:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentifizierung](xref:security/authentication/identity)
* [Kontobestätigung und Kennwortwiederherstellung](xref:security/authentication/accconfirm)
* [Autorisierung](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Die Starter und die fertige app

[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter. [Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.

### <a name="the-starter-app"></a>Das Starter-app

[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.

Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.

## <a name="secure-user-data"></a>Schützen von Benutzerdaten

Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app. Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.

### <a name="tie-the-contact-data-to-the-user"></a>Verknüpfen Sie die Kontaktdaten für den Benutzer

Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können. Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank. Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.

Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Fügen Sie Rollendienste hinzu, die Identität

Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>Authentifizierte Benutzer

Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen. Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller. Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.

Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-und datenschutzseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Konfigurieren des Testkontos

Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager". Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen. Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.

Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Erstellen Sie die Testkonten und aktualisieren Kontakte

Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu. Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt". Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu. Nur ein Kontakt wird angezeigt:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen

Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner. Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist. Autorisierung Handler in der Regel:

* Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.
* Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind. `Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.

Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten. `ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.

### <a name="create-a-manager-authorization-handler"></a>Erstellen Sie einen Ereignishandler der Manager-Autorisierung

Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner. Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist. Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Erstellen Sie ein Administrator autorisierungshandler

Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner. Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist. Administrator möglich, alle Vorgänge.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrieren Sie die Autorisierung-Handler

Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden. Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert. Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind. Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt. Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.

## <a name="support-authorization"></a>Unterstützung für Autorisierung

In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.

### <a name="review-the-contact-operations-requirements-class"></a>Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse

Überprüfen Sie die `ContactOperations`-Klasse. Diese Klasse enthält die Anforderungen der app unterstützt:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten

Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält. Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Der vorangehende Code:

* Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.
* Fügt den Identitäts `UserManager` Dienst hinzu.
* Fügen Sie die `ApplicationDbContext` hinzu.

### <a name="update-the-createmodel"></a>Aktualisieren der CreateModel

Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:

* Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.
* Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualisieren Sie die IndexModel

Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualisieren Sie die EditModel

Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu. Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus. Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden. Ressourcenbasierte Autorisierung muss zwingend erforderlich. Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden. Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualisieren Sie die DeleteModel

Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Fügen Sie dem Prüfdienst in den Ansichten

Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.

Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.

Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern. Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher. Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack. Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.

### <a name="update-details"></a>Updatedetails

Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Aktualisieren Sie das Seitenmodell Details an:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Hinzufügen oder Entfernen eines Benutzers zu einer Rolle

Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :

* Entfernen Berechtigungen eines Benutzers ein. Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.
* Hinzufügen von Berechtigungen für einen Benutzer aus.

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Unterschiede zwischen Challenge und verboten

Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#require-authenticated-users)sind. Der folgende Code ermöglicht anonyme Benutzer. Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

Für den Code oben gilt:

* Wenn der Benutzer **nicht** authentifiziert ist, wird ein `ChallengeResult` zurückgegeben. Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.
* Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, wird ein `ForbidResult` zurückgegeben. Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite "Zugriff verweigert" umgeleitet.

## <a name="test-the-completed-app"></a>Testen Sie die fertige app

Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:

* Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens einen Großbuchstaben, Anzahl und Symbol. `Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.
* Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Wenn die app Kontakte hat:

* Löschen Sie alle Datensätze in der `Contact` Tabelle.
* Starten Sie die app zum Seeding der Datenbank neu.

Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten. Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`). Melden Sie sich an jeden Browser mit einem anderen Benutzer. Überprüfen Sie die folgenden Vorgänge aus:

* Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.
* Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.
* Manager können genehmigen/Kontaktdaten ablehnen. Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .
* Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.

| Benutzer                | Ein Seeding ausgeführt, von der app | Optionen                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | nein                | Löschen Sie bearbeiten und die eigenen Daten.                |
| manager@contoso.com | Ja               | Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen. |
| admin@contoso.com   | Ja               | Genehmigen oder ablehnen und alle Daten bearbeiten und löschen. |

Erstellen Sie einen Kontakt in der Administrator-Browser. Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an. Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.

## <a name="create-the-starter-app"></a>Die Starter-app erstellen

* Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"
  * Erstellen Sie die APP mit **einzelnen Benutzerkonten**.
  * Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.
  * `-uld` gibt localdb anstelle von SQLite an.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modelle/Contact. cs*hinzufügen:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Gerüst für das `Contact` Modell.
* Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).

* Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts

### <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

`SeedData.Initialize` aus `Main`abrufen:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt. Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind. Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben. Es gibt drei Sicherheitsgruppen:

* **Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.
* **Manager** können Kontaktdaten genehmigen oder ablehnen. Nur genehmigte Kontakte für Benutzer sichtbar sind.
* **Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.

In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet. Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** . Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an. Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.

In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

Der Administrator muss alle Berechtigungen. Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.

Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Das Beispiel enthält die folgenden Handler für die Autorisierung:

* `ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.
* `ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.
* `ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.

## <a name="prerequisites"></a>Vorraussetzungen

In diesem Tutorial wird verschoben. Sie sollten mit vertraut sein:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentifizierung](xref:security/authentication/identity)
* [Kontobestätigung und Kennwortwiederherstellung](xref:security/authentication/accconfirm)
* [Autorisierung](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Die Starter und die fertige app

[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter. [Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.

### <a name="the-starter-app"></a>Das Starter-app

[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.

Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.

## <a name="secure-user-data"></a>Schützen von Benutzerdaten

Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app. Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.

### <a name="tie-the-contact-data-to-the-user"></a>Verknüpfen Sie die Kontaktdaten für den Benutzer

Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können. Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank. Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.

Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Fügen Sie Rollendienste hinzu, die Identität

Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>Authentifizierte Benutzer

Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen. Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller. Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.

Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Konfigurieren des Testkontos

Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager". Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen. Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.

Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Erstellen Sie die Testkonten und aktualisieren Kontakte

Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu. Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt". Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu. Nur ein Kontakt wird angezeigt:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen

Erstellen Sie einen *Autorisierungs* Ordner, und erstellen Sie darin eine `ContactIsOwnerAuthorizationHandler`-Klasse. Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist. Autorisierung Handler in der Regel:

* Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.
* Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind. `Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.

Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten. `ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.

### <a name="create-a-manager-authorization-handler"></a>Erstellen Sie einen Ereignishandler der Manager-Autorisierung

Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner. Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist. Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Erstellen Sie ein Administrator autorisierungshandler

Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner. Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist. Administrator möglich, alle Vorgänge.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrieren Sie die Autorisierung-Handler

Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden. Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert. Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind. Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt. Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.

## <a name="support-authorization"></a>Unterstützung für Autorisierung

In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.

### <a name="review-the-contact-operations-requirements-class"></a>Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse

Überprüfen Sie die `ContactOperations`-Klasse. Diese Klasse enthält die Anforderungen der app unterstützt:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten

Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält. Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Der vorangehende Code:

* Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.
* Fügt den Identitäts `UserManager` Dienst hinzu.
* Fügen Sie die `ApplicationDbContext` hinzu.

### <a name="update-the-createmodel"></a>Aktualisieren der CreateModel

Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:

* Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.
* Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualisieren Sie die IndexModel

Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualisieren Sie die EditModel

Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu. Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus. Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden. Ressourcenbasierte Autorisierung muss zwingend erforderlich. Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden. Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualisieren Sie die DeleteModel

Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Fügen Sie dem Prüfdienst in den Ansichten

Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.

Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.

Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern. Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher. Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack. Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.

### <a name="update-details"></a>Updatedetails

Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Aktualisieren Sie das Seitenmodell Details an:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Hinzufügen oder Entfernen eines Benutzers zu einer Rolle

Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :

* Entfernen Berechtigungen eines Benutzers ein. Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.
* Hinzufügen von Berechtigungen für einen Benutzer aus.

## <a name="test-the-completed-app"></a>Testen Sie die fertige app

Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:

* Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens einen Großbuchstaben, Anzahl und Symbol. `Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.
* Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Löschen und Aktualisieren der Datenbank

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Starten Sie die app zum Seeding der Datenbank neu.

Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten. Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`). Melden Sie sich an jeden Browser mit einem anderen Benutzer. Überprüfen Sie die folgenden Vorgänge aus:

* Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.
* Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.
* Manager können genehmigen/Kontaktdaten ablehnen. Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .
* Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.

| Benutzer                | Ein Seeding ausgeführt, von der app | Optionen                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | nein                | Löschen Sie bearbeiten und die eigenen Daten.                |
| manager@contoso.com | Ja               | Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen. |
| admin@contoso.com   | Ja               | Genehmigen oder ablehnen und alle Daten bearbeiten und löschen. |

Erstellen Sie einen Kontakt in der Administrator-Browser. Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an. Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.

## <a name="create-the-starter-app"></a>Die Starter-app erstellen

* Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"
  * Erstellen Sie die APP mit **einzelnen Benutzerkonten**.
  * Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.
  * `-uld` gibt localdb anstelle von SQLite an.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modelle/Contact. cs*hinzufügen:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Gerüst für das `Contact` Modell.
* Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts

### <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.

`SeedData.Initialize` aus `Main`abrufen:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt. Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erstellen einer .net Core-und SQL-Datenbank-Web-App in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop). Dieser Übung wird ausführlicher auf den Sicherheitsfeatures, die in diesem Lernprogramm eingeführt.
* <xref:security/authorization/introduction>
* [Benutzerdefinierte, richtlinienbasierte Autorisierung](xref:security/authorization/policies)
