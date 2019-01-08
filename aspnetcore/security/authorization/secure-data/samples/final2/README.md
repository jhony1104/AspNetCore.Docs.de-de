# <a name="how-to-buildrun-secure-user-data-sample"></a><span data-ttu-id="3742e-101">Wie Sie Build/Datenbeispiel sichere Benutzer ausführen.</span><span class="sxs-lookup"><span data-stu-id="3742e-101">How to build/run Secure user data sample</span></span>

* <span data-ttu-id="3742e-102">Legen Sie das Kennwort, mit dem Geheimnis-Manager-Tool:</span><span class="sxs-lookup"><span data-stu-id="3742e-102">Set password with the Secret Manager tool:</span></span>

  `dotnet user-secrets set SeedUserPW <pw>`

* <span data-ttu-id="3742e-103">Die Datenbank zu aktualisieren:</span><span class="sxs-lookup"><span data-stu-id="3742e-103">Update the database:</span></span>

    `dotnet ef database update`

* <span data-ttu-id="3742e-104">Aktivieren von HTTPS im Projekt</span><span class="sxs-lookup"><span data-stu-id="3742e-104">Enable HTTPS in the project</span></span>
