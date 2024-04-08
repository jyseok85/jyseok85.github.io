# Nsis 문자열 검색

**문자열 검색 함수 StrContains**

```python
${StrContains} $output "비교문자열" $input
```

* 입력 문자열과 비교문자열을 비교해서 발견된 경우 비교문자열을 반환합니다.\
  찾지 못할경우에 null or ("")공백을 반환합니다.

**응용**

```python
${StrContains} $output "http" $input
  StrCmp $output "" notfound
    StrCpy $result $input
  notfound:
    StrCpy $result http://127.0.0.1
  done:
```

* 입력된 문자열에 'http'가 입력되어 있을 경우 result 변수에 해당 주소를 입력하고, 발견하지 못했을 경우 result 변수에 localhost 주소를 입력합니다.

**함수추가**

```python
Var STR_HAYSTACK
Var STR_NEEDLE
Var STR_CONTAINS_VAR_1
Var STR_CONTAINS_VAR_2
Var STR_CONTAINS_VAR_3
Var STR_CONTAINS_VAR_4
Var STR_RETURN_VAR

Function StrContains
  Exch $STR_NEEDLE
  Exch 1
  Exch $STR_HAYSTACK
    StrCpy $STR_RETURN_VAR ""
    StrCpy $STR_CONTAINS_VAR_1 -1
    StrLen $STR_CONTAINS_VAR_2 $STR_NEEDLE
    StrLen $STR_CONTAINS_VAR_4 $STR_HAYSTACK
    loop:
      IntOp $STR_CONTAINS_VAR_1 $STR_CONTAINS_VAR_1 + 1
      StrCpy $STR_CONTAINS_VAR_3 $STR_HAYSTACK $STR_CONTAINS_VAR_2 $STR_CONTAINS_VAR_1
      StrCmp $STR_CONTAINS_VAR_3 $STR_NEEDLE found
      StrCmp $STR_CONTAINS_VAR_1 $STR_CONTAINS_VAR_4 done
      Goto loop
    found:
      StrCpy $STR_RETURN_VAR $STR_NEEDLE
      Goto done
    done:
   Pop $STR_NEEDLE
   Exch $STR_RETURN_VAR
FunctionEnd

!macro _StrContainsConstructor OUT NEEDLE HAYSTACK
  Push `${HAYSTACK}`
  Push `${NEEDLE}`
  Call StrContains
  Pop `${OUT}`
!macroend

!define StrContains '!insertmacro "_StrContainsConstructor"'
```
