---
uid: mvc/overview/getting-started/database-first-development/enhancing-data-validation
title: 'Tutorial: Optimieren der datenüberprüfung für EF Database First mit ASP.NET MVC-app'
description: Dieses Tutorial konzentriert sich auf das Hinzufügen von datenanmerkungen in das Datenmodell, geben Sie die überprüfungsanforderungen zu erfüllen und Formatierung anzeigen.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 0ed5e67a-34c0-4b57-84a6-802b0fb3cd00
msc.legacyurl: /mvc/overview/getting-started/database-first-development/enhancing-data-validation
msc.type: authoredcontent
ms.openlocfilehash: 85299d70c6cba52c1d40a42edfd429c96318134a
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236483"
---
# <a name="tutorial-enhance-data-validation-for-ef-database-first-with-aspnet-mvc-app"></a><span data-ttu-id="eef4e-103">Tutorial: Optimieren der datenüberprüfung für EF Database First mit ASP.NET MVC-app</span><span class="sxs-lookup"><span data-stu-id="eef4e-103">Tutorial: Enhance data validation for EF Database First with ASP.NET MVC app</span></span>

<span data-ttu-id="eef4e-104">Verwenden MVC, Entity Framework und ASP.NET-Gerüstbau, können Sie eine Webanwendung erstellen, die eine Schnittstelle für eine vorhandene Datenbank bereitstellt.</span><span class="sxs-lookup"><span data-stu-id="eef4e-104">Using MVC, Entity Framework, and ASP.NET Scaffolding, you can create a web application that provides an interface to an existing database.</span></span> <span data-ttu-id="eef4e-105">Dieser tutorialreihe erfahren Sie, wie Sie automatisch generierter Code, der ermöglicht Benutzern das anzeigen, bearbeiten, erstellen und Löschen von Daten, die in einer Datenbanktabelle gespeichert.</span><span class="sxs-lookup"><span data-stu-id="eef4e-105">This tutorial series shows you how to automatically generate code that enables users to display, edit, create, and delete data that resides in a database table.</span></span> <span data-ttu-id="eef4e-106">Der generierte Code entspricht die Spalten in der Datenbanktabelle.</span><span class="sxs-lookup"><span data-stu-id="eef4e-106">The generated code corresponds to the columns in the database table.</span></span>

<span data-ttu-id="eef4e-107">Dieses Tutorial konzentriert sich auf das Hinzufügen von datenanmerkungen in das Datenmodell, geben Sie die überprüfungsanforderungen zu erfüllen und Formatierung anzeigen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-107">This tutorial focuses on adding data annotations to the data model to specify validation requirements and display formatting.</span></span> <span data-ttu-id="eef4e-108">Es wurde basierend auf dem Feedback von Benutzern im Abschnitt "Kommentare" verbessert.</span><span class="sxs-lookup"><span data-stu-id="eef4e-108">It was improved based on feedback from users in the comments section.</span></span>

<span data-ttu-id="eef4e-109">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="eef4e-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eef4e-110">Hinzufügen von datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="eef4e-110">Add data annotations</span></span>
> * <span data-ttu-id="eef4e-111">Hinzufügen von Metadatenklassen</span><span class="sxs-lookup"><span data-stu-id="eef4e-111">Add metadata classes</span></span>

## <a name="prerequisites"></a><span data-ttu-id="eef4e-112">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="eef4e-112">Prerequisites</span></span>

* [<span data-ttu-id="eef4e-113">Anpassen einer Ansicht</span><span class="sxs-lookup"><span data-stu-id="eef4e-113">Customize a view</span></span>](customizing-a-view.md)

## <a name="add-data-annotations"></a><span data-ttu-id="eef4e-114">Hinzufügen von datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="eef4e-114">Add data annotations</span></span>

<span data-ttu-id="eef4e-115">In einem vorhergehenden Thema haben Sie gesehen, gelten einige Regeln für die datenvalidierung automatisch auf die Benutzereingabe.</span><span class="sxs-lookup"><span data-stu-id="eef4e-115">As you saw in an earlier topic, some data validation rules are automatically applied to the user input.</span></span> <span data-ttu-id="eef4e-116">Beispielsweise können Sie nur eine Zahl für die Eigenschaft auf Unternehmensniveau bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-116">For example, you can only provide a number for the Grade property.</span></span> <span data-ttu-id="eef4e-117">Um weitere Regeln für die datenvalidierung anzugeben, können Sie Ihrer Modellklasse datenanmerkungen hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-117">To specify more data validation rules, you can add data annotations to your model class.</span></span> <span data-ttu-id="eef4e-118">Diese Anmerkungen werden in Ihre Webanwendung für die angegebene Eigenschaft angewendet.</span><span class="sxs-lookup"><span data-stu-id="eef4e-118">These annotations are applied throughout your web application for the specified property.</span></span> <span data-ttu-id="eef4e-119">Sie können auch Formatierungsattribute anwenden, die sich ändern, wie die Eigenschaften angezeigt werden; z. B. das Ändern des Werts, der für Symboltitel verwendet.</span><span class="sxs-lookup"><span data-stu-id="eef4e-119">You can also apply formatting attributes that change how the properties are displayed; such as, changing the value used for text labels.</span></span>

<span data-ttu-id="eef4e-120">In diesem Tutorial fügen Sie datenanmerkungen, um die Länge der die Werte für die FirstName, MiddleName und LastName-Eigenschaften zu beschränken.</span><span class="sxs-lookup"><span data-stu-id="eef4e-120">In this tutorial, you will add data annotations to restrict the length of the values provided for the FirstName, LastName, and MiddleName properties.</span></span> <span data-ttu-id="eef4e-121">In der Datenbank sind diese Werte auf 50 Zeichen begrenzt. Allerdings wird in der Webanwendung, zeichenbeschränkung derzeit nicht erzwungen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-121">In the database, these values are limited to 50 characters; however, in your web application that character limit is currently not enforced.</span></span> <span data-ttu-id="eef4e-122">Wenn ein Benutzer mehr als 50 Zeichen für einen dieser Werte bereitstellt, stürzt die Seite beim Versuch, den Wert in der Datenbank speichern.</span><span class="sxs-lookup"><span data-stu-id="eef4e-122">If a user provides more than 50 characters for one of those values, the page will crash when attempting to save the value to the database.</span></span> <span data-ttu-id="eef4e-123">Sie werden auch auf Unternehmensniveau mit Werten zwischen 0 und 4 einschränken.</span><span class="sxs-lookup"><span data-stu-id="eef4e-123">You will also restrict Grade to values between 0 and 4.</span></span>

<span data-ttu-id="eef4e-124">Wählen Sie **Modelle** > **ContosoModel.edmx** > **ContosoModel.tt** , und öffnen Sie die *Student.cs* Datei.</span><span class="sxs-lookup"><span data-stu-id="eef4e-124">Select **Models** > **ContosoModel.edmx** > **ContosoModel.tt** and open the *Student.cs* file.</span></span> <span data-ttu-id="eef4e-125">Fügen Sie folgenden hervorgehobenen Code zur Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="eef4e-125">Add the following highlighted code to the class.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample1.cs?highlight=5,15,17,20)]

<span data-ttu-id="eef4e-126">Open *Enrollment.cs* und fügen Sie folgenden hervorgehobenen Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="eef4e-126">Open *Enrollment.cs* and add the following highlighted code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample2.cs?highlight=5,10)]

<span data-ttu-id="eef4e-127">Erstellen Sie die Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="eef4e-127">Build the solution.</span></span>

<span data-ttu-id="eef4e-128">Klicken Sie auf **Liste der Studenten** , und wählen Sie **bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="eef4e-128">Click **List of students** and select **Edit**.</span></span> <span data-ttu-id="eef4e-129">Wenn Sie versuchen, mehr als 50 Zeichen eingeben, wird eine Fehlermeldung angezeigt.</span><span class="sxs-lookup"><span data-stu-id="eef4e-129">If you attempt to enter more than 50 characters, an error message is displayed.</span></span>

![Anzeigen der Fehlermeldung](enhancing-data-validation/_static/image1.png)

<span data-ttu-id="eef4e-131">Wechseln Sie zurück zur Startseite.</span><span class="sxs-lookup"><span data-stu-id="eef4e-131">Go back to the home page.</span></span> <span data-ttu-id="eef4e-132">Klicken Sie auf **Liste der Registrierungen** , und wählen Sie **bearbeiten**.</span><span class="sxs-lookup"><span data-stu-id="eef4e-132">Click **List of enrollments** and select **Edit**.</span></span> <span data-ttu-id="eef4e-133">Versucht eine Grade-Eigenschaft über 4 bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-133">Attempt to provide a grade above 4.</span></span> <span data-ttu-id="eef4e-134">Sie erhalten diesen Fehler: *Das Feld muss auf Unternehmensniveau zwischen 0 und 4 liegen.*</span><span class="sxs-lookup"><span data-stu-id="eef4e-134">You will receive this error: *The field Grade must be between 0 and 4.*</span></span>

## <a name="add-metadata-classes"></a><span data-ttu-id="eef4e-135">Hinzufügen von Metadatenklassen</span><span class="sxs-lookup"><span data-stu-id="eef4e-135">Add metadata classes</span></span>

<span data-ttu-id="eef4e-136">Die Validierungsattribute direkt an die Modellklasse hinzufügen funktioniert, wenn Sie nicht, dass die erwarten zu ändernde Datenbank; aber wenn Ihre Datenbank geändert wird und Sie die Model-Klasse zu generieren müssen, verlieren alle Attribute Sie, die Sie auf die die Model-Klasse angewendet wurde.</span><span class="sxs-lookup"><span data-stu-id="eef4e-136">Adding the validation attributes directly to the model class works when you do not expect the database to change; however, if your database changes and you need to regenerate the model class, you will lose all of the attributes you had applied to the model class.</span></span> <span data-ttu-id="eef4e-137">Dieser Ansatz kann sehr ineffizient und anfällig für Verlust wichtiger Validierungsregeln sein.</span><span class="sxs-lookup"><span data-stu-id="eef4e-137">This approach can be very inefficient and prone to losing important validation rules.</span></span>

<span data-ttu-id="eef4e-138">Um dieses Problem zu vermeiden, können Sie eine Metadatenklasse hinzufügen, die die Attribute enthält.</span><span class="sxs-lookup"><span data-stu-id="eef4e-138">To avoid this problem, you can add a metadata class that contains the attributes.</span></span> <span data-ttu-id="eef4e-139">Wenn Sie die Model-Klasse, auf die Metadatenklasse zuordnen, werden diese Attribute für das Modell angewendet.</span><span class="sxs-lookup"><span data-stu-id="eef4e-139">When you associate the model class to the metadata class, those attributes are applied to the model.</span></span> <span data-ttu-id="eef4e-140">Bei diesem Ansatz kann die Model-Klasse erneut generiert werden, ohne zu verlieren alle Attribute, die auf die Metadatenklasse angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="eef4e-140">In this approach, the model class can be regenerated without losing all of the attributes that have been applied to the metadata class.</span></span>

<span data-ttu-id="eef4e-141">In der **Modelle** Ordner, fügen Sie eine Klasse, die mit dem Namen *Metadata.cs*.</span><span class="sxs-lookup"><span data-stu-id="eef4e-141">In the **Models** folder, add a class named *Metadata.cs*.</span></span>

<span data-ttu-id="eef4e-142">Ersetzen Sie den Code in *Metadata.cs* durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="eef4e-142">Replace the code in *Metadata.cs* with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample3.cs)]

<span data-ttu-id="eef4e-143">Diese Metadatenklassen enthalten alle die Validierungsattribute, die Sie zuvor für die Modellklassen angewendet wurden.</span><span class="sxs-lookup"><span data-stu-id="eef4e-143">These metadata classes contain all of the validation attributes that you had previously applied to the model classes.</span></span> <span data-ttu-id="eef4e-144">Die **Anzeige** Attribut zum Ändern des Werts, der für Symboltitel verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="eef4e-144">The **Display** attribute is used to change the value used for text labels.</span></span>

<span data-ttu-id="eef4e-145">Nun müssen Sie die ViewModel-Klassen mit den Metadatenklassen zuordnen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-145">Now, you must associate the model classes with the metadata classes.</span></span>

<span data-ttu-id="eef4e-146">In der **Modelle** Ordner, fügen Sie eine Klasse, die mit dem Namen *PartialClasses.cs*.</span><span class="sxs-lookup"><span data-stu-id="eef4e-146">In the **Models** folder, add a class named *PartialClasses.cs*.</span></span>

<span data-ttu-id="eef4e-147">Ersetzen Sie den Inhalt der Datei mit dem folgenden Code ein.</span><span class="sxs-lookup"><span data-stu-id="eef4e-147">Replace the contents of the file with the following code.</span></span>

[!code-csharp[Main](enhancing-data-validation/samples/sample4.cs)]

<span data-ttu-id="eef4e-148">Beachten Sie, die jede Klasse, als markiert ist eine `partial` -Klasse, und jedes entspricht dem Namen und Namespace wie die Klasse, die automatisch generiert wird.</span><span class="sxs-lookup"><span data-stu-id="eef4e-148">Notice that each class is marked as a `partial` class, and each matches the name and namespace as the class that is automatically generated.</span></span> <span data-ttu-id="eef4e-149">Das Metadatenattribut auf der partiellen Klasse anwenden, stellen Sie sicher, dass die Attribute für die datenvalidierung der automatisch generierte Klasse angewendet werden.</span><span class="sxs-lookup"><span data-stu-id="eef4e-149">By applying the metadata attribute to the partial class, you ensure that the data validation attributes will be applied to the automatically-generated class.</span></span> <span data-ttu-id="eef4e-150">Diese Attribute werden nicht verloren, wenn Sie die ViewModel-Klassen neu generieren, da das Attribut von Metadaten in partiellen Klassen angewendet wird, die nicht erneut generiert werden.</span><span class="sxs-lookup"><span data-stu-id="eef4e-150">These attributes will not be lost when you regenerate the model classes because the metadata attribute is applied in partial classes that are not regenerated.</span></span>

<span data-ttu-id="eef4e-151">Um die automatisch generierte Klassen neu generieren, öffnen Sie die *ContosoModel.edmx* Datei.</span><span class="sxs-lookup"><span data-stu-id="eef4e-151">To regenerate the automatically-generated classes, open the *ContosoModel.edmx* file.</span></span> <span data-ttu-id="eef4e-152">Wieder mit der rechten Maustaste auf die Entwurfsoberfläche, und wählen **Modell aus der Datenbank aktualisieren**.</span><span class="sxs-lookup"><span data-stu-id="eef4e-152">Once again, right-click on the design surface and select **Update Model from Database**.</span></span> <span data-ttu-id="eef4e-153">Auch wenn Sie die Datenbank nicht geändert haben, wird dieser Prozess die Klassen neu generieren.</span><span class="sxs-lookup"><span data-stu-id="eef4e-153">Even though you have not changed the database, this process will regenerate the classes.</span></span> <span data-ttu-id="eef4e-154">In der **aktualisieren** Registerkarte **Tabellen** und **Fertig stellen**.</span><span class="sxs-lookup"><span data-stu-id="eef4e-154">In the **Refresh** tab, select **Tables** and **Finish**.</span></span>

<span data-ttu-id="eef4e-155">Speichern Sie die *ContosoModel.edmx* Datei, die die Änderungen zu übernehmen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-155">Save the *ContosoModel.edmx* file to apply the changes.</span></span>

<span data-ttu-id="eef4e-156">Öffnen der *Student.cs* Datei oder das *Enrollment.cs* -Datei, und beachten Sie, dass die Validierung zuvor angewendeten Attribute nicht mehr in der Datei.</span><span class="sxs-lookup"><span data-stu-id="eef4e-156">Open the *Student.cs* file or the *Enrollment.cs* file, and notice that the data validation attributes you applied earlier are no longer in the file.</span></span> <span data-ttu-id="eef4e-157">Allerdings wird führen Sie die Anwendung aus, und beachten Sie, dass die Validierungsregeln immer noch angewendet werden, wenn Sie Daten eingeben.</span><span class="sxs-lookup"><span data-stu-id="eef4e-157">However, run the application, and notice that the validation rules are still applied when you enter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="eef4e-158">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="eef4e-158">Additional resources</span></span>

<span data-ttu-id="eef4e-159">Eine vollständige Liste von datenanmerkungen Überprüfung können Sie auf Eigenschaften und Klassen anwenden, finden Sie unter [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span><span class="sxs-lookup"><span data-stu-id="eef4e-159">For a full list of data validation annotations you can apply to properties and classes, see [System.ComponentModel.DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx).</span></span>

## <a name="next-steps"></a><span data-ttu-id="eef4e-160">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="eef4e-160">Next steps</span></span>

<span data-ttu-id="eef4e-161">In diesem Tutorial:</span><span class="sxs-lookup"><span data-stu-id="eef4e-161">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="eef4e-162">Hinzugefügt von datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="eef4e-162">Added data annotations</span></span>
> * <span data-ttu-id="eef4e-163">Hinzugefügte Metadatenklassen</span><span class="sxs-lookup"><span data-stu-id="eef4e-163">Added metadata classes</span></span>

<span data-ttu-id="eef4e-164">Wechseln Sie zum nächsten Tutorial erfahren, wie Sie Web-app und Datenbank in Azure veröffentlichen.</span><span class="sxs-lookup"><span data-stu-id="eef4e-164">Advance to the next tutorial to learn how to publish the web app and database to Azure.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="eef4e-165">Veröffentlichen in Azure</span><span class="sxs-lookup"><span data-stu-id="eef4e-165">Publish to Azure</span></span>](publish-to-azure.md)