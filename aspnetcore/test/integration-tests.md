---
<span data-ttu-id="29317-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-102">'Blazor'</span></span>
- <span data-ttu-id="29317-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-103">'Identity'</span></span>
- <span data-ttu-id="29317-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-105">'Razor'</span></span>
- <span data-ttu-id="29317-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="29317-107">Integrationstests in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29317-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="29317-108">Von [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/) und [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="29317-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="29317-109">Integrationstests stellen sicher, dass die Komponenten einer App auf einer Ebene, die die unterstützende Infrastruktur der App (wie die Datenbank, das Dateisystem und das Netzwerk) umfasst, ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="29317-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="29317-110">ASP.NET Core unterstützt Integrationstests mithilfe eines Komponententest-Frameworks mit einem Testwebhost und einem In-Memory-Testserver.</span><span class="sxs-lookup"><span data-stu-id="29317-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="29317-111">In diesem Thema werden Grundkenntnisse über Komponententests vorausgesetzt.</span><span class="sxs-lookup"><span data-stu-id="29317-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="29317-112">Wenn Sie nicht mit Testkonzepten vertraut sind, lesen Sie das Thema [Komponententests in .NET Core und .NET Standard](/dotnet/core/testing/) und zugehörige Inhalte.</span><span class="sxs-lookup"><span data-stu-id="29317-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="29317-113">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="29317-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="29317-114">Bei der Beispiel-App handelt es sich um eine Razor Pages-App, hierfür werden grundlegende Kenntnisse über Razor Pages vorausgesetzt.</span><span class="sxs-lookup"><span data-stu-id="29317-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="29317-115">Wenn Sie nicht mit Razor Pages vertraut sind, lesen Sie die folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="29317-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="29317-116">[Einführung in Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="29317-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="29317-117">[Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="29317-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="29317-118">[Komponententests für Razor-Seiten](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="29317-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="29317-119">Zum Testen von Single-Page-Webanwendungen empfiehlt es sich, ein Tool wie [Selenium](https://www.seleniumhq.org/) zu verwenden, mit dem ein Browser automatisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="29317-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="29317-120">Einführung in Integrationstests</span><span class="sxs-lookup"><span data-stu-id="29317-120">Introduction to integration tests</span></span>

<span data-ttu-id="29317-121">Integrationstests bewerten die Komponenten einer App auf breiterer Ebene als [Komponententests](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="29317-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="29317-122">Komponententests werden verwendet, um isolierte Softwarekomponenten wie z. B. einzelne Klassenmethoden zu testen.</span><span class="sxs-lookup"><span data-stu-id="29317-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="29317-123">Integrationstests bestätigen, dass zwei oder mehr App-Komponenten zusammenarbeiten, um ein erwartetes Ergebnis zu erzielen, ggf. auch unter Einbindung aller Komponenten, die für die vollständige Verarbeitung einer Anforderung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="29317-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="29317-124">Diese umfassenderen Tests werden verwendet, um die Infrastruktur und das gesamte Framework der App zu testen, häufig einschließlich der folgenden Komponenten:</span><span class="sxs-lookup"><span data-stu-id="29317-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="29317-125">Datenbank</span><span class="sxs-lookup"><span data-stu-id="29317-125">Database</span></span>
* <span data-ttu-id="29317-126">Dateisystem</span><span class="sxs-lookup"><span data-stu-id="29317-126">File system</span></span>
* <span data-ttu-id="29317-127">Netzwerk-Appliances</span><span class="sxs-lookup"><span data-stu-id="29317-127">Network appliances</span></span>
* <span data-ttu-id="29317-128">Anforderung/Antwort-Pipeline</span><span class="sxs-lookup"><span data-stu-id="29317-128">Request-response pipeline</span></span>

<span data-ttu-id="29317-129">Komponententests verwenden anstelle von Infrastrukturkomponenten künstliche Komponenten, die als *Fakes* oder *Pseudoobjekte* bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="29317-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="29317-130">Im Gegensatz zu Komponententests gilt für Integrationstests:</span><span class="sxs-lookup"><span data-stu-id="29317-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="29317-131">Sie verwenden die tatsächlichen Komponenten, die von der App in der Produktionsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="29317-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="29317-132">Sie erfordern mehr Code und Datenverarbeitung.</span><span class="sxs-lookup"><span data-stu-id="29317-132">Require more code and data processing.</span></span>
* <span data-ttu-id="29317-133">Ihre Ausführung dauert länger.</span><span class="sxs-lookup"><span data-stu-id="29317-133">Take longer to run.</span></span>

<span data-ttu-id="29317-134">Beschränken Sie daher die Verwendung von Integrationstests auf die wichtigsten Infrastrukturszenarios.</span><span class="sxs-lookup"><span data-stu-id="29317-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="29317-135">Wenn ein Verhalten mithilfe eines Komponententests oder eines Integrationstests getestet werden kann, wählen Sie den Komponententest.</span><span class="sxs-lookup"><span data-stu-id="29317-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="29317-136">Schreiben Sie keine Integrationstests für jede denkbare Permutation des Daten- und Dateizugriffs bei Datenbanken und Dateisystemen.</span><span class="sxs-lookup"><span data-stu-id="29317-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="29317-137">Unabhängig davon, wie viele Elemente in einer App mit Datenbanken und Dateisystemen interagieren, ist ein fokussierter Satz von Lese-, Schreib-, Update- und Lösch-Integrationstests üblicherweise in der Lage, die Datenbank- und Dateisystemkomponenten adäquat zu testen.</span><span class="sxs-lookup"><span data-stu-id="29317-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="29317-138">Verwenden Sie Komponententests für Routinetests der Methodenlogik, die mit diesen Komponenten interagieren.</span><span class="sxs-lookup"><span data-stu-id="29317-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="29317-139">Bei Komponententests führt die Verwendung von Infrastruktur-Fakes/-Pseudoobjekten zu einer schnelleren Testausführung.</span><span class="sxs-lookup"><span data-stu-id="29317-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="29317-140">Bei der Besprechung von Integrationstests wird das getestete Projekt häufig als *getestetes System* oder kurz „GS“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="29317-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="29317-141">*In diesem Thema wird „GS“ verwendet, um auf die getestete ASP.NET Core-App zu verweisen.*</span><span class="sxs-lookup"><span data-stu-id="29317-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="29317-142">Integrationstests in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29317-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="29317-143">Integrationstests in ASP.NET Core erfordern Folgendes:</span><span class="sxs-lookup"><span data-stu-id="29317-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="29317-144">Es wird ein Testprojekt verwendet, um die Tests einzugrenzen und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="29317-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="29317-145">Das Testprojekt enthält einen Verweis auf das GS.</span><span class="sxs-lookup"><span data-stu-id="29317-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="29317-146">Das Testprojekt erstellt einen Testwebhost für das GS und verwendet einen Testserverclient, um Anforderungen und Antworten im Zusammenhang mit dem GS zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="29317-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="29317-147">Um die Tests auszuführen und die Testergebnisse zu melden, wird ein Test-Runner verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="29317-148">Integrationstests folgen einer Sequenz von Ereignissen, die die üblichen Testschritte *Arrange*, *Act* und *Assert* umfassen:</span><span class="sxs-lookup"><span data-stu-id="29317-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="29317-149">Der Webhost des GS wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="29317-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="29317-150">Es wird ein Testserverclient erstellt, um Anforderungen an die App zu senden.</span><span class="sxs-lookup"><span data-stu-id="29317-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="29317-151">Der Testschritt *Arrange* wird ausgeführt: Die Test-App bereitet eine Anforderung vor.</span><span class="sxs-lookup"><span data-stu-id="29317-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="29317-152">Der Testschritt *Act* wird ausgeführt: Der Client sendet die Anforderung und empfängt die Antwort.</span><span class="sxs-lookup"><span data-stu-id="29317-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="29317-153">Der Testschritt *Assert* wird ausgeführt: Die *tatsächliche* Antwort wird je nach der *erwarteten* Antwort als *Pass* oder *Fail* bewertet.</span><span class="sxs-lookup"><span data-stu-id="29317-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="29317-154">Der Prozess wird so lange fortgesetzt, bis alle Tests ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="29317-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="29317-155">Die Testergebnisse werden gemeldet.</span><span class="sxs-lookup"><span data-stu-id="29317-155">The test results are reported.</span></span>

<span data-ttu-id="29317-156">Üblicherweise ist der Testwebhost anders konfiguriert als der normale Webhost der App für die Testläufe.</span><span class="sxs-lookup"><span data-stu-id="29317-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="29317-157">Beispielsweise könnten für die Tests eine andere Datenbank oder andere App-Einstellungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="29317-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="29317-158">Infrastrukturkomponenten wie der Testwebhost und der In-Memory-Testserver ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) werden durch das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket bereitgestellt oder verwaltet.</span><span class="sxs-lookup"><span data-stu-id="29317-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="29317-159">Durch die Verwendung dieses Pakets werden die Testerstellung und -ausführung optimiert.</span><span class="sxs-lookup"><span data-stu-id="29317-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="29317-160">Das `Microsoft.AspNetCore.Mvc.Testing`-Paket verarbeitet die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="29317-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="29317-161">Es kopiert die Datei für Abhängigkeiten ( *.deps*) aus dem GS in das *bin-* Verzeichnis des Testprojekts.</span><span class="sxs-lookup"><span data-stu-id="29317-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="29317-162">Es legt das [Inhaltsstammelement](xref:fundamentals/index#content-root) auf das Projektstammelement des GS fest, damit statische Dateien und Seiten/Ansichten bei der Ausführung der Tests gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="29317-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="29317-163">Es stellt die Klasse [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) zur Optimierung des Bootstrappings des GS mit `TestServer` bereit.</span><span class="sxs-lookup"><span data-stu-id="29317-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="29317-164">In der Dokumentation der [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) wird beschrieben, wie Sie ein Testprojekt und einen Test-Runner einrichten. Ferner finden Sie dort ausführliche Anweisungen zum Ausführen von Tests sowie Empfehlungen zum Benennen von Tests und Testklassen.</span><span class="sxs-lookup"><span data-stu-id="29317-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="29317-165">Wenn Sie ein Testprojekt für eine App erstellen, verwenden Sie unterschiedliche Projekte für die Komponententests und die Integrationstests.</span><span class="sxs-lookup"><span data-stu-id="29317-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="29317-166">Dadurch wird sichergestellt, dass Komponenten für Infrastrukturtests nicht versehentlich in die Komponententests eingeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="29317-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="29317-167">Durch die Trennung von Komponenten- und Integrationstests können Sie außerdem steuern, welche Testsätze ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="29317-168">Es gibt praktisch keinen Unterschied zwischen der Konfiguration für Tests von Razor Pages-Apps und MVC-Apps.</span><span class="sxs-lookup"><span data-stu-id="29317-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="29317-169">Der einzige Unterschied besteht darin, wie die Tests benannt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="29317-170">In einer Razor Pages-App werden Tests von Seitenendpunkten normalerweise nach der Seitenmodellklasse benannt (z. B. `IndexPageTests` für das Testen der Komponentenintegration für die Indexseite).</span><span class="sxs-lookup"><span data-stu-id="29317-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="29317-171">In einer MVC-App werden Tests in der Regel nach Controllerklassen organisiert und nach den von ihnen getesteten Controllern benannt (z. B. `HomeControllerTests` für das Testen der Komponentenintegration für den Startseitencontroller).</span><span class="sxs-lookup"><span data-stu-id="29317-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="29317-172">Voraussetzungen für Test-Apps</span><span class="sxs-lookup"><span data-stu-id="29317-172">Test app prerequisites</span></span>

<span data-ttu-id="29317-173">Für das Testprojekt muss Folgendes erfüllt sein:</span><span class="sxs-lookup"><span data-stu-id="29317-173">The test project must:</span></span>

* <span data-ttu-id="29317-174">Verweisen Sie auf das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket.</span><span class="sxs-lookup"><span data-stu-id="29317-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="29317-175">Geben Sie das Web SDK in der Projektdatei an (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span><span class="sxs-lookup"><span data-stu-id="29317-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="29317-176">Diese Voraussetzungen können Sie in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) sehen.</span><span class="sxs-lookup"><span data-stu-id="29317-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="29317-177">Beachten Sie die Datei *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*.</span><span class="sxs-lookup"><span data-stu-id="29317-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="29317-178">Die Beispiel-App verwendet das [xUnit](https://xunit.github.io/)-Testframework und die [AngleSharp](https://anglesharp.github.io/)-Parserbibliothek, sodass die Beispiel-APP auch auf Folgendes verweist:</span><span class="sxs-lookup"><span data-stu-id="29317-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="29317-179">xunit</span><span class="sxs-lookup"><span data-stu-id="29317-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="29317-180">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="29317-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="29317-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="29317-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="29317-182">Entity Framework Core wird ebenfalls in den Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="29317-183">Die App verweist auf:</span><span class="sxs-lookup"><span data-stu-id="29317-183">The app references:</span></span>

* [<span data-ttu-id="29317-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="29317-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="29317-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="29317-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="29317-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="29317-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="29317-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="29317-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="29317-188">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="29317-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="29317-189">GS-Umgebung</span><span class="sxs-lookup"><span data-stu-id="29317-189">SUT environment</span></span>

<span data-ttu-id="29317-190">Wenn die [Umgebung](xref:fundamentals/environments) des GS nicht festgelegt ist, wird standardmäßig die Entwicklungsumgebung verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="29317-191">Grundlegende Tests mit der Standard-WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="29317-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="29317-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) wird verwendet, um einen [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) für die Integrationstests zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="29317-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="29317-193">`TEntryPoint` ist die Einstiegspunktklasse des GS, in der Regel die `Startup`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="29317-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="29317-194">Testklassen implementieren eine *class fixture*-Schnittstelle ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), um anzugeben, dass die Klasse Tests enthält und um in den Tests in der Klasse gemeinsame Objektinstanzen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="29317-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="29317-195">Die folgende Testklasse (`BasicTests`) verwendet die `WebApplicationFactory` für den Bootstrap des GS und um einen [HttpClient](/dotnet/api/system.net.http.httpclient) für eine Testmethode `Get_EndpointsReturnSuccessAndCorrectContentType` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="29317-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="29317-196">Die Methode prüft, ob der Antwortstatuscode erfolgreich ist (Statuscodes im Bereich 200-299) und der `Content-Type`-Header für mehrere App-Seiten `text/html; charset=utf-8` lautet.</span><span class="sxs-lookup"><span data-stu-id="29317-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="29317-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) erstellt eine Instanz von `HttpClient`, die automatisch Umleitungen folgt und Cookies verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="29317-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="29317-198">Standardmäßig werden nicht erforderliche Cookies nicht über Anforderungen hinweg beibehalten, wenn die [DSGVO-Zustimmungsrichtlinie](xref:security/gdpr) aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="29317-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="29317-199">Um nicht erforderliche Cookies beizubehalten, wie z. B. diejenigen, die vom TempData-Anbieter verwendet werden, markieren Sie die Cookies in den Tests als unverzichtbar.</span><span class="sxs-lookup"><span data-stu-id="29317-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="29317-200">Anweisungen zum Markieren eines Cookies als erforderlich finden Sie unter [Erforderliche Cookies](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="29317-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="29317-201">Anpassen von WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="29317-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="29317-202">Die Webhostkonfiguration kann unabhängig von den Testklassen durch Erben von der `WebApplicationFactory` erstellt werden, um eine oder mehrere benutzerdefinierte Factorys zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="29317-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="29317-203">Erben Sie von `WebApplicationFactory`, und überschreiben Sie [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="29317-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="29317-204">Der [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) erlaubt die Konfiguration der Dienstsammlung mit [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="29317-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="29317-205">Das Datenbankseeding in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) wird mithilfe der `InitializeDbForTests`-Methode durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="29317-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="29317-206">Die Methode wird im Abschnitt [Beispiel für Integrationstests: Organisation der Test-App](#test-app-organization) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="29317-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="29317-207">Der Datenbankkontext des GS wird in dessen `Startup.ConfigureServices`-Methode registriert.</span><span class="sxs-lookup"><span data-stu-id="29317-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="29317-208">Der `builder.ConfigureServices`-Rückruf der Test-App wird ausgeführt, *nachdem* der `Startup.ConfigureServices`-Code der App ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="29317-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="29317-209">Die Ausführungsreihenfolge ist im Release von ASP.NET Core 3.0 eine Breaking Change für den [generischen Host](xref:fundamentals/host/generic-host).</span><span class="sxs-lookup"><span data-stu-id="29317-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="29317-210">Um für die Tests eine andere Datenbank als die Datenbank der App zu verwenden, muss der Datenbankkontext der App in `builder.ConfigureServices` ersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="29317-211">Für GS, die weiterhin den [Webhost](xref:fundamentals/host/web-host) verwenden, wird der `builder.ConfigureServices`-Rückruf der Test-App ausgeführt, *bevor* der `Startup.ConfigureServices`-Code des GS ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-211">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="29317-212">Der `builder.ConfigureTestServices`-Rückruf der Test-App wird *danach* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="29317-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="29317-213">Die Beispiel-App findet den Dienstdeskriptor für den Datenbankkontext und verwendet den Deskriptor, um die Dienstregistrierung zu entfernen.</span><span class="sxs-lookup"><span data-stu-id="29317-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="29317-214">Als Nächstes fügt die Factory einen neuen `ApplicationDbContext` hinzu, der eine In-Memory-Datenbank für die Tests verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="29317-215">Um eine Verbindung mit einer anderen Datenbank als der In-Memory-Datenbank herzustellen, ändern Sie den `UseInMemoryDatabase`-Aufruf, um den Kontext mit einer anderen Datenbank zu verbinden.</span><span class="sxs-lookup"><span data-stu-id="29317-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="29317-216">So verwenden Sie eine SQL Server-Testdatenbank:</span><span class="sxs-lookup"><span data-stu-id="29317-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="29317-217">Verweisen Sie auf das [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet-Paket in der Projektdatei.</span><span class="sxs-lookup"><span data-stu-id="29317-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="29317-218">Rufen Sie `UseSqlServer` mit einer Verbindungszeichenfolge zu der Datenbank auf.</span><span class="sxs-lookup"><span data-stu-id="29317-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="29317-219">Verwenden Sie die benutzerdefinierte `CustomWebApplicationFactory` in Testklassen.</span><span class="sxs-lookup"><span data-stu-id="29317-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="29317-220">Das folgende Beispiel verwendet die Factory in der `IndexPageTests`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="29317-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="29317-221">Der Client der Beispiel-App wird so konfiguriert, dass der `HttpClient` keinen Umleitungen folgt.</span><span class="sxs-lookup"><span data-stu-id="29317-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="29317-222">Wie weiter unten im Abschnitt [Pseudoauthentifizierung](#mock-authentication) erläutert wird, können Tests so das Ergebnis der ersten Reaktion der App überprüfen.</span><span class="sxs-lookup"><span data-stu-id="29317-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="29317-223">In vielen dieser Tests mit einem `Location`-Header ist die erste Antwort eine Umleitung.</span><span class="sxs-lookup"><span data-stu-id="29317-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="29317-224">Ein typischer Test verwendet die `HttpClient`- und die Hilfsprogrammmethoden, um die Anforderung und die Antwort zu verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="29317-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="29317-225">Jede POST-Anforderung an das GS muss die Fälschungsschutzprüfung bestehen, die automatisch vom [Daten- und Fälschungsschutzsystem](xref:security/data-protection/introduction) der App durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="29317-226">Als Vorbereitung auf die POST-Anforderung eines Tests muss die Test-App folgende Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="29317-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="29317-227">Senden einer Anforderung für die Seite.</span><span class="sxs-lookup"><span data-stu-id="29317-227">Make a request for the page.</span></span>
1. <span data-ttu-id="29317-228">Analysieren des Fälschungsschutzcookies und des Anforderungsvalidierungstokens von der Antwort.</span><span class="sxs-lookup"><span data-stu-id="29317-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="29317-229">Senden der POST-Anforderung mit vorhandenem Fälschungsschutzcookie und Anforderungsvalidierungstoken.</span><span class="sxs-lookup"><span data-stu-id="29317-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="29317-230">Die `SendAsync`-Hilfsprogrammerweiterungsmethoden (*Helpers/HttpClientExtensions.cs*) und die `GetDocumentAsync`-Hilfsprogrammmethode (*Helpers/HtmlHelpers.cs*) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) verwenden den [AngleSharp](https://anglesharp.github.io/)-Parser, um die Fälschungsschutzprüfungen mit den folgenden Methoden durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="29317-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="29317-231">`GetDocumentAsync`: Empfängt die [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) und gibt ein `IHtmlDocument` zurück.</span><span class="sxs-lookup"><span data-stu-id="29317-231">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="29317-232">`GetDocumentAsync` verwendet eine Factory, die eine *virtuelle Antwort* basierend auf der ursprünglichen `HttpResponseMessage` vorbereitet.</span><span class="sxs-lookup"><span data-stu-id="29317-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="29317-233">Weitere Informationen finden Sie in der [AngleSharp-Dokumentation](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="29317-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="29317-234">`SendAsync`-Erweiterungsmethoden für den `HttpClient` verfassen eine [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) und rufen [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) auf, um Anforderungen an das GS zu senden.</span><span class="sxs-lookup"><span data-stu-id="29317-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="29317-235">Überladungen für `SendAsync` akzeptieren das HTML-Formular (`IHtmlFormElement`) und Folgendes:</span><span class="sxs-lookup"><span data-stu-id="29317-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="29317-236">Schaltfläche „Senden“ des Formulars (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="29317-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="29317-237">Formularwerteauflistung (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="29317-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="29317-238">Schaltfläche „Senden“ (`IHtmlElement`) und Formularwerte (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="29317-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="29317-239">[AngleSharp](https://anglesharp.github.io/) ist eine Drittanbieter-Analysebibliothek, die in diesem Thema und in der Beispiel-App zu Demonstrationszwecken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="29317-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="29317-240">AngleSharp wird für Integrationstests von ASP.NET Core-Apps weder unterstützt noch benötigt.</span><span class="sxs-lookup"><span data-stu-id="29317-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="29317-241">Andere Parser können verwendet werden, beispielsweise [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="29317-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="29317-242">Ein anderer Ansatz besteht darin, Code zu schreiben, der das Anforderungsüberprüfungstoken und das Fälschungsschutzcookie des Fälschungsschutzsystems direkt verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="29317-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="29317-243">Anpassen des Clients mit WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="29317-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="29317-244">Wenn eine zusätzliche Konfiguration innerhalb einer Testmethode erforderlich ist, erstellt [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) eine neue `WebApplicationFactory` mit einem [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), der mittels Konfiguration weiter angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="29317-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="29317-245">Die `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Testmethode der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) zeigt die Verwendung von `WithWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="29317-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="29317-246">Bei diesem Test wird eine Datensatzlöschung in der Datenbank durch Auslösen einer Formularübermittlung im GS durchführt.</span><span class="sxs-lookup"><span data-stu-id="29317-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="29317-247">Da ein anderer Test in der `IndexPageTests`-Klasse einen Vorgang durchführt, der alle Datensätze in der Datenbank löscht und der möglicherweise vor der `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Methode ausgeführt wird, wird in dieser Testmethode ein erneutes Seeding der Datenbank durchgeführt, um sicherzustellen, dass ein Datensatz vorhanden ist, den das GS löschen kann.</span><span class="sxs-lookup"><span data-stu-id="29317-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="29317-248">Die Auswahl der ersten Löschschaltfläche des `messages`-Formulars im GS wird in der Anforderung an das GS simuliert:</span><span class="sxs-lookup"><span data-stu-id="29317-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="29317-249">Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="29317-249">Client options</span></span>

<span data-ttu-id="29317-250">In der folgenden Tabelle werden die Standard-[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gezeigt, die bei der Erstellung von `HttpClient`-Instanzen verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="29317-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="29317-251">Option</span><span class="sxs-lookup"><span data-stu-id="29317-251">Option</span></span> | <span data-ttu-id="29317-252">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="29317-252">Description</span></span> | <span data-ttu-id="29317-253">Standard</span><span class="sxs-lookup"><span data-stu-id="29317-253">Default</span></span> |
| ---
<span data-ttu-id="29317-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-255">'Blazor'</span></span>
- <span data-ttu-id="29317-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-256">'Identity'</span></span>
- <span data-ttu-id="29317-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-258">'Razor'</span></span>
- <span data-ttu-id="29317-259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-259">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-261">'Blazor'</span></span>
- <span data-ttu-id="29317-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-262">'Identity'</span></span>
- <span data-ttu-id="29317-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-264">'Razor'</span></span>
- <span data-ttu-id="29317-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-267">'Blazor'</span></span>
- <span data-ttu-id="29317-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-268">'Identity'</span></span>
- <span data-ttu-id="29317-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-270">'Razor'</span></span>
- <span data-ttu-id="29317-271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-273">'Blazor'</span></span>
- <span data-ttu-id="29317-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-274">'Identity'</span></span>
- <span data-ttu-id="29317-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-276">'Razor'</span></span>
- <span data-ttu-id="29317-277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-277">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-279">'Blazor'</span></span>
- <span data-ttu-id="29317-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-280">'Identity'</span></span>
- <span data-ttu-id="29317-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-282">'Razor'</span></span>
- <span data-ttu-id="29317-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-283">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen automatisch Umleitungsantworten befolgen sollen.</span><span class="sxs-lookup"><span data-stu-id="29317-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="29317-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ruft die Basisadresse der `HttpClient`-Instanzen ab oder legt sie fest.</span><span class="sxs-lookup"><span data-stu-id="29317-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="29317-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen Cookies verarbeiten sollen.</span><span class="sxs-lookup"><span data-stu-id="29317-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="29317-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ruft die maximale Anzahl von Umleitungsantworten ab, die von `HttpClient`-Instanzen befolgt werden sollen, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="29317-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="29317-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="29317-288">| 7 |</span></span>

<span data-ttu-id="29317-289">Erstellen Sie die `WebApplicationFactoryClientOptions`-Klasse, und übergeben Sie sie an die [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)-Methode (Standardwerte werden im Codebeispiel gezeigt):</span><span class="sxs-lookup"><span data-stu-id="29317-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="29317-290">Fügen Sie Pseudodienste ein</span><span class="sxs-lookup"><span data-stu-id="29317-290">Inject mock services</span></span>

<span data-ttu-id="29317-291">Dienste können in einem Test mit dem Aufruf [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) auf dem Host-Builder überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="29317-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="29317-292">**Um Pseudodienste einzufügen, muss das GS über eine `Startup`-Klasse mit einer `Startup.ConfigureServices`-Methode verfügen.**</span><span class="sxs-lookup"><span data-stu-id="29317-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="29317-293">Das Beispiel-GS enthält einen bereichsbezogenen Dienst, der ein Zitat zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="29317-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="29317-294">Wenn die Indexseite angefordert wird, wird das Zitat in ein ausgeblendetes Feld auf der Indexseite eingebettet.</span><span class="sxs-lookup"><span data-stu-id="29317-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="29317-295">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="29317-296">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="29317-297">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="29317-298">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="29317-299">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="29317-300">Das folgende Markup wird generiert, wenn die GS-App ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="29317-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="29317-301">Um den Dienst und die Zitateinfügung in einem Integrationstest zu testen, wird vom Test ein Pseudodienst in das GS eingefügt.</span><span class="sxs-lookup"><span data-stu-id="29317-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="29317-302">Der Pseudodienst ersetzt `QuoteService` der App durch einen Dienst namens `TestQuoteService`, der von der Test-App bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="29317-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="29317-303">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="29317-304">`ConfigureTestServices` wird aufgerufen, und der bereichsbezogene Dienst wird registriert:</span><span class="sxs-lookup"><span data-stu-id="29317-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="29317-305">Das während der Ausführung des Tests erstellte Markup gibt das von `TestQuoteService` bereitgestellte Zitat wieder, somit ist die Assertion erfolgreich:</span><span class="sxs-lookup"><span data-stu-id="29317-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="29317-306">Pseudo-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="29317-306">Mock authentication</span></span>

<span data-ttu-id="29317-307">Tests in der `AuthTests`-Klasse prüfen, ob ein sicherer Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="29317-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="29317-308">Einen nicht authentifizierten Benutzer an die Anmeldeseite der App zurückleitet.</span><span class="sxs-lookup"><span data-stu-id="29317-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="29317-309">Den Inhalt für einen authentifizierten Benutzer zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="29317-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="29317-310">Im GS verwendet die `/SecurePage`-Seite eine [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)-Konvention, um einen [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf die Seite anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="29317-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="29317-311">Weitere Informationen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="29317-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="29317-312">Im `Get_SecurePageRedirectsAnUnauthenticatedUser`-Test werden [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) so festgelegt, dass Umleitungen unzulässig sind, indem [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) auf `false` festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="29317-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="29317-313">Indem dem Client untersagt wird, die Umleitung zu befolgen, können folgende Prüfungen durchgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="29317-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="29317-314">Der vom GS zurückgegebene Statuscode kann mit dem erwarteten [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)-Ergebnis verglichen werden – anstatt mit dem endgültigen Statuscode nach der Umleitung zur Anmeldeseite ([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)).</span><span class="sxs-lookup"><span data-stu-id="29317-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="29317-315">Der Wert für den `Location`-Header in den Antwortheadern wird geprüft, um zu bestätigen, dass er mit `http://localhost/Identity/Account/Login` beginnt. (Es wird nicht die abschließende Antwort der Anmeldeseite verwendet, bei der der `Location`-Header nicht vorhanden wäre.)</span><span class="sxs-lookup"><span data-stu-id="29317-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="29317-316">Die Test-App kann einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) simulieren, um Aspekte der Authentifizierung und Autorisierung zu testen.</span><span class="sxs-lookup"><span data-stu-id="29317-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="29317-317">Ein minimales Szenario gibt [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) zurück:</span><span class="sxs-lookup"><span data-stu-id="29317-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="29317-318">Der `TestAuthHandler` wird aufgerufen, um einen Benutzer zu authentifizieren, wenn das Authentifizierungsschema auf `Test` festgelegt ist, in dem `AddAuthentication` für `ConfigureTestServices` registriert ist:</span><span class="sxs-lookup"><span data-stu-id="29317-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="29317-319">Weitere Informationen zu `WebApplicationFactoryClientOptions` finden Sie im Abschnitt [Clientoptionen](#client-options).</span><span class="sxs-lookup"><span data-stu-id="29317-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="29317-320">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="29317-320">Set the environment</span></span>

<span data-ttu-id="29317-321">Standardmäßig ist die Host- und App-Umgebung des GS für die Verwendung der Entwicklungsumgebung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="29317-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="29317-322">So überschreiben Sie die Umgebung des GS bei Verwendung von `IHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="29317-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="29317-323">Legen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable fest (z. B. `Staging`, `Production` oder ein anderer benutzerdefinierter Wert wie `Testing`).</span><span class="sxs-lookup"><span data-stu-id="29317-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="29317-324">Überschreiben Sie `CreateHostBuilder` in der Test-App, um Umgebungsvariablen mit dem Präfix `ASPNETCORE` zu lesen.</span><span class="sxs-lookup"><span data-stu-id="29317-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="29317-325">Wenn das GS den Webhost (`IWebHostBuilder`) verwendet, überschreiben Sie `CreateWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="29317-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="29317-326">Ableitung des Inhaltsstammpfads der App durch die Testinfrastruktur</span><span class="sxs-lookup"><span data-stu-id="29317-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="29317-327">Der `WebApplicationFactory`-Konstruktor leitet den [Inhaltsstammpfad](xref:fundamentals/index#content-root) der App ab, indem er in der Assembly, die die Integrationstests enthält, nach einem [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) mit einem Schlüssel sucht, der `TEntryPoint` assembly `System.Reflection.Assembly.FullName` entspricht.</span><span class="sxs-lookup"><span data-stu-id="29317-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="29317-328">Wenn kein Attribut mit dem richtigen Schlüssel gefunden wird, greift `WebApplicationFactory` auf die Suche nach einer Projektmappendatei ( *.sln*) zurück und fügt den `TEntryPoint`-Assemblynamen an das Projektmappenverzeichnis an.</span><span class="sxs-lookup"><span data-stu-id="29317-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="29317-329">Das Stammverzeichnis der App (der Inhaltsstammpfad) wird verwendet, um Sichten und Inhaltsdateien zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="29317-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="29317-330">Deaktivieren der Erstellung von Schattenkopien</span><span class="sxs-lookup"><span data-stu-id="29317-330">Disable shadow copying</span></span>

<span data-ttu-id="29317-331">Das Erstellen von Schattenkopien bewirkt, dass die Tests in einem anderen Verzeichnis als dem Ausgabeverzeichnis ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="29317-332">Damit Tests ordnungsgemäß funktionieren, muss die Erstellung von Schattenkopien deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="29317-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="29317-333">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) verwendet xUnit und deaktiviert die Erstellung von Schattenkopien für xUnit durch Einschließen einer *xunit.runner.json*-Datei mit der korrekten Konfigurationseinstellung.</span><span class="sxs-lookup"><span data-stu-id="29317-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="29317-334">Weitere Informationen finden Sie unter [Konfigurieren von xUnit mit JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="29317-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="29317-335">Fügen Sie die Datei *xunit.runner.json* mit folgendem Inhalt zum Stamm des Testprojekts hinzu:</span><span class="sxs-lookup"><span data-stu-id="29317-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="29317-336">Verwerfen von Objekten</span><span class="sxs-lookup"><span data-stu-id="29317-336">Disposal of objects</span></span>

<span data-ttu-id="29317-337">Nachdem die Tests der `IClassFixture`-Implementierung ausgeführt wurden, werden [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) und [HttpClient](/dotnet/api/system.net.http.httpclient) verworfen, wenn xUnit die [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) verwirft.</span><span class="sxs-lookup"><span data-stu-id="29317-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="29317-338">Wenn vom Entwickler instanziierte Objekte verworfen werden müssen, müssen Sie dies in der `IClassFixture`-Implementierung tun.</span><span class="sxs-lookup"><span data-stu-id="29317-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="29317-339">Weitere Informationen finden Sie unter [Implementieren einer Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="29317-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="29317-340">Beispiel für Integrationstests</span><span class="sxs-lookup"><span data-stu-id="29317-340">Integration tests sample</span></span>

<span data-ttu-id="29317-341">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) besteht aus zwei Apps:</span><span class="sxs-lookup"><span data-stu-id="29317-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="29317-342">App</span><span class="sxs-lookup"><span data-stu-id="29317-342">App</span></span> | <span data-ttu-id="29317-343">Projektverzeichnis</span><span class="sxs-lookup"><span data-stu-id="29317-343">Project directory</span></span> | <span data-ttu-id="29317-344">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="29317-344">Description</span></span> |
| --- | ---
<span data-ttu-id="29317-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-346">'Blazor'</span></span>
- <span data-ttu-id="29317-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-347">'Identity'</span></span>
- <span data-ttu-id="29317-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-349">'Razor'</span></span>
- <span data-ttu-id="29317-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-352">'Blazor'</span></span>
- <span data-ttu-id="29317-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-353">'Identity'</span></span>
- <span data-ttu-id="29317-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-355">'Razor'</span></span>
- <span data-ttu-id="29317-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-358">'Blazor'</span></span>
- <span data-ttu-id="29317-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-359">'Identity'</span></span>
- <span data-ttu-id="29317-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-361">'Razor'</span></span>
- <span data-ttu-id="29317-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-364">'Blazor'</span></span>
- <span data-ttu-id="29317-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-365">'Identity'</span></span>
- <span data-ttu-id="29317-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-367">'Razor'</span></span>
- <span data-ttu-id="29317-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-370">'Blazor'</span></span>
- <span data-ttu-id="29317-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-371">'Identity'</span></span>
- <span data-ttu-id="29317-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-373">'Razor'</span></span>
- <span data-ttu-id="29317-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-376">'Blazor'</span></span>
- <span data-ttu-id="29317-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-377">'Identity'</span></span>
- <span data-ttu-id="29317-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-379">'Razor'</span></span>
- <span data-ttu-id="29317-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-380">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-382">'Blazor'</span></span>
- <span data-ttu-id="29317-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-383">'Identity'</span></span>
- <span data-ttu-id="29317-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-385">'Razor'</span></span>
- <span data-ttu-id="29317-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-388">'Blazor'</span></span>
- <span data-ttu-id="29317-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-389">'Identity'</span></span>
- <span data-ttu-id="29317-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-391">'Razor'</span></span>
- <span data-ttu-id="29317-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-394">'Blazor'</span></span>
- <span data-ttu-id="29317-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-395">'Identity'</span></span>
- <span data-ttu-id="29317-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-397">'Razor'</span></span>
- <span data-ttu-id="29317-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-398">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-399">------ | | Nachrichten-App (das GS) | *src/RazorPagesProject* | Ermöglicht einem Benutzer, Nachrichten hinzuzufügen, eine oder alle Nachrichten zu löschen und Nachrichten zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="29317-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="29317-400">| | Test-App | *tests/RazorPagesProject.Tests* | Wird verwendet, um das GS zu integrieren.</span><span class="sxs-lookup"><span data-stu-id="29317-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="29317-401">Die Tests können mit den integrierten Testfunktionen einer IDE, wie z. B. [Visual Studio](https://visualstudio.microsoft.com) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="29317-402">Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl an einer Eingabeaufforderung im Verzeichnis *tests/RazorPagesProject.Tests* aus:</span><span class="sxs-lookup"><span data-stu-id="29317-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="29317-403">Organisation der Nachrichten-App (GS)</span><span class="sxs-lookup"><span data-stu-id="29317-403">Message app (SUT) organization</span></span>

<span data-ttu-id="29317-404">Beim GS handelt es sich um ein Razor Pages-Nachrichtensystem mit folgenden Merkmalen:</span><span class="sxs-lookup"><span data-stu-id="29317-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="29317-405">Die Indexseite der App (*Pages/Index.cshtml* und *Pages/Index.cshtml.cs*) stellt eine Benutzeroberfläche und Seitenmodellmethoden bereit, mit denen Sie das Hinzufügen, Löschen und Analysieren von Nachrichten (durchschnittliche Anzahl von Wörtern pro Nachricht) steuern können.</span><span class="sxs-lookup"><span data-stu-id="29317-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="29317-406">Eine Nachricht wird von der `Message`-Klasse (*Data/Message.cs*) mit zwei Eigenschaften beschrieben: `Id` (Schlüssel) und `Text` (Nachricht).</span><span class="sxs-lookup"><span data-stu-id="29317-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="29317-407">Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="29317-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="29317-408">Nachrichten werden mithilfe der [In-Memory-Datenbank von Entity Framework](/ef/core/providers/in-memory/)&#8224; gespeichert.</span><span class="sxs-lookup"><span data-stu-id="29317-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="29317-409">Die App enthält eine Datenzugriffsebene (DAL) in ihrer Datenbankkontextklasse `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="29317-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="29317-410">Wenn die Datenbank beim Starten der App leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="29317-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="29317-411">Die App enthält eine `/SecurePage`, auf die nur ein authentifizierter Benutzer zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="29317-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="29317-412">&#8224;Im Entity Framework-Thema [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory) wird die Verwendung einer In-Memory-Datenbank für Tests mit MSTest erläutert.</span><span class="sxs-lookup"><span data-stu-id="29317-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="29317-413">In diesem Thema wird das Testframework [xUnit](https://xunit.github.io/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="29317-414">Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch.</span><span class="sxs-lookup"><span data-stu-id="29317-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="29317-415">Obwohl die App nicht das Repositorymuster verwendet und kein effektives Beispiel für das [Arbeitseinheitsmuster](https://martinfowler.com/eaaCatalog/unitOfWork.html) ist, unterstützt Razor Pages diese Entwicklungsmuster.</span><span class="sxs-lookup"><span data-stu-id="29317-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="29317-416">Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und [Testcontrollerlogik](/aspnet/core/mvc/controllers/testing) (im Beispiel wird das Repositorymuster implementiert).</span><span class="sxs-lookup"><span data-stu-id="29317-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="29317-417">Organisation der Test-App</span><span class="sxs-lookup"><span data-stu-id="29317-417">Test app organization</span></span>

<span data-ttu-id="29317-418">Bei der Test-App handelt es sich um eine Konsolen-App im Verzeichnis *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="29317-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="29317-419">Test-App-Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="29317-419">Test app directory</span></span> | <span data-ttu-id="29317-420">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="29317-420">Description</span></span> |
| ---
<span data-ttu-id="29317-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-422">'Blazor'</span></span>
- <span data-ttu-id="29317-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-423">'Identity'</span></span>
- <span data-ttu-id="29317-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-425">'Razor'</span></span>
- <span data-ttu-id="29317-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-428">'Blazor'</span></span>
- <span data-ttu-id="29317-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-429">'Identity'</span></span>
- <span data-ttu-id="29317-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-431">'Razor'</span></span>
- <span data-ttu-id="29317-432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-434">'Blazor'</span></span>
- <span data-ttu-id="29317-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-435">'Identity'</span></span>
- <span data-ttu-id="29317-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-437">'Razor'</span></span>
- <span data-ttu-id="29317-438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-440">'Blazor'</span></span>
- <span data-ttu-id="29317-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-441">'Identity'</span></span>
- <span data-ttu-id="29317-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-443">'Razor'</span></span>
- <span data-ttu-id="29317-444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-446">'Blazor'</span></span>
- <span data-ttu-id="29317-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-447">'Identity'</span></span>
- <span data-ttu-id="29317-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-449">'Razor'</span></span>
- <span data-ttu-id="29317-450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-452">'Blazor'</span></span>
- <span data-ttu-id="29317-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-453">'Identity'</span></span>
- <span data-ttu-id="29317-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-455">'Razor'</span></span>
- <span data-ttu-id="29317-456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-458">'Blazor'</span></span>
- <span data-ttu-id="29317-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-459">'Identity'</span></span>
- <span data-ttu-id="29317-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-461">'Razor'</span></span>
- <span data-ttu-id="29317-462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-462">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-464">'Blazor'</span></span>
- <span data-ttu-id="29317-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-465">'Identity'</span></span>
- <span data-ttu-id="29317-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-467">'Razor'</span></span>
- <span data-ttu-id="29317-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-470">'Blazor'</span></span>
- <span data-ttu-id="29317-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-471">'Identity'</span></span>
- <span data-ttu-id="29317-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-473">'Razor'</span></span>
- <span data-ttu-id="29317-474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-476">'Blazor'</span></span>
- <span data-ttu-id="29317-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-477">'Identity'</span></span>
- <span data-ttu-id="29317-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-479">'Razor'</span></span>
- <span data-ttu-id="29317-480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-480">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-481">------ | | *AuthTests* | Enthält Testmethoden für:</span><span class="sxs-lookup"><span data-stu-id="29317-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="29317-482">Zugreifen auf eine sichere Seite durch einen nicht authentifizierten Benutzer.</span><span class="sxs-lookup"><span data-stu-id="29317-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="29317-483">Zugreifen auf eine sichere Seite durch einen authentifizierten Benutzer mit einem Pseudo-<xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span><span class="sxs-lookup"><span data-stu-id="29317-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="29317-484">Abrufen eines GitHub-Benutzerprofils und Überprüfen der Benutzeranmeldung des Profils.</span><span class="sxs-lookup"><span data-stu-id="29317-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="29317-485">| | *BasicTests* | Enthält eine Testmethode für Routing und Inhaltstyp.</span><span class="sxs-lookup"><span data-stu-id="29317-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="29317-486">| | *IntegrationTests* | Enthält die Integrationstests für die Indexseite unter Verwendung der benutzerdefinierten `WebApplicationFactory`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="29317-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="29317-487">| | *Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="29317-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="29317-488">*Utilities.cs* enthält die `InitializeDbForTests`-Methode, mit der ein Seeding der Datenbank mit Testdaten durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="29317-489">*HtmlHelpers.cs* stellt eine Methode bereit, mit der ein AngleSharp-`IHtmlDocument` zur Verwendung durch die Testmethoden zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="29317-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="29317-490">*HttpClientExtensions.cs* stellt Überladungen für `SendAsync` bereit, um Anforderungen an das GS zu senden.</span><span class="sxs-lookup"><span data-stu-id="29317-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="29317-491">Das Testframework ist [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="29317-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="29317-492">Integrationstests werden mit dem [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) durchgeführt, der den [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) umfasst.</span><span class="sxs-lookup"><span data-stu-id="29317-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="29317-493">Da das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket zum Konfigurieren des Testhosts und des Testservers verwendet wird, benötigen die `TestHost`- und `TestServer`-Pakete keine direkten Paketverweise in der Projektdatei der Test-App bzw. keine Entwicklerkonfiguration in der Test-App.</span><span class="sxs-lookup"><span data-stu-id="29317-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="29317-494">**Seeding der Datenbank für Testzwecke**</span><span class="sxs-lookup"><span data-stu-id="29317-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="29317-495">Für Integrationstests muss die Datenbank in der Regel vor der Testausführung ein kleines Dataset enthalten.</span><span class="sxs-lookup"><span data-stu-id="29317-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="29317-496">Beispielsweise wird bei einem Löschtest ein Löschvorgang eines Datensatzes der Datenbank abgerufen, weshalb die Datenbank mindestens einen Datensatz aufweisen muss, damit die Löschanforderung erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="29317-497">Die Beispiel-App führt ein Seeding der Datenbank mit drei Nachrichten in *Utilities.cs* durch, die von Tests bei der Ausführung verwendet werden können:</span><span class="sxs-lookup"><span data-stu-id="29317-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="29317-498">Der Datenbankkontext des GS wird in dessen `Startup.ConfigureServices`-Methode registriert.</span><span class="sxs-lookup"><span data-stu-id="29317-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="29317-499">Der `builder.ConfigureServices`-Rückruf der Test-App wird ausgeführt, *nachdem* der `Startup.ConfigureServices`-Code der App ausgeführt wurde.</span><span class="sxs-lookup"><span data-stu-id="29317-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="29317-500">Um eine andere Datenbank für die Tests zu verwenden, muss der Datenbankkontext der App in `builder.ConfigureServices` ersetzt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="29317-501">Weitere Informationen finden Sie im Abschnitt [Anpassen von WebApplicationFactory](#customize-webapplicationfactory).</span><span class="sxs-lookup"><span data-stu-id="29317-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="29317-502">Für GS, die weiterhin den [Webhost](xref:fundamentals/host/web-host) verwenden, wird der `builder.ConfigureServices`-Rückruf der Test-App ausgeführt, *bevor* der `Startup.ConfigureServices`-Code des GS ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-502">For SUTs that still use the [Web Host](xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="29317-503">Der `builder.ConfigureTestServices`-Rückruf der Test-App wird *danach* ausgeführt.</span><span class="sxs-lookup"><span data-stu-id="29317-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="29317-504">Integrationstests stellen sicher, dass die Komponenten einer App auf einer Ebene, die die unterstützende Infrastruktur der App (wie die Datenbank, das Dateisystem und das Netzwerk) umfasst, ordnungsgemäß funktionieren.</span><span class="sxs-lookup"><span data-stu-id="29317-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="29317-505">ASP.NET Core unterstützt Integrationstests mithilfe eines Komponententest-Frameworks mit einem Testwebhost und einem In-Memory-Testserver.</span><span class="sxs-lookup"><span data-stu-id="29317-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="29317-506">In diesem Thema werden Grundkenntnisse über Komponententests vorausgesetzt.</span><span class="sxs-lookup"><span data-stu-id="29317-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="29317-507">Wenn Sie nicht mit Testkonzepten vertraut sind, lesen Sie das Thema [Komponententests in .NET Core und .NET Standard](/dotnet/core/testing/) und zugehörige Inhalte.</span><span class="sxs-lookup"><span data-stu-id="29317-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="29317-508">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="29317-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="29317-509">Bei der Beispiel-App handelt es sich um eine Razor Pages-App, hierfür werden grundlegende Kenntnisse über Razor Pages vorausgesetzt.</span><span class="sxs-lookup"><span data-stu-id="29317-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="29317-510">Wenn Sie nicht mit Razor Pages vertraut sind, lesen Sie die folgenden Themen:</span><span class="sxs-lookup"><span data-stu-id="29317-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="29317-511">[Einführung in Razor Pages](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="29317-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="29317-512">[Erste Schritte mit Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="29317-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="29317-513">[Komponententests für Razor-Seiten](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="29317-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="29317-514">Zum Testen von Single-Page-Webanwendungen empfiehlt es sich, ein Tool wie [Selenium](https://www.seleniumhq.org/) zu verwenden, mit dem ein Browser automatisiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="29317-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="29317-515">Einführung in Integrationstests</span><span class="sxs-lookup"><span data-stu-id="29317-515">Introduction to integration tests</span></span>

<span data-ttu-id="29317-516">Integrationstests bewerten die Komponenten einer App auf breiterer Ebene als [Komponententests](/dotnet/core/testing/).</span><span class="sxs-lookup"><span data-stu-id="29317-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="29317-517">Komponententests werden verwendet, um isolierte Softwarekomponenten wie z. B. einzelne Klassenmethoden zu testen.</span><span class="sxs-lookup"><span data-stu-id="29317-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="29317-518">Integrationstests bestätigen, dass zwei oder mehr App-Komponenten zusammenarbeiten, um ein erwartetes Ergebnis zu erzielen, ggf. auch unter Einbindung aller Komponenten, die für die vollständige Verarbeitung einer Anforderung erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="29317-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="29317-519">Diese umfassenderen Tests werden verwendet, um die Infrastruktur und das gesamte Framework der App zu testen, häufig einschließlich der folgenden Komponenten:</span><span class="sxs-lookup"><span data-stu-id="29317-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="29317-520">Datenbank</span><span class="sxs-lookup"><span data-stu-id="29317-520">Database</span></span>
* <span data-ttu-id="29317-521">Dateisystem</span><span class="sxs-lookup"><span data-stu-id="29317-521">File system</span></span>
* <span data-ttu-id="29317-522">Netzwerk-Appliances</span><span class="sxs-lookup"><span data-stu-id="29317-522">Network appliances</span></span>
* <span data-ttu-id="29317-523">Anforderung/Antwort-Pipeline</span><span class="sxs-lookup"><span data-stu-id="29317-523">Request-response pipeline</span></span>

<span data-ttu-id="29317-524">Komponententests verwenden anstelle von Infrastrukturkomponenten künstliche Komponenten, die als *Fakes* oder *Pseudoobjekte* bezeichnet werden.</span><span class="sxs-lookup"><span data-stu-id="29317-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="29317-525">Im Gegensatz zu Komponententests gilt für Integrationstests:</span><span class="sxs-lookup"><span data-stu-id="29317-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="29317-526">Sie verwenden die tatsächlichen Komponenten, die von der App in der Produktionsumgebung verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="29317-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="29317-527">Sie erfordern mehr Code und Datenverarbeitung.</span><span class="sxs-lookup"><span data-stu-id="29317-527">Require more code and data processing.</span></span>
* <span data-ttu-id="29317-528">Ihre Ausführung dauert länger.</span><span class="sxs-lookup"><span data-stu-id="29317-528">Take longer to run.</span></span>

<span data-ttu-id="29317-529">Beschränken Sie daher die Verwendung von Integrationstests auf die wichtigsten Infrastrukturszenarios.</span><span class="sxs-lookup"><span data-stu-id="29317-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="29317-530">Wenn ein Verhalten mithilfe eines Komponententests oder eines Integrationstests getestet werden kann, wählen Sie den Komponententest.</span><span class="sxs-lookup"><span data-stu-id="29317-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="29317-531">Schreiben Sie keine Integrationstests für jede denkbare Permutation des Daten- und Dateizugriffs bei Datenbanken und Dateisystemen.</span><span class="sxs-lookup"><span data-stu-id="29317-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="29317-532">Unabhängig davon, wie viele Elemente in einer App mit Datenbanken und Dateisystemen interagieren, ist ein fokussierter Satz von Lese-, Schreib-, Update- und Lösch-Integrationstests üblicherweise in der Lage, die Datenbank- und Dateisystemkomponenten adäquat zu testen.</span><span class="sxs-lookup"><span data-stu-id="29317-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="29317-533">Verwenden Sie Komponententests für Routinetests der Methodenlogik, die mit diesen Komponenten interagieren.</span><span class="sxs-lookup"><span data-stu-id="29317-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="29317-534">Bei Komponententests führt die Verwendung von Infrastruktur-Fakes/-Pseudoobjekten zu einer schnelleren Testausführung.</span><span class="sxs-lookup"><span data-stu-id="29317-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="29317-535">Bei der Besprechung von Integrationstests wird das getestete Projekt häufig als *getestetes System* oder kurz „GS“ bezeichnet.</span><span class="sxs-lookup"><span data-stu-id="29317-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="29317-536">*In diesem Thema wird „GS“ verwendet, um auf die getestete ASP.NET Core-App zu verweisen.*</span><span class="sxs-lookup"><span data-stu-id="29317-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="29317-537">Integrationstests in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="29317-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="29317-538">Integrationstests in ASP.NET Core erfordern Folgendes:</span><span class="sxs-lookup"><span data-stu-id="29317-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="29317-539">Es wird ein Testprojekt verwendet, um die Tests einzugrenzen und auszuführen.</span><span class="sxs-lookup"><span data-stu-id="29317-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="29317-540">Das Testprojekt enthält einen Verweis auf das GS.</span><span class="sxs-lookup"><span data-stu-id="29317-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="29317-541">Das Testprojekt erstellt einen Testwebhost für das GS und verwendet einen Testserverclient, um Anforderungen und Antworten im Zusammenhang mit dem GS zu verarbeiten.</span><span class="sxs-lookup"><span data-stu-id="29317-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="29317-542">Um die Tests auszuführen und die Testergebnisse zu melden, wird ein Test-Runner verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="29317-543">Integrationstests folgen einer Sequenz von Ereignissen, die die üblichen Testschritte *Arrange*, *Act* und *Assert* umfassen:</span><span class="sxs-lookup"><span data-stu-id="29317-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="29317-544">Der Webhost des GS wird konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="29317-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="29317-545">Es wird ein Testserverclient erstellt, um Anforderungen an die App zu senden.</span><span class="sxs-lookup"><span data-stu-id="29317-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="29317-546">Der Testschritt *Arrange* wird ausgeführt: Die Test-App bereitet eine Anforderung vor.</span><span class="sxs-lookup"><span data-stu-id="29317-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="29317-547">Der Testschritt *Act* wird ausgeführt: Der Client sendet die Anforderung und empfängt die Antwort.</span><span class="sxs-lookup"><span data-stu-id="29317-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="29317-548">Der Testschritt *Assert* wird ausgeführt: Die *tatsächliche* Antwort wird je nach der *erwarteten* Antwort als *Pass* oder *Fail* bewertet.</span><span class="sxs-lookup"><span data-stu-id="29317-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="29317-549">Der Prozess wird so lange fortgesetzt, bis alle Tests ausgeführt wurden.</span><span class="sxs-lookup"><span data-stu-id="29317-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="29317-550">Die Testergebnisse werden gemeldet.</span><span class="sxs-lookup"><span data-stu-id="29317-550">The test results are reported.</span></span>

<span data-ttu-id="29317-551">Üblicherweise ist der Testwebhost anders konfiguriert als der normale Webhost der App für die Testläufe.</span><span class="sxs-lookup"><span data-stu-id="29317-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="29317-552">Beispielsweise könnten für die Tests eine andere Datenbank oder andere App-Einstellungen verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="29317-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="29317-553">Infrastrukturkomponenten wie der Testwebhost und der In-Memory-Testserver ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)) werden durch das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket bereitgestellt oder verwaltet.</span><span class="sxs-lookup"><span data-stu-id="29317-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="29317-554">Durch die Verwendung dieses Pakets werden die Testerstellung und -ausführung optimiert.</span><span class="sxs-lookup"><span data-stu-id="29317-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="29317-555">Das `Microsoft.AspNetCore.Mvc.Testing`-Paket verarbeitet die folgenden Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="29317-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="29317-556">Es kopiert die Datei für Abhängigkeiten ( *.deps*) aus dem GS in das *bin-* Verzeichnis des Testprojekts.</span><span class="sxs-lookup"><span data-stu-id="29317-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="29317-557">Es legt das [Inhaltsstammelement](xref:fundamentals/index#content-root) auf das Projektstammelement des GS fest, damit statische Dateien und Seiten/Ansichten bei der Ausführung der Tests gefunden werden.</span><span class="sxs-lookup"><span data-stu-id="29317-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="29317-558">Es stellt die Klasse [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) zur Optimierung des Bootstrappings des GS mit `TestServer` bereit.</span><span class="sxs-lookup"><span data-stu-id="29317-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="29317-559">In der Dokumentation der [Komponententests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) wird beschrieben, wie Sie ein Testprojekt und einen Test-Runner einrichten. Ferner finden Sie dort ausführliche Anweisungen zum Ausführen von Tests sowie Empfehlungen zum Benennen von Tests und Testklassen.</span><span class="sxs-lookup"><span data-stu-id="29317-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="29317-560">Wenn Sie ein Testprojekt für eine App erstellen, verwenden Sie unterschiedliche Projekte für die Komponententests und die Integrationstests.</span><span class="sxs-lookup"><span data-stu-id="29317-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="29317-561">Dadurch wird sichergestellt, dass Komponenten für Infrastrukturtests nicht versehentlich in die Komponententests eingeschlossen werden.</span><span class="sxs-lookup"><span data-stu-id="29317-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="29317-562">Durch die Trennung von Komponenten- und Integrationstests können Sie außerdem steuern, welche Testsätze ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="29317-563">Es gibt praktisch keinen Unterschied zwischen der Konfiguration für Tests von Razor Pages-Apps und MVC-Apps.</span><span class="sxs-lookup"><span data-stu-id="29317-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="29317-564">Der einzige Unterschied besteht darin, wie die Tests benannt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="29317-565">In einer Razor Pages-App werden Tests von Seitenendpunkten normalerweise nach der Seitenmodellklasse benannt (z. B. `IndexPageTests` für das Testen der Komponentenintegration für die Indexseite).</span><span class="sxs-lookup"><span data-stu-id="29317-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="29317-566">In einer MVC-App werden Tests in der Regel nach Controllerklassen organisiert und nach den von ihnen getesteten Controllern benannt (z. B. `HomeControllerTests` für das Testen der Komponentenintegration für den Startseitencontroller).</span><span class="sxs-lookup"><span data-stu-id="29317-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="29317-567">Voraussetzungen für Test-Apps</span><span class="sxs-lookup"><span data-stu-id="29317-567">Test app prerequisites</span></span>

<span data-ttu-id="29317-568">Für das Testprojekt muss Folgendes erfüllt sein:</span><span class="sxs-lookup"><span data-stu-id="29317-568">The test project must:</span></span>

* <span data-ttu-id="29317-569">Verweisen Sie auf die folgenden Pakete:</span><span class="sxs-lookup"><span data-stu-id="29317-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="29317-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="29317-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="29317-571">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="29317-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="29317-572">Geben Sie das Web SDK in der Projektdatei an (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span><span class="sxs-lookup"><span data-stu-id="29317-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="29317-573">Das Web SDK ist bei Verweis auf das [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) erforderlich.</span><span class="sxs-lookup"><span data-stu-id="29317-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="29317-574">Diese Voraussetzungen können Sie in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) sehen.</span><span class="sxs-lookup"><span data-stu-id="29317-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="29317-575">Beachten Sie die Datei *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*.</span><span class="sxs-lookup"><span data-stu-id="29317-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="29317-576">Die Beispiel-App verwendet das [xUnit](https://xunit.github.io/)-Testframework und die [AngleSharp](https://anglesharp.github.io/)-Parserbibliothek, sodass die Beispiel-APP auch auf Folgendes verweist:</span><span class="sxs-lookup"><span data-stu-id="29317-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="29317-577">xunit</span><span class="sxs-lookup"><span data-stu-id="29317-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="29317-578">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="29317-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="29317-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="29317-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="29317-580">GS-Umgebung</span><span class="sxs-lookup"><span data-stu-id="29317-580">SUT environment</span></span>

<span data-ttu-id="29317-581">Wenn die [Umgebung](xref:fundamentals/environments) des GS nicht festgelegt ist, wird standardmäßig die Entwicklungsumgebung verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="29317-582">Grundlegende Tests mit der Standard-WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="29317-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="29317-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) wird verwendet, um einen [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) für die Integrationstests zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="29317-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="29317-584">`TEntryPoint` ist die Einstiegspunktklasse des GS, in der Regel die `Startup`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="29317-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="29317-585">Testklassen implementieren eine *class fixture*-Schnittstelle ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)), um anzugeben, dass die Klasse Tests enthält und um in den Tests in der Klasse gemeinsame Objektinstanzen bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="29317-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="29317-586">Die folgende Testklasse (`BasicTests`) verwendet die `WebApplicationFactory` für den Bootstrap des GS und um einen [HttpClient](/dotnet/api/system.net.http.httpclient) für eine Testmethode `Get_EndpointsReturnSuccessAndCorrectContentType` bereitzustellen.</span><span class="sxs-lookup"><span data-stu-id="29317-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="29317-587">Die Methode prüft, ob der Antwortstatuscode erfolgreich ist (Statuscodes im Bereich 200-299) und der `Content-Type`-Header für mehrere App-Seiten `text/html; charset=utf-8` lautet.</span><span class="sxs-lookup"><span data-stu-id="29317-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="29317-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) erstellt eine Instanz von `HttpClient`, die automatisch Umleitungen folgt und Cookies verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="29317-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="29317-589">Standardmäßig werden nicht erforderliche Cookies nicht über Anforderungen hinweg beibehalten, wenn die [DSGVO-Zustimmungsrichtlinie](xref:security/gdpr) aktiviert ist.</span><span class="sxs-lookup"><span data-stu-id="29317-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="29317-590">Um nicht erforderliche Cookies beizubehalten, wie z. B. diejenigen, die vom TempData-Anbieter verwendet werden, markieren Sie die Cookies in den Tests als unverzichtbar.</span><span class="sxs-lookup"><span data-stu-id="29317-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="29317-591">Anweisungen zum Markieren eines Cookies als erforderlich finden Sie unter [Erforderliche Cookies](xref:security/gdpr#essential-cookies).</span><span class="sxs-lookup"><span data-stu-id="29317-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="29317-592">Anpassen von WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="29317-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="29317-593">Die Webhostkonfiguration kann unabhängig von den Testklassen durch Erben von der `WebApplicationFactory` erstellt werden, um eine oder mehrere benutzerdefinierte Factorys zu erstellen:</span><span class="sxs-lookup"><span data-stu-id="29317-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="29317-594">Erben Sie von `WebApplicationFactory`, und überschreiben Sie [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span><span class="sxs-lookup"><span data-stu-id="29317-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="29317-595">Der [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) erlaubt die Konfiguration der Dienstsammlung mit [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), die vor den `Startup.ConfigureServices` der App ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="29317-596">Der [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) ermöglicht das Überschreiben und Ändern registrierter Dienste in der Dienstsammlung mit [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span><span class="sxs-lookup"><span data-stu-id="29317-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="29317-597">Das Datenbankseeding in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) wird mithilfe der `InitializeDbForTests`-Methode durchgeführt.</span><span class="sxs-lookup"><span data-stu-id="29317-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="29317-598">Die Methode wird im Abschnitt [Beispiel für Integrationstests: Organisation der Test-App](#test-app-organization) beschrieben.</span><span class="sxs-lookup"><span data-stu-id="29317-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="29317-599">Verwenden Sie die benutzerdefinierte `CustomWebApplicationFactory` in Testklassen.</span><span class="sxs-lookup"><span data-stu-id="29317-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="29317-600">Das folgende Beispiel verwendet die Factory in der `IndexPageTests`-Klasse:</span><span class="sxs-lookup"><span data-stu-id="29317-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="29317-601">Der Client der Beispiel-App wird so konfiguriert, dass der `HttpClient` keinen Umleitungen folgt.</span><span class="sxs-lookup"><span data-stu-id="29317-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="29317-602">Wie weiter unten im Abschnitt [Pseudoauthentifizierung](#mock-authentication) erläutert wird, können Tests so das Ergebnis der ersten Reaktion der App überprüfen.</span><span class="sxs-lookup"><span data-stu-id="29317-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="29317-603">In vielen dieser Tests mit einem `Location`-Header ist die erste Antwort eine Umleitung.</span><span class="sxs-lookup"><span data-stu-id="29317-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="29317-604">Ein typischer Test verwendet die `HttpClient`- und die Hilfsprogrammmethoden, um die Anforderung und die Antwort zu verarbeiten:</span><span class="sxs-lookup"><span data-stu-id="29317-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="29317-605">Jede POST-Anforderung an das GS muss die Fälschungsschutzprüfung bestehen, die automatisch vom [Daten- und Fälschungsschutzsystem](xref:security/data-protection/introduction) der App durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="29317-606">Als Vorbereitung auf die POST-Anforderung eines Tests muss die Test-App folgende Schritte ausführen:</span><span class="sxs-lookup"><span data-stu-id="29317-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="29317-607">Senden einer Anforderung für die Seite.</span><span class="sxs-lookup"><span data-stu-id="29317-607">Make a request for the page.</span></span>
1. <span data-ttu-id="29317-608">Analysieren des Fälschungsschutzcookies und des Anforderungsvalidierungstokens von der Antwort.</span><span class="sxs-lookup"><span data-stu-id="29317-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="29317-609">Senden der POST-Anforderung mit vorhandenem Fälschungsschutzcookie und Anforderungsvalidierungstoken.</span><span class="sxs-lookup"><span data-stu-id="29317-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="29317-610">Die `SendAsync`-Hilfsprogrammerweiterungsmethoden (*Helpers/HttpClientExtensions.cs*) und die `GetDocumentAsync`-Hilfsprogrammmethode (*Helpers/HtmlHelpers.cs*) in der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) verwenden den [AngleSharp](https://anglesharp.github.io/)-Parser, um die Fälschungsschutzprüfungen mit den folgenden Methoden durchzuführen:</span><span class="sxs-lookup"><span data-stu-id="29317-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="29317-611">`GetDocumentAsync`: Empfängt die [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) und gibt ein `IHtmlDocument` zurück.</span><span class="sxs-lookup"><span data-stu-id="29317-611">`GetDocumentAsync`: Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="29317-612">`GetDocumentAsync` verwendet eine Factory, die eine *virtuelle Antwort* basierend auf der ursprünglichen `HttpResponseMessage` vorbereitet.</span><span class="sxs-lookup"><span data-stu-id="29317-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="29317-613">Weitere Informationen finden Sie in der [AngleSharp-Dokumentation](https://github.com/AngleSharp/AngleSharp#documentation).</span><span class="sxs-lookup"><span data-stu-id="29317-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="29317-614">`SendAsync`-Erweiterungsmethoden für den `HttpClient` verfassen eine [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) und rufen [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) auf, um Anforderungen an das GS zu senden.</span><span class="sxs-lookup"><span data-stu-id="29317-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="29317-615">Überladungen für `SendAsync` akzeptieren das HTML-Formular (`IHtmlFormElement`) und Folgendes:</span><span class="sxs-lookup"><span data-stu-id="29317-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="29317-616">Schaltfläche „Senden“ des Formulars (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="29317-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="29317-617">Formularwerteauflistung (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="29317-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="29317-618">Schaltfläche „Senden“ (`IHtmlElement`) und Formularwerte (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="29317-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="29317-619">[AngleSharp](https://anglesharp.github.io/) ist eine Drittanbieter-Analysebibliothek, die in diesem Thema und in der Beispiel-App zu Demonstrationszwecken verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="29317-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="29317-620">AngleSharp wird für Integrationstests von ASP.NET Core-Apps weder unterstützt noch benötigt.</span><span class="sxs-lookup"><span data-stu-id="29317-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="29317-621">Andere Parser können verwendet werden, beispielsweise [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span><span class="sxs-lookup"><span data-stu-id="29317-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="29317-622">Ein anderer Ansatz besteht darin, Code zu schreiben, der das Anforderungsüberprüfungstoken und das Fälschungsschutzcookie des Fälschungsschutzsystems direkt verarbeitet.</span><span class="sxs-lookup"><span data-stu-id="29317-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="29317-623">Anpassen des Clients mit WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="29317-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="29317-624">Wenn eine zusätzliche Konfiguration innerhalb einer Testmethode erforderlich ist, erstellt [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) eine neue `WebApplicationFactory` mit einem [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder), der mittels Konfiguration weiter angepasst wird.</span><span class="sxs-lookup"><span data-stu-id="29317-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="29317-625">Die `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Testmethode der [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) zeigt die Verwendung von `WithWebHostBuilder`.</span><span class="sxs-lookup"><span data-stu-id="29317-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="29317-626">Bei diesem Test wird eine Datensatzlöschung in der Datenbank durch Auslösen einer Formularübermittlung im GS durchführt.</span><span class="sxs-lookup"><span data-stu-id="29317-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="29317-627">Da ein anderer Test in der `IndexPageTests`-Klasse einen Vorgang durchführt, der alle Datensätze in der Datenbank löscht und der möglicherweise vor der `Post_DeleteMessageHandler_ReturnsRedirectToRoot`-Methode ausgeführt wird, wird in dieser Testmethode ein erneutes Seeding der Datenbank durchgeführt, um sicherzustellen, dass ein Datensatz vorhanden ist, den das GS löschen kann.</span><span class="sxs-lookup"><span data-stu-id="29317-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="29317-628">Die Auswahl der ersten Löschschaltfläche des `messages`-Formulars im GS wird in der Anforderung an das GS simuliert:</span><span class="sxs-lookup"><span data-stu-id="29317-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="29317-629">Clientoptionen</span><span class="sxs-lookup"><span data-stu-id="29317-629">Client options</span></span>

<span data-ttu-id="29317-630">In der folgenden Tabelle werden die Standard-[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) gezeigt, die bei der Erstellung von `HttpClient`-Instanzen verfügbar sind.</span><span class="sxs-lookup"><span data-stu-id="29317-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="29317-631">Option</span><span class="sxs-lookup"><span data-stu-id="29317-631">Option</span></span> | <span data-ttu-id="29317-632">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="29317-632">Description</span></span> | <span data-ttu-id="29317-633">Standard</span><span class="sxs-lookup"><span data-stu-id="29317-633">Default</span></span> |
| ---
<span data-ttu-id="29317-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-635">'Blazor'</span></span>
- <span data-ttu-id="29317-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-636">'Identity'</span></span>
- <span data-ttu-id="29317-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-638">'Razor'</span></span>
- <span data-ttu-id="29317-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-639">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-641">'Blazor'</span></span>
- <span data-ttu-id="29317-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-642">'Identity'</span></span>
- <span data-ttu-id="29317-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-644">'Razor'</span></span>
- <span data-ttu-id="29317-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-647">'Blazor'</span></span>
- <span data-ttu-id="29317-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-648">'Identity'</span></span>
- <span data-ttu-id="29317-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-650">'Razor'</span></span>
- <span data-ttu-id="29317-651">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-653">'Blazor'</span></span>
- <span data-ttu-id="29317-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-654">'Identity'</span></span>
- <span data-ttu-id="29317-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-656">'Razor'</span></span>
- <span data-ttu-id="29317-657">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-657">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-659">'Blazor'</span></span>
- <span data-ttu-id="29317-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-660">'Identity'</span></span>
- <span data-ttu-id="29317-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-662">'Razor'</span></span>
- <span data-ttu-id="29317-663">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-663">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen automatisch Umleitungsantworten befolgen sollen.</span><span class="sxs-lookup"><span data-stu-id="29317-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="29317-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Ruft die Basisadresse der `HttpClient`-Instanzen ab oder legt sie fest.</span><span class="sxs-lookup"><span data-stu-id="29317-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="29317-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Ruft ab oder legt fest, ob `HttpClient`-Instanzen Cookies verarbeiten sollen.</span><span class="sxs-lookup"><span data-stu-id="29317-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="29317-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Ruft die maximale Anzahl von Umleitungsantworten ab, die von `HttpClient`-Instanzen befolgt werden sollen, oder legt diese fest.</span><span class="sxs-lookup"><span data-stu-id="29317-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="29317-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="29317-668">| 7 |</span></span>

<span data-ttu-id="29317-669">Erstellen Sie die `WebApplicationFactoryClientOptions`-Klasse, und übergeben Sie sie an die [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)-Methode (Standardwerte werden im Codebeispiel gezeigt):</span><span class="sxs-lookup"><span data-stu-id="29317-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="29317-670">Fügen Sie Pseudodienste ein</span><span class="sxs-lookup"><span data-stu-id="29317-670">Inject mock services</span></span>

<span data-ttu-id="29317-671">Dienste können in einem Test mit dem Aufruf [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) auf dem Host-Builder überschrieben werden.</span><span class="sxs-lookup"><span data-stu-id="29317-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="29317-672">**Um Pseudodienste einzufügen, muss das GS über eine `Startup`-Klasse mit einer `Startup.ConfigureServices`-Methode verfügen.**</span><span class="sxs-lookup"><span data-stu-id="29317-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="29317-673">Das Beispiel-GS enthält einen bereichsbezogenen Dienst, der ein Zitat zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="29317-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="29317-674">Wenn die Indexseite angefordert wird, wird das Zitat in ein ausgeblendetes Feld auf der Indexseite eingebettet.</span><span class="sxs-lookup"><span data-stu-id="29317-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="29317-675">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="29317-676">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="29317-677">*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="29317-678">*Pages/Index.cshtml.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="29317-679">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="29317-680">Das folgende Markup wird generiert, wenn die GS-App ausgeführt wird:</span><span class="sxs-lookup"><span data-stu-id="29317-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="29317-681">Um den Dienst und die Zitateinfügung in einem Integrationstest zu testen, wird vom Test ein Pseudodienst in das GS eingefügt.</span><span class="sxs-lookup"><span data-stu-id="29317-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="29317-682">Der Pseudodienst ersetzt `QuoteService` der App durch einen Dienst namens `TestQuoteService`, der von der Test-App bereitgestellt wird:</span><span class="sxs-lookup"><span data-stu-id="29317-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="29317-683">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="29317-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="29317-684">`ConfigureTestServices` wird aufgerufen, und der bereichsbezogene Dienst wird registriert:</span><span class="sxs-lookup"><span data-stu-id="29317-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="29317-685">Das während der Ausführung des Tests erstellte Markup gibt das von `TestQuoteService` bereitgestellte Zitat wieder, somit ist die Assertion erfolgreich:</span><span class="sxs-lookup"><span data-stu-id="29317-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="29317-686">Pseudo-Authentifizierung</span><span class="sxs-lookup"><span data-stu-id="29317-686">Mock authentication</span></span>

<span data-ttu-id="29317-687">Tests in der `AuthTests`-Klasse prüfen, ob ein sicherer Endpunkt:</span><span class="sxs-lookup"><span data-stu-id="29317-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="29317-688">Einen nicht authentifizierten Benutzer an die Anmeldeseite der App zurückleitet.</span><span class="sxs-lookup"><span data-stu-id="29317-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="29317-689">Den Inhalt für einen authentifizierten Benutzer zurückgibt.</span><span class="sxs-lookup"><span data-stu-id="29317-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="29317-690">Im GS verwendet die `/SecurePage`-Seite eine [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)-Konvention, um einen [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) auf die Seite anzuwenden.</span><span class="sxs-lookup"><span data-stu-id="29317-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="29317-691">Weitere Informationen finden Sie unter [Razor Pages-Autorisierungskonventionen](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span><span class="sxs-lookup"><span data-stu-id="29317-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="29317-692">Im `Get_SecurePageRedirectsAnUnauthenticatedUser`-Test werden [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) so festgelegt, dass Umleitungen unzulässig sind, indem [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) auf `false` festgelegt wird:</span><span class="sxs-lookup"><span data-stu-id="29317-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="29317-693">Indem dem Client untersagt wird, die Umleitung zu befolgen, können folgende Prüfungen durchgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="29317-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="29317-694">Der vom GS zurückgegebene Statuscode kann mit dem erwarteten [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)-Ergebnis verglichen werden – anstatt mit dem endgültigen Statuscode nach der Umleitung zur Anmeldeseite ([HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)).</span><span class="sxs-lookup"><span data-stu-id="29317-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="29317-695">Der Wert für den `Location`-Header in den Antwortheadern wird geprüft, um zu bestätigen, dass er mit `http://localhost/Identity/Account/Login` beginnt. (Es wird nicht die abschließende Antwort der Anmeldeseite verwendet, bei der der `Location`-Header nicht vorhanden wäre.)</span><span class="sxs-lookup"><span data-stu-id="29317-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="29317-696">Die Test-App kann einen <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) simulieren, um Aspekte der Authentifizierung und Autorisierung zu testen.</span><span class="sxs-lookup"><span data-stu-id="29317-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="29317-697">Ein minimales Szenario gibt [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*) zurück:</span><span class="sxs-lookup"><span data-stu-id="29317-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="29317-698">Der `TestAuthHandler` wird aufgerufen, um einen Benutzer zu authentifizieren, wenn das Authentifizierungsschema auf `Test` festgelegt ist, in dem `AddAuthentication` für `ConfigureTestServices` registriert ist:</span><span class="sxs-lookup"><span data-stu-id="29317-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="29317-699">Weitere Informationen zu `WebApplicationFactoryClientOptions` finden Sie im Abschnitt [Clientoptionen](#client-options).</span><span class="sxs-lookup"><span data-stu-id="29317-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="29317-700">Festlegen der Umgebung</span><span class="sxs-lookup"><span data-stu-id="29317-700">Set the environment</span></span>

<span data-ttu-id="29317-701">Standardmäßig ist die Host- und App-Umgebung des GS für die Verwendung der Entwicklungsumgebung konfiguriert.</span><span class="sxs-lookup"><span data-stu-id="29317-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="29317-702">So überschreiben Sie die Umgebung des GS:</span><span class="sxs-lookup"><span data-stu-id="29317-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="29317-703">Legen Sie die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable fest (z. B. `Staging`, `Production` oder ein anderer benutzerdefinierter Wert wie `Testing`).</span><span class="sxs-lookup"><span data-stu-id="29317-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="29317-704">Überschreiben Sie `CreateWebHostBuilder` in der Test-App, um die `ASPNETCORE_ENVIRONMENT`-Umgebungsvariable zu lesen.</span><span class="sxs-lookup"><span data-stu-id="29317-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="29317-705">Die Umgebung kann auch direkt auf dem Hostgenerator in einer benutzerdefinierten <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> festgelegt werden:</span><span class="sxs-lookup"><span data-stu-id="29317-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="29317-706">Ableitung des Inhaltsstammpfads der App durch die Testinfrastruktur</span><span class="sxs-lookup"><span data-stu-id="29317-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="29317-707">Der `WebApplicationFactory`-Konstruktor leitet den [Inhaltsstammpfad](xref:fundamentals/index#content-root) der App ab, indem er in der Assembly, die die Integrationstests enthält, nach einem [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) mit einem Schlüssel sucht, der `TEntryPoint` assembly `System.Reflection.Assembly.FullName` entspricht.</span><span class="sxs-lookup"><span data-stu-id="29317-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="29317-708">Wenn kein Attribut mit dem richtigen Schlüssel gefunden wird, greift `WebApplicationFactory` auf die Suche nach einer Projektmappendatei ( *.sln*) zurück und fügt den `TEntryPoint`-Assemblynamen an das Projektmappenverzeichnis an.</span><span class="sxs-lookup"><span data-stu-id="29317-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="29317-709">Das Stammverzeichnis der App (der Inhaltsstammpfad) wird verwendet, um Sichten und Inhaltsdateien zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="29317-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="29317-710">Deaktivieren der Erstellung von Schattenkopien</span><span class="sxs-lookup"><span data-stu-id="29317-710">Disable shadow copying</span></span>

<span data-ttu-id="29317-711">Das Erstellen von Schattenkopien bewirkt, dass die Tests in einem anderen Verzeichnis als dem Ausgabeverzeichnis ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="29317-712">Damit Tests ordnungsgemäß funktionieren, muss die Erstellung von Schattenkopien deaktiviert werden.</span><span class="sxs-lookup"><span data-stu-id="29317-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="29317-713">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) verwendet xUnit und deaktiviert die Erstellung von Schattenkopien für xUnit durch Einschließen einer *xunit.runner.json*-Datei mit der korrekten Konfigurationseinstellung.</span><span class="sxs-lookup"><span data-stu-id="29317-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="29317-714">Weitere Informationen finden Sie unter [Konfigurieren von xUnit mit JSON](https://xunit.github.io/docs/configuring-with-json.html).</span><span class="sxs-lookup"><span data-stu-id="29317-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="29317-715">Fügen Sie die Datei *xunit.runner.json* mit folgendem Inhalt zum Stamm des Testprojekts hinzu:</span><span class="sxs-lookup"><span data-stu-id="29317-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="29317-716">Wenn Sie Visual Studio verwenden, legen Sie die Eigenschaft **In Ausgabeverzeichnis kopieren** der Datei auf **Immer kopieren** fest.</span><span class="sxs-lookup"><span data-stu-id="29317-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="29317-717">Wenn Sie Visual Studio nicht verwenden, fügen Sie der Projektdatei der Test-App ein `Content`-Ziel hinzu:</span><span class="sxs-lookup"><span data-stu-id="29317-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="29317-718">Verwerfen von Objekten</span><span class="sxs-lookup"><span data-stu-id="29317-718">Disposal of objects</span></span>

<span data-ttu-id="29317-719">Nachdem die Tests der `IClassFixture`-Implementierung ausgeführt wurden, werden [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) und [HttpClient](/dotnet/api/system.net.http.httpclient) verworfen, wenn xUnit die [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) verwirft.</span><span class="sxs-lookup"><span data-stu-id="29317-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="29317-720">Wenn vom Entwickler instanziierte Objekte verworfen werden müssen, müssen Sie dies in der `IClassFixture`-Implementierung tun.</span><span class="sxs-lookup"><span data-stu-id="29317-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="29317-721">Weitere Informationen finden Sie unter [Implementieren einer Dispose-Methode](/dotnet/standard/garbage-collection/implementing-dispose).</span><span class="sxs-lookup"><span data-stu-id="29317-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="29317-722">Beispiel für Integrationstests</span><span class="sxs-lookup"><span data-stu-id="29317-722">Integration tests sample</span></span>

<span data-ttu-id="29317-723">Die [Beispiel-App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) besteht aus zwei Apps:</span><span class="sxs-lookup"><span data-stu-id="29317-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="29317-724">App</span><span class="sxs-lookup"><span data-stu-id="29317-724">App</span></span> | <span data-ttu-id="29317-725">Projektverzeichnis</span><span class="sxs-lookup"><span data-stu-id="29317-725">Project directory</span></span> | <span data-ttu-id="29317-726">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="29317-726">Description</span></span> |
| --- | ---
<span data-ttu-id="29317-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-728">'Blazor'</span></span>
- <span data-ttu-id="29317-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-729">'Identity'</span></span>
- <span data-ttu-id="29317-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-731">'Razor'</span></span>
- <span data-ttu-id="29317-732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-734">'Blazor'</span></span>
- <span data-ttu-id="29317-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-735">'Identity'</span></span>
- <span data-ttu-id="29317-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-737">'Razor'</span></span>
- <span data-ttu-id="29317-738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-740">'Blazor'</span></span>
- <span data-ttu-id="29317-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-741">'Identity'</span></span>
- <span data-ttu-id="29317-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-743">'Razor'</span></span>
- <span data-ttu-id="29317-744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-746">'Blazor'</span></span>
- <span data-ttu-id="29317-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-747">'Identity'</span></span>
- <span data-ttu-id="29317-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-749">'Razor'</span></span>
- <span data-ttu-id="29317-750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-752">'Blazor'</span></span>
- <span data-ttu-id="29317-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-753">'Identity'</span></span>
- <span data-ttu-id="29317-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-755">'Razor'</span></span>
- <span data-ttu-id="29317-756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-758">'Blazor'</span></span>
- <span data-ttu-id="29317-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-759">'Identity'</span></span>
- <span data-ttu-id="29317-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-761">'Razor'</span></span>
- <span data-ttu-id="29317-762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-762">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-764">'Blazor'</span></span>
- <span data-ttu-id="29317-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-765">'Identity'</span></span>
- <span data-ttu-id="29317-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-767">'Razor'</span></span>
- <span data-ttu-id="29317-768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-770">'Blazor'</span></span>
- <span data-ttu-id="29317-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-771">'Identity'</span></span>
- <span data-ttu-id="29317-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-773">'Razor'</span></span>
- <span data-ttu-id="29317-774">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-776">'Blazor'</span></span>
- <span data-ttu-id="29317-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-777">'Identity'</span></span>
- <span data-ttu-id="29317-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-779">'Razor'</span></span>
- <span data-ttu-id="29317-780">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-780">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-781">------ | | Nachrichten-App (das GS) | *src/RazorPagesProject* | Ermöglicht einem Benutzer, Nachrichten hinzuzufügen, eine oder alle Nachrichten zu löschen und Nachrichten zu analysieren.</span><span class="sxs-lookup"><span data-stu-id="29317-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="29317-782">| | Test-App | *tests/RazorPagesProject.Tests* | Wird verwendet, um das GS zu integrieren.</span><span class="sxs-lookup"><span data-stu-id="29317-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="29317-783">Die Tests können mit den integrierten Testfunktionen einer IDE, wie z. B. [Visual Studio](https://visualstudio.microsoft.com) ausgeführt werden.</span><span class="sxs-lookup"><span data-stu-id="29317-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="29317-784">Wenn Sie [Visual Studio Code](https://code.visualstudio.com/) oder die Befehlszeile verwenden, führen Sie den folgenden Befehl an einer Eingabeaufforderung im Verzeichnis *tests/RazorPagesProject.Tests* aus:</span><span class="sxs-lookup"><span data-stu-id="29317-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="29317-785">Organisation der Nachrichten-App (GS)</span><span class="sxs-lookup"><span data-stu-id="29317-785">Message app (SUT) organization</span></span>

<span data-ttu-id="29317-786">Beim GS handelt es sich um ein Razor Pages-Nachrichtensystem mit folgenden Merkmalen:</span><span class="sxs-lookup"><span data-stu-id="29317-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="29317-787">Die Indexseite der App (*Pages/Index.cshtml* und *Pages/Index.cshtml.cs*) stellt eine Benutzeroberfläche und Seitenmodellmethoden bereit, mit denen Sie das Hinzufügen, Löschen und Analysieren von Nachrichten (durchschnittliche Anzahl von Wörtern pro Nachricht) steuern können.</span><span class="sxs-lookup"><span data-stu-id="29317-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="29317-788">Eine Nachricht wird von der `Message`-Klasse (*Data/Message.cs*) mit zwei Eigenschaften beschrieben: `Id` (Schlüssel) und `Text` (Nachricht).</span><span class="sxs-lookup"><span data-stu-id="29317-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="29317-789">Die `Text`-Eigenschaft ist erforderlich und auf 200 Zeichen beschränkt.</span><span class="sxs-lookup"><span data-stu-id="29317-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="29317-790">Nachrichten werden mithilfe der [In-Memory-Datenbank von Entity Framework](/ef/core/providers/in-memory/)&#8224; gespeichert.</span><span class="sxs-lookup"><span data-stu-id="29317-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="29317-791">Die App enthält eine Datenzugriffsebene (DAL) in ihrer Datenbankkontextklasse `AppDbContext` (*Data/AppDbContext.cs*).</span><span class="sxs-lookup"><span data-stu-id="29317-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="29317-792">Wenn die Datenbank beim Starten der App leer ist, wird der Nachrichtenspeicher mit drei Nachrichten initialisiert.</span><span class="sxs-lookup"><span data-stu-id="29317-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="29317-793">Die App enthält eine `/SecurePage`, auf die nur ein authentifizierter Benutzer zugreifen kann.</span><span class="sxs-lookup"><span data-stu-id="29317-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="29317-794">&#8224;Im Entity Framework-Thema [Testen mit InMemory](/ef/core/miscellaneous/testing/in-memory) wird die Verwendung einer In-Memory-Datenbank für Tests mit MSTest erläutert.</span><span class="sxs-lookup"><span data-stu-id="29317-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="29317-795">In diesem Thema wird das Testframework [xUnit](https://xunit.github.io/) verwendet.</span><span class="sxs-lookup"><span data-stu-id="29317-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="29317-796">Testkonzepte und Testimplementierungen in verschiedenen Testframeworks sind ähnlich, jedoch nicht identisch.</span><span class="sxs-lookup"><span data-stu-id="29317-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="29317-797">Obwohl die App nicht das Repositorymuster verwendet und kein effektives Beispiel für das [Arbeitseinheitsmuster](https://martinfowler.com/eaaCatalog/unitOfWork.html) ist, unterstützt Razor Pages diese Entwicklungsmuster.</span><span class="sxs-lookup"><span data-stu-id="29317-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="29317-798">Weitere Informationen finden Sie unter [Entwerfen der Persistenzebene der Infrastruktur](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) und [Testcontrollerlogik](/aspnet/core/mvc/controllers/testing) (im Beispiel wird das Repositorymuster implementiert).</span><span class="sxs-lookup"><span data-stu-id="29317-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="29317-799">Organisation der Test-App</span><span class="sxs-lookup"><span data-stu-id="29317-799">Test app organization</span></span>

<span data-ttu-id="29317-800">Bei der Test-App handelt es sich um eine Konsolen-App im Verzeichnis *tests/RazorPagesProject.Tests*.</span><span class="sxs-lookup"><span data-stu-id="29317-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="29317-801">Test-App-Verzeichnis</span><span class="sxs-lookup"><span data-stu-id="29317-801">Test app directory</span></span> | <span data-ttu-id="29317-802">Beschreibung</span><span class="sxs-lookup"><span data-stu-id="29317-802">Description</span></span> |
| ---
<span data-ttu-id="29317-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-804">'Blazor'</span></span>
- <span data-ttu-id="29317-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-805">'Identity'</span></span>
- <span data-ttu-id="29317-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-807">'Razor'</span></span>
- <span data-ttu-id="29317-808">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-810">'Blazor'</span></span>
- <span data-ttu-id="29317-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-811">'Identity'</span></span>
- <span data-ttu-id="29317-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-813">'Razor'</span></span>
- <span data-ttu-id="29317-814">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-816">'Blazor'</span></span>
- <span data-ttu-id="29317-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-817">'Identity'</span></span>
- <span data-ttu-id="29317-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-819">'Razor'</span></span>
- <span data-ttu-id="29317-820">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-822">'Blazor'</span></span>
- <span data-ttu-id="29317-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-823">'Identity'</span></span>
- <span data-ttu-id="29317-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-825">'Razor'</span></span>
- <span data-ttu-id="29317-826">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-828">'Blazor'</span></span>
- <span data-ttu-id="29317-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-829">'Identity'</span></span>
- <span data-ttu-id="29317-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-831">'Razor'</span></span>
- <span data-ttu-id="29317-832">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-834">'Blazor'</span></span>
- <span data-ttu-id="29317-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-835">'Identity'</span></span>
- <span data-ttu-id="29317-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-837">'Razor'</span></span>
- <span data-ttu-id="29317-838">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-840">'Blazor'</span></span>
- <span data-ttu-id="29317-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-841">'Identity'</span></span>
- <span data-ttu-id="29317-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-843">'Razor'</span></span>
- <span data-ttu-id="29317-844">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-844">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-846">'Blazor'</span></span>
- <span data-ttu-id="29317-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-847">'Identity'</span></span>
- <span data-ttu-id="29317-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-849">'Razor'</span></span>
- <span data-ttu-id="29317-850">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-852">'Blazor'</span></span>
- <span data-ttu-id="29317-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-853">'Identity'</span></span>
- <span data-ttu-id="29317-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-855">'Razor'</span></span>
- <span data-ttu-id="29317-856">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="29317-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="29317-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="29317-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="29317-858">'Blazor'</span></span>
- <span data-ttu-id="29317-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="29317-859">'Identity'</span></span>
- <span data-ttu-id="29317-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="29317-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="29317-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="29317-861">'Razor'</span></span>
- <span data-ttu-id="29317-862">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="29317-862">'SignalR' uid:</span></span> 

<span data-ttu-id="29317-863">------ | | *AuthTests* | Enthält Testmethoden für:</span><span class="sxs-lookup"><span data-stu-id="29317-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="29317-864">Zugreifen auf eine sichere Seite durch einen nicht authentifizierten Benutzer.</span><span class="sxs-lookup"><span data-stu-id="29317-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="29317-865">Zugreifen auf eine sichere Seite durch einen authentifizierten Benutzer mit einem Pseudo-<xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span><span class="sxs-lookup"><span data-stu-id="29317-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="29317-866">Abrufen eines GitHub-Benutzerprofils und Überprüfen der Benutzeranmeldung des Profils.</span><span class="sxs-lookup"><span data-stu-id="29317-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="29317-867">| | *BasicTests* | Enthält eine Testmethode für Routing und Inhaltstyp.</span><span class="sxs-lookup"><span data-stu-id="29317-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="29317-868">| | *IntegrationTests* | Enthält die Integrationstests für die Indexseite unter Verwendung der benutzerdefinierten `WebApplicationFactory`-Klasse.</span><span class="sxs-lookup"><span data-stu-id="29317-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="29317-869">| | *Helpers/Utilities* | </span><span class="sxs-lookup"><span data-stu-id="29317-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="29317-870">*Utilities.cs* enthält die `InitializeDbForTests`-Methode, mit der ein Seeding der Datenbank mit Testdaten durchgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="29317-871">*HtmlHelpers.cs* stellt eine Methode bereit, mit der ein AngleSharp-`IHtmlDocument` zur Verwendung durch die Testmethoden zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="29317-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="29317-872">*HttpClientExtensions.cs* stellt Überladungen für `SendAsync` bereit, um Anforderungen an das GS zu senden.</span><span class="sxs-lookup"><span data-stu-id="29317-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="29317-873">Das Testframework ist [xUnit](https://xunit.github.io/).</span><span class="sxs-lookup"><span data-stu-id="29317-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="29317-874">Integrationstests werden mit dem [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost) durchgeführt, der den [Testserver](/dotnet/api/microsoft.aspnetcore.testhost.testserver) umfasst.</span><span class="sxs-lookup"><span data-stu-id="29317-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="29317-875">Da das [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)-Paket zum Konfigurieren des Testhosts und des Testservers verwendet wird, benötigen die `TestHost`- und `TestServer`-Pakete keine direkten Paketverweise in der Projektdatei der Test-App bzw. keine Entwicklerkonfiguration in der Test-App.</span><span class="sxs-lookup"><span data-stu-id="29317-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="29317-876">**Seeding der Datenbank für Testzwecke**</span><span class="sxs-lookup"><span data-stu-id="29317-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="29317-877">Für Integrationstests muss die Datenbank in der Regel vor der Testausführung ein kleines Dataset enthalten.</span><span class="sxs-lookup"><span data-stu-id="29317-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="29317-878">Beispielsweise wird bei einem Löschtest ein Löschvorgang eines Datensatzes der Datenbank abgerufen, weshalb die Datenbank mindestens einen Datensatz aufweisen muss, damit die Löschanforderung erfolgreich ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="29317-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="29317-879">Die Beispiel-App führt ein Seeding der Datenbank mit drei Nachrichten in *Utilities.cs* durch, die von Tests bei der Ausführung verwendet werden können:</span><span class="sxs-lookup"><span data-stu-id="29317-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="29317-880">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="29317-880">Additional resources</span></span>

* [<span data-ttu-id="29317-881">Komponententests</span><span class="sxs-lookup"><span data-stu-id="29317-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
