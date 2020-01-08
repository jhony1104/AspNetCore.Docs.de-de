---
title: Integrationstests in ASP.net Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Integrationstests sicherstellen, dass die Komponenten einer App auf der Infrastrukturebene ordnungsgemäß funktionieren, einschließlich der Datenbank, dem Dateisystem und dem Netzwerk.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
uid: test/integration-tests
ms.openlocfilehash: ccee8957a72da0eb5d870b1bd184ee1ea146a0e6
ms.sourcegitcommit: 79850db9e79b1705b89f466c6f2c961ff15485de
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75693790"
---
# <a name="integration-tests-in-aspnet-core"></a>Integrationstests in ASP.net Core

Von [Luke Latham](https://github.com/guardrex), [Javier calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/)und [Jos van der til](https://jvandertil.nl)

::: moniker range=">= aspnetcore-3.0"

Integrationstests stellen sicher, dass die Komponenten einer APP auf einer Ebene, die die unterstützende Infrastruktur der APP umfasst, wie z. b. die Datenbank, das Dateisystem und das Netzwerk, ordnungsgemäß funktionieren. ASP.net Core unterstützt Integrationstests mithilfe eines Komponenten Test-Frameworks mit einem Testweb Host und einem in-Memory-Testserver.

In diesem Thema wird ein grundlegendes Verständnis von Komponententests vorausgesetzt. Wenn Sie mit den Test Konzepten nicht vertraut sind, finden Sie weitere Informationen unter Komponenten [Tests in .net Core und .NET Standard](/dotnet/core/testing/) Thema und zugehörige Inhalte.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Bei der Beispiel-App handelt es sich um eine Razor Pages-APP, die grundlegend mit der Razor Pages vertraut. Wenn Sie mit Razor Pages nicht vertraut sind, lesen Sie die folgenden Themen:

* [Introduction to Razor Pages (Einführung in Razor Pages)](xref:razor-pages/index)
* [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Komponententests für Razor-Seiten](xref:test/razor-pages-tests)

> [!NOTE]
> Zum Testen von Spas empfiehlt es sich, ein Tool wie [Selenium](https://www.seleniumhq.org/)zu verwenden, mit dem ein Browser automatisiert werden kann.

## <a name="introduction-to-integration-tests"></a>Einführung in Integrationstests

Integrationstests bewerten die Komponenten einer APP auf einer umfassenderen Ebene als Komponenten [Tests](/dotnet/core/testing/). Komponententests werden verwendet, um isolierte Softwarekomponenten, z. b. einzelne Klassen Methoden, zu testen. Integrationstests bestätigen, dass zwei oder mehr App-Komponenten zusammenarbeiten, um ein erwartetes Ergebnis zu erhalten, möglicherweise alle Komponenten, die für die vollständige Verarbeitung einer Anforderung erforderlich sind.

Diese umfassenderen Tests werden verwendet, um die Infrastruktur und das gesamte Framework der APP zu testen, häufig auch die folgenden Komponenten:

* -Datenbank
* Dateisystem
* Netzwerkgeräte
* Anforderungs-/Antwort-Pipeline

Komponententests verwenden hergestellte Komponenten, die als *Fakes* oder *Mock-Objekte*bezeichnet werden, anstelle von Infrastrukturkomponenten.

Im Gegensatz zu Komponententests, Integrationstests:

* Verwenden Sie die tatsächlichen Komponenten, die von der app in der Produktion verwendet werden.
* Erfordert mehr Code und Datenverarbeitung.
* Die Ausführung dauert länger.

Beschränken Sie daher die Verwendung von Integrationstests auf die wichtigsten Infrastruktur Szenarios. Wenn ein Verhalten entweder mithilfe eines Komponententests oder eines Integrationstests getestet werden kann, wählen Sie den Komponenten Test aus.

> [!TIP]
> Schreiben Sie keine Integrationstests für jede mögliche permutations des Daten-und Datei Zugriffs mit Datenbanken und Dateisystemen. Unabhängig davon, wie viele Orte in einer APP mit Datenbanken und Dateisystemen interagieren, sind in der Regel in der Lage, die Datenbank-und Dateisystem Komponenten mit einem fokussierten Satz von Lese-, Schreib-, Update-und Lösch Tests adäquat zu testen. Verwenden Sie Komponententests für routinemäßige Tests der Methoden Logik, die mit diesen Komponenten interagieren. In Komponententests führt die Verwendung von Infrastruktur-Fakes/-mopeln zu einer schnelleren Testausführung.

> [!NOTE]
> Bei der Erörterung von Integrationstests wird das getestete Projekt häufig als zu *testendes System*oder kurz "SUT" bezeichnet.
>
> *"SUT" wird in diesem Thema verwendet, um auf die getestete ASP.net Core-APP zu verweisen.*

## <a name="aspnet-core-integration-tests"></a>ASP.net Core Integrationstests

Integrationstests in ASP.net Core erfordern Folgendes:

* Ein Testprojekt wird verwendet, um die Tests zu enthalten und auszuführen. Das Testprojekt enthält einen Verweis auf das SUT.
* Das Testprojekt erstellt einen Testweb Host für das SUT und verwendet einen Testserver Client, um Anforderungen und Antworten mit dem SUT zu verarbeiten.
* Ein Test Runner wird verwendet, um die Tests auszuführen und die Testergebnisse zu melden.

Integrationstests Folgen einer Sequenz von Ereignissen, die die üblichen Testschritte für die *Anordnung*, den *Act*und den *Assert* einschließen:

1. Der Webhost des SUTs ist konfiguriert.
1. Ein Testserver Client wird erstellt, um Anforderungen an die APP zu senden.
1. Der Schritt "Test *anordnen* " wird ausgeführt: die Test-App bereitet eine Anforderung vor.
1. Der *Act* -Test Schritt wird ausgeführt: der Client sendet die Anforderung und empfängt die Antwort.
1. Der *Assert* -Test Schritt wird ausgeführt: *die tatsächliche* Antwort wird basierend auf einer *erwarteten* Antwort als " *bestanden* " oder " *Fail* " überprüft.
1. Der Prozess wird fortgesetzt, bis alle Tests ausgeführt wurden.
1. Die Testergebnisse werden gemeldet.

Normalerweise ist der Testweb Host anders konfiguriert als der normale Webhost der APP für die Testläufe. Beispielsweise kann eine andere Datenbank oder eine andere app-Einstellung für die Tests verwendet werden.

Infrastrukturkomponenten, z. b. der Testweb Host und der in-Memory-Testserver (Test[Server](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), werden vom Paket [Microsoft. aspnetcore. MVC. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) bereitgestellt oder verwaltet. Durch die Verwendung dieses Pakets werden die Testerstellung und-Ausführung optimiert.

Das `Microsoft.AspNetCore.Mvc.Testing`-Paket verarbeitet die folgenden Aufgaben:

* Kopiert die Abhängigkeits Datei ( *. deps*) aus dem SUT in das *bin* -Verzeichnis des Testprojekts.
* Legt den [Inhalts](xref:fundamentals/index#content-root) Stamm auf den Projektstamm des SUTs fest, sodass beim Ausführen der Tests statische Dateien und Seiten/Sichten gefunden werden.
* Stellt die [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) -Klasse bereit, um das Bootstrapping des SUT mit `TestServer`zu optimieren.

In der Dokumentation zu Komponenten [Tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) wird beschrieben, wie Sie ein Testprojekt und einen Test Runner einrichten, zusammen mit ausführlichen Anweisungen zum Ausführen von Tests und Empfehlungen zum Benennen von Tests und Test Klassen.

> [!NOTE]
> Wenn Sie ein Testprojekt für eine APP erstellen, trennen Sie die Komponententests von den Integrationstests in verschiedene Projekte. Dadurch wird sichergestellt, dass Komponententests nicht versehentlich in Komponententests eingeschlossen werden. Durch die Trennung von Einheiten-und Integrationstests können Sie auch steuern, welche Testsätze ausgeführt werden.

Es gibt praktisch keinen Unterschied zwischen der Konfiguration für Tests von Razor Pages-apps und MVC-apps. Der einzige Unterschied besteht darin, wie die Tests benannt werden. In einer Razor Pages-App werden Tests von Seiten Endpunkten normalerweise nach der Seiten Modell Klasse benannt (z. b. `IndexPageTests`, um die Komponenten Integration für die Index Seite zu testen). In einer MVC-App werden Tests in der Regel nach Controller Klassen organisiert und nach den zu testenden Controllern benannt (z. b. `HomeControllerTests`, um die Komponenten Integration für den Home-Controller zu testen).

## <a name="test-app-prerequisites"></a>Test-App-Komponenten

Das Testprojekt muss folgende Aktionen ausführen:

* Verweisen Sie auf das Paket [Microsoft. aspnetcore. MVC. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) .
* Geben Sie das Web-SDK in der Projektdatei an (`<Project Sdk="Microsoft.NET.Sdk.Web">`).

Diese Voraussetzungen finden Sie in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Überprüfen Sie die Datei *Tests/razorpagesproject. Tests/razorpagesproject. Tests. csproj* . Die Beispiel-App verwendet das [xUnit](https://xunit.github.io/) -Test Framework und die [anglesharp](https://anglesharp.github.io/) -Parser-Bibliothek, sodass die Beispiel-APP auch auf Folgendes verweist:

* [xUnit](https://www.nuget.org/packages/xunit)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp)

Entity Framework Core wird auch in den Tests verwendet. Die APP-Verweise:

* [Microsoft. aspnetcore. Diagnostics. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [Microsoft. aspnetcore. Identity. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [Microsoft. entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [Microsoft. entityframeworkcore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a>SUT-Umgebung

Wenn die SUT- [Umgebung](xref:fundamentals/environments) nicht festgelegt ist, wird standardmäßig die-Umgebung entwickelt.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Grundlegende Tests mit der standardmäßigen webapplicationfactory

[Webapplicationfactory\<tentrypoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) wird verwendet, um einen [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) für die Integrationstests zu erstellen. `TEntryPoint` ist die Einstiegspunkt Klasse des SUT, normalerweise die `Startup` Klasse.

Test Klassen implementieren eine *Klassen Fixierungs* Schnittstelle ([iclassfixd](https://xunit.github.io/docs/shared-context#class-fixture)), um anzugeben, dass die Klasse Tests enthält, und stellen in den Tests in der Klasse gemeinsam genutzte Objektinstanzen bereit.

Die folgende Testklasse, `BasicTests`, verwendet die `WebApplicationFactory`, um das untergeordnete Element zu Bootstrap und einen [HttpClient](/dotnet/api/system.net.http.httpclient) für eine Testmethode, `Get_EndpointsReturnSuccessAndCorrectContentType`, bereitzustellen. Die-Methode überprüft, ob der Antwortstatus Code erfolgreich ist (Statuscodes im Bereich 200-299), und der `Content-Type`-Header ist für mehrere App-Seiten `text/html; charset=utf-8`.

" [Kreateclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) " erstellt eine Instanz von `HttpClient`, die automatisch Umleitungen befolgt und Cookies verarbeitet.

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Standardmäßig werden nicht erforderliche Cookies nicht über Anforderungen hinweg beibehalten, wenn die [Richtlinie](xref:security/gdpr) für die dsgvo-Zustimmung aktiviert ist. Um nicht erforderliche Cookies beizubehalten, wie z. b. diejenigen, die vom TempData-Anbieter verwendet werden, markieren Sie Sie als unverzichtbar in den Tests. Anweisungen zum Markieren eines Cookies als unabdingbar finden Sie unter [wichtige Cookies](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Anpassen von webapplicationfactory

Die Webhost Konfiguration kann unabhängig von den Test Klassen erstellt werden, indem von `WebApplicationFactory` geerbt wird, um eine oder mehrere benutzerdefinierte Factorys zu erstellen:

1. Erben von `WebApplicationFactory` und Überschreiben von [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ermöglicht die Konfiguration der Dienst Sammlung mit [konfigurierstellungsdiensten](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Das datenbankseeding in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) wird durch die `InitializeDbForTests`-Methode ausgeführt. Die-Methode wird im Abschnitt [Integration Tests Sample: Test App Organization](#test-app-organization) beschrieben.

   Der Daten Bank Kontext des Daten Bank Kontexts wird in der `Startup.ConfigureServices`-Methode registriert. Der `builder.ConfigureServices` Rückruf der Test-App wird *nach* der Ausführung des `Startup.ConfigureServices` Codes der app ausgeführt. Die Ausführungsreihenfolge ist eine Breaking Change für den [generischen Host](xref:fundamentals/host/generic-host) mit der Veröffentlichung von ASP.net Core 3,0. Um für die Tests eine andere Datenbank als die Datenbank der APP zu verwenden, muss der Daten Bank Kontext der app in `builder.ConfigureServices`ersetzt werden.

   Die Beispiel-App findet den Dienst Deskriptor für den Daten Bank Kontext und verwendet den Deskriptor, um die Dienst Registrierung zu entfernen. Als Nächstes fügt die Factory eine neue `ApplicationDbContext` hinzu, die eine in-Memory Database für die Tests verwendet.

   Um eine Verbindung mit einer anderen Datenbank als der in-Memory Database herzustellen, ändern Sie den `UseInMemoryDatabase`-Befehl, um den Kontext mit einer anderen Datenbank zu verbinden. So verwenden Sie eine SQL Server Testdatenbank:

   * Verweisen Sie in der Projektdatei auf das nuget-Paket [Microsoft. entityframeworkcore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) .
   * Ruft `UseSqlServer` mit einer Verbindungs Zeichenfolge für die Datenbank auf.

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. Verwenden Sie die benutzerdefinierte `CustomWebApplicationFactory` in Test Klassen. Im folgenden Beispiel wird die Factory in der `IndexPageTests`-Klasse verwendet:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Der Client der Beispiel-APP wird so konfiguriert, dass der `HttpClient` die folgenden Umleitungen verhindert. Wie weiter unten im Abschnitt " [Mock-Authentifizierung](#mock-authentication) " erläutert, können Tests das Ergebnis der ersten Reaktion der APP überprüfen. Die erste Antwort ist eine Umleitung in vielen dieser Tests mit einem `Location`-Header.

3. Ein typischer Test verwendet die `HttpClient`-und Hilfsmethoden, um die Anforderung und die Antwort zu verarbeiten:

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Jede Post-Anforderung an das SUT muss die Überprüfung der Antifälschung erfüllen, die automatisch vom [Datenschutz-antifälschungssystem](xref:security/data-protection/introduction)der APP vorgenommen wird. Um die Post-Anforderung eines Tests anzuordnen, muss die Test-App folgende Schritte ausführen:

1. Stellen Sie eine Anforderung für die Seite.
1. Analysieren Sie das antifälschungs Cookie, und fordern Sie das Validierungs Token von der Antwort an.
1. Stellen Sie die Post-Anforderung mit dem antifälschungscookie und dem Anforderungs Validierungs Token bereit.

Die Methoden für die `SendAsync`-Hilfserweiterung (helper */httpclientextensions. cs*) und die `GetDocumentAsync` Helper-Methode (helper */htmlhelper. cs*) in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) verwenden den [anglesharp](https://anglesharp.github.io/) -Parser, um die antifälschungs Prüfung mit den folgenden Methoden zu verarbeiten:

* `GetDocumentAsync` &ndash; Empfängt die [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) und gibt eine `IHtmlDocument`-Klasse zurück. `GetDocumentAsync` verwendet eine Factory, die eine *virtuelle Antwort* auf der Grundlage der ursprünglichen `HttpResponseMessage`vorbereitet. Weitere Informationen finden Sie in der [anglesharp-Dokumentation](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` Erweiterungs Methoden für die `HttpClient` verfassen Sie eine [httprequestmessage](/dotnet/api/system.net.http.httprequestmessage) , und rufen Sie [SendAsync (httprequestmessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) auf, um Anforderungen an das SUT zu übermitteln. Über Ladungen für `SendAsync` akzeptieren das HTML-Formular (`IHtmlFormElement`) und die folgenden:
  * Schaltfläche "Senden" im Formular (`IHtmlElement`)
  * Sammlung von Formular Werten (`IEnumerable<KeyValuePair<string, string>>`)
  * Schaltfläche "Senden" (`IHtmlElement`) und Formular Werte (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [Anglesharp](https://anglesharp.github.io/) ist eine Drittanbieter-Test Bibliothek, die zu Demonstrationszwecken in diesem Thema und in der Beispiel-App verwendet wird. Anglesharp wird für Integrationstests von ASP.net Core-apps nicht unterstützt oder benötigt. Andere Parser können verwendet werden, wie z. b. das [HTML Agilität Pack (HAP)](https://html-agility-pack.net/). Ein anderer Ansatz besteht darin, Code zu schreiben, der das Anforderungs Überprüfungs Token des antifälschungssystems und das antifälschungscookie direkt behandelt.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Anpassen des Clients mit withwebhostbuilder

Wenn eine zusätzliche Konfiguration innerhalb einer Testmethode erforderlich ist, erstellt [withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) eine neue `WebApplicationFactory` mit einem [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , der durch die Konfiguration weiter angepasst wird.

Die `Post_DeleteMessageHandler_ReturnsRedirectToRoot` Test-Methode der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veranschaulicht die Verwendung von `WithWebHostBuilder`. Bei diesem Test wird ein Daten Satz Löschvorgang in der Datenbank durch Auslösen einer Formular Übermittlung im SUT durchführt.

Da ein anderer Test in der `IndexPageTests`-Klasse einen Vorgang ausführt, der alle Datensätze in der Datenbank löscht und vor der `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Methode ausgeführt werden kann, wird die Datenbank in dieser Testmethode neu angeordnet, um sicherzustellen, dass ein Datensatz vorhanden ist, damit das SUT gelöscht werden kann. Die Auswahl der ersten Lösch Schaltfläche des `messages` Formulars im SUT wird in der Anforderung an das untergeordnete Element simuliert:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Client Optionen

In der folgenden Tabelle wird der Standardwert für [webapplicationfactoriyclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) angezeigt, der beim Erstellen von `HttpClient` Instanzen verfügbar ist.

| -Option | Beschreibung | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ruft ab oder legt fest, ob `HttpClient` Instanzen automatisch Umleitungs Antworten befolgen sollen. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ruft die Basisadresse `HttpClient`-Instanzen ab oder legt Sie fest. | `http://localhost` |
| [Lenker](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen Cookies verarbeiten sollen. | `true` |
| [Maxautomatikredirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ruft die maximale Anzahl von Umleitungs Antworten ab oder legt diese fest, die `HttpClient`-Instanzen befolgt werden sollen. | 7 |

Erstellen Sie die `WebApplicationFactoryClientOptions`-Klasse, und übergeben Sie sie an die Methode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (Standardwerte werden im Codebeispiel angezeigt):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Mock-Dienste einfügen

Dienste können in einem Test mit einem-Befehl konfiguriert werden, der auf dem Host-Generator [konfiguriert](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ist. **Um Mock-Dienste einzufügen, muss das SUT über eine `Startup`-Klasse mit einer `Startup.ConfigureServices`-Methode verfügen.**

Das Beispiel "SUT" enthält einen Bereichs bezogenen Dienst, der ein Anführungszeichen zurückgibt. Wenn die Indexseite angefordert wird, wird das Anführungszeichen in ein ausgeblendetes Feld auf der Indexseite eingebettet.

*Dienste/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Dienste/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Das folgende Markup wird generiert, wenn die SUT-app ausgeführt wird:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Um den Dienst zu testen und in einem Integrations Test eine Injektion durchzusetzen, wird ein Mock-Dienst vom Test in das untergeordnete Element eingefügt. Der Mock-Dienst ersetzt die `QuoteService` der app durch einen Dienst, der von der Test-App bereitgestellt wird, die `TestQuoteService`genannt wird:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` wird aufgerufen, und der Bereichs bezogene Dienst wird registriert:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Das Markup, das während der Ausführung des Tests erstellt wird, spiegelt den von `TestQuoteService`angegebenen Anführungszeichen wider, sodass die-Überprüfung verläuft:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Mock-Authentifizierung

Tests in der `AuthTests`-Klasse überprüfen, ob ein sicherer Endpunkt:

* Leitet einen nicht authentifizierten Benutzer an die Anmeldeseite der App um.
* Gibt den Inhalt für einen authentifizierten Benutzer zurück.

Im Unterbereich verwendet die Seite "`/SecurePage`" eine [autorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) -Konvention, um einen [autorizefilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf die Seite anzuwenden. Weitere Informationen finden Sie unter [Razor Pages Autorisierungs Konventionen](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Im `Get_SecurePageRedirectsAnUnauthenticatedUser` Test wird für [webapplicationfactor yclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) festgelegt, dass Umleitungen nicht zulässig sind, indem [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) auf `false`festgelegt wird:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Indem der Client die Umleitung der Umleitung verweigert, können folgende Prüfungen durchgeführt werden:

* Der vom SUT zurückgegebene Statuscode kann mit dem erwarteten [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) -Ergebnis überprüft werden, nicht nach der Umleitung zur Anmeldeseite ( [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode)).
* Der Wert für den `Location`-Header in den Antwort Headern wird überprüft, um zu bestätigen, dass er mit `http://localhost/Identity/Account/Login`beginnt, nicht mit der Antwort auf die abschließende Anmeldeseite, bei der der `Location` Header nicht vorhanden wäre.

Die Test-App kann eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in " [konfigurierstellungsdiensteserver](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) " simulieren, um Aspekte der Authentifizierung und Autorisierung zu testen. In einem minimalen Szenario wird ein [authenticateresult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)-Vorgang zurückgegeben:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Der `TestAuthHandler` wird aufgerufen, um einen Benutzer zu authentifizieren, wenn das Authentifizierungsschema auf `Test` festgelegt ist, in dem `AddAuthentication` für `ConfigureTestServices`registriert ist:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Weitere Informationen zu `WebApplicationFactoryClientOptions`finden Sie im Abschnitt " [Client Optionen](#client-options) ".

## <a name="set-the-environment"></a>Festlegen der Umgebung

Standardmäßig ist die Host-und App-Umgebung des SUTs für die Verwendung der Entwicklungsumgebung konfiguriert. So überschreiben Sie die Umgebung von SUT:

* Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` fest (z. b. `Staging`, `Production`oder einen anderen benutzerdefinierten Wert, z. b. `Testing`).
* Überschreiben Sie `CreateHostBuilder` in der Test-App, um Umgebungsvariablen mit dem Präfix `ASPNETCORE`zu lesen.

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Wie die Testinfrastruktur den Stamm Pfad der App-Inhalte ableitet

Der `WebApplicationFactory`-Konstruktor leitet den Stamm Pfad der APP- [Inhalte](xref:fundamentals/index#content-root) ab, indem er nach einem [webapplicationfactorycontentrootattribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) in der Assembly sucht, die die Integrationstests mit einem Schlüssel gleich der `TEntryPoint` Assembly `System.Reflection.Assembly.FullName`enthält. Wenn ein Attribut mit dem richtigen Schlüssel nicht gefunden wird, wird `WebApplicationFactory` auf die Suche nach einer Projektmappendatei ( *. sln*) zurückgegriffen und den `TEntryPoint` Assemblynamen an das Projektmappenverzeichnis angefügt. Das Stammverzeichnis der APP (der Inhalts Stamm Pfad) wird verwendet, um Sichten und Inhalts Dateien zu ermitteln.

## <a name="disable-shadow-copying"></a>Schatten Kopie deaktivieren

Das Durchführen von Schatten Kopien bewirkt, dass die Tests in einem anderen Verzeichnis als dem Ausgabeverzeichnis ausgeführt werden. Damit Tests ordnungsgemäß funktionieren, muss die Schatten Kopie deaktiviert werden. Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) verwendet xUnit und deaktiviert das Kopieren von Schatten Kopien für xUnit durch Einschließen einer *xUnit. Runner. JSON* -Datei mit der richtigen Konfigurationseinstellung. Weitere Informationen finden Sie unter [Konfigurieren von xUnit mit JSON](https://xunit.github.io/docs/configuring-with-json.html).

Fügen Sie die Datei " *xUnit. Runner. JSON* " dem Stamm des Testprojekts mit folgendem Inhalt hinzu:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Entfernen von Objekten

Nachdem die Tests der `IClassFixture`-Implementierung ausgeführt wurden, werden [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) und [HttpClient](/dotnet/api/system.net.http.httpclient) verworfen, wenn xUnit die [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)freigibt. Wenn Objekte, die vom Entwickler instanziiert werden, gelöscht werden müssen, müssen Sie Sie in der `IClassFixture`-Implementierung verwerfen. Weitere Informationen finden Sie unter [Implementieren einer](/dotnet/standard/garbage-collection/implementing-dispose)verwerfen-Methode.

## <a name="integration-tests-sample"></a>Beispiel für Integrationstests

Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) besteht aus zwei apps:

| App- | Projektverzeichnis | Beschreibung |
| --- | ----------------- | ----------- |
| Message-app (Das SUT) | *src/RazorPagesProject* | Ermöglicht Benutzern das Hinzufügen, löschen, löschen und Analysieren von Nachrichten. |
| Testen der App | *tests/RazorPagesProject.Tests* | Wird verwendet, um das SUT zu integrieren. |

Die Tests können mit den integrierten Testfunktionen einer IDE ausgeführt werden, z. b. [Visual Studio](https://visualstudio.microsoft.com). Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl an einer Eingabeaufforderung im Verzeichnis *Tests/razorpagesproject. Tests* aus:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Nachrichten-APP-Organisation (SUT)

Das untergeordnete Element ist ein Razor Pages Nachrichtensystem mit folgenden Merkmalen:

* Die Index Seite der APP (*pages/index. cshtml* und *pages/index. cshtml. cs*) stellt eine UI-und Seiten Modell Methode zum Steuern der Addition, Löschung und Analyse von Meldungen (durchschnittliche Wörter pro Nachricht) bereit.
* Eine Meldung wird von der `Message`-Klasse (*Data/Message. cs*) mit zwei Eigenschaften beschrieben: `Id` (Key) und `Text` (Message). Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe [der in-Memory Database](/ef/core/providers/in-memory/)&#8224;Entity Framework gespeichert.
* Die app enthält eine Datenzugriffs Schicht (Data Access Layer, DAL) in der zugehörigen Daten Bank Kontext Klasse `AppDbContext` (*Data/appdbcontext. cs*).
* Wenn die Datenbank beim Starten der APP leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert.
* Die app enthält eine `/SecurePage`, auf die nur ein authentifizierter Benutzer zugreifen kann.

&#8224;Das EF-Thema [Testen mit inMemory](/ef/core/miscellaneous/testing/in-memory)erläutert, wie Sie eine in-Memory Database für Tests mit MSTest verwenden. In diesem Thema wird das [xUnit](https://xunit.github.io/) -Test Framework verwendet. Testkonzepte und Test Implementierungen in verschiedenen Test-Frameworks sind ähnlich, aber nicht identisch.

Obwohl die APP das Repository-Muster nicht verwendet und kein effektives Beispiel für das [Muster der Arbeitseinheit (Unit of Work, UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html)ist, werden Razor Pages diese Entwicklungsmuster unterstützt. Weitere Informationen finden Sie unter [Entwerfen der Infrastruktur-Persistenzebene](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und der [Test Controller Logik](/aspnet/core/mvc/controllers/testing) (im Beispiel wird das Repository-Muster implementiert).

### <a name="test-app-organization"></a>Testen der APP-Organisation

Bei der Test-App handelt es sich um eine Konsolen-App im Verzeichnis *Tests/razorpagesproject. Tests* .

| Test-App-Verzeichnis | Beschreibung |
| ------------------ | ----------- |
| *Authtests* | Enthält Testmethoden für:<ul><li>Zugreifen auf eine sichere Seite durch einen nicht authentifizierten Benutzer.</li><li>Zugreifen auf eine sichere Seite durch einen authentifizierten Benutzer mit einem Mock-<xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Abrufen eines GitHub-Benutzerprofils und Überprüfen der Benutzeranmeldung des Profils.</li></ul> |
| *Basictests* | Enthält eine Testmethode für Routing und Inhaltstyp. |
| *Integrationtests* | Enthält die Integrationstests für die Index Seite mithilfe der benutzerdefinierten `WebApplicationFactory`-Klasse. |
| *Hilfsprogramme/Hilfsprogramme* | <ul><li>*Utilities.cs* enthält die `InitializeDbForTests` Methode, mit der die Datenbank mit Testdaten als Ausgangswert verwendet wird.</li><li>*HtmlHelpers.cs* stellt eine Methode bereit, mit der ein anglesharp-`IHtmlDocument` für die Verwendung durch die Testmethoden zurückgegeben wird.</li><li>*HttpClientExtensions.cs* Bereitstellen von über Ladungen für `SendAsync` zum Übermitteln von Anforderungen an das SUT.</li></ul> |

Das Test Framework ist " [xUnit](https://xunit.github.io/)". Integrationstests werden mithilfe von " [Microsoft. aspnetcore. Testhost](/dotnet/api/microsoft.aspnetcore.testhost)" durchgeführt, das den [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)enthält. Da das [Microsoft. aspnetcore. MVC. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) -Paket zum Konfigurieren des Test Hosts und des Test Servers verwendet wird, sind für die `TestHost`-und `TestServer` Pakete in der Projektdatei der Test-App oder der Entwickler Konfiguration in der Test-App keine direkten Paket Verweise erforderlich.

**Seeding der Datenbank zum Testen**

Für Integrationstests ist in der Regel vor der Testausführung ein kleines DataSet in der Datenbank erforderlich. Beispielsweise wird ein Löschvorgang für einen Löschvorgang eines Datensatzes durchgeführt, sodass die Datenbank mindestens einen Datensatz aufweisen muss, damit die Löschanforderung erfolgreich ausgeführt wird.

Die Beispiel-App startet die Datenbank mit drei Nachrichten in *Utilities.cs* , die von Tests bei der Ausführung verwendet werden können:

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

Der Daten Bank Kontext des Daten Bank Kontexts wird in der `Startup.ConfigureServices`-Methode registriert. Der `builder.ConfigureServices` Rückruf der Test-App wird *nach* der Ausführung des `Startup.ConfigureServices` Codes der app ausgeführt. Um eine andere Datenbank für die Tests zu verwenden, muss der Daten Bank Kontext der app in `builder.ConfigureServices`ersetzt werden. Weitere Informationen finden Sie im Abschnitt [Anpassen von webapplicationfactory](#customize-webapplicationfactory) .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Integrationstests stellen sicher, dass die Komponenten einer APP auf einer Ebene, die die unterstützende Infrastruktur der APP umfasst, wie z. b. die Datenbank, das Dateisystem und das Netzwerk, ordnungsgemäß funktionieren. ASP.net Core unterstützt Integrationstests mithilfe eines Komponenten Test-Frameworks mit einem Testweb Host und einem in-Memory-Testserver.

In diesem Thema wird ein grundlegendes Verständnis von Komponententests vorausgesetzt. Wenn Sie mit den Test Konzepten nicht vertraut sind, finden Sie weitere Informationen unter Komponenten [Tests in .net Core und .NET Standard](/dotnet/core/testing/) Thema und zugehörige Inhalte.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Bei der Beispiel-App handelt es sich um eine Razor Pages-APP, die grundlegend mit der Razor Pages vertraut. Wenn Sie mit Razor Pages nicht vertraut sind, lesen Sie die folgenden Themen:

* [Introduction to Razor Pages (Einführung in Razor Pages)](xref:razor-pages/index)
* [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Komponententests für Razor-Seiten](xref:test/razor-pages-tests)

> [!NOTE]
> Zum Testen von Spas empfiehlt es sich, ein Tool wie [Selenium](https://www.seleniumhq.org/)zu verwenden, mit dem ein Browser automatisiert werden kann.

## <a name="introduction-to-integration-tests"></a>Einführung in Integrationstests

Integrationstests bewerten die Komponenten einer APP auf einer umfassenderen Ebene als Komponenten [Tests](/dotnet/core/testing/). Komponententests werden verwendet, um isolierte Softwarekomponenten, z. b. einzelne Klassen Methoden, zu testen. Integrationstests bestätigen, dass zwei oder mehr App-Komponenten zusammenarbeiten, um ein erwartetes Ergebnis zu erhalten, möglicherweise alle Komponenten, die für die vollständige Verarbeitung einer Anforderung erforderlich sind.

Diese umfassenderen Tests werden verwendet, um die Infrastruktur und das gesamte Framework der APP zu testen, häufig auch die folgenden Komponenten:

* -Datenbank
* Dateisystem
* Netzwerkgeräte
* Anforderungs-/Antwort-Pipeline

Komponententests verwenden hergestellte Komponenten, die als *Fakes* oder *Mock-Objekte*bezeichnet werden, anstelle von Infrastrukturkomponenten.

Im Gegensatz zu Komponententests, Integrationstests:

* Verwenden Sie die tatsächlichen Komponenten, die von der app in der Produktion verwendet werden.
* Erfordert mehr Code und Datenverarbeitung.
* Die Ausführung dauert länger.

Beschränken Sie daher die Verwendung von Integrationstests auf die wichtigsten Infrastruktur Szenarios. Wenn ein Verhalten entweder mithilfe eines Komponententests oder eines Integrationstests getestet werden kann, wählen Sie den Komponenten Test aus.

> [!TIP]
> Schreiben Sie keine Integrationstests für jede mögliche permutations des Daten-und Datei Zugriffs mit Datenbanken und Dateisystemen. Unabhängig davon, wie viele Orte in einer APP mit Datenbanken und Dateisystemen interagieren, sind in der Regel in der Lage, die Datenbank-und Dateisystem Komponenten mit einem fokussierten Satz von Lese-, Schreib-, Update-und Lösch Tests adäquat zu testen. Verwenden Sie Komponententests für routinemäßige Tests der Methoden Logik, die mit diesen Komponenten interagieren. In Komponententests führt die Verwendung von Infrastruktur-Fakes/-mopeln zu einer schnelleren Testausführung.

> [!NOTE]
> Bei der Erörterung von Integrationstests wird das getestete Projekt häufig als zu *testendes System*oder kurz "SUT" bezeichnet.
>
> *"SUT" wird in diesem Thema verwendet, um auf die getestete ASP.net Core-APP zu verweisen.*

## <a name="aspnet-core-integration-tests"></a>ASP.net Core Integrationstests

Integrationstests in ASP.net Core erfordern Folgendes:

* Ein Testprojekt wird verwendet, um die Tests zu enthalten und auszuführen. Das Testprojekt enthält einen Verweis auf das SUT.
* Das Testprojekt erstellt einen Testweb Host für das SUT und verwendet einen Testserver Client, um Anforderungen und Antworten mit dem SUT zu verarbeiten.
* Ein Test Runner wird verwendet, um die Tests auszuführen und die Testergebnisse zu melden.

Integrationstests Folgen einer Sequenz von Ereignissen, die die üblichen Testschritte für die *Anordnung*, den *Act*und den *Assert* einschließen:

1. Der Webhost des SUTs ist konfiguriert.
1. Ein Testserver Client wird erstellt, um Anforderungen an die APP zu senden.
1. Der Schritt "Test *anordnen* " wird ausgeführt: die Test-App bereitet eine Anforderung vor.
1. Der *Act* -Test Schritt wird ausgeführt: der Client sendet die Anforderung und empfängt die Antwort.
1. Der *Assert* -Test Schritt wird ausgeführt: *die tatsächliche* Antwort wird basierend auf einer *erwarteten* Antwort als " *bestanden* " oder " *Fail* " überprüft.
1. Der Prozess wird fortgesetzt, bis alle Tests ausgeführt wurden.
1. Die Testergebnisse werden gemeldet.

Normalerweise ist der Testweb Host anders konfiguriert als der normale Webhost der APP für die Testläufe. Beispielsweise kann eine andere Datenbank oder eine andere app-Einstellung für die Tests verwendet werden.

Infrastrukturkomponenten, z. b. der Testweb Host und der in-Memory-Testserver (Test[Server](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), werden vom Paket [Microsoft. aspnetcore. MVC. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) bereitgestellt oder verwaltet. Durch die Verwendung dieses Pakets werden die Testerstellung und-Ausführung optimiert.

Das `Microsoft.AspNetCore.Mvc.Testing`-Paket verarbeitet die folgenden Aufgaben:

* Kopiert die Abhängigkeits Datei ( *. deps*) aus dem SUT in das *bin* -Verzeichnis des Testprojekts.
* Legt den [Inhalts](xref:fundamentals/index#content-root) Stamm auf den Projektstamm des SUTs fest, sodass beim Ausführen der Tests statische Dateien und Seiten/Sichten gefunden werden.
* Stellt die [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) -Klasse bereit, um das Bootstrapping des SUT mit `TestServer`zu optimieren.

In der Dokumentation zu Komponenten [Tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) wird beschrieben, wie Sie ein Testprojekt und einen Test Runner einrichten, zusammen mit ausführlichen Anweisungen zum Ausführen von Tests und Empfehlungen zum Benennen von Tests und Test Klassen.

> [!NOTE]
> Wenn Sie ein Testprojekt für eine APP erstellen, trennen Sie die Komponententests von den Integrationstests in verschiedene Projekte. Dadurch wird sichergestellt, dass Komponententests nicht versehentlich in Komponententests eingeschlossen werden. Durch die Trennung von Einheiten-und Integrationstests können Sie auch steuern, welche Testsätze ausgeführt werden.

Es gibt praktisch keinen Unterschied zwischen der Konfiguration für Tests von Razor Pages-apps und MVC-apps. Der einzige Unterschied besteht darin, wie die Tests benannt werden. In einer Razor Pages-App werden Tests von Seiten Endpunkten normalerweise nach der Seiten Modell Klasse benannt (z. b. `IndexPageTests`, um die Komponenten Integration für die Index Seite zu testen). In einer MVC-App werden Tests in der Regel nach Controller Klassen organisiert und nach den zu testenden Controllern benannt (z. b. `HomeControllerTests`, um die Komponenten Integration für den Home-Controller zu testen).

## <a name="test-app-prerequisites"></a>Test-App-Komponenten

Das Testprojekt muss folgende Aktionen ausführen:

* Verweisen Sie auf die folgenden Pakete:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Geben Sie das Web-SDK in der Projektdatei an (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Das Web-SDK ist erforderlich, wenn Sie auf das [Metapaket Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)verweisen.

Diese Voraussetzungen finden Sie in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/). Überprüfen Sie die Datei *Tests/razorpagesproject. Tests/razorpagesproject. Tests. csproj* . Die Beispiel-App verwendet das [xUnit](https://xunit.github.io/) -Test Framework und die [anglesharp](https://anglesharp.github.io/) -Parser-Bibliothek, sodass die Beispiel-APP auch auf Folgendes verweist:

* [xUnit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>SUT-Umgebung

Wenn die SUT- [Umgebung](xref:fundamentals/environments) nicht festgelegt ist, wird standardmäßig die-Umgebung entwickelt.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Grundlegende Tests mit der standardmäßigen webapplicationfactory

[Webapplicationfactory\<tentrypoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) wird verwendet, um einen [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) für die Integrationstests zu erstellen. `TEntryPoint` ist die Einstiegspunkt Klasse des SUT, normalerweise die `Startup` Klasse.

Test Klassen implementieren eine *Klassen Fixierungs* Schnittstelle ([iclassfixd](https://xunit.github.io/docs/shared-context#class-fixture)), um anzugeben, dass die Klasse Tests enthält, und stellen in den Tests in der Klasse gemeinsam genutzte Objektinstanzen bereit.

Die folgende Testklasse, `BasicTests`, verwendet die `WebApplicationFactory`, um das untergeordnete Element zu Bootstrap und einen [HttpClient](/dotnet/api/system.net.http.httpclient) für eine Testmethode, `Get_EndpointsReturnSuccessAndCorrectContentType`, bereitzustellen. Die-Methode überprüft, ob der Antwortstatus Code erfolgreich ist (Statuscodes im Bereich 200-299), und der `Content-Type`-Header ist für mehrere App-Seiten `text/html; charset=utf-8`.

" [Kreateclient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) " erstellt eine Instanz von `HttpClient`, die automatisch Umleitungen befolgt und Cookies verarbeitet.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

Standardmäßig werden nicht erforderliche Cookies nicht über Anforderungen hinweg beibehalten, wenn die [Richtlinie](xref:security/gdpr) für die dsgvo-Zustimmung aktiviert ist. Um nicht erforderliche Cookies beizubehalten, wie z. b. diejenigen, die vom TempData-Anbieter verwendet werden, markieren Sie Sie als unverzichtbar in den Tests. Anweisungen zum Markieren eines Cookies als unabdingbar finden Sie unter [wichtige Cookies](xref:security/gdpr#essential-cookies).

## <a name="customize-webapplicationfactory"></a>Anpassen von webapplicationfactory

Die Webhost Konfiguration kann unabhängig von den Test Klassen erstellt werden, indem von `WebApplicationFactory` geerbt wird, um eine oder mehrere benutzerdefinierte Factorys zu erstellen:

1. Erben von `WebApplicationFactory` und Überschreiben von [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). [Iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ermöglicht die Konfiguration der Dienst Sammlung mit [konfigurierstellungsdiensten](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Das datenbankseeding in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) wird durch die `InitializeDbForTests`-Methode ausgeführt. Die-Methode wird im Abschnitt [Integration Tests Sample: Test App Organization](#test-app-organization) beschrieben.

2. Verwenden Sie die benutzerdefinierte `CustomWebApplicationFactory` in Test Klassen. Im folgenden Beispiel wird die Factory in der `IndexPageTests`-Klasse verwendet:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Der Client der Beispiel-APP wird so konfiguriert, dass der `HttpClient` die folgenden Umleitungen verhindert. Wie weiter unten im Abschnitt " [Mock-Authentifizierung](#mock-authentication) " erläutert, können Tests das Ergebnis der ersten Reaktion der APP überprüfen. Die erste Antwort ist eine Umleitung in vielen dieser Tests mit einem `Location`-Header.

3. Ein typischer Test verwendet die `HttpClient`-und Hilfsmethoden, um die Anforderung und die Antwort zu verarbeiten:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Jede Post-Anforderung an das SUT muss die Überprüfung der Antifälschung erfüllen, die automatisch vom [Datenschutz-antifälschungssystem](xref:security/data-protection/introduction)der APP vorgenommen wird. Um die Post-Anforderung eines Tests anzuordnen, muss die Test-App folgende Schritte ausführen:

1. Stellen Sie eine Anforderung für die Seite.
1. Analysieren Sie das antifälschungs Cookie, und fordern Sie das Validierungs Token von der Antwort an.
1. Stellen Sie die Post-Anforderung mit dem antifälschungscookie und dem Anforderungs Validierungs Token bereit.

Die Methoden für die `SendAsync`-Hilfserweiterung (helper */httpclientextensions. cs*) und die `GetDocumentAsync` Helper-Methode (helper */htmlhelper. cs*) in der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) verwenden den [anglesharp](https://anglesharp.github.io/) -Parser, um die antifälschungs Prüfung mit den folgenden Methoden zu verarbeiten:

* `GetDocumentAsync` &ndash; Empfängt die [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) und gibt eine `IHtmlDocument`-Klasse zurück. `GetDocumentAsync` verwendet eine Factory, die eine *virtuelle Antwort* auf der Grundlage der ursprünglichen `HttpResponseMessage`vorbereitet. Weitere Informationen finden Sie in der [anglesharp-Dokumentation](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` Erweiterungs Methoden für die `HttpClient` verfassen Sie eine [httprequestmessage](/dotnet/api/system.net.http.httprequestmessage) , und rufen Sie [SendAsync (httprequestmessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) auf, um Anforderungen an das SUT zu übermitteln. Über Ladungen für `SendAsync` akzeptieren das HTML-Formular (`IHtmlFormElement`) und die folgenden:
  * Schaltfläche "Senden" im Formular (`IHtmlElement`)
  * Sammlung von Formular Werten (`IEnumerable<KeyValuePair<string, string>>`)
  * Schaltfläche "Senden" (`IHtmlElement`) und Formular Werte (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [Anglesharp](https://anglesharp.github.io/) ist eine Drittanbieter-Test Bibliothek, die zu Demonstrationszwecken in diesem Thema und in der Beispiel-App verwendet wird. Anglesharp wird für Integrationstests von ASP.net Core-apps nicht unterstützt oder benötigt. Andere Parser können verwendet werden, wie z. b. das [HTML Agilität Pack (HAP)](https://html-agility-pack.net/). Ein anderer Ansatz besteht darin, Code zu schreiben, der das Anforderungs Überprüfungs Token des antifälschungssystems und das antifälschungscookie direkt behandelt.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Anpassen des Clients mit withwebhostbuilder

Wenn eine zusätzliche Konfiguration innerhalb einer Testmethode erforderlich ist, erstellt [withwebhostbuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) eine neue `WebApplicationFactory` mit einem [iwebhostbuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , der durch die Konfiguration weiter angepasst wird.

Die `Post_DeleteMessageHandler_ReturnsRedirectToRoot` Test-Methode der [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) veranschaulicht die Verwendung von `WithWebHostBuilder`. Bei diesem Test wird ein Daten Satz Löschvorgang in der Datenbank durch Auslösen einer Formular Übermittlung im SUT durchführt.

Da ein anderer Test in der `IndexPageTests`-Klasse einen Vorgang ausführt, der alle Datensätze in der Datenbank löscht und vor der `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Methode ausgeführt werden kann, wird die Datenbank in dieser Testmethode neu angeordnet, um sicherzustellen, dass ein Datensatz vorhanden ist, damit das SUT gelöscht werden kann. Die Auswahl der ersten Lösch Schaltfläche des `messages` Formulars im SUT wird in der Anforderung an das untergeordnete Element simuliert:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Client Optionen

In der folgenden Tabelle wird der Standardwert für [webapplicationfactoriyclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) angezeigt, der beim Erstellen von `HttpClient` Instanzen verfügbar ist.

| -Option | Beschreibung | Default |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ruft ab oder legt fest, ob `HttpClient` Instanzen automatisch Umleitungs Antworten befolgen sollen. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ruft die Basisadresse `HttpClient`-Instanzen ab oder legt Sie fest. | `http://localhost` |
| [Lenker](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen Cookies verarbeiten sollen. | `true` |
| [Maxautomatikredirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ruft die maximale Anzahl von Umleitungs Antworten ab oder legt diese fest, die `HttpClient`-Instanzen befolgt werden sollen. | 7 |

Erstellen Sie die `WebApplicationFactoryClientOptions`-Klasse, und übergeben Sie sie an die Methode [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) (Standardwerte werden im Codebeispiel angezeigt):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Mock-Dienste einfügen

Dienste können in einem Test mit einem-Befehl konfiguriert werden, der auf dem Host-Generator [konfiguriert](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ist. **Um Mock-Dienste einzufügen, muss das SUT über eine `Startup`-Klasse mit einer `Startup.ConfigureServices`-Methode verfügen.**

Das Beispiel "SUT" enthält einen Bereichs bezogenen Dienst, der ein Anführungszeichen zurückgibt. Wenn die Indexseite angefordert wird, wird das Anführungszeichen in ein ausgeblendetes Feld auf der Indexseite eingebettet.

*Dienste/IQuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Dienste/QuoteService. cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/index. cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Das folgende Markup wird generiert, wenn die SUT-app ausgeführt wird:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Um den Dienst zu testen und in einem Integrations Test eine Injektion durchzusetzen, wird ein Mock-Dienst vom Test in das untergeordnete Element eingefügt. Der Mock-Dienst ersetzt die `QuoteService` der app durch einen Dienst, der von der Test-App bereitgestellt wird, die `TestQuoteService`genannt wird:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` wird aufgerufen, und der Bereichs bezogene Dienst wird registriert:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Das Markup, das während der Ausführung des Tests erstellt wird, spiegelt den von `TestQuoteService`angegebenen Anführungszeichen wider, sodass die-Überprüfung verläuft:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a>Mock-Authentifizierung

Tests in der `AuthTests`-Klasse überprüfen, ob ein sicherer Endpunkt:

* Leitet einen nicht authentifizierten Benutzer an die Anmeldeseite der App um.
* Gibt den Inhalt für einen authentifizierten Benutzer zurück.

Im Unterbereich verwendet die Seite "`/SecurePage`" eine [autorizepage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) -Konvention, um einen [autorizefilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf die Seite anzuwenden. Weitere Informationen finden Sie unter [Razor Pages Autorisierungs Konventionen](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

Im `Get_SecurePageRedirectsAnUnauthenticatedUser` Test wird für [webapplicationfactor yclientoptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) festgelegt, dass Umleitungen nicht zulässig sind, indem [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) auf `false`festgelegt wird:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

Indem der Client die Umleitung der Umleitung verweigert, können folgende Prüfungen durchgeführt werden:

* Der vom SUT zurückgegebene Statuscode kann mit dem erwarteten [HttpStatusCode. Redirect](/dotnet/api/system.net.httpstatuscode) -Ergebnis überprüft werden, nicht nach der Umleitung zur Anmeldeseite ( [HttpStatusCode. OK](/dotnet/api/system.net.httpstatuscode)).
* Der Wert für den `Location`-Header in den Antwort Headern wird überprüft, um zu bestätigen, dass er mit `http://localhost/Identity/Account/Login`beginnt, nicht mit der Antwort auf die abschließende Anmeldeseite, bei der der `Location` Header nicht vorhanden wäre.

Die Test-App kann eine <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in " [konfigurierstellungsdiensteserver](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) " simulieren, um Aspekte der Authentifizierung und Autorisierung zu testen. In einem minimalen Szenario wird ein [authenticateresult. Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)-Vorgang zurückgegeben:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

Der `TestAuthHandler` wird aufgerufen, um einen Benutzer zu authentifizieren, wenn das Authentifizierungsschema auf `Test` festgelegt ist, in dem `AddAuthentication` für `ConfigureTestServices`registriert ist:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

Weitere Informationen zu `WebApplicationFactoryClientOptions`finden Sie im Abschnitt " [Client Optionen](#client-options) ".

## <a name="set-the-environment"></a>Festlegen der Umgebung

Standardmäßig ist die Host-und App-Umgebung des SUTs für die Verwendung der Entwicklungsumgebung konfiguriert. So überschreiben Sie die Umgebung von SUT:

* Legen Sie die Umgebungsvariable `ASPNETCORE_ENVIRONMENT` fest (z. b. `Staging`, `Production`oder einen anderen benutzerdefinierten Wert, z. b. `Testing`).
* Überschreiben Sie `CreateHostBuilder` in der Test-App, um Umgebungsvariablen mit dem Präfix `ASPNETCORE`zu lesen.

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Wie die Testinfrastruktur den Stamm Pfad der App-Inhalte ableitet

Der `WebApplicationFactory`-Konstruktor leitet den Stamm Pfad der APP- [Inhalte](xref:fundamentals/index#content-root) ab, indem er nach einem [webapplicationfactorycontentrootattribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) in der Assembly sucht, die die Integrationstests mit einem Schlüssel gleich der `TEntryPoint` Assembly `System.Reflection.Assembly.FullName`enthält. Wenn ein Attribut mit dem richtigen Schlüssel nicht gefunden wird, wird `WebApplicationFactory` auf die Suche nach einer Projektmappendatei ( *. sln*) zurückgegriffen und den `TEntryPoint` Assemblynamen an das Projektmappenverzeichnis angefügt. Das Stammverzeichnis der APP (der Inhalts Stamm Pfad) wird verwendet, um Sichten und Inhalts Dateien zu ermitteln.

## <a name="disable-shadow-copying"></a>Schatten Kopie deaktivieren

Das Durchführen von Schatten Kopien bewirkt, dass die Tests in einem anderen Verzeichnis als dem Ausgabeverzeichnis ausgeführt werden. Damit Tests ordnungsgemäß funktionieren, muss die Schatten Kopie deaktiviert werden. Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) verwendet xUnit und deaktiviert das Kopieren von Schatten Kopien für xUnit durch Einschließen einer *xUnit. Runner. JSON* -Datei mit der richtigen Konfigurationseinstellung. Weitere Informationen finden Sie unter [Konfigurieren von xUnit mit JSON](https://xunit.github.io/docs/configuring-with-json.html).

Fügen Sie die Datei " *xUnit. Runner. JSON* " dem Stamm des Testprojekts mit folgendem Inhalt hinzu:

```json
{
  "shadowCopy": false
}
```

Wenn Sie Visual Studio verwenden, legen Sie die Eigenschaft **in Ausgabeverzeichnis kopieren** auf **immer kopieren**fest. Wenn Sie Visual Studio nicht verwenden, fügen Sie der Projektdatei der Test-App ein `Content` Ziel hinzu:

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a>Entfernen von Objekten

Nachdem die Tests der `IClassFixture`-Implementierung ausgeführt wurden, werden [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) und [HttpClient](/dotnet/api/system.net.http.httpclient) verworfen, wenn xUnit die [webapplicationfactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)freigibt. Wenn Objekte, die vom Entwickler instanziiert werden, gelöscht werden müssen, müssen Sie Sie in der `IClassFixture`-Implementierung verwerfen. Weitere Informationen finden Sie unter [Implementieren einer](/dotnet/standard/garbage-collection/implementing-dispose)verwerfen-Methode.

## <a name="integration-tests-sample"></a>Beispiel für Integrationstests

Die [Beispiel-App](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) besteht aus zwei apps:

| App- | Projektverzeichnis | Beschreibung |
| --- | ----------------- | ----------- |
| Message-app (Das SUT) | *src/RazorPagesProject* | Ermöglicht Benutzern das Hinzufügen, löschen, löschen und Analysieren von Nachrichten. |
| Testen der App | *tests/RazorPagesProject.Tests* | Wird verwendet, um das SUT zu integrieren. |

Die Tests können mit den integrierten Testfunktionen einer IDE ausgeführt werden, z. b. [Visual Studio](https://visualstudio.microsoft.com). Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl an einer Eingabeaufforderung im Verzeichnis *Tests/razorpagesproject. Tests* aus:

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a>Nachrichten-APP-Organisation (SUT)

Das untergeordnete Element ist ein Razor Pages Nachrichtensystem mit folgenden Merkmalen:

* Die Index Seite der APP (*pages/index. cshtml* und *pages/index. cshtml. cs*) stellt eine UI-und Seiten Modell Methode zum Steuern der Addition, Löschung und Analyse von Meldungen (durchschnittliche Wörter pro Nachricht) bereit.
* Eine Meldung wird von der `Message`-Klasse (*Data/Message. cs*) mit zwei Eigenschaften beschrieben: `Id` (Key) und `Text` (Message). Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.
* Nachrichten werden mithilfe [der in-Memory Database](/ef/core/providers/in-memory/)&#8224;Entity Framework gespeichert.
* Die app enthält eine Datenzugriffs Schicht (Data Access Layer, DAL) in der zugehörigen Daten Bank Kontext Klasse `AppDbContext` (*Data/appdbcontext. cs*).
* Wenn die Datenbank beim Starten der APP leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert.
* Die app enthält eine `/SecurePage`, auf die nur ein authentifizierter Benutzer zugreifen kann.

&#8224;Das EF-Thema [Testen mit inMemory](/ef/core/miscellaneous/testing/in-memory)erläutert, wie Sie eine in-Memory Database für Tests mit MSTest verwenden. In diesem Thema wird das [xUnit](https://xunit.github.io/) -Test Framework verwendet. Testkonzepte und Test Implementierungen in verschiedenen Test-Frameworks sind ähnlich, aber nicht identisch.

Obwohl die APP das Repository-Muster nicht verwendet und kein effektives Beispiel für das [Muster der Arbeitseinheit (Unit of Work, UOW)](https://martinfowler.com/eaaCatalog/unitOfWork.html)ist, werden Razor Pages diese Entwicklungsmuster unterstützt. Weitere Informationen finden Sie unter [Entwerfen der Infrastruktur-Persistenzebene](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und der [Test Controller Logik](/aspnet/core/mvc/controllers/testing) (im Beispiel wird das Repository-Muster implementiert).

### <a name="test-app-organization"></a>Testen der APP-Organisation

Bei der Test-App handelt es sich um eine Konsolen-App im Verzeichnis *Tests/razorpagesproject. Tests* .

| Test-App-Verzeichnis | Beschreibung |
| ------------------ | ----------- |
| *Authtests* | Enthält Testmethoden für:<ul><li>Zugreifen auf eine sichere Seite durch einen nicht authentifizierten Benutzer.</li><li>Zugreifen auf eine sichere Seite durch einen authentifizierten Benutzer mit einem Mock-<xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</li><li>Abrufen eines GitHub-Benutzerprofils und Überprüfen der Benutzeranmeldung des Profils.</li></ul> |
| *Basictests* | Enthält eine Testmethode für Routing und Inhaltstyp. |
| *Integrationtests* | Enthält die Integrationstests für die Index Seite mithilfe der benutzerdefinierten `WebApplicationFactory`-Klasse. |
| *Hilfsprogramme/Hilfsprogramme* | <ul><li>*Utilities.cs* enthält die `InitializeDbForTests` Methode, mit der die Datenbank mit Testdaten als Ausgangswert verwendet wird.</li><li>*HtmlHelpers.cs* stellt eine Methode bereit, mit der ein anglesharp-`IHtmlDocument` für die Verwendung durch die Testmethoden zurückgegeben wird.</li><li>*HttpClientExtensions.cs* Bereitstellen von über Ladungen für `SendAsync` zum Übermitteln von Anforderungen an das SUT.</li></ul> |

Das Test Framework ist " [xUnit](https://xunit.github.io/)". Integrationstests werden mithilfe von " [Microsoft. aspnetcore. Testhost](/dotnet/api/microsoft.aspnetcore.testhost)" durchgeführt, das den [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver)enthält. Da das [Microsoft. aspnetcore. MVC. testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) -Paket zum Konfigurieren des Test Hosts und des Test Servers verwendet wird, sind für die `TestHost`-und `TestServer` Pakete in der Projektdatei der Test-App oder der Entwickler Konfiguration in der Test-App keine direkten Paket Verweise erforderlich.

**Seeding der Datenbank zum Testen**

Für Integrationstests ist in der Regel vor der Testausführung ein kleines DataSet in der Datenbank erforderlich. Beispielsweise wird ein Löschvorgang für einen Löschvorgang eines Datensatzes durchgeführt, sodass die Datenbank mindestens einen Datensatz aufweisen muss, damit die Löschanforderung erfolgreich ausgeführt wird.

Die Beispiel-App startet die Datenbank mit drei Nachrichten in *Utilities.cs* , die von Tests bei der Ausführung verwendet werden können:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a>Weitere Ressourcen

* [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
