---
title: Erstellen von Web-APIs mit ASP.NET Core und MongoDB
author: prkhandelwal
description: Dieses Tutorial zeigt, wie Sie eine ASP.NET Core Web-API mit einer MongoDB NoSQL-Datenbank erstellen.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 01/31/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: b9bfc9b9b9cefab74548bc90cdda9d31123e1275
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2019
ms.locfileid: "64883255"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="c564a-103">Erstellen einer Web-API mit ASP.NET Core und MongoDB</span><span class="sxs-lookup"><span data-stu-id="c564a-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="c564a-104">Von [Pratik Khandelwal](https://twitter.com/K2Prk) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="c564a-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="c564a-105">Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.</span><span class="sxs-lookup"><span data-stu-id="c564a-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="c564a-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="c564a-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="c564a-107">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="c564a-107">Configure MongoDB</span></span>
> * <span data-ttu-id="c564a-108">Erstellen einer MongoDB-Datenbank</span><span class="sxs-lookup"><span data-stu-id="c564a-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="c564a-109">Definieren einer MongoDB-Sammlung und eines Schemas</span><span class="sxs-lookup"><span data-stu-id="c564a-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="c564a-110">Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API</span><span class="sxs-lookup"><span data-stu-id="c564a-110">Perform MongoDB CRUD operations from a web API</span></span>

<span data-ttu-id="c564a-111">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c564a-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c564a-112">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="c564a-112">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c564a-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c564a-113">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="c564a-114">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c564a-114">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="c564a-115">[Version 15.9 von Visual Studio 2017 oder höher](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) mit der Workload **ASP.NET und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="c564a-115">[Visual Studio 2017 version 15.9 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="c564a-116">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c564a-116">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c564a-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c564a-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="c564a-118">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c564a-118">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="c564a-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c564a-119">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="c564a-120">C# für Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c564a-120">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="c564a-121">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c564a-121">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c564a-122">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c564a-122">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="c564a-123">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="c564a-123">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="c564a-124">Visual Studio für Mac Version 7.7 oder höher</span><span class="sxs-lookup"><span data-stu-id="c564a-124">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="c564a-125">MongoDB</span><span class="sxs-lookup"><span data-stu-id="c564a-125">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="c564a-126">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="c564a-126">Configure MongoDB</span></span>

<span data-ttu-id="c564a-127">Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\\Programme\\MongoDB* installiert.</span><span class="sxs-lookup"><span data-stu-id="c564a-127">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="c564a-128">Fügen Sie der `Path`-Umgebungsvariablen *C:\\Programme\\MongoDB\\Server\\\<Versionsnummer>\\bin* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c564a-128">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="c564a-129">Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.</span><span class="sxs-lookup"><span data-stu-id="c564a-129">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="c564a-130">Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern.</span><span class="sxs-lookup"><span data-stu-id="c564a-130">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="c564a-131">Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="c564a-131">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="c564a-132">Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten.</span><span class="sxs-lookup"><span data-stu-id="c564a-132">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="c564a-133">Beispiel: *C:\\BooksData* auf Windows.</span><span class="sxs-lookup"><span data-stu-id="c564a-133">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="c564a-134">Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="c564a-134">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="c564a-135">Die Mongo-Shell erstellt keine neuen Verzeichnisse.</span><span class="sxs-lookup"><span data-stu-id="c564a-135">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="c564a-136">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="c564a-136">Open a command shell.</span></span> <span data-ttu-id="c564a-137">Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen.</span><span class="sxs-lookup"><span data-stu-id="c564a-137">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="c564a-138">Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.</span><span class="sxs-lookup"><span data-stu-id="c564a-138">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="c564a-139">Öffnen Sie eine andere Befehlsshellinstanz.</span><span class="sxs-lookup"><span data-stu-id="c564a-139">Open another command shell instance.</span></span> <span data-ttu-id="c564a-140">Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="c564a-140">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="c564a-141">Führen Sie in einer Befehlsshell folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="c564a-141">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="c564a-142">Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt.</span><span class="sxs-lookup"><span data-stu-id="c564a-142">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="c564a-143">Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.</span><span class="sxs-lookup"><span data-stu-id="c564a-143">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="c564a-144">Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="c564a-144">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="c564a-145">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="c564a-145">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="c564a-146">Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:</span><span class="sxs-lookup"><span data-stu-id="c564a-146">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="c564a-147">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="c564a-147">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. <span data-ttu-id="c564a-148">Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:</span><span class="sxs-lookup"><span data-stu-id="c564a-148">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="c564a-149">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="c564a-149">The following result is displayed:</span></span>

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

    <span data-ttu-id="c564a-150">Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.</span><span class="sxs-lookup"><span data-stu-id="c564a-150">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="c564a-151">Die Datenbank ist bereit.</span><span class="sxs-lookup"><span data-stu-id="c564a-151">The database is ready.</span></span> <span data-ttu-id="c564a-152">Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="c564a-152">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="c564a-153">Erstellen eines ASP.NET Core-Web-API-Projektes</span><span class="sxs-lookup"><span data-stu-id="c564a-153">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="c564a-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c564a-154">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="c564a-155">Gehen Sie zu **Datei** > **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="c564a-155">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="c564a-156">Wählen Sie **ASP.NET Core-Webanwendung**, benennen Sie das Projekt mit *BooksApi*, und klicken Sie auf **OK**.</span><span class="sxs-lookup"><span data-stu-id="c564a-156">Select **ASP.NET Core Web Application**, name the project *BooksApi*, and click **OK**.</span></span>
1. <span data-ttu-id="c564a-157">Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="c564a-157">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="c564a-158">Wählen Sie die **API**-Projektvorlage aus, und klicken Sie auf **OK**:</span><span class="sxs-lookup"><span data-stu-id="c564a-158">Select the **API** project template, and click **OK**:</span></span>
1. <span data-ttu-id="c564a-159">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="c564a-159">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="c564a-160">Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="c564a-160">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="c564a-161">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="c564a-161">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="c564a-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="c564a-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="c564a-163">Führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="c564a-163">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="c564a-164">Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="c564a-164">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="c564a-165">Klicken Sie auf **Ja**, wenn die Meldung *Die erforderlichen Objekte für die Erstellung und das Debuggen sind in „BooksApi“ nicht vorhanden. Sollen sie hinzugefügt werden?* angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="c564a-165">Click **Yes** when the *Required assets to build and debug are missing from 'BooksApi'. Add them?* notification appears.</span></span>
1. <span data-ttu-id="c564a-166">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="c564a-166">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="c564a-167">Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="c564a-167">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="c564a-168">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="c564a-168">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="c564a-169">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="c564a-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="c564a-170">Navigieren Sie zu **Datei**  > **Neue Lösung** > **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="c564a-170">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="c564a-171">Wählen Sie die **ASP.NET Core-Web-API**-C#-Projektvorlage, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c564a-171">Select the **ASP.NET Core Web API** C# project template, and click **Next**.</span></span>
1. <span data-ttu-id="c564a-172">Wählen Sie **.NET Core 2.2** aus der Dropdownliste **Zielframework**, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="c564a-172">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and click **Next**.</span></span>
1. <span data-ttu-id="c564a-173">Geben Sie *BooksApi* als **Projektnamen** ein, und klicken Sie dann auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="c564a-173">Enter *BooksApi* for the **Project Name**, and click **Create**.</span></span>
1. <span data-ttu-id="c564a-174">Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="c564a-174">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="c564a-175">Geben Sie *MongoDB.Driver* in das Suchfeld ein, wählen Sie das *MongoDB.Driver*-Paket, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="c564a-175">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and click **Add Package**.</span></span>
1. <span data-ttu-id="c564a-176">Klicken Sie auf die Schaltfläche **Zustimmen** im Dialogfeld **Zustimmung zur Lizenz**.</span><span class="sxs-lookup"><span data-stu-id="c564a-176">Click the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-a-model"></a><span data-ttu-id="c564a-177">Hinzufügen eines Modells</span><span class="sxs-lookup"><span data-stu-id="c564a-177">Add a model</span></span>

1. <span data-ttu-id="c564a-178">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c564a-178">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="c564a-179">Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="c564a-179">Add a `Book` class to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

<span data-ttu-id="c564a-180">In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt,</span><span class="sxs-lookup"><span data-stu-id="c564a-180">In the preceding class, the `Id` property:</span></span>

* <span data-ttu-id="c564a-181">um das Common Language Runtime-Objekt (CLR) der MongoDB-Sammlung zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="c564a-181">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
* <span data-ttu-id="c564a-182">Sie wird mit `[BsonId]` versehen, um diese Eigenschaft als Primärschlüssel des Dokuments festzulegen.</span><span class="sxs-lookup"><span data-stu-id="c564a-182">Is annotated with `[BsonId]` to designate this property as the document's primary key.</span></span>
* <span data-ttu-id="c564a-183">Sie wird mit `[BsonRepresentation(BsonType.ObjectId)]` versehen, um die Übergabe des Parameters als Typ `string` anstelle von `ObjectId` zu ermöglichen.</span><span class="sxs-lookup"><span data-stu-id="c564a-183">Is annotated with `[BsonRepresentation(BsonType.ObjectId)]` to allow passing the parameter as type `string` instead of `ObjectId`.</span></span> <span data-ttu-id="c564a-184">Mongo behandelt die Konvertierung von `string` zu `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="c564a-184">Mongo handles the conversion from `string` to `ObjectId`.</span></span>

<span data-ttu-id="c564a-185">Andere Eigenschaften in der Klasse werden mit dem Attribut `[BsonElement]` ergänzt.</span><span class="sxs-lookup"><span data-stu-id="c564a-185">Other properties in the class are annotated with the `[BsonElement]` attribute.</span></span> <span data-ttu-id="c564a-186">Der Wert des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.</span><span class="sxs-lookup"><span data-stu-id="c564a-186">The attribute's value represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-crud-operations-class"></a><span data-ttu-id="c564a-187">Hinzufügen einer CRUD-Vorgangsklasse</span><span class="sxs-lookup"><span data-stu-id="c564a-187">Add a CRUD operations class</span></span>

1. <span data-ttu-id="c564a-188">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.</span><span class="sxs-lookup"><span data-stu-id="c564a-188">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="c564a-189">Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="c564a-189">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. <span data-ttu-id="c564a-190">Fügen Sie der Datei *appsettings.json* eine MongoDB-Verbindungszeichenfolge hinzu:</span><span class="sxs-lookup"><span data-stu-id="c564a-190">Add the MongoDB connection string to *appsettings.json*:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    <span data-ttu-id="c564a-191">Auf die vorhergehende `BookstoreDb`-Eigenschaft wird über den Klassenkonstruktor `BookService` zugegriffen.</span><span class="sxs-lookup"><span data-stu-id="c564a-191">The preceding `BookstoreDb` property is accessed in the `BookService` class constructor.</span></span>

1. <span data-ttu-id="c564a-192">Registrieren Sie unter `Startup.ConfigureServices` die Klasse `BookService` mit dem Dependency Injection-System:</span><span class="sxs-lookup"><span data-stu-id="c564a-192">In `Startup.ConfigureServices`, register the `BookService` class with the Dependency Injection system:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    <span data-ttu-id="c564a-193">Die vorhergehende Dienstregistrierung ist notwendig, um die Konstruktor-Injection in Verbrauchsklassen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="c564a-193">The preceding service registration is necessary to support constructor injection in consuming classes.</span></span>

<span data-ttu-id="c564a-194">Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:</span><span class="sxs-lookup"><span data-stu-id="c564a-194">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="c564a-195">`MongoClient` &ndash; – Liest die Serverinstanz für das Ausführen von Datenbankvorgängen.</span><span class="sxs-lookup"><span data-stu-id="c564a-195">`MongoClient` &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="c564a-196">Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="c564a-196">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="c564a-197">`IMongoDatabase` &ndash; – Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar.</span><span class="sxs-lookup"><span data-stu-id="c564a-197">`IMongoDatabase` &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="c564a-198">Dieses Tutorial verwendet die generische `GetCollection<T>(collection)`-Methode auf der Schnittstelle, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="c564a-198">This tutorial uses the generic `GetCollection<T>(collection)` method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="c564a-199">CRUD-Vorgänge können für die Sammlung ausgeführt werden, nachdem diese Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="c564a-199">CRUD operations can be performed against the collection after this method is called.</span></span> <span data-ttu-id="c564a-200">Rufen Sie in der `GetCollection<T>(collection)`-Methode folgendes auf:</span><span class="sxs-lookup"><span data-stu-id="c564a-200">In the `GetCollection<T>(collection)` method call:</span></span>
  * <span data-ttu-id="c564a-201">`collection` steht für den Sammlungsnamen.</span><span class="sxs-lookup"><span data-stu-id="c564a-201">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="c564a-202">`T` steht für den in der Sammlung gespeicherten CLR-Objekttypen.</span><span class="sxs-lookup"><span data-stu-id="c564a-202">`T` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="c564a-203">`GetCollection<T>(collection)` gibt ein `MongoCollection`-Objekt zurück, das die Sammlung darstellt.</span><span class="sxs-lookup"><span data-stu-id="c564a-203">`GetCollection<T>(collection)` returns a `MongoCollection` object representing the collection.</span></span> <span data-ttu-id="c564a-204">In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="c564a-204">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="c564a-205">`Find<T>` &ndash; Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.</span><span class="sxs-lookup"><span data-stu-id="c564a-205">`Find<T>` &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="c564a-206">`InsertOne` &ndash; Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.</span><span class="sxs-lookup"><span data-stu-id="c564a-206">`InsertOne` &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="c564a-207">`ReplaceOne` &ndash; Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.</span><span class="sxs-lookup"><span data-stu-id="c564a-207">`ReplaceOne` &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>
* <span data-ttu-id="c564a-208">`DeleteOne` &ndash; Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.</span><span class="sxs-lookup"><span data-stu-id="c564a-208">`DeleteOne` &ndash; Deletes a single document matching the provided search criteria.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="c564a-209">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="c564a-209">Add a controller</span></span>

1. <span data-ttu-id="c564a-210">Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="c564a-210">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    <span data-ttu-id="c564a-211">Der oben aufgeführte Web-API-Controller:</span><span class="sxs-lookup"><span data-stu-id="c564a-211">The preceding web API controller:</span></span>

    * <span data-ttu-id="c564a-212">Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="c564a-212">Uses the `BookService` class to perform CRUD operations.</span></span>
    * <span data-ttu-id="c564a-213">Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="c564a-213">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
    * <span data-ttu-id="c564a-214">Die <xref:System.Web.Http.ApiController.CreatedAtRoute*>-Methode gibt die Antwort „201“ zurück, bei der es sich um die Standardantwort für eine HTTP-POST-Methode handelt, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="c564a-214">The <xref:System.Web.Http.ApiController.CreatedAtRoute*> method returns a 201 response, which is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="c564a-215">`CreatedAtRoute` fügt der Antwort außerdem einen Adressheader hinzu.</span><span class="sxs-lookup"><span data-stu-id="c564a-215">`CreatedAtRoute` also adds a Location header to the response.</span></span> <span data-ttu-id="c564a-216">Der Adressheader gibt den URI des neu erstellten To-Do-Elements zurück.</span><span class="sxs-lookup"><span data-stu-id="c564a-216">The Location header specifies the URI of the newly created to-do item.</span></span> <span data-ttu-id="c564a-217">Siehe [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="c564a-217">See [10.2.2 201 Created](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>
1. <span data-ttu-id="c564a-218">Erstellen Sie die App, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="c564a-218">Build and run the app.</span></span>
1. <span data-ttu-id="c564a-219">Navigieren Sie zu `http://localhost:<port>/api/books` in Ihrem Browser.</span><span class="sxs-lookup"><span data-stu-id="c564a-219">Navigate to `http://localhost:<port>/api/books` in your browser.</span></span> <span data-ttu-id="c564a-220">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="c564a-220">The following JSON response is displayed:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="c564a-221">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="c564a-221">Next steps</span></span>

<span data-ttu-id="c564a-222">Weitere Informationen zum Erstellen von ASP.NET-Core Web-APIs finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="c564a-222">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="c564a-223">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="c564a-223">Youtube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
