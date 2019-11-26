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
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="fa4bd-104">Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind</span><span class="sxs-lookup"><span data-stu-id="fa4bd-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="fa4bd-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="fa4bd-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="fa4bd-106">Informationen zur ASP.net Core MVC-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) .</span><span class="sxs-lookup"><span data-stu-id="fa4bd-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="fa4bd-107">Die ASP.net Core 1,1-Version dieses Tutorials finden Sie in [diesem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="fa4bd-108">Das 1,1-ASP.net Core Beispiel finden Sie in den [Beispielen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="fa4bd-109">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fa4bd-110">In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="fa4bd-111">Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="fa4bd-112">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-112">There are three security groups:</span></span>

* <span data-ttu-id="fa4bd-113">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="fa4bd-114">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="fa4bd-115">Nur genehmigte Kontakte für Benutzer sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="fa4bd-116">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="fa4bd-117">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="fa4bd-118">In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="fa4bd-119">Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** .</span><span class="sxs-lookup"><span data-stu-id="fa4bd-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="fa4bd-120">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="fa4bd-121">Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

<span data-ttu-id="fa4bd-123">In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

<span data-ttu-id="fa4bd-125">Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-125">The following image shows the managers details view of a contact:</span></span>

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="fa4bd-127">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="fa4bd-128">In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

<span data-ttu-id="fa4bd-130">Der Administrator muss alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-130">The administrator has all privileges.</span></span> <span data-ttu-id="fa4bd-131">Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="fa4bd-132">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="fa4bd-133">Das Beispiel enthält die folgenden Handler für die Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="fa4bd-134">`ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="fa4bd-135">`ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="fa4bd-136">`ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fa4bd-137">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-137">Prerequisites</span></span>

<span data-ttu-id="fa4bd-138">In diesem Tutorial wird verschoben.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-138">This tutorial is advanced.</span></span> <span data-ttu-id="fa4bd-139">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-139">You should be familiar with:</span></span>

* [<span data-ttu-id="fa4bd-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa4bd-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="fa4bd-141">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="fa4bd-142">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="fa4bd-143">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="fa4bd-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="fa4bd-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="fa4bd-145">Die Starter und die fertige app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-145">The starter and completed app</span></span>

<span data-ttu-id="fa4bd-146">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="fa4bd-147">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="fa4bd-148">Das Starter-app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-148">The starter app</span></span>

<span data-ttu-id="fa4bd-149">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="fa4bd-150">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="fa4bd-151">Schützen von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="fa4bd-151">Secure user data</span></span>

<span data-ttu-id="fa4bd-152">Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="fa4bd-153">Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="fa4bd-154">Verknüpfen Sie die Kontaktdaten für den Benutzer</span><span class="sxs-lookup"><span data-stu-id="fa4bd-154">Tie the contact data to the user</span></span>

<span data-ttu-id="fa4bd-155">Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="fa4bd-156">Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="fa4bd-157">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="fa4bd-158">Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="fa4bd-159">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="fa4bd-160">Fügen Sie Rollendienste hinzu, die Identität</span><span class="sxs-lookup"><span data-stu-id="fa4bd-160">Add Role services to Identity</span></span>

<span data-ttu-id="fa4bd-161">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="fa4bd-162">Authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="fa4bd-162">Require authenticated users</span></span>

<span data-ttu-id="fa4bd-163">Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="fa4bd-164">Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="fa4bd-165">Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="fa4bd-166">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="fa4bd-167">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-und datenschutzseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="fa4bd-168">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="fa4bd-168">Configure the test account</span></span>

<span data-ttu-id="fa4bd-169">Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager".</span><span class="sxs-lookup"><span data-stu-id="fa4bd-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="fa4bd-170">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="fa4bd-171">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="fa4bd-172">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="fa4bd-173">Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="fa4bd-174">Erstellen Sie die Testkonten und aktualisieren Kontakte</span><span class="sxs-lookup"><span data-stu-id="fa4bd-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="fa4bd-175">Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="fa4bd-176">Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="fa4bd-177">Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="fa4bd-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="fa4bd-178">Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="fa4bd-179">Nur ein Kontakt wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="fa4bd-180">Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="fa4bd-181">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="fa4bd-182">Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="fa4bd-183">Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="fa4bd-184">Autorisierung Handler in der Regel:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="fa4bd-185">Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="fa4bd-186">Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="fa4bd-187">`Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="fa4bd-188">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="fa4bd-189">Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="fa4bd-190">`ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="fa4bd-191">Erstellen Sie einen Ereignishandler der Manager-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-191">Create a manager authorization handler</span></span>

<span data-ttu-id="fa4bd-192">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="fa4bd-193">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="fa4bd-194">Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="fa4bd-195">Erstellen Sie ein Administrator autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="fa4bd-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="fa4bd-196">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="fa4bd-197">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="fa4bd-198">Administrator möglich, alle Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="fa4bd-199">Registrieren Sie die Autorisierung-Handler</span><span class="sxs-lookup"><span data-stu-id="fa4bd-199">Register the authorization handlers</span></span>

<span data-ttu-id="fa4bd-200">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="fa4bd-201">Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="fa4bd-202">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fa4bd-203">Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="fa4bd-204">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="fa4bd-205">Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="fa4bd-206">Unterstützung für Autorisierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-206">Support authorization</span></span>

<span data-ttu-id="fa4bd-207">In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="fa4bd-208">Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse</span><span class="sxs-lookup"><span data-stu-id="fa4bd-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="fa4bd-209">Überprüfen Sie die `ContactOperations`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="fa4bd-210">Diese Klasse enthält die Anforderungen der app unterstützt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="fa4bd-211">Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="fa4bd-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="fa4bd-212">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="fa4bd-213">Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="fa4bd-214">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-214">The preceding code:</span></span>

* <span data-ttu-id="fa4bd-215">Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="fa4bd-216">Fügt den Identitäts `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="fa4bd-217">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="fa4bd-218">Aktualisieren der CreateModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-218">Update the CreateModel</span></span>

<span data-ttu-id="fa4bd-219">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="fa4bd-220">Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="fa4bd-221">Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="fa4bd-222">Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="fa4bd-223">Aktualisieren Sie die IndexModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-223">Update the IndexModel</span></span>

<span data-ttu-id="fa4bd-224">Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="fa4bd-225">Aktualisieren Sie die EditModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-225">Update the EditModel</span></span>

<span data-ttu-id="fa4bd-226">Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="fa4bd-227">Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="fa4bd-228">Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="fa4bd-229">Ressourcenbasierte Autorisierung muss zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="fa4bd-230">Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="fa4bd-231">Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="fa4bd-232">Aktualisieren Sie die DeleteModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-232">Update the DeleteModel</span></span>

<span data-ttu-id="fa4bd-233">Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="fa4bd-234">Fügen Sie dem Prüfdienst in den Ansichten</span><span class="sxs-lookup"><span data-stu-id="fa4bd-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="fa4bd-235">Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="fa4bd-236">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="fa4bd-237">Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="fa4bd-238">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="fa4bd-239">Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="fa4bd-240">Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="fa4bd-241">Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="fa4bd-242">Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="fa4bd-243">Updatedetails</span><span class="sxs-lookup"><span data-stu-id="fa4bd-243">Update Details</span></span>

<span data-ttu-id="fa4bd-244">Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="fa4bd-245">Aktualisieren Sie das Seitenmodell Details an:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="fa4bd-246">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="fa4bd-246">Add or remove a user to a role</span></span>

<span data-ttu-id="fa4bd-247">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="fa4bd-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="fa4bd-248">Entfernen Berechtigungen eines Benutzers ein.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-248">Removing privileges from a user.</span></span> <span data-ttu-id="fa4bd-249">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="fa4bd-250">Hinzufügen von Berechtigungen für einen Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="fa4bd-251">Unterschiede zwischen Challenge und verboten</span><span class="sxs-lookup"><span data-stu-id="fa4bd-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="fa4bd-252">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#require-authenticated-users)sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="fa4bd-253">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-253">The following code allows anonymous users.</span></span> <span data-ttu-id="fa4bd-254">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="fa4bd-255">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-255">In the preceding code:</span></span>

* <span data-ttu-id="fa4bd-256">Wenn der Benutzer **nicht** authentifiziert ist, wird ein `ChallengeResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="fa4bd-257">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="fa4bd-258">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, wird ein `ForbidResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="fa4bd-259">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite "Zugriff verweigert" umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="fa4bd-260">Testen Sie die fertige app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-260">Test the completed app</span></span>

<span data-ttu-id="fa4bd-261">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="fa4bd-262">Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens einen Großbuchstaben, Anzahl und Symbol.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="fa4bd-263">`Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="fa4bd-264">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="fa4bd-265">Wenn die app Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-265">If the app has contacts:</span></span>

* <span data-ttu-id="fa4bd-266">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="fa4bd-267">Starten Sie die app zum Seeding der Datenbank neu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="fa4bd-268">Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="fa4bd-269">Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="fa4bd-270">Melden Sie sich an jeden Browser mit einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="fa4bd-271">Überprüfen Sie die folgenden Vorgänge aus:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-271">Verify the following operations:</span></span>

* <span data-ttu-id="fa4bd-272">Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="fa4bd-273">Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="fa4bd-274">Manager können genehmigen/Kontaktdaten ablehnen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="fa4bd-275">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="fa4bd-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="fa4bd-276">Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="fa4bd-277">Benutzer</span><span class="sxs-lookup"><span data-stu-id="fa4bd-277">User</span></span>                | <span data-ttu-id="fa4bd-278">Ein Seeding ausgeführt, von der app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-278">Seeded by the app</span></span> | <span data-ttu-id="fa4bd-279">Optionen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="fa4bd-280">nein</span><span class="sxs-lookup"><span data-stu-id="fa4bd-280">No</span></span>                | <span data-ttu-id="fa4bd-281">Löschen Sie bearbeiten und die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="fa4bd-282">Ja</span><span class="sxs-lookup"><span data-stu-id="fa4bd-282">Yes</span></span>               | <span data-ttu-id="fa4bd-283">Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="fa4bd-284">Ja</span><span class="sxs-lookup"><span data-stu-id="fa4bd-284">Yes</span></span>               | <span data-ttu-id="fa4bd-285">Genehmigen oder ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="fa4bd-286">Erstellen Sie einen Kontakt in der Administrator-Browser.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="fa4bd-287">Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="fa4bd-288">Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="fa4bd-289">Die Starter-app erstellen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-289">Create the starter app</span></span>

* <span data-ttu-id="fa4bd-290">Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="fa4bd-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="fa4bd-291">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="fa4bd-292">Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="fa4bd-293">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="fa4bd-294">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="fa4bd-295">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="fa4bd-296">Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="fa4bd-297">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="fa4bd-298">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="fa4bd-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="fa4bd-299">Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts</span><span class="sxs-lookup"><span data-stu-id="fa4bd-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="fa4bd-300">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="fa4bd-300">Seed the database</span></span>

<span data-ttu-id="fa4bd-301">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="fa4bd-302">`SeedData.Initialize` aus `Main`abrufen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="fa4bd-303">Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-303">Test that the app seeded the database.</span></span> <span data-ttu-id="fa4bd-304">Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="fa4bd-305">In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="fa4bd-306">Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="fa4bd-307">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-307">There are three security groups:</span></span>

* <span data-ttu-id="fa4bd-308">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="fa4bd-309">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="fa4bd-310">Nur genehmigte Kontakte für Benutzer sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="fa4bd-311">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="fa4bd-312">In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="fa4bd-313">Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** .</span><span class="sxs-lookup"><span data-stu-id="fa4bd-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="fa4bd-314">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="fa4bd-315">Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

<span data-ttu-id="fa4bd-317">In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

<span data-ttu-id="fa4bd-319">Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-319">The following image shows the managers details view of a contact:</span></span>

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="fa4bd-321">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="fa4bd-322">In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

<span data-ttu-id="fa4bd-324">Der Administrator muss alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-324">The administrator has all privileges.</span></span> <span data-ttu-id="fa4bd-325">Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="fa4bd-326">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="fa4bd-327">Das Beispiel enthält die folgenden Handler für die Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="fa4bd-328">`ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="fa4bd-329">`ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="fa4bd-330">`ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fa4bd-331">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-331">Prerequisites</span></span>

<span data-ttu-id="fa4bd-332">In diesem Tutorial wird verschoben.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-332">This tutorial is advanced.</span></span> <span data-ttu-id="fa4bd-333">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-333">You should be familiar with:</span></span>

* [<span data-ttu-id="fa4bd-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa4bd-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="fa4bd-335">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="fa4bd-336">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="fa4bd-337">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="fa4bd-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="fa4bd-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="fa4bd-339">Die Starter und die fertige app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-339">The starter and completed app</span></span>

<span data-ttu-id="fa4bd-340">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="fa4bd-341">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="fa4bd-342">Das Starter-app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-342">The starter app</span></span>

<span data-ttu-id="fa4bd-343">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="fa4bd-344">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="fa4bd-345">Schützen von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="fa4bd-345">Secure user data</span></span>

<span data-ttu-id="fa4bd-346">Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="fa4bd-347">Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="fa4bd-348">Verknüpfen Sie die Kontaktdaten für den Benutzer</span><span class="sxs-lookup"><span data-stu-id="fa4bd-348">Tie the contact data to the user</span></span>

<span data-ttu-id="fa4bd-349">Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="fa4bd-350">Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="fa4bd-351">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="fa4bd-352">Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="fa4bd-353">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="fa4bd-354">Fügen Sie Rollendienste hinzu, die Identität</span><span class="sxs-lookup"><span data-stu-id="fa4bd-354">Add Role services to Identity</span></span>

<span data-ttu-id="fa4bd-355">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="fa4bd-356">Authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="fa4bd-356">Require authenticated users</span></span>

<span data-ttu-id="fa4bd-357">Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="fa4bd-358">Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="fa4bd-359">Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="fa4bd-360">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="fa4bd-361">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="fa4bd-362">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="fa4bd-362">Configure the test account</span></span>

<span data-ttu-id="fa4bd-363">Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager".</span><span class="sxs-lookup"><span data-stu-id="fa4bd-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="fa4bd-364">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="fa4bd-365">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="fa4bd-366">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="fa4bd-367">Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="fa4bd-368">Erstellen Sie die Testkonten und aktualisieren Kontakte</span><span class="sxs-lookup"><span data-stu-id="fa4bd-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="fa4bd-369">Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="fa4bd-370">Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="fa4bd-371">Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="fa4bd-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="fa4bd-372">Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="fa4bd-373">Nur ein Kontakt wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="fa4bd-374">Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="fa4bd-375">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen Sie darin eine `ContactIsOwnerAuthorizationHandler`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="fa4bd-376">Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="fa4bd-377">Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="fa4bd-378">Autorisierung Handler in der Regel:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="fa4bd-379">Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="fa4bd-380">Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="fa4bd-381">`Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="fa4bd-382">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="fa4bd-383">Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="fa4bd-384">`ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="fa4bd-385">Erstellen Sie einen Ereignishandler der Manager-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-385">Create a manager authorization handler</span></span>

<span data-ttu-id="fa4bd-386">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="fa4bd-387">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="fa4bd-388">Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="fa4bd-389">Erstellen Sie ein Administrator autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="fa4bd-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="fa4bd-390">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="fa4bd-391">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="fa4bd-392">Administrator möglich, alle Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="fa4bd-393">Registrieren Sie die Autorisierung-Handler</span><span class="sxs-lookup"><span data-stu-id="fa4bd-393">Register the authorization handlers</span></span>

<span data-ttu-id="fa4bd-394">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="fa4bd-395">Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="fa4bd-396">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fa4bd-397">Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="fa4bd-398">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="fa4bd-399">Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="fa4bd-400">Unterstützung für Autorisierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-400">Support authorization</span></span>

<span data-ttu-id="fa4bd-401">In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="fa4bd-402">Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse</span><span class="sxs-lookup"><span data-stu-id="fa4bd-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="fa4bd-403">Überprüfen Sie die `ContactOperations`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="fa4bd-404">Diese Klasse enthält die Anforderungen der app unterstützt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="fa4bd-405">Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="fa4bd-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="fa4bd-406">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="fa4bd-407">Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="fa4bd-408">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-408">The preceding code:</span></span>

* <span data-ttu-id="fa4bd-409">Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="fa4bd-410">Fügt den Identitäts `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="fa4bd-411">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="fa4bd-412">Aktualisieren der CreateModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-412">Update the CreateModel</span></span>

<span data-ttu-id="fa4bd-413">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="fa4bd-414">Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="fa4bd-415">Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="fa4bd-416">Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="fa4bd-417">Aktualisieren Sie die IndexModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-417">Update the IndexModel</span></span>

<span data-ttu-id="fa4bd-418">Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="fa4bd-419">Aktualisieren Sie die EditModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-419">Update the EditModel</span></span>

<span data-ttu-id="fa4bd-420">Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="fa4bd-421">Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="fa4bd-422">Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="fa4bd-423">Ressourcenbasierte Autorisierung muss zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="fa4bd-424">Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="fa4bd-425">Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="fa4bd-426">Aktualisieren Sie die DeleteModel</span><span class="sxs-lookup"><span data-stu-id="fa4bd-426">Update the DeleteModel</span></span>

<span data-ttu-id="fa4bd-427">Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="fa4bd-428">Fügen Sie dem Prüfdienst in den Ansichten</span><span class="sxs-lookup"><span data-stu-id="fa4bd-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="fa4bd-429">Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="fa4bd-430">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="fa4bd-431">Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="fa4bd-432">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="fa4bd-433">Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="fa4bd-434">Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="fa4bd-435">Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="fa4bd-436">Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="fa4bd-437">Updatedetails</span><span class="sxs-lookup"><span data-stu-id="fa4bd-437">Update Details</span></span>

<span data-ttu-id="fa4bd-438">Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="fa4bd-439">Aktualisieren Sie das Seitenmodell Details an:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="fa4bd-440">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="fa4bd-440">Add or remove a user to a role</span></span>

<span data-ttu-id="fa4bd-441">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="fa4bd-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="fa4bd-442">Entfernen Berechtigungen eines Benutzers ein.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-442">Removing privileges from a user.</span></span> <span data-ttu-id="fa4bd-443">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="fa4bd-444">Hinzufügen von Berechtigungen für einen Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="fa4bd-445">Testen Sie die fertige app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-445">Test the completed app</span></span>

<span data-ttu-id="fa4bd-446">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="fa4bd-447">Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens einen Großbuchstaben, Anzahl und Symbol.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="fa4bd-448">`Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="fa4bd-449">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="fa4bd-450">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="fa4bd-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="fa4bd-451">Starten Sie die app zum Seeding der Datenbank neu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="fa4bd-452">Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="fa4bd-453">Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="fa4bd-454">Melden Sie sich an jeden Browser mit einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="fa4bd-455">Überprüfen Sie die folgenden Vorgänge aus:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-455">Verify the following operations:</span></span>

* <span data-ttu-id="fa4bd-456">Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="fa4bd-457">Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="fa4bd-458">Manager können genehmigen/Kontaktdaten ablehnen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="fa4bd-459">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="fa4bd-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="fa4bd-460">Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="fa4bd-461">Benutzer</span><span class="sxs-lookup"><span data-stu-id="fa4bd-461">User</span></span>                | <span data-ttu-id="fa4bd-462">Ein Seeding ausgeführt, von der app</span><span class="sxs-lookup"><span data-stu-id="fa4bd-462">Seeded by the app</span></span> | <span data-ttu-id="fa4bd-463">Optionen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="fa4bd-464">nein</span><span class="sxs-lookup"><span data-stu-id="fa4bd-464">No</span></span>                | <span data-ttu-id="fa4bd-465">Löschen Sie bearbeiten und die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="fa4bd-466">Ja</span><span class="sxs-lookup"><span data-stu-id="fa4bd-466">Yes</span></span>               | <span data-ttu-id="fa4bd-467">Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="fa4bd-468">Ja</span><span class="sxs-lookup"><span data-stu-id="fa4bd-468">Yes</span></span>               | <span data-ttu-id="fa4bd-469">Genehmigen oder ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="fa4bd-470">Erstellen Sie einen Kontakt in der Administrator-Browser.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="fa4bd-471">Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="fa4bd-472">Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="fa4bd-473">Die Starter-app erstellen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-473">Create the starter app</span></span>

* <span data-ttu-id="fa4bd-474">Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="fa4bd-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="fa4bd-475">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="fa4bd-476">Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="fa4bd-477">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="fa4bd-478">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="fa4bd-479">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="fa4bd-480">Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="fa4bd-481">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="fa4bd-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="fa4bd-482">Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts</span><span class="sxs-lookup"><span data-stu-id="fa4bd-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="fa4bd-483">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="fa4bd-483">Seed the database</span></span>

<span data-ttu-id="fa4bd-484">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="fa4bd-485">`SeedData.Initialize` aus `Main`abrufen:</span><span class="sxs-lookup"><span data-stu-id="fa4bd-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="fa4bd-486">Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-486">Test that the app seeded the database.</span></span> <span data-ttu-id="fa4bd-487">Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="fa4bd-488">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fa4bd-488">Additional resources</span></span>

* [<span data-ttu-id="fa4bd-489">Erstellen einer .net Core-und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="fa4bd-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="fa4bd-490">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="fa4bd-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="fa4bd-491">Dieser Übung wird ausführlicher auf den Sicherheitsfeatures, die in diesem Lernprogramm eingeführt.</span><span class="sxs-lookup"><span data-stu-id="fa4bd-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="fa4bd-492">Benutzerdefinierte, richtlinienbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="fa4bd-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
