
> [!NOTE]
> Für dieses Tutorial verwenden Sie nach Möglichkeit das Entity Framework Core-Feature *Migrationen*. Durch Migrationen wird das Datenbankschema so aktualisiert, dass es den Änderungen am Datenmodell entspricht. Migrationen können jedoch nur die Arten von Änderungen durchführen, die der EF Core-Anbieter unterstützt, und die Funktionen des SQLite-Anbieters sind eingeschränkt. So wird beispielsweise das Hinzufügen einer Spalte unterstützt, aber das Entfernen oder Ändern einer Spalte wird nicht unterstützt. Wenn eine Migration zum Entfernen oder Ändern einer Spalte erstellt wird, ist der Befehl `ef migrations add` erfolgreich, aber der Befehl `ef database update` schlägt fehl. Aufgrund dieser Einschränkungen verwendet dieses Tutorial keine Migrationen für SQLite-Schemaänderungen. Wenn sich das Schema ändert, löschen Sie stattdessen die Datenbank und erstellen sie erneut.
>
>Die Problemumgehung für die SQLite-Einschränkungen besteht darin, manuell Migrationscode zu schreiben, um das erneute Erstellen einer Tabelle durchzuführen, wenn sich etwas in der Tabelle ändert. Das erneute Erstellen einer Tabelle umfasst die folgenden Aufgaben:
>
>* Umbenennen der vorhandenen Tabelle.
>* Erstellen einer neuen Tabelle.
>* Kopieren von Daten aus der alten Tabelle in die neue Tabelle.
>* Löschen der alten Tabelle.
>
>Weitere Informationen finden Sie in den folgenden Ressourcen:
>
> * [SQLite EF Core-Datenbank-Anbieter-Einschränkungen](/ef/core/providers/sqlite/limitations)
> * [Anpassen des Migrationscodes](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Datenseeding](/ef/core/modeling/data-seeding)