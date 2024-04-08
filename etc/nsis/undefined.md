# 파일에서 텍스트 교체



{% file src="../../.gitbook/assets/ReplaceInFile (1).zip" %}

1. SOURCE\_FILE 을 읽어오고
2. 그 안에서 SEARCH\_TEXT를 찾은 후
3. REPLACEMENT 텍스틀로 교체 합니다.&#x20;

```nsis
!include ReplaceInFile.nsh

!macro _ReplaceInFile SOURCE_FILE SEARCH_TEXT REPLACEMENT
  Push "${SOURCE_FILE}"
  Push "${SEARCH_TEXT}"
  Push "${REPLACEMENT}"
  Call RIF
!macroend

!insertmacro _ReplaceInFile $INSTDIR\appsettings.json 
    "http://localhost:8088/api/Documents/PDF" http://localhost:9999/api/Documents/PDF
```

