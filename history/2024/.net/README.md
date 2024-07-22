---
description: .Net 8.0
---

# .NET 간단한 웹 서버

_**프로젝트 생성전에 "간단함" 의 조건을 만족시키기 위한 필수로 배포 게시 옵션을 알아봅니다.**_&#x20;

_**프로그램만 간단한게 아니라 배포도 간단해야 합니다.**_



## 파일 게시 옵션

<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

**ReadyToRun(R2R) 컴파일 사용 (AOT)**

* 애플리케이션 시작 시간을 줄이기 위해 사전 컴파일된 코드를 사용하는 기능입니다.
* 이 옵션은 애플리케이션의 실행 파일 크기를 증가 시킬 수 있지만, 런타임에 컴파일할 필요가 없기 때문에 애플리케이션의 시작 시간을 단축 시킵니다.

**사용되지 않는 코드 자르기 (trim-mode)**

1. 필요 없는 로직을 삭제해서 용량을 줄이는 모드입니다.
2.  ASP.NET Core 웹 API(Native AOT)  기준

    Trim (0) -  15Mb

    Trim (X) - 86Mb

#### 단일 파일 생성

* 웹 서버의 경우 완벽한단일파일이 생성되지 않습니다. \
  Web.Config appsetting.json 같은 파일 때문이며 aspnetcorev2\_inprocess.dll 이 파일은 묶이지가 않습니다.&#x20;



## 사용되지 않는 코드 자르기

* 사용되지 않는 코드 자르기를 적용하면 프로젝트 용량이 크게 줄어들지만, C#의 유용한 기능 중 하나인  리플렉션이 동작하지 않게 됩니다. 이는 타입 변환을 자동으로 처리하는 C#의 편리함이 최적화 과정에서 상실되는 결과를 초래합니다.
* 직접 사용한 적이 없다 하더라도, 리플렉션은 다양한 곳에서 사용되고 있습니다.&#x20;
* Json 변환  또한 리플렉션 처리됩니다.  그러므로 일반적인 방식으로 Json을 사용 할 수 없습니다.&#x20;

**예제: 기본 프로젝트에서 리플렉션의 제한 사례**

아래는 간단한 날씨 예보 API 엔드포인트를 구현한 코드 예제입니다. 이 코드는 최적화 기법을 적용한 경우 리플렉션에 의한 자동 타입 변환이 제한됩니다.

{% code title="일반적인 샘플 예제" %}
```csharp
app.MapGet("/weatherforecast", () =>
{
    var forecast = new WeatherForecast()
    {
        Date = DateTime.Now,
        TemperatureCelsius = Random.Shared.Next(-20, 55),
        Summary = "aaaa"
    };  
    return forecast;
});
```
{% endcode %}

<pre class="language-csharp" data-title="Trim mode 적용"><code class="lang-csharp">app.MapGet("/weatherforecast", () =>
{
    var forecast = new WeatherForecast()
    {
        Date = DateTime.Now,
        TemperatureCelsius = Random.Shared.Next(-20, 55),
        Summary = "aaaa"
    };
    return JsonSerializer.Serialize(forecast, 
                        SourceGenerationContext.Default.WeatherForecast);
}); 

[JsonSourceGenerationOptions(WriteIndented = true)]
[JsonSerializable(typeof(WeatherForecast))]
<strong>internal partial class SourceGenerationContext : JsonSerializerContext
</strong>{
}
</code></pre>

위와같이 모델을 자동으로 변환 해 주던 것을 명시적으로 지정해 줘야 합니다.&#x20;

실질적으로는 모델 생성 후 추가로 JsonSerializerContext 을 상속받는 클래스를 하나 더 생성해야 합니다.&#x20;

```csharp
internal partial class SourceGenerationContext : JsonSerializerContext
```



### 기본 프로젝트 생성

trim-mode 적용시에 기본프로젝트를 아무거나 생성하면 많은 시행착오를 겪을 수 있습니다.&#x20;

기본적인 템플릿이 포함되는 웹 프로젝트를 생성하면 ApiController 가 추가됩니다. 그러나 게시할대 Trim을 적용하게 되면 서버는 실행되도 해당 api로 접근이 불가능합니다.&#x20;

빌드시에 오류를 표시하면 좋을텐데 경고만 표시되서 실제 해당 Api를 호출하기 전에는 알 수가 없습니다.&#x20;



원인은 다음과 같습니다.&#x20;

{% code overflow="wrap" %}
```
Using member 'Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers(IServiceCollection)' which has 'RequiresUnreferencedCodeAttribute' can break functionality when trimming application code. MVC does not currently support trimming or native AOT. https://aka.ms/aspnet/trimming
```
{% endcode %}

* MVC패턴은 Trim-mode가 적용되지 않습니다.
* 빌드시에 <mark style="color:red;">오류</mark>가 아닌 <mark style="color:yellow;">경고</mark> 로 나타납니다.&#x20;
* 프로젝트 생성시에도 따로 Trim-mode 에 대한 설명은 없습니다.&#x20;
* Triming 을 하기 위해선 최소 프로젝트(ASP.NET Core 웹  API(native AOT)) 로 생성해야 합니다.&#x20;

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

물론 완전 빈 프로젝트를 생성 해도 되지만, 사용방법을 모르니까 가능하면 기본 샘플을 있는 프로젝트를 선택하는 것이 좋다고 생각됩니다.&#x20;

<figure><img src="../../../.gitbook/assets/image (2) (2).png" alt=""><figcaption></figcaption></figure>

<details>

<summary>ASP.NET Core 비어 있음 기본 샘플</summary>

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
app.MapGet("/", () => "Hello World!");
app.Run();
```

</details>

<details>

<summary>ASP.NET Core 웹API(Native AOT) 기본 샘플</summary>

```csharp
using System.Text.Json.Serialization;
var builder = WebApplication.CreateSlimBuilder(args);
builder.Services.ConfigureHttpJsonOptions(options =>
{
    options.SerializerOptions.
    TypeInfoResolverChain.Insert(0, AppJsonSerializerContext.Default);
});

var app = builder.Build();
var sampleTodos = new Todo[] {
    new(1, "Walk the dog"),
    new(2, "Do the dishes", DateOnly.FromDateTime(DateTime.Now)),
    new(3, "Do the laundry", DateOnly.FromDateTime(DateTime.Now.AddDays(1))),
    new(4, "Clean the bathroom"),
    new(5, "Clean the car", DateOnly.FromDateTime(DateTime.Now.AddDays(2)))
};

var todosApi = app.MapGroup("/todos");
todosApi.MapGet("/", () => sampleTodos);
todosApi.MapGet("/{id}", (int id) =>
    sampleTodos.FirstOrDefault(a => a.Id == id) is { } todo
        ? Results.Ok(todo)
        : Results.NotFound());

app.Run();
public record Todo(int Id, string? Title, DateOnly? DueBy = null,
 bool IsComplete = false);

[JsonSerializable(typeof(Todo[]))]
internal partial class AppJsonSerializerContext : JsonSerializerContext
{

}
```

</details>

트림 모드(Trim-mode) 적용 전까지 C#에서 리플렉션이 얼마나 많이 사용되는지 몰랐습니다. 실제로 사용하려고 하니 거의 모든 것이 리플렉션을 기반으로 하고 있었습니다. 기존 프로젝트에는 결코 적용할 수 없으며, 새로운 프로젝트에서 외부 라이브러리 참조 없이만 사용할 수 있어 그 실용성이 의문입니다.

이로써 한 가지 궁금증이 해결되었습니다.

C#과 C의 속도 차이가 거의 없다는 것이 이해가 되지 않았습니다. 하지만 실제로는 C#이 많은 리플렉션을 사용하기 때문에 실제 애플리케이션에서는 느릴 수밖에 없었습니다.

리플렉션을 사용하지 않고, 외부 라이브러리참조가 없는 C#이라면 C만큼빠    릅니다!
