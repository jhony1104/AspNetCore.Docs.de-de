---
title: Dependency Injection in Controller in ASP.NET Core
author: ardalis
description: Erfahren Sie, wie ASP.NET Core MVC-Controller Abhängigkeiten mit Dependency Injection in ASP.NET Core explizit über Konstruktoren anfordern.
ms.author: riande
ms.date: 10/14/2016
uid: mvc/controllers/dependency-injection
ms.openlocfilehash: 9d9d0a68927da62fad8df72c868eaf4b8ada440d
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410270"
---
# <a name="dependency-injection-into-controllers-in-aspnet-core"></a><span data-ttu-id="2537d-103">Dependency Injection in Controller in ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2537d-103">Dependency injection into controllers in ASP.NET Core</span></span>

<a name="dependency-injection-controllers"></a>

<span data-ttu-id="2537d-104">Von [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2537d-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2537d-105">ASP.NET Core MVC-Controller sollten ihre Abhängigkeiten explizit über ihre Konstruktoren anfordern.</span><span class="sxs-lookup"><span data-stu-id="2537d-105">ASP.NET Core MVC controllers should request their dependencies explicitly via their constructors.</span></span> <span data-ttu-id="2537d-106">In einigen Fällen können einzelne Controlleraktionen einen Dienst erfordern. Dann ist es möglicherweise nicht sinnvoll, eine Anforderung auf Controllerebene durchzuführen.</span><span class="sxs-lookup"><span data-stu-id="2537d-106">In some instances, individual controller actions may require a service, and it may not make sense to request at the controller level.</span></span> <span data-ttu-id="2537d-107">In diesem Fall können Sie einen Dienst auch als Parameter für die Aktionsmethode einfügen.</span><span class="sxs-lookup"><span data-stu-id="2537d-107">In this case, you can also choose to inject a service as a parameter on the action method.</span></span>

<span data-ttu-id="2537d-108">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2537d-108">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/dependency-injection/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="dependency-injection"></a><span data-ttu-id="2537d-109">Dependency Injection</span><span class="sxs-lookup"><span data-stu-id="2537d-109">Dependency Injection</span></span>

<span data-ttu-id="2537d-110">ASP.NET Core verfügt über integrierte Unterstützung für [Dependency Injection](../../fundamentals/dependency-injection.md). Dadurch können Anwendungen einfacher getestet und verwaltet werden.</span><span class="sxs-lookup"><span data-stu-id="2537d-110">ASP.NET Core has built-in support for [dependency injection](../../fundamentals/dependency-injection.md), which makes applications easier to test and maintain.</span></span>

## <a name="constructor-injection"></a><span data-ttu-id="2537d-111">Constructor Injection</span><span class="sxs-lookup"><span data-stu-id="2537d-111">Constructor Injection</span></span>

<span data-ttu-id="2537d-112">Die integrierte Unterstützung von ASP.NET Core für konstruktorbasierte Dependency Injection gilt auch für MVC-Controller.</span><span class="sxs-lookup"><span data-stu-id="2537d-112">ASP.NET Core's built-in support for constructor-based dependency injection extends to MVC controllers.</span></span> <span data-ttu-id="2537d-113">Indem Sie Ihrem Controller einfach einen Diensttyp als Konstruktorparameter hinzufügen, versucht ASP.NET Core, den Typ mithilfe des integrierten Dienstcontainers aufzulösen.</span><span class="sxs-lookup"><span data-stu-id="2537d-113">By simply adding a service type to your controller as a constructor parameter, ASP.NET Core will attempt to resolve that type using its built in service container.</span></span> <span data-ttu-id="2537d-114">Dienste werden meist, wenn auch nicht immer, mithilfe von Schnittstellen definiert.</span><span class="sxs-lookup"><span data-stu-id="2537d-114">Services are typically, but not always, defined using interfaces.</span></span> <span data-ttu-id="2537d-115">Wenn Ihre Anwendung beispielsweise über Geschäftslogik verfügt, die die aktuelle Uhrzeit benötigt, können Sie einen Dienst einfügen, der die Uhrzeit abruft (anstatt sie vorzudefinieren). Dadurch bestehen Ihre Tests auch in Implementierungen, die eine festgelegte Uhrzeit verwenden.</span><span class="sxs-lookup"><span data-stu-id="2537d-115">For example, if your application has business logic that depends on the current time, you can inject a service that retrieves the time (rather than hard-coding it), which would allow your tests to pass in implementations that use a set time.</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Interfaces/IDateTime.cs)]


<span data-ttu-id="2537d-116">Das Implementieren einer solchen Schnittstelle, die die Systemuhr zur Laufzeit verwendet, ist einfach:</span><span class="sxs-lookup"><span data-stu-id="2537d-116">Implementing an interface like this one so that it uses the system clock at runtime is trivial:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Services/SystemDateTime.cs)]


<span data-ttu-id="2537d-117">Nun kann der Dienst im Controller verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2537d-117">With this in place, we can use the service in our controller.</span></span> <span data-ttu-id="2537d-118">In diesem Fall wurde der `HomeController` `Index`-Methode Logik hinzugefügt, um dem Benutzer je nach Tageszeit einen entsprechenden Gruß anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2537d-118">In this case, we have added some logic to the `HomeController` `Index` method to display a greeting to the user based on the time of day.</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=8,10,12,17,18,19,20,21,22,23,24,25,26,27,28,29,30&range=1-31,51-52)]

<span data-ttu-id="2537d-119">Beim Ausführen der Anwendung wird nun vermutlich die folgende Fehlermeldung angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2537d-119">If we run the application now, we will most likely encounter an error:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Unable to resolve service for type 'ControllerDI.Interfaces.IDateTime' while attempting to activate 'ControllerDI.Controllers.HomeController'.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.GetService(IServiceProvider sp, Type type, Type requiredBy, Boolean isDefaultParameterRequired)
```

<span data-ttu-id="2537d-120">Dieser Fehler tritt auf, wenn in der `ConfigureServices`-Methode der `Startup`-Klasse kein Dienst konfiguriert wurde.</span><span class="sxs-lookup"><span data-stu-id="2537d-120">This error occurs when we have not configured a service in the `ConfigureServices` method in our `Startup` class.</span></span> <span data-ttu-id="2537d-121">Damit Anforderungen für `IDateTime` mithilfe einer Instanz von `SystemDateTime` aufgelöst werden, fügen Sie der `ConfigureServices`-Methode die hervorgehobene Zeile der folgenden Liste hinzu:</span><span class="sxs-lookup"><span data-stu-id="2537d-121">To specify that requests for `IDateTime` should be resolved using an instance of `SystemDateTime`, add the highlighted line in the listing below to your `ConfigureServices` method:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=4&range=26-27,42-44)]

> [!NOTE]
> <span data-ttu-id="2537d-122">Dieser Dienst kann mithilfe von unterschiedlichen Lebensdaueroptionen (`Transient`, `Scoped` oder `Singleton`) implementiert werden.</span><span class="sxs-lookup"><span data-stu-id="2537d-122">This particular service could be implemented using any of several different lifetime options (`Transient`, `Scoped`, or `Singleton`).</span></span> <span data-ttu-id="2537d-123">Informationen zu den Auswirkungen der jeweiligen Bereichsoptionen auf das Verhalten des Diensts finden Sie unter [Dependency Injection](../../fundamentals/dependency-injection.md).</span><span class="sxs-lookup"><span data-stu-id="2537d-123">See [Dependency Injection](../../fundamentals/dependency-injection.md) to understand how each of these scope options will affect the behavior of your service.</span></span>

<span data-ttu-id="2537d-124">Sobald der Dienst konfiguriert wurde, sollte beim Ausführen der Anwendung und beim Navigieren zur Startseite wie erwartet eine zeitbasierte Nachricht angezeigt werden:</span><span class="sxs-lookup"><span data-stu-id="2537d-124">Once the service has been configured, running the application and navigating to the home page should display the time-based message as expected:</span></span>

![Serverbegrüßung](dependency-injection/_static/server-greeting.png)

>[!TIP]
> <span data-ttu-id="2537d-126">Informationen zum einfacheren Testen von Code mithilfe der expliziten Anforderung von Abhängigkeiten in Controllern finden Sie unter [Testen von Controllerlogik](testing.md).</span><span class="sxs-lookup"><span data-stu-id="2537d-126">See [Test controller logic](testing.md) to learn how to make code easier to test by explicitly requesting dependencies in controllers.</span></span>

<span data-ttu-id="2537d-127">Die integrierte Dependency Injection von ASP.NET Core unterstützt das Vorhandensein eines einzelnen Konstruktors für Klassen, die Dienste anfordern.</span><span class="sxs-lookup"><span data-stu-id="2537d-127">ASP.NET Core's built-in dependency injection supports having only a single constructor for classes requesting services.</span></span> <span data-ttu-id="2537d-128">Bei mehr als einem Konstruktor wird möglicherweise die folgende Ausnahme angezeigt:</span><span class="sxs-lookup"><span data-stu-id="2537d-128">If you have more than one constructor, you may get an exception stating:</span></span>

```
An unhandled exception occurred while processing the request.

InvalidOperationException: Multiple constructors accepting all given argument types have been found in type 'ControllerDI.Controllers.HomeController'. There should only be one applicable constructor.
Microsoft.Extensions.DependencyInjection.ActivatorUtilities.FindApplicableConstructor(Type instanceType, Type[] argumentTypes, ConstructorInfo& matchingConstructor, Nullable`1[]& parameterMap)
```

<span data-ttu-id="2537d-129">Wie in der Fehlermeldung angegeben, kann dieses Problem behoben werden, indem nur ein Konstruktor verwendet wird.</span><span class="sxs-lookup"><span data-stu-id="2537d-129">As the error message states, you can correct this problem with the use of a single constructor.</span></span> <span data-ttu-id="2537d-130">Sie können den [Standardcontainer für die Abhängigkeitsinjektion durch Drittanbieterimplementierungen ersetzen](xref:fundamentals/dependency-injection#default-service-container-replacement), von denen einige mehrere Konstruktoren unterstützen.</span><span class="sxs-lookup"><span data-stu-id="2537d-130">You can also [replace the default dependency injection container with a third party implementation](xref:fundamentals/dependency-injection#default-service-container-replacement), many of which support multiple constructors.</span></span>

## <a name="action-injection-with-fromservices"></a><span data-ttu-id="2537d-131">Action Injection mit FromServices</span><span class="sxs-lookup"><span data-stu-id="2537d-131">Action Injection with FromServices</span></span>

<span data-ttu-id="2537d-132">In einigen Fällen benötigen Sie für mehr als eine Aktion innerhalb des Controllers keinen Dienst.</span><span class="sxs-lookup"><span data-stu-id="2537d-132">Sometimes you don't need a service for more than one action within your controller.</span></span> <span data-ttu-id="2537d-133">In diesem Fall kann es sinnvoll sein, den Dienst als Parameter in die Aktionsmethode einzufügen.</span><span class="sxs-lookup"><span data-stu-id="2537d-133">In this case, it may make sense to inject the service as a parameter to the action method.</span></span> <span data-ttu-id="2537d-134">Markieren Sie hierzu wie im folgenden Beispiel gezeigt den Parameter mit dem Attribut `[FromServices]`:</span><span class="sxs-lookup"><span data-stu-id="2537d-134">This is done by marking the parameter with the attribute `[FromServices]` as shown here:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/HomeController.cs?highlight=1&range=33-38)]

## <a name="accessing-settings-from-a-controller"></a><span data-ttu-id="2537d-135">Zugreifen auf Einstellungen von einem Controller</span><span class="sxs-lookup"><span data-stu-id="2537d-135">Accessing Settings from a Controller</span></span>

<span data-ttu-id="2537d-136">Das Zugreifen auf Anwendungs- oder Konfigurationseinstellungen von einem Controller aus ist ein häufiges Szenario.</span><span class="sxs-lookup"><span data-stu-id="2537d-136">Accessing application or configuration settings from within a controller is a common pattern.</span></span> <span data-ttu-id="2537d-137">Verwenden Sie für diesen Zugriff das in [Konfiguration](xref:fundamentals/configuration/index) beschriebene Optionsmuster.</span><span class="sxs-lookup"><span data-stu-id="2537d-137">This access should use the Options pattern described in [configuration](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2537d-138">Einstellungen sollten generell nicht direkt vom Controller mithilfe von Dependency Injection angefordert werden.</span><span class="sxs-lookup"><span data-stu-id="2537d-138">You generally shouldn't request settings directly from your controller using dependency injection.</span></span> <span data-ttu-id="2537d-139">Fordern Sie stattdessen eine `IOptions<T>`-Instanz an, wobei `T` die benötigte Konfigurationsklasse darstellt.</span><span class="sxs-lookup"><span data-stu-id="2537d-139">A better approach is to request an `IOptions<T>` instance, where `T` is the configuration class you need.</span></span>

<span data-ttu-id="2537d-140">Damit Sie mit dem Optionsmuster arbeiten können, erstellen Sie wie folgt eine Klasse, die die Optionen darstellt:</span><span class="sxs-lookup"><span data-stu-id="2537d-140">To work with the options pattern, you need to create a class that represents the options, such as this one:</span></span>

[!code-csharp[](dependency-injection/sample/src/ControllerDI/Model/SampleWebSettings.cs)]

<span data-ttu-id="2537d-141">Konfigurieren Sie anschließend die Anwendung so, dass das Optionsmodell verwendet wird. Fügen Sie Ihre Konfigurationsklasse der Dienstauflistung in `ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="2537d-141">Then you need to configure the application to use the options model and add your configuration class to the services collection in `ConfigureServices`:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Startup.cs?highlight=3,4,5,6,9,16,19&range=14-44)]

> [!NOTE]
> <span data-ttu-id="2537d-142">In der voranstehenden Liste wird die Anwendung so konfiguriert, dass die Einstellungen aus einer JSON-formatierten Datei gelesen werden.</span><span class="sxs-lookup"><span data-stu-id="2537d-142">In the above listing, we are configuring the application to read the settings from a JSON-formatted file.</span></span> <span data-ttu-id="2537d-143">Sie können die Einstellungen auch vollständig in Code konfigurieren, wie im kommentierten Code oben veranschaulicht wird.</span><span class="sxs-lookup"><span data-stu-id="2537d-143">You can also configure the settings entirely in code, as is shown in the commented code above.</span></span> <span data-ttu-id="2537d-144">Weitere Konfigurationsoptionen finden Sie unter [Konfiguration](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2537d-144">See [Configuration](xref:fundamentals/configuration/index) for further configuration options.</span></span>

<span data-ttu-id="2537d-145">Sobald Sie ein stark typisiertes Konfigurationsobjekt (in diesem Fall `SampleWebSettings`) angegeben und der Dienstauflistung hinzugefügt haben, können Sie es von jeder Controller- oder Aktionsmethode mithilfe der Anforderung einer Instanz von `IOptions<T>` (in diesem Fall `IOptions<SampleWebSettings>`) anfordern.</span><span class="sxs-lookup"><span data-stu-id="2537d-145">Once you've specified a strongly-typed configuration object (in this case, `SampleWebSettings`) and added it to the services collection, you can request it from any Controller or Action method by requesting an instance of `IOptions<T>` (in this case, `IOptions<SampleWebSettings>`).</span></span> <span data-ttu-id="2537d-146">Im folgenden Code wird verdeutlicht, wie die Einstellungen von einem Controller angefordert werden können:</span><span class="sxs-lookup"><span data-stu-id="2537d-146">The following code shows how one would request the settings from a controller:</span></span>

[!code-csharp[](./dependency-injection/sample/src/ControllerDI/Controllers/SettingsController.cs?highlight=3,5,7&range=7-22)]

<span data-ttu-id="2537d-147">Mithilfe des Optionsmusters können Einstellungen und Konfiguration voneinander entkoppelt werden. Es wird sichergestellt, dass der Controller das Prinzip der [Trennung von Belangen](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) befolgt, da ihm nicht bekannt sein muss, wie und wo die Einstellungsinformationen zu finden sind.</span><span class="sxs-lookup"><span data-stu-id="2537d-147">Following the Options pattern allows settings and configuration to be decoupled from one another, and ensures the controller is following [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns), since it doesn't need to know how or where to find the settings information.</span></span> <span data-ttu-id="2537d-148">Dadurch kann für den Controller auch einfacher der [Komponententest](testing.md) durchgeführt werden, da es keine direkte Instanziierung von Einstellungsklassen innerhalb der Controllerklasse gibt.</span><span class="sxs-lookup"><span data-stu-id="2537d-148">It also makes the controller easier to [unit test](testing.md), since there's no direct instantiation of settings classes within the controller class.</span></span>
