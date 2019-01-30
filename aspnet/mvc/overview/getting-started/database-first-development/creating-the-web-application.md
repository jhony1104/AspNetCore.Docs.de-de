---
uid: mvc/overview/getting-started/database-first-development/creating-the-web-application
title: 'Tutorial: Erstellen Sie die der Webanwendung und Datenmodelle für EF Database First mit ASP.NET MVC'
description: Dieses Tutorial konzentriert sich auf die Webanwendung erstellen und das Generieren von Datenmodelle auf Grundlage von Datenbanktabellen.
author: Rick-Anderson
ms.author: riande
ms.date: 01/28/2019
ms.topic: tutorial
ms.assetid: bc8f2bd5-ff57-4dcd-8418-a5bd517d8953
msc.legacyurl: /mvc/overview/getting-started/database-first-development/creating-the-web-application
msc.type: authoredcontent
ms.openlocfilehash: dced55386c3f810e406c5c2b3f0071b45e3b2dbd
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236366"
---
# <a name="tutorial-create-the-the-web-application-and-data-models-for-ef-database-first-with-aspnet-mvc"></a>Tutorial: Erstellen Sie die der Webanwendung und Datenmodelle für EF Database First mit ASP.NET MVC

 Verwenden MVC, Entity Framework und ASP.NET-Gerüstbau, können Sie eine Webanwendung erstellen, die eine Schnittstelle für eine vorhandene Datenbank bereitstellt. Dieser tutorialreihe erfahren Sie, wie Sie automatisch generierter Code, der ermöglicht Benutzern das anzeigen, bearbeiten, erstellen und Löschen von Daten, die in einer Datenbanktabelle gespeichert. Der generierte Code entspricht die Spalten in der Datenbanktabelle.

Dieses Tutorial konzentriert sich auf die Webanwendung erstellen und das Generieren von Datenmodelle auf Grundlage von Datenbanktabellen.

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellen einer ASP.NET-Web-App
> * Die Modelle generieren

## <a name="prerequisites"></a>Vorraussetzungen

* [Erste Schritte mit Entity Framework 6 Database First anhand von MVC 5](setting-up-database.md)

## <a name="create-an-aspnet-web-app"></a>Erstellen einer ASP.NET-Web-App

Erstellen Sie ein neues Projekt in Visual Studio in einer neuen Projektmappe oder der gleichen Projektmappe wie das Projekt, und wählen Sie die **ASP.NET-Webanwendung** Vorlage. Nennen Sie das Projekt **ContosoSite**.

![Projekt erstellen](creating-the-web-application/_static/image1.png)

Klicken Sie auf **OK**.

Wählen Sie im Fenster Neues ASP.NET-Projekt die **MVC** Vorlage. Sie können löschen, die **in der Cloud hosten** option jetzt, da Sie später die Anwendung in der Cloud bereitstellen. Klicken Sie auf **OK** zum Erstellen der Anwendung.

Das Projekt wird mit der Standarddateien und Ordner erstellt.

In diesem Tutorial verwenden Sie Entity Framework 6. Sie können die Version von Entity Framework Vergewissern Sie sich in Ihrem Projekt über das Fenster "NuGet-Pakete verwalten". Aktualisieren Sie Ihre Version von Entity Framework, bei Bedarf.

![Version anzeigen](creating-the-web-application/_static/image3.png)

## <a name="generate-the-models"></a>Die Modelle generieren

Erstellen Sie jetzt Entity Framework-Modellen aus den Datenbanktabellen. Diese Modelle sind Klassen, die Sie verwenden werden, um mit den Daten arbeiten. Jedes Modell entspricht eine Tabelle in der Datenbank und enthält Eigenschaften, die die Spalten in der Tabelle entsprechen.

Mit der rechten Maustaste die **Modelle** Ordner, und wählen **hinzufügen** und **neues Element**.

Wählen Sie im Fenster "Neues Element hinzufügen", **Daten** im linken Bereich und **ADO.NET Entity Data Model** aus den Optionen im mittleren Bereich. Nennen Sie die neue Modelldatei **ContosoModel**.

Klicken Sie auf **Hinzufügen**.

Wählen Sie in der Assistent für Entity Data Model **EF Designer aus Datenbank**.

Klicken Sie auf **Weiter**.

Wenn Sie Verbindungen mit der Datenbank in Ihrer Entwicklungsumgebung definiert haben, sehen Sie diese Verbindungen vorab ausgewählt. Sie möchten jedoch eine neue Verbindung mit der Datenbank zu erstellen, die Sie im ersten Teil dieses Tutorials erstellt haben. Klicken Sie auf die **neue Verbindung** Schaltfläche.

Geben Sie im Fenster Eigenschaften der Verbindung den Namen des lokalen Servers, in die Datenbank erstellt wurde (in diesem Fall **(Localdb) \Projects13**). Wählen Sie nachdem Sie den Namen des Servers haben die ContosoUniversityData aus den verfügbaren Datenbanken aus.

![Set-Verbindungseigenschaften](creating-the-web-application/_static/image8.png)

Klicken Sie auf **OK**.

Die richtige Verbindungseigenschaften werden nun angezeigt. Sie können den Standardnamen für die Verbindung in der Datei "Web.config" verwenden.

Klicken Sie auf **Weiter**.

Wählen Sie die neueste Version von Entity Framework.

Klicken Sie auf **Weiter**.

Wählen Sie **Tabellen** zum Generieren von Modellen für alle drei Tabellen.

Klicken Sie auf **Fertig stellen**.

Wenn eine sicherheitswarnung angezeigt wird, wählen Sie **OK** zum Fortsetzen der Ausführung der Vorlage.

Die Modelle aus Tabellen der Datenbank generiert wird, und ein Diagramm wird mit den Eigenschaften und Beziehungen zwischen den Tabellen angezeigt.

![Diagramm des Modells](creating-the-web-application/_static/image11.png)

Der Ordner "Models" enthält jetzt viele neue Dateien, die im Zusammenhang mit der die Modelle, die aus der Datenbank generiert wurden.

Die **ContosoModel.Context.cs** -Datei enthält eine abgeleitete Klasse die **"DbContext"** Klasse, und stellt eine Eigenschaft für jede Modellklasse, die einer Datenbanktabelle entspricht. Die **Course.cs**, **Enrollment.cs**, und **Student.cs** Dateien enthalten die Modellklassen, die in den Datenbanken Tabellen darstellen. Sie werden sowohl der Context-Klasse und die Modellklassen verwenden, bei der Arbeit mit Gerüstbau.

Erstellen Sie bevor Sie mit diesem Tutorial Fortfahren das Projekt ein. Im nächsten Abschnitt generieren Sie Code auf Grundlage der Datenmodelle, aber dieser Abschnitt funktioniert nicht, wenn das Projekt noch nicht erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Erstellt eine ASP.NET Web-app
> * Generiert die Modelle

Die nächsten Tutorial erfahren, wie zum Erstellen generieren Code basierend auf der Datenmodelle.
> [!div class="nextstepaction"]
> [Generieren von Sichten](generating-views.md)