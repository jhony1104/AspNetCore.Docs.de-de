* Vertrauen Sie dem HTTPS-Entwicklungszertifikat, indem Sie den folgenden Befehl ausführen:

    ```console
    dotnet dev-certs https --trust
    ```

* Über den vorherigen Befehl wird die folgende Ausgabe angezeigt:

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* Geben Sie bei Aufforderung den Benutzernamen und das Kennwort des Administrators ein.  Das Zertifikat wird nun installiert und als vertrauenswürdig eingestuft.

    Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).