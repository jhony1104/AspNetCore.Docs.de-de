# <a name="upload-files-sample-app"></a>Beispiel-App zum Hochladen von Dateien

Diese Beispiel-App veranschaulicht die im Thema [Hochladen von Dateien in ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads) beschriebenen Konzepte.

## <a name="security-considerations"></a>Sicherheitsüberlegungen

Gehen Sie mit Bedacht vor, wenn Sie Benutzern die Möglichkeit geben, Dateien auf einen Server hochzuladen. Angreifer können [Denial-of-Service](/windows-hardware/drivers/ifs/denial-of-service)-Angriffe ausführen, Viren oder Schadsoftware hochladen oder versuchen, Netzwerke und Server auf andere Weise zu kompromittieren.

Folgende Schritte können Sie dabei unterstützen, die Wahrscheinlichkeit eines erfolgreichen Angriffs zu verringern:

* Laden Sie Dateien in einen dedizierten Bereich zum Hochladen von Dateien auf dem System hoch, vorzugsweise auf ein Nicht-Systemlaufwerk. Die Verwendung eines dedizierten Speicherorts erleichtert es, Sicherheitsmaßnahmen für hochgeladene Dateien zu erzwingen. Deaktivieren Sie Ausführungsberechtigungen für den Speicherort zum Hochladen Dateien.&dagger;
* Hochgeladene Dateien dürfen nie persistent in der Verzeichnisstruktur gespeichert werden, in der sich auch die App befindet.&dagger;
* Wählen Sie einen sicheren von der App festgelegten Dateinamen. Verwenden Sie keinen vom Benutzer eingegebenen Dateinamen oder den nicht vertrauenswürdigen Dateinamen der hochgeladenen Datei.&dagger;
* Lassen Sie nur einen festgelegten Satz genehmigter Dateierweiterungen zu.&dagger;
* Überprüfen Sie die Signatur des Dateiformats, um zu verhindern, dass ein Benutzer eine getarnte Datei hochlädt (z. B. eine *EXE*-Datei mit der Erweiterung *.txt*).&dagger;
* Stellen Sie sicher, dass clientseitige Überprüfungen auch auf dem Server erfolgen. Clientseitige Überprüfungen sind leicht zu umgehen.&dagger;
* Überprüfen Sie die Größe des Uploads, und verhindern Sie Uploads, die größer als erwartet sind.&dagger;
* Wenn Dateien nicht durch eine hochgeladene Datei mit demselben Namen überschrieben werden sollen, vergleichen Sie den Dateinamen mit der Datenbank oder dem physischen Speicher, bevor Sie die Datei hochladen.
* **Wenden Sie auf die hochgeladenen Inhalte einen Scanner auf Viren und Schadsoftware an, ehe die Datei gespeichert wird.**

&dagger;Die Beispiel-App veranschaulicht einen Ansatz, der die Kriterien erfüllt.

> [!WARNING]
> Das Hochladen von schädlichem Code auf ein System ist häufig der erste Schritt, um Code mit der folgenden Absicht auszuführen:
>
> * Vollständige Übernahme eines Systems
> * Überlasten eines System mit dem Ziel eines Systemausfalls.
> * Kompromittieren von Benutzer- oder Systemdaten
> * Anwenden von Graffiti auf eine öffentliche Benutzeroberfläche.
>
> Wie Sie die Angriffsoberfläche beim Akzeptieren von Benutzerdateien reduzieren, erfahren Sie in den folgenden Artikeln:
>
> * [Unrestricted File Upload (Uneingeschränkter Dateiupload)](https://www.owasp.org/index.php/Unrestricted_File_Upload)
> * [Azure-Sicherheit: Sicherstellen, dass entsprechende Kontrollen erfolgen, wenn Dateien von Benutzern akzeptiert werden](/azure/security/azure-security-threat-modeling-tool-input-validation#controls-users)

Weitere Informationen finden Sie unter [Hochladen von Dateien in ASP.NET Core](https://docs.microsoft.com/aspnet/core/mvc/models/file-uploads).

## <a name="how-to-use-the-sample"></a>Verwendung dieses Beispiels

In der Datei *appsettings.json*:

1. Legen Sie den Pfad für gespeicherte Dateien fest (`StoredFilesPath`).

   * Die Beispiel-App legt den Wert auf `c:\\files` fest, was voraussetzt, dass ein Ordner mit dem Namen *files* im Stammverzeichnis von Laufwerk C: des Systems existiert.
   * Der Pfad muss vorhanden sein. Erstellen Sie den Ordner *files* auf Laufwerk C: des Systems, oder legen Sie den Pfad auf einen geeigneten Speicherort fest.
   * Der Prozess der App erfordert Lese-/Schreibberechtigungen für den Pfad.
   * **WICHTIG!** Deaktivieren Sie Ausführungsberechtigungen für alle Benutzer im Pfad.

1. Legen Sie den Grenzwert für die Dateigröße (`FileSizeLimit`) in Byte fest. Der Standardwert `2097152` (2.097.152 Bytes) der Beispiel-App ermöglicht das Hochladen von Dateien einer Größe von bis zu 2 MB.
