---
uid: mvc/overview/getting-started/database-first-development/changing-the-database
title: 'Tutorial: Ändern Sie die Datenbank für EF Database First mit ASP.NET MVC-app'
description: Dieses Tutorial konzentriert sich auf eine Aktualisierung vornehmen, um die Struktur der Datenbank und das Weitergeben von dieser Änderung in der Webanwendung.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: cfd5c083-a319-482e-8f25-5b38caa93954
msc.legacyurl: /mvc/overview/getting-started/database-first-development/changing-the-database
msc.type: authoredcontent
ms.openlocfilehash: 52cad1120908cf0d4f85770f8e2690f9415c5f56
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667634"
---
# <a name="tutorial-change-the-database-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="4ccb3-103">Tutorial: Ändern Sie die Datenbank für EF Database First mit ASP.NET MVC-app</span><span class="sxs-lookup"><span data-stu-id="4ccb3-103">Tutorial: Change the database for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="4ccb3-104">Verwenden MVC, Entity Framework und ASP.NET-Gerüstbau, können Sie eine Webanwendung erstellen, die eine Schnittstelle für eine vorhandene Datenbank bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="4ccb3-105">Dieser tutorialreihe erfahren Sie, wie Sie automatisch generierter Code, der ermöglicht Benutzern das anzeigen, bearbeiten, erstellen und Löschen von Daten, die in einer Datenbanktabelle gespeichert.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="4ccb3-106">Der generierte Code entspricht die Spalten in der Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="4ccb3-107">Dieses Tutorial konzentriert sich auf eine Aktualisierung vornehmen, um die Struktur der Datenbank und das Weitergeben von dieser Änderung in der Webanwendung.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-107">This tutorial focuses on making an update to the database structure and propagating that change throughout the web application.</span></span>

<span data-ttu-id="4ccb3-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="4ccb3-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ccb3-109">Hinzufügen einer Spalte</span><span class="sxs-lookup"><span data-stu-id="4ccb3-109">Add a column</span></span>
> * <span data-ttu-id="4ccb3-110">Fügen Sie die Eigenschaft, die den Ansichten</span><span class="sxs-lookup"><span data-stu-id="4ccb3-110">Add the property to the views</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4ccb3-111">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="4ccb3-111">Prerequisites</span></span>

* [<span data-ttu-id="4ccb3-112">Generieren von Sichten</span><span class="sxs-lookup"><span data-stu-id="4ccb3-112">Generating views</span></span>](generating-views.md)

## <a name="add-a-column"></a><span data-ttu-id="4ccb3-113">Hinzufügen einer Spalte</span><span class="sxs-lookup"><span data-stu-id="4ccb3-113">Add a column</span></span>

<span data-ttu-id="4ccb3-114">Wenn Sie die Struktur einer Tabelle in der Datenbank aktualisieren, müssen Sie sicherstellen, dass die Änderung an das Datenmodell, Ansichten und Controller weitergegeben wird.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-114">If you update the structure of a table in your database, you need to ensure that your change is propagated to the data model, views, and controller.</span></span>

<span data-ttu-id="4ccb3-115">In diesem Tutorial werden Sie eine neue Spalte der Tabelle "Student", um den zweiten Vornamen der Student aufzuzeichnen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-115">For this tutorial, you will add a new column to the Student table to record the middle name of the student.</span></span> <span data-ttu-id="4ccb3-116">Um diese Spalte hinzuzufügen, öffnen Sie das Datenbankprojekt, und öffnen Sie die Datei Student.sql.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-116">To add this column, open the database project, and open the Student.sql file.</span></span> <span data-ttu-id="4ccb3-117">Über Designer "oder" T-SQL-Code, fügen Sie eine Spalte, die mit dem Namen **MiddleName** , ein NVARCHAR(50) und NULL-Werte zulässt.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-117">Through either the designer or the T-SQL code, add a column named **MiddleName** that is an NVARCHAR(50) and allows NULL values.</span></span>

<span data-ttu-id="4ccb3-118">Ihr Datenbankprojekt (oder F5) starten, um bereitstellen Sie diese Änderung in Ihre lokale Datenbank.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-118">Deploy this change to your local database by starting your database project (or F5).</span></span> <span data-ttu-id="4ccb3-119">Das neue Feld wird in der Tabelle hinzugefügt.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-119">The new field is added to the table.</span></span> <span data-ttu-id="4ccb3-120">Wenn Sie es in der Objekt-Explorer von SQL Server nicht angezeigt werden, klicken Sie auf die Schaltfläche "Aktualisieren", klicken Sie im Bereich.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-120">If you do not see it in the SQL Server Object Explorer, click the Refresh button in the pane.</span></span>

![neue Spalte anzeigen](changing-the-database/_static/image2.png)

<span data-ttu-id="4ccb3-122">Die neue Spalte, die in der Tabelle der Datenbank vorhanden ist, aber derzeit nicht in der Modellklasse für Daten vorhanden.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-122">The new column exists in the database table, but it does not currently exist in the data model class.</span></span> <span data-ttu-id="4ccb3-123">Sie müssen das Modell, um Ihre neue Spalte enthalten aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-123">You must update the model to include your new column.</span></span> <span data-ttu-id="4ccb3-124">In der **Modelle** Ordner die **ContosoModel.edmx** Datei in das Modelldiagramm anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-124">In the **Models** folder, open the **ContosoModel.edmx** file to display the model diagram.</span></span> <span data-ttu-id="4ccb3-125">Beachten Sie, dass das Modell "Student" nicht die MiddleName-Eigenschaft enthält.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-125">Notice that the Student model does not contain the MiddleName property.</span></span> <span data-ttu-id="4ccb3-126">Mit der rechten Maustaste auf die Entwurfsoberfläche, und wählen **Modell aus der Datenbank aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-126">Right-click anywhere on the design surface, and select **Update Model from Database**.</span></span>

<span data-ttu-id="4ccb3-127">Wählen Sie in der Update-Assistenten die **aktualisieren** Registerkarte, und wählen Sie dann **Tabellen** > **Dbo** > **für Schüler und Studenten**.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-127">In the Update Wizard, select the **Refresh** tab and then select **Tables** > **dbo** > **Student**.</span></span> <span data-ttu-id="4ccb3-128">Klicken Sie auf **Fertig stellen**.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-128">Click **Finish**.</span></span>

<span data-ttu-id="4ccb3-129">Nachdem der Aktualisierungsvorgang abgeschlossen ist, enthält das Datenbankdiagramm neuen **MiddleName** Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-129">After the update process is finished, the database diagram includes the new **MiddleName** property.</span></span> <span data-ttu-id="4ccb3-130">Speichern Sie die **ContosoModel.edmx** Datei.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-130">Save the **ContosoModel.edmx** file.</span></span> <span data-ttu-id="4ccb3-131">Sie müssen diese Datei für die neue Eigenschaft an weitergegeben speichern die **Student.cs** Klasse.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-131">You must save this file for the new property to be propagated to the **Student.cs** class.</span></span> <span data-ttu-id="4ccb3-132">Sie haben jetzt die Datenbank und das Modell aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-132">You have now updated the database and the model.</span></span>

<span data-ttu-id="4ccb3-133">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-133">Build the solution.</span></span>

## <a name="add-the-property-to-the-views"></a><span data-ttu-id="4ccb3-134">Fügen Sie die Eigenschaft, die den Ansichten</span><span class="sxs-lookup"><span data-stu-id="4ccb3-134">Add the property to the views</span></span>

<span data-ttu-id="4ccb3-135">Die Ansichten enthalten noch leider nicht die neue Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-135">Unfortunately, the views still do not contain the new property.</span></span> <span data-ttu-id="4ccb3-136">Aktualisieren Sie die Ansichten haben Sie zwei Optionen: können Sie entweder erneut generieren die Ansichten von wieder hinzufügen Gerüst für die Klasse "Student", oder Sie können die neue Eigenschaft manuell hinzufügen, um Ihre vorhandenen Ansichten.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-136">To update the views you have two options - you can either re-generate the views by once again adding scaffolding for the Student class, or you can manually add the new property to your existing views.</span></span> <span data-ttu-id="4ccb3-137">In diesem Tutorial fügen Sie das Gerüst erneut aus, da Sie nicht benutzerdefinierten Änderungen an den automatisch generierten Ansichten vorgenommen haben.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-137">In this tutorial, you will add the scaffolding again because you have not made any customized changes to the automatically-generated views.</span></span> <span data-ttu-id="4ccb3-138">Sie sollten erwägen, die Eigenschaft manuell hinzufügen, wenn Sie Änderungen, in den Ansichten vorgenommen haben und nicht, diese Änderungen verloren möchten.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-138">You might consider manually adding the property when you have made changes to the views and do not want to lose those changes.</span></span>

<span data-ttu-id="4ccb3-139">Um sicherzustellen, die Ansichten werden neu erstellt, löschen Sie die **Schüler/Studenten** Ordner unter **Ansichten**, und Löschen der **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-139">To ensure the views are re-created, delete the **Students** folder under **Views**, and delete the **StudentsController**.</span></span> <span data-ttu-id="4ccb3-140">Klicken Sie dann mit der rechten Maustaste die **Controller** Ordner und Hinzufügen der Gerüstbau für die **für Schüler und Studenten** Modell.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-140">Then, right-click the **Controllers** folder and add scaffolding for the **Student** model.</span></span> <span data-ttu-id="4ccb3-141">Nennen Sie den Controller wieder **StudentsController**.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-141">Again, name the controller **StudentsController**.</span></span> <span data-ttu-id="4ccb3-142">Wählen Sie **Hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-142">Select **Add**.</span></span>

<span data-ttu-id="4ccb3-143">Erstellen Sie die Projektmappe erneut.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-143">Build the solution again.</span></span> <span data-ttu-id="4ccb3-144">Die Ansichten enthalten jetzt die MiddleName-Eigenschaft.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-144">The views now contain the MiddleName property.</span></span>

![Zweiter Vorname anzeigen](changing-the-database/_static/image5.png)

## <a name="next-steps"></a><span data-ttu-id="4ccb3-146">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="4ccb3-146">Next steps</span></span>

<span data-ttu-id="4ccb3-147">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="4ccb3-147">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="4ccb3-148">Eine Spalte hinzugefügt</span><span class="sxs-lookup"><span data-stu-id="4ccb3-148">Added a column</span></span>
> * <span data-ttu-id="4ccb3-149">Die Eigenschaft, die Ansichten hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-149">Added the property to the views</span></span>

<span data-ttu-id="4ccb3-150">Fahren Sie fort mit dem nächsten Tutorial erfahren, wie die Ansicht zum Anzeigen von Details zu einem Student-Datensatz ändern.</span><span class="sxs-lookup"><span data-stu-id="4ccb3-150">Advance to the next tutorial to learn how to customize the view for showing details about a student record.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="4ccb3-151">Anpassen einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="4ccb3-151">Customize a view</span></span>](customizing-a-view.md)