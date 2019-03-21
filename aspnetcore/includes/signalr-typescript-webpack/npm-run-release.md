---
ms.openlocfilehash: c82571d3cfa57ccd6e7c83f654f119bdd8991486
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58210439"
---
```console
npm run release
```

<span data-ttu-id="a6b48-101">Dieser Befehl hält die clientseitigen Objekte an, die bereitgestellt werden, wenn die App ausgeführt wird.</span><span class="sxs-lookup"><span data-stu-id="a6b48-101">This command yields the client-side assets to be served when running the app.</span></span> <span data-ttu-id="a6b48-102">Die Objekte werden im Ordner *wwwroot* gespeichert.</span><span class="sxs-lookup"><span data-stu-id="a6b48-102">The assets are placed in the *wwwroot* folder.</span></span>

<span data-ttu-id="a6b48-103">Webpack hat die folgenden Aufgaben durchgeführt:</span><span class="sxs-lookup"><span data-stu-id="a6b48-103">Webpack completed the following tasks:</span></span>

* <span data-ttu-id="a6b48-104">Die Inhalte des Verzeichnis *wwwroot* wurden bereinigt.</span><span class="sxs-lookup"><span data-stu-id="a6b48-104">Purged the contents of the *wwwroot* directory.</span></span>
* <span data-ttu-id="a6b48-105">TypeScript wurde in JavaScript konvertiert. Dieser Prozess ist als *Transpilierung* bekannt.</span><span class="sxs-lookup"><span data-stu-id="a6b48-105">Converted the TypeScript to JavaScript&mdash;a process known as *transpilation*.</span></span>
* <span data-ttu-id="a6b48-106">Der generierte JavaScript-Code wurde gekürzt, um die Dateigröße zu reduzieren. Dieser Prozess ist als *Minimierung* bekannt.</span><span class="sxs-lookup"><span data-stu-id="a6b48-106">Mangled the generated JavaScript to reduce file size&mdash;a process known as *minification*.</span></span>
* <span data-ttu-id="a6b48-107">Die verarbeiteten JavaScript-, CSS- und HTML-Dateien wurden aus *src* in das Verzeichnis *wwwroot* kopiert.</span><span class="sxs-lookup"><span data-stu-id="a6b48-107">Copied the processed JavaScript, CSS, and HTML files from *src* to the *wwwroot* directory.</span></span>
* <span data-ttu-id="a6b48-108">Die folgenden Elemente wurden in die Datei *wwwroot/index.html* eingefügt:</span><span class="sxs-lookup"><span data-stu-id="a6b48-108">Injected the following elements into the *wwwroot/index.html* file:</span></span>
  * <span data-ttu-id="a6b48-109">Ein `<link>`-Tag, das auf die Datei *wwwroot/main.\<hash\>.css* verweist.</span><span class="sxs-lookup"><span data-stu-id="a6b48-109">A `<link>` tag, referencing the *wwwroot/main.\<hash\>.css* file.</span></span> <span data-ttu-id="a6b48-110">Dieses Tag wird unmittelbar vor dem schließenden Tag `</head>` platziert.</span><span class="sxs-lookup"><span data-stu-id="a6b48-110">This tag is placed immediately before the closing `</head>` tag.</span></span>
  * <span data-ttu-id="a6b48-111">Ein `<script>`-Tag, das auf die minimierte Datei *wwwroot/main.\<hash\>.js* verweist.</span><span class="sxs-lookup"><span data-stu-id="a6b48-111">A `<script>` tag, referencing the minified *wwwroot/main.\<hash\>.js* file.</span></span> <span data-ttu-id="a6b48-112">Dieses Tag wird unmittelbar vor dem schließenden Tag `</body>` platziert.</span><span class="sxs-lookup"><span data-stu-id="a6b48-112">This tag is placed immediately before the closing `</body>` tag.</span></span>
