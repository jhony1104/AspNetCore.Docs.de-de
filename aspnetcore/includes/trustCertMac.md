* <span data-ttu-id="e46d5-101">Vertrauen Sie dem HTTPS-Entwicklungszertifikat, indem Sie den folgenden Befehl ausführen:</span><span class="sxs-lookup"><span data-stu-id="e46d5-101">Trust the HTTPS development certificate by running the following command:</span></span>

    ```dotnetcli
    dotnet dev-certs https --trust
    ```

* <span data-ttu-id="e46d5-102">Über den vorherigen Befehl wird die folgende Ausgabe angezeigt:</span><span class="sxs-lookup"><span data-stu-id="e46d5-102">The preceding command displays the following output:</span></span>

    ```console
    Trusting the HTTPS development certificate was requested. If the certificate 
    is not already trusted we will run the following command:
    'sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain 
    <<certificate>>'
    This command might prompt you for your password to install the certificate on the 
    system keychain.
    The HTTPS developer certificate was generated successfully.
    ```

* <span data-ttu-id="e46d5-103">Geben Sie bei Aufforderung den Benutzernamen und das Kennwort des Administrators ein.</span><span class="sxs-lookup"><span data-stu-id="e46d5-103">Enter the admin username and password if prompted.</span></span>  <span data-ttu-id="e46d5-104">Das Zertifikat wird nun installiert und als vertrauenswürdig eingestuft.</span><span class="sxs-lookup"><span data-stu-id="e46d5-104">The certificate will now be installed and trusted.</span></span>

    <span data-ttu-id="e46d5-105">Weitere Informationen finden Sie unter [Trust the ASP.NET Core HTTPS development certificate (Festlegen des ASP.NET Core-HTTPS-Entwicklungszertifikats als vertrauenswürdig)](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos).</span><span class="sxs-lookup"><span data-stu-id="e46d5-105">See [Trust the ASP.NET Core HTTPS development certificate](xref:security/enforcing-ssl#trust-the-aspnet-core-https-development-certificate-on-windows-and-macos) for more information.</span></span>