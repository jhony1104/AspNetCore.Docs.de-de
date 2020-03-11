---
title: Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind
author: rick-anderson
description: Informationen Sie zum Erstellen einer Razor-Seiten-app mit Benutzerdaten, die durch Autorisierung geschützt sind. Enthält, HTTPS, Authentifizierung und Sicherheit, ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 7710a8965771db02e601dafb7da752906bcd43e5
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651919"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="57c0a-104">Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind</span><span class="sxs-lookup"><span data-stu-id="57c0a-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="57c0a-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="57c0a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="57c0a-106">Informationen zur ASP.net Core MVC-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) .</span><span class="sxs-lookup"><span data-stu-id="57c0a-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="57c0a-107">Die ASP.net Core 1,1-Version dieses Tutorials finden Sie in [diesem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner.</span><span class="sxs-lookup"><span data-stu-id="57c0a-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="57c0a-108">Das 1,1-ASP.net Core Beispiel finden Sie in den [Beispielen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="57c0a-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="57c0a-109">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="57c0a-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="57c0a-110">In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="57c0a-111">Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben.</span><span class="sxs-lookup"><span data-stu-id="57c0a-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="57c0a-112">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-112">There are three security groups:</span></span>

* <span data-ttu-id="57c0a-113">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="57c0a-114">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="57c0a-115">Nur genehmigte Kontakte für Benutzer sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="57c0a-116">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="57c0a-117">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="57c0a-118">In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="57c0a-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="57c0a-119">Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** .</span><span class="sxs-lookup"><span data-stu-id="57c0a-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="57c0a-120">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="57c0a-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="57c0a-121">Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

<span data-ttu-id="57c0a-123">In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:</span><span class="sxs-lookup"><span data-stu-id="57c0a-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

<span data-ttu-id="57c0a-125">Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:</span><span class="sxs-lookup"><span data-stu-id="57c0a-125">The following image shows the managers details view of a contact:</span></span>

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="57c0a-127">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="57c0a-128">In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="57c0a-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

<span data-ttu-id="57c0a-130">Der Administrator muss alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-130">The administrator has all privileges.</span></span> <span data-ttu-id="57c0a-131">Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="57c0a-132">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="57c0a-133">Das Beispiel enthält die folgenden Handler für die Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="57c0a-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="57c0a-134">`ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="57c0a-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="57c0a-135">`ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="57c0a-136">`ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57c0a-137">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="57c0a-137">Prerequisites</span></span>

<span data-ttu-id="57c0a-138">In diesem Tutorial wird verschoben.</span><span class="sxs-lookup"><span data-stu-id="57c0a-138">This tutorial is advanced.</span></span> <span data-ttu-id="57c0a-139">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="57c0a-139">You should be familiar with:</span></span>

* [<span data-ttu-id="57c0a-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57c0a-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="57c0a-141">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="57c0a-142">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="57c0a-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="57c0a-143">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="57c0a-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="57c0a-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="57c0a-145">Die Starter und die fertige app</span><span class="sxs-lookup"><span data-stu-id="57c0a-145">The starter and completed app</span></span>

<span data-ttu-id="57c0a-146">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="57c0a-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="57c0a-147">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="57c0a-148">Das Starter-app</span><span class="sxs-lookup"><span data-stu-id="57c0a-148">The starter app</span></span>

<span data-ttu-id="57c0a-149">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="57c0a-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="57c0a-150">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="57c0a-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="57c0a-151">Schützen von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="57c0a-151">Secure user data</span></span>

<span data-ttu-id="57c0a-152">Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app.</span><span class="sxs-lookup"><span data-stu-id="57c0a-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="57c0a-153">Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="57c0a-154">Verknüpfen Sie die Kontaktdaten für den Benutzer</span><span class="sxs-lookup"><span data-stu-id="57c0a-154">Tie the contact data to the user</span></span>

<span data-ttu-id="57c0a-155">Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="57c0a-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="57c0a-156">Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:</span><span class="sxs-lookup"><span data-stu-id="57c0a-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="57c0a-157">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="57c0a-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="57c0a-158">Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="57c0a-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="57c0a-159">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="57c0a-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="57c0a-160">Fügen Sie Rollendienste hinzu, die Identität</span><span class="sxs-lookup"><span data-stu-id="57c0a-160">Add Role services to Identity</span></span>

<span data-ttu-id="57c0a-161">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="57c0a-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="57c0a-162">Authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="57c0a-162">Require authenticated users</span></span>

<span data-ttu-id="57c0a-163">Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="57c0a-164">Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="57c0a-165">Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="57c0a-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="57c0a-166">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="57c0a-167">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-und datenschutzseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="57c0a-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="57c0a-168">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="57c0a-168">Configure the test account</span></span>

<span data-ttu-id="57c0a-169">Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager".</span><span class="sxs-lookup"><span data-stu-id="57c0a-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="57c0a-170">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="57c0a-171">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="57c0a-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="57c0a-172">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="57c0a-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="57c0a-173">Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="57c0a-174">Erstellen Sie die Testkonten und aktualisieren Kontakte</span><span class="sxs-lookup"><span data-stu-id="57c0a-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="57c0a-175">Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="57c0a-176">Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="57c0a-177">Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="57c0a-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="57c0a-178">Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="57c0a-179">Nur ein Kontakt wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="57c0a-180">Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen</span><span class="sxs-lookup"><span data-stu-id="57c0a-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="57c0a-181">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="57c0a-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="57c0a-182">Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="57c0a-183">Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="57c0a-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="57c0a-184">Autorisierung Handler in der Regel:</span><span class="sxs-lookup"><span data-stu-id="57c0a-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="57c0a-185">Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="57c0a-186">Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="57c0a-187">`Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="57c0a-188">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="57c0a-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="57c0a-189">Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="57c0a-190">`ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="57c0a-191">Erstellen Sie einen Ereignishandler der Manager-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-191">Create a manager authorization handler</span></span>

<span data-ttu-id="57c0a-192">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="57c0a-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="57c0a-193">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="57c0a-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="57c0a-194">Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).</span><span class="sxs-lookup"><span data-stu-id="57c0a-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="57c0a-195">Erstellen Sie ein Administrator autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="57c0a-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="57c0a-196">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="57c0a-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="57c0a-197">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="57c0a-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="57c0a-198">Administrator möglich, alle Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="57c0a-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="57c0a-199">Registrieren Sie die Autorisierung-Handler</span><span class="sxs-lookup"><span data-stu-id="57c0a-199">Register the authorization handlers</span></span>

<span data-ttu-id="57c0a-200">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="57c0a-201">Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="57c0a-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="57c0a-202">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="57c0a-203">Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:</span><span class="sxs-lookup"><span data-stu-id="57c0a-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="57c0a-204">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="57c0a-205">Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="57c0a-206">Unterstützung für Autorisierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-206">Support authorization</span></span>

<span data-ttu-id="57c0a-207">In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="57c0a-208">Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse</span><span class="sxs-lookup"><span data-stu-id="57c0a-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="57c0a-209">Überprüfen Sie die `ContactOperations`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="57c0a-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="57c0a-210">Diese Klasse enthält die Anforderungen der app unterstützt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="57c0a-211">Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="57c0a-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="57c0a-212">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="57c0a-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="57c0a-213">Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:</span><span class="sxs-lookup"><span data-stu-id="57c0a-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="57c0a-214">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="57c0a-214">The preceding code:</span></span>

* <span data-ttu-id="57c0a-215">Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="57c0a-216">Fügt den Identitäts `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="57c0a-217">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="57c0a-218">Aktualisieren der CreateModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-218">Update the CreateModel</span></span>

<span data-ttu-id="57c0a-219">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="57c0a-220">Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="57c0a-221">Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="57c0a-222">Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="57c0a-223">Aktualisieren Sie die IndexModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-223">Update the IndexModel</span></span>

<span data-ttu-id="57c0a-224">Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="57c0a-225">Aktualisieren Sie die EditModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-225">Update the EditModel</span></span>

<span data-ttu-id="57c0a-226">Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="57c0a-227">Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus.</span><span class="sxs-lookup"><span data-stu-id="57c0a-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="57c0a-228">Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="57c0a-229">Ressourcenbasierte Autorisierung muss zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="57c0a-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="57c0a-230">Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="57c0a-231">Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="57c0a-232">Aktualisieren Sie die DeleteModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-232">Update the DeleteModel</span></span>

<span data-ttu-id="57c0a-233">Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="57c0a-234">Fügen Sie dem Prüfdienst in den Ansichten</span><span class="sxs-lookup"><span data-stu-id="57c0a-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="57c0a-235">Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="57c0a-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="57c0a-236">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="57c0a-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="57c0a-237">Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="57c0a-238">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="57c0a-239">Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern.</span><span class="sxs-lookup"><span data-stu-id="57c0a-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="57c0a-240">Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher.</span><span class="sxs-lookup"><span data-stu-id="57c0a-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="57c0a-241">Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack.</span><span class="sxs-lookup"><span data-stu-id="57c0a-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="57c0a-242">Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="57c0a-243">Updatedetails</span><span class="sxs-lookup"><span data-stu-id="57c0a-243">Update Details</span></span>

<span data-ttu-id="57c0a-244">Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:</span><span class="sxs-lookup"><span data-stu-id="57c0a-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="57c0a-245">Aktualisieren Sie das Seitenmodell Details an:</span><span class="sxs-lookup"><span data-stu-id="57c0a-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="57c0a-246">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="57c0a-246">Add or remove a user to a role</span></span>

<span data-ttu-id="57c0a-247">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="57c0a-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="57c0a-248">Entfernen Berechtigungen eines Benutzers ein.</span><span class="sxs-lookup"><span data-stu-id="57c0a-248">Removing privileges from a user.</span></span> <span data-ttu-id="57c0a-249">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="57c0a-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="57c0a-250">Hinzufügen von Berechtigungen für einen Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="57c0a-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="57c0a-251">Unterschiede zwischen Challenge und verboten</span><span class="sxs-lookup"><span data-stu-id="57c0a-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="57c0a-252">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#require-authenticated-users)sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="57c0a-253">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="57c0a-253">The following code allows anonymous users.</span></span> <span data-ttu-id="57c0a-254">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="57c0a-255">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-255">In the preceding code:</span></span>

* <span data-ttu-id="57c0a-256">Wenn der Benutzer **nicht** authentifiziert ist, wird ein `ChallengeResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="57c0a-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="57c0a-257">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="57c0a-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="57c0a-258">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, wird ein `ForbidResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="57c0a-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="57c0a-259">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite "Zugriff verweigert" umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="57c0a-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="57c0a-260">Testen Sie die fertige app</span><span class="sxs-lookup"><span data-stu-id="57c0a-260">Test the completed app</span></span>

<span data-ttu-id="57c0a-261">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="57c0a-262">Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens einen Großbuchstaben, Anzahl und Symbol.</span><span class="sxs-lookup"><span data-stu-id="57c0a-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="57c0a-263">`Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="57c0a-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="57c0a-264">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="57c0a-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="57c0a-265">Wenn die app Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="57c0a-265">If the app has contacts:</span></span>

* <span data-ttu-id="57c0a-266">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="57c0a-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="57c0a-267">Starten Sie die app zum Seeding der Datenbank neu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="57c0a-268">Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="57c0a-269">Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="57c0a-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="57c0a-270">Melden Sie sich an jeden Browser mit einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="57c0a-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="57c0a-271">Überprüfen Sie die folgenden Vorgänge aus:</span><span class="sxs-lookup"><span data-stu-id="57c0a-271">Verify the following operations:</span></span>

* <span data-ttu-id="57c0a-272">Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="57c0a-273">Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="57c0a-274">Manager können genehmigen/Kontaktdaten ablehnen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="57c0a-275">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="57c0a-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="57c0a-276">Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="57c0a-277">Benutzer</span><span class="sxs-lookup"><span data-stu-id="57c0a-277">User</span></span>                | <span data-ttu-id="57c0a-278">Ein Seeding ausgeführt, von der app</span><span class="sxs-lookup"><span data-stu-id="57c0a-278">Seeded by the app</span></span> | <span data-ttu-id="57c0a-279">Tastatur</span><span class="sxs-lookup"><span data-stu-id="57c0a-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="57c0a-280">Nein</span><span class="sxs-lookup"><span data-stu-id="57c0a-280">No</span></span>                | <span data-ttu-id="57c0a-281">Löschen Sie bearbeiten und die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="57c0a-282">Ja</span><span class="sxs-lookup"><span data-stu-id="57c0a-282">Yes</span></span>               | <span data-ttu-id="57c0a-283">Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="57c0a-284">Ja</span><span class="sxs-lookup"><span data-stu-id="57c0a-284">Yes</span></span>               | <span data-ttu-id="57c0a-285">Genehmigen oder ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="57c0a-286">Erstellen Sie einen Kontakt in der Administrator-Browser.</span><span class="sxs-lookup"><span data-stu-id="57c0a-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="57c0a-287">Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="57c0a-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="57c0a-288">Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.</span><span class="sxs-lookup"><span data-stu-id="57c0a-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="57c0a-289">Die Starter-app erstellen</span><span class="sxs-lookup"><span data-stu-id="57c0a-289">Create the starter app</span></span>

* <span data-ttu-id="57c0a-290">Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="57c0a-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="57c0a-291">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="57c0a-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="57c0a-292">Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.</span><span class="sxs-lookup"><span data-stu-id="57c0a-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="57c0a-293">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="57c0a-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="57c0a-294">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="57c0a-295">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="57c0a-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="57c0a-296">Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:</span><span class="sxs-lookup"><span data-stu-id="57c0a-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="57c0a-297">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="57c0a-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="57c0a-298">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="57c0a-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="57c0a-299">Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts</span><span class="sxs-lookup"><span data-stu-id="57c0a-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="57c0a-300">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="57c0a-300">Seed the database</span></span>

<span data-ttu-id="57c0a-301">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="57c0a-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="57c0a-302">`SeedData.Initialize` aus `Main`abrufen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="57c0a-303">Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-303">Test that the app seeded the database.</span></span> <span data-ttu-id="57c0a-304">Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="57c0a-305">In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="57c0a-306">Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben.</span><span class="sxs-lookup"><span data-stu-id="57c0a-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="57c0a-307">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-307">There are three security groups:</span></span>

* <span data-ttu-id="57c0a-308">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="57c0a-309">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="57c0a-310">Nur genehmigte Kontakte für Benutzer sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="57c0a-311">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="57c0a-312">In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="57c0a-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="57c0a-313">Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** .</span><span class="sxs-lookup"><span data-stu-id="57c0a-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="57c0a-314">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="57c0a-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="57c0a-315">Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

<span data-ttu-id="57c0a-317">In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:</span><span class="sxs-lookup"><span data-stu-id="57c0a-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

<span data-ttu-id="57c0a-319">Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:</span><span class="sxs-lookup"><span data-stu-id="57c0a-319">The following image shows the managers details view of a contact:</span></span>

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="57c0a-321">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="57c0a-322">In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="57c0a-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

<span data-ttu-id="57c0a-324">Der Administrator muss alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-324">The administrator has all privileges.</span></span> <span data-ttu-id="57c0a-325">Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="57c0a-326">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="57c0a-327">Das Beispiel enthält die folgenden Handler für die Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="57c0a-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="57c0a-328">`ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="57c0a-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="57c0a-329">`ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="57c0a-330">`ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="57c0a-331">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="57c0a-331">Prerequisites</span></span>

<span data-ttu-id="57c0a-332">In diesem Tutorial wird verschoben.</span><span class="sxs-lookup"><span data-stu-id="57c0a-332">This tutorial is advanced.</span></span> <span data-ttu-id="57c0a-333">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="57c0a-333">You should be familiar with:</span></span>

* [<span data-ttu-id="57c0a-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="57c0a-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="57c0a-335">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="57c0a-336">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="57c0a-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="57c0a-337">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="57c0a-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="57c0a-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="57c0a-339">Die Starter und die fertige app</span><span class="sxs-lookup"><span data-stu-id="57c0a-339">The starter and completed app</span></span>

<span data-ttu-id="57c0a-340">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="57c0a-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="57c0a-341">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="57c0a-342">Das Starter-app</span><span class="sxs-lookup"><span data-stu-id="57c0a-342">The starter app</span></span>

<span data-ttu-id="57c0a-343">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="57c0a-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="57c0a-344">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="57c0a-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="57c0a-345">Schützen von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="57c0a-345">Secure user data</span></span>

<span data-ttu-id="57c0a-346">Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app.</span><span class="sxs-lookup"><span data-stu-id="57c0a-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="57c0a-347">Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="57c0a-348">Verknüpfen Sie die Kontaktdaten für den Benutzer</span><span class="sxs-lookup"><span data-stu-id="57c0a-348">Tie the contact data to the user</span></span>

<span data-ttu-id="57c0a-349">Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="57c0a-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="57c0a-350">Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:</span><span class="sxs-lookup"><span data-stu-id="57c0a-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="57c0a-351">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="57c0a-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="57c0a-352">Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="57c0a-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="57c0a-353">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="57c0a-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="57c0a-354">Fügen Sie Rollendienste hinzu, die Identität</span><span class="sxs-lookup"><span data-stu-id="57c0a-354">Add Role services to Identity</span></span>

<span data-ttu-id="57c0a-355">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="57c0a-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="57c0a-356">Authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="57c0a-356">Require authenticated users</span></span>

<span data-ttu-id="57c0a-357">Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="57c0a-358">Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="57c0a-359">Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="57c0a-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="57c0a-360">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="57c0a-361">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="57c0a-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="57c0a-362">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="57c0a-362">Configure the test account</span></span>

<span data-ttu-id="57c0a-363">Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager".</span><span class="sxs-lookup"><span data-stu-id="57c0a-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="57c0a-364">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="57c0a-365">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="57c0a-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="57c0a-366">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="57c0a-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="57c0a-367">Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="57c0a-368">Erstellen Sie die Testkonten und aktualisieren Kontakte</span><span class="sxs-lookup"><span data-stu-id="57c0a-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="57c0a-369">Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="57c0a-370">Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="57c0a-371">Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="57c0a-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="57c0a-372">Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="57c0a-373">Nur ein Kontakt wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="57c0a-374">Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen</span><span class="sxs-lookup"><span data-stu-id="57c0a-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="57c0a-375">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen Sie darin eine `ContactIsOwnerAuthorizationHandler`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="57c0a-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="57c0a-376">Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="57c0a-377">Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="57c0a-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="57c0a-378">Autorisierung Handler in der Regel:</span><span class="sxs-lookup"><span data-stu-id="57c0a-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="57c0a-379">Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="57c0a-380">Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="57c0a-381">`Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="57c0a-382">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="57c0a-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="57c0a-383">Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="57c0a-384">`ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="57c0a-385">Erstellen Sie einen Ereignishandler der Manager-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-385">Create a manager authorization handler</span></span>

<span data-ttu-id="57c0a-386">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="57c0a-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="57c0a-387">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="57c0a-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="57c0a-388">Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).</span><span class="sxs-lookup"><span data-stu-id="57c0a-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="57c0a-389">Erstellen Sie ein Administrator autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="57c0a-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="57c0a-390">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="57c0a-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="57c0a-391">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="57c0a-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="57c0a-392">Administrator möglich, alle Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="57c0a-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="57c0a-393">Registrieren Sie die Autorisierung-Handler</span><span class="sxs-lookup"><span data-stu-id="57c0a-393">Register the authorization handlers</span></span>

<span data-ttu-id="57c0a-394">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="57c0a-395">Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="57c0a-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="57c0a-396">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="57c0a-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="57c0a-397">Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:</span><span class="sxs-lookup"><span data-stu-id="57c0a-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="57c0a-398">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="57c0a-399">Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="57c0a-400">Unterstützung für Autorisierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-400">Support authorization</span></span>

<span data-ttu-id="57c0a-401">In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="57c0a-402">Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse</span><span class="sxs-lookup"><span data-stu-id="57c0a-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="57c0a-403">Überprüfen Sie die `ContactOperations`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="57c0a-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="57c0a-404">Diese Klasse enthält die Anforderungen der app unterstützt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="57c0a-405">Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="57c0a-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="57c0a-406">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="57c0a-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="57c0a-407">Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:</span><span class="sxs-lookup"><span data-stu-id="57c0a-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="57c0a-408">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="57c0a-408">The preceding code:</span></span>

* <span data-ttu-id="57c0a-409">Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="57c0a-410">Fügt den Identitäts `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="57c0a-411">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="57c0a-412">Aktualisieren der CreateModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-412">Update the CreateModel</span></span>

<span data-ttu-id="57c0a-413">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="57c0a-414">Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:</span><span class="sxs-lookup"><span data-stu-id="57c0a-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="57c0a-415">Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="57c0a-416">Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="57c0a-417">Aktualisieren Sie die IndexModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-417">Update the IndexModel</span></span>

<span data-ttu-id="57c0a-418">Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="57c0a-419">Aktualisieren Sie die EditModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-419">Update the EditModel</span></span>

<span data-ttu-id="57c0a-420">Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="57c0a-421">Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus.</span><span class="sxs-lookup"><span data-stu-id="57c0a-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="57c0a-422">Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="57c0a-423">Ressourcenbasierte Autorisierung muss zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="57c0a-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="57c0a-424">Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="57c0a-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="57c0a-425">Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="57c0a-426">Aktualisieren Sie die DeleteModel</span><span class="sxs-lookup"><span data-stu-id="57c0a-426">Update the DeleteModel</span></span>

<span data-ttu-id="57c0a-427">Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="57c0a-428">Fügen Sie dem Prüfdienst in den Ansichten</span><span class="sxs-lookup"><span data-stu-id="57c0a-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="57c0a-429">Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="57c0a-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="57c0a-430">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="57c0a-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="57c0a-431">Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="57c0a-432">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="57c0a-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="57c0a-433">Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern.</span><span class="sxs-lookup"><span data-stu-id="57c0a-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="57c0a-434">Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher.</span><span class="sxs-lookup"><span data-stu-id="57c0a-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="57c0a-435">Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack.</span><span class="sxs-lookup"><span data-stu-id="57c0a-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="57c0a-436">Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="57c0a-437">Updatedetails</span><span class="sxs-lookup"><span data-stu-id="57c0a-437">Update Details</span></span>

<span data-ttu-id="57c0a-438">Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:</span><span class="sxs-lookup"><span data-stu-id="57c0a-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="57c0a-439">Aktualisieren Sie das Seitenmodell Details an:</span><span class="sxs-lookup"><span data-stu-id="57c0a-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="57c0a-440">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="57c0a-440">Add or remove a user to a role</span></span>

<span data-ttu-id="57c0a-441">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="57c0a-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="57c0a-442">Entfernen Berechtigungen eines Benutzers ein.</span><span class="sxs-lookup"><span data-stu-id="57c0a-442">Removing privileges from a user.</span></span> <span data-ttu-id="57c0a-443">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="57c0a-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="57c0a-444">Hinzufügen von Berechtigungen für einen Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="57c0a-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="57c0a-445">Testen Sie die fertige app</span><span class="sxs-lookup"><span data-stu-id="57c0a-445">Test the completed app</span></span>

<span data-ttu-id="57c0a-446">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="57c0a-447">Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens einen Großbuchstaben, Anzahl und Symbol.</span><span class="sxs-lookup"><span data-stu-id="57c0a-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="57c0a-448">`Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="57c0a-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="57c0a-449">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="57c0a-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="57c0a-450">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="57c0a-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="57c0a-451">Starten Sie die app zum Seeding der Datenbank neu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="57c0a-452">Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="57c0a-453">Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="57c0a-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="57c0a-454">Melden Sie sich an jeden Browser mit einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="57c0a-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="57c0a-455">Überprüfen Sie die folgenden Vorgänge aus:</span><span class="sxs-lookup"><span data-stu-id="57c0a-455">Verify the following operations:</span></span>

* <span data-ttu-id="57c0a-456">Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="57c0a-457">Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="57c0a-458">Manager können genehmigen/Kontaktdaten ablehnen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="57c0a-459">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="57c0a-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="57c0a-460">Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="57c0a-461">Benutzer</span><span class="sxs-lookup"><span data-stu-id="57c0a-461">User</span></span>                | <span data-ttu-id="57c0a-462">Ein Seeding ausgeführt, von der app</span><span class="sxs-lookup"><span data-stu-id="57c0a-462">Seeded by the app</span></span> | <span data-ttu-id="57c0a-463">Tastatur</span><span class="sxs-lookup"><span data-stu-id="57c0a-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="57c0a-464">Nein</span><span class="sxs-lookup"><span data-stu-id="57c0a-464">No</span></span>                | <span data-ttu-id="57c0a-465">Löschen Sie bearbeiten und die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="57c0a-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="57c0a-466">Ja</span><span class="sxs-lookup"><span data-stu-id="57c0a-466">Yes</span></span>               | <span data-ttu-id="57c0a-467">Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="57c0a-468">Ja</span><span class="sxs-lookup"><span data-stu-id="57c0a-468">Yes</span></span>               | <span data-ttu-id="57c0a-469">Genehmigen oder ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="57c0a-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="57c0a-470">Erstellen Sie einen Kontakt in der Administrator-Browser.</span><span class="sxs-lookup"><span data-stu-id="57c0a-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="57c0a-471">Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="57c0a-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="57c0a-472">Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.</span><span class="sxs-lookup"><span data-stu-id="57c0a-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="57c0a-473">Die Starter-app erstellen</span><span class="sxs-lookup"><span data-stu-id="57c0a-473">Create the starter app</span></span>

* <span data-ttu-id="57c0a-474">Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="57c0a-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="57c0a-475">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="57c0a-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="57c0a-476">Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.</span><span class="sxs-lookup"><span data-stu-id="57c0a-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="57c0a-477">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="57c0a-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="57c0a-478">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="57c0a-479">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="57c0a-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="57c0a-480">Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:</span><span class="sxs-lookup"><span data-stu-id="57c0a-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="57c0a-481">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="57c0a-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="57c0a-482">Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts</span><span class="sxs-lookup"><span data-stu-id="57c0a-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="57c0a-483">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="57c0a-483">Seed the database</span></span>

<span data-ttu-id="57c0a-484">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="57c0a-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="57c0a-485">`SeedData.Initialize` aus `Main`abrufen:</span><span class="sxs-lookup"><span data-stu-id="57c0a-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="57c0a-486">Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-486">Test that the app seeded the database.</span></span> <span data-ttu-id="57c0a-487">Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="57c0a-488">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="57c0a-488">Additional resources</span></span>

* [<span data-ttu-id="57c0a-489">Erstellen einer .net Core-und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="57c0a-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="57c0a-490">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="57c0a-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="57c0a-491">Dieser Übung wird ausführlicher auf den Sicherheitsfeatures, die in diesem Lernprogramm eingeführt.</span><span class="sxs-lookup"><span data-stu-id="57c0a-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="57c0a-492">Benutzerdefinierte, richtlinienbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="57c0a-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
