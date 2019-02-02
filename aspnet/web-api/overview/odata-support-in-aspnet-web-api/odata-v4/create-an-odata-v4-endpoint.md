---
uid: web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
title: Erstellen einer ASP.NET Web API 2.2 verwendet OData v4-Endpunkts | Microsoft-Dokumentation
author: MikeWasson
description: Das Open Data Protocol (OData) ist eine Data Access-Protokoll für das Web. OData bietet eine einheitliche Möglichkeit zum Abfragen und Bearbeiten von Datensätzen über CRUD-Vorgänge...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 1e1927c0-ded1-4752-80fd-a146628d2f09
msc.legacyurl: /web-api/overview/odata-support-in-aspnet-web-api/odata-v4/create-an-odata-v4-endpoint
msc.type: authoredcontent
ms.openlocfilehash: c6a4aa4eb563fd77d5afd9248175d5f5b7984d19
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667569"
---
# <a name="create-an-odata-v4-endpoint-using-aspnet-web-api"></a>Erstellen Sie einen OData v4-Endpunkt mithilfe von ASP.NET Web-API 

> Das Open Data Protocol (OData) ist eine Data Access-Protokoll für das Web. OData bietet eine einheitliche Möglichkeit zum Abfragen und Bearbeiten von Datensätzen über CRUD-Vorgänge (erstellen, lesen, aktualisieren und löschen).
>
> ASP.NET Web-API unterstützt v3 und v4 des Protokolls. Sie haben sogar einen v4-Endpunkts, die Seite-an-Seite ausgeführt wird mit einem v3-Endpunkt.
>
> Dieses Tutorial veranschaulicht, wie einen OData v4-Endpunkt zu erstellen, der CRUD-Vorgänge unterstützt.
>
> ## <a name="software-versions-used-in-the-tutorial"></a>Softwareversionen, die in diesem Tutorial verwendet werden.
>
> - Web-API 5.2
> - OData v4
> - Visual Studio 2017 (Visual Studio 2017 herunterladen [hier](https://visualstudio.microsoft.com/downloads/))
> - Entity Framework 6
> - .NET 4.7.2
>
> ## <a name="tutorial-versions"></a>Lernprogramm-Versionen
>
> Die OData-Version 3, finden Sie unter [Erstellen eines OData-v3-Endpunkts](../odata-v3/creating-an-odata-endpoint.md).

## <a name="create-the-visual-studio-project"></a>Visual Studio-Projekt erstellen

In Visual Studio aus der **Datei** , wählen Sie im Menü **neu** &gt; **Projekt**.

Erweitern Sie **installiert** &gt; **Visual C#**  &gt; **Web**, und wählen Sie die **ASP.NET-Webanwendung ((.NET Framework)**  Vorlage. Nennen Sie das Projekt &quot;ProductService&quot;.

[![](create-an-odata-v4-endpoint/_static/image7.png)](create-an-odata-v4-endpoint/_static/image7.png)

Klicken Sie auf **OK**.



[![](create-an-odata-v4-endpoint/_static/image8.png)](create-an-odata-v4-endpoint/_static/image8.png)

Wählen Sie die **leere** Vorlage. Klicken Sie unter **fügen Sie Ordner und kernreferenzen für:** Option **Web-API-**. Klicken Sie auf **OK**.

## <a name="install-the-odata-packages"></a>Installieren Sie die OData-Pakete

Wählen Sie im Menü **Tools** die Option **NuGet-Paket-Manager** &gt; **Paket-Manager-Konsole** aus. Geben Sie im Fenster Paket-Manager-Konsole:

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample1.cmd)]

Mit diesem Befehl wird der aktuelle OData-NuGet-Pakete installiert.

## <a name="add-a-model-class"></a>Hinzufügen einer Modellklasse

Ein *Modell* ist ein Objekt, das eine Datenentität in Ihrer Anwendung darstellt.

Klicken Sie im Projektmappen-Explorer den Ordner "Models". Wählen Sie im Kontextmenü des **hinzufügen** &gt; **Klasse**.

[![](create-an-odata-v4-endpoint/_static/image6.png)](create-an-odata-v4-endpoint/_static/image5.png)

> [!NOTE]
> Gemäß der Konvention ViewModel-Klassen befinden sich im Ordner "Models", aber Sie müssen keine dieser Konvention in Ihren eigenen Projekten folgen.


Nennen Sie die Klasse `Product`. Ersetzen Sie in der Datei "Product.cs" den Standardcode durch den folgenden:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample2.cs)]

Die `Id` -Eigenschaft ist der Entitätsschlüssel. Clients können Entitäten nach Schlüssel abrufen. Um das Produkt mit der ID 5 zu erhalten, ist der URI beispielsweise `/Products(5)`. Die `Id` Eigenschaft werden ebenfalls der Primärschlüssel in der Back-End-Datenbank.

## <a name="enable-entity-framework"></a>Aktivieren von Entitätsframework

In diesem Tutorial wird Entity Framework (EF) Code First verwendet, um die Back-End-Datenbank zu erstellen.

> [!NOTE]
> Web-API OData ist EF nicht erforderlich. Verwenden Sie Datenzugriffsebene, die Datenbankentitäten in Modellen umsetzen kann.


Installieren Sie zuerst das NuGet-Paket für EF. Wählen Sie im Menü **Tools** die Option **NuGet-Paket-Manager** &gt; **Paket-Manager-Konsole** aus. Geben Sie im Fenster Paket-Manager-Konsole:

[!code-console[Main](create-an-odata-v4-endpoint/samples/sample3.cmd)]

Öffnen Sie die Datei "Web.config", und fügen Sie folgenden Abschnitt in der **Konfiguration** Element nach dem **ConfigSections** Element.

[!code-xml[Main](create-an-odata-v4-endpoint/samples/sample4.xml?highlight=6)]

Diese Einstellung wird eine Verbindungszeichenfolge für eine LocalDB-Datenbank hinzugefügt. Wenn Sie die app lokal ausführen, wird diese Datenbank verwendet werden.

Als Nächstes fügen Sie eine Klasse, die mit dem Namen `ProductsContext` zum Ordner "Models":

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample5.cs)]

Im Konstruktor `"name=ProductsContext"` gibt den Namen der Verbindungszeichenfolge.

## <a name="configure-the-odata-endpoint"></a>Konfigurieren Sie den OData-Endpunkt

Öffnen Sie die Datei App\_Start/WebApiConfig.cs. Fügen Sie die folgenden **mit** Anweisungen:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample6.cs)]

Klicken Sie dann fügen Sie den folgenden Code der **registrieren** Methode:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample7.cs)]

Dieser Code bewirkt zwei Dinge:

- Erstellt ein Entity Data Model (EDM).
- Fügt eine Route hinzu.

Ein EDM ist ein abstraktes Modell der Daten. Das EDM wird verwendet, um das Metadatendokument für den Dienst zu erstellen. Die **ODataConventionModelBuilder** Klasse erstellt ein EDM mithilfe von Standard-Benennungskonventionen. Dieser Ansatz erfordert den geringsten Code. Wenn Sie mehr Kontrolle über das EDM möchten, können Sie die **ODataModelBuilder** Klasse, um die EDM zu erstellen, durch das Hinzufügen von Eigenschaften, Schlüssel und Navigationseigenschaften explizit.

Ein *Route* weist Web-API wie HTTP-Anforderungen an den Endpunkt weitergeleitet. Rufen Sie zum Erstellen einer OData v4-Route dem **MapODataServiceRoute** -Erweiterungsmethode.

Wenn Ihre Anwendung mehrere OData-Endpunkte verfügt, erstellen Sie eine separate Route für jeden. Geben Sie jede Route, einen eindeutigen Routennamen und Präfix zu.

## <a name="add-the-odata-controller"></a>Hinzufügen des OData-Controllers

Ein *Controller* ist eine Klasse, die HTTP-Anforderungen verarbeitet. Erstellen Sie einen separaten Controller für jede Entität im OData-Dienst festgelegt. In diesem Tutorial erstellen Sie einen Controller für die `Product` Entität.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in den Ordner "Controllers", und wählen Sie **hinzufügen** &gt; **Klasse**. Nennen Sie die Klasse `ProductsController`.

> [!NOTE]
> Die Version dieses Tutorials für OData v3 verwendet die **Controller hinzufügen** Gerüstbau. Es gibt derzeit keine Gerüstbau für OData v4.


Ersetzen Sie die Codebausteine in ProductsController.cs durch Folgendes.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample8.cs)]

Der Controller verwendet die `ProductsContext` Klasse Zugriff auf die Datenbank mithilfe von EF. Beachten Sie, die der Controller überschreibt die **Dispose** Methode zum Verwerfen der **ProductsContext**.

Dies ist der Ausgangspunkt für den Controller. Als Nächstes fügen wir Methoden für alle CRUD-Vorgänge.

## <a name="query-the-entity-set"></a>Die Entitätenmenge Abfragen

Fügen Sie die folgenden Methoden auf `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample9.cs)]

Der parameterlosen Version von der `Get` die gesamte Auflistung für die Produkte der Methodenrückgabe. Die `Get` -Methode mit einem *Schlüssel* Parameter sucht ein Produkt durch deren Schlüssel (in diesem Fall die `Id` Eigenschaft).

Die **[EnableQuery]** Attribut ermöglicht Clients, die Abfrage mithilfe von Abfrageoptionen wie $filter "," $sort, und "$page zu ändern. Weitere Informationen finden Sie unter [unterstützt OData-Abfrageoptionen](../supporting-odata-query-options.md).

## <a name="add-an-entity-to-the-entity-set"></a>Hinzufügen einer Entität, auf die Entitätenmenge

Um Clients zum Hinzufügen eines neuen Produkts in die Datenbank zu aktivieren, fügen Sie die folgende Methode `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample10.cs)]

## <a name="update-an-entity"></a>Aktualisieren einer Entität

OData unterstützt zwei unterschiedliche Semantiken zum Aktualisieren einer Entität, Patch- und PUT.

- PATCH führt eine partielle Aktualisierung. Der Client gibt nur die Eigenschaften zu aktualisieren.
- PUT wird die gesamte Entität ersetzt.

Der Nachteil von PUT ist, dass der Client gesendet werden muss Werte für alle Eigenschaften in der Entität, einschließlich der Werte, die nicht geändert werden. Die [OData-Spezifikation](http://docs.oasis-open.org/odata/odata/v4.0/os/part1-protocol/odata-v4.0-os-part1-protocol.html#_Toc372793719) gibt an, dass ein PATCH bevorzugt wird.

In jedem Fall sieht der Code für sowohl Patch- und PUT-Methoden:

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample11.cs)]

Im Fall von PATCH, der Controller verwendet die **Delta&lt;T&gt;**  Typ, um die Änderungen nachzuverfolgen.

## <a name="delete-an-entity"></a>Löschen einer Entität

Um Clients So löschen Sie ein Produkt aus der Datenbank zu aktivieren, fügen Sie die folgende Methode `ProductsController`.

[!code-csharp[Main](create-an-odata-v4-endpoint/samples/sample12.cs)]
