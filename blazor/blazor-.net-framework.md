# Blazor 에서 .NET Framework 참조

기존 프로젝트를 활용하기 위해서 참조하는 것은 문제가 안된다.



단지 참조하는 프로젝트 내에서 윈도우 전용 DLL을 사용하는 경우는 실행되지 않는다.&#x20;

ex)System.Windows.Forms





기존 IIS환경 내에서 실행시

Blazor 웹 ->  ASP.NET + .NET Framework&#x20;

문제점 : 서버 환경설치가 번거롭다.

Blazor 웹 ->  Blazor 앱 + .NET Framework&#x20;

문제점 : 윈도우전용 DLL 참조 문제

Blazor 웹 ->  Blazor 앱  -> .NET Framework  Exe

문제점 : 권한 문제가 발생
