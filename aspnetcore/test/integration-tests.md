---
title: Integrationstests in ASP.NET Core
author: guardrex
description: In diesem Artikel erfahren Sie, wie Integrationstests sicherstellen, dass die Komponenten einer App auf der Infrastrukturebene ordnungsgemäß funktionieren, einschließlich der Datenbank, dem Dateisystem und dem Netzwerk.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: test/integration-tests
ms.openlocfilehash: 11a8f4296e1b0b229c736645f1aa598307b88ec4
ms.sourcegitcommit: 088e6744cd67a62f214f25146313a53949b17d35
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58320185"
---
# <a name="integration-tests-in-aspnet-core"></a>Integrationstests in ASP.NET Core

Durch [Luke Latham](https://github.com/guardrex) und [Steve Smith](https://ardalis.com/)

Integrationstests stellen Sie sicher, dass der app-Komponenten auf einer Ebene ordnungsgemäß, die unterstützende Infrastruktur der app, wie z. B. die Datenbank, Dateisystem und Netzwerk enthält. ASP.NET Core unterstützt Integrationstests, die mit einem Komponententest-Framework einen testwebhost und ein in-Memory-Testserver.

In diesem Thema wird davon ausgegangen, ein grundlegendes Verständnis der Komponententests. Wenn mit Konzepte nicht vertraut sind, finden Sie unter den [Unit Testing in .NET Core und .NET Standard](/dotnet/core/testing/) Thema und seine verknüpften Inhalt.

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

Die Beispiel-app ist eine Razor-Seiten-app und geht davon aus ein grundlegendes Verständnis der Razor-Seiten. Wenn Sie mit Razor-Seiten nicht vertraut sind, finden Sie unter den folgenden Themen:

* [Introduction to Razor Pages (Einführung in Razor Pages)](xref:razor-pages/index)
* [Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)
* [Komponententests für Razor-Seiten](xref:test/razor-pages-tests)

> [!NOTE]
> Testen von SPAs, es empfiehlt sich ein Tool wie z. B. [Selenium](https://www.seleniumhq.org/), kann der ein Browsers automatisieren.

## <a name="introduction-to-integration-tests"></a>Einführung in die Integrationstests

Integrationstests Auswerten einer app-Komponenten auf einen umfassenderen als [Komponententests](/dotnet/core/testing/). Komponententests werden verwendet, um isolierte Softwarekomponenten wie einzelne Klasse, Methoden zu testen. Integrationstests zu bestätigen, dass zwei oder mehr app-Komponenten zusammenarbeiten, um ein erwartetes Ergebnis, das möglicherweise auch jede Komponente erforderlich, um vollständig verarbeiten einer Anforderung zu erstellen.

Diese breiter angelegte Tests werden verwendet, um Infrastruktur und das gesamte Framework an, z. B. durch die folgenden Komponenten der app zu testen:

* Datenbank
* Dateisystem
* Netzwerkgeräte
* Anforderung / Antwort-pipeline

Komponententests verwenden, die erstellte mir Komponenten, genannt *fakes* oder *Mockobjekte*, anstelle von Infrastrukturkomponenten.

Im Gegensatz zu Komponententests tests Integration:

* Verwenden Sie die eigentlichen Komponenten, die die app in der Produktion verwendet.
* Benötigen Sie weitere Code und die Datenverarbeitung.
* Dauern Sie zum Ausführen länger.

Aus diesem Grund schränken Sie die Verwendung von Integrationstests für die wichtigsten Szenarien der Infrastruktur an. Wenn ein Verhalten mit einem Komponententest oder einen Integrationstest getestet werden kann, wählen Sie den Komponententest aus.

> [!TIP]
> Schreiben Sie Integrationstest für jede mögliche Permutation und Zugriff mit Datenbanken und Dateisysteme nicht. Unabhängig davon, wie viele in eine app werden die Interaktion mit Datenbanken und Dateisysteme, einen fokussierten Satz von Lese-, Schreib-, Update und Delete-Integration Tests werden in der Regel ausreichend Testdatenbank kann und die Datei der Systemkomponenten aus. Verwenden Sie Komponententests für routinemäßige Tests der Methodenlogik, mit die diese Komponenten interagieren. In Komponententests Fake-/Pseudoobjekten die Verwendung der Infrastruktur führen zu schnelleren Ausführung des Tests.

> [!NOTE]
> In Diskussionen von Integrationstests, das getestete Projekt wird häufig aufgerufen, die *zu testende System*, oder "GS", kurz.

## <a name="aspnet-core-integration-tests"></a>ASP.NET Core-Integrationstests

Integrationstests in ASP.NET Core wird Folgendes benötigt:

* Ein Testprojekt wird verwendet, enthalten und die Tests auszuführen. Das Testprojekt enthält einen Verweis auf den getesteten ASP.NET Core-Projekt Namens der *zu testende System* (GS). _"GS" wird in diesem Thema verwendet, um auf die getestete app zu verweisen._
* Das Testprojekt einen testwebhost für das GS erstellt und verwendet einen Testclient für Server, um Anforderungen und-Antworten werden dem GS Auslöser zu behandeln.
* Ein Test Runner wird verwendet, um die Tests und der Bericht die Testergebnisse auszuführen.

Integrationstests führen Sie eine Sequenz von Ereignissen, die die üblichen *anordnen*, *Act*, und *Assert* Testschritte:

1. GSs-Web-Host wird konfiguriert.
1. Ein Testclient-Server wird erstellt, um Anforderungen an die app zu senden.
1. Die *anordnen* Schritt ausgeführt wird: Die Test-app wird vorbereitet, eine Anforderung.
1. Die *Act* Schritt ausgeführt wird: Der Client sendet die Anforderung und empfängt die Antwort.
1. Die *Assert* Schritt ausgeführt wird: Die *tatsächliche* Antwort wird als validiert eine *übergeben* oder *fehlschlagen* basierend auf einer *erwartet* Antwort.
1. Der Prozess wird fortgesetzt, bis alle Tests ausgeführt werden.
1. Die Testergebnisse werden gemeldet.

Der testwebhost ist in der Regel anders konfiguriert, als normalen Web-Host für den Test der Anwendung ausgeführt wird. Beispielsweise können eine andere Datenbank oder andere app-Einstellungen für die Tests verwendet werden.

Infrastrukturkomponenten, wie etwa die testwebhost und in-Memory-Testserver ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), bereitgestellt oder verwaltet von der [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) Paket. Mithilfe dieses Pakets optimiert die Test-Erstellung und Ausführung.

Die `Microsoft.AspNetCore.Mvc.Testing` -Paket verarbeitet die folgenden Aufgaben:

* Kopiert die Abhängigkeitsdatei (*\*.deps*) vom GS im Testprojekt *Bin* Ordner.
* Legt das inhaltsstammverzeichnis GSs Projektstamm fest, damit, dass statische Dateien und Seiten/Ansichten gefunden werden, wenn die Tests ausgeführt werden.
* Stellt die [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) Klasse für die Optimierung bootstrapping GS mit `TestServer`.

Die [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) Dokumentation wird beschrieben, wie ein und Erstellen eines Testprogramm, sowie ausführliche Anleitungen zum Ausführen von Tests und Empfehlungen dafür, wie Namen Tests aus, und Testklassen einrichten.

> [!NOTE]
> Wenn ein Testprojekt für eine app erstellen möchten, trennen Sie die Komponententests von Integrationstests in verschiedene Projekte. Damit wird sichergestellt, dass Tests Infrastrukturkomponenten nicht versehentlich in die Komponententests enthalten sind. Ermöglicht die Trennung von Unit- und Integrationstests auch steuern, über welche Gruppe von Tests ausgeführt werden.

Es gibt praktisch keinen Unterschied zwischen der Konfiguration für Tests von apps mit Razor-Seiten und MVC-apps. Der einzige Unterschied besteht in der Benennung von Tests. In einer Razor Pages-app, Tests von Seite-Endpunkten in der Regel nach der Seitenklasse für das Modell benannt (z. B. `IndexPageTests` auf die Komponente für die Indexseite Integrationstest). In einer MVC-app, Tests sind in der Regel nach Controllerklassen organisiert und nach den Controllern, die sie testen benannt (z. B. `HomeControllerTests` zum Testen der Komponentenintegration für den Home-Controller).

## <a name="test-app-prerequisites"></a>App-Test-Voraussetzungen

Müssen das Testprojekt:

* Verweisen Sie auf die folgenden Pakete:
  * [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* Geben Sie die Webdienst-SDK in der Projektdatei (`<Project Sdk="Microsoft.NET.Sdk.Web">`). Die Webdienst-SDK ist erforderlich, beim Verweisen auf die [Microsoft.AspNetCore.App metapaket](xref:fundamentals/metapackage-app).

Diese erforderlichen Komponenten finden Sie in der [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples/). Überprüfen Sie die *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* Datei. Die beispielanwendung verwendet die [xUnit](https://xunit.github.io/) Testframework und die [AngleSharp](https://anglesharp.github.io/) Parser-Bibliothek, damit die Beispiel-app auch verweist:

* [xunit](https://www.nuget.org/packages/xunit/)
* [xunit.runner.visualstudio](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [AngleSharp](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a>GS-Umgebung

Wenn des GS [Umgebung](xref:fundamentals/environments) ist nicht festgelegt, die Standardwerte der Umgebung für die Entwicklung.

## <a name="basic-tests-with-the-default-webapplicationfactory"></a>Grundlegende Tests mit der standardmäßigen WebApplicationFactory

[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) dient zum Erstellen einer [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) für Integrationstests. `TEntryPoint` ist die Einstiegspunktklasse des GS, in der Regel die `Startup` Klasse.

Test-Klassen implementieren einen *Klasse Fixture* Schnittstelle ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) an, dass die Klasse enthält Tests, und geben Sie die freigegebenen Instanzen zwischen den Tests in der Klasse.

### <a name="basic-test-of-app-endpoints"></a>Grundlegenden Tests des app-Endpunkte

Der folgende test-Klasse, `BasicTests`, verwendet der `WebApplicationFactory` zu bootstrap das GS ein ["HttpClient"](/dotnet/api/system.net.http.httpclient) mit einer Testmethode `Get_EndpointsReturnSuccessAndCorrectContentType`. Überprüft die Methode, wenn der Statuscode der Antwort erfolgreich ausgeführt wird (Statuscodes im Bereich 200-299 zurückgibt) und die `Content-Type` Header `text/html; charset=utf-8` für mehrere app-Seiten.

[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) erstellt eine Instanz des `HttpClient` automatisch folgende umleitungen und verarbeitet Cookies.

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

### <a name="test-a-secure-endpoint"></a>Testen Sie einen sicheren Endpunkt

Ein weiterer Test in der `BasicTests` Klasse überprüft, dass ein sicherer Endpunkt nicht authentifizierten Benutzer an der app-Anmeldeseite umgeleitet.

Im GS die `/SecurePage` verwendet eine [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) Konvention zum Anwenden einer [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf der Seite. Weitere Informationen finden Sie unter [autorisierungskonventionen für Razor Pages](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

In der `Get_SecurePageRequiresAnAuthenticatedUser` zu testen, eine [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) wird festgelegt, umleitungen zu unterbinden, indem Sie festlegen [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) zu `false`:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

Durch Verhindern des Clients für die Umleitung zu folgen, können die folgenden Prüfungen erfolgen:

* Die vom GS zurückgegebene Statuscode kann überprüft werden für den erwarteten [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) Ergebnis, nach der Umleitung zur Anmeldeseite, die nicht der endgültige Statuscode [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).
* Die `Location` Headerwert in die Header der Antwort wird überprüft, um sicherzustellen, dass er mit beginnt `http://localhost/Identity/Account/Login`, nicht die letzte Anmeldung Seitenantwort, in denen die `Location` Header wäre nicht vorhanden sein.

Weitere Informationen zu `WebApplicationFactoryClientOptions`, finden Sie unter den [Clientoptionen](#client-options) Abschnitt.

## <a name="customize-webapplicationfactory"></a>Anpassen von WebApplicationFactory

Webhostkonfiguration kann unabhängig von den Testklassen erstellt werden, durch Erben von `WebApplicationFactory` um eine oder mehrere benutzerdefinierte Factorys zu erstellen:

1. Erben von `WebApplicationFactory` und überschreiben [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost). Die [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ermöglicht die Konfiguration der Sammlung von Diensten mit ["configureservices"](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   Im seeding der Datenbank die [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) erfolgt durch die `InitializeDbForTests` Methode. Die Methode wird beschrieben, der [Integrationstests-Beispiel: Testen der app-Organisation](#test-app-organization) Abschnitt.

2. Verwenden Sie die benutzerdefinierte `CustomWebApplicationFactory` in Testklassen. Im folgenden Beispiel wird die Factory in der `IndexPageTests` Klasse:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   Die Beispiel-app-Client ist so konfiguriert, dass zu verhindern, dass die `HttpClient` aus folgenden umleitungen. Siehe die [einen sicheren Endpunkt testen](#test-a-secure-endpoint) Abschnitt Dies ermöglicht die Tests, um das Ergebnis der erste Antwort von der app zu überprüfen. Die erste Reaktion ist eine Umleitung in vielen dieser Tests mit einer `Location` Header.

3. Ein typische Test verwendet die `HttpClient` und Hilfsmethoden zum Verarbeiten der Anforderung und die Antwort:

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

Alle POST-Anforderung an das GS erfüllen muss die antiforgery-Überprüfung, die automatisch mit der app erfolgt [antiforgery Datenschutzsystem](xref:security/data-protection/introduction). Um für einen Test der POST-Anforderung anordnen zu können, müssen die Test-app:

1. Stellen Sie eine Anforderung für die Seite.
1. Analysieren Sie die antiforgery Cookie und Überprüfung Anfordern eines Tokens aus der Antwort.
1. Stellen Sie die POST-Anforderung mit dem Cookie und Anforderung antifälschungsvalidierung token vorhanden.

Die `SendAsync` Hilfsmethoden für die Erweiterung (*Helpers/HttpClientExtensions.cs*) und die `GetDocumentAsync` Hilfsmethode (*Helpers/HtmlHelpers.cs*) in der [-Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples/) verwenden die [AngleSharp](https://anglesharp.github.io/) Parser behandelt die antiforgery Überprüfung mit den folgenden Methoden:

* `GetDocumentAsync` &ndash; Empfängt die [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) und gibt eine `IHtmlDocument`. `GetDocumentAsync` eine Factory, die vorbereitet wird eine *virtuellen Antwort* auf Grundlage der ursprünglichen `HttpResponseMessage`. Weitere Informationen finden Sie unter den [AngleSharp Dokumentation](https://github.com/AngleSharp/AngleSharp#documentation).
* `SendAsync` Erweiterungsmethoden für die `HttpClient` compose eine [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) , und rufen Sie [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) , Anforderungen werden dem GS Auslöser zu senden. Überladungen für `SendAsync` akzeptieren Sie die HTML-Formular (`IHtmlFormElement`) und die folgenden:
  * Schaltfläche der Form "Senden" (`IHtmlElement`)
  * Formular Werte Auflistung (`IEnumerable<KeyValuePair<string, string>>`)
  * Schaltfläche "Senden" (`IHtmlElement`) und Werte bilden (`IEnumerable<KeyValuePair<string, string>>`)

> [!NOTE]
> [AngleSharp](https://anglesharp.github.io/) ist ein Drittanbieter-Analyse-Bibliothek, die in diesem Thema und die Beispiel-app zu Demonstrationszwecken verwendet. AngleSharp wird nicht unterstützt, oder für Integrationstests von ASP.NET Core-apps erforderlich sind. Andere Parser können verwendet werden, z. B. die [HTML-Agilität Pack (HAP)](http://html-agility-pack.net/). Ein anderer Ansatz ist Code schreiben, um der antiforgery Systemvariable Überprüfung Anfordern eines Tokens und antiforgery Cookie direkt behandeln.

## <a name="customize-the-client-with-withwebhostbuilder"></a>Anpassen des Clients mit WithWebHostBuilder

Wenn zusätzliche Konfigurationsschritte erforderlich, innerhalb einer Testmethode ist [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) erstellt ein neues `WebApplicationFactory` mit einer [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) , die weitere Konfiguration angepasst ist.

Die `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test-Methode von der [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) veranschaulicht die Verwendung von `WithWebHostBuilder`. Dieser Test führt einen Löschvorgang Datensatz in der Datenbank durch Auslösen einer Formularübergabe im GS.

Da eine andere Testen in der `IndexPageTests` Klasse führt einen Vorgang, der alle Datensätze in der Datenbank gelöscht, und führen kann, bevor die `Post_DeleteMessageHandler_ReturnsRedirectToRoot` -Methode, die Datenbank wird in dieser Testmethode aus, um sicherzustellen, dass ein Datensatz vorhanden ist, für das GS löschen ist ein Seeding durchgeführt. Auswählen der `deleteBtn1` -Schaltfläche der `messages` Form im GS wird in der Anforderung werden dem GS Auslöser simuliert:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a>Clientoptionen

Die folgende Tabelle zeigt die [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) verfügbar, beim Erstellen von `HttpClient` Instanzen.

| Option | Beschreibung | Standard |
| ------ | ----------- | ------- |
| [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ruft ab oder legt ihn fest, ob `HttpClient` Instanzen sollte Umleitung Antworten automatisch folgen. | `true` |
| [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Übernimmt oder bestimmt die Basisadresse des `HttpClient` Instanzen. | `http://localhost` |
| [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ruft ab oder legt ihn fest, ob `HttpClient` Instanzen sollten Cookies verarbeiten. | `true` |
| [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Übernimmt oder bestimmt der maximalen Anzahl von umleitungs-Antworten, die `HttpClient` Instanzen folgen soll. | 7 |

Erstellen der `WebApplicationFactoryClientOptions` Klasse und übergeben es an der [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) -Methode (Standard-Werte werden im Codebeispiel gezeigt):

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a>Mock-Dienste einzufügen

Dienste können überschrieben werden, in einem Test mit einem Aufruf von [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) auf dem Host-Generator. **Um pseudodienste einzufügen, müssen das GS ein `Startup` -Klasse mit einer `Startup.ConfigureServices` Methode.**

Das GS-Beispiel enthält einen bereichsbezogenen Dienst, der ein Zitat zurückgibt. Das Angebot wird in einem ausgeblendeten Feld auf der Indexseite eingebettet, wenn die Indexseite angefordert wird.

*Services/IQuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

*Services/QuoteService.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

*Startup.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

*Pages/Index.cshtml.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

*Pages/Index.cs*:

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

Das folgende Markup wird generiert, wenn die GS-app ausgeführt wird:

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

Um die Einfügung-Dienst und Anführungszeichen in einem Integrationstest zu testen, wird ein mock-Dienst in dem GS vom Test eingefügt. Der mock Dienst ersetzt der app `QuoteService` mit einem Dienst, der von der Test-app bereitgestellt wird, wird aufgerufen, `TestQuoteService`:

*IntegrationTests.IndexPageTests.cs*:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

`ConfigureTestServices` wird aufgerufen, und die Bereichsbezogene Dienst registriert ist:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

Das Markup, das während des Tests Ausführung erzeugt spiegelt der Angebots-Text, der vom `TestQuoteService`, daher der Assertion übergibt:

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a>Wie leitet die Test-Infrastruktur des inhaltsstammpfads app

Die `WebApplicationFactory` Konstruktor leitet die app-inhaltsstammpfads durch Suchen nach einem [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) auf die Assembly mit den Integrationstests mit einem Schlüssel, die gleich der `TEntryPoint` Assembly `System.Reflection.Assembly.FullName`. Für den Fall, dass ein Attribut mit dem richtigen Schlüssel nicht gefunden wird, `WebApplicationFactory` greift auf die Suche nach einer Projektmappendatei zurück (*\*sln*) und fügt die `TEntryPoint` Assemblynamen, um das Projektmappenverzeichnis. Das app-Stammverzeichnis (des inhaltsstammpfads) wird zur Ermittlung von Ansichten und Inhaltsdateien verwendet.

In den meisten Fällen müssen Sie nicht explizit das inhaltsstammverzeichnis app festgelegt wie die Logik für die Suche in der Regel das richtige inhaltsstammverzeichnis zur Laufzeit sucht. In besonderen Szenarien, in dem das inhaltsstammverzeichnis nicht gefunden wird, mithilfe des integrierten Suchalgorithmus, der app Content Root kann explizit oder indem Sie benutzerdefinierte Logik angegeben werden. Rufen Sie zum Stammverzeichnis der app-Inhalte in diesen Szenarien Festlegen der `UseSolutionRelativeContentRoot` Erweiterungsmethode aus der [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost) Paket. Geben Sie der Projektmappe relativen Pfad und eine optionale Lösung, Dateien, Namen oder die Glob-Muster (Standardwert = `*.sln`).

Rufen Sie die [UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot) Erweiterung Methode mit *eine* der folgenden Methoden:

* Beim Konfigurieren von Testklassen mit `WebApplicationFactory`, geben Sie eine benutzerdefinierte Konfiguration mit der [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):

   ```csharp
   public IndexPageTests(
       WebApplicationFactory<RazorPagesProject.Startup> factory)
   {
       var _factory = factory.WithWebHostBuilder(builder =>
       {
           builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

           ...
       });
   }
   ```

* Beim Konfigurieren von Testklassen mit einer benutzerdefinierten `WebApplicationFactory`, erben von `WebApplicationFactory` und überschreiben [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):

   ```csharp
   public class CustomWebApplicationFactory<TStartup>
       : WebApplicationFactory<RazorPagesProject.Startup>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

               ...
           });
       }
   }
   ```

## <a name="disable-shadow-copying"></a>Deaktivieren von Schattenkopien

Erstellen von Schattenkopien führt dazu, dass die Tests in einem anderen Ordner als den Ausgabeordner ausführen. Für die Tests ordnungsgemäß funktioniert muss die Option Schattenkopien deaktiviert werden. Die [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) verwendet xUnit und deaktiviert die Schattenkopiefunktion für xUnit dazu ein *xunit.runner.json* -Datei mit der richtigen Einstellung. Weitere Informationen finden Sie unter [Konfigurieren von xUnit mit JSON](https://xunit.github.io/docs/configuring-with-json.html).

Hinzufügen der *xunit.runner.json* Datei zum Stamm des Testprojekts mit dem folgenden Inhalt:

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a>Verwerfen von Objekten

Nach den Tests für die `IClassFixture` Implementierung ausgeführt wird, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) und ["HttpClient"](/dotnet/api/system.net.http.httpclient) verworfen werden, wenn xUnit freigibt, die [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) . Wenn Objekte instanziiert, indem der Entwickler Freigabe erforderlich, entsorgen Sie sie in der `IClassFixture` Implementierung. Weitere Informationen finden Sie unter [Implementieren einer Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose).

## <a name="integration-tests-sample"></a>Tests-integrationsbeispiel

Die [Beispiel-app](https://github.com/aspnet/Docs/tree/master/aspnetcore/test/integration-tests/samples) besteht aus zwei apps:

| App | Projektordner | Beschreibung |
| --- | -------------- | ----------- |
| Nachrichten-app (GS) | *src/RazorPagesProject* | Ermöglicht es einem Benutzer hinzufügen, löschen, löschen Sie alle und Analysieren von Nachrichten. |
| Testen der app | *tests/RazorPagesProject.Tests* | Zum Integrationstest GS verwendet. |

Die Tests können ausgeführt werden, verwenden integrierte Funktionen von einer IDE, z. B. [Visual Studio](https://www.visualstudio.com/vs/). Wenn [Visual Studio Code](https://code.visualstudio.com/) oder der Befehlszeile, und führen Sie den folgenden Befehl an einer Eingabeaufforderung in das *tests/RazorPagesProject.Tests* Ordner:

```console
dotnet test
```

### <a name="message-app-sut-organization"></a>Nachrichten-app (GS)-Organisation

Das GS ist ein Nachrichtensystem für Razor-Seiten mit den folgenden Merkmalen:

* Die Indexseite der app (*Pages/Index.cshtml* und *Pages/Index.cshtml.cs*) eine Benutzeroberfläche und die Seite stellt Modellmethoden bereit, um zu steuern, das Hinzufügen, löschen und Analyse von Nachrichten (durchschnittliche Wörter pro Nachricht) .
* Eine Nachricht wird beschrieben, durch die `Message` Klasse (*Data/Message.cs*) mit zwei Eigenschaften: `Id` (Schlüssel) und `Text` (Nachricht). Die `Text` Eigenschaft ist erforderlich, und bis zu 200 Zeichen.
* Nachrichten werden gespeichert mit [des Entity Framework-in-Memory-Datenbank](/ef/core/providers/in-memory/)&#8224;.
* Die app enthält eine Datenzugriffsschicht (DAL) in der Datenbank-Kontextklasse `AppDbContext` (*Data/AppDbContext.cs*).
* Wenn die Datenbank beim Start der app leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert.
* Die app enthält eine `/SecurePage` , kann nur von einem authentifizierten Benutzer zugegriffen werden.

&#8224;Das Thema EF [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory), wird erläutert, wie eine in-Memory-Datenbank für Tests mit MSTest verwenden. In diesem Thema verwendet die [xUnit](https://xunit.github.io/) Testframework. Test-Implementierung für andere Testframeworks und Konzepte sind ähnlich, aber nicht identisch.

Obwohl die app nicht das Repositorymuster verwenden und kein Beispiel für effektive ist der [Arbeitseinheit (UoW) Muster](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor-Seiten unterstützt diese Muster der Entwicklung. Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und [Testen von Controllerlogik](/aspnet/core/mvc/controllers/testing) (im Beispiel wird das Repositorymuster implementiert).

### <a name="test-app-organization"></a>Testen der app-Organisation

Die Test-app ist eine Konsolen-app in der *tests/RazorPagesProject.Tests* Ordner.

| Test-app-Ordner | Beschreibung |
| --------------- | ----------- |
| *BasicTests* | *BasicTests.cs* Testmethoden für routing, Zugriff auf eine sichere Seite durch ein nicht authentifizierter Benutzer und ein Benutzerprofil von GitHub abrufen und überprüfen das Profil für die Anmeldung des Benutzers enthält. |
| *IntegrationTests* | *IndexPageTests.cs* enthält die Integrationstests für die Indexseite mit benutzerdefinierten `WebApplicationFactory` Klasse. |
| *Hilfsprogramme/-Dienstprogramme* | <ul><li>*Utilities.cs* enthält die `InitializeDbForTests` Methode verwendet, um das Seeding der Datenbank mit Testdaten.</li><li>*HtmlHelpers.cs* bietet eine Methode zum Zurückgeben einer AngleSharp `IHtmlDocument` für die Verwendung durch die Testmethoden.</li><li>*HttpClientExtensions.cs* bieten Überladungen für `SendAsync` , Anforderungen werden dem GS Auslöser zu senden.</li></ul> |

Das Testframework ist [xUnit](https://xunit.github.io/). Integrationstests durchgeführt werden, mithilfe der [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), wozu die [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver). Da die [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) Paket wird verwendet, um den Host "und" Test Testserver, konfigurieren die `TestHost` und `TestServer` Pakete erfordern keine direkte paketverweisen in der Test-app-Projektdatei oder entwicklerkonfiguration in der Test-app.

**Das Seeding der Datenbank zu Testzwecken**

Integrationstests erfordern in der Regel ein kleines Dataset in der Datenbank vor der Ausführung des Tests. Beispielsweise ein Löschvorgang testen Aufrufe für einen Datensatz löschen, damit die Datenbank mindestens ein Datensatz für die Delete-Anforderung erfolgreich ausgeführt werden muss.

Die Beispiel-app startet die Datenbank mit drei Nachrichten im *Utilities.cs* , dass Tests verwendet werden können, wenn sie ausgeführt:

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [Komponententests für Razor-Seiten](xref:test/razor-pages-tests)
* [Middleware](xref:fundamentals/middleware/index)
* [Testen von Controllern](xref:mvc/controllers/testing)
