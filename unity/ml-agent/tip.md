# Tip

#### 1. WriteDiscreteActionMask

```
public override void WriteDiscreteActionMask(IDiscreteActionMask actionMask)
{
    //인덱스
    //액션
    //활서 비활성
    actionMask.SetActionEnabled(index, action, false);
}
```

액션마스크를 사용하면 에이전트가 학습할때 제약조건을 받아서 처리가 가능하다. 그러나 이것은 학습할때 사용하면 안된다. 해당 조건을 학습 못하기 때문이다.

예를들어 벽이 랜덤 위치에 생성되는데, 코드상에서 벽이므로 갈수 없게 예외처리 한 경우 에이전트는 벽이 있어서 갈수 없다라고 학습하지 못한다.

이건 학습이 완료된 이후에 프로그래밍적으로에이전트의 행동을 제약걸때 사용한다.

#### 2. AddReward & SetReward

목적지까지 이동하는 에이전트를 학습할때

보통 최종보상을 획득할 경우 SetReward(1)로 설정하고,

에이전트가 아무행동도 안하는것을 방지하기위해서 AddReward(-0.001f) 로 마이너스 보상을 주도록 만들수 있는데

**이건 잘못된 생각이다**

최종 보상이 1로 수렴될 경우 에이전트는 <mark style="color:blue;">"</mark><mark style="color:blue;">**중간에 뭔짓을 해도 목적지에만 도달하면 된다"**</mark> 라는 마인드로 학습을 하게 된다.&#x20;

그러므로 최적의 경로를 찾지 못하고, 빙글빙글 돌다가 결국 시간내에 도달하기만 한다.

SetReward는 중간보상이 없는 경우에 설정함이 맞는듯 하다.

다른 코드들을 보면 보상을 고정수치가 아닌 currentStep / maxStep 으로 계산해서 보상을 주는 케이스도 있다.

#### 3. 점진적 증가 학습

처음부터 큰 사이즈의 맵에서 학습을 하면 MaxStep도 늘려줘야하고, 학습시간도 늘어난다.

중간에 Behavior Parameters 의 관찰대상을 변경하는 것만 조심하고, 작은 환경에서 학습시켜서 점점 큰 환경으로 학습이 가능하다.

```
//이어하기
--resume
```

```
//특정 버전을 기본값으로 설정 후 진행
--initialize-from=이전run-id
```

버전별 관리를 하기 위해서 가능하면 --initialize-from 옵션을 사용하자.
