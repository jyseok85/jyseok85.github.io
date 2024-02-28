# 완벽한 로깅을 향하여

### Log Level 은 기본이다.&#x20;

* Trace   -  자세한 내용
* Debug - 일반적인 내용
* Info      - 주요 로직의 시작 끝을 알림
* Warning - 예상 가능한 오류로 이 부분이 발생되지 않도록 설계를 개선할 필요성이 있음.
* Error - 프로그램은 실행 가능 한 오류지만 무조건 수정해야 하는 오류
* Fatal - 치명적인 오류(ERROR 단에서 넘어가면 안되므로 개인적으로 의미 없음)

많은 로깅 프로젝트나 블로그에서 대략적으로 정의를 하지만, 실질적인 사용 시점에 대해서는 전부 사용자에게 맡기고 있다.&#x20;

그래서 프로젝트를 새로 시작 할 때마다 매번 다른 로그 설정을 적용하게 된다. 그리고 결국 뒤섞이며, 결국 모든 로그를 보게 되며, 그냥 레벨 없이 적용한 것과 별 차이 가 없게 된다.&#x20;



### 다음은 현재 정착된 나의 기준이다. (2024.02.28)

* Trace : 평소에 쓰던 로그 내용을 적는다.&#x20;
* Debug : 함수  프로퍼티 등의 시작과 끝
* Info : 주요 로직의 시작과 끝
* Warning : 발생되면 안되지만, 프로그램은 정상 서비스 가능( ex 사용자에게 알리는 경고)
* Error : 무조건 수정할 오류(프로그램 실행되더라도 정상 동작이 아님)



### 위와 같은 기준이 만들어진 배경

원래는 debug를 기본 베이스로 사용하고, trace를 상세 로그로 사용하려고 프로젝트를 구성한  적이 많이 있었다.(로그 레벨 설명을 보면 그런 느낌으로 사용하도록 적혀있다)

그러나 실제 개발 후 서비스를 운영하면서 trace를 사용한 적이 없다.&#x20;

내가 상세하게 봐야할 부분은 이미 debug를 통해서 전부 출력되고 있었다.&#x20;

기준이 모호해서 얼마나 더 자세하게 봐야 trace를 써야할지 예측 할 수 없었다.&#x20;



info는 확실히 주요 로직에 쓰고 있었다.  그러나 가끔씩 함수의 시작과 끝에도 Info를 사용할 때가 있었다.&#x20;

함수에 시작과 끝에 Debug로 체크 할 때도 있었다. 이것도 기준이 모호해 졌다.&#x20;



Warning 과 Error 는 워낙 명시적으로 잘 구분 되므로 고민될 건 없었다.&#x20;

Fatal 항목이 있지만, 솔직히 Error 단에서 전부 수정되야 하기 때문에 사용하는 의미가 없다.&#x20;



### 로그 데이터 형식

로그만 보고 문제를 파악하기 위해서는 사용자가 입력한 정보 외에 개인적으로 생각되는 필수 정보가 있다.

1. Time
2. Class&#x20;
3. Method&#x20;

아무리 생각해도 저 3가지 정보는 필수라고 생각된다.&#x20;

2,3 번은 필수가 아니라고 생각하시는 분들도 있을 텐 데, 그거야 자기가 만든 프로그램의 로그를 볼 경우이며, 한번도 본 적 없는 업무 맡아서 수정하려고 할 때 2, 3 번이 없다면 어떨지 생각해 보면 된다.&#x20;



다행히 닷넷에는 현재 함수의 StackTrace 정보를 알 수 있으므로 자동으로 생성 가능하다.&#x20;

같은 닷넷이라도 프레임워크별로 약간 씩 다를 수 있지만 대부분 아래와 같은 형식으로 사용 한다.&#x20;

```csharp
private List<string> GetStacks(int stackLevel = 2)
{
    string? method = null;
    string? nameSpace = null;
    string? className = null;
    var stackFrame = new StackFrame(stackLevel, true);
    MethodBase? methodBase = stackFrame?.GetMethod();

    if (methodBase?.Name == "MoveNext") //코루틴
    {
        method = methodBase?.DeclaringType?.Name;
        nameSpace = methodBase?.DeclaringType?.Namespace;
        className = methodBase?.DeclaringType?.DeclaringType?.Name;
    }
    else
    {
        method = methodBase?.Name;
        className = methodBase?.DeclaringType?.Name;
        nameSpace = methodBase?.DeclaringType?.Namespace;
    }

    if (method is not null && method.Contains("<")) //MEC 코루틴용
    {
        method = method.Replace("<", "");
        int position = method.IndexOf(">");
        method = method.Substring(0, position);
    }

    List<string> rValue = new List<string>();
    if (nameSpace is not null && className is not null && method is not null)
    {
        rValue.Add(nameSpace);
        rValue.Add(className);
        rValue.Add(method);
    }
    return rValue;
}
```



### 동적 로그 활성화

로그 레벨을 지정함으로써 필터해서 볼 수 있는 방법이 생겼다. &#x20;

그러나 모든 로그를 작성하도록 배포 하면 당연히 안된다. 잘못 사용될 경우 프로그램의 엄청난 성능저하를 발생 시키기 때문이다. &#x20;

그렇다고 프로그램 시작 시 마다 수동으로 옵션을 변경하는 건 너무 구리다.&#x20;

우리는 프로그램이 중단되지 않고 로그레벨을 변경해야 한다.&#x20;

* 로그 작성시 파일을 읽던지, 주기적으로  API를 호출해 옵션을 변경하던지 한다.&#x20;
* 추가로 User ID, PC Address 등으로 필터를 걸어도 좋다.&#x20;



그러나 프로젝트가 커지면 결국 제대로 활용하기 어려운건 마찬가지다.&#x20;

왜냐하면&#x20;

1. 실제 보고 싶은 건 Trace 쪽의 로그이며, &#x20;
2. Trace 의 로그는 많고
3. 정확히 어느 시점(시간)을 찾아야 하며,&#x20;
4. 찾은 시점 이후의 로직도 보고 싶고

등등 다양한 이유로 에러로그 작성 or 전체로그 작성 두가지 모드만 사용하게 된다.&#x20;



### 지정위치 로그 활성화

그래서 든 생각이 특정 함수 기준으로 로그를 활성화 시키는 것 이다.&#x20;

다행히도 로그 작성 시점의 함수명을 알 수 있다.  (클래스 명도 같이 체크해도 될테지만, 사용이 번거로워질듯 하다)



서비스 실행 중에 동적으로 로그 작성 조건을 변경을 했고,

해당 조건에 맞는 함수가 호출되면

1. 지정한 시간동안 특정 레벨 이상 로그를 전부 작성한다.
2. 지정한 횟수만큼 특정 레벨 이상 로그를 전부 작성한다.&#x20;

```json
{
    "LogLevel": "trace",
    "UseDetailLogging": true,
    "DetailLogLevel": "trace",
    "DetailLogFunction": "A,B", //A or B 함수가 호출되면 상세 로그 작성
    "DetailLogEndCondition": "Count", //or time
    "DetailLogEndValue": 1000 // 1000초나 1000번 등
}
```

위와 같이 설정한 경우 다음과 같이 사용자 액션에 의해 발생된 시나리오 측정이 가능하다.

* 회원가입 버튼을 누른 경우 그 이후의 로그 필터
* 프로그램 해상도가 변경되고 난 이후에 동작되는 로그 필터

