---
title: Erstellen einer Web-API mit ASP.NET Core und MongoDB
author: prkhandelwal
description: In diesem Tutorial wird beschrieben, wie Sie eine ASP.NET Core-Web-API mit einer MongoDB NoSQL-Datenbank erstellen.
ms.author: scaddie
ms.custom: mvc, seodec18
ms.date: 07/10/2019
uid: tutorials/first-mongo-app
ms.openlocfilehash: 99b28407a249a5c0bc6a0cf3a285c04f1d6187a7
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815648"
---
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a><span data-ttu-id="56e40-103">Erstellen einer Web-API mit ASP.NET Core und MongoDB</span><span class="sxs-lookup"><span data-stu-id="56e40-103">Create a web API with ASP.NET Core and MongoDB</span></span>

<span data-ttu-id="56e40-104">Von [Pratik Khandelwal](https://twitter.com/K2Prk) und [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="56e40-104">By [Pratik Khandelwal](https://twitter.com/K2Prk) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="56e40-105">Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.</span><span class="sxs-lookup"><span data-stu-id="56e40-105">This tutorial creates a web API that performs Create, Read, Update, and Delete (CRUD) operations on a [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL database.</span></span>

<span data-ttu-id="56e40-106">In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:</span><span class="sxs-lookup"><span data-stu-id="56e40-106">In this tutorial, you learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="56e40-107">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="56e40-107">Configure MongoDB</span></span>
> * <span data-ttu-id="56e40-108">Erstellen einer MongoDB-Datenbank</span><span class="sxs-lookup"><span data-stu-id="56e40-108">Create a MongoDB database</span></span>
> * <span data-ttu-id="56e40-109">Definieren einer MongoDB-Sammlung und eines Schemas</span><span class="sxs-lookup"><span data-stu-id="56e40-109">Define a MongoDB collection and schema</span></span>
> * <span data-ttu-id="56e40-110">Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API</span><span class="sxs-lookup"><span data-stu-id="56e40-110">Perform MongoDB CRUD operations from a web API</span></span>
> * <span data-ttu-id="56e40-111">Anpassen der JSON-Serialisierung</span><span class="sxs-lookup"><span data-stu-id="56e40-111">Customize JSON serialization</span></span>

<span data-ttu-id="56e40-112">[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="56e40-112">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="56e40-113">Erforderliche Komponenten</span><span class="sxs-lookup"><span data-stu-id="56e40-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e40-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e40-114">Visual Studio</span></span>](#tab/visual-studio)

* [<span data-ttu-id="56e40-115">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="56e40-115">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* <span data-ttu-id="56e40-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) mit der Workload **ASP.NET- und Webentwicklung**</span><span class="sxs-lookup"><span data-stu-id="56e40-116">[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [<span data-ttu-id="56e40-117">MongoDB</span><span class="sxs-lookup"><span data-stu-id="56e40-117">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e40-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e40-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

* [<span data-ttu-id="56e40-119">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="56e40-119">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="56e40-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e40-120">Visual Studio Code</span></span>](https://code.visualstudio.com/download)
* [<span data-ttu-id="56e40-121">C# für Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e40-121">C# for Visual Studio Code</span></span>](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [<span data-ttu-id="56e40-122">MongoDB</span><span class="sxs-lookup"><span data-stu-id="56e40-122">MongoDB</span></span>](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e40-123">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="56e40-123">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="56e40-124">.NET Core SDK 2.2 oder höher</span><span class="sxs-lookup"><span data-stu-id="56e40-124">.NET Core SDK 2.2 or later</span></span>](https://www.microsoft.com/net/download/all)
* [<span data-ttu-id="56e40-125">Visual Studio für Mac Version 7.7 oder höher</span><span class="sxs-lookup"><span data-stu-id="56e40-125">Visual Studio for Mac version 7.7 or later</span></span>](https://visualstudio.microsoft.com/downloads/)
* [<span data-ttu-id="56e40-126">MongoDB</span><span class="sxs-lookup"><span data-stu-id="56e40-126">MongoDB</span></span>](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a><span data-ttu-id="56e40-127">Konfigurieren von MongoDB</span><span class="sxs-lookup"><span data-stu-id="56e40-127">Configure MongoDB</span></span>

<span data-ttu-id="56e40-128">Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\\Programme\\MongoDB* installiert.</span><span class="sxs-lookup"><span data-stu-id="56e40-128">If using Windows, MongoDB is installed at *C:\\Program Files\\MongoDB* by default.</span></span> <span data-ttu-id="56e40-129">Fügen Sie der `Path`-Umgebungsvariablen *C:\\Programme\\MongoDB\\Server\\\<Versionsnummer>\\bin* hinzu.</span><span class="sxs-lookup"><span data-stu-id="56e40-129">Add *C:\\Program Files\\MongoDB\\Server\\\<version_number>\\bin* to the `Path` environment variable.</span></span> <span data-ttu-id="56e40-130">Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.</span><span class="sxs-lookup"><span data-stu-id="56e40-130">This change enables MongoDB access from anywhere on your development machine.</span></span>

<span data-ttu-id="56e40-131">Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern.</span><span class="sxs-lookup"><span data-stu-id="56e40-131">Use the mongo Shell in the following steps to create a database, make collections, and store documents.</span></span> <span data-ttu-id="56e40-132">Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span><span class="sxs-lookup"><span data-stu-id="56e40-132">For more information on mongo Shell commands, see [Working with the mongo Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).</span></span>

1. <span data-ttu-id="56e40-133">Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten.</span><span class="sxs-lookup"><span data-stu-id="56e40-133">Choose a directory on your development machine for storing the data.</span></span> <span data-ttu-id="56e40-134">Beispiel: *C:\\BooksData* auf Windows.</span><span class="sxs-lookup"><span data-stu-id="56e40-134">For example, *C:\\BooksData* on Windows.</span></span> <span data-ttu-id="56e40-135">Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist.</span><span class="sxs-lookup"><span data-stu-id="56e40-135">Create the directory if it doesn't exist.</span></span> <span data-ttu-id="56e40-136">Die Mongo-Shell erstellt keine neuen Verzeichnisse.</span><span class="sxs-lookup"><span data-stu-id="56e40-136">The mongo Shell doesn't create new directories.</span></span>
1. <span data-ttu-id="56e40-137">Öffnen Sie eine Befehlsshell.</span><span class="sxs-lookup"><span data-stu-id="56e40-137">Open a command shell.</span></span> <span data-ttu-id="56e40-138">Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen.</span><span class="sxs-lookup"><span data-stu-id="56e40-138">Run the following command to connect to MongoDB on default port 27017.</span></span> <span data-ttu-id="56e40-139">Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.</span><span class="sxs-lookup"><span data-stu-id="56e40-139">Remember to replace `<data_directory_path>` with the directory you chose in the previous step.</span></span>

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. <span data-ttu-id="56e40-140">Öffnen Sie eine andere Befehlsshellinstanz.</span><span class="sxs-lookup"><span data-stu-id="56e40-140">Open another command shell instance.</span></span> <span data-ttu-id="56e40-141">Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="56e40-141">Connect to the default test database by running the following command:</span></span>

    ```console
    mongo
    ```

1. <span data-ttu-id="56e40-142">Führen Sie in einer Befehlsshell folgenden Befehl aus:</span><span class="sxs-lookup"><span data-stu-id="56e40-142">Run the following in a command shell:</span></span>

    ```console
    use BookstoreDb
    ```

    <span data-ttu-id="56e40-143">Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt.</span><span class="sxs-lookup"><span data-stu-id="56e40-143">If it doesn't already exist, a database named *BookstoreDb* is created.</span></span> <span data-ttu-id="56e40-144">Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.</span><span class="sxs-lookup"><span data-stu-id="56e40-144">If the database does exist, its connection is opened for transactions.</span></span>

1. <span data-ttu-id="56e40-145">Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:</span><span class="sxs-lookup"><span data-stu-id="56e40-145">Create a `Books` collection using following command:</span></span>

    ```console
    db.createCollection('Books')
    ```

    <span data-ttu-id="56e40-146">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="56e40-146">The following result is displayed:</span></span>

    ```console
    { "ok" : 1 }
    ```

1. <span data-ttu-id="56e40-147">Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:</span><span class="sxs-lookup"><span data-stu-id="56e40-147">Define a schema for the `Books` collection and insert two documents using the following command:</span></span>

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    <span data-ttu-id="56e40-148">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="56e40-148">The following result is displayed:</span></span>

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```
  
  > [!NOTE]
  > <span data-ttu-id="56e40-149">Beim Ausführen dieses Beispiels stimmen die in diesem Artikel angezeigten IDs nicht mit den IDs überein.</span><span class="sxs-lookup"><span data-stu-id="56e40-149">The ID's shown in this article will not match the IDs when you run this sample.</span></span>

1. <span data-ttu-id="56e40-150">Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:</span><span class="sxs-lookup"><span data-stu-id="56e40-150">View the documents in the database using the following command:</span></span>

    ```console
    db.Books.find({}).pretty()
    ```

    <span data-ttu-id="56e40-151">Das folgende Ergebnis wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="56e40-151">The following result is displayed:</span></span>

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

    <span data-ttu-id="56e40-152">Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.</span><span class="sxs-lookup"><span data-stu-id="56e40-152">The schema adds an autogenerated `_id` property of type `ObjectId` for each document.</span></span>

<span data-ttu-id="56e40-153">Die Datenbank ist bereit.</span><span class="sxs-lookup"><span data-stu-id="56e40-153">The database is ready.</span></span> <span data-ttu-id="56e40-154">Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="56e40-154">You can start creating the ASP.NET Core web API.</span></span>

## <a name="create-the-aspnet-core-web-api-project"></a><span data-ttu-id="56e40-155">Erstellen eines ASP.NET Core-Web-API-Projektes</span><span class="sxs-lookup"><span data-stu-id="56e40-155">Create the ASP.NET Core web API project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="56e40-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="56e40-156">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="56e40-157">Gehen Sie zu **Datei** > **Neu** > **Projekt**.</span><span class="sxs-lookup"><span data-stu-id="56e40-157">Go to **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="56e40-158">Wählen Sie den Projekttyp **ASP.NET Core-Webanwendung** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="56e40-158">Select the **ASP.NET Core Web Application** project type, and select **Next**.</span></span>
1. <span data-ttu-id="56e40-159">Geben Sie dem Projekt den Namen *BooksApi*, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="56e40-159">Name the project *BooksApi*, and select **Create**.</span></span>
1. <span data-ttu-id="56e40-160">Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 2.2**.</span><span class="sxs-lookup"><span data-stu-id="56e40-160">Select the **.NET Core** target framework and **ASP.NET Core 2.2**.</span></span> <span data-ttu-id="56e40-161">Wählen Sie die Projektvorlage **API** aus, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="56e40-161">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="56e40-162">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="56e40-162">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="56e40-163">Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="56e40-163">In the **Package Manager Console** window, navigate to the project root.</span></span> <span data-ttu-id="56e40-164">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="56e40-164">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```powershell
    Install-Package MongoDB.Driver -Version {VERSION}
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="56e40-165">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="56e40-165">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="56e40-166">Führen Sie die folgenden Befehle in einer Befehlsshell aus:</span><span class="sxs-lookup"><span data-stu-id="56e40-166">Run the following commands in a command shell:</span></span>

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    <span data-ttu-id="56e40-167">Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.</span><span class="sxs-lookup"><span data-stu-id="56e40-167">A new ASP.NET Core web API project targeting .NET Core is generated and opened in Visual Studio Code.</span></span>

1. <span data-ttu-id="56e40-168">Sobald das OmniSharp-Flammensymbol in der Statusleiste grün angezeigt wird, wird ein Dialogfeld mit folgender Meldung angezeigt: **Required assets to build and debug are missing from 'BooksApi' (Die erforderlichen Objekte zum Erstellen und Debuggen sind in „BooksApi“ nicht vorhanden). Add them? (Sollen sie hinzugefügt werden?)** .</span><span class="sxs-lookup"><span data-stu-id="56e40-168">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'BooksApi'. Add them?**.</span></span> <span data-ttu-id="56e40-169">Wählen Sie **Ja**.</span><span class="sxs-lookup"><span data-stu-id="56e40-169">Select **Yes**.</span></span>
1. <span data-ttu-id="56e40-170">Besuchen Sie den [NuGet-Katalog: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/), um die neueste stabile Version des .NET-Treibers für MongoDB zu ermitteln.</span><span class="sxs-lookup"><span data-stu-id="56e40-170">Visit the [NuGet Gallery: MongoDB.Driver](https://www.nuget.org/packages/MongoDB.Driver/) to determine the latest stable version of the .NET driver for MongoDB.</span></span> <span data-ttu-id="56e40-171">Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts.</span><span class="sxs-lookup"><span data-stu-id="56e40-171">Open **Integrated Terminal** and navigate to the project root.</span></span> <span data-ttu-id="56e40-172">Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:</span><span class="sxs-lookup"><span data-stu-id="56e40-172">Run the following command to install the .NET driver for MongoDB:</span></span>

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v {VERSION}
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="56e40-173">Visual Studio für Mac</span><span class="sxs-lookup"><span data-stu-id="56e40-173">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="56e40-174">Navigieren Sie zu **Datei**  > **Neue Lösung** >  **.NET Core** > **App**.</span><span class="sxs-lookup"><span data-stu-id="56e40-174">Go to **File** > **New Solution** > **.NET Core** > **App**.</span></span>
1. <span data-ttu-id="56e40-175">Wählen Sie die C#-Projektvorlage **ASP.NET Core-Web-API**aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="56e40-175">Select the **ASP.NET Core Web API** C# project template, and select **Next**.</span></span>
1. <span data-ttu-id="56e40-176">Wählen Sie aus der Dropdownliste **Zielframework** die Option **.NET Core 2.2** aus, und klicken Sie auf **Weiter**.</span><span class="sxs-lookup"><span data-stu-id="56e40-176">Select **.NET Core 2.2** from the **Target Framework** drop-down list, and select **Next**.</span></span>
1. <span data-ttu-id="56e40-177">Geben Sie als **Projektnamen** *BooksApi* ein, und klicken Sie auf **Erstellen**.</span><span class="sxs-lookup"><span data-stu-id="56e40-177">Enter *BooksApi* for the **Project Name**, and select **Create**.</span></span>
1. <span data-ttu-id="56e40-178">Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="56e40-178">In the **Solution** pad, right-click the project's **Dependencies** node and select **Add Packages**.</span></span>
1. <span data-ttu-id="56e40-179">Geben Sie in das Suchfeld *MongoDB.Driver* ein, wählen Sie das Paket *MongoDB.Driver* aus, und klicken Sie auf **Paket hinzufügen**.</span><span class="sxs-lookup"><span data-stu-id="56e40-179">Enter *MongoDB.Driver* in the search box, select the *MongoDB.Driver* package, and select **Add Package**.</span></span>
1. <span data-ttu-id="56e40-180">Klicken Sie im Dialogfeld **Zustimmung zur Lizenz** auf die Schaltfläche **Annehmen**.</span><span class="sxs-lookup"><span data-stu-id="56e40-180">Select the **Accept** button in the **License Acceptance** dialog.</span></span>

---

## <a name="add-an-entity-model"></a><span data-ttu-id="56e40-181">Hinzufügen eines Entitätsmodells</span><span class="sxs-lookup"><span data-stu-id="56e40-181">Add an entity model</span></span>

1. <span data-ttu-id="56e40-182">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.</span><span class="sxs-lookup"><span data-stu-id="56e40-182">Add a *Models* directory to the project root.</span></span>
1. <span data-ttu-id="56e40-183">Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="56e40-183">Add a `Book` class to the *Models* directory with the following code:</span></span>

    ```csharp
    using MongoDB.Bson;
    using MongoDB.Bson.Serialization.Attributes;
    
    namespace BooksApi.Models
    {
        public class Book
        {
            [BsonId]
            [BsonRepresentation(BsonType.ObjectId)]
            public string Id { get; set; }
    
            [BsonElement("Name")]
            public string BookName { get; set; }
    
            public decimal Price { get; set; }
    
            public string Category { get; set; }
    
            public string Author { get; set; }
        }
    }
    ```

    <span data-ttu-id="56e40-184">In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt,</span><span class="sxs-lookup"><span data-stu-id="56e40-184">In the preceding class, the `Id` property:</span></span>
    
    * <span data-ttu-id="56e40-185">um das Common Language Runtime-Objekt (CLR) der MongoDB-Sammlung zuzuordnen.</span><span class="sxs-lookup"><span data-stu-id="56e40-185">Is required for mapping the Common Language Runtime (CLR) object to the MongoDB collection.</span></span>
    * <span data-ttu-id="56e40-186">Sie wird mit [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) versehen, um diese Eigenschaft als Primärschlüssel des Dokuments festzulegen.</span><span class="sxs-lookup"><span data-stu-id="56e40-186">Is annotated with [[BsonId]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonIdAttribute.htm) to designate this property as the document's primary key.</span></span>
    * <span data-ttu-id="56e40-187">Sie wird mit [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) versehen, damit der Parameter als Typ `string` und nicht als [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm)-Struktur übergeben werden kann.</span><span class="sxs-lookup"><span data-stu-id="56e40-187">Is annotated with [[BsonRepresentation(BsonType.ObjectId)]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonRepresentationAttribute.htm) to allow passing the parameter as type `string` instead of an [ObjectId](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_ObjectId.htm) structure.</span></span> <span data-ttu-id="56e40-188">Mongo behandelt die Konvertierung von `string` zu `ObjectId`.</span><span class="sxs-lookup"><span data-stu-id="56e40-188">Mongo handles the conversion from `string` to `ObjectId`.</span></span>
    
    <span data-ttu-id="56e40-189">Die Eigenschaft `BookName` ist mit dem Attribut [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) versehen.</span><span class="sxs-lookup"><span data-stu-id="56e40-189">The `BookName` property is annotated with the [[BsonElement]](https://api.mongodb.com/csharp/current/html/T_MongoDB_Bson_Serialization_Attributes_BsonElementAttribute.htm) attribute.</span></span> <span data-ttu-id="56e40-190">Der Wert `Name` des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.</span><span class="sxs-lookup"><span data-stu-id="56e40-190">The attribute's value of `Name` represents the property name in the MongoDB collection.</span></span>

## <a name="add-a-configuration-model"></a><span data-ttu-id="56e40-191">Hinzufügen eines Konfigurationsmodells</span><span class="sxs-lookup"><span data-stu-id="56e40-191">Add a configuration model</span></span>

1. <span data-ttu-id="56e40-192">Fügen Sie *appsettings.json* die folgenden Konfigurationswerte für die Datenbank hinzu:</span><span class="sxs-lookup"><span data-stu-id="56e40-192">Add the following database configuration values to *appsettings.json*:</span></span>

    [!code-json[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-6)]

1. <span data-ttu-id="56e40-193">Fügen Sie mit dem folgenden Code dem Verzeichnis *Modelle* die Datei *BookstoreDatabaseSettings.cs* hinzu:</span><span class="sxs-lookup"><span data-stu-id="56e40-193">Add a *BookstoreDatabaseSettings.cs* file to the *Models* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/BookstoreDatabaseSettings.cs)]

    <span data-ttu-id="56e40-194">Mit der vorhergehenden Klasse `BookstoreDatabaseSettings` werden die Eigenschaftswerte `BookstoreDatabaseSettings` der Datei *appsettings.json* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="56e40-194">The preceding `BookstoreDatabaseSettings` class is used to store the *appsettings.json* file's `BookstoreDatabaseSettings` property values.</span></span> <span data-ttu-id="56e40-195">Die Namen der JSON- und der C#-Eigenschaften sind identisch, um den Zuordnungsprozess zu erleichtern.</span><span class="sxs-lookup"><span data-stu-id="56e40-195">The JSON and C# property names are named identically to ease the mapping process.</span></span>

1. <span data-ttu-id="56e40-196">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="56e40-196">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddDbSettings.cs?highlight=3-7)]

    <span data-ttu-id="56e40-197">Für den Code oben gilt:</span><span class="sxs-lookup"><span data-stu-id="56e40-197">In the preceding code:</span></span>

    * <span data-ttu-id="56e40-198">Die Konfigurationsinstanz, an die der Abschnitt `BookstoreDatabaseSettings` der Datei *appsettings.json* gebunden ist, ist beim Abhängigkeitsinjektionscontainer (DI, Dependency Injection) registriert.</span><span class="sxs-lookup"><span data-stu-id="56e40-198">The configuration instance to which the *appsettings.json* file's `BookstoreDatabaseSettings` section binds is registered in the Dependency Injection (DI) container.</span></span> <span data-ttu-id="56e40-199">D. h. die Eigenschaft `ConnectionString` eines `BookstoreDatabaseSettings`-Objekts wird beispielsweise mit der Eigenschaft `BookstoreDatabaseSettings:ConnectionString` in *appsettings.json* aufgefüllt.</span><span class="sxs-lookup"><span data-stu-id="56e40-199">For example, a `BookstoreDatabaseSettings` object's `ConnectionString` property is populated with the `BookstoreDatabaseSettings:ConnectionString` property in *appsettings.json*.</span></span>
    * <span data-ttu-id="56e40-200">Die Schnittstelle `IBookstoreDatabaseSettings` ist bei DI mit der Lebensdauer eines [Singletondiensts](xref:fundamentals/dependency-injection#service-lifetimes) registriert.</span><span class="sxs-lookup"><span data-stu-id="56e40-200">The `IBookstoreDatabaseSettings` interface is registered in DI with a singleton [service lifetime](xref:fundamentals/dependency-injection#service-lifetimes).</span></span> <span data-ttu-id="56e40-201">Beim Einfügen wird die Schnittstelleninstanz in ein `BookstoreDatabaseSettings`-Objekt aufgelöst.</span><span class="sxs-lookup"><span data-stu-id="56e40-201">When injected, the interface instance resolves to a `BookstoreDatabaseSettings` object.</span></span>

1. <span data-ttu-id="56e40-202">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um die Verweise `BookstoreDatabaseSettings` und `IBookstoreDatabaseSettings` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="56e40-202">Add the following code to the top of *Startup.cs* to resolve the `BookstoreDatabaseSettings` and `IBookstoreDatabaseSettings` references:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiModels)]

## <a name="add-a-crud-operations-service"></a><span data-ttu-id="56e40-203">Hinzufügen eines CRUD-Vorgangsdiensts</span><span class="sxs-lookup"><span data-stu-id="56e40-203">Add a CRUD operations service</span></span>

1. <span data-ttu-id="56e40-204">Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.</span><span class="sxs-lookup"><span data-stu-id="56e40-204">Add a *Services* directory to the project root.</span></span>
1. <span data-ttu-id="56e40-205">Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="56e40-205">Add a `BookService` class to the *Services* directory with the following code:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

    <span data-ttu-id="56e40-206">Im vorhergehenden Code wird eine `IBookstoreDatabaseSettings`-Instanz mittels Konstruktorinjektion über DI abgerufen.</span><span class="sxs-lookup"><span data-stu-id="56e40-206">In the preceding code, an `IBookstoreDatabaseSettings` instance is retrieved from DI via constructor injection.</span></span> <span data-ttu-id="56e40-207">Damit kann auf die Konfigurationswerte von *appsettings.json* zugegriffen werden, die im Abschnitt [Hinzufügen eines Konfigurationsmodells](#add-a-configuration-model) hinzugefügt wurden.</span><span class="sxs-lookup"><span data-stu-id="56e40-207">This technique provides access to the *appsettings.json* configuration values that were added in the [Add a configuration model](#add-a-configuration-model) section.</span></span>

1. <span data-ttu-id="56e40-208">Fügen Sie folgenden hervorgehobenen Code zu `Startup.ConfigureServices` hinzu:</span><span class="sxs-lookup"><span data-stu-id="56e40-208">Add the following highlighted code to `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](first-mongo-app/sample_snapshot/BooksApi/Startup.ConfigureServices.AddSingletonService.cs?highlight=9)]

    <span data-ttu-id="56e40-209">Im vorhergehenden Code wird die Klasse `BookService` bei DI registriert, um die Konstruktorinjektion in verarbeitenden Klassen zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="56e40-209">In the preceding code, the `BookService` class is registered with DI to support constructor injection in consuming classes.</span></span> <span data-ttu-id="56e40-210">Die Lebensdauer des Singletondiensts ist am besten geeignet, da `BookService` direkt von `MongoClient` abhängig ist.</span><span class="sxs-lookup"><span data-stu-id="56e40-210">The singleton service lifetime is most appropriate because `BookService` takes a direct dependency on `MongoClient`.</span></span> <span data-ttu-id="56e40-211">Gemäß den offiziellen [Mongo Client-Richtlinien zur Wiederverwendung (Mongo Client reuse guidelines)](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use) sollte `MongoClient` bei DI mit der Lebensdauer eines Singletondiensts registriert werden.</span><span class="sxs-lookup"><span data-stu-id="56e40-211">Per the official [Mongo Client reuse guidelines](https://mongodb.github.io/mongo-csharp-driver/2.8/reference/driver/connecting/#re-use), `MongoClient` should be registered in DI with a singleton service lifetime.</span></span>

1. <span data-ttu-id="56e40-212">Fügen Sie den folgenden Code am Anfang der Datei *Startup.cs* hinzu, um den Verweis `BookService` aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="56e40-212">Add the following code to the top of *Startup.cs* to resolve the `BookService` reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_UsingBooksApiServices)]

<span data-ttu-id="56e40-213">Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:</span><span class="sxs-lookup"><span data-stu-id="56e40-213">The `BookService` class uses the following `MongoDB.Driver` members to perform CRUD operations against the database:</span></span>

* <span data-ttu-id="56e40-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm): Liest die Serverinstanz zum Ausführen von Datenbankvorgängen.</span><span class="sxs-lookup"><span data-stu-id="56e40-214">[MongoClient](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoClient.htm) &ndash; Reads the server instance for performing database operations.</span></span> <span data-ttu-id="56e40-215">Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:</span><span class="sxs-lookup"><span data-stu-id="56e40-215">The constructor of this class is provided the MongoDB connection string:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* <span data-ttu-id="56e40-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm): Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar.</span><span class="sxs-lookup"><span data-stu-id="56e40-216">[IMongoDatabase](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_IMongoDatabase.htm) &ndash; Represents the Mongo database for performing operations.</span></span> <span data-ttu-id="56e40-217">In diesem Tutorial wird die generische Methode [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) für die Schnittstelle verwendet, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="56e40-217">This tutorial uses the generic [GetCollection\<TDocument>(collection)](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoDatabase_GetCollection__1.htm) method on the interface to gain access to data in a specific collection.</span></span> <span data-ttu-id="56e40-218">Führen Sie CRUD-Vorgänge für die Sammlung aus, nachdem diese Methode aufgerufen wurde.</span><span class="sxs-lookup"><span data-stu-id="56e40-218">Perform CRUD operations against the collection after this method is called.</span></span> <span data-ttu-id="56e40-219">Rufen Sie in der `GetCollection<TDocument>(collection)`-Methode folgendes auf:</span><span class="sxs-lookup"><span data-stu-id="56e40-219">In the `GetCollection<TDocument>(collection)` method call:</span></span>
  * <span data-ttu-id="56e40-220">`collection` steht für den Sammlungsnamen.</span><span class="sxs-lookup"><span data-stu-id="56e40-220">`collection` represents the collection name.</span></span>
  * <span data-ttu-id="56e40-221">`TDocument` steht für den in der Sammlung gespeicherten CLR-Objekttypen.</span><span class="sxs-lookup"><span data-stu-id="56e40-221">`TDocument` represents the CLR object type stored in the collection.</span></span>

<span data-ttu-id="56e40-222">`GetCollection<TDocument>(collection)` gibt ein [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm)-Objekt zurück, das die Sammlung darstellt.</span><span class="sxs-lookup"><span data-stu-id="56e40-222">`GetCollection<TDocument>(collection)` returns a [MongoCollection](https://api.mongodb.com/csharp/current/html/T_MongoDB_Driver_MongoCollection.htm) object representing the collection.</span></span> <span data-ttu-id="56e40-223">In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:</span><span class="sxs-lookup"><span data-stu-id="56e40-223">In this tutorial, the following methods are invoked on the collection:</span></span>

* <span data-ttu-id="56e40-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm): Löscht das einzelne Dokument, das den angegebenen Suchkriterien entspricht.</span><span class="sxs-lookup"><span data-stu-id="56e40-224">[DeleteOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_DeleteOne.htm) &ndash; Deletes a single document matching the provided search criteria.</span></span>
* <span data-ttu-id="56e40-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm): Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.</span><span class="sxs-lookup"><span data-stu-id="56e40-225">[Find\<TDocument>](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollectionExtensions_Find__1_1.htm) &ndash; Returns all documents in the collection matching the provided search criteria.</span></span>
* <span data-ttu-id="56e40-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm): Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.</span><span class="sxs-lookup"><span data-stu-id="56e40-226">[InsertOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_InsertOne.htm) &ndash; Inserts the provided object as a new document in the collection.</span></span>
* <span data-ttu-id="56e40-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm): Ersetzt das einzelne Dokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.</span><span class="sxs-lookup"><span data-stu-id="56e40-227">[ReplaceOne](https://api.mongodb.com/csharp/current/html/M_MongoDB_Driver_IMongoCollection_1_ReplaceOne.htm) &ndash; Replaces the single document matching the provided search criteria with the provided object.</span></span>

## <a name="add-a-controller"></a><span data-ttu-id="56e40-228">Hinzufügen eines Controllers</span><span class="sxs-lookup"><span data-stu-id="56e40-228">Add a controller</span></span>

<span data-ttu-id="56e40-229">Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:</span><span class="sxs-lookup"><span data-stu-id="56e40-229">Add a `BooksController` class to the *Controllers* directory with the following code:</span></span>

[!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

<span data-ttu-id="56e40-230">Der oben aufgeführte Web-API-Controller:</span><span class="sxs-lookup"><span data-stu-id="56e40-230">The preceding web API controller:</span></span>

* <span data-ttu-id="56e40-231">Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.</span><span class="sxs-lookup"><span data-stu-id="56e40-231">Uses the `BookService` class to perform CRUD operations.</span></span>
* <span data-ttu-id="56e40-232">Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="56e40-232">Contains action methods to support GET, POST, PUT, and DELETE HTTP requests.</span></span>
* <span data-ttu-id="56e40-233">Ruft <xref:System.Web.Http.ApiController.CreatedAtRoute*> in der Aktionsmethode `Create` auf, um eine [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)-Antwort zurückzugeben.</span><span class="sxs-lookup"><span data-stu-id="56e40-233">Calls <xref:System.Web.Http.ApiController.CreatedAtRoute*> in the `Create` action method to return an [HTTP 201](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) response.</span></span> <span data-ttu-id="56e40-234">Der Statuscode 201 ist die Standardantwort für eine HTTP POST-Methode, die eine neue Ressource auf dem Server erstellt.</span><span class="sxs-lookup"><span data-stu-id="56e40-234">Status code 201 is the standard response for an HTTP POST method that creates a new resource on the server.</span></span> <span data-ttu-id="56e40-235">`CreatedAtRoute` fügt der Antwort außerdem einen `Location`-Header hinzu.</span><span class="sxs-lookup"><span data-stu-id="56e40-235">`CreatedAtRoute` also adds a `Location` header to the response.</span></span> <span data-ttu-id="56e40-236">Der `Location`-Header gibt den URI des neu erstellten Buchs an.</span><span class="sxs-lookup"><span data-stu-id="56e40-236">The `Location` header specifies the URI of the newly created book.</span></span>

## <a name="test-the-web-api"></a><span data-ttu-id="56e40-237">Testen der Web-API</span><span class="sxs-lookup"><span data-stu-id="56e40-237">Test the web API</span></span>

1. <span data-ttu-id="56e40-238">Erstellen Sie die App, und führen Sie sie aus.</span><span class="sxs-lookup"><span data-stu-id="56e40-238">Build and run the app.</span></span>

1. <span data-ttu-id="56e40-239">Wechseln Sie zu `http://localhost:<port>/api/books`, um die parameterlose Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="56e40-239">Navigate to `http://localhost:<port>/api/books` to test the controller's parameterless `Get` action method.</span></span> <span data-ttu-id="56e40-240">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="56e40-240">The following JSON response is displayed:</span></span>

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

1. <span data-ttu-id="56e40-241">Wechseln Sie zu `http://localhost:<port>/api/books/{id here}`, um die überladene Aktionsmethode `Get` des Controllers zu testen.</span><span class="sxs-lookup"><span data-stu-id="56e40-241">Navigate to `http://localhost:<port>/api/books/{id here}` to test the controller's overloaded `Get` action method.</span></span> <span data-ttu-id="56e40-242">Die folgende JSON-Antwort wird angezeigt:</span><span class="sxs-lookup"><span data-stu-id="56e40-242">The following JSON response is displayed:</span></span>

    ```json
    {
      "id":"{ID}",
      "bookName":"Clean Code",
      "price":43.15,
      "category":"Computers",
      "author":"Robert C. Martin"
    }
    ```

## <a name="configure-json-serialization-options"></a><span data-ttu-id="56e40-243">Konfigurieren von JSON-Serialisierungsoptionen</span><span class="sxs-lookup"><span data-stu-id="56e40-243">Configure JSON serialization options</span></span>

<span data-ttu-id="56e40-244">Es gibt zwei Details, die bei JSON-Antworten zu ändern sind, die im Abschnitt [Testen der Web-API](#test-the-web-api) zurückgegeben werden:</span><span class="sxs-lookup"><span data-stu-id="56e40-244">There are two details to change about the JSON responses returned in the [Test the web API](#test-the-web-api) section:</span></span>

* <span data-ttu-id="56e40-245">Die Camel-Case-Standardschreibweise von Eigenschaftsnamen sollte so geändert werden, dass sie der Pascal-Schreibweise der Eigenschaften des CLR-Objekts entspricht.</span><span class="sxs-lookup"><span data-stu-id="56e40-245">The property names' default camel casing should be changed to match the Pascal casing of the CLR object's property names.</span></span>
* <span data-ttu-id="56e40-246">Die Eigenschaft `bookName` sollte als `Name` zurückgegeben werden.</span><span class="sxs-lookup"><span data-stu-id="56e40-246">The `bookName` property should be returned as `Name`.</span></span>

<span data-ttu-id="56e40-247">Um die zuvor genannten Anforderungen zu erfüllen, nehmen Sie die folgenden Änderungen vor:</span><span class="sxs-lookup"><span data-stu-id="56e40-247">To satisfy the preceding requirements, make the following changes:</span></span>

1. <span data-ttu-id="56e40-248">Verketten Sie in `Startup.ConfigureServices` den folgenden hervorgehobenen Code mit dem Aufruf der `AddMvc`-Methode:</span><span class="sxs-lookup"><span data-stu-id="56e40-248">In `Startup.ConfigureServices`, chain the following highlighted code on to the `AddMvc` method call:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=12)]

    <span data-ttu-id="56e40-249">Durch die vorherige Änderung stimmen Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API mit ihren entsprechenden Eigenschaftsnamen im CLR-Objekttyp überein.</span><span class="sxs-lookup"><span data-stu-id="56e40-249">With the preceding change, property names in the web API's serialized JSON response match their corresponding property names in the CLR object type.</span></span> <span data-ttu-id="56e40-250">Beispielsweise wird die Eigenschaft `Author` der `Book`-Klasse als `Author` serialisiert.</span><span class="sxs-lookup"><span data-stu-id="56e40-250">For example, the `Book` class's `Author` property serializes as `Author`.</span></span>

1. <span data-ttu-id="56e40-251">Versehen Sie in *Models/Book.cs* die Eigenschaft `BookName` mit dem folgenden [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm)-Attribut:</span><span class="sxs-lookup"><span data-stu-id="56e40-251">In *Models/Book.cs*, annotate the `BookName` property with the following [[JsonProperty]](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_JsonPropertyAttribute.htm) attribute:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_BookNameProperty&highlight=2)]

    <span data-ttu-id="56e40-252">Der Wert `Name` des `[JsonProperty]`-Attributs stellt den Eigenschaftsnamen in der serialisierten JSON-Antwort der Web-API dar.</span><span class="sxs-lookup"><span data-stu-id="56e40-252">The `[JsonProperty]` attribute's value of `Name` represents the property name in the web API's serialized JSON response.</span></span>

1. <span data-ttu-id="56e40-253">Fügen Sie den folgenden Code am Anfang von *Models/Book.cs* hinzu, um den `[JsonProperty]`-Attributverweis aufzulösen:</span><span class="sxs-lookup"><span data-stu-id="56e40-253">Add the following code to the top of *Models/Book.cs* to resolve the `[JsonProperty]` attribute reference:</span></span>

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs?name=snippet_NewtonsoftJsonImport)]

1. <span data-ttu-id="56e40-254">Wiederholen Sie die im Abschnitt [Testen der Web-API](#test-the-web-api) definierten Schritte.</span><span class="sxs-lookup"><span data-stu-id="56e40-254">Repeat the steps defined in the [Test the web API](#test-the-web-api) section.</span></span> <span data-ttu-id="56e40-255">Beachten Sie den Unterschied bei den JSON-Eigenschaftsnamen.</span><span class="sxs-lookup"><span data-stu-id="56e40-255">Notice the difference in JSON property names.</span></span>

## <a name="next-steps"></a><span data-ttu-id="56e40-256">Nächste Schritte</span><span class="sxs-lookup"><span data-stu-id="56e40-256">Next steps</span></span>

<span data-ttu-id="56e40-257">Weitere Informationen zum Erstellen von ASP.NET-Core Web-APIs finden Sie in den folgenden Ressourcen:</span><span class="sxs-lookup"><span data-stu-id="56e40-257">For more information on building ASP.NET Core web APIs, see the following resources:</span></span>

* [<span data-ttu-id="56e40-258">Dieses Tutorial auf YouTube</span><span class="sxs-lookup"><span data-stu-id="56e40-258">YouTube version of this article</span></span>](https://www.youtube.com/watch?v=7uJt_sOenyo&feature=youtu.be)
* <xref:web-api/index>
* <xref:web-api/action-return-types>
