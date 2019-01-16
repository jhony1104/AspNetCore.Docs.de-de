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
<a name="getting-started-with-aspnet-45-web-forms-and-visual-studio-2017"></a><span data-ttu-id="c507d-103">Erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="c507d-103">Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2017</span></span>
====================

<span data-ttu-id="c507d-104">[Herunterladen der Wingtip Toys-Beispielprojekts (c#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) oder [E-Book (PDF) herunterladen](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span><span class="sxs-lookup"><span data-stu-id="c507d-104">[Download Wingtip Toys Sample Project (C#)](http://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) or [Download E-book (PDF)](http://download.microsoft.com/download/0/F/B/0FBFAA46-2BFD-478F-8E56-7BF3C672DF9D/Getting%20Started%20with%20ASP.NET%204.5%20Web%20Forms%20and%20Visual%20Studio%202013.pdf)</span></span>

<span data-ttu-id="c507d-105">Dieser tutorialreihe erfahren Sie, wie Sie eine ASP.NET Web Forms-Anwendung mit ASP.NET 4.5 und Microsoft Visual Studio 2017 erstellen.</span><span class="sxs-lookup"><span data-stu-id="c507d-105">This tutorial series shows you how to build an ASP.NET Web Forms application with ASP.NET 4.5 and Microsoft Visual Studio 2017.</span></span> 

## <a name="introduction"></a><span data-ttu-id="c507d-106">Einführung</span><span class="sxs-lookup"><span data-stu-id="c507d-106">Introduction</span></span>

<span data-ttu-id="c507d-107">Diese lernprogrammreihe führt Sie durch Erstellen einer ASP.NET Web Forms-Anwendung, die mithilfe von Visual Studio 2017 und ASP.NET 4.5.</span><span class="sxs-lookup"><span data-stu-id="c507d-107">This tutorial series guides you through creating an ASP.NET Web Forms application using Visual Studio 2017 and ASP.NET 4.5.</span></span> <span data-ttu-id="c507d-108">Erstellen Sie eine Anwendung namens **Wingtip Toys** – eine vereinfachte verkauften Artikel online Storefront-Website.</span><span class="sxs-lookup"><span data-stu-id="c507d-108">You'll create an application named **Wingtip Toys** - a simplified storefront web site selling items online.</span></span> <span data-ttu-id="c507d-109">Während der Reihe werden die neuen Features von ASP.NET 4.5 hervorgehoben.</span><span class="sxs-lookup"><span data-stu-id="c507d-109">During the series, new ASP.NET 4.5 features are highlighted.</span></span>

### <a name="target-audience"></a><span data-ttu-id="c507d-110">Zielgruppe</span><span class="sxs-lookup"><span data-stu-id="c507d-110">Target audience</span></span>

<span data-ttu-id="c507d-111">Entwickler, die noch nicht mit ASP.NET Web Forms sind die Zielgruppe für diese tutorialreihe.</span><span class="sxs-lookup"><span data-stu-id="c507d-111">Developers new to ASP.NET Web Forms are the target audience for this tutorial series.</span></span>

<span data-ttu-id="c507d-112">Sie benötigen Grundkenntnisse in den folgenden Bereichen:</span><span class="sxs-lookup"><span data-stu-id="c507d-112">You should have some knowledge in the following areas:</span></span>

- <span data-ttu-id="c507d-113">Objektorientierte Programmierung (OOP) und Sprachen</span><span class="sxs-lookup"><span data-stu-id="c507d-113">Object-oriented programming (OOP) and languages</span></span>
- <span data-ttu-id="c507d-114">Webentwicklung (HTML, CSS, JavaScript)</span><span class="sxs-lookup"><span data-stu-id="c507d-114">Web development (HTML, CSS, JavaScript)</span></span>
- <span data-ttu-id="c507d-115">relationale Datenbanken</span><span class="sxs-lookup"><span data-stu-id="c507d-115">Relational databases</span></span>
- <span data-ttu-id="c507d-116">Architektur mit N Ebenen</span><span class="sxs-lookup"><span data-stu-id="c507d-116">N-tier architecture</span></span>

<span data-ttu-id="c507d-117">Um diese Bereiche zu überprüfen, sollten Sie in der Untersuchung von des folgenden Inhalts:</span><span class="sxs-lookup"><span data-stu-id="c507d-117">To review these areas, consider studying the following content:</span></span>

- [<span data-ttu-id="c507d-118">Erste Schritte mit Visual c#</span><span class="sxs-lookup"><span data-stu-id="c507d-118">Getting Started with Visual C#</span></span>](https://msdn.microsoft.com/library/a72418yk.aspx)
- <span data-ttu-id="c507d-119">[Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)</span><span class="sxs-lookup"><span data-stu-id="c507d-119">[Web Development](https://msdn.microsoft.com/beginner/bb308760.aspx), [HTML, CSS, JavaScript, SQL, PHP, JQuery](http://w3schools.com/)</span></span>
- [<span data-ttu-id="c507d-120">Relationale Datenbank</span><span class="sxs-lookup"><span data-stu-id="c507d-120">Relational database</span></span>](http://en.wikipedia.org/wiki/Relational_database)
- [<span data-ttu-id="c507d-121">Multi-Tier-Architektur</span><span class="sxs-lookup"><span data-stu-id="c507d-121">Multitier architecture</span></span>](http://en.wikipedia.org/wiki/Multitier_architecture)

### <a name="application-features"></a><span data-ttu-id="c507d-122">Anwendungsfeatures</span><span class="sxs-lookup"><span data-stu-id="c507d-122">Application features</span></span>

<span data-ttu-id="c507d-123">Die ASP.NET Web Form-Features, die in dieser Serie vorgestellten umfassen:</span><span class="sxs-lookup"><span data-stu-id="c507d-123">The ASP.NET Web Form features presented in this series include:</span></span>

- <span data-ttu-id="c507d-124">Das Webanwendungsprojekt (keine Website-Projekt)</span><span class="sxs-lookup"><span data-stu-id="c507d-124">The Web Application Project (not Web Site Project)</span></span>
- <span data-ttu-id="c507d-125">Web Forms</span><span class="sxs-lookup"><span data-stu-id="c507d-125">Web Forms</span></span>
- <span data-ttu-id="c507d-126">Masterseiten, Konfiguration</span><span class="sxs-lookup"><span data-stu-id="c507d-126">Master Pages, Configuration</span></span>
- <span data-ttu-id="c507d-127">Bootstrap-Stil</span><span class="sxs-lookup"><span data-stu-id="c507d-127">Bootstrap</span></span>
- <span data-ttu-id="c507d-128">Entitätsframework Code First, LocalDB</span><span class="sxs-lookup"><span data-stu-id="c507d-128">Entity Framework Code First, LocalDB</span></span>
- <span data-ttu-id="c507d-129">Request-Überprüfung</span><span class="sxs-lookup"><span data-stu-id="c507d-129">Request Validation</span></span>
- <span data-ttu-id="c507d-130">Stark typisierte Datensteuerelemente</span><span class="sxs-lookup"><span data-stu-id="c507d-130">Strongly-typed Data Controls</span></span>
- <span data-ttu-id="c507d-131">Modellbindung</span><span class="sxs-lookup"><span data-stu-id="c507d-131">Model Binding</span></span>
- <span data-ttu-id="c507d-132">Datenanmerkungen</span><span class="sxs-lookup"><span data-stu-id="c507d-132">Data Annotations</span></span>
- <span data-ttu-id="c507d-133">Wertanbieter</span><span class="sxs-lookup"><span data-stu-id="c507d-133">Value Providers</span></span>
- <span data-ttu-id="c507d-134">SSL- und OAuth</span><span class="sxs-lookup"><span data-stu-id="c507d-134">SSL and OAuth</span></span>
- <span data-ttu-id="c507d-135">ASP.NET Identity, Konfiguration und -Autorisierung</span><span class="sxs-lookup"><span data-stu-id="c507d-135">ASP.NET Identity, Configuration, and Authorization</span></span>
- <span data-ttu-id="c507d-136">Unaufdringliche Validierung</span><span class="sxs-lookup"><span data-stu-id="c507d-136">Unobtrusive Validation</span></span>
- <span data-ttu-id="c507d-137">Routing</span><span class="sxs-lookup"><span data-stu-id="c507d-137">Routing</span></span>
- <span data-ttu-id="c507d-138">ASP.NET – Fehlerbehandlung</span><span class="sxs-lookup"><span data-stu-id="c507d-138">ASP.NET Error Handling</span></span>

### <a name="application-scenarios-and-tasks"></a><span data-ttu-id="c507d-139">Anwendungsszenarien und Aufgaben</span><span class="sxs-lookup"><span data-stu-id="c507d-139">Application scenarios and tasks</span></span>

<span data-ttu-id="c507d-140">Tutorial-Reihe von Aufgaben:</span><span class="sxs-lookup"><span data-stu-id="c507d-140">Tutorial series tasks include:</span></span>

- <span data-ttu-id="c507d-141">Erstellen, überprüfen und Ausführen eines neuen Projekts</span><span class="sxs-lookup"><span data-stu-id="c507d-141">Creating, reviewing, and running a new project</span></span>
- <span data-ttu-id="c507d-142">Erstellen einer Datenbankstruktur</span><span class="sxs-lookup"><span data-stu-id="c507d-142">Creating a database structure</span></span>
- <span data-ttu-id="c507d-143">Initialisieren und Ausführen eines Seedings einer Datenbank</span><span class="sxs-lookup"><span data-stu-id="c507d-143">Initializing and seeding a database</span></span>
- <span data-ttu-id="c507d-144">Anpassen der Benutzeroberfläche mit Stilen, Grafiken und eine Masterseite</span><span class="sxs-lookup"><span data-stu-id="c507d-144">Customizing the UI with styles, graphics, and a master page</span></span>
- <span data-ttu-id="c507d-145">Hinzufügen von Seiten und navigation</span><span class="sxs-lookup"><span data-stu-id="c507d-145">Adding pages and navigation</span></span>
- <span data-ttu-id="c507d-146">Anzeigen von Menü-Details und Produktdaten</span><span class="sxs-lookup"><span data-stu-id="c507d-146">Displaying menu details and product data</span></span>
- <span data-ttu-id="c507d-147">Erstellen einen Einkaufswagen gelegt hat</span><span class="sxs-lookup"><span data-stu-id="c507d-147">Creating a shopping cart</span></span>
- <span data-ttu-id="c507d-148">Hinzufügen von SSL und OAuth-Unterstützung</span><span class="sxs-lookup"><span data-stu-id="c507d-148">Adding SSL and OAuth support</span></span>
- <span data-ttu-id="c507d-149">Eine Zahlungsmethode hinzufügen</span><span class="sxs-lookup"><span data-stu-id="c507d-149">Adding a payment method</span></span>
- <span data-ttu-id="c507d-150">Einschließlich der Rolle Administrator angehören und ein Benutzer der Anwendung</span><span class="sxs-lookup"><span data-stu-id="c507d-150">Including an administrator role and a user to the application</span></span>
- <span data-ttu-id="c507d-151">Einschränken des Zugriffs auf bestimmte Seiten und Ordner</span><span class="sxs-lookup"><span data-stu-id="c507d-151">Restricting access to specific pages and folder</span></span>
- <span data-ttu-id="c507d-152">Hochladen einer Datei in der Webanwendung</span><span class="sxs-lookup"><span data-stu-id="c507d-152">Uploading a file to the web application</span></span>
- <span data-ttu-id="c507d-153">Implementieren der eingabeüberprüfung</span><span class="sxs-lookup"><span data-stu-id="c507d-153">Implementing input validation</span></span>
- <span data-ttu-id="c507d-154">Registrieren von Routen für die Webanwendung</span><span class="sxs-lookup"><span data-stu-id="c507d-154">Registering routes for the web application</span></span>
- <span data-ttu-id="c507d-155">Implementieren der Fehlerbehandlung und Protokollierung von Anzeigefehlern</span><span class="sxs-lookup"><span data-stu-id="c507d-155">Implementing error handling and error logging</span></span>

## <a name="overview"></a><span data-ttu-id="c507d-156">Übersicht</span><span class="sxs-lookup"><span data-stu-id="c507d-156">Overview</span></span>

<span data-ttu-id="c507d-157">Dieser tutorialreihe ist die Linie für eine Person mit Programmierkonzepten vertraut, aber noch nicht mit ASP.NET Web Forms.</span><span class="sxs-lookup"><span data-stu-id="c507d-157">This tutorial series is intended for someone familiar with programming concepts, but new to ASP.NET Web Forms.</span></span> <span data-ttu-id="c507d-158">Wenn Sie bereits mit ASP.NET Web Forms vertraut sind, können diese weiterhin neue ASP.NET 4.5-Funktionen Informationen helfen.</span><span class="sxs-lookup"><span data-stu-id="c507d-158">If you're already familiar with ASP.NET Web Forms, this series can still help you learn about new ASP.NET 4.5 features.</span></span> <span data-ttu-id="c507d-159">Leser, die nicht mit den Konzepten und ASP.NET Web Forms-Programmierung finden Sie unter der weitere Web Forms-Tutorials zur Verfügung gestellt, der [Einstieg](../../../index.md) Abschnitt auf der ASP.NET-Website.</span><span class="sxs-lookup"><span data-stu-id="c507d-159">For readers unfamiliar with programming concepts and ASP.NET Web Forms, see the additional Web Forms tutorials provided in the [Getting Started](../../../index.md) section on the ASP.NET Web site.</span></span>

<span data-ttu-id="c507d-160">Die ASP.NET 4.5 bereitgestellt, die in dieser tutorialreihe umfasst die folgenden Funktionen:</span><span class="sxs-lookup"><span data-stu-id="c507d-160">The ASP.NET 4.5 provided in this tutorial series includes the following features:</span></span>

- <span data-ttu-id="c507d-161">Eine einfache Benutzeroberfläche zum Erstellen von Projekten, die bietet [Unterstützung für viele ASP.NET Frameworks](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC und Web-API).</span><span class="sxs-lookup"><span data-stu-id="c507d-161">A simple UI for creating projects that offers [support for many ASP.NET frameworks](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#add) (Web Forms, MVC, and Web API).</span></span>
- <span data-ttu-id="c507d-162">[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), Layout, Designs und Framework für reaktionsfähiges Design.</span><span class="sxs-lookup"><span data-stu-id="c507d-162">[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap), a layout, theming, and responsive design framework.</span></span>
- <span data-ttu-id="c507d-163">[ASP.NET Identity](../../../../identity/index.md), ein neues ASP.NET-Mitgliedschaftssystem, die in allen ASP.NET-Framework-Versionen und funktioniert mit Web-Software als IIS-hosting funktioniert.</span><span class="sxs-lookup"><span data-stu-id="c507d-163">[ASP.NET Identity](../../../../identity/index.md), a new ASP.NET membership system that works the same in all ASP.NET frameworks and works with web hosting software other than IIS.</span></span>
- [<span data-ttu-id="c507d-164">Entity Framework 6</span><span class="sxs-lookup"><span data-stu-id="c507d-164">Entity Framework 6</span></span>](https://msdn.microsoft.com/data/ef.aspx)

  <span data-ttu-id="c507d-165">Ein Update zu Entity Framework können Sie:</span><span class="sxs-lookup"><span data-stu-id="c507d-165">An update to the Entity Framework enabling you to:</span></span>
  - <span data-ttu-id="c507d-166">Abrufen und Bearbeiten von Daten als stark typisierte Objekte</span><span class="sxs-lookup"><span data-stu-id="c507d-166">Retrieve and manipulate data as strongly-typed objects</span></span>
  - <span data-ttu-id="c507d-167">Zugriff auf Daten asynchron</span><span class="sxs-lookup"><span data-stu-id="c507d-167">Access data asynchronously</span></span>
  - <span data-ttu-id="c507d-168">Behandeln von vorübergehenden Verbindungsfehlern</span><span class="sxs-lookup"><span data-stu-id="c507d-168">Handle transient connection faults</span></span>
  - <span data-ttu-id="c507d-169">Log-SQL-Anweisungen</span><span class="sxs-lookup"><span data-stu-id="c507d-169">Log SQL statements</span></span>

<span data-ttu-id="c507d-170">Eine vollständige Liste der ASP.NET 4.5-Funktionen, finden Sie unter [ASP.NET and Web Tools für Visual Studio 2013 Release Notes](../../../../visual-studio/overview/2013/release-notes.md).</span><span class="sxs-lookup"><span data-stu-id="c507d-170">For a complete ASP.NET 4.5 feature list, see [ASP.NET and Web Tools for Visual Studio 2013 Release Notes](../../../../visual-studio/overview/2013/release-notes.md).</span></span>

### <a name="the-wingtip-toys-sample-application"></a><span data-ttu-id="c507d-171">Die Wingtip Toys-beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="c507d-171">The Wingtip Toys sample application</span></span>

<span data-ttu-id="c507d-172">Die folgenden Screenshots sind von der ASP.NET Web Forms-Anwendung, die Sie in diesem Tutorial erstellen.</span><span class="sxs-lookup"><span data-stu-id="c507d-172">The following screenshots are from the ASP.NET Web Forms application that you create in this tutorial series.</span></span> <span data-ttu-id="c507d-173">Wenn Sie die Anwendung in Visual Studio ausführen, wird die folgende Webseite-Startseite angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c507d-173">When you run the application in Visual Studio, the following web Home page appears.</span></span>

![Wingtip Toys - Standardseite](introduction-and-overview/_static/image1.png)

<span data-ttu-id="c507d-175">Sie können als neuer Benutzer registrieren, oder melden Sie sich als einen vorhandenen Benutzer.</span><span class="sxs-lookup"><span data-stu-id="c507d-175">You can register as a new user, or sign in as an existing user.</span></span> <span data-ttu-id="c507d-176">Der oberen Navigationsleiste verfügt über Links zu Produktkategorien und ihre Produkte aus der Datenbank.</span><span class="sxs-lookup"><span data-stu-id="c507d-176">The top navigation has links to product categories and their products from the database.</span></span>

<span data-ttu-id="c507d-177">Bei Auswahl von **Produkte**, alle verfügbaren Produkte werden angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c507d-177">If you select **Products**, all available products are displayed.</span></span> 

![Wingtip Toys - Produkte](introduction-and-overview/_static/image2.png)

<span data-ttu-id="c507d-179">Wenn Sie ein bestimmtes Produkt auswählen, werden die Produktdetails angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c507d-179">If you select a specific product, product details are displayed.</span></span>


![Wingtip Toys - Produktinformationen](introduction-and-overview/_static/image3.png)

<span data-ttu-id="c507d-181">Sie können als Benutzer registrieren und melden Sie sich mit Standardfunktionen für Web Forms-Vorlage.</span><span class="sxs-lookup"><span data-stu-id="c507d-181">As a user, you can register and sign in with Web Forms template default functionality.</span></span> <span data-ttu-id="c507d-182">In diesem Tutorial wird die Verwendung mit einem vorhandenen Gmail-Konto anmelden.</span><span class="sxs-lookup"><span data-stu-id="c507d-182">This tutorial also explains how to sign in using an existing Gmail account.</span></span> <span data-ttu-id="c507d-183">Darüber hinaus können Sie als Administrator hinzufügen und Entfernen von Produkten aus der Datenbank anmelden.</span><span class="sxs-lookup"><span data-stu-id="c507d-183">Additionally, you can sign in as the administrator to add and remove products from the database.</span></span>

![Wingtip Toys - Anmeldung](introduction-and-overview/_static/image4.png)

<span data-ttu-id="c507d-185">Nachdem Sie sich als Benutzer angemeldet haben, können Sie Produkte auf den Einkaufswagen und Diskussion zum Bezahlvorgang mit PayPal hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="c507d-185">Once you've signed in as a user, you can add products to the shopping cart and checkout with PayPal.</span></span> <span data-ttu-id="c507d-186">Die beispielanwendung dient, in PayPals-Entwickler-Sandbox arbeiten.</span><span class="sxs-lookup"><span data-stu-id="c507d-186">The sample application is designed to work in PayPal's developer sandbox.</span></span> <span data-ttu-id="c507d-187">Keine Transaktion tatsächlich Zahlung erfolgt.</span><span class="sxs-lookup"><span data-stu-id="c507d-187">No actual money transaction takes place.</span></span>

![Wingtip Toys - Warenkorb](introduction-and-overview/_static/image5.png)

<span data-ttu-id="c507d-189">PayPal bestätigt Ihre Informationen zu Konto, Reihenfolge und Zahlung.</span><span class="sxs-lookup"><span data-stu-id="c507d-189">PayPal confirms your account, order, and payment information.</span></span>

![Wingtip Toys - PayPal](introduction-and-overview/_static/image6.png)

<span data-ttu-id="c507d-191">Sie können nach der Rückkehr aus PayPal, überprüfen und Abschließen der Bestellung.</span><span class="sxs-lookup"><span data-stu-id="c507d-191">After returning from PayPal, you can review and complete your order.</span></span>

![Wingtip Toys - Order-Überprüfung](introduction-and-overview/_static/image7.png)

## <a name="prerequisites"></a><span data-ttu-id="c507d-193">Vorraussetzungen</span><span class="sxs-lookup"><span data-stu-id="c507d-193">Prerequisites</span></span>

<span data-ttu-id="c507d-194">Bevor Sie beginnen, stellen Sie sicher, dass die folgende Software auf Ihrem Computer installiert ist:</span><span class="sxs-lookup"><span data-stu-id="c507d-194">Before you start, make sure the following software is installed on your computer:</span></span>

- <span data-ttu-id="c507d-195">[Microsoft Visual Studio 2017 "oder" Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).</span><span class="sxs-lookup"><span data-stu-id="c507d-195">[Microsoft Visual Studio 2017 or Microsoft Visual Studio Community 2017](https://visualstudio.microsoft.com/downloads/).</span></span>

<span data-ttu-id="c507d-196">.NET Framework wird automatisch installiert.</span><span class="sxs-lookup"><span data-stu-id="c507d-196">The .NET Framework is installed automatically.</span></span>

<span data-ttu-id="c507d-197">Dieser tutorialreihe wird Microsoft Visual Studio Community 2017 verwendet.</span><span class="sxs-lookup"><span data-stu-id="c507d-197">This tutorial series uses Microsoft Visual Studio Community 2017.</span></span> <span data-ttu-id="c507d-198">Sie können entweder, dass oder Microsoft Visual Studio 2017 zum Abschließen dieser tutorialreihe.</span><span class="sxs-lookup"><span data-stu-id="c507d-198">You can use either that or Microsoft Visual Studio 2017 to complete this tutorial series.</span></span>

<span data-ttu-id="c507d-199">Beachten Sie Folgendes im Zusammenhang mit Visual Studio:</span><span class="sxs-lookup"><span data-stu-id="c507d-199">Note the following about Visual Studio:</span></span>

* <span data-ttu-id="c507d-200">Microsoft Visual Studio 2017 und Microsoft Visual Studio Community 2017 als bezeichnet *Visual Studio* in dieser tutorialreihe.</span><span class="sxs-lookup"><span data-stu-id="c507d-200">Microsoft Visual Studio 2017 and Microsoft Visual Studio Community 2017 are referred to as *Visual Studio* throughout this tutorial series.</span></span>

* <span data-ttu-id="c507d-201">Visual Studio 2017 installiert ist neben älteren Versionen bereits installiert.</span><span class="sxs-lookup"><span data-stu-id="c507d-201">Visual Studio 2017 is installed next to any older versions already installed.</span></span> <span data-ttu-id="c507d-202">In früheren Versionen erstellten Websites können in Visual Studio 2017 geöffnet werden und weiterhin in früheren Versionen öffnen.</span><span class="sxs-lookup"><span data-stu-id="c507d-202">Sites created in earlier versions can be opened in Visual Studio 2017 and continue to open in previous versions.</span></span>

* <span data-ttu-id="c507d-203">Zum ersten Mal Sie Visual Studio gestartet, es wird davon ausgegangen, dass Sie ausgewählt haben die *Webentwicklung* Einstellungen.</span><span class="sxs-lookup"><span data-stu-id="c507d-203">The first time you started Visual Studio, it is assumed you selected the *Web Development* settings.</span></span> <span data-ttu-id="c507d-204">Weitere Informationen finden Sie unter [Vorgehensweise: Wählen Sie die Umgebungseinstellungen für die Webentwicklung](https://msdn.microsoft.com/library/ff521558.aspx).</span><span class="sxs-lookup"><span data-stu-id="c507d-204">For more information, see [How to: Select Web Development Environment Settings](https://msdn.microsoft.com/library/ff521558.aspx).</span></span>

<span data-ttu-id="c507d-205">Nach der Installation der erforderlichen Komponenten können Sie damit beginnen, erstellen das Webprojekt, das in dieser tutorialreihe angezeigt.</span><span class="sxs-lookup"><span data-stu-id="c507d-205">After installing the prerequisites, you're ready to begin creating the Web project presented in this tutorial series.</span></span>

## <a name="download-the-sample-application"></a><span data-ttu-id="c507d-206">Herunterladen der beispielanwendung</span><span class="sxs-lookup"><span data-stu-id="c507d-206">Download the sample application</span></span>

 <span data-ttu-id="c507d-207">Sie können das vollständige Beispiel Applicatiion an jederzeit und überall auf der MSDN Samples-Website herunterladen:</span><span class="sxs-lookup"><span data-stu-id="c507d-207">You can download the completed sample applicatiion at anytime from the MSDN Samples site:</span></span>

<span data-ttu-id="c507d-208">[Erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (c#)</span><span class="sxs-lookup"><span data-stu-id="c507d-208">[Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#)</span></span> 

 <span data-ttu-id="c507d-209">Dieser Download umfasst die folgenden Elemente:</span><span class="sxs-lookup"><span data-stu-id="c507d-209">This download has the following items:</span></span>

- <span data-ttu-id="c507d-210">Die beispielanwendung in der *WingtipToys* Ordner.</span><span class="sxs-lookup"><span data-stu-id="c507d-210">The sample application in the *WingtipToys* folder.</span></span>
- <span data-ttu-id="c507d-211">Die Ressourcen, die zum Erstellen der beispielanwendung in der *WingtipToys-Assets* Ordner in der *WingtipToys* Ordner.</span><span class="sxs-lookup"><span data-stu-id="c507d-211">The resources used to create the sample application in the *WingtipToys-Assets* folder in the *WingtipToys* folder.</span></span>

<span data-ttu-id="c507d-212">Der Download ist eine *ZIP* Datei.</span><span class="sxs-lookup"><span data-stu-id="c507d-212">The download is a *.zip* file.</span></span> <span data-ttu-id="c507d-213">Anzeigen des abgeschlossenen Projekts, das dieser tutorialreihe erstellt suchen und Auswählen der *C#* Ordner in der ZIP-Datei.</span><span class="sxs-lookup"><span data-stu-id="c507d-213">To see the completed project that this tutorial series creates, find and select the *C#* folder in the .zip file.</span></span> <span data-ttu-id="c507d-214">Speichern Sie die C# Ordner in den Ordner, die Sie verwenden, um mit Visual Studio-Projekten zu arbeiten.</span><span class="sxs-lookup"><span data-stu-id="c507d-214">Save the C# folder to the folder you use to work with Visual Studio projects.</span></span> <span data-ttu-id="c507d-215">Standardmäßig ist der Ordner von Visual Studio 2017-Projekte:</span><span class="sxs-lookup"><span data-stu-id="c507d-215">By default, the Visual Studio 2017 projects folder is:</span></span>

<span data-ttu-id="c507d-216"><strong>C:\Users&#92;</strong><strong><em>&lt;Benutzername&gt;</em></strong><strong>\source\repos</strong></span><span class="sxs-lookup"><span data-stu-id="c507d-216"><strong>C:\Users&#92;</strong><strong><em>&lt;username&gt;</em></strong><strong>\source\repos</strong></span></span>

<span data-ttu-id="c507d-217">Benennen Sie die ***c#*** Ordner ***WingtipToys***.</span><span class="sxs-lookup"><span data-stu-id="c507d-217">Rename the ***C#*** folder to ***WingtipToys***.</span></span>

> [!NOTE]
> <span data-ttu-id="c507d-218">Wenn Sie bereits einen Ordner namens haben *WingtipToys* im Projektordner, bevor Sie Sie umbenennen, vorhandenen Ordner vorübergehend Umbenennen der *c#* Ordner *WingtipToys*.</span><span class="sxs-lookup"><span data-stu-id="c507d-218">If you already have a folder named *WingtipToys* in your Projects folder, temporarily rename that existing folder before renaming the *C#* folder to *WingtipToys*.</span></span>

<span data-ttu-id="c507d-219">Öffnen Sie zum Ausführen des abgeschlossenen Projekts der *WingtipToys* Ordner und doppelklicken Sie auf die *WingtipToys.sln* Datei.</span><span class="sxs-lookup"><span data-stu-id="c507d-219">To run the completed project, open the *WingtipToys* folder and double-click the *WingtipToys.sln* file.</span></span> <span data-ttu-id="c507d-220">Visual Studio 2017 wird das Projekt geöffnet.</span><span class="sxs-lookup"><span data-stu-id="c507d-220">Visual Studio 2017 opens the project.</span></span> <span data-ttu-id="c507d-221">Als Nächstes mit der rechten Maustaste die *"default.aspx"* Datei **Projektmappen-Explorer** , und wählen Sie **In Browser anzeigen**.</span><span class="sxs-lookup"><span data-stu-id="c507d-221">Next, right-click the *Default.aspx* file in **Solution Explorer** and select **View In Browser**.</span></span>

## <a name="take-a-aspnet-web-forms-quiz-to-review-content"></a><span data-ttu-id="c507d-222">Machen Sie ein ASP.NET Web Forms-Quiz, Inhalte zu überprüfen.</span><span class="sxs-lookup"><span data-stu-id="c507d-222">Take a ASP.NET Web Forms quiz to review content</span></span>

<span data-ttu-id="c507d-223">Nach Abschluss der tutorialreihe können ein Quiz können Sie Ihr Wissen testen und wichtige Konzepte zu verstärken.</span><span class="sxs-lookup"><span data-stu-id="c507d-223">After completing the tutorial series, take a quiz to test your knowledge and reinforce key concepts.</span></span> <span data-ttu-id="c507d-224">Jeder Frage enthält eine Erläuterung und Links zu weiteren Anleitungen.</span><span class="sxs-lookup"><span data-stu-id="c507d-224">Each question provides an explanation and links to additional guidance.</span></span>

 * [<span data-ttu-id="c507d-225">ASP.NET Web Forms-Quiz</span><span class="sxs-lookup"><span data-stu-id="c507d-225">ASP.NET Web Forms Quiz</span></span>](https://blogs.msdn.microsoft.com/erikreitan/2016/01/08/asp-net-web-forms-quiz/) 

## <a name="tutorial-support-and-comments"></a><span data-ttu-id="c507d-226">Tutorial Support und Kommentare</span><span class="sxs-lookup"><span data-stu-id="c507d-226">Tutorial support and comments</span></span>

<span data-ttu-id="c507d-227">Für Fragen und Kommentare, verwenden Sie den f und A-Abschnitt enthalten die [erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) Beispielseite.</span><span class="sxs-lookup"><span data-stu-id="c507d-227">For questions and comments, use the Q and A section included on the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span>

<span data-ttu-id="c507d-228">Kommentare zu dieser tutorialreihe sind Willkommen.</span><span class="sxs-lookup"><span data-stu-id="c507d-228">Comments on this tutorial series are welcome.</span></span> <span data-ttu-id="c507d-229">Wenn dieser tutorialreihe aktualisiert wird, wird versucht, Korrekturen oder Vorschläge für Verbesserungen zu berücksichtigen.</span><span class="sxs-lookup"><span data-stu-id="c507d-229">When this tutorial series is updated, every effort is made to consider corrections or suggestions for improvements.</span></span>

<span data-ttu-id="c507d-230">Wenn ein Fehler auftritt, die entsprechende Fehlermeldungen können verwirrend sein, ohne gute Erklärung dazu, wie Sie diesen Fehler beheben.</span><span class="sxs-lookup"><span data-stu-id="c507d-230">If an error occurs, the corresponding error messages could be confusing, with no good explanation on how to fix it.</span></span> <span data-ttu-id="c507d-231">Um Hilfe zu erhalten, sehen Sie sich die [ASP.NET-Foren](https://forums.asp.net/).</span><span class="sxs-lookup"><span data-stu-id="c507d-231">For help, you can check the [ASP.NET forums](https://forums.asp.net/).</span></span> <span data-ttu-id="c507d-232">Eine weitere gute Quelle ist der f und A-Abschnitt in der [erste Schritte mit ASP.NET 4.5 Web Forms und Visual Studio 2013 – Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) Beispielseite.</span><span class="sxs-lookup"><span data-stu-id="c507d-232">Another good source is the Q and A section in the [Getting Started with ASP.NET 4.5 Web Forms and Visual Studio 2013 - Wingtip Toys](https://go.microsoft.com/fwlink/?LinkID=389434&clcid=0x409) (C#) sample page.</span></span> 

> [!div class="step-by-step"]
> [<span data-ttu-id="c507d-233">Nächste</span><span class="sxs-lookup"><span data-stu-id="c507d-233">Next</span></span>](create-the-project.md)
