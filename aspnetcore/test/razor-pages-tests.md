---
title: Komponententests Razor pages in ASP.net Core
author: guardrex
description: Erfahren Sie, wie Sie Komponententests für Razor Pages-Apps erstellen.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/14/2019
uid: test/razor-pages-tests
ms.openlocfilehash: afac97d686ef190ebb92d20a55a15dd774b0d1de
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081428"
---
# <a name="razor-pages-unit-tests-in-aspnet-core"></a>Komponententests Razor pages in ASP.net Core

Von [Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

ASP.net Core unterstützt Komponententests von Razor Pages-apps. Tests der Datenzugriffs Schicht (Data Access Layer, DAL) und Seiten Modelle helfen dabei, Folgendes sicherzustellen:

* Teile einer Razor Pages-App funktionieren während der APP-Erstellung unabhängig und zusammen als Einheit.
* Klassen und Methoden weisen eingeschränkte Zuständigkeitsbereiche auf.
* Es gibt zusätzliche Dokumentation zum Verhalten der app.
* Regressionen, bei denen es sich um Fehler bei Updates des Codes handelt, werden während der automatisierten Bereitstellung und Bereitstellung gefunden.

In diesem Thema wird davon ausgegangen, dass Sie über grundlegende Kenntnisse der Razor Pages-apps und-Komponententests verfügen. Wenn Sie mit Razor Pages-Apps oder Test Konzepten nicht vertraut sind, finden Sie weitere Informationen in den folgenden Themen:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Komponententests C# in .net Core mithilfe von "dotnet Test" und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispiel Projekt besteht aus zwei apps:

| App         | Projektordner                     | Beschreibung |
| ----------- | ---------------------------------- | ----------- |
| Message-App | *src/RazorPagesTestSample*         | Ermöglicht es einem Benutzer, eine Nachricht hinzuzufügen, eine Nachricht zu löschen, alle Nachrichten zu löschen und Nachrichten zu analysieren (Ermitteln der durchschnittlichen Anzahl von Wörtern pro Nachricht). |
| Test-App    | *tests/RazorPagesTestSample.Tests* | Wird verwendet, um Komponententests für das dal-und Index page-Modell der Message-App durchzusetzen. |

Die Tests können mit den integrierten Testfunktionen einer IDE ausgeführt werden, z. b. [Visual Studio](/visualstudio/test/unit-test-your-code) oder [Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl an einer Eingabeaufforderung im Ordner *Tests/razorpgestestsample. Tests* aus:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Nachrichten-APP-Organisation

Bei der Message-App handelt es sich um ein Razor Pages Nachrichtensystem mit folgenden Merkmalen:

* Die Index Seite der APP (*pages/index. cshtml* und *pages/index. cshtml. cs*) stellt eine UI-und Seiten Modell Methode bereit, mit der Sie das Hinzufügen, löschen und Analysieren von Nachrichten steuern können (suchen Sie die durchschnittliche Anzahl von Wörtern pro Nachricht).
* Eine `Message` Meldung wird von der-Klasse (*Data/Message. cs*) mit zwei Eigenschaften beschrieben `Id` : (Key) `Text` und (Message). Die `Text` -Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe [der in-Memory Database](/ef/core/providers/in-memory/)&#8224;Entity Framework gespeichert.
* Die app enthält eine Dal in der Daten Bank Kontext Klasse `AppDbContext` (*Data/appdbcontext. cs*). Die DAL-Methoden sind `virtual`gekennzeichnet, sodass Sie die Methoden für die Verwendung in den Tests simulieren können.
* Wenn die Datenbank beim Starten der APP leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert. Diese *Seeding Nachrichten* werden auch in Tests verwendet.

&#8224;Das EF-Thema [Testen mit inMemory](/ef/core/miscellaneous/testing/in-memory)erläutert, wie Sie eine in-Memory Database für Tests mit MSTest verwenden. In diesem Thema wird das [xUnit](https://xunit.github.io/) -Test Framework verwendet. Testkonzepte und Test Implementierungen in verschiedenen Test-Frameworks sind ähnlich, aber nicht identisch.

Obwohl die Beispiel-APP nicht das Repository-Muster verwendet und kein effektives Beispiel für das [Muster der Arbeitseinheit (Unit of Work, UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html)ist, werden Razor Pages diese Entwicklungsmuster unterstützt. Weitere Informationen finden Sie unter [Entwerfen der Infrastruktur-Persistenzebene](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und (im Beispiel wird das Repository- <xref:mvc/controllers/testing> Muster implementiert).

## <a name="test-app-organization"></a>Testen der APP-Organisation

Bei der Test-App handelt es sich um eine Konsolen-App im Ordner *Tests/razorpfgestestsample. Tests* .

| Test-App-Ordner | Beschreibung |
| --------------- | ----------- |
| *Unittests*     | <ul><li>*DataAccessLayerTest.cs* enthält die Komponententests für die DAL.</li><li>*IndexPageTests.cs* enthält die Komponententests für das Index Seiten Modell.</li></ul> |
| *Energie*     | Enthält die `TestDbContextOptions` Methode, mit der neue Daten Bank Kontextoptionen für jeden dal-UnitTest erstellt werden, sodass die Datenbank für jeden Test auf Ihren Baseline-Zustand zurückgesetzt wird. |

Das Test Framework ist " [xUnit](https://xunit.github.io/)". Das Objekt zum Simulieren von Objekten ist " [muq](https://github.com/moq/moq4)".

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Komponententests der Datenzugriffs Schicht (Data Access Layer, DAL)

Die Message-App verfügt über eine dal mit vier Methoden, `AppDbContext` die in der-Klasse enthalten sind (*src/razorpgestestsample/Data/appdbcontext. cs*). Jede Methode verfügt über einen oder zwei Komponententests in der Test-App.

| Dal-Methode               | Funktion                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ruft ein `List<Message>` aus der Datenbank ab, sortiert `Text` nach der-Eigenschaft. |
| `AddMessageAsync`        | Fügt der `Message` Datenbank einen hinzu.                                          |
| `DeleteAllMessagesAsync` | Löscht alle `Message` Einträge aus der Datenbank.                           |
| `DeleteMessageAsync`     | Löscht eine einzelne `Message` aus der Datenbank durch `Id`.                      |

Komponententests der dal erfordern <xref:Microsoft.EntityFrameworkCore.DbContextOptions> , wenn für jeden Test ein neuer `AppDbContext` erstellt wird. Ein Ansatz zum Erstellen der `DbContextOptions` für jeden Test ist die Verwendung einer <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Das Problem bei diesem Ansatz besteht darin, dass jeder Test die Datenbank in dem Zustand empfängt, in dem der vorherige Test ihn ausgelassen hat. Dies kann problematisch sein, wenn versucht wird, atomarische Komponententests zu schreiben, die sich nicht gegenseitig beeinträchtigen. Wenn Sie erzwingen `AppDbContext` möchten, dass für jeden Test ein neuer Daten Bank Kontext verwendet `DbContextOptions` wird, stellen Sie eine-Instanz bereit, die auf einem neuen Dienstanbieter basiert. Die Test-App zeigt, wie Sie dies mithilfe `Utilities` der- `TestDbContextOptions` Klassenmethode (*Tests/razorpgestestsample. Tests/Utilities/Utilities. cs*) erreichen:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Durch die `DbContextOptions` Verwendung von in den dal-Komponententests kann jeder Test atomarisch mit einer neuen Daten Bank Instanz ausgeführt werden:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Jede Testmethode in der `DataAccessLayerTest` -Klasse (*Unittests/dataaccesslayertest. cs*) folgt einem ähnlichen Arrange-Act-Assert-Muster:

1. Neu Die Datenbank ist für den Test konfiguriert und/oder das erwartete Ergebnis ist definiert.
1. Auftreten Der Test wird ausgeführt.
1. Machung Assertionen werden erstellt, um zu bestimmen, ob das Testergebnis ein Erfolg ist.

Beispielsweise ist die `DeleteMessageAsync` -Methode für das Entfernen einer einzelnen Nachricht zuständig, die `Id` durch its (*src/razorpgestestsample/Data/appdbcontext. cs*) identifiziert wird:

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Für diese Methode sind zwei Tests vorhanden. Ein Test prüft, ob die-Methode eine Meldung löscht, wenn die Meldung in der Datenbank vorhanden ist. Die andere Methode testet, ob die Datenbank nicht geändert wird, `Id` wenn die Meldung zum Löschen nicht vorhanden ist. Die `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` -Methode wird unten dargestellt:

[!code-csharp[](razor-pages-tests/samples_snapshot/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Zuerst führt die-Methode den Anordnungs Schritt aus, bei dem die Vorbereitung für den Act-Schritt stattfindet. Die Seeding Nachrichten werden abgerufen und in `seedMessages`gespeichert. Die Seeding Nachrichten werden in der Datenbank gespeichert. Die Meldung mit `Id` `1` dem-Wert wird zum Löschen festgelegt. Wenn die `DeleteMessageAsync` -Methode ausgeführt wird, sollten die erwarteten Nachrichten alle Meldungen `Id` mit Ausnahme derjenigen mit `1`dem-Wert aufweisen. Die `expectedMessages` Variable stellt dieses erwartete Ergebnis dar.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode verhält sich wie folgt: Die `DeleteMessageAsync` -Methode wird ausgeführt und übergibt `recId` den `1`von:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Schließlich ruft die-Methode den `Messages` aus dem Kontext ab und vergleicht ihn mit `expectedMessages` dem, der behauptet, dass die beiden identisch sind:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Um zu vergleichen, dass die beiden `List<Message>` identisch sind:

* Die Nachrichten werden nach `Id`geordnet.
* Nachrichten Paare werden mit der `Text` -Eigenschaft verglichen.

Eine ähnliche Testmethode überprüft `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` das Ergebnis des Versuchs, eine Meldung zu löschen, die nicht vorhanden ist. In diesem Fall sollten die erwarteten Nachrichten in der Datenbank mit den tatsächlichen Nachrichten übereinstimmen, `DeleteMessageAsync` nachdem die Methode ausgeführt wurde. Der Inhalt der Datenbank sollte nicht geändert werden:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Komponententests der Seiten Modell Methoden

Ein weiterer Satz von Komponententests ist für Tests von Seiten Modell Methoden verantwortlich. In der Message-App befinden sich die Index Seiten Modelle in der `IndexModel` -Klasse in *src/razorpgestestsample/pages/index. cshtml. cs*.

| Page Model-Methode | Funktion |
| ----------------- | -------- |
| `OnGetAsync` | Ruft die Nachrichten aus der dal für die Benutzeroberfläche `GetMessagesAsync` mithilfe der-Methode ab. |
| `OnPostAddMessageAsync` | Wenn [Model State](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) gültig ist, wird von `AddMessageAsync` aufgerufen, um der Datenbank eine Meldung hinzuzufügen. |
| `OnPostDeleteAllMessagesAsync` | Ruft `DeleteAllMessagesAsync` auf, um alle Nachrichten in der Datenbank zu löschen. |
| `OnPostDeleteMessageAsync` | Wird `DeleteMessageAsync` ausgeführt, um eine Meldung mit `Id` der angegebenen zu löschen. |
| `OnPostAnalyzeMessagesAsync` | Wenn eine oder mehrere Nachrichten in der Datenbank vorliegen, berechnet die durchschnittliche Anzahl von Wörtern pro Nachricht. |

Die Seiten Modell Methoden werden mithilfe von sieben Tests in der `IndexPageTests` -Klasse getestet (*Tests/razorpgestestsample. Tests/Unittests/indexpagetests. cs*). Bei den Tests wird das vertraute "Arrange-Assert-Act"-Muster verwendet. Diese Tests konzentrieren sich auf:

* Ermitteln, ob die Methoden das richtige Verhalten aufweisen, wenn der [modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) ungültig ist.
* Die Bestätigung der Methoden erzeugt den <xref:Microsoft.AspNetCore.Mvc.IActionResult>richtigen.
* Es wird überprüft, ob die Zuweisung von Eigenschafts Werten korrekt ist

Diese Gruppe von Tests simulierten die Methoden der dal häufig, um erwartete Daten für den Act-Schritt zu liefern, in dem eine Seiten Modell Methode ausgeführt wird. Beispielsweise `AppDbContext` wird die `GetMessagesAsync` -Methode der zum Ausgeben der Ausgabe simuliert. Wenn eine Seiten Modell Methode diese Methode ausführt, gibt das Mock das Ergebnis zurück. Die Daten stammen nicht aus der Datenbank. Dadurch werden vorhersagbare, zuverlässige Testbedingungen für die Verwendung der Dal in den Seiten Modell Tests erstellt.

Der `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` Test zeigt, wie `GetMessagesAsync` die-Methode für das Seiten Modell verspottet wird:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Wenn die `OnGetAsync` -Methode im Act-Schritt ausgeführt wird, wird die- `GetMessagesAsync` Methode des Seiten Modells aufgerufen.

UnitTest-Act-Schritt (*Tests/razorpgestestsample. Tests/Unittests/indexpagetests. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`Methode des `OnGetAsync` Seiten Modells (*src/razorumgestestsample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/3.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Die `GetMessagesAsync` -Methode in der dal gibt nicht das Ergebnis für diesen Methoden aufzurufen. Die simulierte Version der Methode gibt das Ergebnis zurück.

In diesem `Assert` Schritt werden die tatsächlichen Nachrichten`actualMessages`() aus der `Messages` -Eigenschaft des Seiten Modells zugewiesen. Eine Typüberprüfung wird auch ausgeführt, wenn die Nachrichten zugewiesen werden. Die erwarteten und tatsächlichen Nachrichten werden anhand ihrer `Text` Eigenschaften verglichen. Der Test bestätigt, dass die `List<Message>` beiden Instanzen die gleichen Nachrichten enthalten.

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bei anderen Tests in <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>dieser Gruppe <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>werden Seiten Modell Objekte erstellt, die,, `PageContext`, <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext`zum Einrichten von, a `ViewDataDictionary`und enthalten. Diese sind beim Durchführen von Tests nützlich. Beispielsweise wird in der Nachrichten-APP `ModelState` ein Fehler <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> mit der Meldung erstellt, <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> dass bei `OnPostAddMessageAsync` Ausführung von ein gültiger zurückgegeben wird:

[!code-csharp[](razor-pages-tests/samples/3.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Komponententests C# in .net Core mithilfe von "dotnet Test" und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* Komponenten [Tests für Ihren Code](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Erstellen einer vollständigen .NET Core-Lösung unter macOS mit Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Einstieg in xUnit.net: Verwenden von .net Core mit der .NET SDK-Befehlszeile](https://xunit.github.io/docs/getting-started-dotnet-core)
* [MOQ](https://github.com/moq/moq4)
* [Schnellstart für die Schnellstartanleitung](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.net Core unterstützt Komponententests von Razor Pages-apps. Tests der Datenzugriffs Schicht (Data Access Layer, DAL) und Seiten Modelle helfen dabei, Folgendes sicherzustellen:

* Teile einer Razor Pages-App funktionieren während der APP-Erstellung unabhängig und zusammen als Einheit.
* Klassen und Methoden weisen eingeschränkte Zuständigkeitsbereiche auf.
* Es gibt zusätzliche Dokumentation zum Verhalten der app.
* Regressionen, bei denen es sich um Fehler bei Updates des Codes handelt, werden während der automatisierten Bereitstellung und Bereitstellung gefunden.

In diesem Thema wird davon ausgegangen, dass Sie über grundlegende Kenntnisse der Razor Pages-apps und-Komponententests verfügen. Wenn Sie mit Razor Pages-Apps oder Test Konzepten nicht vertraut sind, finden Sie weitere Informationen in den folgenden Themen:

* <xref:razor-pages/index>
* <xref:tutorials/razor-pages/razor-pages-start>
* [Komponententests C# in .net Core mithilfe von "dotnet Test" und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/razor-pages-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Das Beispiel Projekt besteht aus zwei apps:

| App         | Projektordner                     | Beschreibung |
| ----------- | ---------------------------------- | ----------- |
| Message-App | *src/RazorPagesTestSample*         | Ermöglicht es einem Benutzer, eine Nachricht hinzuzufügen, eine Nachricht zu löschen, alle Nachrichten zu löschen und Nachrichten zu analysieren (Ermitteln der durchschnittlichen Anzahl von Wörtern pro Nachricht). |
| Test-App    | *tests/RazorPagesTestSample.Tests* | Wird verwendet, um Komponententests für das dal-und Index page-Modell der Message-App durchzusetzen. |

Die Tests können mit den integrierten Testfunktionen einer IDE ausgeführt werden, z. b. [Visual Studio](/visualstudio/test/unit-test-your-code) oder [Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution). Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl an einer Eingabeaufforderung im Ordner *Tests/razorpgestestsample. Tests* aus:

```dotnetcli
dotnet test
```

## <a name="message-app-organization"></a>Nachrichten-APP-Organisation

Bei der Message-App handelt es sich um ein Razor Pages Nachrichtensystem mit folgenden Merkmalen:

* Die Index Seite der APP (*pages/index. cshtml* und *pages/index. cshtml. cs*) stellt eine UI-und Seiten Modell Methode bereit, mit der Sie das Hinzufügen, löschen und Analysieren von Nachrichten steuern können (suchen Sie die durchschnittliche Anzahl von Wörtern pro Nachricht).
* Eine `Message` Meldung wird von der-Klasse (*Data/Message. cs*) mit zwei Eigenschaften beschrieben `Id` : (Key) `Text` und (Message). Die `Text` -Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe [der in-Memory Database](/ef/core/providers/in-memory/)&#8224;Entity Framework gespeichert.
* Die app enthält eine Dal in der Daten Bank Kontext Klasse `AppDbContext` (*Data/appdbcontext. cs*). Die DAL-Methoden sind `virtual`gekennzeichnet, sodass Sie die Methoden für die Verwendung in den Tests simulieren können.
* Wenn die Datenbank beim Starten der APP leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert. Diese *Seeding Nachrichten* werden auch in Tests verwendet.

&#8224;Das EF-Thema [Testen mit inMemory](/ef/core/miscellaneous/testing/in-memory)erläutert, wie Sie eine in-Memory Database für Tests mit MSTest verwenden. In diesem Thema wird das [xUnit](https://xunit.github.io/) -Test Framework verwendet. Testkonzepte und Test Implementierungen in verschiedenen Test-Frameworks sind ähnlich, aber nicht identisch.

Obwohl die Beispiel-APP nicht das Repository-Muster verwendet und kein effektives Beispiel für das [Muster der Arbeitseinheit (Unit of Work, UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html)ist, werden Razor Pages diese Entwicklungsmuster unterstützt. Weitere Informationen finden Sie unter [Entwerfen der Infrastruktur-Persistenzebene](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und (im Beispiel wird das Repository- <xref:mvc/controllers/testing> Muster implementiert).

## <a name="test-app-organization"></a>Testen der APP-Organisation

Bei der Test-App handelt es sich um eine Konsolen-App im Ordner *Tests/razorpfgestestsample. Tests* .

| Test-App-Ordner | Beschreibung |
| --------------- | ----------- |
| *Unittests*     | <ul><li>*DataAccessLayerTest.cs* enthält die Komponententests für die DAL.</li><li>*IndexPageTests.cs* enthält die Komponententests für das Index Seiten Modell.</li></ul> |
| *Energie*     | Enthält die `TestDbContextOptions` Methode, mit der neue Daten Bank Kontextoptionen für jeden dal-UnitTest erstellt werden, sodass die Datenbank für jeden Test auf Ihren Baseline-Zustand zurückgesetzt wird. |

Das Test Framework ist " [xUnit](https://xunit.github.io/)". Das Objekt zum Simulieren von Objekten ist " [muq](https://github.com/moq/moq4)".

## <a name="unit-tests-of-the-data-access-layer-dal"></a>Komponententests der Datenzugriffs Schicht (Data Access Layer, DAL)

Die Message-App verfügt über eine dal mit vier Methoden, `AppDbContext` die in der-Klasse enthalten sind (*src/razorpgestestsample/Data/appdbcontext. cs*). Jede Methode verfügt über einen oder zwei Komponententests in der Test-App.

| Dal-Methode               | Funktion                                                                   |
| ------------------------ | -------------------------------------------------------------------------- |
| `GetMessagesAsync`       | Ruft ein `List<Message>` aus der Datenbank ab, sortiert `Text` nach der-Eigenschaft. |
| `AddMessageAsync`        | Fügt der `Message` Datenbank einen hinzu.                                          |
| `DeleteAllMessagesAsync` | Löscht alle `Message` Einträge aus der Datenbank.                           |
| `DeleteMessageAsync`     | Löscht eine einzelne `Message` aus der Datenbank durch `Id`.                      |

Komponententests der dal erfordern <xref:Microsoft.EntityFrameworkCore.DbContextOptions> , wenn für jeden Test ein neuer `AppDbContext` erstellt wird. Ein Ansatz zum Erstellen der `DbContextOptions` für jeden Test ist die Verwendung einer <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder>:

```csharp
var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
    .UseInMemoryDatabase("InMemoryDb");

using (var db = new AppDbContext(optionsBuilder.Options))
{
    // Use the db here in the unit test.
}
```

Das Problem bei diesem Ansatz besteht darin, dass jeder Test die Datenbank in dem Zustand empfängt, in dem der vorherige Test ihn ausgelassen hat. Dies kann problematisch sein, wenn versucht wird, atomarische Komponententests zu schreiben, die sich nicht gegenseitig beeinträchtigen. Wenn Sie erzwingen `AppDbContext` möchten, dass für jeden Test ein neuer Daten Bank Kontext verwendet `DbContextOptions` wird, stellen Sie eine-Instanz bereit, die auf einem neuen Dienstanbieter basiert. Die Test-App zeigt, wie Sie dies mithilfe `Utilities` der- `TestDbContextOptions` Klassenmethode (*Tests/razorpgestestsample. Tests/Utilities/Utilities. cs*) erreichen:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/Utilities/Utilities.cs?name=snippet1)]

Durch die `DbContextOptions` Verwendung von in den dal-Komponententests kann jeder Test atomarisch mit einer neuen Daten Bank Instanz ausgeführt werden:

```csharp
using (var db = new AppDbContext(Utilities.TestDbContextOptions()))
{
    // Use the db here in the unit test.
}
```

Jede Testmethode in der `DataAccessLayerTest` -Klasse (*Unittests/dataaccesslayertest. cs*) folgt einem ähnlichen Arrange-Act-Assert-Muster:

1. Neu Die Datenbank ist für den Test konfiguriert und/oder das erwartete Ergebnis ist definiert.
1. Auftreten Der Test wird ausgeführt.
1. Machung Assertionen werden erstellt, um zu bestimmen, ob das Testergebnis ein Erfolg ist.

Beispielsweise ist die `DeleteMessageAsync` -Methode für das Entfernen einer einzelnen Nachricht zuständig, die `Id` durch its (*src/razorpgestestsample/Data/appdbcontext. cs*) identifiziert wird:

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Data/AppDbContext.cs?name=snippet4)]

Für diese Methode sind zwei Tests vorhanden. Ein Test prüft, ob die-Methode eine Meldung löscht, wenn die Meldung in der Datenbank vorhanden ist. Die andere Methode testet, ob die Datenbank nicht geändert wird, `Id` wenn die Meldung zum Löschen nicht vorhanden ist. Die `DeleteMessageAsync_MessageIsDeleted_WhenMessageIsFound` -Methode wird unten dargestellt:

[!code-csharp[](razor-pages-tests/samples_snapshot/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Zuerst führt die-Methode den Anordnungs Schritt aus, bei dem die Vorbereitung für den Act-Schritt stattfindet. Die Seeding Nachrichten werden abgerufen und in `seedMessages`gespeichert. Die Seeding Nachrichten werden in der Datenbank gespeichert. Die Meldung mit `Id` `1` dem-Wert wird zum Löschen festgelegt. Wenn die `DeleteMessageAsync` -Methode ausgeführt wird, sollten die erwarteten Nachrichten alle Meldungen `Id` mit Ausnahme derjenigen mit `1`dem-Wert aufweisen. Die `expectedMessages` Variable stellt dieses erwartete Ergebnis dar.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet1)]

Die Methode verhält sich wie folgt: Die `DeleteMessageAsync` -Methode wird ausgeführt und übergibt `recId` den `1`von:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet2)]

Schließlich ruft die-Methode den `Messages` aus dem Kontext ab und vergleicht ihn mit `expectedMessages` dem, der behauptet, dass die beiden identisch sind:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet3)]

Um zu vergleichen, dass die beiden `List<Message>` identisch sind:

* Die Nachrichten werden nach `Id`geordnet.
* Nachrichten Paare werden mit der `Text` -Eigenschaft verglichen.

Eine ähnliche Testmethode überprüft `DeleteMessageAsync_NoMessageIsDeleted_WhenMessageIsNotFound` das Ergebnis des Versuchs, eine Meldung zu löschen, die nicht vorhanden ist. In diesem Fall sollten die erwarteten Nachrichten in der Datenbank mit den tatsächlichen Nachrichten übereinstimmen, `DeleteMessageAsync` nachdem die Methode ausgeführt wurde. Der Inhalt der Datenbank sollte nicht geändert werden:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/DataAccessLayerTest.cs?name=snippet4)]

## <a name="unit-tests-of-the-page-model-methods"></a>Komponententests der Seiten Modell Methoden

Ein weiterer Satz von Komponententests ist für Tests von Seiten Modell Methoden verantwortlich. In der Message-App befinden sich die Index Seiten Modelle in der `IndexModel` -Klasse in *src/razorpgestestsample/pages/index. cshtml. cs*.

| Page Model-Methode | Funktion |
| ----------------- | -------- |
| `OnGetAsync` | Ruft die Nachrichten aus der dal für die Benutzeroberfläche `GetMessagesAsync` mithilfe der-Methode ab. |
| `OnPostAddMessageAsync` | Wenn [Model State](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) gültig ist, wird von `AddMessageAsync` aufgerufen, um der Datenbank eine Meldung hinzuzufügen. |
| `OnPostDeleteAllMessagesAsync` | Ruft `DeleteAllMessagesAsync` auf, um alle Nachrichten in der Datenbank zu löschen. |
| `OnPostDeleteMessageAsync` | Wird `DeleteMessageAsync` ausgeführt, um eine Meldung mit `Id` der angegebenen zu löschen. |
| `OnPostAnalyzeMessagesAsync` | Wenn eine oder mehrere Nachrichten in der Datenbank vorliegen, berechnet die durchschnittliche Anzahl von Wörtern pro Nachricht. |

Die Seiten Modell Methoden werden mithilfe von sieben Tests in der `IndexPageTests` -Klasse getestet (*Tests/razorpgestestsample. Tests/Unittests/indexpagetests. cs*). Bei den Tests wird das vertraute "Arrange-Assert-Act"-Muster verwendet. Diese Tests konzentrieren sich auf:

* Ermitteln, ob die Methoden das richtige Verhalten aufweisen, wenn der [modelstate](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary) ungültig ist.
* Die Bestätigung der Methoden erzeugt den <xref:Microsoft.AspNetCore.Mvc.IActionResult>richtigen.
* Es wird überprüft, ob die Zuweisung von Eigenschafts Werten korrekt ist

Diese Gruppe von Tests simulierten die Methoden der dal häufig, um erwartete Daten für den Act-Schritt zu liefern, in dem eine Seiten Modell Methode ausgeführt wird. Beispielsweise `AppDbContext` wird die `GetMessagesAsync` -Methode der zum Ausgeben der Ausgabe simuliert. Wenn eine Seiten Modell Methode diese Methode ausführt, gibt das Mock das Ergebnis zurück. Die Daten stammen nicht aus der Datenbank. Dadurch werden vorhersagbare, zuverlässige Testbedingungen für die Verwendung der Dal in den Seiten Modell Tests erstellt.

Der `OnGetAsync_PopulatesThePageModel_WithAListOfMessages` Test zeigt, wie `GetMessagesAsync` die-Methode für das Seiten Modell verspottet wird:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet1&highlight=3-4)]

Wenn die `OnGetAsync` -Methode im Act-Schritt ausgeführt wird, wird die- `GetMessagesAsync` Methode des Seiten Modells aufgerufen.

UnitTest-Act-Schritt (*Tests/razorpgestestsample. Tests/Unittests/indexpagetests. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet2)]

`IndexPage`Methode des `OnGetAsync` Seiten Modells (*src/razorumgestestsample/pages/index. cshtml. cs*):

[!code-csharp[](razor-pages-tests/samples/2.x/src/RazorPagesTestSample/Pages/Index.cshtml.cs?name=snippet1&highlight=3)]

Die `GetMessagesAsync` -Methode in der dal gibt nicht das Ergebnis für diesen Methoden aufzurufen. Die simulierte Version der Methode gibt das Ergebnis zurück.

In diesem `Assert` Schritt werden die tatsächlichen Nachrichten`actualMessages`() aus der `Messages` -Eigenschaft des Seiten Modells zugewiesen. Eine Typüberprüfung wird auch ausgeführt, wenn die Nachrichten zugewiesen werden. Die erwarteten und tatsächlichen Nachrichten werden anhand ihrer `Text` Eigenschaften verglichen. Der Test bestätigt, dass die `List<Message>` beiden Instanzen die gleichen Nachrichten enthalten.

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet3)]

Bei anderen Tests in <xref:Microsoft.AspNetCore.Http.DefaultHttpContext>dieser Gruppe <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary>werden Seiten Modell Objekte erstellt, die,, `PageContext`, <xref:Microsoft.AspNetCore.Mvc.ActionContext> `PageContext`zum Einrichten von, a `ViewDataDictionary`und enthalten. Diese sind beim Durchführen von Tests nützlich. Beispielsweise wird in der Nachrichten-APP `ModelState` ein Fehler <xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.AddModelError*> mit der Meldung erstellt, <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageResult> dass bei `OnPostAddMessageAsync` Ausführung von ein gültiger zurückgegeben wird:

[!code-csharp[](razor-pages-tests/samples/2.x/tests/RazorPagesTestSample.Tests/UnitTests/IndexPageTests.cs?name=snippet4&highlight=11,26,29,32)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Komponententests C# in .net Core mithilfe von "dotnet Test" und xUnit](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:mvc/controllers/testing>
* Komponenten [Tests für Ihren Code](/visualstudio/test/unit-test-your-code) (Visual Studio)
* <xref:test/integration-tests>
* [xUnit.net](https://xunit.github.io/)
* [Erstellen einer vollständigen .NET Core-Lösung unter macOS mit Visual Studio für Mac](/dotnet/core/tutorials/using-on-mac-vs-full-solution)
* [Einstieg in xUnit.net: Verwenden von .net Core mit der .NET SDK-Befehlszeile](https://xunit.github.io/docs/getting-started-dotnet-core)
* [MOQ](https://github.com/moq/moq4)
* [Schnellstart für die Schnellstartanleitung](https://github.com/Moq/moq4/wiki/Quickstart)

::: moniker-end
