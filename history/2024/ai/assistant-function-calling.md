# Assistant - Function Calling

단순 텍스트 분석, 생성이 아니라, 서비스하는 시스템의 내부 함수를 호출하기 위한 기능입니다. &#x20;

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption><p>함수호출을 사용하는 이유</p></figcaption></figure>

왼쪽 기존 프로그램의 기능을 오른쪽처럼 텍스트 입력으로 수행한다고 생각하면 이해하기가 쉽습니다.&#x20;



과거라면 다음과 같이 코드를 작성해서 텍스트 입력을 통한 내부 서비스를 호출했다면

```csharp
if(text.contains("연차") && text.contains("휴가") && text.contains("작성"))
{
    CreateAnnualLeave();
}
```



OpenAI를 사용하는 방법은 다음과 같습니다.&#x20;

<pre class="language-python"><code class="lang-python">import os 
from openai import AzureOpenAI 
client = AzureOpenAI( 
	azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT"), 	
	api_key=os.getenv("AZURE_OPENAI_API_KEY"), 
	api_version="2024-02-01" 
) 

response = client.chat.completions.create( 
	model="gpt-4", 
	messages = [{"role": "user", "content": 
	"연차휴가서 작성해"}]
)
response_message = response.choices[0].message
tool_calls = response_message.tool_calls
if tool_calls:
    for tool_call in tool_calls:
            function_name = tool_call.function.name
            function_to_call = available_functions[function_name]
            function_args = json.loads(tool_call.function.arguments)
            ...
            ...
            
<strong>            //넘어온 함수 호출후 응답결과 다시 전송
</strong>            messages.append(
                {
                    "tool_call_id": tool_call.id,
                    "role": "tool",
                    "name": function_name,
                    "content": function_response,
                }
            )  
        
        second_response = client.chat.completions.create(
            model="gpt-4o",
            messages=messages,
        )  # get a new response from the model where it can see the function response
        return second_response
</code></pre>

코드 내용을 보면&#x20;

1. 질의&#x20;
2. Function call 응답에 의해 자체 서비스의 함수 호출
3. 함수 호출 결과 or 지정 메시지 전송(뭐 때문에 실패했다면 오류내용이라도 전달해야하므로)
4. 결과와 첫 질의를 합쳐서 텍스트 생성



핵심은 자연어를 분석할 방법 없으니,   GPT에게 해당 작업을 일임하고,&#x20;

사용자는 함수명과 파라메터를 받는다는 점 입니다.&#x20;

물론 사전에 함수를 등록하던가, 질의를 요청하기 전에 함수를 추가하던가 해야 합니다.&#x20;



마지막 다시 결과를 전송하는 이유는 질의에 의한 자연스러운 텍스트를 생성하기위함입니다.&#x20;

단지 내부 서비스만 호출하려면, 3번 이후 작업은 필요 없습니다.&#x20;



Playground 에서 Function 을 등록하면 다음과 같이 바로 테스트가 가능합니다.&#x20;

<figure><img src="https://lh7-us.googleusercontent.com/yvoYcxRL4vg_9rLxUKMKEHU3-AbyZ-pvK69NlXwBdo2pOrbca-DvJ7wFswG3fOQkwKJ6wqtBCMZQwQ8miHZUbrBslWDyXnm_Ep_3-ptL4zW1sufg_wGmhexYhyzPiRoDNrxTfolUvdV8ORCmzCwk=s2048" alt=""><figcaption></figcaption></figure>

여기서 제일 중요한 것은 함수나, 파라메터의 description 입니다.  이 Description 을 얼마나 잘 작성하느냐에 따라서 function call 이 될수도 있고 안될 수도 있습니다.&#x20;

물론 질문을 정확하게 한다면 상관없습니다.&#x20;

그러나 사용자가 항상 연차휴가서를 작성해 달라고 스크립트를 작성하지는 않습니다.&#x20;

그냥 넉두리로 채팅을 할수도 있습니다. "오늘좀 우울해"  이렇게요.



그러면 AI라면 내일 연차를 작성했습니다. 상신해주세요. 이렇게 나와야 찐 AI 겠죠.



그럴려면 다음과 같이 description 을 변경하면 됩니다.&#x20;

<figure><img src="../../../.gitbook/assets/image (41).png" alt=""><figcaption></figcaption></figure>

3.5 버전에서는 감정인식을 제대로 못했지만 4o 버전에서는 감정에 대한 학습도 이뤄졌습니다.&#x20;

버전이 더 올라가면 설명이 없다고 해도 자연스럽게 추천이 될수 있을것 같습니다.&#x20;

