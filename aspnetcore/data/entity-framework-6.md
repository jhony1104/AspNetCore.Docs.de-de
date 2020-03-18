---
title: Erste Schritte mit ASP.NET Core und Entity Framework 6
author: rick-anderson
description: Dieser Artikel veranschaulicht die Verwendung von Entity Framework 6 in einer ASP.NET Core-Anwendung.
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: data/entity-framework-6
ms.openlocfilehash: 85cf86dcb22ef94cfc87975abaab176e4f1227d3
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78645427"
---
# <a name="get-started-with-aspnet-core-and-entity-framework-6"></a><span data-ttu-id="338c0-103">Erste Schritte mit ASP.NET Core und Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="338c0-103">Get Started with ASP.NET Core and Entity Framework 6</span></span>

<span data-ttu-id="338c0-104">Von [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex) und [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="338c0-104">By [Paweł Grudzień](https://github.com/pgrudzien12), [Damien Pontifex](https://github.com/DamienPontifex), and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="338c0-105">Dieser Artikel veranschaulicht die Verwendung von Entity Framework 6 in einer ASP.NET Core-Anwendung.</span><span class="sxs-lookup"><span data-stu-id="338c0-105">This article shows how to use Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="overview"></a><span data-ttu-id="338c0-106">Übersicht</span><span class="sxs-lookup"><span data-stu-id="338c0-106">Overview</span></span>

<span data-ttu-id="338c0-107">Damit Sie Entity Framework 6 verwenden können, muss Ihr Projekt mit .NET Framework kompiliert werden, da .NET Core von Entity Framework 6 nicht unterstützt wird.</span><span class="sxs-lookup"><span data-stu-id="338c0-107">To use Entity Framework 6, your project has to compile against .NET Framework, as Entity Framework 6 doesn't support .NET Core.</span></span> <span data-ttu-id="338c0-108">Wenn Sie plattformübergreifende Features benötigen, müssen Sie ein Upgrade auf [Entity Framework Core](/ef/) durchführen.</span><span class="sxs-lookup"><span data-stu-id="338c0-108">If you need cross-platform features you will need to upgrade to [Entity Framework Core](/ef/).</span></span>

<span data-ttu-id="338c0-109">Die empfohlene Methode zum Verwenden von Entity Framework 6 in einer ASP.NET Core-Anwendung besteht darin, den Kontext von Entity Framework 6 und die Modellklassen in einem Klassenbibliotheksprojekt einzufügen, das das .NET-Framework anzielt.</span><span class="sxs-lookup"><span data-stu-id="338c0-109">The recommended way to use Entity Framework 6 in an ASP.NET Core application is to put the EF6 context and model classes in a class library project that targets .NET Framework.</span></span> <span data-ttu-id="338c0-110">Fügen Sie einen Verweis vom ASP.NET Core-Projekt zur Klassenbibliothek hinzu.</span><span class="sxs-lookup"><span data-stu-id="338c0-110">Add a reference to the class library from the ASP.NET Core project.</span></span> <span data-ttu-id="338c0-111">Weitere Informationen finden Sie im Beispiel [Visual Studio-Projektmappe mit Entity Framework 6 und ASP.NET Core-Projekten](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span><span class="sxs-lookup"><span data-stu-id="338c0-111">See the sample [Visual Studio solution with EF6 and ASP.NET Core projects](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/).</span></span>

<span data-ttu-id="338c0-112">Sie können den Kontext von Entity Framework 6 nicht in ASP.NET Core-Projekte einfügen, da diese nicht alle Funktionalitäten unterstützen, die für Entity Framework 6-Befehle (wie *Enable-Migrations*) erforderlich sind.</span><span class="sxs-lookup"><span data-stu-id="338c0-112">You can't put an EF6 context in an ASP.NET Core project because .NET Core projects don't support all of the functionality that EF6 commands such as *Enable-Migrations* require.</span></span>

<span data-ttu-id="338c0-113">Unabhängig vom Projekttyp, in dem Sie den Kontext von Entity Framework 6 platzieren, funktionieren nur Entity Framework 6-Befehlszeilentools mit diesem Kontext.</span><span class="sxs-lookup"><span data-stu-id="338c0-113">Regardless of project type in which you locate your EF6 context, only EF6 command-line tools work with an EF6 context.</span></span> <span data-ttu-id="338c0-114">`Scaffold-DbContext` ist beispielsweise nur in Entity Framework Core verfügbar.</span><span class="sxs-lookup"><span data-stu-id="338c0-114">For example, `Scaffold-DbContext` is only available in Entity Framework Core.</span></span> <span data-ttu-id="338c0-115">Wenn Sie bei einer Datenbank das Reverse Engineering (Zurückentwicklung) in ein Entity Framework 6-Modell durchführen müssen, finden Sie weitere Informationen unter [Code First to an Existing Database (Entity Framework Code First für eine vorhandene Datenbank)](https://msdn.microsoft.com/jj200620).</span><span class="sxs-lookup"><span data-stu-id="338c0-115">If you need to do reverse engineering of a database into an EF6 model, see [Code First to an Existing Database](https://msdn.microsoft.com/jj200620).</span></span>

## <a name="reference-full-framework-and-ef6-in-the-aspnet-core-project"></a><span data-ttu-id="338c0-116">Verweisen auf das vollständige Framework und auf Entity Framework 6 in einem ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="338c0-116">Reference full framework and EF6 in the ASP.NET Core project</span></span>

<span data-ttu-id="338c0-117">Das ASP.NET Core-Projekt muss .NET-Framework anzielen und auf das Entity Framework 6 verweisen.</span><span class="sxs-lookup"><span data-stu-id="338c0-117">Your ASP.NET Core project needs to target .NET Framework and reference EF6.</span></span> <span data-ttu-id="338c0-118">Die *CSPROJ*-Datei Ihres ASP.NET Core-Projekts ähnelt beispielsweise folgendem Beispiel (nur die relevanten Teile der Datei werden dargestellt).</span><span class="sxs-lookup"><span data-stu-id="338c0-118">For example, the *.csproj* file of your ASP.NET Core project will look similar to the following example (only relevant parts of the file are shown).</span></span>

[!code-xml[](entity-framework-6/sample/MVCCore/MVCCore.csproj?range=3-9&highlight=2)]

<span data-ttu-id="338c0-119">Verwenden Sie die Vorlage **ASP.NET Core-Webanwendung (.NET Framework)** , wenn Sie ein neues Projekt erstellen.</span><span class="sxs-lookup"><span data-stu-id="338c0-119">When creating a new project, use the **ASP.NET Core Web Application (.NET Framework)** template.</span></span>

## <a name="handle-connection-strings"></a><span data-ttu-id="338c0-120">Verarbeiten von Verbindungszeichenfolgen</span><span class="sxs-lookup"><span data-stu-id="338c0-120">Handle connection strings</span></span>

<span data-ttu-id="338c0-121">Die Entity Framework 6-Befehlszeilentools, die Sie im Entity Framework 6-Klassenbibliotheksprojekt verwenden, erfordern einen Standardkonstruktor, um den Kontext zu instanziieren.</span><span class="sxs-lookup"><span data-stu-id="338c0-121">The EF6 command-line tools that you'll use in the EF6 class library project require a default constructor so they can instantiate the context.</span></span> <span data-ttu-id="338c0-122">Wenn Sie die Verbindungszeichenfolge angeben möchten, die im ASP.NET Core-Projekt verwendet werden soll, muss der Kontextkonstruktor über einen Parameter verfügen, der das Übergeben der Verbindungszeichenfolge ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="338c0-122">But you'll probably want to specify the connection string to use in the ASP.NET Core project, in which case your context constructor must have a parameter that lets you pass in the connection string.</span></span> <span data-ttu-id="338c0-123">Hier finden Sie ein Beispiel dafür.</span><span class="sxs-lookup"><span data-stu-id="338c0-123">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContext.cs?name=snippet_Constructor)]

<span data-ttu-id="338c0-124">Da in Ihrem Entity Framework 6-Kontext kein Konstruktor ohne Parameter vorhanden ist, muss Ihr Entity Framework 6-Projekt eine Implementierung von [IDbContextFactory](https://msdn.microsoft.com/library/hh506876) bereitstellen.</span><span class="sxs-lookup"><span data-stu-id="338c0-124">Since your EF6 context doesn't have a parameterless constructor, your EF6 project has to provide an implementation of [IDbContextFactory](https://msdn.microsoft.com/library/hh506876).</span></span> <span data-ttu-id="338c0-125">Das Entity Framework 6-Befehlszeilentool sucht und verwendet diese Implementierung, damit der Kontext instanziiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="338c0-125">The EF6 command-line tools will find and use that implementation so they can instantiate the context.</span></span> <span data-ttu-id="338c0-126">Hier finden Sie ein Beispiel dafür.</span><span class="sxs-lookup"><span data-stu-id="338c0-126">Here's an example.</span></span>

[!code-csharp[](entity-framework-6/sample/EF6/SchoolContextFactory.cs?name=snippet_IDbContextFactory)]

<span data-ttu-id="338c0-127">In diesem Beispielcode übergibt die `IDbContextFactory`-Implementierung eine hart codierte Verbindungszeichenfolge.</span><span class="sxs-lookup"><span data-stu-id="338c0-127">In this sample code, the `IDbContextFactory` implementation passes in a hard-coded connection string.</span></span> <span data-ttu-id="338c0-128">Dabei handelt es sich um die Verbindungszeichenfolge, die von den Befehlszeilentools verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="338c0-128">This is the connection string that the command-line tools will use.</span></span> <span data-ttu-id="338c0-129">Sie sollten eine Strategie implementieren, um sicherzustellen, dass die Klassenbibliothek die gleiche Verbindungszeichenfolge wie die aufrufende Anwendung verwendet.</span><span class="sxs-lookup"><span data-stu-id="338c0-129">You'll want to implement a strategy to ensure that the class library uses the same connection string that the calling application uses.</span></span> <span data-ttu-id="338c0-130">Sie könnten den Wert in beiden Projekten beispielsweise aus einer Umgebungsvariable abrufen.</span><span class="sxs-lookup"><span data-stu-id="338c0-130">For example, you could get the value from an environment variable in both projects.</span></span>

## <a name="set-up-dependency-injection-in-the-aspnet-core-project"></a><span data-ttu-id="338c0-131">Einrichten von Dependency Injection im ASP.NET Core-Projekt</span><span class="sxs-lookup"><span data-stu-id="338c0-131">Set up dependency injection in the ASP.NET Core project</span></span>

<span data-ttu-id="338c0-132">Richten Sie den Entity Framework 6-Kontext in der Datei *Startup.cs* des Core-Projekts für Dependency Injection (DI) in `ConfigureServices` ein.</span><span class="sxs-lookup"><span data-stu-id="338c0-132">In the Core project's *Startup.cs* file, set up the EF6 context for dependency injection (DI) in `ConfigureServices`.</span></span> <span data-ttu-id="338c0-133">Die Lebensdauer der Entity Framework-Kontextobjekte sollte auf den Zeitraum vor der Anforderung begrenzt werden.</span><span class="sxs-lookup"><span data-stu-id="338c0-133">EF context objects should be scoped for a per-request lifetime.</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

<span data-ttu-id="338c0-134">Sie können dann mithilfe von DI eine Instanz des Kontext in Ihre Controller abrufen.</span><span class="sxs-lookup"><span data-stu-id="338c0-134">You can then get an instance of the context in your controllers by using DI.</span></span> <span data-ttu-id="338c0-135">Der Code ähnelt dem, den Sie für einen Entity Framework Core-Kontext verwenden würden:</span><span class="sxs-lookup"><span data-stu-id="338c0-135">The code is similar to what you'd write for an EF Core context:</span></span>

[!code-csharp[](entity-framework-6/sample/MVCCore/Controllers/StudentsController.cs?name=snippet_ContextInController)]

## <a name="sample-application"></a><span data-ttu-id="338c0-136">Beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="338c0-136">Sample application</span></span>

<span data-ttu-id="338c0-137">Eine funktionierende Beispielanwendung finden Sie in der [Visual Studio-Beispielprojektmappe](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/), die in diesem Artikel verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="338c0-137">For a working sample application, see the [sample Visual Studio solution](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/entity-framework-6/sample/) that accompanies this article.</span></span>

<span data-ttu-id="338c0-138">Dieses Beispiel kann von Grund auf neu erstellt werden, indem Sie folgende Schritte in Visual Studio befolgen:</span><span class="sxs-lookup"><span data-stu-id="338c0-138">This sample can be created from scratch by the following steps in Visual Studio:</span></span>

* <span data-ttu-id="338c0-139">Erstellen Sie eine Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="338c0-139">Create a solution.</span></span>

* <span data-ttu-id="338c0-140">**Hinzufügen** > **Neues Projekt** > **Web** > **ASP.NET Core-Webanwendung**</span><span class="sxs-lookup"><span data-stu-id="338c0-140">**Add** > **New Project** > **Web** > **ASP.NET Core Web Application**</span></span>
  * <span data-ttu-id="338c0-141">Wählen Sie im Dialogfeld zur Auswahl der Projektvorlage „API und .NET Framework“ in der Dropdown-Liste aus.</span><span class="sxs-lookup"><span data-stu-id="338c0-141">In project template selection dialog, select API and .NET Framework in dropdown</span></span>

* <span data-ttu-id="338c0-142">**Hinzufügen** > **Neues Projekt** > **Windows Desktop** > **Klassenbibliothek (.NET Framework)**</span><span class="sxs-lookup"><span data-stu-id="338c0-142">**Add** > **New Project** > **Windows Desktop** > **Class Library (.NET Framework)**</span></span>

* <span data-ttu-id="338c0-143">Führen Sie in der **Paket-Manager-Konsole** für beide Projekte den Befehl `Install-Package Entityframework` aus.</span><span class="sxs-lookup"><span data-stu-id="338c0-143">In **Package Manager Console** (PMC) for both projects, run the command `Install-Package Entityframework`.</span></span>

* <span data-ttu-id="338c0-144">Erstellen Sie Datenmodellklassen, eine Kontextklasse und eine Implementierung von `IDbContextFactory` im Klassenbibliotheksprojekt.</span><span class="sxs-lookup"><span data-stu-id="338c0-144">In the class library project, create data model classes and a context class, and an implementation of `IDbContextFactory`.</span></span>

* <span data-ttu-id="338c0-145">Führen Sie in der Paket-Manager-Konsole des Klassenbibliotheksprojekts die Befehle `Enable-Migrations` und `Add-Migration Initial` aus.</span><span class="sxs-lookup"><span data-stu-id="338c0-145">In PMC for the class library project, run the commands `Enable-Migrations` and `Add-Migration Initial`.</span></span> <span data-ttu-id="338c0-146">Wenn Sie das ASP.NET Core-Projekt als Startprojekt festgelegt haben, fügen Sie `-StartupProjectName EF6` zu diesen Befehlen hinzu.</span><span class="sxs-lookup"><span data-stu-id="338c0-146">If you have set the ASP.NET Core project as the startup project, add `-StartupProjectName EF6` to these commands.</span></span>

* <span data-ttu-id="338c0-147">Fügen Sie im Core-Projekt einen Projektverweis zum Klassenbibliotheksprojekt hinzu.</span><span class="sxs-lookup"><span data-stu-id="338c0-147">In the Core project, add a project reference to the class library project.</span></span>

* <span data-ttu-id="338c0-148">Registrieren Sie im Core-Projekt in der Datei *Startup.cs* den Kontext für DI.</span><span class="sxs-lookup"><span data-stu-id="338c0-148">In the Core project, in *Startup.cs*, register the context for DI.</span></span>

* <span data-ttu-id="338c0-149">Fügen Sie im Core-Projekt in der Datei *appsettings.json* die Verbindungszeichenfolge hinzu.</span><span class="sxs-lookup"><span data-stu-id="338c0-149">In the Core project, in *appsettings.json*, add the connection string.</span></span>

* <span data-ttu-id="338c0-150">Fügen Sie im Core-Projekt einen Controller und Ansichten hinzu, um zu überprüfen, dass Daten gelesen und geschrieben werden können.</span><span class="sxs-lookup"><span data-stu-id="338c0-150">In the Core project, add a controller and view(s) to verify that you can read and write data.</span></span> <span data-ttu-id="338c0-151">(Beachten Sie, dass der ASP.NET Core MVC-Gerüstbau nicht mit dem Entity Framework 6-Kontext funktioniert, auf den durch die Klassenbibliothek verwiesen wird.)</span><span class="sxs-lookup"><span data-stu-id="338c0-151">(Note that ASP.NET Core MVC scaffolding won't work with the EF6 context referenced from the class library.)</span></span>

## <a name="summary"></a><span data-ttu-id="338c0-152">Zusammenfassung</span><span class="sxs-lookup"><span data-stu-id="338c0-152">Summary</span></span>

<span data-ttu-id="338c0-153">In diesem Artikel wurde die Verwendung von Entity Framework 6 in einer ASP.NET Core-Anwendung grundlegend erläutert.</span><span class="sxs-lookup"><span data-stu-id="338c0-153">This article has provided basic guidance for using Entity Framework 6 in an ASP.NET Core application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="338c0-154">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="338c0-154">Additional resources</span></span>

* [<span data-ttu-id="338c0-155">Entity Framework – Code-Based Configuration (Codebasierte Konfiguration von Entity Framework)</span><span class="sxs-lookup"><span data-stu-id="338c0-155">Entity Framework - Code-Based Configuration</span></span>](https://msdn.microsoft.com/data/jj680699.aspx)
