---
title: Dependency Injection in Controller in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core MVC-Controller Abhängigkeiten mit Dependency Injection in ASP.NET Core explizit über Konstruktoren anfordern.
ms.author: riande
ms.date: 02/24/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 67861b2cdb946f7cd630770507181ee853b7fbfd
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85408421"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="fa7ee-103">Dependency Injection in Controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fa7ee-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="fa7ee-104">Von [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT) und [Steve Smith](https://github.com/ardalis)</span><span class="sxs-lookup"><span data-stu-id="fa7ee-104">By [Shadi Namrouti](https://github.com/shadinamrouti), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Steve Smith](https://github.com/ardalis)</span></span>

<span data-ttu-id="fa7ee-105">ASP.NET Core MVC-Controller fordern Abhängigkeiten explizit über Konstruktoren an.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-105">ASP.NET Core MVC controllers request dependencies explicitly via constructors.</span></span> <span data-ttu-id="fa7ee-106">ASP.NET Core verfügt über integrierte Unterstützung für [Dependency Injection ( DI)](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="fa7ee-106">ASP.NET Core has built-in support for [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="fa7ee-107">DI erleichtert das Testen und die Wartung von Apps.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-107">DI makes apps easier to test and maintain.</span></span>

<span data-ttu-id="fa7ee-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fa7ee-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="fa7ee-109">Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="fa7ee-109">Constructor Injection</span></span>

<span data-ttu-id="fa7ee-110">Dienste werden als Konstruktorparameter hinzugefügt, und die Runtime löst den Dienst aus dem Dienstcontainer auf.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-110">Services are added as a constructor parameter, and the runtime resolves the service from the service container.</span></span> <span data-ttu-id="fa7ee-111">Dienste werden meist mithilfe von Schnittstellen definiert.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-111">Services are typically defined using interfaces.</span></span> <span data-ttu-id="fa7ee-112">Stellen Sich sich z.B. eine App vor, für die die aktuelle Uhrzeit erforderlich ist.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-112">For example, consider an app that requires the current time.</span></span> <span data-ttu-id="fa7ee-113">Die folgende Schnittstelle macht den `IDateTime`-Dienst verfügbar:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-113">The following interface exposes the `IDateTime` service:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Interfaces/IDateTime.cs?name=snippet)]

<span data-ttu-id="fa7ee-114">Im folgenden Code wird die `IDateTime`-Schnittstelle implementiert:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-114">The following code implements the `IDateTime` interface:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Services/SystemDateTime.cs?name=snippet)]

<span data-ttu-id="fa7ee-115">Fügen Sie anschließend dem Dienstcontainer den Dienst hinzu:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-115">Add the service to the service container:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup1.cs?name=snippet&highlight=3)]

<span data-ttu-id="fa7ee-116">Weitere Informationen zu <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*> finden Sie unter [Dependency Injection in ASP.NET Core](xref:fundamentals/dependency-injection#service-lifetimes).</span><span class="sxs-lookup"><span data-stu-id="fa7ee-116">For more information on <xref:Microsoft.Extensions.DependencyInjection.ServiceCollectionServiceExtensions.AddSingleton*>, see [DI service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

<span data-ttu-id="fa7ee-117">Im folgenden Code sehen Sie eine Begrüßung des Benutzers, die sich an der jeweiligen Tageszeit orientiert:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-117">The following code displays a greeting to the user based on the time of day:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet)]

<span data-ttu-id="fa7ee-118">Führen Sie die App aus, und eine Nachricht wird angezeigt, die von der Tageszeit abhängt.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-118">Run the app and a message is displayed based on the time.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="fa7ee-119">Action Injection mit FromServices</span><span class="sxs-lookup"><span data-stu-id="fa7ee-119">Action injection with FromServices</span></span>

<span data-ttu-id="fa7ee-120">Mit dem <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> ist es möglich, einen Dienst direkt in eine Aktionsmethode einzufügen, ohne eine Constructor Injection verwenden zu müssen:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-120">The <xref:Microsoft.AspNetCore.Mvc.FromServicesAttribute> enables injecting a service directly into an action method without using constructor injection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/HomeController.cs?name=snippet2)]

## <a name="access-settings-from-a-controller"></a><span data-ttu-id="fa7ee-121">Zugreifen auf Einstellungen von einem Controller</span><span class="sxs-lookup"><span data-stu-id="fa7ee-121">Access settings from a controller</span></span>

<span data-ttu-id="fa7ee-122">Das Zugreifen auf App- oder Konfigurationseinstellungen von einem Controller aus ist ein häufiges Szenario.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-122">Accessing app or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="fa7ee-123">Das in <xref:fundamentals/configuration/options> beschriebene *Optionsmuster* ist der bevorzugte Ansatz, um Einstellungen zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-123">The *options pattern* described in <xref:fundamentals/configuration/options> is the preferred approach to manage settings.</span></span> <span data-ttu-id="fa7ee-124">Fügen Sie generell <xref:Microsoft.Extensions.Configuration.IConfiguration> nicht direkt in einen Controller ein.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-124">Generally, don't directly inject <xref:Microsoft.Extensions.Configuration.IConfiguration> into a controller.</span></span>

<span data-ttu-id="fa7ee-125">Erstellen Sie eine Klasse, die die Optionen darstellt.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-125">Create a class that represents the options.</span></span> <span data-ttu-id="fa7ee-126">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-126">For example:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Models/SampleWebSettings.cs?name=snippet)]

<span data-ttu-id="fa7ee-127">Hinzufügen der Konfigurationsklasse in die Dienstauflistung:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-127">Add the configuration class to the services collection:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Startup.cs?highlight=4&name=snippet1)]

<span data-ttu-id="fa7ee-128">Konfigurieren Sie die App, sodass sie die Einstellungen aus einer Datei mit JSON-Formatierung liest:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-128">Configure the app to read the settings from a JSON-formatted file:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Program.cs?name=snippet&range=10-15)]

<span data-ttu-id="fa7ee-129">Der folgende Code fordert die `IOptions<SampleWebSettings>`-Einstellungen aus dem Dienstcontainer an, und verwendet sie in der `Index`-Methode:</span><span class="sxs-lookup"><span data-stu-id="fa7ee-129">The following code requests the `IOptions<SampleWebSettings>` settings from the service container and uses them in the `Index` method:</span></span>

[!code-csharp[](dependency-injection/sample/ControllerDI/Controllers/SettingsController.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="fa7ee-130">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="fa7ee-130">Additional resources</span></span>

* <span data-ttu-id="fa7ee-131">Informationen zum einfacheren Testen von Code mithilfe der expliziten Anforderung von Abhängigkeiten in Controllern finden Sie unter <xref:mvc/controllers/testing>.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-131">See <xref:mvc/controllers/testing> to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

* <span data-ttu-id="fa7ee-132">Informationen zum [Ersetzen von Standarddienstcontainern](xref:fundamentals/dependency-injection#default-service-container-replacement) mit einer Drittanbieterimplementierung.</span><span class="sxs-lookup"><span data-stu-id="fa7ee-132">[Replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement).</span></span>
