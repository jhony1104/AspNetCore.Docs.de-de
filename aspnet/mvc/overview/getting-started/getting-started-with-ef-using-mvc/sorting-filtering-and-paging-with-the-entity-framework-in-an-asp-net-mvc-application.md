---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
title: 'Tutorial: Hinzufügen von sortieren, Filtern und paging mit Entity Framework in einer ASP.NET MVC-Anwendung | Microsoft-Dokumentation'
author: tdykstra
description: In diesem Tutorial fügen Sie sortieren, Filtern und Pagingfunktionen für die **Schüler/Studenten** Indexseite. Sie erstellen außerdem eine einfache Gruppierung-Seite.
ms.author: riande
ms.date: 01/14/2019
ms.assetid: d5723e46-41fe-4d09-850a-e03b9e285bfa
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1f18a15d39d58ffb4ac48cfccee6519d33294e85
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444194"
---
# <a name="tutorial-add-sorting-filtering-and-paging-with-the-entity-framework-in-an-aspnet-mvc-application"></a>Tutorial: Hinzufügen von sortieren, Filtern und paging mit Entity Framework in einer ASP.NET MVC-Anwendung

In der [vorherigen Tutorial](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md), Sie implementiert eine Reihe von Webseiten für grundlegende CRUD-Vorgänge für `Student` Entitäten. In diesem Tutorial fügen Sie sortieren, Filtern und Pagingfunktionen für die **Schüler/Studenten** Indexseite. Sie erstellen außerdem eine einfache Gruppierung-Seite.

Die folgende Abbildung zeigt, wie die Seite aussehen wird, wenn Sie fertig sind. Die Spaltenüberschriften sind Links, auf die der Benutzer klicken kann, um die Spalte zu sortieren. Wiederholtes Klicken auf eine Spaltenüberschrift schaltet zwischen aufsteigender und absteigender Sortierreihenfolge um.

![Students_Index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

In diesem Tutorial:

> [!div class="checklist"]
> * Hinzufügen von spaltensortierungslinks
> * Hinzufügen eines Suchfelds
> * Hinzufügen von paging
> * Erstellen Sie eine Seite "Info"

## <a name="prerequisites"></a>Vorraussetzungen

* [Implementieren von grundlegenden CRUD-Funktionen](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)

## <a name="add-column-sort-links"></a>Hinzufügen von spaltensortierungslinks

Um der Indexseite die Sortierfunktion hinzuzufügen, ändern Sie die `Index` Methode der `Student` Controller und fügen Sie Code die `Student` indizieren Sie die Sicht.

### <a name="add-sorting-functionality-to-the-index-method"></a>Hinzufügen der Sortierfunktion zur Indexmethode

- In *Controllers\StudentController.cs*, ersetzen Sie die `Index` Methode durch den folgenden Code:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

Dieser Code empfängt einen `sortOrder`-Parameter aus der Abfragezeichenfolge in der URL. Den Wert der Abfragezeichenfolge wird von ASP.NET MVC als Parameter an die Aktionsmethode bereitgestellt. Der Parameter ist eine Zeichenfolge, die entweder "Name" oder "Date", optional gefolgt von einem Unterstrich und die Zeichenfolge "Desc", um die Angabe einer absteigenden Reihenfolge. Standardmäßig wird eine aufsteigende Sortierreihenfolge verwendet.

Bei der ersten Anforderung der Indexseite gibt es keine Abfragezeichenfolge. Die Studenten werden angezeigt, in aufsteigender Reihenfolge von `LastName`, dies ist die Standardeinstellung, wie das Fall-through-Fall in die `switch` Anweisung. Wenn der Benutzer auf den Link einer Spaltenüberschrift klickt, wird der entsprechende `sortOrder`-Wert in der Abfragezeichenfolge bereitgestellt.

Die beiden `ViewBag` Variablen werden verwendet, sodass die Ansicht die Links der Spaltenüberschriften mit den entsprechenden Abfragezeichenfolgenwerten konfigurieren kann:

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

Hierbei handelt es sich um ternäre Anweisungen. Die erste gibt an, dass bei der `sortOrder` -Parameter ist null oder leer ist, `ViewBag.NameSortParm` sollte so festgelegt werden "Namen\_Desc" ist, andernfalls sollte Sie auf eine leere Zeichenfolge festgelegt werden. Durch diese beiden Anweisungen können in der Ansicht die Hyperlinks in den Spaltenüberschriften wie folgt festgelegt werden:

| Aktuelle Sortierreihenfolge | Hyperlink „Nachname“ | Hyperlink „Datum“ |
| --- | --- | --- |
| Nachname (aufsteigend) | descending | ascending |
| Nachname (absteigend) | ascending | ascending |
| Datum (aufsteigend) | ascending | descending |
| Datum (absteigend) | ascending | ascending |

Die Methode verwendet [LINQ to Entities](/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities) an die Spalte für die Sortierung. Der Code erstellt ein <xref:System.Linq.IQueryable%601> Variable vor dem die `switch` -Anweisung ändert in der `switch` -Anweisung und ruft die `ToList` Methode nach der `switch` Anweisung. Es wir keine Abfrage an die Datenbank gesendet, wenn Sie die `IQueryable`-Variablen erstellen und ändern. Die Abfrage wird nicht ausgeführt, bis Sie konvertieren die `IQueryable` Objekt in einer Auflistung durch Aufrufen einer Methode wie z. B. `ToList`. Aus diesem Grund führt dieser Code zu einer einzelnen Abfrage, die nicht, bis ausgeführt wird die `return View` Anweisung.

Als Alternative zum Schreiben von verschiedenen LINQ-Anweisungen für jede Sortierreihenfolge können Sie dynamisch eine LINQ-Anweisung erstellen. Weitere Informationen zu dynamischen LINQ finden Sie unter [dynamische LINQ](https://go.microsoft.com/fwlink/?LinkID=323957).

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a>Hinzufügen von spaltenüberschriftenlinks zur Indexansicht für Schüler und Studenten

1. In *Views\Student\Index.cshtml*, ersetzen Sie die `<tr>` und `<th>` Elemente für die Zeile mit der Überschrift mit den hervorgehobenen Code:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=5-15)]

   Dieser Code verwendet die Informationen in den `ViewBag` Eigenschaften zum Einrichten von Hyperlinks mit der entsprechenden Abfrage-Zeichenfolgenwerte.

2. Führen Sie die Seite, und klicken Sie auf die **Nachname** und **Registrierungsdatum** funktioniert Spaltenüberschriften, um diese Sortierung zu überprüfen.

   Nachdem Sie auf die **Nachname** Überschrift werden Schüler/Studenten in absteigender Reihenfolge der letzten Namen angezeigt.

## <a name="add-a-search-box"></a>Hinzufügen eines Suchfelds

Zum Hinzufügen der Filterung auf der Indexseite "Studenten", Sie fügen einem Textfeld und eine Schaltfläche "Senden" zur Ansicht und stellen Sie die entsprechende Änderungen in der `Index` Methode. Das Textfeld können Sie eine Zeichenfolge, die in den Vornamen und Nachnamen gesucht werden soll.

### <a name="add-filtering-functionality-to-the-index-method"></a>Hinzufügen der Filterfunktion zur Indexmethode

- In *Controllers\StudentController.cs*, ersetzen Sie die `Index` Methode durch den folgenden Code (die Änderungen sind hervorgehoben):

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=1,7-11)]

Der Code Fügt eine `searchString` Parameter, um die `Index` Methode. Der Zeichenfolgenwert für die Suche wird aus einem Textfeld empfangen, das Sie zur Indexansicht hinzufügen. Sie fügt außerdem eine `where` Klausel zur LINQ-Anweisung, die nur Studenten auswählt, deren vor- oder Nachname die Suchzeichenfolge enthält. Die Anweisung, die fügt die <xref:System.Linq.Queryable.Where%2A> -Klausel ausgeführt wird, nur dann, wenn ein zu suchende Wert.

> [!NOTE]
> In vielen Fällen können Sie die gleiche Methode aufrufen, an einer Entity Framework-Entitätenmenge oder als eine Erweiterungsmethode in einer in-Memory-Sammlung. Die Ergebnisse sind normalerweise identisch, aber in einigen Fällen können abweichen.
>
> Z. B. die Implementierung von .NET Framework die `Contains` Methode gibt alle Zeilen zurück, wenn Sie eine leere Zeichenfolge an sie übergeben, aber die Entity Framework-Anbieter für SQL Server Compact 4.0 gibt 0 (null) Zeilen auf leere Zeichenfolgen zurück. Aus diesem Grund wird der Code im Beispiel (Einfügen der `Where` -Anweisung innerhalb einer `if` Anweisung) stellt sicher, dass Sie die gleichen Ergebnisse für alle Versionen von SQL Server erhalten. Darüber hinaus die Implementierung von .NET Framework die `Contains` -Methode führt einen Vergleich Groß-/Kleinschreibung standardmäßig, aber die Entity Framework SQL Server-Ressourcenanbieter führen die Groß-/Kleinschreibung Vergleiche standardmäßig. Aus diesem Grund Aufrufen der `ToUpper` Methode, um den Test explizit Groß-/Kleinschreibung, wird sichergestellt, dass die Ergebnisse nicht ändern, wenn Sie ändern den Code später ein Repository verwenden, die zurückgibt, wird ein `IEnumerable` -Sammlung anstelle einer `IQueryable` Objekt. (Beim Aufrufen der `Contains`-Methode einer `IEnumerable`-Sammlung erhalten Sie die .NET Framework-Implementierung. Wenn Sie sie auf einem `IQueryable`-Objekt aufrufen, erhalten Sie die Implementierung des Datenanbieters.)
>
> NULL-Behandlung für unterschiedliche Datenbankanbieter oder wenn Sie möglicherweise auch eine `IQueryable` Objekt im Vergleich zu, bei der Verwendung einer `IEnumerable` Auflistung. Beispielsweise in einigen Szenarien eine `Where` wie z. B. für die erste Bedingung `table.Column != 0` Spalten, die möglicherweise nicht zurück `null` als Wert. Weitere Informationen finden Sie unter [fehlerhafte Behandlung von null-Variablen in der where-Klausel](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl).

### <a name="add-a-search-box-to-the-student-index-view"></a>Hinzufügen einer Suchfelds zur studentenindexansicht

1. In *Views\Student\Index.cshtml*, fügen Sie den hervorgehobenen Code unmittelbar vor dem Öffnen `table` Tag, um eine Beschriftung, ein Textfeld, erstellen und eine **Suche** Schaltfläche.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=4-11)]

2. Führen Sie die Seite, geben Sie eine Suchzeichenfolge ein, und klicken Sie auf **Suche** um sicherzustellen, dass die Funktionsweise des Filters.

   Beachten Sie, dass die URL nicht die Suchzeichenfolge "an" enthält, was bedeutet, dass wenn Sie diese Seite kennzeichnen, die gefilterte Liste erhalten Sie wird nicht bei der Verwendung von Lesezeichen. Dies gilt auch für die spaltensortierungslinks, wie sie die gesamte Liste sortiert werden sollen. Ändern Sie die **Suche** Schaltfläche, um später in diesem Tutorial verwenden Sie Abfragezeichenfolgen für Filterkriterien.

## <a name="add-paging"></a>Hinzufügen von paging

Zum Auslagern der Indexseite "Studenten" hinzugefügt haben, beginnen Sie durch die Installation von der **PagedList.Mvc** NuGet-Paket. Sie zusätzliche Änderungen vornehmen, werden die `Index` Methode und Hinzufügen von paginglinks in der `Index` anzeigen. **PagedList.Mvc** ist einer der vielen guten Paginierung und Sortierung von Paketen für ASP.NET MVC und dessen Verwendung hier dient nur als Beispiel, nicht als eine Empfehlung für die sie über weitere Optionen.

### <a name="install-the-pagedlistmvc-nuget-package"></a>Installieren Sie das PagedList.MVC NuGet-Paket

NuGet **PagedList.Mvc** Paketinstallationen automatisch die **PagedList** Paket als Abhängigkeit. Die **PagedList** Paket installiert einen `PagedList` Sammlungsmethoden Typ und die Erweiterung für `IQueryable` und `IEnumerable` Sammlungen. Die Erweiterungsmethoden erstellen Sie eine einzelne Seite mit Daten in eine `PagedList` Auflistung von Ihrer `IQueryable` oder `IEnumerable`, und die `PagedList` enthält verschiedene Eigenschaften und Methoden, die das Paging zu erleichtern. Die **PagedList.Mvc** Paketinstallationen eine paging-Hilfsprogramm, das die Pagingschaltflächen anzeigt.

1. Von der **Tools** , wählen Sie im Menü **NuGet Package Manager** und dann **-Paket-Manager-Konsole**.

2. In der **-Paket-Manager-Konsole** Fenster, stellen Sie sicher, dass die **Paketquelle** ist **nuget.org** und die **Standardprojekt** ist**ContosoUniversity**, und geben Sie dann den folgenden Befehl aus:

   ```text
   Install-Package PagedList.Mvc
   ```

3. Erstellen Sie das Projekt.

### <a name="add-paging-functionality-to-the-index-method"></a>Fügen Sie Pagingfunktionen zur Indexmethode hinzu

1. In *Controllers\StudentController.cs*, Hinzufügen einer `using` -Anweisung für die `PagedList` Namespace:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

2. Ersetzen Sie die `Index`-Methode durch folgenden Code:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=1,3,7-16,41-43)]

   Dieser Code Fügt eine `page` -Parameter, einen aktuellen Sortierparameter Reihenfolge und einen aktuellen Filterparameter zur Methodensignatur:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

   Zum ersten Mal die Seite wird angezeigt, oder wenn der Benutzer einen Paging- oder sortierlink geklickt hat noch nicht für alle Parameter null sind. Wenn auf ein paginglink geklickt wird, die `page` Variable enthält die anzuzeigende Seitenzahl.

   Ein `ViewBag` Eigenschaft stellt die Ansicht mit der aktuellen Sortierreihenfolge bereit, da dies in den paginglinks enthalten sein muss, um der Sortierreihenfolge der während des Pagings beibehalten:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

   Eine andere Eigenschaft, `ViewBag.CurrentFilter`, bietet eine Ansicht mit der aktuellen Filterzeichenfolge. Dieser Wert muss in den Paginglinks enthalten sein, damit die Filtereinstellungen während des Pagingvorgangs beibehalten werden, und er muss im Textfeld wiederhergestellt werden, wenn die Seite erneut angezeigt wird. Wenn die Suchzeichenfolge während des Pagingvorgangs geändert wird, muss die Seite auf 1 zurückgesetzt werden, da der neue Filter andere Daten anzeigen kann. Die Suchzeichenfolge wird geändert, wenn ein Wert in das Textfeld eingegeben, und die Schaltfläche "Senden" geklickt wird. In diesem Fall die `searchString` -Parameter ist nicht null.

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

   Am Ende der Methode die `ToPagedList` Erweiterungsmethode für die Schüler/Studenten `IQueryable` Objekt konvertiert die studentenabfrage in eine einzelne Seite in einem Sammlungstyp, der Pagingvorgänge unterstützt. Diese einzelnen Seite mit Studentendaten wird dann an die Ansicht übergeben:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

   Die `ToPagedList`-Methode nimmt eine Seitenanzahl. Die zwei Fragezeichen darstellen der [Null-Sammeloperator](/dotnet/csharp/language-reference/operators/null-coalescing-operator). Der Nullzusammensetzungsoperator definiert einen Standardwert für einen Nullable-Typ. Der `(page ?? 1)`-Ausdruck bedeutet, dass `page` zurückgegeben wird, wenn dies über einen Wert verfügt, oder 1, wenn `page` gleich 0 (null) ist.

### <a name="add-paging-links-to-the-student-index-view"></a>Hinzufügen eines paginglinks zur Indexansicht für Schüler und Studenten

1. In *Views\Student\Index.cshtml*, ersetzen Sie den vorhandenen Code durch den folgenden Code. Die Änderungen werden hervorgehoben.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=1-3,6,9,14,17,24,30,55-56,58-59)]

   Die `@model`-Anweisung am oberen Rand der Seite gibt an, dass die Ansicht nun ein `PagedList`-Objekt anstelle eines `List`-Objekts aufruft.

   Die `using` -Anweisung für `PagedList.Mvc` bietet Zugriff auf das MVC-Hilfsprogramm für die Pagingschaltflächen.

   Der Code verwendet eine Überladung der [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) , ermöglicht es an [FormMethod.Get](/previous-versions/aspnet/dd460179(v=vs.100)).

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cshtml?highlight=1)]

   Der Standardwert [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) sendet Formulardaten mit einem POST, was bedeutet, dass Parameter als Abfragezeichenfolgen im Hauptteil HTTP-Nachricht und nicht in der URL übergeben werden. Bei der Angabe von HTTP GET werden die Formulardaten als Abfragezeichenfolgen an die URL übergeben. Dadurch können Benutzer ein Lesezeichen für die URL erstellen. Die [W3C-Richtlinien für die Verwendung von HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) wird empfohlen, dass Sie GET verwenden sollten, wenn die Aktion nicht in einem Update führt.

   Das Textfeld wird mit die aktuelle Suchzeichenfolge initialisiert, wenn Sie eine neue Seite klicken Sie auf die aktuelle Suchzeichenfolge zu sehen.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml?highlight=1)]

   Die Spaltenüberschriftenlinks verwenden die Abfragezeichenfolge, um die aktuelle Suchzeichenfolge an den Controller zu übergeben, damit Benutzer Filterergebnisse sortieren können:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1)]

   Die aktuelle Seite und die Summe der Anzahl der Seiten werden angezeigt.

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]

   Wenn keine Seiten zum Anzeigen vorhanden sind, wird "Seite 0 0" angezeigt. (In diesem Fall ist die Nummer der Seite größer als die Anzahl der Seiten da `Model.PageNumber` 1 ist, und `Model.PageCount` ist 0.)

   Die Pagingschaltflächen werden angezeigt, durch die `PagedListPager` Hilfsprogramm:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]

   Die `PagedListPager` Hilfsprogramm stellt eine Reihe von Optionen, die Sie anpassen können, einschließlich URLs und formatieren,. Weitere Informationen finden Sie unter [TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) auf der GitHub-Website.

2. Führen Sie die Seite.

   Klicken Sie auf die Paginglinks in verschiedenen Sortierreihenfolgen, um sicherzustellen, dass die Paging funktioniert. Geben Sie dann eine Suchzeichenfolge ein. Probieren Sie Paging erneut aus, um sicherzustellen, dass sie auch mit Sortier- und Filtervorgängen ordnungsgemäß funktioniert.

## <a name="create-an-about-page"></a>Erstellen Sie eine Seite "Info"

Zeigen Sie für der Contoso University-Website die Infoseite wie viele Studenten jedes Anmeldedatum angemeldet haben. Das erfordert Gruppieren und einfache Berechnungen dieser Gruppen. Um dies zu erreichen, ist Folgendes erforderlich:

- Erstellen Sie eine Ansichtsmodellklasse für die Daten, die Sie an die Ansicht übergeben müssen.
- Ändern der `About` -Methode in der die `Home` Controller.
- Ändern der `About` anzeigen.

### <a name="create-the-view-model"></a>Erstellen des Ansichtsmodells

Erstellen Sie eine *ViewModels* Ordner im Projektordner. Fügen Sie in diesem Ordner die Klassendatei *EnrollmentDateGroup.cs* , und Ersetzen Sie den Vorlagencode durch den folgenden Code:

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

### <a name="modify-the-home-controller"></a>Ändern des Home-Controllers

1. In *"HomeController.cs"*, fügen Sie die folgenden `using` Anweisungen am Anfang der Datei:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

2. Fügen Sie unmittelbar nach der öffnenden geschweiften Klammer für die Klasse eine Klassenvariable für den Datenbankkontext hinzu:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs?highlight=3)]

3. Ersetzen Sie die `About`-Methode durch folgenden Code:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs)]

   Die LINQ-Anweisung gruppiert die Studentenentitäten nach Anmeldedatum, berechnet die Anzahl der Entitäten in jeder Gruppe und speichert die Ergebnisse in einer Sammlung von `EnrollmentDateGroup`-Ansichtsmodellobjekten.

4. Hinzufügen einer `Dispose` Methode:

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

### <a name="modify-the-about-view"></a>Ändern der Infoansicht

1. Ersetzen Sie den Code in die *Views\Home\About.cshtml* -Datei mit den folgenden Code:

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml)]

2. Führen Sie die app, und klicken Sie auf die **zu** Link.

   Die Anzahl der Schüler/Studenten für jedes Anmeldedatum zeigt in einer Tabelle.

   ![About_page](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

## <a name="get-the-code"></a>Abrufen des Codes

[Herunterladen des abgeschlossenen Projekts](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Links zu anderen Ressourcen des Entity Framework finden Sie im [ASP.NET-Datenzugriff – empfohlene Ressourcen](../../../../whitepapers/aspnet-data-access-content-map.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial:

> [!div class="checklist"]
> * Hinzufügen von spaltensortierungslinks
> * Hinzufügen eines Suchfelds
> * Hinzufügen von paging
> * Erstellen Sie eine Seite "Info"

Wechseln Sie zum nächsten Artikel erfahren, wie Sie mit der Verbindung resilienz und Befehl abfangen.
> [!div class="nextstepaction"]
> [Abfangen von Verbindung resilienz und Befehl](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)