## <a name="debug-diagnostics"></a>Debugdiagnose

Legen Sie für eine ausführliche Routingdiagnoseausgabe `Logging:LogLevel:Microsoft` auf `Debug` fest. Legen Sie beispielsweise in der Entwicklungsumgebung *appsettings.Development.json* fest:

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