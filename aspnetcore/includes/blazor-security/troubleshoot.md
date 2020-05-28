## <a name="troubleshoot"></a>Problembehandlung

### <a name="cookies-and-site-data"></a>Cookies und Standortdaten

Cookies und Standortdaten können über APP-Updates hinweg beibehalten werden und das Testen und die Problembehandlung beeinträchtigen. Entfernen Sie Folgendes, wenn Sie Änderungen am APP-Code, Änderungen an den Benutzerkonten mit dem Anbieter oder Konfigurationsänderungen an den Anbieter-Apps vornehmen:

* Benutzer Anmelde Cookies
* App-Cookies
* Zwischengespeicherte und gespeicherte Standortdaten

Ein Ansatz, um zu verhindern, dass veraltete Cookies und Standortdaten das Testen und die Problembehandlung beeinträchtigen, ist Folgendes:

* Browser konfigurieren
  * Verwenden Sie einen Browser zum Testen, den Sie so konfigurieren können, dass alle Cookies und Standortdaten jedes Mal gelöscht werden, wenn der Browser geschlossen wird.
  * Stellen Sie sicher, dass der Browser manuell oder durch die IDE zwischen allen Änderungen an der APP, dem Test Benutzer oder der Anbieter Konfiguration geschlossen wird.
* Verwenden Sie einen benutzerdefinierten Befehl, um in Visual Studio einen Browser im Inkognito-Modus oder im privaten Modus zu öffnen:
  * Öffnen Sie in der Visual Studio-Schaltfläche **Ausführen** das Dialogfeld **Durchsuchen** nach.
  * Wählen Sie die Schaltfläche **Hinzufügen** aus.
  * Geben Sie im Feld **Programm** den Pfad zu Ihrem Browser an.
  * Geben Sie im Feld **Argumente** die Befehlszeilenoption an, die der Browser verwendet, um im Inkognito-oder privaten Modus und die URL der APP zu öffnen. Beispiel:
    * Google Chrome:`--incognito --new-window https://localhost:5001`
    * Mozilla Firefox:`-private -url https://localhost:5001`
  * Geben Sie im Feld Anzeige **Name** einen Namen ein. Beispiel: `Firefox Auth Testing`.
  * Klicken Sie auf die Schaltfläche **OK**.
  * Um zu vermeiden, dass das Browserprofil für die einzelnen Iterationen von Tests mit einer APP ausgewählt werden muss, legen Sie das Profil mit der Schaltfläche **als Standard festlegen** als Standard fest.
  * Stellen Sie sicher, dass der Browser von der IDE zwischen allen Änderungen an der APP, dem Test Benutzer oder der Anbieter Konfiguration geschlossen wird.

### <a name="run-the-server-app"></a>Ausführen der Server-App

Stellen Sie beim Testen und Beheben von Problemen bei einer gehosteten blazor-App sicher, dass Sie die APP aus dem **Server** Projekt ausführen. Vergewissern Sie sich z. b. in Visual Studio, dass das Server Projekt in **Projektmappen-Explorer** hervorgehoben wird, bevor Sie die APP mit einem der folgenden Ansätze starten:

* Wählen Sie die Schaltfläche **Ausführen**.
* Verwenden **Sie**  >  **Debuggen Debuggen starten** im Menü.
* Drücken Sie <kbd>F5</kbd>.

### <a name="inspect-the-content-of-a-json-web-token-jwt"></a>Überprüfen des Inhalts eines JSON Web Token (JWT)

Zum Decodieren eines JSON Web Token (JWT) verwenden Sie das [JWT.ms](https://jwt.ms/) -Tool von Microsoft. Werte in der Benutzeroberfläche verlassen ihren Browser nie.
