---
uid: mvc/overview/getting-started/database-first-development/generating-views
title: 'Tutorial: Generieren von Ansichten für EF Database First mit ASP.NET MVC-app'
description: Dieses Tutorial konzentriert sich auf ASP.NET-Gerüstbau verwenden, um die Controller und Ansichten zu generieren.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: 669367cf-8e30-4eb6-821d-10a7d9bb906c
msc.legacyurl: /mvc/overview/getting-started/database-first-development/generating-views
msc.type: authoredcontent
ms.openlocfilehash: 7a56c0f9197a99427bcde6103ebc69d245e8ce63
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236418"
---
# <a name="tutorial-generate-views-for-ef-database-first-with-aspnet-mvc-app"></a>Tutorial: Generieren von Ansichten für EF Database First mit ASP.NET MVC-app

Verwenden MVC, Entity Framework und ASP.NET-Gerüstbau, können Sie eine Webanwendung erstellen, die eine Schnittstelle für eine vorhandene Datenbank bereitstellt. Dieser tutorialreihe erfahren Sie, wie Sie automatisch generierter Code, der ermöglicht Benutzern das anzeigen, bearbeiten, erstellen und Löschen von Daten, die in einer Datenbanktabelle gespeichert. Der generierte Code entspricht die Spalten in der Datenbanktabelle.

Dieses Tutorial konzentriert sich auf ASP.NET-Gerüstbau verwenden, um die Controller und Ansichten zu generieren.

In diesem Tutorial:

> [!div class="checklist"]
> * Gerüst hinzufügen
> * Hinzufügen von Links zu den neuen Ansichten
> * Anzeigen von für Schüler und Studenten-Ansichten
> * Anzeigen von Ansichten der Registrierung

## <a name="prerequisite"></a>Vorbereitungsmaßnahme

* [Erstellen Sie die Anwendungs- und Datenmodelle](creating-the-web-application.md)

## <a name="add-scaffold"></a>Gerüst hinzufügen

Sie können zum Generieren von Code, die standard-Vorgänge für die Modellklassen bereitstellt. Sie können den Code durch Hinzufügen eines Elements Gerüst hinzufügen. Es gibt viele Optionen für den Typ der Gerüstbau, die Sie hinzufügen können. In diesem Tutorial enthält das Gerüst, einen Controller und Ansichten, die die Modelle "Student" und der Registrierung entsprechen, die Sie im vorherigen Abschnitt erstellt haben.

Um Konsistenz in Ihrem Projekt zu gewährleisten, fügen Sie den neuen Controller hinzu, die vorhandene **Controller** Ordner. Mit der rechten Maustaste die **Controller** Ordner, und wählen **hinzufügen** > **neues Gerüstelement**.

Wählen Sie die **MVC 5-Controller mit Ansichten unter Verwendung von Entity Framework** Option. Diese Option wird die Controller und Ansichten zum Aktualisieren, löschen, erstellen und Anzeigen der Daten in Ihrem Modell generiert.

![Hinzufügen der Mvc-controller](generating-views/_static/image2.png)

Wählen Sie **für Schüler und Studenten (ContosoSite.Models)** für die Model-Klasse, und wählen die **ContosoUniversityDataEntities (ContosoSite.Models)** für der Context-Klasse. Behalten Sie den Namen der Controller als **StudentsController**.

Klicken Sie auf **Hinzufügen**.

Wenn Sie eine Fehlermeldung erhalten, kann es sein, da Sie nicht auf das Projekt im vorherigen Abschnitt erstellt haben. Wenn dies der Fall ist, versuchen Sie es beim Erstellen des Projekts, und klicken Sie dann das erstellte Element erneut hinzufügen.

Nachdem der Prozess der codegenerierung abgeschlossen ist, sehen Sie einen neuen Controller und Ansichten in Ihrem Projekts die **Controller** und **Ansichten** > **Schüler/Studenten** Ordner .


Führen Sie die gleichen Schritte erneut aus, aber fügen Sie ein Gerüst für die **Registrierung** Klasse. Wenn Sie fertig sind, müssen Sie eine **EnrollmentsController.cs** Datei sowie einen Ordner unter **Ansichten** mit dem Namen **Registrierungen** mit den Ansichten erstellen "," Delete "," Details "," Bearbeiten "und" Index.

## <a name="add-links-to-new-views"></a>Hinzufügen von Links zu den neuen Ansichten

Um für die Navigation zu Ihrer neuen Ansichten vereinfachen, können Sie eine Reihe von Links für Schüler/Studenten und Registrierungen, die den Index Ansichten hinzufügen. Öffnen Sie die Datei unter **Ansichten** > **Home** > *"Index.cshtml"*, dies ist die Startseite für Ihre Website. Fügen Sie den folgenden Code unter den Jumbotron hinzu.

[!code-cshtml[Main](generating-views/samples/sample1.cshtml)]

Für die ActionLink-Methode ist der erste Parameter der im Link anzuzeigende Text. Der zweite Parameter ist die Aktion aus, und der dritte Parameter ist der Name des Controllers. Zeigt beispielsweise der erste Link zu der Aktion Index StudentsController an. Der tatsächlichen Link wird unter folgenden Werten erstellt. Die erste Verknüpfung letztendlich gelangen Benutzer zum die **"Index.cshtml"** Datei innerhalb der **Ansichten/Schüler/Studenten** Ordner.

## <a name="display-student-views"></a>Anzeigen von für Schüler und Studenten-Ansichten

Sie werden überprüfen, dass der Code ordnungsgemäß zu Ihrem Projekt hinzugefügt wird eine Liste der Studenten angezeigt, und Benutzern ermöglicht das Bearbeiten, erstellen oder löschen Sie die Studentendatensätze für Schüler und in der Datenbank.

Mit der rechten Maustaste die **Ansichten** > **Startseite** > *"Index.cshtml"* , und wählen Sie **in Browser anzeigen**. Wählen Sie auf der Startseite der Anwendung **Liste der Studenten**.

![](generating-views/_static/image6.png)

Auf der **Index** Seite, beachten Sie, dass die Liste der Schüler/Studenten und Links zum Ändern dieser Daten. Wählen Sie die **neu erstellen** verknüpfen, und geben Sie einige Werte für einen neuen Studenten. Klicken Sie auf **erstellen**, und beachten Sie, dass die neue Studenten zur Liste hinzugefügt wird.

Auf der **Index** Seite die **bearbeiten** verknüpfen, und einige der Werte für Schüler/Student ändern. Klicken Sie auf **speichern**, und beachten Sie, dass der Student-Datensatz geändert wurde.

Wählen Sie abschließend die **löschen** verknüpfen, und bestätigen Sie den Datensatz zu löschen, indem Sie auf die **löschen** Schaltfläche.

Ohne Code schreiben zu müssen, haben Sie die Sichten hinzugefügt, die allgemeine Vorgänge auf die Daten in der Tabelle "Student" ausführen.

Ihnen möglicherweise aufgefallen, dass das die textbezeichnung für ein Feld auf die Datenbankeigenschaft (z. B. **"LastName"**) dem ist nicht unbedingt auf der Webseite anzeigen möchten. Z. B. möglicherweise bevorzugen Sie die Bezeichnung sein **Nachname**. Sie werden später im Tutorial dieses Anzeigeproblems behoben.

## <a name="display-enrollment-views"></a>Anzeigen von Ansichten der Registrierung

Ihre Datenbank enthält eine 1: n Beziehung zwischen den Tabellen "Student" und Registrierung, und eine 1: n Beziehung zwischen den Tabellen Kurs- und Registrierungsentitäten. Die Ansichten für die Registrierung zu diese Beziehungen verarbeiten. Navigieren Sie zur Startseite für Ihre Website, und wählen die **Liste der Registrierungen** Link und klicken Sie dann die **neu erstellen** Link.

Die Ansicht zeigt ein Formular zum Erstellen eines neuen Eintrags für die Registrierung. Beachten Sie insbesondere die, dass das Formular enthält eine **CourseID** Dropdown-Liste und eine **"StudentID"** Dropdown-Liste. Beide werden mit Werten aus der verknüpften Tabellen aufgefüllt.

Darüber hinaus wird die Überprüfung der bereitgestellten Werte automatisch basierend auf dem Datentyp des Felds angewendet. **Professionelle** erfordert eine Zahl ist, eine Fehlermeldung angezeigt wird, wenn Sie versuchen, einen nicht kompatiblen Wert angeben: *Das Feld "Grade" muss eine Zahl sein.*

Sie haben sichergestellt, dass die automatisch generierte Ansichten für die Arbeit mit den Daten in der Datenbank ermöglichen. Im nächsten Tutorial dieser Reihe Sie die Datenbank aktualisieren, und nehmen die entsprechenden Änderungen in der Webanwendung.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Hinzugefügten scaffold
> * Links zu neuen Funktionen hinzugefügt
> * Ansichten der angezeigten für Schüler und Studenten
> * Angezeigte Registrierung-Ansichten

Fahren Sie fort mit dem nächsten Tutorial erfahren, wie die Datenbank ändern.
> [!div class="nextstepaction"]
> [Ändern Sie die Datenbank](changing-the-database.md)