---
description: .Net 7
---

# Blazor 에 Api Controller 추가

### 1. Api가 호출가능하도록 Program.cs 파일 수정

{% code title="Program.cs" %}
```csharp
builder.Services.AddControllers();
builder.Services.AddHttpClient();

app.MapControllers();
```
{% endcode %}

### 2. 테스트용Api 컨트롤러 추가

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

{% code title="ValuesController.cs" %}
```csharp
[Route("api/[controller]")]
    [ApiController]
    public class ValuesController : ControllerBase
    {
        // GET: api/<ValuesController>
        [HttpGet]
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }
....
```
{% endcode %}

### 3. 브라우저에 (도메인+/api+/Controller) 명을 입력해서  기본 Get API 결과 확인

ex) [https://localhost:44377/api](https://localhost:44377/api/pdf)/values

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>
