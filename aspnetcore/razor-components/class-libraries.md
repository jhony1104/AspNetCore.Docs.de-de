---
title: Klassenbibliotheken für Razor-Komponenten
author: guardrex
description: Ermitteln Sie, wie die Komponenten in Razor-Komponenten-apps aus einer externen Komponentenbibliothek aufgenommen werden können.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: razor-components/class-libraries
ms.openlocfilehash: 1064ad60d90af15af483ba9bca5ed85fb63c2924
ms.sourcegitcommit: 10e14b85490f064395e9b2f423d21e3c2d39ed8b
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "59515461"
---
# <a name="razor-components-class-libraries"></a><span data-ttu-id="b4cc5-103">Klassenbibliotheken für Razor-Komponenten</span><span class="sxs-lookup"><span data-stu-id="b4cc5-103">Razor Components Class Libraries</span></span>

<span data-ttu-id="b4cc5-104">Durch [Simon Timms](https://github.com/stimms)</span><span class="sxs-lookup"><span data-stu-id="b4cc5-104">By [Simon Timms](https://github.com/stimms)</span></span>

<span data-ttu-id="b4cc5-105">Komponenten können in Razor-Klassenbibliotheken für Projekte gemeinsam genutzt werden.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-105">Components can be shared in Razor class libraries across projects.</span></span> <span data-ttu-id="b4cc5-106">Komponenten können aus berücksichtigt werden:</span><span class="sxs-lookup"><span data-stu-id="b4cc5-106">Components can be included from:</span></span>

* <span data-ttu-id="b4cc5-107">Ein anderes Projekt in der Projektmappe.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-107">Another project in the solution.</span></span>
* <span data-ttu-id="b4cc5-108">Ein NuGet-Paket.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-108">A NuGet package.</span></span>
* <span data-ttu-id="b4cc5-109">Eine Bibliothek mit .NET auf die verwiesen wird.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-109">A referenced .NET library.</span></span>

<span data-ttu-id="b4cc5-110">Genau, wie Komponenten reguläre .NET Typen sind, sind Komponenten, die durch Razor-Klassenbibliotheken bereitgestellt normale .NET Assemblys.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-110">Just as components are regular .NET types, components provided by Razor class libraries are normal .NET assemblies.</span></span>

<span data-ttu-id="b4cc5-111">Verwenden der `razorclasslib` (Razor) Klassenbibliotheksvorlage mit der [Dotnet neue](/dotnet/core/tools/dotnet-new) Befehl:</span><span class="sxs-lookup"><span data-stu-id="b4cc5-111">Use the `razorclasslib` (Razor class library) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command:</span></span>

```console
dotnet new razorclasslib -o MyComponentLib1
```

<span data-ttu-id="b4cc5-112">Hinzufügen von Dateien mit Razor-Komponente (*.razor*) auf die Razor-Klassenbibliothek.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-112">Add Razor Component files (*.razor*) to the Razor class library.</span></span>

<span data-ttu-id="b4cc5-113">Um die Bibliothek zu einem vorhandenen Projekt hinzuzufügen, verwenden die [Dotnet-Sln](/dotnet/core/tools/dotnet-sln) Befehl:</span><span class="sxs-lookup"><span data-stu-id="b4cc5-113">To add the library to an existing project, use the [dotnet sln](/dotnet/core/tools/dotnet-sln) command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="b4cc5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b4cc5-114">Visual Studio</span></span>](#tab/visual-studio)

```console
dotnet sln add .\MyComponentLib1
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="b4cc5-115">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="b4cc5-115">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet add WebApplication1 reference MyComponentLib1
```

---

> [!NOTE]
> <span data-ttu-id="b4cc5-116">Razor-Klassenbibliotheken nicht kompatibel mit Blazor-apps in ASP.NET Core Preview 3.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-116">Razor class libraries aren't compatible with Blazor apps in ASP.NET Core Preview 3.</span></span>
>
> <span data-ttu-id="b4cc5-117">Verwenden Sie zum Erstellen von Komponenten in einer Bibliothek, die mit Blazor und Razor-Komponenten-apps freigegeben werden können, eine Klassenbibliothek Blazor erstellt die `blazorlib` Vorlage.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-117">To create components in a library that can be shared with Blazor and Razor Components apps, use a Blazor class library created by the `blazorlib` template.</span></span>
>
> <span data-ttu-id="b4cc5-118">Razor-Klassenbibliotheken unterstützt nicht statische Ressourcen in ASP.NET Core Preview 3.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-118">Razor class libraries don't support static assets in ASP.NET Core Preview 3.</span></span> <span data-ttu-id="b4cc5-119">Komponentenbibliotheken, die mit der `blazorlib` Vorlage kann statische Dateien, z. B. Bilder, JavaScript und Stylesheets enthalten.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-119">Component libraries using the `blazorlib` template can include static files, such as images, JavaScript, and stylesheets.</span></span> <span data-ttu-id="b4cc5-120">Zur Buildzeit werden statische Dateien in die erstellte Assembly-Datei eingebettet (*DLL*), der Verbrauch der Komponenten ermöglicht, ohne dazu, wie Sie ihre Ressourcen enthalten.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-120">At build time, static files are embedded into the built assembly file (*.dll*), which allows consumption of the components without having to worry about how to include their resources.</span></span> <span data-ttu-id="b4cc5-121">Alle Dateien in die `content` Verzeichnis als eingebettete Ressource gekennzeichnet sind.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-121">Any files included in the `content` directory are marked as an embedded resource.</span></span>

## <a name="consume-a-library-component"></a><span data-ttu-id="b4cc5-122">Nutzen Sie eine Bibliothekskomponente</span><span class="sxs-lookup"><span data-stu-id="b4cc5-122">Consume a library component</span></span>

<span data-ttu-id="b4cc5-123">Um in einer Bibliothek in einem anderen Projekt definierten Komponenten nutzen den [ @addTagHelper ](xref:mvc/views/tag-helpers/intro#add-helper-label) Richtlinie muss verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-123">In order to consume components defined in a library in another project, the [@addTagHelper](xref:mvc/views/tag-helpers/intro#add-helper-label) directive must be used.</span></span> <span data-ttu-id="b4cc5-124">Einzelne Komponenten können anhand des Namens hinzugefügt werden.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-124">Individual components may be added by name.</span></span>

<span data-ttu-id="b4cc5-125">Das allgemeine Format für die Direktive ist:</span><span class="sxs-lookup"><span data-stu-id="b4cc5-125">The general format of the directive is:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1

<h1>Hello, world!</h1>

Welcome to your new app.

<Component1 />
```

<span data-ttu-id="b4cc5-126">Die folgende Anweisung fügt beispielsweise `Component1` von `MyComponentLib1`:</span><span class="sxs-lookup"><span data-stu-id="b4cc5-126">For example, the following directive adds `Component1` of `MyComponentLib1`:</span></span>

```cshtml
@addTagHelper MyComponentLib1.Component1, MyComponentLib1
```

<span data-ttu-id="b4cc5-127">Es ist jedoch üblich, alle Komponenten aus einer Assembly mit einem Platzhalter enthalten (`*`):</span><span class="sxs-lookup"><span data-stu-id="b4cc5-127">However, it's common to include all of the components from an assembly using a wildcard (`*`):</span></span>

```cshtml
@addTagHelper *, MyComponentLib1
```

<span data-ttu-id="b4cc5-128">Die `@addTagHelper` Richtlinie enthalten sein kann, im *_ViewImport.cshtml* die Komponenten verfügbar, die für ein gesamtes Projekt oder angewendet auf eine einzelne Seite oder eine Gruppe von Seiten in einem Ordner vornehmen.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-128">The `@addTagHelper` directive can be included in *_ViewImport.cshtml* to make the components available for an entire project or applied to a single page or set of pages within a folder.</span></span> <span data-ttu-id="b4cc5-129">Mit der `@addTagHelper` Direktive vorhanden, die Bestandteile der Komponentenbibliothek genutzt werden können als wären sie in der gleichen Assembly wie die app.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-129">With the `@addTagHelper` directive in place, the components of the component library can be consumed as if they were in the same assembly as the app.</span></span>

## <a name="build-pack-and-ship-to-nuget"></a><span data-ttu-id="b4cc5-130">Build, Pack und Versand zu NuGet</span><span class="sxs-lookup"><span data-stu-id="b4cc5-130">Build, pack, and ship to NuGet</span></span>

<span data-ttu-id="b4cc5-131">Da Komponentenbibliotheken .NET Standardbibliotheken sind, unterscheidet Verpackung und Lieferung an den NuGet sich nicht von der Verpackung und Versand einer Bibliothek auf NuGet.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-131">Because component libraries are standard .NET libraries, packaging and shipping them to NuGet is no different from packaging and shipping any library to NuGet.</span></span> <span data-ttu-id="b4cc5-132">Paketerstellung erfolgt unter Verwendung der [Dotnet-Pack](/dotnet/core/tools/dotnet-pack) Befehl:</span><span class="sxs-lookup"><span data-stu-id="b4cc5-132">Packaging is performed using the [dotnet pack](/dotnet/core/tools/dotnet-pack) command:</span></span>

```console
dotnet pack
```

<span data-ttu-id="b4cc5-133">Hochladen des Pakets mit NuGet die [Dotnet Nuget veröffentlichen](/dotnet/core/tools/dotnet-nuget-push) Befehl:</span><span class="sxs-lookup"><span data-stu-id="b4cc5-133">Upload the package to NuGet using the [dotnet nuget publish](/dotnet/core/tools/dotnet-nuget-push) command:</span></span>

```console
dotnet nuget publish
```

<span data-ttu-id="b4cc5-134">Bei Verwendung der `blazorlib` Vorlage statische Ressourcen in das NuGet-Paket enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-134">When using the `blazorlib` template, static resources are included in the NuGet package.</span></span> <span data-ttu-id="b4cc5-135">Consumer erhalten automatisch Skripts und Stylesheets, damit der Consumer nicht erforderlich, um die Ressourcen manuell zu installieren sind.</span><span class="sxs-lookup"><span data-stu-id="b4cc5-135">Library consumers automatically receive scripts and stylesheets, so consumers aren't required to manually install the resources.</span></span>
