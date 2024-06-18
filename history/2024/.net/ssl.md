# SSL 인증서 적용

ASP.NET Core 비어 있음 기본 샘플에 다음과 같이 추가합니다.&#x20;

{% content-ref url="../../../etc/server-side/undefined.md" %}
[undefined.md](../../../etc/server-side/undefined.md)
{% endcontent-ref %}

```csharp
var builder = WebApplication.CreateBuilder(args);

//인증서 추가
builder.WebHost.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Loopback, 포트번호, listenOptions =>
    {
        listenOptions.UseHttps("pfx 파일경로", "비밀번호");
    });
});

var app = builder.Build();
app.MapGet("/", () => "Hello World!");
app.Run();
```



인증서를 추가하지 않더라도 테스트시에 https 가 정상적으로 동작됩니다.&#x20;

그 이유는 인증서가 없다면 기본적으로 테스트용 개발 인증서가 실행되기 때문입니다.&#x20;

<figure><img src="../../../.gitbook/assets/image (40).png" alt=""><figcaption></figcaption></figure>



## http https 동시적용

{% code title="appsettings.json 수정" %}
```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "path/to/your/certificate.pfx",
          "Password": "your-password"
        }
      }
    }
  }
}
```
{% endcode %}
