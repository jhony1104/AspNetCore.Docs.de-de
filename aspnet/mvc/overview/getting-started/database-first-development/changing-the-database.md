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
# <a name="tutorial-change-the-database-for-ef-database-first-with-aspnet-mvc-app"></a>Tutorial: Ändern Sie die Datenbank für EF Database First mit ASP.NET MVC-app

Verwenden MVC, Entity Framework und ASP.NET-Gerüstbau, können Sie eine Webanwendung erstellen, die eine Schnittstelle für eine vorhandene Datenbank bereitstellt. Dieser tutorialreihe erfahren Sie, wie Sie automatisch generierter Code, der ermöglicht Benutzern das anzeigen, bearbeiten, erstellen und Löschen von Daten, die in einer Datenbanktabelle gespeichert. Der generierte Code entspricht die Spalten in der Datenbanktabelle.

Dieses Tutorial konzentriert sich auf eine Aktualisierung vornehmen, um die Struktur der Datenbank und das Weitergeben von dieser Änderung in der Webanwendung.

In diesem Tutorial:

> [!div class="checklist"]
> * Hinzufügen einer Spalte
> * Fügen Sie die Eigenschaft, die den Ansichten

## <a name="prerequisites"></a>Vorraussetzungen

* [Generieren von Sichten](generating-views.md)

## <a name="add-a-column"></a>Hinzufügen einer Spalte

Wenn Sie die Struktur einer Tabelle in der Datenbank aktualisieren, müssen Sie sicherstellen, dass die Änderung an das Datenmodell, Ansichten und Controller weitergegeben wird.

In diesem Tutorial werden Sie eine neue Spalte der Tabelle "Student", um den zweiten Vornamen der Student aufzuzeichnen hinzufügen. Um diese Spalte hinzuzufügen, öffnen Sie das Datenbankprojekt, und öffnen Sie die Datei Student.sql. Über Designer "oder" T-SQL-Code, fügen Sie eine Spalte, die mit dem Namen **MiddleName** , ein NVARCHAR(50) und NULL-Werte zulässt.

Ihr Datenbankprojekt (oder F5) starten, um bereitstellen Sie diese Änderung in Ihre lokale Datenbank. Das neue Feld wird in der Tabelle hinzugefügt. Wenn Sie es in der Objekt-Explorer von SQL Server nicht angezeigt werden, klicken Sie auf die Schaltfläche "Aktualisieren", klicken Sie im Bereich.

![neue Spalte anzeigen](changing-the-database/_static/image2.png)

Die neue Spalte, die in der Tabelle der Datenbank vorhanden ist, aber derzeit nicht in der Modellklasse für Daten vorhanden. Sie müssen das Modell, um Ihre neue Spalte enthalten aktualisieren. In der **Modelle** Ordner die **ContosoModel.edmx** Datei in das Modelldiagramm anzuzeigen. Beachten Sie, dass das Modell "Student" nicht die MiddleName-Eigenschaft enthält. Mit der rechten Maustaste auf die Entwurfsoberfläche, und wählen **Modell aus der Datenbank aktualisieren**.

Wählen Sie in der Update-Assistenten die **aktualisieren** Registerkarte, und wählen Sie dann **Tabellen** > **Dbo** > **für Schüler und Studenten**. Klicken Sie auf **Fertig stellen**.

Nachdem der Aktualisierungsvorgang abgeschlossen ist, enthält das Datenbankdiagramm neuen **MiddleName** Eigenschaft. Speichern Sie die **ContosoModel.edmx** Datei. Sie müssen diese Datei für die neue Eigenschaft an weitergegeben speichern die **Student.cs** Klasse. Sie haben jetzt die Datenbank und das Modell aktualisiert.

Erstellen Sie die Projektmappe.

## <a name="add-the-property-to-the-views"></a>Fügen Sie die Eigenschaft, die den Ansichten

Die Ansichten enthalten noch leider nicht die neue Eigenschaft. Aktualisieren Sie die Ansichten haben Sie zwei Optionen: können Sie entweder erneut generieren die Ansichten von wieder hinzufügen Gerüst für die Klasse "Student", oder Sie können die neue Eigenschaft manuell hinzufügen, um Ihre vorhandenen Ansichten. In diesem Tutorial fügen Sie das Gerüst erneut aus, da Sie nicht benutzerdefinierten Änderungen an den automatisch generierten Ansichten vorgenommen haben. Sie sollten erwägen, die Eigenschaft manuell hinzufügen, wenn Sie Änderungen, in den Ansichten vorgenommen haben und nicht, diese Änderungen verloren möchten.

Um sicherzustellen, die Ansichten werden neu erstellt, löschen Sie die **Schüler/Studenten** Ordner unter **Ansichten**, und Löschen der **StudentsController**. Klicken Sie dann mit der rechten Maustaste die **Controller** Ordner und Hinzufügen der Gerüstbau für die **für Schüler und Studenten** Modell. Nennen Sie den Controller wieder **StudentsController**. Wählen Sie **Hinzufügen** aus.

Erstellen Sie die Projektmappe erneut. Die Ansichten enthalten jetzt die MiddleName-Eigenschaft.

![Zweiter Vorname anzeigen](changing-the-database/_static/image5.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Eine Spalte hinzugefügt
> * Die Eigenschaft, die Ansichten hinzugefügt wurden.

Fahren Sie fort mit dem nächsten Tutorial erfahren, wie die Ansicht zum Anzeigen von Details zu einem Student-Datensatz ändern.
> [!div class="nextstepaction"]
> [Anpassen einer Ansicht](customizing-a-view.md)