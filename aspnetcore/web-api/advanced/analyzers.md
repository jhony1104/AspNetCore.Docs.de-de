---
title: Verwenden von Analysetools für Web-APIs
author: pranavkm
description: Informationen zu Analysetools für Web-APIs in Microsoft.AspNetCore.Mvc.Api.Analyzers
monikerRange: '>= aspnetcore-2.2'
ms.author: pranavkm
ms.custom: mvc
ms.date: 12/14/2018
uid: web-api/advanced/analyzers
ms.openlocfilehash: 7558552586d3056c43d8bfd9ef74cbcb3396726f
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425093"
---
# <a name="use-web-api-analyzers"></a><span data-ttu-id="2a79d-103">Verwenden von Analysetools für Web-APIs</span><span class="sxs-lookup"><span data-stu-id="2a79d-103">Use web API analyzers</span></span>

<span data-ttu-id="2a79d-104">ASP.NET Core 2.2 und höher umfasst das NuGet-Paket [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers), in dessen Lieferumfang Analysetools für Web-APIs enthalten sind.</span><span class="sxs-lookup"><span data-stu-id="2a79d-104">ASP.NET Core 2.2 and later includes the [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers) NuGet package containing analyzers for web APIs.</span></span> <span data-ttu-id="2a79d-105">Die Analysetools arbeiten mit Controllern mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse zusammen und basieren auf [API-Konventionen](xref:web-api/advanced/conventions).</span><span class="sxs-lookup"><span data-stu-id="2a79d-105">The analyzers work with controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>, while building on [API conventions](xref:web-api/advanced/conventions).</span></span>

## <a name="package-installation"></a><span data-ttu-id="2a79d-106">Paketinstallation</span><span class="sxs-lookup"><span data-stu-id="2a79d-106">Package installation</span></span>

<span data-ttu-id="2a79d-107">Das Paket `Microsoft.AspNetCore.Mvc.Api.Analyzers` kann wie folgt hinzugefügt werden:</span><span class="sxs-lookup"><span data-stu-id="2a79d-107">`Microsoft.AspNetCore.Mvc.Api.Analyzers` can be added with one of the following approaches:</span></span>

### <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a79d-108">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a79d-108">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2a79d-109">Aus dem Fenster **Paket-Manager-Konsole**:</span><span class="sxs-lookup"><span data-stu-id="2a79d-109">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="2a79d-110">Navigieren Sie zu **Ansicht** > **Other Windows** (Weitere Fenster)  > **Paket-Manager-Konsole**.</span><span class="sxs-lookup"><span data-stu-id="2a79d-110">Go to **View** > **Other Windows** > **Package Manager Console**.</span></span>
  * <span data-ttu-id="2a79d-111">Navigieren Sie zu dem Verzeichnis, in dem die *ApiConventions.csproj*-Datei gespeichert ist.</span><span class="sxs-lookup"><span data-stu-id="2a79d-111">Navigate to the directory in which the *ApiConventions.csproj* file exists.</span></span>
  * <span data-ttu-id="2a79d-112">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="2a79d-112">Execute the following command:</span></span>

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* <span data-ttu-id="2a79d-113">Aus dem Dialogfeld **NuGet-Pakete verwalten**:</span><span class="sxs-lookup"><span data-stu-id="2a79d-113">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="2a79d-114">Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.</span><span class="sxs-lookup"><span data-stu-id="2a79d-114">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**.</span></span>
  * <span data-ttu-id="2a79d-115">Legen Sie die **Paketquelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="2a79d-115">Set the **Package source** to "nuget.org".</span></span>
  * <span data-ttu-id="2a79d-116">Geben Sie „Microsoft.AspNetCore.Mvc.Api.Analyzers“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="2a79d-116">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
  * <span data-ttu-id="2a79d-117">Wählen Sie das Paket „Microsoft.AspNetCore.Mvc.Api.Analyzers“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.</span><span class="sxs-lookup"><span data-stu-id="2a79d-117">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the **Browse** tab and click **Install**.</span></span>

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2a79d-118">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="2a79d-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2a79d-119">Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.</span><span class="sxs-lookup"><span data-stu-id="2a79d-119">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**.</span></span>
* <span data-ttu-id="2a79d-120">Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.</span><span class="sxs-lookup"><span data-stu-id="2a79d-120">Set the **Add Packages** window's **Source** drop-down to "nuget.org".</span></span>
* <span data-ttu-id="2a79d-121">Geben Sie „Microsoft.AspNetCore.Mvc.Api.Analyzers“ in das Suchfeld ein.</span><span class="sxs-lookup"><span data-stu-id="2a79d-121">Enter "Microsoft.AspNetCore.Mvc.Api.Analyzers" in the search box.</span></span>
* <span data-ttu-id="2a79d-122">Wählen Sie das Paket „Microsoft.AspNetCore.Mvc.Api.Analyzers“ über den Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="2a79d-122">Select the "Microsoft.AspNetCore.Mvc.Api.Analyzers" package from the results pane and click **Add Package**.</span></span>

### <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2a79d-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2a79d-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2a79d-124">Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:</span><span class="sxs-lookup"><span data-stu-id="2a79d-124">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2a79d-125">.NET Core-CLI</span><span class="sxs-lookup"><span data-stu-id="2a79d-125">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2a79d-126">Führen Sie den folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="2a79d-126">Run the following command:</span></span>

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a><span data-ttu-id="2a79d-127">Analysetools für API-Konventionen</span><span class="sxs-lookup"><span data-stu-id="2a79d-127">Analyzers for API conventions</span></span>

<span data-ttu-id="2a79d-128">OpenAPI-Dokumente enthalten Statuscodes und Antworttypen die von einer Aktion zurückgegeben werden können.</span><span class="sxs-lookup"><span data-stu-id="2a79d-128">OpenAPI documents contain status codes and response types that an action may return.</span></span> <span data-ttu-id="2a79d-129">In ASP.NET Core MVC werden Attribute wie <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> und <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> verwendet, um eine Aktion zu dokumentieren.</span><span class="sxs-lookup"><span data-stu-id="2a79d-129">In ASP.NET Core MVC, attributes such as <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> and <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> are used to document an action.</span></span> <span data-ttu-id="2a79d-130">Weitere Informationen zum Dokumentieren Ihrer API finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.</span><span class="sxs-lookup"><span data-stu-id="2a79d-130"><xref:tutorials/web-api-help-pages-using-swagger> goes into further detail on documenting your API.</span></span>

<span data-ttu-id="2a79d-131">Eins der Analysetools in dem Paket untersucht Controller mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse und erkennt Aktionen, deren Antworten nicht vollständig dokumentiert werden.</span><span class="sxs-lookup"><span data-stu-id="2a79d-131">One of the analyzers in the package inspects controllers annotated with <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute> and identifies actions that don't entirely document their responses.</span></span> <span data-ttu-id="2a79d-132">Betrachten Sie das folgende Beispiel:</span><span class="sxs-lookup"><span data-stu-id="2a79d-132">Consider the following example:</span></span>

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

<span data-ttu-id="2a79d-133">Diese Aktion dokumentiert zwar den HTTP 200-Rückgabetyp „Erfolg“, aber nicht den HTTP 404-Statuscode „Fehler“.</span><span class="sxs-lookup"><span data-stu-id="2a79d-133">The preceding action documents the HTTP 200 success return type but doesn't document the HTTP 404 failure status code.</span></span> <span data-ttu-id="2a79d-134">Das Analysetool meldet die fehlende Dokumentation für den HTTP 404-Statuscode als Warnung.</span><span class="sxs-lookup"><span data-stu-id="2a79d-134">The analyzer reports the missing documentation for the HTTP 404 status code as a warning.</span></span> <span data-ttu-id="2a79d-135">Es gibt eine Möglichkeit, dieses Problem zu beheben.</span><span class="sxs-lookup"><span data-stu-id="2a79d-135">An option to fix the problem is provided.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a79d-136">Zusätzliche Ressourcen</span><span class="sxs-lookup"><span data-stu-id="2a79d-136">Additional resources</span></span>

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* [<span data-ttu-id="2a79d-137">Anmerkungen mit dem ApiController-Attribut</span><span class="sxs-lookup"><span data-stu-id="2a79d-137">Annotation with ApiController attribute</span></span>](xref:web-api/index#annotation-with-apicontroller-attribute)
