* `-F|--no-formatting`

  <span data-ttu-id="b9d92-101">Ein Flag, das die Unterdrückung der HTTP-Antwortformatierung bewirkt.</span><span class="sxs-lookup"><span data-stu-id="b9d92-101">A flag whose presence suppresses HTTP response formatting.</span></span>

* `-h|--header`

  <span data-ttu-id="b9d92-102">Legt einen HTTP-Anforderungsheader fest.</span><span class="sxs-lookup"><span data-stu-id="b9d92-102">Sets an HTTP request header.</span></span> <span data-ttu-id="b9d92-103">Die folgenden zwei Werteformate werden unterstützt:</span><span class="sxs-lookup"><span data-stu-id="b9d92-103">The following two value formats are supported:</span></span>

  * `{header}={value}`
  * `{header}:{value}`

* `--response`

  <span data-ttu-id="b9d92-104">Gibt eine Datei an, in die die gesamte HTTP-Antwort (inklusive Header und Text) geschrieben werden soll.</span><span class="sxs-lookup"><span data-stu-id="b9d92-104">Specifies a file to which the entire HTTP response (including headers and body) should be written.</span></span> <span data-ttu-id="b9d92-105">Beispiel: `--response "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="b9d92-105">For example, `--response "C:\response.txt"`.</span></span> <span data-ttu-id="b9d92-106">Wenn die Datei nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b9d92-106">The file is created if it doesn't exist.</span></span>

* `--response:body`

  <span data-ttu-id="b9d92-107">Gibt eine Datei an, in die der HTTP-Antworttext geschrieben werden soll.</span><span class="sxs-lookup"><span data-stu-id="b9d92-107">Specifies a file to which the HTTP response body should be written.</span></span> <span data-ttu-id="b9d92-108">Beispiel: `--response:body "C:\response.json"`.</span><span class="sxs-lookup"><span data-stu-id="b9d92-108">For example, `--response:body "C:\response.json"`.</span></span> <span data-ttu-id="b9d92-109">Wenn die Datei nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b9d92-109">The file is created if it doesn't exist.</span></span>

* `--response:headers`

  <span data-ttu-id="b9d92-110">Gibt eine Datei an, in die die HTTP-Antwortheader geschrieben werden sollen.</span><span class="sxs-lookup"><span data-stu-id="b9d92-110">Specifies a file to which the HTTP response headers should be written.</span></span> <span data-ttu-id="b9d92-111">Beispiel: `--response:headers "C:\response.txt"`.</span><span class="sxs-lookup"><span data-stu-id="b9d92-111">For example, `--response:headers "C:\response.txt"`.</span></span> <span data-ttu-id="b9d92-112">Wenn die Datei nicht vorhanden ist, wird sie erstellt.</span><span class="sxs-lookup"><span data-stu-id="b9d92-112">The file is created if it doesn't exist.</span></span>

* `-s|--streaming`

  <span data-ttu-id="b9d92-113">Ein Flag, das das Streaming der HTTP-Antwort aktiviert.</span><span class="sxs-lookup"><span data-stu-id="b9d92-113">A flag whose presence enables streaming of the HTTP response.</span></span>
