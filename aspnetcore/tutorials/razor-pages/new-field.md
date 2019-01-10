---
title: Hinzufügen eines neuen Felds zu einer Razor-Seite in ASP.NET Core
author: rick-anderson
description: Veranschaulicht das Hinzufügen ein neues Felds zu einer Razor Page mit Entity Framework Core
monikerRange: '>= aspnetcore-2.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 9b3ad5f6c4b1c9b5f016f5591127c8d1b213948d
ms.sourcegitcommit: 1ea1b4fc58055c62728143388562689f1ef96cb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53329132"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a>Hinzufügen eines neuen Felds zu einer Razor-Seite in ASP.NET Core

Von [Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/rp/download.md)]

In diesem Abschnitt werden [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First-Migrationen für folgende Zwecke verwendet:

* Dem Modell wird ein neues Feld hinzugefügt.
* Die neue Änderung am Feldschema wird in die Datenbank migriert.

Bei der Verwendung von EF Code First für die automatische Erstellung einer Datenbank geht Code First wie folgt vor:

* Es fügt eine Tabelle zur Datenbank hinzu, um nachzuverfolgen, ob das Schema der Datenbank mit den Modellklassen synchron ist, aus denen sie generiert wurde.
* Wenn die Datenbank mit den Modellklassen nicht synchron ist, löst EF eine Ausnahme aus.

Durch die automatische Überprüfung, ob das Schema und das Modell synchron sind, können Probleme inkonsistenter Datenbanken/Codes leichter ermittelt werden.

## <a name="adding-a-rating-property-to-the-movie-model"></a>Hinzufügen einer Rating-Eigenschaft zum Movie-Modell

Öffnen Sie die Datei *Models/Movie.cs*, und fügen Sie eine `Rating`-Eigenschaft hinzu:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

Erstellen Sie die App.

Bearbeiten Sie *Pages/Movies/Index.cshtml*, und fügen ein `Rating`-Feld hinzu:

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

Aktualisieren Sie die folgenden Seiten:

* Fügen Sie das `Rating`-Feld zu den Seiten „Delete“ und „Details“ hinzu.
* Aktualisieren Sie die Datei [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) mit einem `Rating`-Feld.
* Fügen Sie das Feld `Rating` der Bearbeitungsseite hinzu.

Die App funktioniert erst, nachdem die Datenbank mit dem neuen Feld aktualisiert wurde. Wenn sie jetzt ausgeführt wird, löst die App eine `SqlException` aus:

`SqlException: Invalid column name 'Rating'.`

Dieser Fehler wird dadurch verursacht, dass sich die aktualisierte Modellklasse „Movie“ vom Schema der Tabelle „Movie“ der Datenbank unterscheidet. (Die Datenbanktabelle enthält nicht die Spalte `Rating`.)

Es gibt mehrere Vorgehensweisen zum Beheben des Fehlers:

1. Lassen Sie die Datenbank von Entity Framework automatisch löschen und mit dem neuen Modellklassenschema neu erstellen. Dieser Ansatz ist früh im Entwicklungszyklus sehr praktisch. Er ermöglicht Ihnen, das Modell und das Datenbankschema schnell gemeinsam weiterzuentwickeln. Der Nachteil ist, dass in der Datenbank vorhandene Daten verloren gehen. Verwenden Sie diesen Ansatz nicht in einer Produktionsdatenbank! Das Löschen der Datenbank bei Schemaänderungen und das Verwenden eines Initialisierers zum automatischen Ausführen eines Seedings für eine Datenbank mit Testdaten ist häufig eine produktive Möglichkeit zum Entwickeln einer App.

2. Ändern Sie das Schema der vorhandenen Datenbank explizit so, dass es mit den Modellklassen übereinstimmt. Der Vorteil dieses Ansatzes ist, dass Sie Ihre Daten behalten. Sie können diese Änderung entweder manuell oder durch Erstellen eines Änderungsskripts für die Datenbank vornehmen.

3. Verwenden Sie Code First-Migrationen, um das Datenbankschema zu aktualisieren.

Verwenden Sie für dieses Tutorial Code First-Migrationen.

Aktualisieren Sie die `SeedData`-Klasse so, dass sie einen Wert für die neue Spalte bereitstellt. Eine Beispieländerung ist nachstehend gezeigt, aber Sie sollten diese Änderung für jeden `new Movie`-Block vornehmen.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

Sehen Sie sich die [fertige SeedData.cs-Datei an](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).

Erstellen Sie die Projektmappe.

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>Hinzufügen einer Migration für das Bewertungsfeld

Öffnen Sie das Menü **Extras**. Wählen Sie dort **NuGet-Paket-Manager > Paket-Manager-Konsole** aus.
Geben Sie in der PMC die folgenden Befehle ein:

```powershell
Add-Migration Rating
Update-Database
```

Der `Add-Migration`-Befehl weist das Framework an, Folgendes auszuführen:

* Das `Movie`-Modell mit dem Schema der `Movie`-Datenbank vergleichen.
* Code erstellen, um das Schema der Datenbank in das neue Modell zu migrieren.

Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen. Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.

Der `Update-Database`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden.

<a name="ssox"></a>

Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu. Dies ist über die Links „Löschen“ im Browser oder [SQL Server-Objekt-Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX) möglich.

Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen. So löschen Sie die Datenbank in SSOX

* Wählen Sie die Datenbank in SSOX aus.
* Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie *Löschen* aus.
* Aktivieren Sie **Vorhandene Verbindungen schließen**.
* Klicken Sie auf **OK**.
* Aktualisieren Sie die Datenbank in [PMC](xref:tutorials/razor-pages/new-field#pmc):

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[Visual Studio Code / Visual Studio für Mac](#tab/visual-studio-code+visual-studio-mac)

<!-- copy/paste this tab to the next. Not worth an include  -->

Führen Sie die folgenden .NET Core-CLI-Befehle aus:

```console
dotnet ef migrations add Rating
dotnet ef database update
```

Der `ef migrations add`-Befehl weist das Framework an, Folgendes auszuführen:

* Das `Movie`-Modell mit dem Schema der `Movie`-Datenbank vergleichen.
* Code erstellen, um das Schema der Datenbank in das neue Modell zu migrieren.

Der Name „Rating“ ist beliebig und wird verwendet, um die Migrationsdatei zu benennen. Es ist hilfreich, einen aussagekräftigen Namen für die Migrationsdatei zu verwenden.

Der `ef database update`-Befehl weist das Framework an, die Schemaänderungen auf die Datenbank anzuwenden.

Wenn Sie alle Datensätze aus der Datenbank löschen, führt der Initialisierer ein Seeding für die Datenbank aus und fügt das Feld `Rating` hinzu. Dies ist über die „Löschen“-Links im Browser oder über ein SQLite-Tool möglich.

Eine weitere Möglichkeit ist, die Datenbank zu löschen und Migrationen zu verwenden, um die Datenbank neu zu erstellen. Um die Datenbank zu löschen, löschen Sie die Datenbankdatei (*MvcMovie.db*). Führen Sie dann den `ef database update`-Befehl aus: 

```console
dotnet ef database update
```

> [!NOTE]
> Viele Schemaänderungsvorgänge werden vom EF Core SQLite-Anbieter nicht unterstützt. Zum Beispiel wird Hinzufügen einer Spalten unterstützt, wogegen Entfernen einer Spalte nicht unterstützt wird. Wenn Sie eine Migration hinzufügen, um eine Spalte zu entfernen, wird der `ef migrations add`-Befehl erfolgreich ausgeführt, aber der `ef database update`-Befehl schlägt fehl. Sie können einige der Einschränkungen umgehen, indem Sie manuell Migrationscode schreiben, um eine Tabellenneuerstellung auszuführen. Eine Tabellenneuerstellung umfasst Umbenennen der vorhandenen Tabelle, Erstellen einer neuen Tabelle, Kopieren von Daten in die neue Tabelle und Löschen der alten Tabelle. Weitere Informationen finden Sie in den folgenden Ressourcen:
> * [SQLite EF Core-Datenbank-Anbieter-Einschränkungen](/ef/core/providers/sqlite/limitations)
> * [Anpassen des Migrationscodes](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Datenseeding](/ef/core/modeling/data-seeding)

---  
<!-- End of VS tabs -->

Führen Sie die App aus, und überprüfen Sie, ob Sie Filme mit dem Feld `Rating` erstellen/bearbeiten/anzeigen können. Wenn für die Datenbank kein Seed ausgeführt wird, legen Sie einen Haltepunkt in der `SeedData.Initialize`-Methode fest.

> [!div class="step-by-step"]
> [Zurück: Hinzufügen der Suche](xref:tutorials/razor-pages/search)
> [Weiter: Hinzufügen der Validierung](xref:tutorials/razor-pages/validation)
