---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
title: Erste Schritte mit ASP.NET 4.7 Web Forms und Visual Studio 2017 | Microsoft-Dokumentation
author: Erikre
description: Diese detaillierte lernprogrammreihe vermittelt Sie die Grundlagen der Erstellung einer ASP.NET Web Forms-Anwendung mithilfe von ASP.NET 4.7 und Microsoft Visual Studio
ms.author: riande
ms.date: 01/09/2019
ms.assetid: 9b96eaa1-8ef0-4338-a2e8-e0f970bfaf68
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/introduction-and-overview
msc.type: authoredcontent
ms.openlocfilehash: fb41ce72e9454d8d670a0b95234d2bc3f909f0ee
ms.sourcegitcommit: 42a8164b8aba21f322ffefacb92301bdfb4d3c2d
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2019
ms.locfileid: "54341552"
---
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a>Erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2017
====================

[Herunterladen der Wingtip Toys-Beispielprojekts (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) oder [E-Book (PDF) herunterladen](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)

Dieser tutorialreihe erfahren Sie, wie Sie eine ASP.NET Web Forms-Anwendung mit ASP.NET 4.5 und Microsoft Visual Studio 2017 erstellen. 

## <a name="introduction"></a>Einführung

Diese lernprogrammreihe führt Sie durch Erstellen einer ASP.NET Web Forms-Anwendung, die mithilfe von Visual Studio 2017 und ASP.NET 4.5. Erstellen Sie eine Anwendung namens **Wingtip Toys** – eine vereinfachte verkauften Artikel online Storefront-Website. Während der Reihe werden die neuen Features von ASP.NET 4.5 hervorgehoben.

### <a name="target-audience"></a>Zielgruppe

Entwickler, die noch nicht mit ASP.NET Web Forms sind die Zielgruppe für diese tutorialreihe.

Sie benötigen Grundkenntnisse in den folgenden Bereichen:

- Objektorientierte Programmierung (OOP) und Sprachen
- Webentwicklung (HTML, CSS, JavaScript)
- relationale Datenbanken
- Architektur mit N Ebenen

Um diese Bereiche zu überprüfen, sollten Sie in der Untersuchung von des folgenden Inhalts:

- [Erste Schritte mit Visual c#](https://msdn.microsoft.com/library/a72418yk.aspx)
- [Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)
- [Relationale Datenbank](http://en.wikipedia.org/wiki/Relational_database)
- [Multi-Tier-Architektur](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a>Anwendungsfeatures

Die ASP.NET Web Form-Features, die in dieser Serie vorgestellten umfassen:

- Das Webanwendungsprojekt (keine Website-Projekt)
- Web Forms
- Masterseiten, Konfiguration
- Bootstrap-Stil
- Entitätsframework Code First, LocalDB
- Request-Überprüfung
- Stark typisierte Datensteuerelemente
- Modellbindung
- Datenanmerkungen
- Wertanbieter
- SSL- und OAuth
- ASP.NET Identity, Konfiguration und -Autorisierung
- Unaufdringliche Validierung
- Routing
- ASP.NET – Fehlerbehandlung

### <a name="application-scenarios-and-tasks"></a>Anwendungsszenarien und Aufgaben

Tutorial-Reihe von Aufgaben:

- Erstellen, überprüfen und Ausführen eines neuen Projekts
- Erstellen einer Datenbankstruktur
- Initialisieren und Ausführen eines Seedings einer Datenbank
- Anpassen der Benutzeroberfläche mit Stilen, Grafiken und eine Masterseite
- Hinzufügen von Seiten und navigation
- Anzeigen von Menü-Details und Produktdaten
- Erstellen einen Einkaufswagen gelegt hat
- Hinzufügen von SSL und OAuth-Unterstützung
- Eine Zahlungsmethode hinzufügen
- Einschließlich der Rolle Administrator angehören und ein Benutzer der Anwendung
- Einschränken des Zugriffs auf bestimmte Seiten und Ordner
- Hochladen einer Datei in der Webanwendung
- Implementieren der eingabeüberprüfung
- Registrieren von Routen für die Webanwendung
- Implementieren der Fehlerbehandlung und Protokollierung von Anzeigefehlern

## <a name="overview"></a>Übersicht

Dieser tutorialreihe ist die Linie für eine Person mit Programmierkonzepten vertraut, aber noch nicht mit ASP.NET Web Forms. Wenn Sie bereits mit ASP.NET Web Forms vertraut sind, können diese weiterhin neue ASP.NET 4.5-Funktionen Informationen helfen. Leser, die nicht mit den Konzepten und ASP.NET Web Forms-Programmierung finden Sie unter der weitere Web Forms-Tutorials zur Verfügung gestellt, der [Einstieg](../../../index.md) Abschnitt auf der ASP.NET-Website.

Die ASP.NET 4.5 bereitgestellt, die in dieser tutorialreihe umfasst die folgenden Funktionen:

- Eine einfache Benutzeroberfläche zum Erstellen von Projekten, die bietet [Unterstützung für viele ASP.NET Frameworks](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC und Web-API).
- [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), Layout, Designs und Framework für reaktionsfähiges Design.
- [ASP.NET Identity](../../../../identity/index.md), ein neues ASP.NET-Mitgliedschaftssystem, die in allen ASP.NET-Framework-Versionen und funktioniert mit Web-Software als IIS-hosting funktioniert.
- [Entity Framework 6](https://msdn.microsoft.com/data/ef.aspx)

  Ein Update zu Entity Framework können Sie:
  - Abrufen und Bearbeiten von Daten als stark typisierte Objekte
  - Zugriff auf Daten asynchron
  - Behandeln von vorübergehenden Verbindungsfehlern
  - Log-SQL-Anweisungen

Eine vollständige Liste der ASP.NET 4.5-Funktionen, finden Sie unter [ASP.NET and Web Tools für Visual Studio 2013 Release Notes](../../../../visual-studio/overview/2013/release-notes.md).

### <a name="the-wingtip-toys-sample-application"></a>Die Wingtip Toys-beispielanwendung

Die folgenden Screenshots sind von der ASP.NET Web Forms-Anwendung, die Sie in diesem Tutorial erstellen. Wenn Sie die Anwendung in Visual Studio ausführen, wird die folgende Webseite-Startseite angezeigt.

![Wingtip Toys - Standardseite](introduction-and-overview/_static/image1.png)

Sie können als neuer Benutzer registrieren, oder melden Sie sich als einen vorhandenen Benutzer. Der oberen Navigationsleiste verfügt über Links zu Produktkategorien und ihre Produkte aus der Datenbank.

Bei Auswahl von **Produkte**, alle verfügbaren Produkte werden angezeigt. 

![Wingtip Toys - Produkte](introduction-and-overview/_static/image2.png)

Wenn Sie ein bestimmtes Produkt auswählen, werden die Produktdetails angezeigt.


![Wingtip Toys - Produktinformationen](introduction-and-overview/_static/image3.png)

Sie können als Benutzer registrieren und melden Sie sich mit Standardfunktionen für Web Forms-Vorlage. In diesem Tutorial wird die Verwendung mit einem vorhandenen Gmail-Konto anmelden. Darüber hinaus können Sie als Administrator hinzufügen und Entfernen von Produkten aus der Datenbank anmelden.

![Wingtip Toys - Anmeldung](introduction-and-overview/_static/image4.png)

Nachdem Sie sich als Benutzer angemeldet haben, können Sie Produkte auf den Einkaufswagen und Diskussion zum Bezahlvorgang mit PayPal hinzufügen. Die beispielanwendung dient, in PayPals-Entwickler-Sandbox arbeiten. Keine Transaktion tatsächlich Zahlung erfolgt.

![Wingtip Toys - Warenkorb](introduction-and-overview/_static/image5.png)

PayPal bestätigt Ihre Informationen zu Konto, Reihenfolge und Zahlung.

![Wingtip Toys - PayPal](introduction-and-overview/_static/image6.png)

Sie können nach der Rückkehr aus PayPal, überprüfen und Abschließen der Bestellung.

![Wingtip Toys - Order-Überprüfung](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a>Vorraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass die folgende Software auf Ihrem Computer installiert ist:

- [Microsoft Visual Studio 2017 "oder" Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).

.NET Framework wird automatisch installiert.

Dieser tutorialreihe wird Microsoft Visual Studio Community 2017 verwendet. Sie können entweder, dass oder Microsoft Visual Studio 2017 zum Abschließen dieser tutorialreihe.

Beachten Sie Folgendes im Zusammenhang mit Visual Studio:

* Microsoft Visual Studio 2017 und Microsoft Visual Studio Community 2017 als bezeichnet *Visual Studio* in dieser tutorialreihe.

* Visual Studio 2017 installiert ist neben älteren Versionen bereits installiert. In früheren Versionen erstellten Websites können in Visual Studio 2017 geöffnet werden und weiterhin in früheren Versionen öffnen.

* Zum ersten Mal Sie Visual Studio gestartet, es wird davon ausgegangen, dass Sie ausgewählt haben die *Webentwicklung* Einstellungen. Weitere Informationen finden Sie unter [Vorgehensweise: Wählen Sie die Umgebungseinstellungen für die Webentwicklung](https://msdn.microsoft.com/library/ff521558.aspx).

Nach der Installation der erforderlichen Komponenten können Sie damit beginnen, erstellen das Webprojekt, das in dieser tutorialreihe angezeigt.

## <a name="download-the-sample-application"></a>Herunterladen der beispielanwendung

 Sie können das vollständige Beispiel Applicatiion an jederzeit und überall auf der MSDN Samples-Website herunterladen:

[Erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (c#) 

 Dieser Download umfasst die folgenden Elemente:

- Die beispielanwendung in der *WingtipToys* Ordner.
- Die Ressourcen, die zum Erstellen der beispielanwendung in der *WingtipToys-Assets* Ordner in der *WingtipToys* Ordner.

Der Download ist eine *ZIP* Datei. Anzeigen des abgeschlossenen Projekts, das dieser tutorialreihe erstellt suchen und Auswählen der *C#* Ordner in der ZIP-Datei. Speichern Sie die C# Ordner in den Ordner, die Sie verwenden, um mit Visual Studio-Projekten zu arbeiten. Standardmäßig ist der Ordner von Visual Studio 2017-Projekte:

<strong>C:\Users&#92;</strong><strong><em>&lt;Benutzername&gt;</em></strong><strong>\source\repos</strong>

Benennen Sie die ***c#*** Ordner ***WingtipToys***.

> [!NOTE]
> Wenn Sie bereits einen Ordner namens haben *WingtipToys* im Projektordner, bevor Sie Sie umbenennen, vorhandenen Ordner vorübergehend Umbenennen der *c#* Ordner *WingtipToys*.

Öffnen Sie zum Ausführen des abgeschlossenen Projekts der *WingtipToys* Ordner und doppelklicken Sie auf die *WingtipToys.sln* Datei. Visual Studio 2017 wird das Projekt geöffnet. Als Nächstes mit der rechten Maustaste die *"default.aspx"* Datei **Projektmappen-Explorer** , und wählen Sie **In Browser anzeigen**.

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a>Machen Sie ein ASP.NET Web Forms-Quiz, Inhalte zu überprüfen.

Nach Abschluss der tutorialreihe können ein Quiz können Sie Ihr Wissen testen und wichtige Konzepte zu verstärken. Jeder Frage enthält eine Erläuterung und Links zu weiteren Anleitungen.

 * [ASP.NET Web Forms-Quiz](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a>Tutorial Support und Kommentare

Für Fragen und Kommentare, verwenden Sie den f und A-Abschnitt enthalten die [erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) Beispielseite.

Kommentare zu dieser tutorialreihe sind Willkommen. Wenn dieser tutorialreihe aktualisiert wird, wird versucht, Korrekturen oder Vorschläge für Verbesserungen zu berücksichtigen.

Wenn ein Fehler auftritt, die entsprechende Fehlermeldungen können verwirrend sein, ohne gute Erklärung dazu, wie Sie diesen Fehler beheben. Um Hilfe zu erhalten, sehen Sie sich die [ASP.NET-Foren](https://forums.asp.net/). Eine weitere gute Quelle ist der f und A-Abschnitt in der [erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) Beispielseite. 

> [!div class="step-by-step"]
> [Nächste](create-the-project.md)
