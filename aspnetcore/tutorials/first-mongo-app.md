---
title: Erstellen von Web-APIs mit ASP.NET Core und MongoDB
author: prkhandelwal
description: Dieses Tutorial zeigt, wie Sie eine ASP.NET Core Web-API mit einer MongoDB NoSQL-Datenbank erstellen.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 11/29/2018
uid: tutorials/first-mongo-app
ms.openlocfilehash: bd9a36c5eb06542c820e71e937b8da10f735a0f8
ms.sourcegitcommit: 68a3081dd175d6518d1bfa31b4712bd8a2dd3864
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53577837"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="8f00c-103">Erstellen einer Web-API mit ASP.NET Core und MongoDB</span><span class="sxs-lookup"><span data-stu-id="8f00c-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="8f00c-104">Von [Pratik Khandelwal](https://twitter.com/K2Prk) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="8f00c-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="8f00c-105">Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.</span><span class="sxs-lookup"><span data-stu-id="8f00c-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="8f00c-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="8f00c-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8f00c-107">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="8f00c-107">Configure MongoDB</span></span>
> * <span data-ttu-id="8f00c-108">Erstellen einer MongoDB-Datenbank</span><span class="sxs-lookup"><span data-stu-id="8f00c-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="8f00c-109">Definieren einer MongoDB-Sammlung und eines Schemas</span><span class="sxs-lookup"><span data-stu-id="8f00c-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="8f00c-110">Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API</span><span class="sxs-lookup"><span data-stu-id="8f00c-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="8f00c-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8f00c-111">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8f00c-112">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="8f00c-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8f00c-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f00c-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="8f00c-114">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="8f00c-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="8f00c-115">[Version 15.9 von Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="8f00c-115">[Visual Studio 2017 version 15.9 or later](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="8f00c-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8f00c-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8f00c-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8f00c-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="8f00c-118">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="8f00c-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="8f00c-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8f00c-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="8f00c-120">C# für Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8f00c-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="8f00c-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8f00c-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8f00c-122">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8f00c-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="8f00c-123">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="8f00c-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="8f00c-124">Visual Studio für Mac Version 7.7 oder höher</span><span class="sxs-lookup"><span data-stu-id="8f00c-124">Visual Studio for Mac version 7.7 or later</span></span>](https://www.visualstudio.com/downloads/)
* [<span data-ttu-id="8f00c-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="8f00c-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="8f00c-126">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="8f00c-126">Configure MongoDB</span></span>

<span data-ttu-id="8f00c-127">Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\Program Files\MongoDB* installiert.</span><span class="sxs-lookup"><span data-stu-id="8f00c-127">If using Windows, MongoDB is installed at *C:\Program Files\MongoDB* by default.</span></span> <span data-ttu-id="8f00c-128">Fügen Sie der Umgebungsvariablen `Path` *C:\Program Files\MongoDB\Server\<version_number>\bin* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8f00c-128">Add *C:\Program Files\MongoDB\Server\<version_number>\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="8f00c-129">Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="8f00c-130">Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern.</span><span class="sxs-lookup"><span data-stu-id="8f00c-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="8f00c-131">Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="8f00c-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="8f00c-132">Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten.</span><span class="sxs-lookup"><span data-stu-id="8f00c-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="8f00c-133">Zum Beispiel *C:\BooksData* auf Windows.</span><span class="sxs-lookup"><span data-stu-id="8f00c-133">For example, *C:\BooksData* on Windows.</span></span> <span data-ttu-id="8f00c-134">Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="8f00c-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="8f00c-135">Die Mongo-Shell erstellt keine neuen Verzeichnisse.</span><span class="sxs-lookup"><span data-stu-id="8f00c-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="8f00c-136">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="8f00c-136">Open a command shell.</span></span> <span data-ttu-id="8f00c-137">Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="8f00c-138">Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.</span><span class="sxs-lookup"><span data-stu-id="8f00c-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="8f00c-139">Öffnen Sie eine andere Befehlsshellinstanz.</span><span class="sxs-lookup"><span data-stu-id="8f00c-139">Open another command shell instance.</span></span> <span data-ttu-id="8f00c-140">Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="8f00c-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="8f00c-141">Führen Sie in einer Befehlsshell folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="8f00c-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="8f00c-142">Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt.</span><span class="sxs-lookup"><span data-stu-id="8f00c-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="8f00c-143">Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.</span><span class="sxs-lookup"><span data-stu-id="8f00c-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="8f00c-144">Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="8f00c-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="8f00c-145">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8f00c-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="8f00c-146">Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:</span><span class="sxs-lookup"><span data-stu-id="8f00c-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="8f00c-147">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8f00c-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="8f00c-148">Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:</span><span class="sxs-lookup"><span data-stu-id="8f00c-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="8f00c-149">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8f00c-149">The following result is displayed:</span></span>

    ```console
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215d"),
      "Name" : "Design Patterns",
      "Price" : 54.93,
      "Category" : "Computers",
      "Author" : "Ralph Johnson"
    }
    {
      "_id" : ObjectId("5bfd996f7b8e48dc15ff215e"),
      "Name" : "Clean Code",
      "Price" : 43.15,
      "Category" : "Computers",
      "Author" : "Robert C. Martin"
    }
    ```

    <span data-ttu-id="8f00c-150">Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.</span><span class="sxs-lookup"><span data-stu-id="8f00c-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="8f00c-151">Die Datenbank ist bereit.</span><span class="sxs-lookup"><span data-stu-id="8f00c-151">The database is ready.</span></span> <span data-ttu-id="8f00c-152">Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="8f00c-153">Erstellen eines ASP.NET Core-Web-API-Projektes</span><span class="sxs-lookup"><span data-stu-id="8f00c-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8f00c-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8f00c-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8f00c-155">Gehen Sie zu **Datei** > **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="8f00c-156">Wählen Sie **ASP.NET Core-Webanwendung**, benennen Sie das Projekt mit *BooksApi*, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-156">Select **ASP.NET Core Web Application**, name the project *BooksApi*, and click **OK**.</span></span>
1. <span data-ttu-id="8f00c-157">Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 2.1**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-157">Select the **.NET Core** target framework and **ASP.NET Core 2.1**.</span></span> <span data-ttu-id="8f00c-158">Wählen Sie die **API**-Projektvorlage aus, und klicken Sie auf **OK**:</span><span class="sxs-lookup"><span data-stu-id="8f00c-158">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="8f00c-159">Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8f00c-159">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="8f00c-160">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="8f00c-160">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version 2.7.2
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8f00c-161">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8f00c-161">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8f00c-162">Führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="8f00c-162">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="8f00c-163">Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="8f00c-163">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="8f00c-164">Klicken Sie auf **Ja**, wenn die Meldung *Die erforderlichen Objekte für die Erstellung und das Debuggen sind in „BooksApi“ nicht vorhanden. Sollen sie hinzugefügt werden?* angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="8f00c-164">Click **Yes** when the *Required assets to build and debug are missing from 'BooksApi'. Add them?* notification appears.</span></span>
1. <span data-ttu-id="8f00c-165">Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="8f00c-165">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="8f00c-166">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="8f00c-166">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v 2.7.2
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8f00c-167">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="8f00c-167">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8f00c-168">Navigieren Sie zu **Datei**  > **Neue Lösung** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-168">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="8f00c-169">Wählen Sie die **ASP.NET Core-Web-API**-C#-Projektvorlage, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-169">Select the **ASP.NET Core Web API** C# project template, and click **Next**.</span></span>
1. <span data-ttu-id="8f00c-170">Wählen Sie **.NET Core 2.2** aus der Dropdownliste **Zielframework**, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-170">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and click **Next**.</span></span>
1. <span data-ttu-id="8f00c-171">Geben Sie *BooksApi* als **Projektnamen** ein, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-171">Enter *BooksApi* for the **Project Name**, and click **Create**.</span></span>
1. <span data-ttu-id="8f00c-172">Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-172">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="8f00c-173">Geben Sie *MongoDB.Driver* in das Suchfeld ein, wählen Sie das *MongoDB.Driver*-Paket, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-173">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and click **Add Package**.</span></span>
1. <span data-ttu-id="8f00c-174">Klicken Sie auf die Schaltfläche **Zustimmen** im Dialogfeld **Zustimmung zur Lizenz**.</span><span class="sxs-lookup"><span data-stu-id="8f00c-174">Click the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-a-model"></a><span data-ttu-id="8f00c-175">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="8f00c-175">Add a model</span></span>

1. <span data-ttu-id="8f00c-176">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8f00c-176">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="8f00c-177">Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="8f00c-177">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

<span data-ttu-id="8f00c-178">In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt, um das Common Language Runtime (CLR)-Objekt auf die MongoDB-Sammlung zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-178">In the preceding class, the `Id` property is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span> <span data-ttu-id="8f00c-179">Andere Eigenschaften in der Klasse werden mit dem Attribut `[BsonElement]` ergänzt.</span><span class="sxs-lookup"><span data-stu-id="8f00c-179">Other properties in the class are decorated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="8f00c-180">Der Wert des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.</span><span class="sxs-lookup"><span data-stu-id="8f00c-180">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="8f00c-181">Hinzufügen einer CRUD-Vorgangsklasse</span><span class="sxs-lookup"><span data-stu-id="8f00c-181">Add a CRUD operations class</span></span>

1. <span data-ttu-id="8f00c-182">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.</span><span class="sxs-lookup"><span data-stu-id="8f00c-182">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="8f00c-183">Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="8f00c-183">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="8f00c-184">Fügen Sie der Datei *appsettings.json* eine MongoDB-Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="8f00c-184">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    <span data-ttu-id="8f00c-185">Auf die vorhergehende `BookstoreDb`-Eigenschaft wird über den Klassenkonstruktor `BookService` zugegriffen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-185">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="8f00c-186">Registrieren Sie unter `Startup.ConfigureServices` die Klasse `BookService` mit dem Dependency Injection-System:</span><span class="sxs-lookup"><span data-stu-id="8f00c-186">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="8f00c-187">Die vorhergehende Dienstregistrierung ist notwendig, um die Konstruktor-Injection in Verbrauchsklassen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-187">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="8f00c-188">Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:</span><span class="sxs-lookup"><span data-stu-id="8f00c-188">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="8f00c-189">`MongoClient` &ndash; – Liest die Serverinstanz für das Ausführen von Datenbankvorgängen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-189">`MongoClient` &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="8f00c-190">Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="8f00c-190">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="8f00c-191">`IMongoDatabase` &ndash; – Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar.</span><span class="sxs-lookup"><span data-stu-id="8f00c-191">`IMongoDatabase` &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="8f00c-192">Dieses Tutorial verwendet die generische `GetCollection<T>(collection)`-Methode auf der Schnittstelle, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="8f00c-192">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="8f00c-193">CRUD-Vorgänge können für die Sammlung ausgeführt werden, nachdem diese Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="8f00c-193">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="8f00c-194">Rufen Sie in der `GetCollection<T>(collection)`-Methode folgendes auf:</span><span class="sxs-lookup"><span data-stu-id="8f00c-194">In the `GetCollection<T>(collection)` method call:</span></span>
  * <span data-ttu-id="8f00c-195">`collection` steht für den Sammlungsnamen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-195">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="8f00c-196">`T` steht für den in der Sammlung gespeicherten CLR-Objekttypen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-196">`T` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="8f00c-197">`GetCollection<T>(collection)` gibt ein `MongoCollection`-Objekt zurück, das die Sammlung darstellt.</span><span class="sxs-lookup"><span data-stu-id="8f00c-197">`GetCollection<T>(collection)` returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="8f00c-198">In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="8f00c-198">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="8f00c-199">`Find<T>` &ndash; Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-199">`Find<T>` &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="8f00c-200">`InsertOne` &ndash; Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.</span><span class="sxs-lookup"><span data-stu-id="8f00c-200">`InsertOne` &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="8f00c-201">`ReplaceOne` &ndash; Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.</span><span class="sxs-lookup"><span data-stu-id="8f00c-201">`ReplaceOne` &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* <span data-ttu-id="8f00c-202">`DeleteOne` &ndash; Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.</span><span class="sxs-lookup"><span data-stu-id="8f00c-202">`DeleteOne` &ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="8f00c-203">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="8f00c-203">Add a controller</span></span>

1. <span data-ttu-id="8f00c-204">Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="8f00c-204">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    <span data-ttu-id="8f00c-205">Der oben aufgeführte Web-API-Controller:</span><span class="sxs-lookup"><span data-stu-id="8f00c-205">The preceding web API controller:</span></span>

    * <span data-ttu-id="8f00c-206">Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-206">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="8f00c-207">Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="8f00c-207">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
1. <span data-ttu-id="8f00c-208">Erstellen Sie die App, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="8f00c-208">Build and run the app.</span></span>
1. <span data-ttu-id="8f00c-209">Navigieren Sie zu `http://localhost:<port>/api/books` in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="8f00c-209">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="8f00c-210">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="8f00c-210">The following JSON response is displayed:</span></span>

    ```json
    [
      {
        "id":"5bfd996f7b8e48dc15ff215d",
        "bookName":"Design Patterns",
        "price":54.93,
        "category":"Computers",
        "author":"Ralph Johnson"
      },
      {
        "id":"5bfd996f7b8e48dc15ff215e",
        "bookName":"Clean Code",
        "price":43.15,
        "category":"Computers",
        "author":"Robert C. Martin"
      }
    ]
    ```

## <a name="next-steps"></a><span data-ttu-id="8f00c-211">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="8f00c-211">Next steps</span></span>

<span data-ttu-id="8f00c-212">Weitere Informationen zum Erstellen von ASP.NET-Core Web-APIs finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="8f00c-212">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
