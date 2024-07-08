# 서버 로컬 보안 정책 일괄 변경하기

회사에서 운영하는 원격 프로그램이 개인정보취급으로 인하여 보안이 까다로워 졌습니다.&#x20;



그래서 작업 PC에도 보안 프로그램 덕지덕지 설치해야하고, 분기마다 보고서도 작성해야 하고, 등등 불편해서, 개인정보를 전부 제거하고, 사내메신저  인증을 연동시켰는데, 왜 ISMS 인증에서 안빼주는지.....

계속 보안 이슈가 있을때마다 적용하라고 합니다..



수십대 서버에 일일히 적용하기 힘들어서 스크립트를 만들었습니다. &#x20;

그 중 조치했던 몇가지 항목을 기준으로 사용법을 작성하겠습니다.&#x20;

(다음의 값들은 KISA의 가이드라인에 따른 값입니다.)



### 보안을 적용할 항목에는 크게 두가지가 있습니다.

1. Regedit
2. 로컬 보안 정책

레지스트를 수정하는 방법은 어떻게보면 널리 알려져 있습니다만, 그래도 같이 한꺼번에 만들어서 적용하도록 해봅니다.&#x20;



각 점검항목을 각각의 항목으로 만들어야 나중에 수정하기가 편할 듯 보입니다.&#x20;



W-42(SAM 계정과 공유의 익명 열거 허용 안 함)

```
Windows Registry Editor Version 5.00
 
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa]
"restrictanonymous"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa]
"restrictanonymoussam"=dword:00000001
```

W-72(DOS 공격 방어 레지 설정)

<pre><code><strong>Windows Registry Editor Version 5.00
</strong>
[HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Tcpip\Parameters]
"SynAttackProtect"=dword:00000001

[HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Tcpip\Parameters]
"EnableDeadGWDetect"=dword:00000000

[HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Tcpip\Parameters]
"KeepAliveTime"=dword:000493e0

[HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\Tcpip\Parameters]
"NoNameReleaseOnDemand"=dword:00000001
</code></pre>

W-75(경고 메시지 설정)

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon]
"LegalNoticeCaption"="Warring!!"
"LegalNoticeText"="This System is for the use of authorized users only.!"
```



로컬 보안 정책의 수정가능 한 항목은 다음과 같이 확인 가능합니다.

```
secedit /export /cfg c:\test.inf
```

크게 보면 4가지 섹션으로 구성되어 있고

\[System Access] \[Event Audit] \[Registry Values] \[Privilege Rights]

수정할 항목의 값만 놔두고 나머지를 삭제하시면 됩니다.  Version은 꼭 필요합니다.&#x20;



W-53(로컬로그온허용).inf

```
[Privilege Rights]
SeInteractiveLogonRight = *S-1-5-32-544
[Version]
signature="$CHICAGO$"
Revision=1
```

W-69(로컬정책-감사정책).inf

```
[Event Audit]
AuditSystemEvents = 3
AuditLogonEvents = 3
AuditObjectAccess = 0
AuditPrivilegeUse = 0
AuditPolicyChange = 1
AuditAccountManage = 1
AuditProcessTracking = 0
AuditDSAccess = 1
AuditAccountLogon = 1
[Version]
signature="$CHICAGO$"
Revision=1
```



## 일괄반영 배치파일 만들기

{% code title="실행.bat" %}
```batch
@echo off
echo 현재 디렉토리의 *.reg, *.inf 파일을 적용합니다.

REM Get the directory of the batch file
set "SCRIPT_DIR=%~dp0"

REM Change to the directory of the batch file
cd /d "%SCRIPT_DIR%"

echo Reg 파일을 적용합니다.
for %%f in (*.reg) do (
    echo Applying registry settings from %%f    
)

echo inf 파일을 적용합니다.
for %%f in (*.inf) do (
    echo Applying security settings from %%f
    secedit /configure /db c:\test.sdb /cfg %%f /overwrite
    echo Security settings applied successfully.    
)
echo Press any key to exit.
pause >nul
```
{% endcode %}
