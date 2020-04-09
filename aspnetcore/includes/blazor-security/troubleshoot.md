## <a name="troubleshoot"></a><span data-ttu-id="91b9e-101">Problembehandlung</span><span class="sxs-lookup"><span data-stu-id="91b9e-101">Troubleshoot</span></span>

### <a name="cookies-and-site-data"></a><span data-ttu-id="91b9e-102">Cookies und Website-Daten</span><span class="sxs-lookup"><span data-stu-id="91b9e-102">Cookies and site data</span></span>

<span data-ttu-id="91b9e-103">Cookies und Websitedaten können über App-Updates hinweg beibehalten werden und das Testen und Beheben von Problemen beeinträchtigen.</span><span class="sxs-lookup"><span data-stu-id="91b9e-103">Cookies and site data can persist across app updates and interfere with testing and troubleshooting.</span></span> <span data-ttu-id="91b9e-104">Löschen Sie Folgendes, wenn Sie App-Codeänderungen, Benutzerkontenänderungen beim Anbieter oder Konfigurationsänderungen für Anbieter-Apps vornehmen:</span><span class="sxs-lookup"><span data-stu-id="91b9e-104">Clear the following when making app code changes, user account changes with the provider, or provider app configuration changes:</span></span>

* <span data-ttu-id="91b9e-105">Benutzer-Anmelde-Cookies</span><span class="sxs-lookup"><span data-stu-id="91b9e-105">User sign-in cookies</span></span>
* <span data-ttu-id="91b9e-106">App-Cookies</span><span class="sxs-lookup"><span data-stu-id="91b9e-106">App cookies</span></span>
* <span data-ttu-id="91b9e-107">Zwischengespeicherte und gespeicherte Websitedaten</span><span class="sxs-lookup"><span data-stu-id="91b9e-107">Cached and stored site data</span></span>

<span data-ttu-id="91b9e-108">Ein Ansatz, um zu verhindern, dass veraltete Cookies und Websitedaten das Testen und Beheben von Problemen beeinträchtigen, ist:</span><span class="sxs-lookup"><span data-stu-id="91b9e-108">One approach to prevent lingering cookies and site data from interfering with testing and troubleshooting is to:</span></span>

* <span data-ttu-id="91b9e-109">Verwenden Sie einen Browser zum Testen, den Sie konfigurieren können, um alle Cookie- und Websitedaten jedes Mal zu löschen, wenn der Browser geschlossen wird.</span><span class="sxs-lookup"><span data-stu-id="91b9e-109">Use a browser for testing that you can configure to delete all cookie and site data each time the browser is closed.</span></span>
* <span data-ttu-id="91b9e-110">Schließen Sie den Browser zwischen jeder Änderung an der App, dem Testbenutzer oder der Anbieterkonfiguration.</span><span class="sxs-lookup"><span data-stu-id="91b9e-110">Close the browser between any change to the app, test user, or provider configuration.</span></span>

### <a name="run-the-server-app"></a><span data-ttu-id="91b9e-111">Ausführen der Server-App</span><span class="sxs-lookup"><span data-stu-id="91b9e-111">Run the Server app</span></span>

<span data-ttu-id="91b9e-112">Stellen Sie beim Testen und Beheben einer gehosteten Blazor-App sicher, dass Sie die App über das **Server-Projekt** ausführen.</span><span class="sxs-lookup"><span data-stu-id="91b9e-112">When testing and troubleshooting a hosted Blazor app, make sure that you're running the app from the **Server** project.</span></span> <span data-ttu-id="91b9e-113">Vergewissern Sie sich beispielsweise in Visual Studio, dass das Serverprojekt im **Projektmappen-Explorer** hervorgehoben ist, bevor Sie die App mit einem der folgenden Ansätze starten:</span><span class="sxs-lookup"><span data-stu-id="91b9e-113">For example in Visual Studio, confirm that the Server project is highlighted in **Solution Explorer** before you start the app with any of the following approaches:</span></span>

* <span data-ttu-id="91b9e-114">Wählen Sie die Schaltfläche **Ausführen**.</span><span class="sxs-lookup"><span data-stu-id="91b9e-114">Select the **Run** button.</span></span>
* <span data-ttu-id="91b9e-115">Verwenden Sie **Debug** > **Start Debugging** aus dem Menü.</span><span class="sxs-lookup"><span data-stu-id="91b9e-115">Use **Debug** > **Start Debugging** from the menu.</span></span>
* <span data-ttu-id="91b9e-116">Drücken Sie <kbd>F5</kbd>.</span><span class="sxs-lookup"><span data-stu-id="91b9e-116">Press <kbd>F5</kbd>.</span></span>
