# Chat 구현

일반적인 Chat 구현은 너무 간단합니다.&#x20;

1. API 키 입력
2. 기반이 될 데이터가 있으면 입력
3. 질의 내용 입력
4. 응답 결과 출력

```python
import os 
from openai import AzureOpenAI 
client = AzureOpenAI( 
	azure_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT"), 	
	api_key=os.getenv("AZURE_OPENAI_API_KEY"), 
	api_version="2024-02-01" 
) 

response = client.chat.completions.create( 
	model="gpt-4", 
	messages=[
		   {
		     "role": "system", 
		     "content": "여기에 데이터를 넣으면 됩니다."
		   }, 
		   {  
		      "role": "user", 
		      "content": "전체 매출액 대비 월별 매출액 구성비를 표로 그려줘"
		   }
	]
)
print(response.choices[0].message.content)
```

역할에는"system", "user", assistant" 가 있다.&#x20;

system , assistant 를 상세 하게 작성하는 것이 좋다.&#x20;

