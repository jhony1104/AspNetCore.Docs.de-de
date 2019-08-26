---
title: Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind
author: rick-anderson
description: Informationen Sie zum Erstellen einer Razor-Seiten-app mit Benutzerdaten, die durch Autorisierung geschützt sind. Enthält, HTTPS, Authentifizierung und Sicherheit, ASP.NET Core Identity.
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 225d0e3aa51745253d03e614b1c8568b3a6ba731
ms.sourcegitcommit: 983b31449fe398e6e922eb13e9eb6f4287ec91e8
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2019
ms.locfileid: "70017485"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="d479f-104">Erstellen einer ASP.NET Core-app mit Benutzerdaten, die durch Autorisierung geschützt sind</span><span class="sxs-lookup"><span data-stu-id="d479f-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="d479f-105">Von [Rick Anderson](https://twitter.com/RickAndMSFT) und [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="d479f-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="d479f-106">Finden Sie unter [dieses PDF-Dokument](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) für die ASP.NET Core MVC-Version.</span><span class="sxs-lookup"><span data-stu-id="d479f-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="d479f-107">Die ASP.NET Core 1.1-Version dieses Tutorials wird [dies](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="d479f-108">Die ASP.NET Core-Beispiel befindet sich in 1.1 die [Beispiele](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span><span class="sxs-lookup"><span data-stu-id="d479f-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="d479f-109">Finden Sie unter [dieses PDF-Dokument](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="d479f-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d479f-110">In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="d479f-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="d479f-111">Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben.</span><span class="sxs-lookup"><span data-stu-id="d479f-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="d479f-112">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="d479f-112">There are three security groups:</span></span>

* <span data-ttu-id="d479f-113">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und bearbeiten und löschen können ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="d479f-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="d479f-114">**Manager** genehmigen oder ablehnen von Kontaktdaten können.</span><span class="sxs-lookup"><span data-stu-id="d479f-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="d479f-115">Nur genehmigte Kontakte für Benutzer sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="d479f-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="d479f-116">**Administratoren** können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="d479f-117">Die Bilder in diesem Dokument entsprechen nicht exakt den neuesten Vorlagen.</span><span class="sxs-lookup"><span data-stu-id="d479f-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="d479f-118">In der folgenden Abbildung wird der Benutzer Rick (`rick@example.com`) angemeldet ist.</span><span class="sxs-lookup"><span data-stu-id="d479f-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="d479f-119">Rick kann nur genehmigte Kontakte anzeigen und **bearbeiten**/**löschen**/**neu erstellen** Links, um seine Kontakte.</span><span class="sxs-lookup"><span data-stu-id="d479f-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="d479f-120">Nur der letzte Datensatz erstellt, von Rick, zeigt **bearbeiten** und **löschen** Links.</span><span class="sxs-lookup"><span data-stu-id="d479f-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="d479f-121">Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.</span><span class="sxs-lookup"><span data-stu-id="d479f-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

<span data-ttu-id="d479f-123">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="d479f-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot mit manager@contoso.com angemeldet](secure-data/_static/manager1.png)

<span data-ttu-id="d479f-125">Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:</span><span class="sxs-lookup"><span data-stu-id="d479f-125">The following image shows the managers details view of a contact:</span></span>

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="d479f-127">Die **genehmigen** und **ablehnen** Schaltflächen sind nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d479f-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="d479f-128">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="d479f-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot mit admin@contoso.com angemeldet](secure-data/_static/admin.png)

<span data-ttu-id="d479f-130">Der Administrator muss alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="d479f-130">The administrator has all privileges.</span></span> <span data-ttu-id="d479f-131">Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="d479f-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="d479f-132">Die app wurde erstellt, indem [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) Folgendes `Contact` Modell:</span><span class="sxs-lookup"><span data-stu-id="d479f-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="d479f-133">Das Beispiel enthält die folgenden Handler für die Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="d479f-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="d479f-134">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="d479f-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="d479f-135">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="d479f-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="d479f-136">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="d479f-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d479f-137">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="d479f-137">Prerequisites</span></span>

<span data-ttu-id="d479f-138">In diesem Tutorial wird verschoben.</span><span class="sxs-lookup"><span data-stu-id="d479f-138">This tutorial is advanced.</span></span> <span data-ttu-id="d479f-139">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="d479f-139">You should be familiar with:</span></span>

* [<span data-ttu-id="d479f-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d479f-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="d479f-141">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d479f-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="d479f-142">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="d479f-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d479f-143">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="d479f-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="d479f-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d479f-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="d479f-145">Die Starter und die fertige app</span><span class="sxs-lookup"><span data-stu-id="d479f-145">The starter and completed app</span></span>

<span data-ttu-id="d479f-146">[Herunterladen](xref:index#how-to-download-a-sample) der [abgeschlossen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span><span class="sxs-lookup"><span data-stu-id="d479f-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="d479f-147">[Test](#test-the-completed-app) die fertige app, damit Sie mit den Security-Features vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="d479f-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="d479f-148">Das Starter-app</span><span class="sxs-lookup"><span data-stu-id="d479f-148">The starter app</span></span>

<span data-ttu-id="d479f-149">[Herunterladen](xref:index#how-to-download-a-sample) der [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span><span class="sxs-lookup"><span data-stu-id="d479f-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="d479f-150">Führen Sie die app, tippen Sie auf die **ContactManager** verknüpfen, und stellen Sie sicher, Sie erstellen, bearbeiten und Löschen eines Kontakts.</span><span class="sxs-lookup"><span data-stu-id="d479f-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="d479f-151">Schützen von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="d479f-151">Secure user data</span></span>

<span data-ttu-id="d479f-152">Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app.</span><span class="sxs-lookup"><span data-stu-id="d479f-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="d479f-153">Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="d479f-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="d479f-154">Verknüpfen Sie die Kontaktdaten für den Benutzer</span><span class="sxs-lookup"><span data-stu-id="d479f-154">Tie the contact data to the user</span></span>

<span data-ttu-id="d479f-155">Verwenden Sie die ASP.NET [Identität](xref:security/authentication/identity) Benutzer-ID, um sicherzustellen, dass Benutzer ihre Daten, aber nicht für andere Benutzerdaten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="d479f-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="d479f-156">Hinzufügen `OwnerID` und `ContactStatus` auf die `Contact` Modell:</span><span class="sxs-lookup"><span data-stu-id="d479f-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="d479f-157">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` -Tabelle in der [Identität](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d479f-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="d479f-158">Die `Status` Feld bestimmt, ob ein Kontakt allgemeine Benutzer angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d479f-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="d479f-159">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="d479f-159">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="d479f-160">Fügen Sie Rollendienste hinzu, die Identität</span><span class="sxs-lookup"><span data-stu-id="d479f-160">Add Role services to Identity</span></span>

<span data-ttu-id="d479f-161">Fügen Sie [Rollen](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) Rollendienste hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="d479f-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="d479f-162">Authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="d479f-162">Require authenticated users</span></span>

<span data-ttu-id="d479f-163">Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="d479f-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="d479f-164">Sie können die Authentifizierung auf der Ebene der Razor Page, Controller und Aktion mit Deaktivieren der `[AllowAnonymous]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="d479f-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="d479f-165">Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="d479f-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="d479f-166">Mit der Authentifizierung erforderlich, die in der Standardeinstellung sicherer ist als die auf neue Domänencontroller und Razor-Seiten enthalten die `[Authorize]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="d479f-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="d479f-167">Fügen Sie die [Zuordnung](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) zu den Index-und datenschutzseiten hinzu, damit anonyme Benutzer vor der Registrierung Informationen über die Website erhalten können.</span><span class="sxs-lookup"><span data-stu-id="d479f-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="d479f-168">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="d479f-168">Configure the test account</span></span>

<span data-ttu-id="d479f-169">Die `SeedData` Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="d479f-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="d479f-170">Verwenden der [Secret Manager-Tool](xref:security/app-secrets) , ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d479f-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="d479f-171">Legen Sie das Kennwort aus dem Projektverzeichnis (das Verzeichnis mit *"Program.cs"* ):</span><span class="sxs-lookup"><span data-stu-id="d479f-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="d479f-172">Wenn Sie ein sicheres Kennwort nicht angegeben ist, eine Ausnahme wird ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d479f-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="d479f-173">Update `Main` Testkennwort verwenden:</span><span class="sxs-lookup"><span data-stu-id="d479f-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="d479f-174">Erstellen Sie die Testkonten und aktualisieren Kontakte</span><span class="sxs-lookup"><span data-stu-id="d479f-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="d479f-175">Update der `Initialize` -Methode in der die `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="d479f-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="d479f-176">Fügen Sie die Administrator-Benutzer-ID und `ContactStatus` für die Contacts.</span><span class="sxs-lookup"><span data-stu-id="d479f-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="d479f-177">Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="d479f-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="d479f-178">Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu.</span><span class="sxs-lookup"><span data-stu-id="d479f-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="d479f-179">Nur ein Kontakt wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d479f-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="d479f-180">Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen</span><span class="sxs-lookup"><span data-stu-id="d479f-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="d479f-181">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` -Klasse in der *Autorisierung* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d479f-182">Die `ContactIsOwnerAuthorizationHandler` stellt sicher, dass der Benutzer, die auf eine Ressource, das die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="d479f-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="d479f-183">Die `ContactIsOwnerAuthorizationHandler` Aufrufe [Kontext. Erfolgreiche](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) ist der aktuelle authentifizierte Benutzer der Besitzer des Kontakts.</span><span class="sxs-lookup"><span data-stu-id="d479f-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="d479f-184">Autorisierung Handler in der Regel:</span><span class="sxs-lookup"><span data-stu-id="d479f-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="d479f-185">Zurückgeben `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="d479f-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="d479f-186">Zurückgeben `Task.CompletedTask` Wenn sind nicht erfüllt.</span><span class="sxs-lookup"><span data-stu-id="d479f-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="d479f-187">`Task.CompletedTask`ist nicht erfolgreich oder Fehler&mdash;Haft, sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="d479f-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="d479f-188">Wenn Sie explizit ausführen müssen, zurückgeben [Kontext. Fehler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="d479f-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="d479f-189">Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="d479f-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="d479f-190">`ContactIsOwnerAuthorizationHandler` Überprüfen Sie den Vorgang, der im Parameter "Anforderung" übergeben muss nicht.</span><span class="sxs-lookup"><span data-stu-id="d479f-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="d479f-191">Erstellen Sie einen Ereignishandler der Manager-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="d479f-191">Create a manager authorization handler</span></span>

<span data-ttu-id="d479f-192">Erstellen Sie eine `ContactManagerAuthorizationHandler` -Klasse in der *Autorisierung* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d479f-193">Die `ContactManagerAuthorizationHandler` überprüft, ob sich der Benutzer, die auf die Ressource eines Managers.</span><span class="sxs-lookup"><span data-stu-id="d479f-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="d479f-194">Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).</span><span class="sxs-lookup"><span data-stu-id="d479f-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="d479f-195">Erstellen Sie ein Administrator autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="d479f-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="d479f-196">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` -Klasse in der *Autorisierung* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d479f-197">Die `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, die auf die Ressource ist ein Administrator.</span><span class="sxs-lookup"><span data-stu-id="d479f-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="d479f-198">Administrator möglich, alle Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="d479f-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="d479f-199">Registrieren Sie die Autorisierung-Handler</span><span class="sxs-lookup"><span data-stu-id="d479f-199">Register the authorization handlers</span></span>

<span data-ttu-id="d479f-200">Dienste, die mit Entity Framework Core müssen registriert werden, für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mit [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="d479f-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="d479f-201">Die `ContactIsOwnerAuthorizationHandler` verwendet ASP.NET Core [Identität](xref:security/authentication/identity), die basiert auf Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d479f-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="d479f-202">Registrieren Sie die Handler mit der Sammlung von Diensten, damit sie verfügbar sind die `ContactsController` über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d479f-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d479f-203">Fügen Sie den folgenden Code am Ende der `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d479f-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="d479f-204">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` als Singletons hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d479f-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="d479f-205">Sie Singletons sind, da sie nicht die EF verwenden und alle erforderlichen Informationen in den `Context` Parameter, der die `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="d479f-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="d479f-206">Unterstützung für Autorisierung</span><span class="sxs-lookup"><span data-stu-id="d479f-206">Support authorization</span></span>

<span data-ttu-id="d479f-207">In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d479f-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="d479f-208">Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse</span><span class="sxs-lookup"><span data-stu-id="d479f-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="d479f-209">Überprüfen Sie die `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="d479f-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="d479f-210">Diese Klasse enthält die Anforderungen der app unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d479f-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="d479f-211">Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="d479f-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="d479f-212">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="d479f-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="d479f-213">Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:</span><span class="sxs-lookup"><span data-stu-id="d479f-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="d479f-214">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d479f-214">The preceding code:</span></span>

* <span data-ttu-id="d479f-215">Fügt der `IAuthorizationService` Dienst den Zugriff auf die Handler für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="d479f-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="d479f-216">Fügt die Identität `UserManager` Service.</span><span class="sxs-lookup"><span data-stu-id="d479f-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="d479f-217">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d479f-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="d479f-218">Aktualisieren der CreateModel</span><span class="sxs-lookup"><span data-stu-id="d479f-218">Update the CreateModel</span></span>

<span data-ttu-id="d479f-219">Aktualisieren den erstellen-Page-Modell-Konstruktor verwendet die `DI_BasePageModel` Basisklasse:</span><span class="sxs-lookup"><span data-stu-id="d479f-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="d479f-220">Update der `CreateModel.OnPostAsync` Methode, um:</span><span class="sxs-lookup"><span data-stu-id="d479f-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="d479f-221">Hinzufügen der Benutzer-ID an den `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="d479f-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="d479f-222">Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.</span><span class="sxs-lookup"><span data-stu-id="d479f-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="d479f-223">Aktualisieren Sie die IndexModel</span><span class="sxs-lookup"><span data-stu-id="d479f-223">Update the IndexModel</span></span>

<span data-ttu-id="d479f-224">Update der `OnGetAsync` Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="d479f-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="d479f-225">Aktualisieren Sie die EditModel</span><span class="sxs-lookup"><span data-stu-id="d479f-225">Update the EditModel</span></span>

<span data-ttu-id="d479f-226">Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu.</span><span class="sxs-lookup"><span data-stu-id="d479f-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="d479f-227">Da die Ressource Autorisierung überprüft wird, die `[Authorize]` -Attribut ist nicht ausreichend.</span><span class="sxs-lookup"><span data-stu-id="d479f-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="d479f-228">Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="d479f-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="d479f-229">Ressourcenbasierte Autorisierung muss zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d479f-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="d479f-230">Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="d479f-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="d479f-231">Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d479f-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="d479f-232">Aktualisieren Sie die DeleteModel</span><span class="sxs-lookup"><span data-stu-id="d479f-232">Update the DeleteModel</span></span>

<span data-ttu-id="d479f-233">Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.</span><span class="sxs-lookup"><span data-stu-id="d479f-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="d479f-234">Fügen Sie dem Prüfdienst in den Ansichten</span><span class="sxs-lookup"><span data-stu-id="d479f-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="d479f-235">Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="d479f-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="d479f-236">Fügen Sie den Autorisierungs Dienst in die Datei *pages/_ViewImports. cshtml* ein, damit er für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="d479f-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="d479f-237">Das vorhergehende Markup fügt mehrere `using` Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="d479f-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="d479f-238">Update der **bearbeiten** und **löschen** verknüpft *Pages/Contacts/Index.cshtml* , sodass sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="d479f-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="d479f-239">Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern.</span><span class="sxs-lookup"><span data-stu-id="d479f-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="d479f-240">Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher.</span><span class="sxs-lookup"><span data-stu-id="d479f-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="d479f-241">Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack.</span><span class="sxs-lookup"><span data-stu-id="d479f-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="d479f-242">Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.</span><span class="sxs-lookup"><span data-stu-id="d479f-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="d479f-243">Updatedetails</span><span class="sxs-lookup"><span data-stu-id="d479f-243">Update Details</span></span>

<span data-ttu-id="d479f-244">Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:</span><span class="sxs-lookup"><span data-stu-id="d479f-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="d479f-245">Aktualisieren Sie das Seitenmodell Details an:</span><span class="sxs-lookup"><span data-stu-id="d479f-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="d479f-246">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="d479f-246">Add or remove a user to a role</span></span>

<span data-ttu-id="d479f-247">Finden Sie unter [dieses Problem](https://github.com/aspnet/AspNetCore.Docs/issues/8502) Informationen auf:</span><span class="sxs-lookup"><span data-stu-id="d479f-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="d479f-248">Entfernen Berechtigungen eines Benutzers ein.</span><span class="sxs-lookup"><span data-stu-id="d479f-248">Removing privileges from a user.</span></span> <span data-ttu-id="d479f-249">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="d479f-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="d479f-250">Hinzufügen von Berechtigungen für einen Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="d479f-250">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="d479f-251">Testen Sie die fertige app</span><span class="sxs-lookup"><span data-stu-id="d479f-251">Test the completed app</span></span>

<span data-ttu-id="d479f-252">Wenn Sie ein Kennwort für die per Seeding hinzugefügten Benutzerkonten bereits festgelegt haben, verwenden Sie die [Secret Manager-Tool](xref:security/app-secrets#secret-manager) zum Festlegen eines Kennworts:</span><span class="sxs-lookup"><span data-stu-id="d479f-252">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="d479f-253">Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="d479f-253">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="d479f-254">Z. B. `Passw0rd!` erfüllt die Anforderungen für sichere Kennwörter.</span><span class="sxs-lookup"><span data-stu-id="d479f-254">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="d479f-255">Führen Sie den folgenden Befehl aus dem Ordner des Projekts, in denen `<PW>` ist das Kennwort:</span><span class="sxs-lookup"><span data-stu-id="d479f-255">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="d479f-256">Wenn die app Kontakte hat:</span><span class="sxs-lookup"><span data-stu-id="d479f-256">If the app has contacts:</span></span>

* <span data-ttu-id="d479f-257">Löschen Sie alle Datensätze in der `Contact` Tabelle.</span><span class="sxs-lookup"><span data-stu-id="d479f-257">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="d479f-258">Starten Sie die app zum Seeding der Datenbank neu.</span><span class="sxs-lookup"><span data-stu-id="d479f-258">Restart the app to seed the database.</span></span>

<span data-ttu-id="d479f-259">Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="d479f-259">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="d479f-260">Registrieren Sie einen neuen Benutzer in einem Browser (z. B. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="d479f-260">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="d479f-261">Melden Sie sich an jeden Browser mit einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="d479f-261">Sign in to each browser with a different user.</span></span> <span data-ttu-id="d479f-262">Überprüfen Sie die folgenden Vorgänge aus:</span><span class="sxs-lookup"><span data-stu-id="d479f-262">Verify the following operations:</span></span>

* <span data-ttu-id="d479f-263">Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="d479f-263">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="d479f-264">Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-264">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="d479f-265">Manager können genehmigen/Kontaktdaten ablehnen.</span><span class="sxs-lookup"><span data-stu-id="d479f-265">Managers can approve/reject contact data.</span></span> <span data-ttu-id="d479f-266">Die `Details` anzeigen zeigt **genehmigen** und **ablehnen** Schaltflächen.</span><span class="sxs-lookup"><span data-stu-id="d479f-266">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="d479f-267">Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-267">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="d479f-268">Benutzer</span><span class="sxs-lookup"><span data-stu-id="d479f-268">User</span></span>                | <span data-ttu-id="d479f-269">Ein Seeding ausgeführt, von der app</span><span class="sxs-lookup"><span data-stu-id="d479f-269">Seeded by the app</span></span> | <span data-ttu-id="d479f-270">Optionen</span><span class="sxs-lookup"><span data-stu-id="d479f-270">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="d479f-271">Nein</span><span class="sxs-lookup"><span data-stu-id="d479f-271">No</span></span>                | <span data-ttu-id="d479f-272">Löschen Sie bearbeiten und die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="d479f-272">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="d479f-273">Ja</span><span class="sxs-lookup"><span data-stu-id="d479f-273">Yes</span></span>               | <span data-ttu-id="d479f-274">Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-274">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="d479f-275">Ja</span><span class="sxs-lookup"><span data-stu-id="d479f-275">Yes</span></span>               | <span data-ttu-id="d479f-276">Genehmigen oder ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-276">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="d479f-277">Erstellen Sie einen Kontakt in der Administrator-Browser.</span><span class="sxs-lookup"><span data-stu-id="d479f-277">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="d479f-278">Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="d479f-278">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="d479f-279">Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.</span><span class="sxs-lookup"><span data-stu-id="d479f-279">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="d479f-280">Die Starter-app erstellen</span><span class="sxs-lookup"><span data-stu-id="d479f-280">Create the starter app</span></span>

* <span data-ttu-id="d479f-281">Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="d479f-281">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="d479f-282">Erstellen Sie die app mit **einzelne Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="d479f-282">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="d479f-283">Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.</span><span class="sxs-lookup"><span data-stu-id="d479f-283">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="d479f-284">`-uld` Gibt an, LocalDB anstelle von SQLite</span><span class="sxs-lookup"><span data-stu-id="d479f-284">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="d479f-285">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="d479f-285">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="d479f-286">Gerüst der `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="d479f-286">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="d479f-287">Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:</span><span class="sxs-lookup"><span data-stu-id="d479f-287">Create initial migration and update the database:</span></span>

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
  ```

<span data-ttu-id="d479f-288">Wenn Sie einen Fehler mit dem `dotnet aspnet-codegenerator razorpage` Befehl haben, finden Sie weitere Informationen in [diesem GitHub-Problem](https://github.com/aspnet/Scaffolding/issues/984).</span><span class="sxs-lookup"><span data-stu-id="d479f-288">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="d479f-289">Aktualisieren Sie den **ContactManager** -Anker in der Datei *pages/Shared/_Layout. cshtml* :</span><span class="sxs-lookup"><span data-stu-id="d479f-289">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="d479f-290">Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts</span><span class="sxs-lookup"><span data-stu-id="d479f-290">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="d479f-291">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="d479f-291">Seed the database</span></span>

<span data-ttu-id="d479f-292">Fügen Sie dem *Daten* Ordner die [seeddata](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) -Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="d479f-292">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="d479f-293">Rufen Sie `SeedData.Initialize` aus `Main`:</span><span class="sxs-lookup"><span data-stu-id="d479f-293">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="d479f-294">Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt.</span><span class="sxs-lookup"><span data-stu-id="d479f-294">Test that the app seeded the database.</span></span> <span data-ttu-id="d479f-295">Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d479f-295">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="d479f-296">In diesem Tutorial veranschaulicht das Erstellen einer ASP.NET Core-Web-Apps mit Benutzerdaten, die durch Autorisierung geschützt sind.</span><span class="sxs-lookup"><span data-stu-id="d479f-296">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="d479f-297">Es zeigt eine Liste von Kontakten, die (registrierter) Benutzer authentifiziert haben.</span><span class="sxs-lookup"><span data-stu-id="d479f-297">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="d479f-298">Es gibt drei Sicherheitsgruppen:</span><span class="sxs-lookup"><span data-stu-id="d479f-298">There are three security groups:</span></span>

* <span data-ttu-id="d479f-299">**Registrierte Benutzer** können alle genehmigten Daten anzeigen und bearbeiten und löschen können ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="d479f-299">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="d479f-300">**Manager** genehmigen oder ablehnen von Kontaktdaten können.</span><span class="sxs-lookup"><span data-stu-id="d479f-300">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="d479f-301">Nur genehmigte Kontakte für Benutzer sichtbar sind.</span><span class="sxs-lookup"><span data-stu-id="d479f-301">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="d479f-302">**Administratoren** können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-302">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="d479f-303">In der folgenden Abbildung wird der Benutzer Rick (`rick@example.com`) angemeldet ist.</span><span class="sxs-lookup"><span data-stu-id="d479f-303">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="d479f-304">Rick kann nur genehmigte Kontakte anzeigen und **bearbeiten**/**löschen**/**neu erstellen** Links, um seine Kontakte.</span><span class="sxs-lookup"><span data-stu-id="d479f-304">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="d479f-305">Nur der letzte Datensatz erstellt, von Rick, zeigt **bearbeiten** und **löschen** Links.</span><span class="sxs-lookup"><span data-stu-id="d479f-305">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="d479f-306">Andere Benutzer werden der letzte Eintrag nicht angezeigt, bis Manager oder Administratoren den Status "Genehmigt" annimmt.</span><span class="sxs-lookup"><span data-stu-id="d479f-306">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![Screenshot der Rick angemeldet](secure-data/_static/rick.png)

<span data-ttu-id="d479f-308">In der folgenden Abbildung `manager@contoso.com` ist angemeldet und in der Rolle des Vorgesetzten:</span><span class="sxs-lookup"><span data-stu-id="d479f-308">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![Screenshot mit manager@contoso.com angemeldet](secure-data/_static/manager1.png)

<span data-ttu-id="d479f-310">Die folgende Abbildung zeigt die Manager Detailansicht eines Kontakts an:</span><span class="sxs-lookup"><span data-stu-id="d479f-310">The following image shows the managers details view of a contact:</span></span>

![Anzeigen von Vorgesetzten eines Kontakts](secure-data/_static/manager.png)

<span data-ttu-id="d479f-312">Die **genehmigen** und **ablehnen** Schaltflächen sind nur für Manager und Administratoren angezeigt.</span><span class="sxs-lookup"><span data-stu-id="d479f-312">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="d479f-313">In der folgenden Abbildung `admin@contoso.com` ist angemeldet und in der Rolle des Administrators:</span><span class="sxs-lookup"><span data-stu-id="d479f-313">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![Screenshot mit admin@contoso.com angemeldet](secure-data/_static/admin.png)

<span data-ttu-id="d479f-315">Der Administrator muss alle Berechtigungen.</span><span class="sxs-lookup"><span data-stu-id="d479f-315">The administrator has all privileges.</span></span> <span data-ttu-id="d479f-316">Sie können lesen, bearbeiten und löschen Sie einen beliebigen Kontakt, und ändern Sie den Status von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="d479f-316">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="d479f-317">Die app wurde erstellt, indem [Gerüstbau](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) Folgendes `Contact` Modell:</span><span class="sxs-lookup"><span data-stu-id="d479f-317">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="d479f-318">Das Beispiel enthält die folgenden Handler für die Autorisierung:</span><span class="sxs-lookup"><span data-stu-id="d479f-318">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="d479f-319">`ContactIsOwnerAuthorizationHandler`: Stellt sicher, dass ein Benutzer nur die Daten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="d479f-319">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="d479f-320">`ContactManagerAuthorizationHandler`: Ermöglicht Managern das genehmigen oder ablehnen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="d479f-320">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="d479f-321">`ContactAdministratorsAuthorizationHandler`: Ermöglicht Administratoren das genehmigen oder ablehnen von Kontakten sowie das Bearbeiten/Löschen von Kontakten.</span><span class="sxs-lookup"><span data-stu-id="d479f-321">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d479f-322">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="d479f-322">Prerequisites</span></span>

<span data-ttu-id="d479f-323">In diesem Tutorial wird verschoben.</span><span class="sxs-lookup"><span data-stu-id="d479f-323">This tutorial is advanced.</span></span> <span data-ttu-id="d479f-324">Sie sollten mit vertraut sein:</span><span class="sxs-lookup"><span data-stu-id="d479f-324">You should be familiar with:</span></span>

* [<span data-ttu-id="d479f-325">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d479f-325">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="d479f-326">Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="d479f-326">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="d479f-327">Kontobestätigung und Kennwortwiederherstellung</span><span class="sxs-lookup"><span data-stu-id="d479f-327">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d479f-328">Autorisierung</span><span class="sxs-lookup"><span data-stu-id="d479f-328">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="d479f-329">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="d479f-329">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="d479f-330">Die Starter und die fertige app</span><span class="sxs-lookup"><span data-stu-id="d479f-330">The starter and completed app</span></span>

<span data-ttu-id="d479f-331">[Herunterladen](xref:index#how-to-download-a-sample) der [abgeschlossen](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span><span class="sxs-lookup"><span data-stu-id="d479f-331">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="d479f-332">[Test](#test-the-completed-app) die fertige app, damit Sie mit den Security-Features vertraut machen.</span><span class="sxs-lookup"><span data-stu-id="d479f-332">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="d479f-333">Das Starter-app</span><span class="sxs-lookup"><span data-stu-id="d479f-333">The starter app</span></span>

<span data-ttu-id="d479f-334">[Herunterladen](xref:index#how-to-download-a-sample) der [Starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span><span class="sxs-lookup"><span data-stu-id="d479f-334">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="d479f-335">Führen Sie die app, tippen Sie auf die **ContactManager** verknüpfen, und stellen Sie sicher, Sie erstellen, bearbeiten und Löschen eines Kontakts.</span><span class="sxs-lookup"><span data-stu-id="d479f-335">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="d479f-336">Schützen von Benutzerdaten</span><span class="sxs-lookup"><span data-stu-id="d479f-336">Secure user data</span></span>

<span data-ttu-id="d479f-337">Die folgenden Abschnitte enthalten die wichtigsten Schritte zum Erstellen der sicheren Benutzer Daten-app.</span><span class="sxs-lookup"><span data-stu-id="d479f-337">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="d479f-338">Möglicherweise finden Sie es hilfreich sein, auf das abgeschlossene Projekt zu verweisen.</span><span class="sxs-lookup"><span data-stu-id="d479f-338">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="d479f-339">Verknüpfen Sie die Kontaktdaten für den Benutzer</span><span class="sxs-lookup"><span data-stu-id="d479f-339">Tie the contact data to the user</span></span>

<span data-ttu-id="d479f-340">Verwenden Sie die ASP.NET [Identität](xref:security/authentication/identity) Benutzer-ID, um sicherzustellen, dass Benutzer ihre Daten, aber nicht für andere Benutzerdaten bearbeiten kann.</span><span class="sxs-lookup"><span data-stu-id="d479f-340">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="d479f-341">Hinzufügen `OwnerID` und `ContactStatus` auf die `Contact` Modell:</span><span class="sxs-lookup"><span data-stu-id="d479f-341">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="d479f-342">`OwnerID` ist die ID des Benutzers aus der `AspNetUser` -Tabelle in der [Identität](xref:security/authentication/identity) Datenbank.</span><span class="sxs-lookup"><span data-stu-id="d479f-342">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="d479f-343">Die `Status` Feld bestimmt, ob ein Kontakt allgemeine Benutzer angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="d479f-343">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="d479f-344">Erstellen Sie eine neue Migration, und aktualisieren Sie die Datenbank:</span><span class="sxs-lookup"><span data-stu-id="d479f-344">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="d479f-345">Fügen Sie Rollendienste hinzu, die Identität</span><span class="sxs-lookup"><span data-stu-id="d479f-345">Add Role services to Identity</span></span>

<span data-ttu-id="d479f-346">Fügen Sie [Rollen](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) Rollendienste hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="d479f-346">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="d479f-347">Authentifizierte Benutzer</span><span class="sxs-lookup"><span data-stu-id="d479f-347">Require authenticated users</span></span>

<span data-ttu-id="d479f-348">Legen Sie die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen:</span><span class="sxs-lookup"><span data-stu-id="d479f-348">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="d479f-349">Sie können die Authentifizierung auf der Ebene der Razor Page, Controller und Aktion mit Deaktivieren der `[AllowAnonymous]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="d479f-349">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="d479f-350">Einstellung die Standardrichtlinie für die Authentifizierung der Benutzer authentifiziert werden müssen schützt das neu hinzugefügte Razor-Seiten und Controller.</span><span class="sxs-lookup"><span data-stu-id="d479f-350">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="d479f-351">Mit der Authentifizierung erforderlich, die in der Standardeinstellung sicherer ist als die auf neue Domänencontroller und Razor-Seiten enthalten die `[Authorize]` Attribut.</span><span class="sxs-lookup"><span data-stu-id="d479f-351">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="d479f-352">Hinzufügen ["AllowAnonymous"](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) auf den Index, Info "und" Wenden Sie sich an Seiten, damit anonyme Benutzer Informationen über die Website nutzen können, bevor sie sich registriert haben.</span><span class="sxs-lookup"><span data-stu-id="d479f-352">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="d479f-353">Konfigurieren des Testkontos</span><span class="sxs-lookup"><span data-stu-id="d479f-353">Configure the test account</span></span>

<span data-ttu-id="d479f-354">Die `SeedData` Klasse erstellt zwei Konten: Administrator und Manager.</span><span class="sxs-lookup"><span data-stu-id="d479f-354">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="d479f-355">Verwenden der [Secret Manager-Tool](xref:security/app-secrets) , ein Kennwort für diese Konten festzulegen.</span><span class="sxs-lookup"><span data-stu-id="d479f-355">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="d479f-356">Legen Sie das Kennwort aus dem Projektverzeichnis (das Verzeichnis mit *"Program.cs"* ):</span><span class="sxs-lookup"><span data-stu-id="d479f-356">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="d479f-357">Wenn Sie ein sicheres Kennwort nicht angegeben ist, eine Ausnahme wird ausgelöst, wenn `SeedData.Initialize` aufgerufen wird.</span><span class="sxs-lookup"><span data-stu-id="d479f-357">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="d479f-358">Update `Main` Testkennwort verwenden:</span><span class="sxs-lookup"><span data-stu-id="d479f-358">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="d479f-359">Erstellen Sie die Testkonten und aktualisieren Kontakte</span><span class="sxs-lookup"><span data-stu-id="d479f-359">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="d479f-360">Update der `Initialize` -Methode in der die `SeedData` Klasse, um die Testkonten zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="d479f-360">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="d479f-361">Fügen Sie die Administrator-Benutzer-ID und `ContactStatus` für die Contacts.</span><span class="sxs-lookup"><span data-stu-id="d479f-361">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="d479f-362">Stellen Sie einen der Kontakte für "Gesendet" und eine "abgelehnt".</span><span class="sxs-lookup"><span data-stu-id="d479f-362">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="d479f-363">Fügen Sie die Benutzer-ID und den Status an alle Kontakte hinzu.</span><span class="sxs-lookup"><span data-stu-id="d479f-363">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="d479f-364">Nur ein Kontakt wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="d479f-364">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="d479f-365">Besitzer, Manager und Administratoren Autorisierung Handler zu erstellen</span><span class="sxs-lookup"><span data-stu-id="d479f-365">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="d479f-366">Erstellen Sie eine `ContactIsOwnerAuthorizationHandler` -Klasse in der *Autorisierung* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-366">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d479f-367">Die `ContactIsOwnerAuthorizationHandler` stellt sicher, dass der Benutzer, die auf eine Ressource, das die Ressource besitzt.</span><span class="sxs-lookup"><span data-stu-id="d479f-367">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="d479f-368">Die `ContactIsOwnerAuthorizationHandler` Aufrufe [Kontext. Erfolgreiche](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) ist der aktuelle authentifizierte Benutzer der Besitzer des Kontakts.</span><span class="sxs-lookup"><span data-stu-id="d479f-368">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="d479f-369">Autorisierung Handler in der Regel:</span><span class="sxs-lookup"><span data-stu-id="d479f-369">Authorization handlers generally:</span></span>

* <span data-ttu-id="d479f-370">Zurückgeben `context.Succeed` Wenn die Anforderungen erfüllt sind.</span><span class="sxs-lookup"><span data-stu-id="d479f-370">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="d479f-371">Zurückgeben `Task.CompletedTask` Wenn sind nicht erfüllt.</span><span class="sxs-lookup"><span data-stu-id="d479f-371">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="d479f-372">`Task.CompletedTask`ist nicht erfolgreich oder Fehler&mdash;Haft, sodass andere Autorisierungs Handler ausgeführt werden können.</span><span class="sxs-lookup"><span data-stu-id="d479f-372">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="d479f-373">Wenn Sie explizit ausführen müssen, zurückgeben [Kontext. Fehler](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span><span class="sxs-lookup"><span data-stu-id="d479f-373">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="d479f-374">Der app können Besitzer von Kontaktinformationen bearbeiten/löschen/erstellen ihre eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="d479f-374">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="d479f-375">`ContactIsOwnerAuthorizationHandler` Überprüfen Sie den Vorgang, der im Parameter "Anforderung" übergeben muss nicht.</span><span class="sxs-lookup"><span data-stu-id="d479f-375">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="d479f-376">Erstellen Sie einen Ereignishandler der Manager-Autorisierung</span><span class="sxs-lookup"><span data-stu-id="d479f-376">Create a manager authorization handler</span></span>

<span data-ttu-id="d479f-377">Erstellen Sie eine `ContactManagerAuthorizationHandler` -Klasse in der *Autorisierung* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-377">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d479f-378">Die `ContactManagerAuthorizationHandler` überprüft, ob sich der Benutzer, die auf die Ressource eines Managers.</span><span class="sxs-lookup"><span data-stu-id="d479f-378">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="d479f-379">Nur Manager können genehmigen oder ablehnen von Änderungen am Inhalt (neue oder geänderte).</span><span class="sxs-lookup"><span data-stu-id="d479f-379">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="d479f-380">Erstellen Sie ein Administrator autorisierungshandler</span><span class="sxs-lookup"><span data-stu-id="d479f-380">Create an administrator authorization handler</span></span>

<span data-ttu-id="d479f-381">Erstellen Sie eine `ContactAdministratorsAuthorizationHandler` -Klasse in der *Autorisierung* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-381">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d479f-382">Die `ContactAdministratorsAuthorizationHandler` überprüft, ob der Benutzer, die auf die Ressource ist ein Administrator.</span><span class="sxs-lookup"><span data-stu-id="d479f-382">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="d479f-383">Administrator möglich, alle Vorgänge.</span><span class="sxs-lookup"><span data-stu-id="d479f-383">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="d479f-384">Registrieren Sie die Autorisierung-Handler</span><span class="sxs-lookup"><span data-stu-id="d479f-384">Register the authorization handlers</span></span>

<span data-ttu-id="d479f-385">Dienste, die mit Entity Framework Core müssen registriert werden, für die [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection) mit [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span><span class="sxs-lookup"><span data-stu-id="d479f-385">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="d479f-386">Die `ContactIsOwnerAuthorizationHandler` verwendet ASP.NET Core [Identität](xref:security/authentication/identity), die basiert auf Entity Framework Core.</span><span class="sxs-lookup"><span data-stu-id="d479f-386">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="d479f-387">Registrieren Sie die Handler mit der Sammlung von Diensten, damit sie verfügbar sind die `ContactsController` über [Abhängigkeitsinjektion](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="d479f-387">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d479f-388">Fügen Sie den folgenden Code am Ende der `ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="d479f-388">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="d479f-389">`ContactAdministratorsAuthorizationHandler` und `ContactManagerAuthorizationHandler` als Singletons hinzugefügt werden sollen.</span><span class="sxs-lookup"><span data-stu-id="d479f-389">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="d479f-390">Sie Singletons sind, da sie nicht die EF verwenden und alle erforderlichen Informationen in den `Context` Parameter, der die `HandleRequirementAsync` Methode.</span><span class="sxs-lookup"><span data-stu-id="d479f-390">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="d479f-391">Unterstützung für Autorisierung</span><span class="sxs-lookup"><span data-stu-id="d479f-391">Support authorization</span></span>

<span data-ttu-id="d479f-392">In diesem Abschnitt Aktualisieren Sie die Razor-Seiten und ein Operations-Anforderungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="d479f-392">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="d479f-393">Überprüfen Sie die Kontaktinformationen Vorgänge Anforderungen-Klasse</span><span class="sxs-lookup"><span data-stu-id="d479f-393">Review the contact operations requirements class</span></span>

<span data-ttu-id="d479f-394">Überprüfen Sie die `ContactOperations` Klasse.</span><span class="sxs-lookup"><span data-stu-id="d479f-394">Review the `ContactOperations` class.</span></span> <span data-ttu-id="d479f-395">Diese Klasse enthält die Anforderungen der app unterstützt:</span><span class="sxs-lookup"><span data-stu-id="d479f-395">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="d479f-396">Erstellen Sie eine Basisklasse für die Kontakte-Razor-Seiten</span><span class="sxs-lookup"><span data-stu-id="d479f-396">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="d479f-397">Erstellen Sie eine Basisklasse, die die in den Kontakten Razor-Seiten verwendeten Dienste enthält.</span><span class="sxs-lookup"><span data-stu-id="d479f-397">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="d479f-398">Die Basisklasse der Klasse wird den Initialisierungscode an einem Ort:</span><span class="sxs-lookup"><span data-stu-id="d479f-398">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="d479f-399">Der vorangehende Code:</span><span class="sxs-lookup"><span data-stu-id="d479f-399">The preceding code:</span></span>

* <span data-ttu-id="d479f-400">Fügt der `IAuthorizationService` Dienst den Zugriff auf die Handler für die Autorisierung.</span><span class="sxs-lookup"><span data-stu-id="d479f-400">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="d479f-401">Fügt die Identität `UserManager` Service.</span><span class="sxs-lookup"><span data-stu-id="d479f-401">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="d479f-402">Fügen Sie die `ApplicationDbContext` hinzu.</span><span class="sxs-lookup"><span data-stu-id="d479f-402">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="d479f-403">Aktualisieren der CreateModel</span><span class="sxs-lookup"><span data-stu-id="d479f-403">Update the CreateModel</span></span>

<span data-ttu-id="d479f-404">Aktualisieren den erstellen-Page-Modell-Konstruktor verwendet die `DI_BasePageModel` Basisklasse:</span><span class="sxs-lookup"><span data-stu-id="d479f-404">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="d479f-405">Update der `CreateModel.OnPostAsync` Methode, um:</span><span class="sxs-lookup"><span data-stu-id="d479f-405">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="d479f-406">Hinzufügen der Benutzer-ID an den `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="d479f-406">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="d479f-407">Rufen Sie die autorisierungshandler, um sicherzustellen, dass der Benutzer die Berechtigung zum Erstellen von Kontakten verfügt.</span><span class="sxs-lookup"><span data-stu-id="d479f-407">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="d479f-408">Aktualisieren Sie die IndexModel</span><span class="sxs-lookup"><span data-stu-id="d479f-408">Update the IndexModel</span></span>

<span data-ttu-id="d479f-409">Update der `OnGetAsync` Methode, sodass nur genehmigte Kontakte für allgemeine Benutzer angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="d479f-409">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="d479f-410">Aktualisieren Sie die EditModel</span><span class="sxs-lookup"><span data-stu-id="d479f-410">Update the EditModel</span></span>

<span data-ttu-id="d479f-411">Fügen Sie einem autorisierungshandler, um sicherzustellen, dass der Benutzer ist Besitzer der den Kontakt hinzu.</span><span class="sxs-lookup"><span data-stu-id="d479f-411">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="d479f-412">Da die Ressource Autorisierung überprüft wird, die `[Authorize]` -Attribut ist nicht ausreichend.</span><span class="sxs-lookup"><span data-stu-id="d479f-412">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="d479f-413">Die app hat keinen Zugriff auf die Ressource, wenn die Attribute ausgewertet werden.</span><span class="sxs-lookup"><span data-stu-id="d479f-413">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="d479f-414">Ressourcenbasierte Autorisierung muss zwingend erforderlich.</span><span class="sxs-lookup"><span data-stu-id="d479f-414">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="d479f-415">Überprüfungen müssen ausgeführt werden, sobald die app Zugriff auf die Ressource, oder durch Laden in das Modell als auch innerhalb des Handlers selbst laden.</span><span class="sxs-lookup"><span data-stu-id="d479f-415">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="d479f-416">Sie werden häufig die Ressource durch die Übergabe der Ressourcenschlüssel zugreifen.</span><span class="sxs-lookup"><span data-stu-id="d479f-416">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="d479f-417">Aktualisieren Sie die DeleteModel</span><span class="sxs-lookup"><span data-stu-id="d479f-417">Update the DeleteModel</span></span>

<span data-ttu-id="d479f-418">Aktualisieren Sie das Seitenmodell "löschen" um den autorisierungshandler für die zu verwenden, um sicherzustellen, dass der Benutzer über die Löschberechtigung für den Kontakt verfügt.</span><span class="sxs-lookup"><span data-stu-id="d479f-418">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="d479f-419">Fügen Sie dem Prüfdienst in den Ansichten</span><span class="sxs-lookup"><span data-stu-id="d479f-419">Inject the authorization service into the views</span></span>

<span data-ttu-id="d479f-420">Derzeit wird die Benutzeroberfläche zeigt bearbeiten und Löschen von Verknüpfungen für Kontakte, die der Benutzer nicht ändern kann.</span><span class="sxs-lookup"><span data-stu-id="d479f-420">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="d479f-421">Einfügen des autorisierungsdiensts in die *Views/_viewimports.cshtml* Datei, sodass sie für alle Ansichten verfügbar ist:</span><span class="sxs-lookup"><span data-stu-id="d479f-421">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="d479f-422">Das vorhergehende Markup fügt mehrere `using` Anweisungen.</span><span class="sxs-lookup"><span data-stu-id="d479f-422">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="d479f-423">Update der **bearbeiten** und **löschen** verknüpft *Pages/Contacts/Index.cshtml* , sodass sie nur für Benutzer mit den entsprechenden Berechtigungen gerendert werden:</span><span class="sxs-lookup"><span data-stu-id="d479f-423">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="d479f-424">Ausblenden von Links von Benutzern, die keine Berechtigung zum Ändern von Daten haben nicht die app zu sichern.</span><span class="sxs-lookup"><span data-stu-id="d479f-424">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="d479f-425">Ausblenden von Links wird die app durch Anzeigen der einzige gültige Links Benutzerfreundlicher.</span><span class="sxs-lookup"><span data-stu-id="d479f-425">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="d479f-426">Benutzer können die generierten URLs zum Aufrufen von bearbeiten und Löschen von Vorgänge für Daten, die sie besitzen keine hack.</span><span class="sxs-lookup"><span data-stu-id="d479f-426">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="d479f-427">Der Razor Page oder einen Controller muss zugriffsüberprüfungen zum Schutz der Daten erzwingen.</span><span class="sxs-lookup"><span data-stu-id="d479f-427">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="d479f-428">Updatedetails</span><span class="sxs-lookup"><span data-stu-id="d479f-428">Update Details</span></span>

<span data-ttu-id="d479f-429">Aktualisieren Sie die Detailansicht, damit Manager genehmigen oder ablehnen von Kontakten können:</span><span class="sxs-lookup"><span data-stu-id="d479f-429">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="d479f-430">Aktualisieren Sie das Seitenmodell Details an:</span><span class="sxs-lookup"><span data-stu-id="d479f-430">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="d479f-431">Hinzufügen oder Entfernen eines Benutzers zu einer Rolle</span><span class="sxs-lookup"><span data-stu-id="d479f-431">Add or remove a user to a role</span></span>

<span data-ttu-id="d479f-432">Finden Sie unter [dieses Problem](https://github.com/aspnet/AspNetCore.Docs/issues/8502) Informationen auf:</span><span class="sxs-lookup"><span data-stu-id="d479f-432">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="d479f-433">Entfernen Berechtigungen eines Benutzers ein.</span><span class="sxs-lookup"><span data-stu-id="d479f-433">Removing privileges from a user.</span></span> <span data-ttu-id="d479f-434">Beispielsweise das stumm Schaltung eines Benutzers in einer Chat-app.</span><span class="sxs-lookup"><span data-stu-id="d479f-434">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="d479f-435">Hinzufügen von Berechtigungen für einen Benutzer aus.</span><span class="sxs-lookup"><span data-stu-id="d479f-435">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="d479f-436">Testen Sie die fertige app</span><span class="sxs-lookup"><span data-stu-id="d479f-436">Test the completed app</span></span>

<span data-ttu-id="d479f-437">Wenn Sie ein Kennwort für die per Seeding hinzugefügten Benutzerkonten bereits festgelegt haben, verwenden Sie die [Secret Manager-Tool](xref:security/app-secrets#secret-manager) zum Festlegen eines Kennworts:</span><span class="sxs-lookup"><span data-stu-id="d479f-437">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="d479f-438">Wählen Sie ein sicheres Kennwort: Verwenden Sie acht oder mehr Zeichen und mindestens ein Großbuchstabe, eine Zahl und ein Symbol.</span><span class="sxs-lookup"><span data-stu-id="d479f-438">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="d479f-439">Z. B. `Passw0rd!` erfüllt die Anforderungen für sichere Kennwörter.</span><span class="sxs-lookup"><span data-stu-id="d479f-439">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="d479f-440">Führen Sie den folgenden Befehl aus dem Ordner des Projekts, in denen `<PW>` ist das Kennwort:</span><span class="sxs-lookup"><span data-stu-id="d479f-440">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="d479f-441">Löschen und Aktualisieren der Datenbank</span><span class="sxs-lookup"><span data-stu-id="d479f-441">Drop and update the Database</span></span>

    ```console
     dotnet ef database drop -f
     dotnet ef database update  
     ```

* <span data-ttu-id="d479f-442">Starten Sie die app zum Seeding der Datenbank neu.</span><span class="sxs-lookup"><span data-stu-id="d479f-442">Restart the app to seed the database.</span></span>

<span data-ttu-id="d479f-443">Eine einfache Möglichkeit zum Testen der fertigen app wird auf drei verschiedene Browser (oder inkognito/InPrivate-Sitzungen) zu starten.</span><span class="sxs-lookup"><span data-stu-id="d479f-443">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="d479f-444">Registrieren Sie einen neuen Benutzer in einem Browser (z. B. `test@example.com`).</span><span class="sxs-lookup"><span data-stu-id="d479f-444">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="d479f-445">Melden Sie sich an jeden Browser mit einem anderen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="d479f-445">Sign in to each browser with a different user.</span></span> <span data-ttu-id="d479f-446">Überprüfen Sie die folgenden Vorgänge aus:</span><span class="sxs-lookup"><span data-stu-id="d479f-446">Verify the following operations:</span></span>

* <span data-ttu-id="d479f-447">Registrierte Benutzer können alle genehmigten wenden Sie sich an Daten anzeigen.</span><span class="sxs-lookup"><span data-stu-id="d479f-447">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="d479f-448">Registrierte Benutzer können bearbeiten und ihre eigenen Daten löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-448">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="d479f-449">Manager können genehmigen/Kontaktdaten ablehnen.</span><span class="sxs-lookup"><span data-stu-id="d479f-449">Managers can approve/reject contact data.</span></span> <span data-ttu-id="d479f-450">Die `Details` anzeigen zeigt **genehmigen** und **ablehnen** Schaltflächen.</span><span class="sxs-lookup"><span data-stu-id="d479f-450">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="d479f-451">Administratoren können genehmigen/ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-451">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="d479f-452">Benutzer</span><span class="sxs-lookup"><span data-stu-id="d479f-452">User</span></span>                | <span data-ttu-id="d479f-453">Ein Seeding ausgeführt, von der app</span><span class="sxs-lookup"><span data-stu-id="d479f-453">Seeded by the app</span></span> | <span data-ttu-id="d479f-454">Optionen</span><span class="sxs-lookup"><span data-stu-id="d479f-454">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="d479f-455">Nein</span><span class="sxs-lookup"><span data-stu-id="d479f-455">No</span></span>                | <span data-ttu-id="d479f-456">Löschen Sie bearbeiten und die eigenen Daten.</span><span class="sxs-lookup"><span data-stu-id="d479f-456">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="d479f-457">Ja</span><span class="sxs-lookup"><span data-stu-id="d479f-457">Yes</span></span>               | <span data-ttu-id="d479f-458">Genehmigen oder ablehnen und eigene Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-458">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="d479f-459">Ja</span><span class="sxs-lookup"><span data-stu-id="d479f-459">Yes</span></span>               | <span data-ttu-id="d479f-460">Genehmigen oder ablehnen und alle Daten bearbeiten und löschen.</span><span class="sxs-lookup"><span data-stu-id="d479f-460">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="d479f-461">Erstellen Sie einen Kontakt in der Administrator-Browser.</span><span class="sxs-lookup"><span data-stu-id="d479f-461">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="d479f-462">Kopieren Sie die URL für das Löschen und Bearbeiten von den Administrator wenden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="d479f-462">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="d479f-463">Fügen Sie diesen Links, in den Browser des Testbenutzers zu überprüfen, ob die Testbenutzers diese Vorgänge kann nicht ein.</span><span class="sxs-lookup"><span data-stu-id="d479f-463">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="d479f-464">Die Starter-app erstellen</span><span class="sxs-lookup"><span data-stu-id="d479f-464">Create the starter app</span></span>

* <span data-ttu-id="d479f-465">Erstellen einer Razor Pages-app, die mit dem Namen "ContactManager"</span><span class="sxs-lookup"><span data-stu-id="d479f-465">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="d479f-466">Erstellen Sie die app mit **einzelne Benutzerkonten**.</span><span class="sxs-lookup"><span data-stu-id="d479f-466">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="d479f-467">Nennen Sie ihn "ContactManager", sodass der Namespace den im Beispiel verwendeten Namespace entspricht.</span><span class="sxs-lookup"><span data-stu-id="d479f-467">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="d479f-468">`-uld` Gibt an, LocalDB anstelle von SQLite</span><span class="sxs-lookup"><span data-stu-id="d479f-468">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="d479f-469">*Modelle/Contact. cs*hinzufügen:</span><span class="sxs-lookup"><span data-stu-id="d479f-469">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="d479f-470">Gerüst der `Contact` Modell.</span><span class="sxs-lookup"><span data-stu-id="d479f-470">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="d479f-471">Erstellen Sie der anfänglichen Migration und aktualisieren Sie die Datenbank zu:</span><span class="sxs-lookup"><span data-stu-id="d479f-471">Create initial migration and update the database:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="d479f-472">Update der **ContactManager** verankert werden der *Pages/_Layout.cshtml* Datei:</span><span class="sxs-lookup"><span data-stu-id="d479f-472">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="d479f-473">Testen der app, indem erstellen, bearbeiten und Löschen eines Kontakts</span><span class="sxs-lookup"><span data-stu-id="d479f-473">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="d479f-474">Ausführen eines Seedings für die Datenbank</span><span class="sxs-lookup"><span data-stu-id="d479f-474">Seed the database</span></span>

<span data-ttu-id="d479f-475">Hinzufügen der [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) -Klasse auf die *Daten* Ordner.</span><span class="sxs-lookup"><span data-stu-id="d479f-475">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="d479f-476">Rufen Sie `SeedData.Initialize` aus `Main`:</span><span class="sxs-lookup"><span data-stu-id="d479f-476">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="d479f-477">Testen Sie, dass die app die Datenbank mit Anfangsdaten gefüllt.</span><span class="sxs-lookup"><span data-stu-id="d479f-477">Test that the app seeded the database.</span></span> <span data-ttu-id="d479f-478">Wenn alle Zeilen in der DB-Kontakt vorhanden sind, wird die Seed-Methode nicht ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="d479f-478">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="d479f-479">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="d479f-479">Additional resources</span></span>

* [<span data-ttu-id="d479f-480">Erstellen einer .NET Core- und SQL-Datenbank-Web-Apps in Azure App Service</span><span class="sxs-lookup"><span data-stu-id="d479f-480">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="d479f-481">[ASP.NET Core-Autorisierung Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span><span class="sxs-lookup"><span data-stu-id="d479f-481">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="d479f-482">Dieser Übung wird ausführlicher auf den Sicherheitsfeatures, die in diesem Lernprogramm eingeführt.</span><span class="sxs-lookup"><span data-stu-id="d479f-482">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="d479f-483">Benutzerdefinierte, richtlinienbasierte Autorisierung</span><span class="sxs-lookup"><span data-stu-id="d479f-483">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
