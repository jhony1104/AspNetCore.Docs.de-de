---
title: Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten
author: rick-anderson
description: Erfahren Sie, wie Sie Razor eine Pages-App mit von der Autorisierung geschützten Benutzerdaten erstellen. Umfasst HTTPS, Authentifizierung, Sicherheit, ASP.net Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f52b08786dde54e7dcbd2e00f43badb58879cf79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775751"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten

Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

Informationen zur ASP.net Core MVC-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) . Die ASP.net Core 1,1-Version dieses Tutorials finden Sie in [diesem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner. Das 1,1-ASP.net Core Beispiel finden Sie in den [Beispielen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden. Es gibt drei Sicherheitsgruppen:

* **Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.
* **Manager** können Kontaktdaten genehmigen oder ablehnen. Nur genehmigte Kontakte sind für Benutzer sichtbar.
* **Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.

Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.

In der folgenden Abbildung ist der Benutzer Rick`rick@example.com`() angemeldet. Rick kann nur genehmigte Kontakte anzeigen und **Edit**/**Löschen**/bearbeiten neue Verknüpfungen für seine Kontakte**Erstellen** . Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an. Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:

![Screenshot der manager@contoso.com Anmeldung](secure-data/_static/manager1.png)

Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.

In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:

![Screenshot der admin@contoso.com Anmeldung](secure-data/_static/admin.png)

Der Administrator verfügt über alle Berechtigungen. Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.

Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf `Contact` das folgende Modell erstellt:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Das Beispiel enthält die folgenden Autorisierungs Handler:

* `ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.
* `ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.
* `ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial ist erweitert. Sie sollten sich mit folgenden Aktionen vertraut machen:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentifizierung](xref:security/authentication/identity)
* [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)
* [Autorisierung](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter und abgeschlossene App

[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter. [Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.

### <a name="the-starter-app"></a>Die Starter-App

[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.

Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.

## <a name="secure-user-data"></a>Sichern von Benutzerdaten

In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben. Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.

### <a name="tie-the-contact-data-to-the-user"></a>Verknüpfen der Kontaktdaten mit dem Benutzer

Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können. Fügen `OwnerID` Sie `ContactStatus` dem `Contact` Modell und hinzu:

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank. Das `Status` -Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.

Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Rollen Dienste zur Identität hinzufügen

Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>Authentifizierte Benutzer erforderlich

Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]` -Attribut ablehnen. Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Pages und Controller geschützt. Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, das `[Authorize]` -Attribut einzubeziehen.

Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-und datenschutzseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>Konfigurieren des Testkontos

Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager. Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen. Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, `SeedData.Initialize` wenn aufgerufen wird.

Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Erstellen der Testkonten und Aktualisieren der Kontakte

Aktualisieren Sie `Initialize` die-Methode `SeedData` in der-Klasse, um die Testkonten zu erstellen:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

Fügen Sie die Benutzer-ID `ContactStatus` des Administrators und den Kontakten hinzu. Machen Sie einen der Kontakte "gesendet" und "abgelehnt". Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu. Es wird nur ein Kontakt angezeigt:

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren

Erstellen Sie `ContactIsOwnerAuthorizationHandler` eine Klasse im *Autorisierungs* Ordner. Mit `ContactIsOwnerAuthorizationHandler` wird überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist. Autorisierungs Handler im allgemeinen:

* Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.
* Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt werden. `Task.CompletedTask`ist nicht erfolgreich oder Fehler&mdash;Haft, sodass andere Autorisierungs Handler ausgeführt werden können.

Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen. `ContactIsOwnerAuthorizationHandler`der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.

### <a name="create-a-manager-authorization-handler"></a>Erstellen eines Manager-Autorisierungs Handlers

Erstellen Sie `ContactManagerAuthorizationHandler` eine Klasse im *Autorisierungs* Ordner. Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist. Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Erstellen eines Administrator Autorisierungs Handlers

Erstellen Sie `ContactAdministratorsAuthorizationHandler` eine Klasse im *Autorisierungs* Ordner. Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist. Der Administrator kann alle Vorgänge ausführen.

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrieren von Autorisierungs Handlern

Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden. `ContactIsOwnerAuthorizationHandler` Verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert. Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die über `ContactsController` die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind. Fügen Sie den folgenden Code am Ende von `ConfigureServices`hinzu:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler`und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt. Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.

## <a name="support-authorization"></a>Autorisierung unterstützen

In diesem Abschnitt aktualisieren Sie die Razor Pages und fügen eine Vorgangs Anforderungs Klasse hinzu.

### <a name="review-the-contact-operations-requirements-class"></a>Überprüfen der "Contact Operations Requirements"-Klasse

Überprüfen `ContactOperations` Sie die-Klasse. Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Erstellen einer Basisklasse für die Kontakte Razor Pages

Erstellen Sie eine Basisklasse, die die in den Kontakten Razor Pages verwendeten Dienste enthält. Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

Der vorangehende Code:

* Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.
* Fügt den Identitäts `UserManager` Dienst hinzu.
* Fügen Sie die `ApplicationDbContext` hinzu.

### <a name="update-the-createmodel"></a>Aktualisieren von "up Model"

Aktualisieren Sie den Konstruktor für das Erstellen von Seiten `DI_BasePageModel` Modellen, um die Basisklasse zu verwenden:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualisieren Sie `CreateModel.OnPostAsync` die-Methode wie folgt:

* Fügen Sie dem `Contact` Modell die Benutzer-ID hinzu.
* Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualisieren von indexmodel

Aktualisieren Sie `OnGetAsync` die-Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualisieren von "editmodel"

Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht. Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden. Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein. Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden. Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualisieren von deletemodel

Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Einfügen des Autorisierungs Dienstanbieter in die Ansichten

Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.

Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

Das vorangehende Markup fügt `using` mehrere-Anweisungen hinzu.

Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht. Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden. Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen. Die Razor Page oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.

### <a name="update-details"></a>Details aktualisieren

Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

Aktualisieren Sie das Detailseiten Modell:

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Hinzufügen oder Entfernen eines Benutzers zu einer Rolle

Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :

* Entfernen von Berechtigungen für einen Benutzer. Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.
* Hinzufügen von Berechtigungen zu einem Benutzer

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a>Unterschiede zwischen Challenge und verboten

Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#require-authenticated-users)sind. Der folgende Code ermöglicht anonyme Benutzer. Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

Für den Code oben gilt:

* Wenn der Benutzer **nicht** authentifiziert ist, wird `ChallengeResult` eine zurückgegeben. Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.
* Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, wird `ForbidResult` eine zurückgegeben. Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite Zugriff verweigert umgeleitet.

## <a name="test-the-completed-app"></a>Testen der abgeschlossenen App

Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:

* Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol. Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.
* Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus `<PW>` , wobei das Kennwort ist:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

Wenn die APP Kontakte hat:

* Löschen Sie alle Datensätze in der `Contact` Tabelle.
* Starten Sie die APP neu, um die Datenbank zu starten.

Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten. Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com`.). Melden Sie sich bei jedem Browser mit einem anderen Benutzer an. Überprüfen Sie die folgenden Vorgänge:

* Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.
* Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.
* Manager können Kontaktdaten genehmigen/ablehnen. Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .
* Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.

| Benutzer                | Seeding von der APP | Optionen                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Nein                | Bearbeiten/Löschen Sie die eigenen Daten.                |
| manager@contoso.com | Ja               | Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten. |
| admin@contoso.com   | Ja               | Alle Daten genehmigen/ablehnen und bearbeiten/löschen. |

Erstellen Sie einen Kontakt im Browser des Administrators. Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt. Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.

## <a name="create-the-starter-app"></a>Erstellen der Starter-App

* Erstellen Sie eine Razor Pages-App mit dem Namen "ContactManager".
  * Erstellen Sie die APP mit **einzelnen Benutzerkonten**.
  * Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.
  * `-uld`gibt localdb anstelle von SQLite an.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modelle/Contact. cs*hinzufügen:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Gerüst für das `Contact` Modell.
* Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:

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

* Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.

### <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

Aufrufe `SeedData.Initialize` von `Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

Testen Sie, ob die APP die Datenbank durchsucht hat. Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden. Es gibt drei Sicherheitsgruppen:

* **Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.
* **Manager** können Kontaktdaten genehmigen oder ablehnen. Nur genehmigte Kontakte sind für Benutzer sichtbar.
* **Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.

In der folgenden Abbildung ist der Benutzer Rick`rick@example.com`() angemeldet. Rick kann nur genehmigte Kontakte anzeigen und **Edit**/**Löschen**/bearbeiten neue Verknüpfungen für seine Kontakte**Erstellen** . Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an. Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:

![Screenshot der manager@contoso.com Anmeldung](secure-data/_static/manager1.png)

Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.

In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:

![Screenshot der admin@contoso.com Anmeldung](secure-data/_static/admin.png)

Der Administrator verfügt über alle Berechtigungen. Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.

Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf `Contact` das folgende Modell erstellt:

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

Das Beispiel enthält die folgenden Autorisierungs Handler:

* `ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.
* `ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.
* `ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.

## <a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial ist erweitert. Sie sollten sich mit folgenden Aktionen vertraut machen:

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [Authentifizierung](xref:security/authentication/identity)
* [Konto Bestätigung und Kenn Wort Wiederherstellung](xref:security/authentication/accconfirm)
* [Autorisierung](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter und abgeschlossene App

[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter. [Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.

### <a name="the-starter-app"></a>Die Starter-App

[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.

Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.

## <a name="secure-user-data"></a>Sichern von Benutzerdaten

In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben. Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.

### <a name="tie-the-contact-data-to-the-user"></a>Verknüpfen der Kontaktdaten mit dem Benutzer

Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können. Fügen `OwnerID` Sie `ContactStatus` dem `Contact` Modell und hinzu:

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID`die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank. Das `Status` -Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.

Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>Rollen Dienste zur Identität hinzufügen

Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>Authentifizierte Benutzer erforderlich

Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]` -Attribut ablehnen. Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Pages und Controller geschützt. Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, das `[Authorize]` -Attribut einzubeziehen.

Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>Konfigurieren des Testkontos

Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager. Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen. Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, `SeedData.Initialize` wenn aufgerufen wird.

Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>Erstellen der Testkonten und Aktualisieren der Kontakte

Aktualisieren Sie `Initialize` die-Methode `SeedData` in der-Klasse, um die Testkonten zu erstellen:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

Fügen Sie die Benutzer-ID `ContactStatus` des Administrators und den Kontakten hinzu. Machen Sie einen der Kontakte "gesendet" und "abgelehnt". Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu. Es wird nur ein Kontakt angezeigt:

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren

Erstellen Sie einen *Autorisierungs* Ordner, `ContactIsOwnerAuthorizationHandler` und erstellen Sie eine Klasse darin. Mit `ContactIsOwnerAuthorizationHandler` wird überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist. Autorisierungs Handler im allgemeinen:

* Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.
* Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt werden. `Task.CompletedTask`ist nicht erfolgreich oder Fehler&mdash;Haft, sodass andere Autorisierungs Handler ausgeführt werden können.

Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).

Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen. `ContactIsOwnerAuthorizationHandler`der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.

### <a name="create-a-manager-authorization-handler"></a>Erstellen eines Manager-Autorisierungs Handlers

Erstellen Sie `ContactManagerAuthorizationHandler` eine Klasse im *Autorisierungs* Ordner. Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist. Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>Erstellen eines Administrator Autorisierungs Handlers

Erstellen Sie `ContactAdministratorsAuthorizationHandler` eine Klasse im *Autorisierungs* Ordner. Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist. Der Administrator kann alle Vorgänge ausführen.

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>Registrieren von Autorisierungs Handlern

Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden. `ContactIsOwnerAuthorizationHandler` Verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert. Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die über `ContactsController` die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind. Fügen Sie den folgenden Code am Ende von `ConfigureServices`hinzu:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler`und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt. Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.

## <a name="support-authorization"></a>Autorisierung unterstützen

In diesem Abschnitt aktualisieren Sie die Razor Pages und fügen eine Vorgangs Anforderungs Klasse hinzu.

### <a name="review-the-contact-operations-requirements-class"></a>Überprüfen der "Contact Operations Requirements"-Klasse

Überprüfen `ContactOperations` Sie die-Klasse. Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>Erstellen einer Basisklasse für die Kontakte Razor Pages

Erstellen Sie eine Basisklasse, die die in den Kontakten Razor Pages verwendeten Dienste enthält. Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

Der vorangehende Code:

* Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.
* Fügt den Identitäts `UserManager` Dienst hinzu.
* Fügen Sie die `ApplicationDbContext` hinzu.

### <a name="update-the-createmodel"></a>Aktualisieren von "up Model"

Aktualisieren Sie den Konstruktor für das Erstellen von Seiten `DI_BasePageModel` Modellen, um die Basisklasse zu verwenden:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

Aktualisieren Sie `CreateModel.OnPostAsync` die-Methode wie folgt:

* Fügen Sie dem `Contact` Modell die Benutzer-ID hinzu.
* Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>Aktualisieren von indexmodel

Aktualisieren Sie `OnGetAsync` die-Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>Aktualisieren von "editmodel"

Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht. Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden. Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein. Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden. Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>Aktualisieren von deletemodel

Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>Einfügen des Autorisierungs Dienstanbieter in die Ansichten

Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.

Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

Das vorangehende Markup fügt `using` mehrere-Anweisungen hinzu.

Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht. Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden. Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen. Die Razor Page oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.

### <a name="update-details"></a>Details aktualisieren

Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

Aktualisieren Sie das Detailseiten Modell:

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>Hinzufügen oder Entfernen eines Benutzers zu einer Rolle

Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :

* Entfernen von Berechtigungen für einen Benutzer. Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.
* Hinzufügen von Berechtigungen zu einem Benutzer

## <a name="test-the-completed-app"></a>Testen der abgeschlossenen App

Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:

* Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol. Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.
* Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus `<PW>` , wobei das Kennwort ist:

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* Löschen und Aktualisieren der Datenbank

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* Starten Sie die APP neu, um die Datenbank zu starten.

Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten. Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com`.). Melden Sie sich bei jedem Browser mit einem anderen Benutzer an. Überprüfen Sie die folgenden Vorgänge:

* Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.
* Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.
* Manager können Kontaktdaten genehmigen/ablehnen. Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .
* Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.

| Benutzer                | Seeding von der APP | Optionen                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | Nein                | Bearbeiten/Löschen Sie die eigenen Daten.                |
| manager@contoso.com | Ja               | Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten. |
| admin@contoso.com   | Ja               | Alle Daten genehmigen/ablehnen und bearbeiten/löschen. |

Erstellen Sie einen Kontakt im Browser des Administrators. Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt. Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.

## <a name="create-the-starter-app"></a>Erstellen der Starter-App

* Erstellen Sie Razor eine Seiten-App mit dem Namen "ContactManager".
  * Erstellen Sie die APP mit **einzelnen Benutzerkonten**.
  * Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.
  * `-uld`gibt localdb anstelle von SQLite an.

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* *Modelle/Contact. cs*hinzufügen:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* Gerüst für das `Contact` Modell.
* Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:

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

* Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.

### <a name="seed-the-database"></a>Ausführen eines Seedings für die Datenbank

Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.

Aufrufe `SeedData.Initialize` von `Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

Testen Sie, ob die APP die Datenbank durchsucht hat. Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Erstellen einer .net Core-und SQL-Datenbank-Web-App in Azure App Service](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop). In dieser Übungseinheit werden die in diesem Tutorial vorgestellten Sicherheitsfeatures ausführlicher erläutert.
* <xref:security/authorization/introduction>
* [Benutzerdefinierte Richtlinien basierte Autorisierung](xref:security/authorization/policies)
