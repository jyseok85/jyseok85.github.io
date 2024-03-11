# 텍스트 속성 정리

개행 포함하기           -  white-space:pre

자동줄바꿈(기본값) - white-space:normal&#x20;

개행없음                    - white-space:nowrap



영역을 벗어나도 텍스트 보이기

overflow:visible



영역을 벗어나면 말줄임표(...)표시

white-space:pre or nowrap

overflow:hidden

text-overflow:ellipsis

주의 : display 타입에 따라서 적용됨.



display : block 에서 정렬

text-align: start center end justify

주의: word-break 속성이 있을경우 기능이 약간 달라짐..

justfiy 의 경우 white-space:normal 경우에 동작



word-break:

1. normal : 단어 단위로 자름(영어), 한국어 글자 단위로 잘림
2. keep-all : 단어 단위로 자름(한국어,영어)
3. break-all : 글자 단위로 자름(한국어,영어)

즉. 일반적이라면 keep-all 이나 break-all 둘중하나 사용하면 될 듯 보인다.
