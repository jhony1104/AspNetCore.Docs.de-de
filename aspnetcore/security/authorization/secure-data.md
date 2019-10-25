---
title: Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten
author: rick-anderson
description: Erfahren Sie, wie Sie eine Razor Pages-App mit von der Autorisierung geschützten Benutzerdaten erstellen. Umfasst HTTPS, Authentifizierung, Sicherheit, ASP.net Core Identität.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 6e2f785a6dc014884f105766686f284cb2685530
ms.sourcegitcommit: 383017d7060a6d58f6a79cf4d7335d5b4b6c5659
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72816152"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="80782-104">Erstellen einer ASP.net Core-App mit von der Autorisierung geschützten Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="80782-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="80782-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="80782-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="80782-106">Informationen zur ASP.net Core MVC-Version finden Sie in [dieser PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) .</span><span class="sxs-lookup"><span data-stu-id="80782-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="80782-107">Die ASP.net Core 1,1-Version dieses Tutorials finden Sie in [diesem](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner.</span><span class="sxs-lookup"><span data-stu-id="80782-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="80782-108">Das 1,1-ASP.net Core Beispiel finden Sie in den [Beispielen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="80782-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="80782-109">[Diese PDF-Datei](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf) anzeigen</span><span class="sxs-lookup"><span data-stu-id="80782-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="80782-110">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="80782-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="80782-111">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="80782-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="80782-112">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="80782-112">There are three security groups:</span></span>

* <span data-ttu-id="80782-113">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="80782-114">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="80782-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="80782-115">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="80782-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="80782-116">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="80782-117">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="80782-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="80782-118">In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="80782-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="80782-119">Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** .</span><span class="sxs-lookup"><span data-stu-id="80782-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="80782-120">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="80782-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="80782-121">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="80782-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="80782-123">In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:</span><span class="sxs-lookup"><span data-stu-id="80782-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

<span data-ttu-id="80782-125">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="80782-125">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="80782-127">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="80782-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="80782-128">In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="80782-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

<span data-ttu-id="80782-130">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="80782-130">The administrator has all privileges.</span></span> <span data-ttu-id="80782-131">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="80782-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="80782-132">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="80782-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="80782-133">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="80782-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="80782-134">`ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="80782-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="80782-135">`ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="80782-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="80782-136">`ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="80782-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80782-137">Erforderliche Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="80782-137">Prerequisites</span></span>

<span data-ttu-id="80782-138">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="80782-138">This tutorial is advanced.</span></span> <span data-ttu-id="80782-139">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="80782-139">You should be familiar with:</span></span>

* [<span data-ttu-id="80782-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80782-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="80782-141">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="80782-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="80782-142">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="80782-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="80782-143">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="80782-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="80782-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="80782-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="80782-145">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="80782-145">The starter and completed app</span></span>

<span data-ttu-id="80782-146">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="80782-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="80782-147">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="80782-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="80782-148">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="80782-148">The starter app</span></span>

<span data-ttu-id="80782-149">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="80782-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="80782-150">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="80782-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="80782-151">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="80782-151">Secure user data</span></span>

<span data-ttu-id="80782-152">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="80782-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="80782-153">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="80782-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="80782-154">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="80782-154">Tie the contact data to the user</span></span>

<span data-ttu-id="80782-155">Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="80782-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="80782-156">Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:</span><span class="sxs-lookup"><span data-stu-id="80782-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="80782-157">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="80782-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="80782-158">Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="80782-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="80782-159">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="80782-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="80782-160">Rollen Dienste zur Identität hinzufügen</span><span class="sxs-lookup"><span data-stu-id="80782-160">Add Role services to Identity</span></span>

<span data-ttu-id="80782-161">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="80782-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="80782-162">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="80782-162">Require authenticated users</span></span>

<span data-ttu-id="80782-163">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="80782-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="80782-164">Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="80782-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="80782-165">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Pages und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="80782-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="80782-166">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="80782-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="80782-167">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-und datenschutzseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="80782-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="80782-168">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="80782-168">Configure the test account</span></span>

<span data-ttu-id="80782-169">Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager".</span><span class="sxs-lookup"><span data-stu-id="80782-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="80782-170">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="80782-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="80782-171">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="80782-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="80782-172">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="80782-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="80782-173">Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="80782-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="80782-174">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="80782-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="80782-175">Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="80782-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="80782-176">Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="80782-177">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="80782-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="80782-178">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="80782-179">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="80782-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="80782-180">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="80782-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="80782-181">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="80782-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="80782-182">Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="80782-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="80782-183">Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="80782-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="80782-184">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="80782-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="80782-185">Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="80782-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="80782-186">Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="80782-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="80782-187">`Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="80782-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="80782-188">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="80782-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="80782-189">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="80782-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="80782-190">`ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.</span><span class="sxs-lookup"><span data-stu-id="80782-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="80782-191">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="80782-191">Create a manager authorization handler</span></span>

<span data-ttu-id="80782-192">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="80782-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="80782-193">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="80782-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="80782-194">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="80782-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="80782-195">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="80782-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="80782-196">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="80782-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="80782-197">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="80782-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="80782-198">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="80782-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="80782-199">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="80782-199">Register the authorization handlers</span></span>

<span data-ttu-id="80782-200">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="80782-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="80782-201">Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="80782-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="80782-202">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="80782-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="80782-203">Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:</span><span class="sxs-lookup"><span data-stu-id="80782-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="80782-204">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="80782-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="80782-205">Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.</span><span class="sxs-lookup"><span data-stu-id="80782-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="80782-206">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="80782-206">Support authorization</span></span>

<span data-ttu-id="80782-207">In diesem Abschnitt aktualisieren Sie die Razor Pages und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="80782-208">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="80782-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="80782-209">Überprüfen Sie die `ContactOperations`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="80782-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="80782-210">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="80782-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="80782-211">Erstellen einer Basisklasse für die Kontakte Razor Pages</span><span class="sxs-lookup"><span data-stu-id="80782-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="80782-212">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor Pages verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="80782-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="80782-213">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="80782-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="80782-214">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="80782-214">The preceding code:</span></span>

* <span data-ttu-id="80782-215">Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="80782-216">Fügt den Identitäts `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="80782-217">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="80782-218">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="80782-218">Update the CreateModel</span></span>

<span data-ttu-id="80782-219">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="80782-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="80782-220">Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:</span><span class="sxs-lookup"><span data-stu-id="80782-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="80782-221">Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="80782-222">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="80782-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="80782-223">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="80782-223">Update the IndexModel</span></span>

<span data-ttu-id="80782-224">Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="80782-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="80782-225">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="80782-225">Update the EditModel</span></span>

<span data-ttu-id="80782-226">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="80782-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="80782-227">Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus.</span><span class="sxs-lookup"><span data-stu-id="80782-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="80782-228">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="80782-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="80782-229">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="80782-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="80782-230">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="80782-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="80782-231">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="80782-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="80782-232">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="80782-232">Update the DeleteModel</span></span>

<span data-ttu-id="80782-233">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="80782-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="80782-234">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="80782-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="80782-235">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="80782-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="80782-236">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="80782-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="80782-237">Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="80782-238">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="80782-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="80782-239">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="80782-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="80782-240">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="80782-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="80782-241">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="80782-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="80782-242">Die Razor Page oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="80782-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="80782-243">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="80782-243">Update Details</span></span>

<span data-ttu-id="80782-244">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="80782-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="80782-245">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="80782-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="80782-246">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="80782-246">Add or remove a user to a role</span></span>

<span data-ttu-id="80782-247">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="80782-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="80782-248">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="80782-248">Removing privileges from a user.</span></span> <span data-ttu-id="80782-249">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="80782-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="80782-250">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="80782-250">Adding privileges to a user.</span></span>

## <a name="differences-between-challenge-vs-forbid"></a><span data-ttu-id="80782-251">Unterschiede zwischen Challenge vs verboten</span><span class="sxs-lookup"><span data-stu-id="80782-251">Differences between Challenge vs Forbid</span></span>

<span data-ttu-id="80782-252">Diese APP legt die Standard Richtlinie so fest, dass [Authentifizierte Benutzer erforderlich](#require-authenticated-users)sind.</span><span class="sxs-lookup"><span data-stu-id="80782-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="80782-253">Der folgende Code ermöglicht anonyme Benutzer.</span><span class="sxs-lookup"><span data-stu-id="80782-253">The following code allows anonymous users.</span></span> <span data-ttu-id="80782-254">Anonyme Benutzer können die Unterschiede zwischen Challenge vs verboten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="80782-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="80782-255">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="80782-255">In the preceding code:</span></span>

* <span data-ttu-id="80782-256">Wenn der Benutzer **nicht** authentifiziert ist, wird ein `ChallengeResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="80782-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="80782-257">Wenn eine `ChallengeResult` zurückgegeben wird, wird der Benutzer zur Anmeldeseite umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="80782-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="80782-258">Wenn der Benutzer authentifiziert, aber nicht autorisiert ist, wird ein `ForbidResult` zurückgegeben.</span><span class="sxs-lookup"><span data-stu-id="80782-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="80782-259">Wenn eine `ForbidResult` zurückgegeben wird, wird der Benutzer zur Seite "Zugriff verweigert" umgeleitet.</span><span class="sxs-lookup"><span data-stu-id="80782-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="80782-260">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="80782-260">Test the completed app</span></span>

<span data-ttu-id="80782-261">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="80782-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="80782-262">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="80782-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="80782-263">`Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="80782-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="80782-264">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="80782-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="80782-265">Wenn die APP Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="80782-265">If the app has contacts:</span></span>

* <span data-ttu-id="80782-266">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="80782-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="80782-267">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="80782-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="80782-268">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="80782-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="80782-269">Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="80782-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="80782-270">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="80782-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="80782-271">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="80782-271">Verify the following operations:</span></span>

* <span data-ttu-id="80782-272">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="80782-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="80782-273">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="80782-274">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="80782-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="80782-275">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="80782-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="80782-276">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="80782-277">Benutzer</span><span class="sxs-lookup"><span data-stu-id="80782-277">User</span></span>                | <span data-ttu-id="80782-278">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="80782-278">Seeded by the app</span></span> | <span data-ttu-id="80782-279">Optionen</span><span class="sxs-lookup"><span data-stu-id="80782-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="80782-280">Nein</span><span class="sxs-lookup"><span data-stu-id="80782-280">No</span></span>                | <span data-ttu-id="80782-281">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="80782-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="80782-282">Ja</span><span class="sxs-lookup"><span data-stu-id="80782-282">Yes</span></span>               | <span data-ttu-id="80782-283">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="80782-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="80782-284">Ja</span><span class="sxs-lookup"><span data-stu-id="80782-284">Yes</span></span>               | <span data-ttu-id="80782-285">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="80782-286">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="80782-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="80782-287">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="80782-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="80782-288">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="80782-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="80782-289">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="80782-289">Create the starter app</span></span>

* <span data-ttu-id="80782-290">Erstellen Sie eine Razor Pages-App mit dem Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="80782-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="80782-291">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="80782-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="80782-292">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="80782-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="80782-293">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="80782-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="80782-294">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="80782-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="80782-295">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="80782-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="80782-296">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="80782-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="80782-297">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="80782-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="80782-298">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="80782-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="80782-299">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="80782-300">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="80782-300">Seed the database</span></span>

<span data-ttu-id="80782-301">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="80782-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="80782-302">`SeedData.Initialize` aus `Main`abrufen:</span><span class="sxs-lookup"><span data-stu-id="80782-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="80782-303">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="80782-303">Test that the app seeded the database.</span></span> <span data-ttu-id="80782-304">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="80782-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="80782-305">In diesem Tutorial wird gezeigt, wie Sie eine ASP.net Core-Web-App mit Benutzerdaten erstellen, die durch Autorisierung geschützt</span><span class="sxs-lookup"><span data-stu-id="80782-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="80782-306">Es wird eine Liste von Kontakten angezeigt, die von authentifizierten (registrierten) Benutzern erstellt wurden.</span><span class="sxs-lookup"><span data-stu-id="80782-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="80782-307">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="80782-307">There are three security groups:</span></span>

* <span data-ttu-id="80782-308">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="80782-309">**Manager** können Kontaktdaten genehmigen oder ablehnen.</span><span class="sxs-lookup"><span data-stu-id="80782-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="80782-310">Nur genehmigte Kontakte sind für Benutzer sichtbar.</span><span class="sxs-lookup"><span data-stu-id="80782-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="80782-311">**Administratoren** können beliebige Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="80782-312">In der folgenden Abbildung ist der Benutzer Rick (`rick@example.com`) angemeldet.</span><span class="sxs-lookup"><span data-stu-id="80782-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="80782-313">Rick kann nur genehmigte Kontakte anzeigen und/**Lösch** Vorgang **Bearbeiten**/neue Verknüpfungen für seine Kontakte **Erstellen** .</span><span class="sxs-lookup"><span data-stu-id="80782-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="80782-314">Nur der letzte von Rick erstellte Datensatz zeigt **Bearbeitungs** -und **Lösch** Links an.</span><span class="sxs-lookup"><span data-stu-id="80782-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="80782-315">Andere Benutzer sehen den letzten Datensatz erst, wenn ein Manager oder Administrator den Status in "genehmigt" ändert.</span><span class="sxs-lookup"><span data-stu-id="80782-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Anmeldung mit Rick](secure-data/_static/rick.png)

<span data-ttu-id="80782-317">In der folgenden Abbildung ist `manager@contoso.com` in und in der Rolle des Managers angemeldet:</span><span class="sxs-lookup"><span data-stu-id="80782-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot, der anzeigt, manager@contoso.com angemeldet ist](secure-data/_static/manager1.png)

<span data-ttu-id="80782-319">Die folgende Abbildung zeigt die Ansicht "Manager-Details" eines Kontakts:</span><span class="sxs-lookup"><span data-stu-id="80782-319">The following image shows the managers details view of a contact:</span></span>

![Manager-Ansicht eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="80782-321">Die Schaltflächen **genehmigen** und **ablehnen** werden nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="80782-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="80782-322">In der folgenden Abbildung ist `admin@contoso.com` angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="80782-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot, der anzeigt, admin@contoso.com angemeldet ist](secure-data/_static/admin.png)

<span data-ttu-id="80782-324">Der Administrator verfügt über alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="80782-324">The administrator has all privileges.</span></span> <span data-ttu-id="80782-325">Sie kann beliebige Kontakte lesen, bearbeiten/löschen und den Status von Kontakten ändern.</span><span class="sxs-lookup"><span data-stu-id="80782-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="80782-326">Die APP wurde durch [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) der folgenden `Contact` Modell erstellt:</span><span class="sxs-lookup"><span data-stu-id="80782-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="80782-327">Das Beispiel enthält die folgenden Autorisierungs Handler:</span><span class="sxs-lookup"><span data-stu-id="80782-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="80782-328">`ContactIsOwnerAuthorizationHandler`: stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="80782-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="80782-329">`ContactManagerAuthorizationHandler`: ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="80782-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="80782-330">`ContactAdministratorsAuthorizationHandler`: ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="80782-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="80782-331">Erforderliche Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="80782-331">Prerequisites</span></span>

<span data-ttu-id="80782-332">Dieses Tutorial ist erweitert.</span><span class="sxs-lookup"><span data-stu-id="80782-332">This tutorial is advanced.</span></span> <span data-ttu-id="80782-333">Sie sollten sich mit folgenden Aktionen vertraut machen:</span><span class="sxs-lookup"><span data-stu-id="80782-333">You should be familiar with:</span></span>

* [<span data-ttu-id="80782-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="80782-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="80782-335">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="80782-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="80782-336">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="80782-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="80782-337">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="80782-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="80782-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="80782-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="80782-339">Starter und abgeschlossene App</span><span class="sxs-lookup"><span data-stu-id="80782-339">The starter and completed app</span></span>

<span data-ttu-id="80782-340">[Laden Sie](xref:index#how-to-download-a-sample) die [fertige](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) APP herunter.</span><span class="sxs-lookup"><span data-stu-id="80782-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="80782-341">[Testen](#test-the-completed-app) Sie die fertige APP, damit Sie sich mit den Sicherheitsfunktionen vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="80782-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="80782-342">Die Starter-App</span><span class="sxs-lookup"><span data-stu-id="80782-342">The starter app</span></span>

<span data-ttu-id="80782-343">[Laden Sie](xref:index#how-to-download-a-sample) die [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) -APP herunter.</span><span class="sxs-lookup"><span data-stu-id="80782-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="80782-344">Führen Sie die APP aus, tippen Sie auf den Link **ContactManager** , und überprüfen Sie, ob Sie einen Kontakt erstellen, bearbeiten und löschen können.</span><span class="sxs-lookup"><span data-stu-id="80782-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="80782-345">Sichern von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="80782-345">Secure user data</span></span>

<span data-ttu-id="80782-346">In den folgenden Abschnitten werden die wichtigsten Schritte zum Erstellen der APP für sichere Benutzerdaten beschrieben.</span><span class="sxs-lookup"><span data-stu-id="80782-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="80782-347">Möglicherweise ist es hilfreich, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="80782-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="80782-348">Verknüpfen der Kontaktdaten mit dem Benutzer</span><span class="sxs-lookup"><span data-stu-id="80782-348">Tie the contact data to the user</span></span>

<span data-ttu-id="80782-349">Verwenden Sie die Benutzer-ID ASP.net [Identity](xref:security/authentication/identity) , um sicherzustellen, dass Benutzer Ihre Daten, aber keine anderen Benutzerdaten bearbeiten können.</span><span class="sxs-lookup"><span data-stu-id="80782-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="80782-350">Fügen Sie dem `Contact` Modell `OwnerID` und `ContactStatus` hinzu:</span><span class="sxs-lookup"><span data-stu-id="80782-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="80782-351">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` Tabelle in der [Identitäts](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="80782-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="80782-352">Das `Status`-Feld bestimmt, ob ein Kontakt von allgemeinen Benutzern angezeigt werden kann.</span><span class="sxs-lookup"><span data-stu-id="80782-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="80782-353">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="80782-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="80782-354">Rollen Dienste zur Identität hinzufügen</span><span class="sxs-lookup"><span data-stu-id="80782-354">Add Role services to Identity</span></span>

<span data-ttu-id="80782-355">Anfügen von [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) zum Hinzufügen von Rollen Diensten:</span><span class="sxs-lookup"><span data-stu-id="80782-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="80782-356">Authentifizierte Benutzer erforderlich</span><span class="sxs-lookup"><span data-stu-id="80782-356">Require authenticated users</span></span>

<span data-ttu-id="80782-357">Legen Sie die Standard Authentifizierungs Richtlinie so fest, dass Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="80782-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="80782-358">Sie können die Authentifizierung auf der Razor Page-, Controller-oder Aktionsmethoden Ebene mit dem `[AllowAnonymous]`-Attribut ablehnen.</span><span class="sxs-lookup"><span data-stu-id="80782-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="80782-359">Wenn Sie die Standard Authentifizierungs Richtlinie so festlegen, dass Benutzer authentifiziert werden müssen, werden neu hinzugefügte Razor Pages und Controller geschützt.</span><span class="sxs-lookup"><span data-stu-id="80782-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="80782-360">Die standardmäßig erforderliche Authentifizierung ist sicherer als die Verwendung neuer Controller und Razor Pages, um das `[Authorize]` Attribut einzubeziehen.</span><span class="sxs-lookup"><span data-stu-id="80782-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="80782-361">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-, Info-und Kontaktseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="80782-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="80782-362">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="80782-362">Configure the test account</span></span>

<span data-ttu-id="80782-363">Die `SeedData`-Klasse erstellt zwei Konten: "Administrator" und "Manager".</span><span class="sxs-lookup"><span data-stu-id="80782-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="80782-364">Verwenden Sie das [Tool Secret Manager](xref:security/app-secrets) , um ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="80782-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="80782-365">Legen Sie das Kennwort aus dem Projektverzeichnis (dem Verzeichnis mit *Program.cs*) fest:</span><span class="sxs-lookup"><span data-stu-id="80782-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="80782-366">Wenn kein sicheres Kennwort angegeben wird, wird eine Ausnahme ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="80782-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="80782-367">Aktualisieren Sie `Main`, um das Test Kennwort zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="80782-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="80782-368">Erstellen der Testkonten und Aktualisieren der Kontakte</span><span class="sxs-lookup"><span data-stu-id="80782-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="80782-369">Aktualisieren Sie die `Initialize`-Methode in der `SeedData`-Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="80782-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="80782-370">Fügen Sie die Administrator-Benutzer-ID und den `ContactStatus` den Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="80782-371">Machen Sie einen der Kontakte "gesendet" und "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="80782-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="80782-372">Fügen Sie die Benutzer-ID und den Status allen Kontakten hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="80782-373">Es wird nur ein Kontakt angezeigt:</span><span class="sxs-lookup"><span data-stu-id="80782-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="80782-374">Erstellen von Autorisierungs Handlern für Besitzer, Manager und Administratoren</span><span class="sxs-lookup"><span data-stu-id="80782-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="80782-375">Erstellen Sie einen *Autorisierungs* Ordner, und erstellen Sie darin eine `ContactIsOwnerAuthorizationHandler`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="80782-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="80782-376">Der `ContactIsOwnerAuthorizationHandler` überprüft, ob der Benutzer, der für eine Ressource agiert, die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="80782-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="80782-377">Der `ContactIsOwnerAuthorizationHandler` Ruft den [Kontext auf. Erfolgreich](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) , wenn der aktuelle authentifizierte Benutzer der Kontakt Besitzer ist.</span><span class="sxs-lookup"><span data-stu-id="80782-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="80782-378">Autorisierungs Handler im allgemeinen:</span><span class="sxs-lookup"><span data-stu-id="80782-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="80782-379">Gibt `context.Succeed` zurück, wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="80782-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="80782-380">Gibt `Task.CompletedTask` zurück, wenn die Anforderungen nicht erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="80782-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="80782-381">`Task.CompletedTask` ist nicht erfolgreich oder Fehler&mdash;es zulässt, dass andere Autorisierungs Handler ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="80782-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="80782-382">Wenn Sie einen expliziten Fehler verursachen müssen, geben Sie [context zurück. Schlägt fehl](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="80782-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="80782-383">Mit der App können Besitzer von Kontakten Ihre eigenen Daten bearbeiten/löschen/erstellen.</span><span class="sxs-lookup"><span data-stu-id="80782-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="80782-384">`ContactIsOwnerAuthorizationHandler` muss den im Anforderungs Parameter übergebenen Vorgang nicht überprüfen.</span><span class="sxs-lookup"><span data-stu-id="80782-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="80782-385">Erstellen eines Manager-Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="80782-385">Create a manager authorization handler</span></span>

<span data-ttu-id="80782-386">Erstellen Sie eine `ContactManagerAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="80782-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="80782-387">Der `ContactManagerAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Manager ist.</span><span class="sxs-lookup"><span data-stu-id="80782-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="80782-388">Nur Manager können Inhalts Änderungen genehmigen oder ablehnen (neu oder geändert).</span><span class="sxs-lookup"><span data-stu-id="80782-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="80782-389">Erstellen eines Administrator Autorisierungs Handlers</span><span class="sxs-lookup"><span data-stu-id="80782-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="80782-390">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` Klasse im *Autorisierungs* Ordner.</span><span class="sxs-lookup"><span data-stu-id="80782-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="80782-391">Der `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, der für die Ressource agiert, ein Administrator ist.</span><span class="sxs-lookup"><span data-stu-id="80782-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="80782-392">Der Administrator kann alle Vorgänge ausführen.</span><span class="sxs-lookup"><span data-stu-id="80782-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="80782-393">Registrieren von Autorisierungs Handlern</span><span class="sxs-lookup"><span data-stu-id="80782-393">Register the authorization handlers</span></span>

<span data-ttu-id="80782-394">Dienste, die Entity Framework Core verwenden, müssen für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mithilfe von [addscoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)registriert werden.</span><span class="sxs-lookup"><span data-stu-id="80782-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="80782-395">Der `ContactIsOwnerAuthorizationHandler` verwendet ASP.net Core [Identität](xref:security/authentication/identity), die auf Entity Framework Core basiert.</span><span class="sxs-lookup"><span data-stu-id="80782-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="80782-396">Registrieren Sie die Handler bei der Dienst Sammlung, damit Sie für die `ContactsController` über die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection)verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="80782-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="80782-397">Fügen Sie den folgenden Code am Ende der `ConfigureServices`hinzu:</span><span class="sxs-lookup"><span data-stu-id="80782-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="80782-398">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` werden als Singletons hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="80782-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="80782-399">Sie sind Singletons, da Sie EF nicht verwenden und alle benötigten Informationen im `Context`-Parameter der `HandleRequirementAsync`-Methode liegen.</span><span class="sxs-lookup"><span data-stu-id="80782-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="80782-400">Autorisierung unterstützen</span><span class="sxs-lookup"><span data-stu-id="80782-400">Support authorization</span></span>

<span data-ttu-id="80782-401">In diesem Abschnitt aktualisieren Sie die Razor Pages und fügen eine Vorgangs Anforderungs Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="80782-402">Überprüfen der "Contact Operations Requirements"-Klasse</span><span class="sxs-lookup"><span data-stu-id="80782-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="80782-403">Überprüfen Sie die `ContactOperations`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="80782-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="80782-404">Diese Klasse enthält die Anforderungen, die von der App unterstützt werden:</span><span class="sxs-lookup"><span data-stu-id="80782-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="80782-405">Erstellen einer Basisklasse für die Kontakte Razor Pages</span><span class="sxs-lookup"><span data-stu-id="80782-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="80782-406">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor Pages verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="80782-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="80782-407">Die Basisklasse fügt den Initialisierungs Code an einem Speicherort ein:</span><span class="sxs-lookup"><span data-stu-id="80782-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="80782-408">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="80782-408">The preceding code:</span></span>

* <span data-ttu-id="80782-409">Fügt dem Zugriff auf die Autorisierungs Handler den `IAuthorizationService` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="80782-410">Fügt den Identitäts `UserManager` Dienst hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="80782-411">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="80782-412">Aktualisieren von "up Model"</span><span class="sxs-lookup"><span data-stu-id="80782-412">Update the CreateModel</span></span>

<span data-ttu-id="80782-413">Aktualisieren Sie den Konstruktor für das Erstellen von Seiten Modellen, um die `DI_BasePageModel` Basisklasse zu verwenden:</span><span class="sxs-lookup"><span data-stu-id="80782-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="80782-414">Aktualisieren Sie die `CreateModel.OnPostAsync`-Methode wie folgt:</span><span class="sxs-lookup"><span data-stu-id="80782-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="80782-415">Fügen Sie die Benutzer-ID dem `Contact` Modell hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="80782-416">Rufen Sie den Autorisierungs Handler auf, um zu überprüfen, ob der Benutzer berechtigt ist, Kontakte</span><span class="sxs-lookup"><span data-stu-id="80782-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="80782-417">Aktualisieren von indexmodel</span><span class="sxs-lookup"><span data-stu-id="80782-417">Update the IndexModel</span></span>

<span data-ttu-id="80782-418">Aktualisieren Sie die `OnGetAsync`-Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="80782-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="80782-419">Aktualisieren von "editmodel"</span><span class="sxs-lookup"><span data-stu-id="80782-419">Update the EditModel</span></span>

<span data-ttu-id="80782-420">Fügen Sie einen Autorisierungs Handler hinzu, um sicherzustellen, dass der Benutzer den Kontakt besitzt</span><span class="sxs-lookup"><span data-stu-id="80782-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="80782-421">Da die Ressourcen Autorisierung überprüft wird, reicht das `[Authorize]` Attribut nicht aus.</span><span class="sxs-lookup"><span data-stu-id="80782-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="80782-422">Die APP hat keinen Zugriff auf die Ressource, wenn Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="80782-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="80782-423">Die ressourcenbasierte Autorisierung muss zwingend erforderlich sein.</span><span class="sxs-lookup"><span data-stu-id="80782-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="80782-424">Überprüfungen müssen ausgeführt werden, sobald die APP auf die Ressource zugreifen kann, indem Sie Sie entweder im Seiten Modell laden oder innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="80782-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="80782-425">Sie greifen häufig auf die Ressource zu, indem Sie den Ressourcen Schlüssel übergeben.</span><span class="sxs-lookup"><span data-stu-id="80782-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="80782-426">Aktualisieren von deletemodel</span><span class="sxs-lookup"><span data-stu-id="80782-426">Update the DeleteModel</span></span>

<span data-ttu-id="80782-427">Aktualisieren Sie das Seiten Modell löschen, um mithilfe des Autorisierungs Handlers zu überprüfen, ob der Benutzer über die DELETE-Berechtigung für den Kontakt</span><span class="sxs-lookup"><span data-stu-id="80782-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="80782-428">Einfügen des Autorisierungs Dienstanbieter in die Ansichten</span><span class="sxs-lookup"><span data-stu-id="80782-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="80782-429">Zurzeit werden auf der Benutzeroberfläche Bearbeitungs-und Lösch Links für Kontakte angezeigt, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="80782-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="80782-430">Fügen Sie den Autorisierungs Dienst in die Datei *views/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="80782-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="80782-431">Das vorangehende Markup fügt mehrere `using`-Anweisungen hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="80782-432">Aktualisieren Sie die Links " **Bearbeiten** " und " **Löschen** " in " *pages/Contacts/Index. cshtml* ", sodass Sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="80782-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="80782-433">Das Ausblenden von Links von Benutzern, die nicht über die Berechtigung zum Ändern von Daten verfügen, sichert die APP nicht.</span><span class="sxs-lookup"><span data-stu-id="80782-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="80782-434">Durch das Ausblenden von Verknüpfungen wird die APP benutzerfreundlicher, da nur gültige Links angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="80782-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="80782-435">Benutzer können die generierten URLs hacken, um Bearbeitungs-und Löschvorgänge für Daten aufzurufen, die Sie nicht besitzen.</span><span class="sxs-lookup"><span data-stu-id="80782-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="80782-436">Die Razor Page oder der Controller muss Zugriffs Überprüfungen erzwingen, um die Daten zu sichern.</span><span class="sxs-lookup"><span data-stu-id="80782-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="80782-437">Details aktualisieren</span><span class="sxs-lookup"><span data-stu-id="80782-437">Update Details</span></span>

<span data-ttu-id="80782-438">Aktualisieren Sie die Detailansicht, damit Manager Kontakte genehmigen oder ablehnen können:</span><span class="sxs-lookup"><span data-stu-id="80782-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="80782-439">Aktualisieren Sie das Detailseiten Modell:</span><span class="sxs-lookup"><span data-stu-id="80782-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="80782-440">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="80782-440">Add or remove a user to a role</span></span>

<span data-ttu-id="80782-441">Weitere Informationen zu finden Sie in [diesem Thema](https://github.com/aspnet/AspNetCore.Docs/issues/8502) :</span><span class="sxs-lookup"><span data-stu-id="80782-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="80782-442">Entfernen von Berechtigungen für einen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="80782-442">Removing privileges from a user.</span></span> <span data-ttu-id="80782-443">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="80782-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="80782-444">Hinzufügen von Berechtigungen zu einem Benutzer</span><span class="sxs-lookup"><span data-stu-id="80782-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="80782-445">Testen der abgeschlossenen App</span><span class="sxs-lookup"><span data-stu-id="80782-445">Test the completed app</span></span>

<span data-ttu-id="80782-446">Wenn Sie noch kein Kennwort für Benutzerkonten mit Seeding festgelegt haben, verwenden Sie das [Tool Secret Manager](xref:security/app-secrets#secret-manager) , um ein Kennwort festzulegen:</span><span class="sxs-lookup"><span data-stu-id="80782-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="80782-447">Sicheres Kennwort auswählen: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="80782-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="80782-448">`Passw0rd!` erfüllt z. b. die Anforderungen für starke Kenn Wörter.</span><span class="sxs-lookup"><span data-stu-id="80782-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="80782-449">Führen Sie den folgenden Befehl aus dem Ordner des Projekts aus, wobei `<PW>` das Kennwort ist:</span><span class="sxs-lookup"><span data-stu-id="80782-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="80782-450">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="80782-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="80782-451">Starten Sie die APP neu, um die Datenbank zu starten.</span><span class="sxs-lookup"><span data-stu-id="80782-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="80782-452">Eine einfache Möglichkeit zum Testen der abgeschlossenen App besteht darin, drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="80782-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="80782-453">Registrieren Sie in einem Browser einen neuen Benutzer (z. b. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="80782-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="80782-454">Melden Sie sich bei jedem Browser mit einem anderen Benutzer an.</span><span class="sxs-lookup"><span data-stu-id="80782-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="80782-455">Überprüfen Sie die folgenden Vorgänge:</span><span class="sxs-lookup"><span data-stu-id="80782-455">Verify the following operations:</span></span>

* <span data-ttu-id="80782-456">Registrierte Benutzer können alle genehmigten Kontaktdaten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="80782-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="80782-457">Registrierte Benutzer können Ihre eigenen Daten bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="80782-458">Manager können Kontaktdaten genehmigen/ablehnen.</span><span class="sxs-lookup"><span data-stu-id="80782-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="80782-459">Die `Details` Ansicht zeigt die Schaltflächen **genehmigen** und **ablehnen** .</span><span class="sxs-lookup"><span data-stu-id="80782-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="80782-460">Administratoren können alle Daten genehmigen/ablehnen und bearbeiten bzw. löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="80782-461">Benutzer</span><span class="sxs-lookup"><span data-stu-id="80782-461">User</span></span>                | <span data-ttu-id="80782-462">Seeding von der APP</span><span class="sxs-lookup"><span data-stu-id="80782-462">Seeded by the app</span></span> | <span data-ttu-id="80782-463">Optionen</span><span class="sxs-lookup"><span data-stu-id="80782-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="80782-464">Nein</span><span class="sxs-lookup"><span data-stu-id="80782-464">No</span></span>                | <span data-ttu-id="80782-465">Bearbeiten/Löschen Sie die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="80782-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="80782-466">Ja</span><span class="sxs-lookup"><span data-stu-id="80782-466">Yes</span></span>               | <span data-ttu-id="80782-467">Genehmigen/ablehnen und Bearbeiten/Löschen eigener Daten.</span><span class="sxs-lookup"><span data-stu-id="80782-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="80782-468">Ja</span><span class="sxs-lookup"><span data-stu-id="80782-468">Yes</span></span>               | <span data-ttu-id="80782-469">Alle Daten genehmigen/ablehnen und bearbeiten/löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="80782-470">Erstellen Sie einen Kontakt im Browser des Administrators.</span><span class="sxs-lookup"><span data-stu-id="80782-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="80782-471">Kopieren Sie die URL zum Löschen und bearbeiten vom Administrator Kontakt.</span><span class="sxs-lookup"><span data-stu-id="80782-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="80782-472">Fügen Sie diese Links in den Browser des Test Benutzers ein, um zu überprüfen, ob der Test Benutzer diese Vorgänge nicht ausführen kann.</span><span class="sxs-lookup"><span data-stu-id="80782-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="80782-473">Erstellen der Starter-App</span><span class="sxs-lookup"><span data-stu-id="80782-473">Create the starter app</span></span>

* <span data-ttu-id="80782-474">Erstellen Sie eine Razor Pages-App mit dem Namen "ContactManager".</span><span class="sxs-lookup"><span data-stu-id="80782-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="80782-475">Erstellen Sie die APP mit **einzelnen Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="80782-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="80782-476">Nennen Sie Sie "ContactManager", damit der Namespace mit dem Namespace übereinstimmt, der im Beispiel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="80782-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="80782-477">`-uld` gibt localdb anstelle von SQLite an.</span><span class="sxs-lookup"><span data-stu-id="80782-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="80782-478">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="80782-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="80782-479">Gerüst für das `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="80782-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="80782-480">Erstellen Sie zunächst die Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="80782-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="80782-481">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="80782-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="80782-482">Testen Sie die APP, indem Sie einen Kontakt erstellen, bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="80782-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="80782-483">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="80782-483">Seed the database</span></span>

<span data-ttu-id="80782-484">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="80782-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="80782-485">`SeedData.Initialize` aus `Main`abrufen:</span><span class="sxs-lookup"><span data-stu-id="80782-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="80782-486">Testen Sie, ob die APP die Datenbank durchsucht hat.</span><span class="sxs-lookup"><span data-stu-id="80782-486">Test that the app seeded the database.</span></span> <span data-ttu-id="80782-487">Wenn die Contact DB Zeilen enthält, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="80782-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="80782-488">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="80782-488">Additional resources</span></span>

* [<span data-ttu-id="80782-489">Erstellen einer .net Core-und SQL-Datenbank-Web-App in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="80782-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="80782-490">[ASP.net Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="80782-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="80782-491">In dieser Übungseinheit werden die in diesem Tutorial vorgestellten Sicherheitsfeatures ausführlicher erläutert.</span><span class="sxs-lookup"><span data-stu-id="80782-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="80782-492">Benutzerdefinierte, richtlinienbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="80782-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
