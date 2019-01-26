---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Lesen Sie verwandter Daten mit EF in einer ASP.NET MVC-app'
description: In diesem Tutorial werden Sie lesen und Anzeigen von verknüpften Daten, d. h. Daten, die Entity Framework in Navigationseigenschaften lädt.
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: 18cdd896-8ed9-4547-b143-114711e3eafb
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: e4f23dbdb604dd513e42b7b8ff7b727245b9b637
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889963"
---
# <a name="tutorial-read-related-data-with-ef-in-an-aspnet-mvc-app"></a>Tutorial: Lesen Sie verwandter Daten mit EF in einer ASP.NET MVC-app

Im vorherigen Tutorial haben Sie das Datenmodell "School". In diesem Tutorial werden Sie lesen und Anzeigen von verknüpften Daten, d. h. Daten, die Entity Framework in Navigationseigenschaften lädt.

Die folgenden Abbildungen zeigen die Seiten, mit den Sie arbeiten werden.

![](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructors_index_page_with_instructor_and_course_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Erfahren Sie, wie zum Laden von verknüpfter Daten
> * Erstellen einer Kursseite
> * Erstellen einer dozentenseite

## <a name="prerequisites"></a>Vorraussetzungen

* [Erstellen eines komplexeren Datenmodells](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)

## <a name="learn-how-to-load-related-data"></a>Erfahren Sie, wie zum Laden von verknüpfter Daten

Es gibt mehrere Möglichkeiten, Entity Framework zugehörige Daten in die Navigationseigenschaften einer Entität laden kann:

- *Lazy Loading (verzögertes Laden)*. Wenn die Entität zuerst gelesen wird, werden verwandte Daten nicht abgerufen. Wenn Sie jedoch zum ersten Mal versuchen, auf eine Navigationseigenschaft zuzugreifen, werden die für diese Navigationseigenschaft erforderlichen Daten automatisch abgerufen. Dies führt zu mehreren Abfragen, die an die Datenbank gesendet – eine für die Entität selbst und eine jedes Mal, die Daten für die Entität beziehen abgerufen werden muss. Die `DbContext` Klasse Lazy Load sind standardmäßig aktiviert.

    ![Lazy_loading_example](https://asp.net/media/2577850/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Lazy_loading_example_2c44eabb-5fd3-485a-837d-8e3d053f2c0c.png)
- *Eager Loading (vorzeitiges Laden)*. Wenn die Entität gelesen wird, werden ihre verwandten Daten mit ihr abgerufen. Dies führt normalerweise zu einer einzelnen Joinabfrage, die alle Daten abruft, die erforderlich sind. Sie geben eager Loading mit der `Include` Methode.

    ![Eager_loading_example](https://asp.net/media/2577856/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Eager_loading_example_33f907ff-f0b0-4057-8e75-05a8cacac807.png)
- *Explizites Laden*. Dies ist ähnlich wie beim verzögerten Laden, mit dem Unterschied, dass Sie explizit im Code die verwandten Daten abgerufen werden; Dies erfolgt nicht automatisch beim Zugriff auf eine Navigationseigenschaft. Sie verwandte Daten manuell laden durch Abrufen der Objekt-Status-Manager-Eintrag für eine Entität und das Aufrufen der [Collection.Load](https://msdn.microsoft.com/library/gg696220(v=vs.103).aspx) -Methode für Sammlungen oder [Reference.Load](https://msdn.microsoft.com/library/gg679166(v=vs.103).aspx) Methode für die Eigenschaften, die enthalten eine einzelne Entität. (Im folgenden Beispiel, wenn Sie die Administrator-Navigationseigenschaft laden möchten ersetzen `Collection(x => x.Courses)` mit `Reference(x => x.Administrator)`.) In der Regel verwenden Sie explizite Laden nur, wenn Sie lazy loading-aktiviert haben.

    ![Explicit_loading_example](https://asp.net/media/2577862/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Explicit_loading_example_79d8c368-6d82-426f-be9a-2b443644ab15.png)

Da sie sofort der Abrufen von Eigenschaftswerten nicht lazy Loading und explizites Laden werden auch beide genannt *verzögertes Laden*.

### <a name="performance-considerations"></a>Überlegungen zur Leistung

Wenn Sie wissen, dass Sie für jede abgerufene Entität verwandte Daten benötigen, bietet Eager Loading häufig die beste Leistung, da eine einzelne Abfrage, die an die Datenbank gesendet wird, in der Regel effizienter ist als separate Abfragen für jede abgerufene Entität. Angenommen Sie in den obigen Beispielen wird, dass jede Abteilung zehn Verwandte Kurse hat. Das Beispiel für eager Loading würde nur einen einzelnen (Join)-Abfrage und einem einzelnen Roundtrip zur Datenbank führen. Der lazy Loading und Beispiele für explizites Laden ergibt beide elf Abfragen und elf Roundtrips zur Datenbank. Die zusätzlichen Roundtrips zur Datenbank beeinträchtigen die Leistung besonders bei hoher Latenz.

In einigen Szenarien ist lazy Loading auf der anderen Seite effizienter. Eager Loading möglicherweise eine sehr komplexe Verknüpfung generieren, die SQL Server nicht effizient verarbeiten kann. Oder Sie verarbeiten können, Lazy Load möglicherweise besser ausgeführt werden, da eager Loading mehr Daten als benötigt abrufen würde, wenn Sie Navigationseigenschaften einer Entität, nur für einen Teil einer Reihe von Entitäten zugreifen müssen. Wenn die Leistung wichtig ist, empfiehlt es sich, die Leistung mit beiden Möglichkeiten zu testen, um die beste Wahl treffen zu können.

Lazy Loading kann Code maskiert, für die Leistungsprobleme verursacht. Kann z. B., Code, der nicht geben eager oder expliziten Laden jedoch eine große Anzahl von Entitäten verarbeitet und verwendet mehrere Navigationseigenschaften in jeder Iteration sehr ineffizient (Dank der viele Roundtrips zur Datenbank). Eine Anwendung, die sich für die Entwicklung mit einer lokalen SQLServer führt möglicherweise Leistungsprobleme, wenn aufgrund der Latenzzeit und Lazy Load in Azure SQL-Datenbank verschoben. Profilieren von Datenbankabfragen mit einer realistischen testladevorgang können Sie bestimmen, wenn lazy Loading geeignet ist. Weitere Informationen finden Sie unter [die Enträtselung der Entity Framework-Strategien: Laden zugehöriger Daten](https://msdn.microsoft.com/magazine/hh205756.aspx) und [mithilfe von Entitätsframework zum Reduzieren der Netzwerklatenz für SQL Azure](https://msdn.microsoft.com/magazine/gg309181.aspx).

### <a name="disable-lazy-loading-before-serialization"></a>Deaktivieren Sie lazy Loading vor der Serialisierung

Wenn Sie lazy loading-aktivierten während der Serialisierung lassen, können Sie schließlich Abfragen deutlich mehr Daten als beabsichtigt. Serialisierung funktioniert im Allgemeinen durch den Zugriff auf jede Eigenschaft in einer Instanz eines Typs. Zugriff auf Eigenschaften wird lazy Loading ausgelöst, und die verzögerte geladenen Entitäten serialisiert werden. Der Serialisierungsprozess greift dann auf jede Eigenschaft der lazy geladenen Entitäten, und zwar mit möglicherweise sogar noch mehr lazy Loading und Serialisierung. Um diese runaway Kettenreaktion zu verhindern, aktivieren Sie lazy loading-deaktivieren, bevor Sie eine Entität zu serialisieren.

Serialisierung kann auch durch die Webdienstproxy-Klassen, die das Entity Framework verwendet werden, erschwert werden, wie unter der [erweiterte Szenarien der Tutorial](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#proxies).

Eine Möglichkeit zur Vermeidung von Problemen der Serialisierung wird zum Serialisieren von datentransferobjekte (DTOs) anstelle von Entitätsobjekten, siehe die [mithilfe des Web-API mit Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-5.md) Tutorial.

Wenn Sie keine DTOs verwenden, können Sie diese deaktivieren Sie lazy Loading und vermeiden Sie Proxyprobleme von [deaktivieren die Proxyerstellung](https://msdn.microsoft.com/data/jj592886.aspx).

Hier sind einige andere [Möglichkeiten zur Deaktivierung von lazy Loading](https://msdn.microsoft.com/data/jj574232):

- Lassen Sie für bestimmte Eigenschaften, die `virtual` -Schlüsselwort, wenn Sie die Eigenschaft deklarieren.
- Legen Sie für alle Navigationseigenschaften `LazyLoadingEnabled` zu `false`, platzieren Sie den folgenden Code im Konstruktor der Kontextklasse:

    [!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="create-a-courses-page"></a>Erstellen einer Kursseite

Die `Course` Entität enthält eine Navigationseigenschaft, enthält die `Department` Entität des Fachbereichs, die der Kurs zugewiesen ist. Um den Namen der zugewiesenen Abteilung in einer Kursliste anzuzeigen, müssen Sie zum Abrufen der `Name` Eigenschaft aus der `Department` Entität, die in der `Course.Department` Navigationseigenschaft.

Erstellen Sie einen Controller mit dem Namen `CourseController` (nicht CoursesController) für die `Course` Entitätstyp, verwenden Sie dieselben Optionen für die **MVC 5-Controller mit Ansichten unter Verwendung von Entity Framework** gerüstbauers, die bereits für die zuvor`Student` Controller:

| Einstellung | Wert |
| ------- | ----- |
| Model-Klasse | Wählen Sie **Kurs (ContosoUniversity.Models)**. |
| Datenkontextklasse | Wählen Sie **SchoolContext (ContosoUniversity.DAL)**. |
| Name des Domänencontrollers | Geben Sie *CourseController*. In diesem Fall nicht *CoursesController* mit einer *s*. Wenn Sie ausgewählt **Kurs (ContosoUniversity.Models)**, **Controllername** Wert automatisch aufgefüllt wurde. Sie müssen den Wert zu ändern. |

Übernehmen Sie die anderen Standardwerte, und fügen Sie den Controller.

Open *Controllers\CourseController.cs* und sehen Sie sich die `Index` Methode:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Der automatische Gerüstbau hat mithilfe der `Include`-Methode ein Eager Loading für die `Department`-Navigationseigenschaft angegeben.

Open *Views\Course\Index.cshtml* , und Ersetzen Sie den Vorlagencode durch den folgenden Code. Die Änderungen werden hervorgehoben:

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=4,7,14-16,23-25,31-33,40-42)]

Sie haben die folgenden Änderungen am eingerüsteten Code vorgenommen:

- Die Überschrift von geändert **Index** zu **Kurse**.
- Die Spalte **Anzahl** wurde hinzugefügt. Sie zeigt den `CourseID`-Eigenschaftswert an. Standardmäßig werden nicht die Primärschlüssel eingerüstet, da Sie normalerweise sie ohne Bedeutung für Endbenutzer sind. Allerdings ist in diesem Fall der Primärschlüssel sinnvoll, und Sie möchten ihn anzeigen.
- Verschoben der **Abteilung** Spalte auf der rechten Seite und deren Überschrift geändert. Der gerüstbauer ordnungsgemäß anzeigen möchte die `Name` Eigenschaft aus der `Department` Entität, aber hier in der Seite "Kurs" die Überschrift der Spalte muss **Abteilung** statt **Namen**.

Beachten Sie, dass für die Department-Spalte der eingerüstete Code zeigt die `Name` Eigenschaft der `Department` Entität, die in den geladen wird die `Department` Navigationseigenschaft:

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=2)]

Führen Sie die Seite (Wählen Sie die **Kurse** Registerkarte auf der Contoso University-Startseite) um die Liste mit den Abteilungsnamen anzuzeigen.

## <a name="create-an-instructors-page"></a>Erstellen einer dozentenseite

In diesem Abschnitt erstellen Sie einen Controller und zeigen Sie für die `Instructor` Entität, um die dozentenseite anzuzeigen. Auf dieser Seite werden verwandte Daten auf folgende Weise gelesen und angezeigt:

- Die Liste der Dozenten Zeigt verwandte Daten aus der `OfficeAssignment` Entität. Die `Instructor`- und `OfficeAssignment`-Entitäten stehen in einer 1:0..1-Beziehung zueinander. Verwenden Sie eager Loading für die `OfficeAssignment` Entitäten. Wie zuvor erläutert, ist Eager Loading in der Regel effizienter, wenn Sie die verwandten Daten für alle abgerufenen Zeilen der primären Tabelle benötigen. In diesem Fall sollten Sie die Office-Anweisungen für alle angezeigten Dozenten anzeigen.
- Wenn der Benutzer einen Dozenten, die im Zusammenhang auswählt `Course` Entitäten werden angezeigt. Die `Instructor`- und `Course`-Entitäten stehen in einer m:n-Beziehung zueinander. Verwenden Sie eager Loading für die `Course` Entitäten und den zugehörigen `Department` Entitäten. In diesem Fall kann Lazy Load effizienter sein, da Sie Kurse nur für den ausgewählten Dozenten benötigt. Dieses Beispiel zeigt jedoch, wie Eager Loading für Navigationseigenschaften in Entitäten in den Navigationseigenschaften verwendet wird.
- Wenn der Benutzer einen Kurs auswählt, verwandte Daten aus der `Enrollments` Entitätenmenge wird angezeigt. Die `Course`- und `Enrollment`-Entitäten stehen in einer 1:n-Beziehung zueinander. Fügen Sie das explizite Laden für `Enrollment` Entitäten und den zugehörigen `Student` Entitäten. (Explizites Laden nicht erforderlich, da lazy Loading aktiviert, jedoch wird veranschaulicht, wie Eager Loading.)

### <a name="create-a-view-model-for-the-instructor-index-view"></a>Erstellen Sie ein Ansichtsmodell für "Instructor" Ansicht "Index"

Die dozentenseite zeigt drei verschiedene Tabellen. Aus diesem Grund erstellen Sie ein Ansichtsmodell, das drei Eigenschaften enthält. Jede enthält Daten für eine der Tabellen.

In der *ViewModels* Ordner erstellen *InstructorIndexData.cs* , und Ersetzen Sie den vorhandenen Code durch den folgenden Code:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

### <a name="create-the-instructor-controller-and-views"></a>Erstellen der Dozentencontroller und-Ansichten

Erstellen Sie eine `InstructorController` (nicht InstructorsController) Controller mit Lese-/Schreibzugriff EF-Aktion:

| Einstellung | Wert |
| ------- | ----- |
| Model-Klasse | Wählen Sie **"Instructor" (ContosoUniversity.Models)**. |
| Datenkontextklasse | Wählen Sie **SchoolContext (ContosoUniversity.DAL)**. |
| Name des Domänencontrollers | Geben Sie *InstructorController*. In diesem Fall nicht *InstructorsController* mit einer *s*. Wenn Sie ausgewählt **Kurs (ContosoUniversity.Models)**, **Controllername** Wert automatisch aufgefüllt wurde. Sie müssen den Wert zu ändern. |

Übernehmen Sie die anderen Standardwerte, und fügen Sie den Controller.

Open *Controllers\InstructorController.cs* und Hinzufügen einer `using` -Anweisung für die `ViewModels` Namespace:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

Der eingerüstete Code in die `Index` -Methode gibt eager Loading nur für die `OfficeAssignment` Navigationseigenschaft:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

Ersetzen Sie die `Index` -Methode durch den folgenden Code, um weitere laden zugehöriger Daten, und fügen Sie ihn in das Ansichtsmodell:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

Die Methode akzeptiert optionale Routendaten (`id`) und einen Abfragezeichenfolgen-Parameter (`courseID`) die ID-Werte, der den ausgewählten Dozenten und Kurses bereitstellen und alle erforderlichen Daten an die Ansicht übergibt. Die Parameter werden durch die **Auswählen**-Links auf der Seite bereitgestellt.

Der Code erstellt zuerst eine Instanz des Ansichtsmodells und fügt die Dozentenliste ein. Der Code gibt eager Loading für die `Instructor.OfficeAssignment` und `Instructor.Courses` Navigationseigenschaft.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=3-4)]

Die zweite `Include` Methode lädt, Kurse und für jeden Kurs, der geladen wird, ist dies der Fall ist eager Loading für die `Course.Department` Navigationseigenschaft.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

Wie bereits erwähnt, ist Eager Load ist nicht erforderlich, aber erfolgt, um die Leistung zu verbessern. Da die Ansicht immer erforderlich ist der `OfficeAssignment` Entität ist es effizienter, die in der gleichen Abfrage abgerufen. `Course` Entitäten sind erforderlich, wenn auf der Webseite ein Dozent ausgewählt wird, damit Sie eager Loading ist besser als das verzögerte Laden nur dann, wenn die Seite häufiger mit einem ausgewählten Kurs als ohne angezeigt wird.

Wenn eine ID für "Instructor" ausgewählt wurde, wird der ausgewählte Dozent aus der Liste der Dozenten im Ansichtsmodell abgerufen. Des Ansichtsmodells `Courses` Eigenschaft wird dann geladen, mit der `Course` Entitäten aus dieser "Instructor" `Courses` Navigationseigenschaft.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

Die `Where` -Methode gibt eine Auflistung, aber in diesem Fall die Kriterien, die nur eine einzige Methode zu übergeben `Instructor` Entität zurückgegeben wird. Die `Single` Methode konvertiert die Sammlung in eine einzelne `Instructor` -Entität, die Sie Zugriff auf die juristischen Person erhalten `Courses` Eigenschaft.

Sie verwenden die [einzelne](https://msdn.microsoft.com/library/system.linq.enumerable.single.aspx) Methode für eine Sammlung aus, wenn Sie wissen, dass die Auflistung wird nur ein Element aufweisen. Die `Single` Methode löst eine Ausnahme aus, wenn die Auflistung, die an sie übergebenen leer ist oder wenn mehr als ein Element vorhanden ist. Eine Alternative ist die [SingleOrDefault](https://msdn.microsoft.com/library/bb342451.aspx), womit einen Standardwert (`null` in diesem Fall), wenn die Auflistung leer ist. Allerdings in diesem Fall, die immer noch würde zu einer Ausnahme (versucht, finden Sie eine `Courses` Eigenschaft für eine `null` Verweis), und die Ausnahmemeldung würde die Ursache des Problems weniger deutlich angeben. Beim Aufrufen der `Single` -Methode, Sie können auch übergeben die `Where` Bedingung statt der `Where` Methode getrennt:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]

anstelle von:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

Wenn ein Kurs ausgewählt wurde, wird der ausgewählte Kurs aus der Kursliste im Ansichtsmodell abgerufen. Klicken Sie dann die des Ansichtsmodells `Enrollments` Eigenschaft wird geladen, mit der `Enrollment` Entitäten aus dieser Kurs die `Enrollments` Navigationseigenschaft.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

### <a name="modify-the-instructor-index-view"></a>Ändern der Indexansicht "Instructor"

In *Views\Instructor\Index.cshtml*, ersetzen Sie den Vorlagencode durch den folgenden Code. Die Änderungen werden hervorgehoben:

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1,4,14-18,21,23-28,38-43,45)]

Sie haben die folgenden Änderungen am bestehenden Code vorgenommen:

- Die Modellklasse wurde zu `InstructorIndexData` geändert.
- Der Seitenname wurde von **Index** in **Dozenten** geändert.
- Hinzugefügt ein **Office** Spalte `item.OfficeAssignment.Location` nur, wenn `item.OfficeAssignment` nicht null ist. (Da dies eine 1: 0 (null)-oder-1-Beziehung ist, gibt es möglicherweise keine verknüpfte `OfficeAssignment` Entität.)

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]
- Code hinzugefügt, die dynamisch hinzufügen `class="success"` auf die `tr` Element des ausgewählten Dozenten. Hiermit wird eine Hintergrundfarbe für die ausgewählte Zeile mithilfe einer [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap) Klasse.

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]
- Einen neuen `ActionLink` mit der Bezeichnung **wählen** unmittelbar vor den anderen Links in jeder Zeile, wodurch die ID des ausgewählten Dozenten zu sendende der `Index` Methode.

Führen Sie die Anwendung, und wählen Sie die **Dozenten** Registerkarte. Die Seite zeigt die `Location` Eigenschaft des zugehörigen `OfficeAssignment` Entitäten und eine leere Tabelle Zelle bei Nein, die im Zusammenhang `OfficeAssignment` Entität.

In der *Views\Instructor\Index.cshtml* -Datei nach dem schließenden `table` Element (am Ende der Datei), fügen Sie den folgenden Code hinzu. Dieser Code zeigt eine Liste der Kurse an, die im Zusammenhang mit einem Dozenten stehen, wenn ein Dozent ausgewählt wird.

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

Dieser Code liest die `Courses`-Eigenschaft des Ansichtsmodells, um eine Kursliste anzuzeigen. Es bietet auch eine `Select` Hyperlink, der die ID des ausgewählten Kurs, sendet der `Index` Aktionsmethode.

Führen Sie die Seite, und wählen Sie einen Dozenten. Jetzt sehen Sie ein Raster, das die dem Dozenten zugewiesenen Kurse anzeigt. Sie sehen auch den Namen der zugewiesenen Abteilung für jeden Kurs.

Fügen Sie den folgenden Code hinzu, nachdem Sie den Codeblock hinzugefügt haben. Dies zeigt eine Liste der Studenten an, die im Kurs registriert sind, wenn dieser Kurs ausgewählt ist.

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

Dieser Code liest die `Enrollments` -Eigenschaft des Ansichtsmodells, um eine Liste von Teilnehmern anzuzeigen, die in diesem Kurs registrierten.

Führen Sie die Seite, und wählen Sie einen Dozenten. Wählen Sie dann einen Kurs aus, um die Liste der registrierten Studenten und deren Noten einzusehen.

### <a name="adding-explicit-loading"></a>Hinzufügen von expliziten Laden

Open *InstructorController.cs* und sehen Sie sich wie die `Index` Methode ruft die Liste der Registrierungen für einen ausgewählten Kurs:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

Wenn Sie die Liste der Dozenten abgerufen haben, Sie angegeben haben eager Loading für die `Courses` Navigationseigenschaft und für die `Department` Eigenschaft für jeden Kurs. Platzieren Sie Sie der `Courses` Sammlung im Ansichtsmodell, und nachdem auf Sie zugreifen, die `Enrollments` Navigationseigenschaft von einer Entität in der Auflistung. Da Sie eager Loading für nicht angegeben haben die `Course.Enrollments` Navigationseigenschaft, die Daten aus dieser Eigenschaft werden auf der Seite als Ergebnis der Lazy Load angezeigt.

Wenn Sie lazy Loading deaktiviert, ohne den Code auf andere Weise, die `Enrollments` Eigenschaft würde unabhängig davon, wie viele Registrierungen, die die Vorgehensweise bekannt, ob null sein. In diesem Fall beim Laden der `Enrollments` -Eigenschaft müssen entweder eager Loading oder expliziten Laden angeben. Sie haben bereits Eager Load Vorgehensweise gesehen. Um ein Beispiel für explizites Laden anzuzeigen, ersetzen die `Index` Methode durch den folgenden Code, der explizit lädt die `Enrollments` Eigenschaft. Der Code geändert werden hervorgehoben.

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs?highlight=20-31)]

Nach dem Abrufen des ausgewählten `Course` Entität, der neue Code lädt explizit des Kurses `Enrollments` Navigationseigenschaft:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

Anschließend er explizit jedes lädt `Enrollment` Entität die verbundene `Student` Entität:

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cs)]

Beachten Sie, die Sie verwenden die `Collection` Methode zum Laden einer Auflistungseigenschaft, aber für eine Eigenschaft, die nur eine einzelne Entität enthält, verwenden Sie die `Reference` Methode.

Die Indexseite für "Instructor" jetzt ausführen, und Sie sehen keinen Unterschied in der Anzeige auf der Seite auf, obwohl Sie geändert haben, wie die Daten abgerufen werden.

## <a name="get-the-code"></a>Abrufen des Codes

[Abgeschlossenes Projekt herunterladen](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Links zu anderen Ressourcen des Entity Framework finden Sie in der [ASP.NET-Datenzugriff – empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Haben gelernt, wie zum Laden von verknüpfter Daten
> * Erstellt eine Kursseite
> * Erstellt ein "Dozenten"

Fahren Sie fort mit dem nächsten Artikel erfahren, wie verwandte Daten aktualisiert werden soll.

> [!div class="nextstepaction"]
> [Aktualisieren relevanter Daten](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)