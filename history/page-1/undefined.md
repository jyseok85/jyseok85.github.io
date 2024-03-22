# 로컬 웹 서버

.Net 8.0



### 파일 게시 옵션

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

사용되지 않는 코드 자르기 (trim-mode) :&#x20;

필요없는 로직을 삭제해서 용량을 줄이는 모드로

적용하지 않고 기본 프로젝트 생성시

.Net8 AspireApp.Service 기본 프로젝트의 용량은 약 89Mb 입니다.&#x20;

적용한다면 프로젝트의 용량이 19Mb로 줄어들게 됩니다.&#x20;



그러나 리플렉션은 동작하지 않게 됩니다.&#x20;

C#의 특징이 바로 느리더라도 알아서 타입 변환해줘서 사용하기 편한건데 그 장점이 전부 사라집니다.



간단히 말해서 위에 생성한 기본프로젝트에 딱 아래 내용만 있는데

```csharp
app.MapGet("/weatherforecast", () =>
{
    var forecast = 
    Enumerable.Range(1, 5).Select(index =>
        new WeatherForecast
        (
            DateOnly.FromDateTime(DateTime.Now.AddDays(index)),
            Random.Shared.Next(-20, 55),
            summaries[Random.Shared.Next(summaries.Length)]
        ))
        .ToArray();
    return forecast;
});
```

여기서 return 을 WeatherForecast\[] 로 해주고 있고,&#x20;

WeatherForecast\[]를 시스템 내부적으로 json 변환해서 넘겨주는데

내부적으로 변환하는 것이 실패하게 됩니다.&#x20;



사용자가 일일히 문자로 변환한 후에 리턴해야 하는겁니다.&#x20;

그러면 이런곳만 찾아서 json 만들어주면 되는거 아닌가?? 하고 생각할텐데..&#x20;

단순 json 변환이 아니라, 그 타입에 맞는 변환 함수를 만들어야 합니다

즉 아래 코드는 동작하지 않습니다.  이것도 내부적으로 리플렉션 처리 되어 있을테니까요.&#x20;

```
JsonSerializer.Serialize(forecast);
```



C#은 C랑 실질적으로 속도차이가 거의 없다고 합니다.&#x20;

느린이유는 전부 리플렉션때문이라고 생각됩니다.&#x20;
