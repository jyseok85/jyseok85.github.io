# PushGateWay로 Metric전송하기

프로메테우스 exporter 는 아주 간단한 구조입니다.&#x20;

그러나 설명이 어렵죠.

```
#TYPE 이름 타입(게이지,카운트, 등)
이름 값
```

* \#TYPE : 타입을 선언하기 위해
* 이름 : 원하는 이름
* 타입 : 데이터의 형태에 따라서 프로메테우스에서 지원하는 타입을 입력
* 값     : 데이터 형태에 맞춰서 정수, 실수, 문자열 등등

이 값만 만들면 됩니다.&#x20;

항목을  여러 개 만들고 웹 서버로 띄워서 주기적으로 갱신해주면 그게 exporter입니다.

프로메테우스는 웹 서버에 접속해서 텍스트를 읽을 뿐이구요.&#x20;



PushGateWay로 전송하기 위해

웹서버를 띄웠다면 curl로 바로 읽어서 전송해도 되고,

코드로 전송하려면 다음과 같이 하면  됩니다..

<pre class="language-csharp"><code class="lang-csharp"><strong>public Send()
</strong><strong>{
</strong>    string pushGatewayUri = "";
    string myParameters = MakeMetric("CPU", 20, Type.gauge);
<strong>    using (WebClient wc = new WebClient())
</strong>    {
        // 푸시게이트웨이에는 무조건 텍스트 플레인으로 보내야 함
        wc.Headers[HttpRequestHeader.ContentType] = "text/plain"; 
        // POST 요청 보내기
        string HtmlResult = wc.UploadString(pushGatewayUri, myParameters); 
        Console.WriteLine(HtmlResult);
    }
}
public string MakeMetric(string name, object value, Type type )
{
    //캐리지리턴은 오류발생시키므로 제거하고, \n만 포함시켜야 함. 다 푸시게이트웨이 특성
    string result = $"#TYPE {name} {type.ToString()}\n{name} {value}\n";           
    return result;
}
</code></pre>



엘라스틱도 비슷한 구조라고 생각됩니다.&#x20;

단지 일반적인 언어를 사용하지 않고, 시스템 자원을 최소한 사용하는 언어를 사용하기에 접근이 어려운 것 뿐이죠.&#x20;
