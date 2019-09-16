---
title: Verwenden von Analysetools für Web-APIs
author: pranavkm
description: Erfahren Sie mehr über das ASP.NET Core MVC-Web-API-Analysetool-Paket.
monikerRange: '>= aspnetcore-2.2'
ms.author: prkrishn
ms.custom: mvc
ms.date: 09/05/2019
uid: web-api/advanced/analyzers
ms.openlocfilehash: 1568eb0304a58758caa5f82249dc42872f5c36b9
ms.sourcegitcommit: 116bfaeab72122fa7d586cdb2e5b8f456a2dc92a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70384867"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="176e1-103">Verwenden von Analysetools für Web-APIs</span><span class="sxs-lookup"><span data-stu-id="176e1-103">Use web API analyzers</span></span>

<span data-ttu-id="176e1-104">ASP.NET Core 2.2 und höher bietet ein MVC-Analysetool-Paket zur Verwendung mit Web-API-Projekten.</span><span class="sxs-lookup"><span data-stu-id="176e1-104">ASP.NET Core 2.2 and later provides an MVC analyzers package intended for use with web API projects.</span></span> <span data-ttu-id="176e1-105">Die Analysetools arbeiten mit Controllern mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse zusammen und basieren auf [Web-API-Konventionen](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="176e1-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [web API conventions](xref:web-api/advanced/conventions).</span></span>

<span data-ttu-id="176e1-106">Das Analysetoolpaket unterrichtet Sie über alle Controlleraktionen, auf die Folgendes zutrifft:</span><span class="sxs-lookup"><span data-stu-id="176e1-106">The analyzers package notifies you of any controller action that:</span></span>

* <span data-ttu-id="176e1-107">Rückgabe eines nicht deklarierten Statuscodes.</span><span class="sxs-lookup"><span data-stu-id="176e1-107">Returns an undeclared status code.</span></span>
* <span data-ttu-id="176e1-108">Rückgabe eines nicht deklarierten Erfolgsergebnisses.</span><span class="sxs-lookup"><span data-stu-id="176e1-108">Returns an undeclared success result.</span></span>
* <span data-ttu-id="176e1-109">Dokumentation eines Statuscodes, der nicht zurückgegeben wird.</span><span class="sxs-lookup"><span data-stu-id="176e1-109">Documents a status code that isn't returned.</span></span>
* <span data-ttu-id="176e1-110">Eine explizite Modellüberprüfung ist enthalten.</span><span class="sxs-lookup"><span data-stu-id="176e1-110">Includes an explicit model validation check.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="reference-the-analyzer-package"></a><span data-ttu-id="176e1-111">Verweisen auf das Analysepaket</span><span class="sxs-lookup"><span data-stu-id="176e1-111">Reference the analyzer package</span></span>

<span data-ttu-id="176e1-112">In ASP.NET Core 3.0 oder höher sind die Analysetools im .NET Core SDK enthalten.</span><span class="sxs-lookup"><span data-stu-id="176e1-112">In ASP.NET Core 3.0 or later, the analyzers are included in the .NET Core SDK.</span></span> <span data-ttu-id="176e1-113">Um das Analysetool im Projekt zu aktivieren, schließen Sie die `IncludeOpenAPIAnalyzers`-Eigenschaft in die Projektdatei ein:</span><span class="sxs-lookup"><span data-stu-id="176e1-113">To enable the analyzer in your project, include the `IncludeOpenAPIAnalyzers` property in the project file:</span></span>

```xml
<PropertyGroup>
 <IncludeOpenAPIAnalyzers>true</IncludeOpenAPIAnalyzers>
</PropertyGroup>
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

## <a name="package-installation"></a><span data-ttu-id="176e1-114">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="176e1-114">Package installation</span></span>

<span data-ttu-id="176e1-115">Installieren Sie das [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers)-NuGet-Paket mit einem der folgenden Ansätze:</span><span class="sxs-lookup"><span data-stu-id="176e1-115">Install the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package with one of the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="176e1-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="176e1-116">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="176e1-117">Aus dem Fenster **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="176e1-117">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="176e1-118">Navigieren Sie zu **Ansicht** > **Weitere Fenster** > **Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="176e1-118">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="176e1-119">Navigieren Sie zu dem Verzeichnis, in dem die *ApiConventions.csproj*-Datei gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="176e1-119">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="176e1-120">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="176e1-120">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="176e1-121">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="176e1-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="176e1-122">Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="176e1-122">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="176e1-123">Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="176e1-123">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="176e1-124">Geben Sie „Microsoft.AspNetCore.Mvc.Api.Analyzers“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="176e1-124">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="176e1-125">Wählen Sie das Paket „Microsoft.AspNetCore.Mvc.Api.Analyzers“ über den Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="176e1-125">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="176e1-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="176e1-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="176e1-127">Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="176e1-127">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="176e1-128">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="176e1-128">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="176e1-129">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="176e1-129">Run the following command:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

::: moniker-end

## <a name="analyzers-for-web-api-conventions"></a><span data-ttu-id="176e1-130">Analysetools für Web-API-Konventionen</span><span class="sxs-lookup"><span data-stu-id="176e1-130">Analyzers for web API conventions</span></span>

<span data-ttu-id="176e1-131">OpenAPI-Dokumente enthalten Statuscodes und Antworttypen die von einer Aktion zurückgegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="176e1-131">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="176e1-132">In ASP.NET Core MVC werden Attribute wie <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> und <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> verwendet, um eine Aktion zu dokumentieren.</span><span class="sxs-lookup"><span data-stu-id="176e1-132">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="176e1-133">Weitere Informationen zum Dokumentieren Ihrer Web-API finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="176e1-133"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your web API.</span></span>

<span data-ttu-id="176e1-134">Eins der Analysetools in dem Paket untersucht Controller mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse und erkennt Aktionen, deren Antworten nicht vollständig dokumentiert werden.</span><span class="sxs-lookup"><span data-stu-id="176e1-134">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="176e1-135">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="176e1-135">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=10)]

<span data-ttu-id="176e1-136">Diese Aktion dokumentiert zwar den HTTP 200-Rückgabetyp „Erfolg“, aber nicht den HTTP 404-Statuscode „Fehler“.</span><span class="sxs-lookup"><span data-stu-id="176e1-136">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="176e1-137">Das Analysetool meldet die fehlende Dokumentation für den HTTP 404-Statuscode als Warnung.</span><span class="sxs-lookup"><span data-stu-id="176e1-137">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="176e1-138">Es gibt eine Möglichkeit, dieses Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="176e1-138">An option to fix the problem is provided.</span></span>

![Analysetool mit Warnung](conventions/_static/Analyzer.gif)

## <a name="additional-resources"></a><span data-ttu-id="176e1-140">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="176e1-140">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:web-api/index>
