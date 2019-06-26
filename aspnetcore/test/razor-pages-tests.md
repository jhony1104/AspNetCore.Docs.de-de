---
title: Razor-Seiten-Komponententests in ASP.NET Core
author: guardrex
description: Erfahren Sie, wie Sie Komponententests für Razor Pages-apps zu erstellen.
ms.author: riande
ms.custom: mvc
ms.date: 11/27/2017
uid: test/razor-pages-tests
ms.openlocfilehash: f0e47f975579dc114eaeda375028ec62696f58ed
ms.sourcegitcommit: 763af2cbdab0da62d1f1cfef4bcf787f251dfb5c
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2019
ms.locfileid: "67394738"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Razor-Seiten-Komponententests in ASP.NET Core

Von [Luke Latham](https://github.com/guardrex)

ASP.NET Core unterstützt Komponententests mit Razor Pages-apps. Tests für die Daten der Datenzugriffsschicht (DAL), und seitenmodellen sicherzustellen:

* Teile einer Razor Pages app arbeiten unabhängig und zusammen als eine Einheit während der Erstellung der app an.
* Klassen und Methoden eingeschränkt Bereiche der Aufgabe.
* Zusätzliche Dokumentation vorhanden ist, auf wie die app Verhalten soll.
* Regressionen, die Fehler aufgrund von Aktualisierungen von Code sind, befinden sich während des automatisierten erstellen und bereitstellen.

In diesem Thema wird davon ausgegangen, dass Sie ein grundlegendes Verständnis der Razor-Seiten-apps und Komponententests haben. Wenn Sie mit Razor Pages-apps oder Konzepte nicht vertraut sind, finden Sie unter den folgenden Themen:

* [Introduction to Razor Pages (Einführung in Razor Pages)](xref:razor-pages/index)
* [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Komponententest von C# -Code in .NET Core mit Dotnet Test und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispielprojekt besteht aus zwei apps:

| App         | Projektordner                        | Beschreibung |
| ----------- | ------------------------------------- | ----------- |
| Nachrichten-app | *src/RazorPagesTestSample*            | Ermöglicht es einem Benutzer hinzufügen, löschen, löschen Sie alle und Analysieren von Nachrichten. |
| Testen der app    | *tests/RazorPagesTestSample.Tests*    | Um einen Komponententest durchführen, die Nachrichten-app verwendet: Die Datenzugriffsebene (DAL) und Index-Seitenmodell. |

Die Tests können ausgeführt werden, verwenden integrierte Funktionen von einer IDE, z. B. [Visual Studio](https://visualstudio.microsoft.com). Wenn [Visual Studio Code](https://code.visualstudio.com/) oder der Befehlszeile, und führen Sie den folgenden Befehl an einer Eingabeaufforderung in das *tests/RazorPagesTestSample.Tests* Ordner:

```console
dotnet test
```

## <a name="message-app-organization"></a>Nachrichten-app-Organisation

Die Nachrichten-app ist eine einfache Razor-Seiten-Nachrichtensystem mit den folgenden Merkmalen:

* Die Indexseite der app (*Pages/Index.cshtml* und *Pages/Index.cshtml.cs*) eine Benutzeroberfläche und die Seite stellt Modellmethoden bereit, um zu steuern, das Hinzufügen, löschen und Analyse von Nachrichten (durchschnittliche Wörter pro Nachricht) .
* Eine Nachricht wird beschrieben, durch die `Message` Klasse (*Data/Message.cs*) mit zwei Eigenschaften: `Id` (Schlüssel) und `Text` (Nachricht). Die `Text` Eigenschaft ist erforderlich, und bis zu 200 Zeichen.
* Nachrichten werden gespeichert mit [des Entity Framework-in-Memory-Datenbank](/ef/core/providers/in-memory/)&#8224;.
* Die app enthält eine Datenzugriffsschicht (DAL) in der Datenbank-Kontextklasse `AppDbContext` (*Data/AppDbContext.cs*). Die DAL-Methoden werden markiert `virtual`, wodurch simulieren die Methoden für die Verwendung in den Tests.
* Wenn die Datenbank beim Start der app leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert. Diese *Nachrichten das Seeding* werden auch in den Tests verwendet.

&#8224;Das Thema EF [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory), wird erläutert, wie eine in-Memory-Datenbank für Tests mit MSTest verwenden. In diesem Thema verwendet die [xUnit](https://xunit.github.io/) Testframework. Test-Implementierung für andere Testframeworks und Konzepte sind ähnlich, aber nicht identisch.

Obwohl die app nicht das Repositorymuster verwenden und kein Beispiel für effektive ist der [Arbeitseinheit (UoW) Muster](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor-Seiten unterstützt diese Muster der Entwicklung. Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und [Testen von Controllerlogik](/aspnet/core/mvc/controllers/testing) (im Beispiel wird das Repositorymuster implementiert).

## <a name="test-app-organization"></a>Testen der app-Organisation

Die Test-app ist eine Konsolen-app in der *tests/RazorPagesTestSample.Tests* Ordner.

| Test-app-Ordner | Beschreibung |
| --------------- | ----------- |
| *UnitTests*     | <ul><li>*DataAccessLayerTest.cs* die Komponententests für die DAL enthält.</li><li>*IndexPageTests.cs* die Komponententests für das Seitenmodell Index enthält.</li></ul> |
| *Dienstprogramme*     | Enthält die `TestDbContextOptions` Methode verwendet, um die neue Datenbank zu Optionen für jede DAL-Komponententest erstellen, damit die Datenbank auf die Baseline-Bedingung für jeden Test zurückgesetzt wird. |

Das Testframework ist [xUnit](https://xunit.github.io/). Das Objekt mit dem mocking-Framework ist [Moq](https://github.com/moq/moq4).

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Komponententests der Daten der Datenzugriffsschicht (DAL)

Die Nachrichten-app hat eine DAL mit vier Methoden in der `AppDbContext` Klasse (*src/RazorPagesTestSample/Data/AppDbContext.cs*). Jede Methode hat ein oder zwei Komponententests im Test-app.

| DAL-Methode               | Funktion                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ruft eine `List<Message>` aus der Datenbank, sortiert nach der `Text` Eigenschaft. |
| `AddMessageAsync`        | Fügt eine `Message` in der Datenbank.                                          |
| `DeleteAllMessagesAsync` | Löscht alle `Message` Einträge aus der Datenbank.                           |
| `DeleteMessageAsync`     | Löscht eine einzelne `Message` aus der Datenbank durch `Id`.                      |

Komponententests von der DAL benötigen ["dbcontextoptions"](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) beim Erstellen eines neuen `AppDbContext` für jeden Test. Ein Ansatz zum Erstellen der `DbContextOptions` für jeden Test ist die Verwendung einer [Element](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptionsbuilder):

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Das Problem bei diesem Ansatz ist, dass jeder Test die Datenbank erhält in unabhängig von Ihrem Status der vorherige Test ihn verlassen. Dies kann problematisch sein, beim Versuch, eine unteilbare Einheit des Tests schreiben, die einander nicht beeinträchtigen. Erzwingen der `AppDbContext` um einen neuen Datenbankkontext für jeden Test zu verwenden, geben Sie einen `DbContextOptions` -Instanz, die basierend auf einer neuen Service-Anbieter. Die Test-app zeigt, wie dieses Vorgangs unter Verwendung der `Utilities` -Klassenmethode `TestDbContextOptions` (*tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Mithilfe der `DbContextOptions` in der DAL-Einheit Tests jeder Test automatisch mit einer neuen Datenbank-Instanz ausführen können:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Jeder Testmethode in der `DataAccessLayerTest` Klasse (*UnitTests/DataAccessLayerTest.cs*) folgt einem ähnlichen Arrange-Act-Assert-Muster:

1. Arrangieren: Die Datenbank konfiguriert ist, für den Test bzw. das erwartete Ergebnis definiert ist.
1. ACT: Der Test ausgeführt wird.
1. Bestätigen: Assertionen werden vorgenommen, um festzustellen, ob das Testergebnis erfolgreich ist.

Z. B. die `DeleteMessageAsync` Methode ist verantwortlich für das Entfernen einer einzelnen Nachricht identifizierte seine `Id` (*src/RazorPagesTestSample/Data/AppDbContext.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Es gibt zwei Tests für diese Methode ein. Ein Test überprüft, dass die Methode eine Nachricht löscht, wenn die Nachricht in der Datenbank vorhanden ist. Die andere Methode, Tests, die die Datenbank ändern, wenn keine Nachricht `Id` für die Löschung nicht vorhanden ist. Die `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` Methode wird unten gezeigt:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode führt zuerst im Schritt anordnen, findet die Vorbereitung der Schritt statt. Die seeding-Nachrichten werden abgerufen und in gespeicherten `seedMessages`. Die seeding-Nachrichten werden in der Datenbank gespeichert. Die Nachricht mit einer `Id` von `1` zum Löschen festgelegt ist. Wenn die `DeleteMessageAsync` Methode ausgeführt wird, wird die erwarteten Nachrichten müssen alle Nachrichten mit Ausnahme der mit einer `Id` von `1`. Die `expectedMessages` Variable steht für diese erwartete Ergebnis.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode dient: Die `DeleteMessageAsync` Methode ausgeführt wird, übergibt die `recId` von `1`:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Zum Schluss die Methode erhält den `Messages` aus dem Kontext und vergleicht ihn mit der `expectedMessages` behauptet werden, dass beide Werte gleich sind:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Um zu vergleichen, die die beiden `List<Message>` identisch sind:

* Die Nachrichten werden geordnet nach `Id`.
* Message-Paaren werden verglichen, auf die `Text` Eigenschaft.

Eine ähnliche Testmethode, `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` überprüft das Ergebnis des Versuchs, eine Nachricht zu löschen, die nicht vorhanden. In diesem Fall die erwarteten Nachrichten in der Datenbank übereinstimmen sollten auf die Nachrichten nach dem `DeleteMessageAsync` Methode ausgeführt wird. Keine Änderung an der Datenbank-Inhalt sollte vorhanden sein:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Komponententests von der Seite Modellmethoden

Eine andere Gruppe von Komponententests ist verantwortlich für Tests der Seitenmethoden-Modell. In der Nachrichten-app, die Index-seitenmodelle finden Sie in der `IndexModel` -Klasse im *src/RazorPagesTestSample/Pages/Index.cshtml.cs*.

| Seite "-Modell-Methode | Funktion |
| ----------------- | -------- |
| `OnGetAsync` | Ruft die Nachrichten aus der DAL für die Benutzeroberfläche mit dem `GetMessagesAsync` Methode. |
| `OnPostAddMessageAsync` | Wenn die `ModelState` gültig ist, ruft `AddMessageAsync` zum Hinzufügen einer Nachricht in der Datenbank. |
| `OnPostDeleteAllMessagesAsync` | Aufrufe `DeleteAllMessagesAsync` alle Nachrichten in der Datenbank zu löschen. |
| `OnPostDeleteMessageAsync` | Führt `DeleteMessageAsync` So löschen Sie eine Nachricht mit der `Id` angegebenen. |
| `OnPostAnalyzeMessagesAsync` | Wenn eine oder mehrere Nachrichten in der Datenbank sind, berechnet die durchschnittliche Anzahl von Wörtern pro Nachricht. |

Die Seite Modellmethoden werden getestet mit sieben Tests in der `IndexPageTests` Klasse (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*). Die Tests verwenden Sie das vertraute Arrange-Assert-Act-Muster. Diese Tests beziehen sich auf:

* Bestimmen, ob die Methoden das richtige Verhalten führen Sie bei der `ModelState` ist ungültig.
* Bestätigen die Methoden erzeugen der richtigen `IActionResult`.
* Überprüfen, dass Wert eigenschaftenzuweisungen ordnungsgemäß vorgenommen werden.

Diese Gruppe von Tests simulieren häufig die Methoden von der DAL zum Erzeugen der Erwarteter Daten für den Act-Schritt, in eine Seitenmethode-Modell ausgeführt wird. Z. B. die `GetMessagesAsync` Methode der `AppDbContext` simuliert, um die Ausgabe zu erzeugen. Wenn diese Methode von eine Seitenmethode-Modell ausgeführt wird, gibt das Mock das Ergebnis zurück. Die Daten stammen nicht aus der Datenbank. Dadurch wird die vorhersagbare und zuverlässige testbedingungen für die Verwendung von der DAL in die Seite Modell Tests erstellt.

Die `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` testen zeigt wie die `GetMessagesAsync` -Methode simuliert, für das Seitenmodell:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Wenn die `OnGetAsync` Methode in der Schritt ausgeführt wird, ruft des Seitenmodells `GetMessagesAsync` Methode.

Komponententests für Schritt (*tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage` Seitenmodells `OnGetAsync` Methode (*src/RazorPagesTestSample/Pages/Index.cshtml.cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Die `GetMessagesAsync` -Methode in der die DAL gibt nicht das Ergebnis für diesen Methodenaufruf zurück. Die simulierte Version der Methode gibt das Ergebnis zurück.

In der `Assert` einen Schritt oder die tatsächlichen Nachrichten (`actualMessages`) zugewiesen sind, aus der `Messages` Eigenschaft des Seitenmodells. Eine typüberprüfung wird auch ausgeführt, wenn die Nachrichten zugeordnet sind. Die erwarteten und tatsächlichen Nachrichten werden bei einem Vergleich ihrer `Text` Eigenschaften. Der Test bestimmt, dass die beiden `List<Message>` Instanzen enthalten die gleichen Meldungen.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Andere Tests in dieser Gruppe erstellen Seite Model-Objekte, die implizit enthalten die `DefaultHttpContext`, `ModelStateDictionary`, wird eine `ActionContext` Herstellen der `PageContext`, `ViewDataDictionary`, und ein `PageContext`. Diese sind hilfreich bei der Ausführung von Tests. Beispielsweise die Nachrichten-app richtet eine `ModelState` Fehler mit `AddModelError` überprüfen, ob ein gültiger `PageResult` wird zurückgegeben, wenn `OnPostAddMessageAsync` ausgeführt wird:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Komponententest von C# -Code in .NET Core mit Dotnet Test und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Testen von Controllern](xref:mvc/controllers/testing)
* [Komponententests für Code](/visualstudio/test/unit-test-your-code) (Visual Studio)
* [Integrationstests](xref:test/integration-tests)
* [xUnit.net](https://xunit.github.io/)
* [Erste Schritte mit xUnit.net (.NET Core bzw. ASP.NET Core)](https://xunit.github.io/docs/getting-started-dotnet-core)
* [Moq](https://github.com/moq/moq4)
* [Moq-Schnellstart](https://github.com/Moq/moq4/wiki/Quickstart)
