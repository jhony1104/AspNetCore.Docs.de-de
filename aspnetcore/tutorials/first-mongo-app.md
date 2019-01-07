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
# <a name="create-a-web-api-with-aspnet-core-and-mongodb"></a>Erstellen einer Web-API mit ASP.NET Core und MongoDB

Von [Pratik Khandelwal](https://twitter.com/K2Prk) und [Scott Addie](https://twitter.com/Scott_Addie)

Dieses Tutorial erstellt eine Web-API, die Create-, Read-, Update- und Delete (CRUD)-Vorgänge auf einer [MongoDB](https://www.mongodb.com/what-is-mongodb) NoSQL-Datenbank durchführt.

In diesem Tutorial lernen Sie, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
> * Konfigurieren von MongoDB
> * Erstellen einer MongoDB-Datenbank
> * Definieren einer MongoDB-Sammlung und eines Schemas
> * Ausführen von MongoDB-CRUD-Vorgänge über eine Web-API

[Anzeigen oder Herunterladen von Beispielcode](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-mongo-app/sample) ([Vorgehensweise zum Herunterladen](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Erforderliche Komponenten

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* [.NET Core SDK 2.2 oder höher](https://www.microsoft.com/net/download/all)
* [Version 15.9 von Visual Studio 2017 oder höher](https://www.visualstudio.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017) mit der Workload **ASP.NET und Webentwicklung**
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* [.NET Core SDK 2.2 oder höher](https://www.microsoft.com/net/download/all)
* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
* [MongoDB](https://docs.mongodb.com/manual/administration/install-community/)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

* [.NET Core SDK 2.2 oder höher](https://www.microsoft.com/net/download/all)
* [Visual Studio für Mac Version 7.7 oder höher](https://www.visualstudio.com/downloads/)
* [MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)

---

## <a name="configure-mongodb"></a>Konfigurieren von MongoDB

Wenn Sie Windows verwenden, wird MongoDB standardmäßig unter *C:\Program Files\MongoDB* installiert. Fügen Sie der Umgebungsvariablen `Path` *C:\Program Files\MongoDB\Server\<version_number>\bin* hinzu. Durch diese Änderung können Sie von einer beliebigen Stelle auf Ihrem Entwicklungscomputer auf MongoDB zugreifen.

Verwenden Sie die Mongo-Shell in den folgenden Schritten, um eine Datenbank zu erstellen, Sammlungen durchzuführen und Dokumente zu speichern. Weitere Informationen zu Mongo-Shell-Befehlen finden Sie unter [Arbeiten mit der Mongo-Shell](https://docs.mongodb.com/manual/mongo/#working-with-the-mongo-shell).

1. Wählen Sie ein Verzeichnis auf Ihrem Entwicklungscomputer zum Speichern der Daten. Zum Beispiel *C:\BooksData* auf Windows. Erstellen Sie das Verzeichnis, falls es nicht vorhanden ist. Die Mongo-Shell erstellt keine neuen Verzeichnisse.
1. Öffnen Sie eine Befehlsshell. Führen Sie den folgenden Befehl aus, um eine Verbindung zu MongoDB auf dem Standardport 27017 herzustellen. Denken Sie daran, `<data_directory_path>` durch das Verzeichnis zu ersetzen, das Sie im vorherigen Schritt gewählt haben.

    ```console
    mongod --dbpath <data_directory_path>
    ```

1. Öffnen Sie eine andere Befehlsshellinstanz. Verbinden Sie sich mit der Standardtestdatenbank, indem Sie den folgenden Befehl ausführen:

    ```console
    mongo
    ```

1. Führen Sie in einer Befehlsshell folgenden Befehl aus:

    ```console
    use BookstoreDb
    ```

    Wenn nicht bereits vorhanden, wird eine Datenbank namens *BookstoreDb* erstellt. Wenn die Datenbank vorhanden ist, wird die Verbindung für Transaktionen geöffnet.

1. Erstellen Sie eine `Books`-Sammlung mithilfe des folgenden Befehls:

    ```console
    db.createCollection('Books')
    ```

    Das folgende Ergebnis wird angezeigt:

    ```console
    { "ok" : 1 }
    ```

1. Definieren Sie ein Schema für die `Books`-Sammlung. und fügen zwei Dokumente mithilfe des folgenden Befehls ein:

    ```console
    db.Books.insertMany([{'Name':'Design Patterns','Price':54.93,'Category':'Computers','Author':'Ralph Johnson'}, {'Name':'Clean Code','Price':43.15,'Category':'Computers','Author':'Robert C. Martin'}])
    ```

    Das folgende Ergebnis wird angezeigt:

    ```console
    {
      "acknowledged" : true,
      "insertedIds" : [
        ObjectId("5bfd996f7b8e48dc15ff215d"),
        ObjectId("5bfd996f7b8e48dc15ff215e")
      ]
    }
    ```

1. Zeigen Sie die Dokumente in der Datenbank mithilfe des folgenden Befehls an:

    ```console
    db.Books.find({}).pretty()
    ```

    Das folgende Ergebnis wird angezeigt:

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

    Das Schema fügt eine automatisch generierte `_id` Eigenschaft vom Typ `ObjectId` für jedes Dokument hinzu.

Die Datenbank ist bereit. Sie können beginnen, die ASP.NET Core-Web-API zu erstellen.

## <a name="create-the-aspnet-core-web-api-project"></a>Erstellen eines ASP.NET Core-Web-API-Projektes

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Gehen Sie zu **Datei** > **Neu** > **Projekt**.
1. Wählen Sie **ASP.NET Core-Webanwendung**, benennen Sie das Projekt mit *BooksApi*, und klicken Sie auf **OK**.
1. Wählen Sie das **.NET Core**-Zielframework und **ASP.NET Core 2.1**. Wählen Sie die **API**-Projektvorlage aus, und klicken Sie auf **OK**:
1. Navigieren Sie im Fenster **Paket-Manager-Konsole** zum Stammverzeichnis des Projekts. Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:

    ```powershell
    Install-Package MongoDB.Driver -Version 2.7.2
    ```

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. Führen Sie die folgenden Befehle in einer Befehlsshell aus:

    ```console
    dotnet new webapi -o BooksApi
    code BooksApi
    ```

    Es wird ein neues ASP.NET Core Web-API-Projekt für .NET Core generiert und in Visual Studio Code geöffnet.

1. Klicken Sie auf **Ja**, wenn die Meldung *Die erforderlichen Objekte für die Erstellung und das Debuggen sind in „BooksApi“ nicht vorhanden. Sollen sie hinzugefügt werden?* angezeigt wird.
1. Öffnen Sie das **integrierte Terminal** und navigieren Sie zum Stammverzeichnis des Projekts. Führen Sie den folgenden Befehl aus, um den .NET-Treiber für MongoDB zu installieren:

    ```console
    dotnet add BooksApi.csproj package MongoDB.Driver -v 2.7.2
    ```

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio für Mac](#tab/visual-studio-mac)

1. Navigieren Sie zu **Datei**  > **Neue Lösung** > **.NET Core** > **App**.
1. Wählen Sie die **ASP.NET Core-Web-API**-C#-Projektvorlage, und klicken Sie auf **Weiter**.
1. Wählen Sie **.NET Core 2.2** aus der Dropdownliste **Zielframework**, und klicken Sie auf **Weiter**.
1. Geben Sie *BooksApi* als **Projektnamen** ein, und klicken Sie dann auf **Erstellen**.
1. Klicken Sie im Pad **Lösung** mit der rechten Maustaste auf den Knoten **Abhängigkeiten** des Projekts, und wählen Sie **Pakete hinzufügen**.
1. Geben Sie *MongoDB.Driver* in das Suchfeld ein, wählen Sie das *MongoDB.Driver*-Paket, und klicken Sie auf **Paket hinzufügen**.
1. Klicken Sie auf die Schaltfläche **Zustimmen** im Dialogfeld **Zustimmung zur Lizenz**.

---

## <a name="add-a-model"></a>Hinzufügen eines Modells

1. Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Modelle* hinzu.
1. Fügen Sie eine `Book`-Klasse zum Verzeichnis *Modelle* mit dem folgenden Code hinzu:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Models/Book.cs)]

In der vorhergehenden Klasse wird die Eigenschaft `Id` benötigt, um das Common Language Runtime (CLR)-Objekt auf die MongoDB-Sammlung zuzuordnen. Andere Eigenschaften in der Klasse werden mit dem Attribut `[BsonElement]` ergänzt. Der Wert des Attributs stellt den Eigenschaftsnamen in der MongoDB-Sammlung dar.

## <a name="add-a-crud-operations-class"></a>Hinzufügen einer CRUD-Vorgangsklasse

1. Fügen Sie zum Stammverzeichnis des Projekts ein Verzeichnis *Dienste* hinzu.
1. Fügen Sie eine `BookService`-Klasse zum Verzeichnis *Dienste* mit dem folgenden Code hinzu:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceClass)]

1. Fügen Sie der Datei *appsettings.json* eine MongoDB-Verbindungszeichenfolge hinzu:

    [!code-csharp[](first-mongo-app/sample/BooksApi/appsettings.json?highlight=2-4)]

    Auf die vorhergehende `BookstoreDb`-Eigenschaft wird über den Klassenkonstruktor `BookService` zugegriffen.

1. Registrieren Sie unter `Startup.ConfigureServices` die Klasse `BookService` mit dem Dependency Injection-System:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

    Die vorhergehende Dienstregistrierung ist notwendig, um die Konstruktor-Injection in Verbrauchsklassen zu unterstützen.

Die `BookService`-Klasse verwendet die folgenden `MongoDB.Driver`-Mitglieder, um CRUD-Vorgänge für die Datenbank ausführen:

* `MongoClient` &ndash; – Liest die Serverinstanz für das Ausführen von Datenbankvorgängen. Der Konstruktor dieser Klasse wird in der MongoDB-Verbindungszeichenfolge bereitgestellt:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Services/BookService.cs?name=snippet_BookServiceConstructor&highlight=3)]

* `IMongoDatabase` &ndash; – Stellt die Mongo-Datenbank zum Ausführen von Vorgängen dar. Dieses Tutorial verwendet die generische `GetCollection<T>(collection)`-Methode auf der Schnittstelle, um Zugriff auf Daten in einer bestimmten Sammlung zu erhalten. CRUD-Vorgänge können für die Sammlung ausgeführt werden, nachdem diese Methode aufgerufen wurde. Rufen Sie in der `GetCollection<T>(collection)`-Methode folgendes auf:
  * `collection` steht für den Sammlungsnamen.
  * `T` steht für den in der Sammlung gespeicherten CLR-Objekttypen.

`GetCollection<T>(collection)` gibt ein `MongoCollection`-Objekt zurück, das die Sammlung darstellt. In diesem Tutorial werden die folgenden Methoden für der Sammlung aufgerufen:

* `Find<T>` &ndash; Gibt alle Dokumente in der Sammlung zurück, die den angegebenen Suchkriterien entsprechen.
* `InsertOne` &ndash; Fügt das angegebene Objekt als neues Dokument in die Sammlung ein.
* `ReplaceOne` &ndash; Ersetzt das Einzeldokument, das den angegebenen Suchkriterien entspricht, durch das angegebene Objekt.
* `DeleteOne` &ndash; Löscht das Einzeldokument, das den angegebenen Suchkriterien entspricht.

## <a name="add-a-controller"></a>Hinzufügen eines Controllers

1. Fügen Sie eine `BooksController`-Klasse zum Verzeichnis *Controller* mit dem folgenden Code hinzu:

    [!code-csharp[](first-mongo-app/sample/BooksApi/Controllers/BooksController.cs)]

    Der oben aufgeführte Web-API-Controller:

    * Verwendet die `BookService`-Klasse, um CRUD-Vorgänge auszuführen.
    * Enthält Aktionsmethoden zur Unterstützung von GET-, POST-, PUT- und DELETE HTTP-Anforderungen.
1. Erstellen Sie die App, und führen Sie sie aus.
1. Navigieren Sie zu `http://localhost:<port>/api/books` in Ihrem Browser. Die folgende JSON-Antwort wird angezeigt:

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

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen von ASP.NET-Core Web-APIs finden Sie in den folgenden Ressourcen:

* <xref:web-api/index>
* <xref:web-api/action-return-types>
