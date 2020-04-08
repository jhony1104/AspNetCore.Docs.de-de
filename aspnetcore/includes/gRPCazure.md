## <a name="grpc-not-supported-on-azure-app-service"></a>gRPC wird in Azure App Service nicht unterstützt

> [!WARNING]
> [ASP.NET Core gRPC](xref:grpc/index) wird derzeit nicht in Azure App Service oder IIS unterstützt. Die HTTP/2-Implementierung von „Http.Sys“ unterstützt keine nachstehenden HTTP-Antwortheader, auf die gRPC sich stützt. Weitere Informationen finden Sie in [diesem GitHub-Problem](https://github.com/dotnet/AspNetCore/issues/9020).
