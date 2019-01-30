---
uid: mvc/overview/getting-started/database-first-development/customizing-a-view
title: 'Tutorial: Anpassen der Ansicht für EF Database First mit ASP.NET MVC-app'
description: Dieses Tutorial konzentriert sich auf die automatisch generierten Ansichten zur Verbesserung der Präsentation zu ändern.
author: Rick-Anderson
ms.author: riande
ms.date: 01/24/2019
ms.topic: tutorial
ms.assetid: 269380ff-d7e1-4035-8ad1-fe1316a25f76
msc.legacyurl: /mvc/overview/getting-started/database-first-development/customizing-a-view
msc.type: authoredcontent
ms.openlocfilehash: 89b8a0eb84b6e287c45bc141c68a2c76e63b0e41
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236496"
---
# <a name="tutorial-customize-view-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="69146-103">Tutorial: Anpassen der Ansicht für EF Database First mit ASP.NET MVC-app</span><span class="sxs-lookup"><span data-stu-id="69146-103">Tutorial: Customize view for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="69146-104">Verwenden MVC, Entity Framework und ASP.NET-Gerüstbau, können Sie eine Webanwendung erstellen, die eine Schnittstelle für eine vorhandene Datenbank bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="69146-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="69146-105">Dieser tutorialreihe erfahren Sie, wie Sie automatisch generierter Code, der ermöglicht Benutzern das anzeigen, bearbeiten, erstellen und Löschen von Daten, die in einer Datenbanktabelle gespeichert.</span><span class="sxs-lookup"><span data-stu-id="69146-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="69146-106">Der generierte Code entspricht die Spalten in der Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="69146-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="69146-107">Dieses Tutorial konzentriert sich auf die automatisch generierten Ansichten zur Verbesserung der Präsentation zu ändern.</span><span class="sxs-lookup"><span data-stu-id="69146-107">This tutorial focuses on changing the automatically-generated views to enhance the presentation.</span></span>

<span data-ttu-id="69146-108">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="69146-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="69146-109">Hinzufügen von Kursen zur Detailseite für Schüler und Studenten</span><span class="sxs-lookup"><span data-stu-id="69146-109">Add courses to the student detail page</span></span>
> * <span data-ttu-id="69146-110">Vergewissern Sie sich, dass die Kurse zur Seite hinzugefügt werden</span><span class="sxs-lookup"><span data-stu-id="69146-110">Confirm that the courses are added to the page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="69146-111">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="69146-111">Prerequisites</span></span>

* [<span data-ttu-id="69146-112">Ändern Sie die Datenbank</span><span class="sxs-lookup"><span data-stu-id="69146-112">Change the database</span></span>](changing-the-database.md)

## <a name="add-courses-to-student-detail"></a><span data-ttu-id="69146-113">Kurse, Detail zum Kursteilnehmer hinzufügen</span><span class="sxs-lookup"><span data-stu-id="69146-113">Add courses to student detail</span></span>

<span data-ttu-id="69146-114">Der generierte Code bietet einen guten Ausgangspunkt für Ihre Anwendung bietet jedoch nicht unbedingt alle Funktionen, die Sie in Ihrer Anwendung benötigen.</span><span class="sxs-lookup"><span data-stu-id="69146-114">The generated code provides a good starting point for your application but it does not necessarily provide all of the functionality that you need in your application.</span></span> <span data-ttu-id="69146-115">Sie können den Code, um die bestimmten Anforderungen Ihrer Anwendung anpassen.</span><span class="sxs-lookup"><span data-stu-id="69146-115">You can customize the code to meet the particular requirements of your application.</span></span> <span data-ttu-id="69146-116">Die Anwendung wird derzeit nicht die registrierten Kurse für den ausgewählten Studenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="69146-116">Currently, your application does not display the enrolled courses for the selected student.</span></span> <span data-ttu-id="69146-117">In diesem Abschnitt fügen Sie die registrierten Kurse für jeden Kursteilnehmer auf die **Details** Ansicht für den Studenten.</span><span class="sxs-lookup"><span data-stu-id="69146-117">In this section, you will add the enrolled courses for each student to the **Details** view for the student.</span></span>

<span data-ttu-id="69146-118">Open **Ansichten** > **Schüler/Studenten** > *Details.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="69146-118">Open **Views** > **Students** > *Details.cshtml*.</span></span> <span data-ttu-id="69146-119">Unterhalb der letzten &lt;/DL&gt; -Tag, aber vor dem schließenden &lt;/div&gt; markieren, fügen Sie den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="69146-119">Below the last &lt;/dl&gt; tag, but before the closing &lt;/div&gt; tag, add the following code.</span></span>

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

<span data-ttu-id="69146-120">Dieser Code erstellt eine Tabelle, die eine Zeile für jeden Datensatz in der Enrollment-Tabelle für den ausgewählten Studenten angezeigt.</span><span class="sxs-lookup"><span data-stu-id="69146-120">This code creates a table that displays a row for each record in the Enrollment table for the selected student.</span></span> <span data-ttu-id="69146-121">Die **Anzeige** -Methode rendert die HTML für das Objekt (ModelItem), die den Ausdruck darstellt.</span><span class="sxs-lookup"><span data-stu-id="69146-121">The **Display** method renders HTML for the object (modelItem) that represents the expression.</span></span> <span data-ttu-id="69146-122">Verwenden Sie die Anzeige-Methode (anstatt einfach den Wert der Eigenschaft im Code) um sicherzustellen, dass der Wert ist ordnungsgemäß basierend auf den Typ und die Vorlage für diesen Typ formatiert.</span><span class="sxs-lookup"><span data-stu-id="69146-122">You use the Display method (rather than simply embedding the property value in the code) to make sure the value is formatted correctly based on its type and the template for that type.</span></span> <span data-ttu-id="69146-123">In diesem Beispiel jeder Ausdruck gibt eine einzelne Eigenschaft des aktuellen Datensatzes in der Schleife und die Werte sind primitive Typen, die als Text gerendert werden.</span><span class="sxs-lookup"><span data-stu-id="69146-123">In this example, each expression returns a single property from the current record in the loop, and the values are primitive types which are rendered as text.</span></span>

## <a name="confirm-courses-are-added"></a><span data-ttu-id="69146-124">Bestätigen Sie, dass die Kurse hinzugefügt werden</span><span class="sxs-lookup"><span data-stu-id="69146-124">Confirm courses are added</span></span>

<span data-ttu-id="69146-125">Führen Sie die Projektmappe aus.</span><span class="sxs-lookup"><span data-stu-id="69146-125">Run the solution.</span></span> <span data-ttu-id="69146-126">Klicken Sie auf **Liste der Studenten** , und wählen Sie **Details** für eines der Schüler/Studenten.</span><span class="sxs-lookup"><span data-stu-id="69146-126">Click **List of students** and select **Details** for one of the students.</span></span> <span data-ttu-id="69146-127">Sie sehen, dass die registrierten Kurse in der Ansicht hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="69146-127">You will see the enrolled courses have been included in the view.</span></span>

!["Student" mit Registrierung](customizing-a-view/_static/image1.png)

## <a name="next-steps"></a><span data-ttu-id="69146-129">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="69146-129">Next steps</span></span>
<span data-ttu-id="69146-130">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="69146-130">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="69146-131">Hinzugefügte Kurse an, um die Detailseite für Schüler und Studenten</span><span class="sxs-lookup"><span data-stu-id="69146-131">Added courses to the student detail page</span></span>
> * <span data-ttu-id="69146-132">Bestätigt, dass die Kurse zur Seite hinzugefügt werden</span><span class="sxs-lookup"><span data-stu-id="69146-132">Confirmed that the courses are added to the page</span></span>

<span data-ttu-id="69146-133">Fahren Sie fort mit dem nächsten Tutorial erfahren Sie, wie Hinzufügen von datenanmerkungen, geben Sie die überprüfungsanforderungen zu erfüllen und Formatierung anzeigen.</span><span class="sxs-lookup"><span data-stu-id="69146-133">Advance to the next tutorial to learn how to add data annotations to specify validation requirements and display formatting.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="69146-134">Optimieren der datenüberprüfung</span><span class="sxs-lookup"><span data-stu-id="69146-134">Enhance data validation</span></span>](enhancing-data-validation.md)
