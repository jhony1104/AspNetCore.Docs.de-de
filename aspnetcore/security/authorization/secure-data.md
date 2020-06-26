---
title: Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten
author: rick-anderson
description: Erfahren Sie, wie Sie eine Razor pages-App mit von der Autorisierung geschützten Benutzerdaten erstellen. Umfasst HTTPS, Authentifizierung, Sicherheit, ASP.net Core Identity .
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f50015af864a4a62abd5e2eab508aac915cb6370
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404716"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="5e061-104">Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="5e061-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="5e061-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="5e061-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="5e061-106">Informationen zur ASP.net Core MVC-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) .</span><span class="sxs-lookup"><span data-stu-id="5e061-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="5e061-107">Die ASP.net Core 1,1-Version dieses Tutorials finden Sie in [diesem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner.</span><span class="sxs-lookup"><span data-stu-id="5e061-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="5e061-108">Das 1,1-ASP.net Core Beispiel finden Sie in den [Beispielen](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="5e061-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="5e061-109">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="5e061-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5e061-110">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="5e061-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="5e061-111">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="5e061-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="5e061-112">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="5e061-112">There are three security groups:</span></span>

* <span data-ttu-id="5e061-113">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="5e061-114">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="5e061-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="5e061-115">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="5e061-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="5e061-116">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="5e061-117">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="5e061-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="5e061-118">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="5e061-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="5e061-119">Rick kann nur genehmigte Kontakte anzeigen und **Edit** / **Löschen**bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="5e061-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="5e061-120">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="5e061-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="5e061-121">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="5e061-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="5e061-123">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="5e061-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="5e061-125">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="5e061-125">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="5e061-127">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5e061-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="5e061-128">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="5e061-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="5e061-130">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="5e061-130">The administrator has all privileges.</span></span> <span data-ttu-id="5e061-131">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="5e061-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="5e061-132">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="5e061-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="5e061-133">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="5e061-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="5e061-134">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="5e061-135">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="5e061-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="5e061-136">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="5e061-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e061-137">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="5e061-137">Prerequisites</span></span>

<span data-ttu-id="5e061-138">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="5e061-138">This tutorial is advanced.</span></span> <span data-ttu-id="5e061-139">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="5e061-139">You should be familiar with:</span></span>

* [<span data-ttu-id="5e061-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e061-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="5e061-141">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5e061-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5e061-142">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="5e061-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="5e061-143">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="5e061-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="5e061-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5e061-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="5e061-145">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="5e061-145">The starter and completed app</span></span>

<span data-ttu-id="5e061-146">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="5e061-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="5e061-147">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="5e061-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="5e061-148">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="5e061-148">The starter app</span></span>

<span data-ttu-id="5e061-149">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="5e061-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="5e061-150">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="5e061-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="5e061-151">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="5e061-151">Secure user data</span></span>

<span data-ttu-id="5e061-152">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5e061-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="5e061-153">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="5e061-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="5e061-154">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="5e061-154">Tie the contact data to the user</span></span>

<span data-ttu-id="5e061-155">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="5e061-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="5e061-156">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="5e061-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="5e061-157">`OwnerID`die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="5e061-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="5e061-158">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="5e061-159">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="5e061-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="5e061-160">Rollen Dienste hinzufügen zuIdentity</span><span class="sxs-lookup"><span data-stu-id="5e061-160">Add Role services to Identity</span></span>

<span data-ttu-id="5e061-161">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="5e061-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="5e061-162">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="5e061-162">Require authenticated users</span></span>

<span data-ttu-id="5e061-163">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="5e061-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="5e061-164">Sie können die Authentifizierung auf Razor Seiten-, Controller-oder Aktionsmethoden Ebene mit dem- `[AllowAnonymous]` Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="5e061-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="5e061-165">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Seiten und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="5e061-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="5e061-166">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="5e061-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="5e061-167">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-und datenschutzseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="5e061-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="5e061-168">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="5e061-168">Configure the test account</span></span>

<span data-ttu-id="5e061-169">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="5e061-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="5e061-170">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="5e061-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="5e061-171">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="5e061-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="5e061-172">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5e061-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="5e061-173">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="5e061-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="5e061-174">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="5e061-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="5e061-175">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="5e061-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="5e061-176">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="5e061-177">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="5e061-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="5e061-178">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="5e061-179">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5e061-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="5e061-180">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="5e061-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="5e061-181">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="5e061-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5e061-182">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="5e061-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="5e061-183">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="5e061-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="5e061-184">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="5e061-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="5e061-185">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="5e061-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="5e061-186">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="5e061-187">`Task.CompletedTask`ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="5e061-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="5e061-188">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="5e061-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="5e061-189">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="5e061-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="5e061-190">`ContactIsOwnerAuthorizationHandler`der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="5e061-191">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="5e061-191">Create a manager authorization handler</span></span>

<span data-ttu-id="5e061-192">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="5e061-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5e061-193">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="5e061-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="5e061-194">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="5e061-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="5e061-195">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="5e061-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="5e061-196">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="5e061-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5e061-197">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="5e061-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="5e061-198">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="5e061-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="5e061-199">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="5e061-199">Register the authorization handlers</span></span>

<span data-ttu-id="5e061-200">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="5e061-201">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="5e061-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="5e061-202">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="5e061-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5e061-203">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5e061-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="5e061-204">`ContactAdministratorsAuthorizationHandler`und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5e061-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="5e061-205">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="5e061-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="5e061-206">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="5e061-206">Support authorization</span></span>

<span data-ttu-id="5e061-207">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="5e061-208">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="5e061-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="5e061-209">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="5e061-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="5e061-210">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="5e061-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="5e061-211">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="5e061-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="5e061-212">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="5e061-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="5e061-213">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="5e061-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="5e061-214">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="5e061-214">The preceding code:</span></span>

* <span data-ttu-id="5e061-215">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="5e061-216">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="5e061-217">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="5e061-218">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="5e061-218">Update the CreateModel</span></span>

<span data-ttu-id="5e061-219">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="5e061-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="5e061-220">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="5e061-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="5e061-221">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="5e061-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="5e061-222">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="5e061-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="5e061-223">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="5e061-223">Update the IndexModel</span></span>

<span data-ttu-id="5e061-224">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="5e061-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="5e061-225">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="5e061-225">Update the EditModel</span></span>

<span data-ttu-id="5e061-226">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="5e061-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="5e061-227">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="5e061-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="5e061-228">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="5e061-229">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="5e061-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="5e061-230">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="5e061-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="5e061-231">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="5e061-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="5e061-232">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="5e061-232">Update the DeleteModel</span></span>

<span data-ttu-id="5e061-233">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="5e061-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="5e061-234">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="5e061-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="5e061-235">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="5e061-236">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="5e061-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="5e061-237">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="5e061-238">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="5e061-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="5e061-239">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="5e061-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="5e061-240">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="5e061-241">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="5e061-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="5e061-242">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="5e061-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="5e061-243">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="5e061-243">Update Details</span></span>

<span data-ttu-id="5e061-244">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="5e061-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="5e061-245">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="5e061-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="5e061-246">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="5e061-246">Add or remove a user to a role</span></span>

<span data-ttu-id="5e061-247">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="5e061-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="5e061-248">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="5e061-248">Removing privileges from a user.</span></span> <span data-ttu-id="5e061-249">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="5e061-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="5e061-250">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="5e061-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="5e061-251">Unterschiede zwischen Challenge und verboten</span><span class="sxs-lookup"><span data-stu-id="5e061-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="5e061-252">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#require-authenticated-users)sind.</span><span class="sxs-lookup"><span data-stu-id="5e061-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="5e061-253">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="5e061-253">The following code allows anonymous users.</span></span> <span data-ttu-id="5e061-254">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e061-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="5e061-255">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="5e061-255">In the preceding code:</span></span>

* <span data-ttu-id="5e061-256">Wenn der Benutzer **nicht** authentifiziert ist, `ChallengeResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5e061-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="5e061-257">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="5e061-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="5e061-258">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, `ForbidResult` wird eine zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="5e061-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="5e061-259">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite Zugriff verweigert umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="5e061-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="5e061-260">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="5e061-260">Test the completed app</span></span>

<span data-ttu-id="5e061-261">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="5e061-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="5e061-262">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="5e061-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="5e061-263">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="5e061-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="5e061-264">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="5e061-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="5e061-265">Wenn die APP Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="5e061-265">If the app has contacts:</span></span>

* <span data-ttu-id="5e061-266">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="5e061-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="5e061-267">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="5e061-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="5e061-268">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="5e061-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="5e061-269">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="5e061-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="5e061-270">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="5e061-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="5e061-271">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="5e061-271">Verify the following operations:</span></span>

* <span data-ttu-id="5e061-272">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e061-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="5e061-273">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="5e061-274">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="5e061-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="5e061-275">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="5e061-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="5e061-276">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="5e061-277">Benutzer</span><span class="sxs-lookup"><span data-stu-id="5e061-277">User</span></span>                | <span data-ttu-id="5e061-278">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="5e061-278">Seeded by the app</span></span> | <span data-ttu-id="5e061-279">Optionen</span><span class="sxs-lookup"><span data-stu-id="5e061-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="5e061-280">Nein</span><span class="sxs-lookup"><span data-stu-id="5e061-280">No</span></span>                | <span data-ttu-id="5e061-281">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="5e061-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="5e061-282">Ja</span><span class="sxs-lookup"><span data-stu-id="5e061-282">Yes</span></span>               | <span data-ttu-id="5e061-283">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="5e061-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="5e061-284">Ja</span><span class="sxs-lookup"><span data-stu-id="5e061-284">Yes</span></span>               | <span data-ttu-id="5e061-285">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="5e061-286">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="5e061-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="5e061-287">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="5e061-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="5e061-288">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="5e061-289">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="5e061-289">Create the starter app</span></span>

* <span data-ttu-id="5e061-290">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="5e061-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="5e061-291">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="5e061-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="5e061-292">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5e061-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="5e061-293">`-uld`gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="5e061-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="5e061-294">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="5e061-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="5e061-295">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="5e061-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="5e061-296">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="5e061-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="5e061-297">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="5e061-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="5e061-298">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5e061-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="5e061-299">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="5e061-300">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="5e061-300">Seed the database</span></span>

<span data-ttu-id="5e061-301">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="5e061-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="5e061-302">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="5e061-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="5e061-303">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="5e061-303">Test that the app seeded the database.</span></span> <span data-ttu-id="5e061-304">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5e061-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="5e061-305">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="5e061-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="5e061-306">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="5e061-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="5e061-307">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="5e061-307">There are three security groups:</span></span>

* <span data-ttu-id="5e061-308">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="5e061-309">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="5e061-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="5e061-310">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="5e061-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="5e061-311">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="5e061-312">In der folgenden Abbildung ist der Benutzer Rick ( `rick@example.com` ) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="5e061-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="5e061-313">Rick kann nur genehmigte Kontakte anzeigen und **Edit** / **Löschen**bearbeiten / **neue** Verknüpfungen für seine Kontakte erstellen.</span><span class="sxs-lookup"><span data-stu-id="5e061-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="5e061-314">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="5e061-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="5e061-315">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="5e061-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="5e061-317">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="5e061-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot manager@contoso.com der Anmeldung](secure-data/_static/manager1.png)

<span data-ttu-id="5e061-319">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="5e061-319">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="5e061-321">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="5e061-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="5e061-322">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="5e061-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot admin@contoso.com der Anmeldung](secure-data/_static/admin.png)

<span data-ttu-id="5e061-324">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="5e061-324">The administrator has all privileges.</span></span> <span data-ttu-id="5e061-325">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="5e061-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="5e061-326">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) auf das folgende `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="5e061-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="5e061-327">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="5e061-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="5e061-328">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="5e061-329">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="5e061-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="5e061-330">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="5e061-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="5e061-331">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="5e061-331">Prerequisites</span></span>

<span data-ttu-id="5e061-332">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="5e061-332">This tutorial is advanced.</span></span> <span data-ttu-id="5e061-333">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="5e061-333">You should be familiar with:</span></span>

* [<span data-ttu-id="5e061-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="5e061-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="5e061-335">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="5e061-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="5e061-336">Konto Bestätigung und Kenn Wort Wiederherstellung</span><span class="sxs-lookup"><span data-stu-id="5e061-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="5e061-337">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="5e061-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="5e061-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="5e061-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="5e061-339">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="5e061-339">The starter and completed app</span></span>

<span data-ttu-id="5e061-340">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="5e061-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="5e061-341">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="5e061-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="5e061-342">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="5e061-342">The starter app</span></span>

<span data-ttu-id="5e061-343">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="5e061-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="5e061-344">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="5e061-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="5e061-345">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="5e061-345">Secure user data</span></span>

<span data-ttu-id="5e061-346">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="5e061-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="5e061-347">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="5e061-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="5e061-348">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="5e061-348">Tie the contact data to the user</span></span>

<span data-ttu-id="5e061-349">Verwenden Sie die [Identity](xref:security/authentication/identity) Benutzer-ID ASP.net, um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="5e061-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="5e061-350">Fügen `OwnerID` Sie `ContactStatus` dem Modell und hinzu `Contact` :</span><span class="sxs-lookup"><span data-stu-id="5e061-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="5e061-351">`OwnerID`die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identity](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="5e061-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="5e061-352">Das- `Status` Feld bestimmt, ob ein Kontakt durch allgemeine Benutzer angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="5e061-353">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="5e061-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="5e061-354">Rollen Dienste hinzufügen zuIdentity</span><span class="sxs-lookup"><span data-stu-id="5e061-354">Add Role services to Identity</span></span>

<span data-ttu-id="5e061-355">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="5e061-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="5e061-356">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="5e061-356">Require authenticated users</span></span>

<span data-ttu-id="5e061-357">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="5e061-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="5e061-358">Sie können die Authentifizierung auf Razor Seiten-, Controller-oder Aktionsmethoden Ebene mit dem- `[AllowAnonymous]` Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="5e061-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="5e061-359">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Seiten und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="5e061-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="5e061-360">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Seiten zum Einbeziehen des `[Authorize]` Attributs.</span><span class="sxs-lookup"><span data-stu-id="5e061-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="5e061-361">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="5e061-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="5e061-362">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="5e061-362">Configure the test account</span></span>

<span data-ttu-id="5e061-363">Die `SeedData` -Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="5e061-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="5e061-364">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="5e061-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="5e061-365">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="5e061-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="5e061-366">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="5e061-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="5e061-367">Aktualisieren `Main` Sie, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="5e061-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="5e061-368">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="5e061-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="5e061-369">Aktualisieren Sie die- `Initialize` Methode in der- `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="5e061-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="5e061-370">Fügen Sie die Benutzer-ID des Administrators und `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="5e061-371">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="5e061-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="5e061-372">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="5e061-373">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="5e061-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="5e061-374">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="5e061-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="5e061-375">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen `ContactIsOwnerAuthorizationHandler` Sie eine Klasse darin.</span><span class="sxs-lookup"><span data-stu-id="5e061-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="5e061-376">Mit wird `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="5e061-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="5e061-377">Der `ContactIsOwnerAuthorizationHandler` Aufruf [Kontext. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="5e061-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="5e061-378">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="5e061-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="5e061-379">Gibt zurück, `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="5e061-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="5e061-380">Gibt zurück, `Task.CompletedTask` Wenn die Anforderungen nicht erfüllt werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="5e061-381">`Task.CompletedTask`ist nicht erfolgreich oder fehlerhaft &mdash; , sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="5e061-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="5e061-382">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="5e061-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="5e061-383">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="5e061-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="5e061-384">`ContactIsOwnerAuthorizationHandler`der im Anforderungs Parameter übergebenen Vorgang muss nicht überprüft werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="5e061-385">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="5e061-385">Create a manager authorization handler</span></span>

<span data-ttu-id="5e061-386">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="5e061-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5e061-387">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="5e061-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="5e061-388">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="5e061-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="5e061-389">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="5e061-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="5e061-390">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="5e061-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="5e061-391">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="5e061-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="5e061-392">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="5e061-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="5e061-393">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="5e061-393">Register the authorization handlers</span></span>

<span data-ttu-id="5e061-394">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="5e061-395">`ContactIsOwnerAuthorizationHandler`Verwendet ASP.net Core [Identity](xref:security/authentication/identity) , das auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="5e061-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="5e061-396">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="5e061-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5e061-397">Fügen Sie den folgenden Code am Ende von hinzu `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="5e061-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="5e061-398">`ContactAdministratorsAuthorizationHandler`und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="5e061-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="5e061-399">Sie sind Singletons, da Sie EF nicht verwenden. alle benötigten Informationen sind im- `Context` Parameter der- `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="5e061-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="5e061-400">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="5e061-400">Support authorization</span></span>

<span data-ttu-id="5e061-401">In diesem Abschnitt aktualisieren Sie die Razor Seiten und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="5e061-402">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="5e061-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="5e061-403">Überprüfen Sie die- `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="5e061-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="5e061-404">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="5e061-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="5e061-405">Erstellen einer Basisklasse für die Seite "Kontakte" Razor</span><span class="sxs-lookup"><span data-stu-id="5e061-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="5e061-406">Erstellen Sie eine Basisklasse, die die in den Kontaktseiten verwendeten Dienste enthält Razor .</span><span class="sxs-lookup"><span data-stu-id="5e061-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="5e061-407">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="5e061-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="5e061-408">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="5e061-408">The preceding code:</span></span>

* <span data-ttu-id="5e061-409">Fügt den `IAuthorizationService` Dienst für den Zugriff auf die Autorisierungs Handler hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="5e061-410">Fügt den Identity `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="5e061-411">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="5e061-412">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="5e061-412">Update the CreateModel</span></span>

<span data-ttu-id="5e061-413">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="5e061-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="5e061-414">Aktualisieren Sie die-Methode wie folgt `CreateModel.OnPostAsync` :</span><span class="sxs-lookup"><span data-stu-id="5e061-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="5e061-415">Fügen Sie dem Modell die Benutzer-ID hinzu `Contact` .</span><span class="sxs-lookup"><span data-stu-id="5e061-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="5e061-416">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="5e061-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="5e061-417">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="5e061-417">Update the IndexModel</span></span>

<span data-ttu-id="5e061-418">Aktualisieren Sie die- `OnGetAsync` Methode, sodass den allgemeinen Benutzern nur genehmigte Kontakte angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="5e061-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="5e061-419">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="5e061-419">Update the EditModel</span></span>

<span data-ttu-id="5e061-420">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="5e061-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="5e061-421">Da die Ressourcen Autorisierung überprüft wird, `[Authorize]` genügt das-Attribut nicht.</span><span class="sxs-lookup"><span data-stu-id="5e061-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="5e061-422">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="5e061-423">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="5e061-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="5e061-424">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="5e061-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="5e061-425">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="5e061-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="5e061-426">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="5e061-426">Update the DeleteModel</span></span>

<span data-ttu-id="5e061-427">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="5e061-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="5e061-428">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="5e061-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="5e061-429">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="5e061-430">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="5e061-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="5e061-431">Das vorangehende Markup fügt mehrere- `using` Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="5e061-432">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="5e061-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="5e061-433">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="5e061-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="5e061-434">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="5e061-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="5e061-435">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="5e061-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="5e061-436">Die Razor Seite oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="5e061-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="5e061-437">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="5e061-437">Update Details</span></span>

<span data-ttu-id="5e061-438">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="5e061-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="5e061-439">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="5e061-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="5e061-440">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="5e061-440">Add or remove a user to a role</span></span>

<span data-ttu-id="5e061-441">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/dotnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="5e061-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="5e061-442">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="5e061-442">Removing privileges from a user.</span></span> <span data-ttu-id="5e061-443">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="5e061-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="5e061-444">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="5e061-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="5e061-445">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="5e061-445">Test the completed app</span></span>

<span data-ttu-id="5e061-446">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="5e061-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="5e061-447">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="5e061-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="5e061-448">Beispielsweise `Passw0rd!` erfüllt die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="5e061-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="5e061-449">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="5e061-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="5e061-450">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="5e061-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="5e061-451">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="5e061-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="5e061-452">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="5e061-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="5e061-453">Registrieren Sie in einem Browser einen neuen Benutzer (z. b `test@example.com` .).</span><span class="sxs-lookup"><span data-stu-id="5e061-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="5e061-454">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="5e061-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="5e061-455">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="5e061-455">Verify the following operations:</span></span>

* <span data-ttu-id="5e061-456">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="5e061-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="5e061-457">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="5e061-458">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="5e061-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="5e061-459">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="5e061-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="5e061-460">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="5e061-461">Benutzer</span><span class="sxs-lookup"><span data-stu-id="5e061-461">User</span></span>                | <span data-ttu-id="5e061-462">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="5e061-462">Seeded by the app</span></span> | <span data-ttu-id="5e061-463">Optionen</span><span class="sxs-lookup"><span data-stu-id="5e061-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="5e061-464">Nein</span><span class="sxs-lookup"><span data-stu-id="5e061-464">No</span></span>                | <span data-ttu-id="5e061-465">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="5e061-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="5e061-466">Ja</span><span class="sxs-lookup"><span data-stu-id="5e061-466">Yes</span></span>               | <span data-ttu-id="5e061-467">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="5e061-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="5e061-468">Ja</span><span class="sxs-lookup"><span data-stu-id="5e061-468">Yes</span></span>               | <span data-ttu-id="5e061-469">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="5e061-470">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="5e061-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="5e061-471">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="5e061-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="5e061-472">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="5e061-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="5e061-473">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="5e061-473">Create the starter app</span></span>

* <span data-ttu-id="5e061-474">Erstellen Sie eine Seiten-App mit dem Razor Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="5e061-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="5e061-475">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="5e061-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="5e061-476">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="5e061-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="5e061-477">`-uld`gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="5e061-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="5e061-478">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="5e061-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="5e061-479">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="5e061-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="5e061-480">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="5e061-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="5e061-481">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="5e061-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="5e061-482">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="5e061-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="5e061-483">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="5e061-483">Seed the database</span></span>

<span data-ttu-id="5e061-484">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="5e061-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="5e061-485">Aufrufe `SeedData.Initialize` von `Main` :</span><span class="sxs-lookup"><span data-stu-id="5e061-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="5e061-486">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="5e061-486">Test that the app seeded the database.</span></span> <span data-ttu-id="5e061-487">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="5e061-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="5e061-488">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="5e061-488">Additional resources</span></span>

* [<span data-ttu-id="5e061-489">Erstellen einer .NET Core- und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="5e061-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="5e061-490">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="5e061-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="5e061-491">In dieser Übungseinheit werden die in diesem Tutorial vorgestellten Sicherheitsfeatures ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="5e061-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="5e061-492">Benutzerdefinierte Richtlinien basierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="5e061-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
