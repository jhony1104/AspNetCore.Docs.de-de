---
title: Verwalten von Protobuf-Verweisen mit dotnet-grpc
author: juntaoluo
description: Erfahren Sie mehr über das Hinzufügen, aktualisieren, entfernen und Auflisten von protobuf-verweisen mit dem globalen Tool dotnet-GrpC.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 10/17/2019
uid: grpc/dotnet-grpc
ms.openlocfilehash: 994597c854a95bb33de1686ab025cb3744cf6845
ms.sourcegitcommit: e71b6a85b0e94a600af607107e298f932924c849
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72519037"
---
# <a name="manage-protobuf-references-with-dotnet-grpc"></a>Verwalten von Protobuf-Verweisen mit dotnet-grpc

Von [John Luo](https://github.com/juntaoluo)

`dotnet-grpc` ist ein globales .net Core-Tool zum Verwalten von [protobuf-verweisen ( *. proto*)](xref:grpc/basics#proto-file) in einem .net-GrpC-Projekt. Das Tool kann zum Hinzufügen, aktualisieren, entfernen und Auflisten von protobuf-verweisen verwendet werden.

## <a name="installation"></a>Installation

Führen Sie den folgenden Befehl aus, um das [.net Core-Tool](/dotnet/core/tools/global-tools)"`dotnet-grpc`" zu installieren:

```dotnetcli
dotnet tool install -g dotnet-grpc
```

## <a name="add-references"></a>Hinzufügen von Verweisen

`dotnet-grpc` kann verwendet werden, um protobuf-Verweise als `<Protobuf />`-Elemente der *csproj* -Datei hinzuzufügen:

```xml
<Protobuf Include="Protos\greet.proto" GrpcServices="Server" />
```

Die protobuf-Verweise werden verwendet, um C# die Client-und/oder Server Objekte zu generieren. Das Tool "`dotnet-grpc`" kann folgende Aktionen ausführen:

* Erstellen Sie einen protobuf-Verweis aus lokalen Dateien auf dem Datenträger.
* Erstellen Sie einen protobuf-Verweis aus einer Remote Datei, die durch eine URL angegeben ist.
* Stellen Sie sicher, dass dem Projekt die richtigen GrpC-Paketabhängigkeiten hinzugefügt werden.

Beispielsweise wird das Paket "`Grpc.AspNetCore`" einer Web-App hinzugefügt. `Grpc.AspNetCore` enthält Unterstützung für GrpC-Server und Client Bibliotheken und-Tools. Alternativ dazu werden die Pakete "`Grpc.Net.Client`", "`Grpc.Tools`" und "`Google.Protobuf`", die nur die Unterstützung von GrpC-Client Bibliotheken und-Tools enthalten, einer Konsolen-app hinzugefügt.

### <a name="add-file"></a>Datei hinzufügen

Der Befehl "`add-file`" wird verwendet, um lokale Dateien auf dem Datenträger als protobuf-Verweise hinzuzufügen. Die bereitgestellten Dateipfade:

* Kann relativ zum aktuellen Verzeichnis oder zu absoluten Pfaden sein.
* Kann Platzhalter für die Musterbasierte Datei [Globalisierung](https://wikipedia.org/wiki/Glob_(programming))enthalten.

Wenn Dateien außerhalb des Projektverzeichnisses liegen, wird ein `Link`-Element hinzugefügt, um die Datei unter dem Ordner `Protos` in Visual Studio anzuzeigen.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet grpc add-file [options] <files>...
```

#### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| Dateien | Die protobuf-Datei Verweise. Hierbei kann es sich um einen Pfad zu globmuster für lokale protobuf-Dateien handeln. |

#### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --Projekt | Der Pfad zu der Projektdatei, die verwendet werden soll. Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.
| -s | --Dienste | Der Typ der GrpC-Dienste, die generiert werden sollen. Wenn `Default` angegeben wird, wird `Both` für Webprojekte verwendet, und `Client` wird für nicht-Webprojekte verwendet. Akzeptierte Werte sind `Both`, `Client`, `Default`, `None`, `Server`.
| -i | --Weitere Import-dirs | Zusätzliche Verzeichnisse, die beim Auflösen von Importen für die protobuf-Dateien verwendet werden sollen. Dies ist eine durch Semikolons getrennte Liste von Pfaden.
| | --Zugriff | Der Zugriffsmodifizierer für C# die generierten Klassen. Der Standardwert ist `Public`sein. Akzeptierte Werte sind `Internal` und `Public`.

### <a name="add-url"></a>URL hinzufügen

Der Befehl "`add-url`" wird verwendet, um eine durch eine Quell-URL als protobuf-Referenz angegebene Remote Datei hinzuzufügen. Ein Dateipfad muss angegeben werden, um anzugeben, wo die Remote Datei heruntergeladen werden soll. Der Dateipfad kann relativ zum aktuellen Verzeichnis oder als absoluter Pfad sein. Wenn sich der Dateipfad außerhalb des Projektverzeichnisses befindet, wird ein `Link`-Element hinzugefügt, um die Datei unter dem virtuellen Ordner `Protos` in Visual Studio anzuzeigen.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc add-url [options] <url>
```

#### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| url | Die URL zu einer Remote-protobuf-Datei. |

#### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -o | --Ausgabe | Gibt den Download Pfad für die Remote-protobuf-Datei an. Diese Option muss angegeben werden.
| -p | --Projekt | Der Pfad zu der Projektdatei, die verwendet werden soll. Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.
| -s | --Dienste | Der Typ der GrpC-Dienste, die generiert werden sollen. Wenn `Default` angegeben wird, wird `Both` für Webprojekte verwendet, und `Client` wird für nicht-Webprojekte verwendet. Akzeptierte Werte sind `Both`, `Client`, `Default`, `None`, `Server`.
| -i | --Weitere Import-dirs | Zusätzliche Verzeichnisse, die beim Auflösen von Importen für die protobuf-Dateien verwendet werden sollen. Dies ist eine durch Semikolons getrennte Liste von Pfaden.
| | --Zugriff | Der Zugriffsmodifizierer für C# die generierten Klassen. Der Standardwert ist `Public`sein. Akzeptierte Werte sind `Internal` und `Public`.

## <a name="remove"></a>Remove

Der Befehl "`remove`" wird verwendet, um protobuf-Verweise aus der *csproj* -Datei zu entfernen. Der Befehl akzeptiert Pfad Argumente und Quell-URLs als Argumente. Das Tool:

* Entfernt nur den protobuf-Verweis.
* Die *. proto* -Datei wird nicht gelöscht, auch wenn Sie ursprünglich von einer Remote-URL heruntergeladen wurde.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc remove [options] <references>...
```

### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| Referenzen | Die URLs oder Dateipfade der zu entfernenden protobuf-Verweise. |

### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --Projekt | Der Pfad zu der Projektdatei, die verwendet werden soll. Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.

## <a name="refresh"></a>Aktualisieren

Der Befehl "`refresh`" wird zum Aktualisieren eines Remote Verweises mit dem neuesten Inhalt aus der Quell-URL verwendet. Sowohl der Download Dateipfad als auch die Quell-URL können zum Angeben des zu aktualisierenden Verweises verwendet werden. Hinweis:

* Die Hashwerte für den Dateiinhalt werden verglichen, um zu bestimmen, ob die lokale Datei aktualisiert werden soll.
* Es werden keine Zeitstempel Informationen verglichen.

Wenn ein Update erforderlich ist, ersetzt das Tool immer die lokale Datei durch die Remote Datei.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc refresh [options] [<references>...]
```

### <a name="arguments"></a>Argumente

| Argument | Beschreibung |
|-|-|
| Referenzen | Die URLs oder Dateipfade zu Remote-protobuf-verweisen, die aktualisiert werden sollen. Lassen Sie dieses Argument leer, um alle Remote Verweise zu aktualisieren. |

### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --Projekt | Der Pfad zu der Projektdatei, die verwendet werden soll. Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.
| | --Dry-Run | Gibt eine Liste der Dateien aus, die aktualisiert werden, ohne dass neue Inhalte heruntergeladen werden.

## <a name="list"></a>Liste

Der Befehl "`list`" wird verwendet, um alle protobuf-Verweise in der Projektdatei anzuzeigen. Wenn alle Werte einer Spalte Standardwerte sind, darf die Spalte weggelassen werden.

### <a name="usage"></a>Verwendung

```dotnetcli
dotnet-grpc list [options]
```

### <a name="options"></a>Optionen

| Kurze Option | Lange Option | Beschreibung |
|-|-|-|
| -p | --Projekt | Der Pfad zu der Projektdatei, die verwendet werden soll. Wenn keine Datei angegeben ist, durchsucht der Befehl das aktuelle Verzeichnis nach einem.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
