## <a name="debug-diagnostics"></a><span data-ttu-id="87b5a-101">Debugdiagnose</span><span class="sxs-lookup"><span data-stu-id="87b5a-101">Debug diagnostics</span></span>

<span data-ttu-id="87b5a-102">Legen Sie für eine ausführliche Routingdiagnoseausgabe `Logging:LogLevel:Microsoft` auf `Debug` fest.</span><span class="sxs-lookup"><span data-stu-id="87b5a-102">For detailed routing diagnostic output, set `Logging:LogLevel:Microsoft` to `Debug`.</span></span> <span data-ttu-id="87b5a-103">Legen Sie beispielsweise in der Entwicklungsumgebung *appsettings.Development.json* fest:</span><span class="sxs-lookup"><span data-stu-id="87b5a-103">For example, in the development environment, set *appsettings.Development.json*:</span></span>

```JSON
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Debug",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  }
}