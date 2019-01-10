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
# <a name="use-web-api-analyzers"></a>Verwenden von Analysetools für Web-APIs

ASP.NET Core 2.2 und höher umfasst das NuGet-Paket [Microsoft.AspNetCore.Mvc.Api.Analyzers](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Api.Analyzers), in dessen Lieferumfang Analysetools für Web-APIs enthalten sind. Die Analysetools arbeiten mit Controllern mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse zusammen und basieren auf [API-Konventionen](xref:web-api/advanced/conventions).

## <a name="package-installation"></a>Paketinstallation

Das Paket `Microsoft.AspNetCore.Mvc.Api.Analyzers` kann wie folgt hinzugefügt werden:

### <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aus dem Fenster **Paket-Manager-Konsole**:
  * Navigieren Sie zu **Ansicht** > **Other Windows** (Weitere Fenster)  > **Paket-Manager-Konsole**.
  * Navigieren Sie zu dem Verzeichnis, in dem die *ApiConventions.csproj*-Datei gespeichert ist.
  * Führen Sie den folgenden Befehl aus:

    ```powershell
    Install-Package Microsoft.AspNetCore.Mvc.Api.Analyzers
    ```

* Aus dem Dialogfeld **NuGet-Pakete verwalten**:
  * Klicken Sie mit der rechten Maustaste unter **Projektmappen-Explorer** > **NuGet-Pakete verwalten** auf Ihr Projekt.
  * Legen Sie die **Paketquelle** auf „nuget.org“ fest.
  * Geben Sie „Microsoft.AspNetCore.Mvc.Api.Analyzers“ in das Suchfeld ein.
  * Wählen Sie das Paket „Microsoft.AspNetCore.Mvc.Api.Analyzers“ auf der Registerkarte **Durchsuchen** aus, und klicken Sie auf **Installieren**.

### <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* Klicken Sie mit der rechten Maustaste auf den Ordner *Pakete* unter **Lösungspad** > **Pakete hinzufügen...**.
* Legen Sie im Fenster **Pakete hinzufügen** das Dropdownmenü **Quelle** auf „nuget.org“ fest.
* Geben Sie „Microsoft.AspNetCore.Mvc.Api.Analyzers“ in das Suchfeld ein.
* Wählen Sie das Paket „Microsoft.AspNetCore.Mvc.Api.Analyzers“ über den Ergebnisbereich aus, und klicken Sie auf **Paket hinzufügen**.

### <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Führen Sie folgenden Befehl aus dem **integrierten Terminal** aus:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

### <a name="net-core-clitabnetcore-cli"></a>[.NET Core-CLI](#tab/netcore-cli)

Führen Sie den folgenden Befehl aus:

```console
dotnet add ApiConventions.csproj package Microsoft.AspNetCore.Mvc.Api.Analyzers
```

---

## <a name="analyzers-for-api-conventions"></a>Analysetools für API-Konventionen

OpenAPI-Dokumente enthalten Statuscodes und Antworttypen die von einer Aktion zurückgegeben werden können. In ASP.NET Core MVC werden Attribute wie <xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute> und <xref:Microsoft.AspNetCore.Mvc.ProducesAttribute> verwendet, um eine Aktion zu dokumentieren. Weitere Informationen zum Dokumentieren Ihrer API finden Sie unter <xref:tutorials/web-api-help-pages-using-swagger>.

Eins der Analysetools in dem Paket untersucht Controller mit der <xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute>-Klasse und erkennt Aktionen, deren Antworten nicht vollständig dokumentiert werden. Betrachten Sie das folgende Beispiel:

[!code-csharp[](conventions/sample/Controllers/ContactsController.cs?name=missing404docs&highlight=9)]

Diese Aktion dokumentiert zwar den HTTP 200-Rückgabetyp „Erfolg“, aber nicht den HTTP 404-Statuscode „Fehler“. Das Analysetool meldet die fehlende Dokumentation für den HTTP 404-Statuscode als Warnung. Es gibt eine Möglichkeit, dieses Problem zu beheben.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:web-api/advanced/conventions>
* <xref:tutorials/web-api-help-pages-using-swagger>
* [Anmerkungen mit dem ApiController-Attribut](xref:web-api/index#annotation-with-apicontroller-attribute)
