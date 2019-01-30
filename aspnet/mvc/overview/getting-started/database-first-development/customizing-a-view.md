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
# <a name="tutorial-customize-view-for-ef-database-first-with-aspnet-mvc-app"></a>Tutorial: Anpassen der Ansicht für EF Database First mit ASP.NET MVC-app

Verwenden MVC, Entity Framework und ASP.NET-Gerüstbau, können Sie eine Webanwendung erstellen, die eine Schnittstelle für eine vorhandene Datenbank bereitstellt. Dieser tutorialreihe erfahren Sie, wie Sie automatisch generierter Code, der ermöglicht Benutzern das anzeigen, bearbeiten, erstellen und Löschen von Daten, die in einer Datenbanktabelle gespeichert. Der generierte Code entspricht die Spalten in der Datenbanktabelle.

Dieses Tutorial konzentriert sich auf die automatisch generierten Ansichten zur Verbesserung der Präsentation zu ändern.

In diesem Tutorial:

> [!div class="checklist"]
> * Hinzufügen von Kursen zur Detailseite für Schüler und Studenten
> * Vergewissern Sie sich, dass die Kurse zur Seite hinzugefügt werden

## <a name="prerequisites"></a>Vorraussetzungen

* [Ändern Sie die Datenbank](changing-the-database.md)

## <a name="add-courses-to-student-detail"></a>Kurse, Detail zum Kursteilnehmer hinzufügen

Der generierte Code bietet einen guten Ausgangspunkt für Ihre Anwendung bietet jedoch nicht unbedingt alle Funktionen, die Sie in Ihrer Anwendung benötigen. Sie können den Code, um die bestimmten Anforderungen Ihrer Anwendung anpassen. Die Anwendung wird derzeit nicht die registrierten Kurse für den ausgewählten Studenten angezeigt. In diesem Abschnitt fügen Sie die registrierten Kurse für jeden Kursteilnehmer auf die **Details** Ansicht für den Studenten.

Open **Ansichten** > **Schüler/Studenten** > *Details.cshtml*. Unterhalb der letzten &lt;/DL&gt; -Tag, aber vor dem schließenden &lt;/div&gt; markieren, fügen Sie den folgenden Code hinzu.

[!code-cshtml[Main](customizing-a-view/samples/sample1.cshtml)]

Dieser Code erstellt eine Tabelle, die eine Zeile für jeden Datensatz in der Enrollment-Tabelle für den ausgewählten Studenten angezeigt. Die **Anzeige** -Methode rendert die HTML für das Objekt (ModelItem), die den Ausdruck darstellt. Verwenden Sie die Anzeige-Methode (anstatt einfach den Wert der Eigenschaft im Code) um sicherzustellen, dass der Wert ist ordnungsgemäß basierend auf den Typ und die Vorlage für diesen Typ formatiert. In diesem Beispiel jeder Ausdruck gibt eine einzelne Eigenschaft des aktuellen Datensatzes in der Schleife und die Werte sind primitive Typen, die als Text gerendert werden.

## <a name="confirm-courses-are-added"></a>Bestätigen Sie, dass die Kurse hinzugefügt werden

Führen Sie die Projektmappe aus. Klicken Sie auf **Liste der Studenten** , und wählen Sie **Details** für eines der Schüler/Studenten. Sie sehen, dass die registrierten Kurse in der Ansicht hinzugefügt wurden.

!["Student" mit Registrierung](customizing-a-view/_static/image1.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial:

> [!div class="checklist"]
> * Hinzugefügte Kurse an, um die Detailseite für Schüler und Studenten
> * Bestätigt, dass die Kurse zur Seite hinzugefügt werden

Fahren Sie fort mit dem nächsten Tutorial erfahren Sie, wie Hinzufügen von datenanmerkungen, geben Sie die überprüfungsanforderungen zu erfüllen und Formatierung anzeigen.
> [!div class="nextstepaction"]
> [Optimieren der datenüberprüfung](enhancing-data-validation.md)
