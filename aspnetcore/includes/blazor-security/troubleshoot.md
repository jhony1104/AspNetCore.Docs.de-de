## <a name="troubleshoot"></a>Problembehandlung

### <a name="cookies-and-site-data"></a>Cookies und Website-Daten

Cookies und Websitedaten können über App-Updates hinweg beibehalten werden und das Testen und Beheben von Problemen beeinträchtigen. Löschen Sie Folgendes, wenn Sie App-Codeänderungen, Benutzerkontenänderungen beim Anbieter oder Konfigurationsänderungen für Anbieter-Apps vornehmen:

* Benutzer-Anmelde-Cookies
* App-Cookies
* Zwischengespeicherte und gespeicherte Websitedaten

Ein Ansatz, um zu verhindern, dass veraltete Cookies und Websitedaten das Testen und Beheben von Problemen beeinträchtigen, ist:

* Verwenden Sie einen Browser zum Testen, den Sie konfigurieren können, um alle Cookie- und Websitedaten jedes Mal zu löschen, wenn der Browser geschlossen wird.
* Schließen Sie den Browser zwischen jeder Änderung an der App, dem Testbenutzer oder der Anbieterkonfiguration.

### <a name="run-the-server-app"></a>Ausführen der Server-App

Stellen Sie beim Testen und Beheben einer gehosteten Blazor-App sicher, dass Sie die App über das **Server-Projekt** ausführen. Vergewissern Sie sich beispielsweise in Visual Studio, dass das Serverprojekt im **Projektmappen-Explorer** hervorgehoben ist, bevor Sie die App mit einem der folgenden Ansätze starten:

* Wählen Sie die Schaltfläche **Ausführen**.
* Verwenden Sie **Debug** > **Start Debugging** aus dem Menü.
* Drücken Sie <kbd>F5</kbd>.
