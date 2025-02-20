---
description: >-
  10년 넘게 개발하지만 헷깔립니다. 그 이유는 자주 사용하지 않기 때문이며, 참조형태를 넘기는 방식은 거대한 프로젝트가 아닌이상 사용하지
  말아야 한다고 생각합니다. (시간도 없고, 코드도 길어지고 하니까 쉽게 쉽게 가는 것 뿐이죠). 이런 참조형태로 개발하면 나중에 필요부분만
  갖다 쓰기도 어렵습니다. 유일한 장점은 캐스팅 시간이 없어서 빠르다는것 뿐.
---

# 참조타입에 ref 를 사용하는 경우

## 1. 참조로 넘기기(기본)

```csharp
void ModifyList(List<string> myList)
{
    myList.Add("New Item");
}

void Start()
{
    List<string> myStrings = new List<string> { "Item 1", "Item 2" };
    ModifyList(myStrings);
    // myStrings는 이제 "Item 1", "Item 2", "New Item"을 포함합니다.
}
```

## 2.ref 없이 넘겨서 새롭게 만들기

```csharp
void ReplaceList(List<string> myList)
{
    myList = new List<string> { "Replaced Item" }; // 새로운 리스트를 생성
}

void Start()
{
    List<string> myStrings = new List<string> { "Item 1", "Item 2" };
    ReplaceList(myStrings);
    // myStrings는 여전히 "Item 1", "Item 2"를 포함합니다.
}
```

## 3.ref 사용 후 새롭게 만들기

`ref`를 사용할 경우, 리스트 자체를 다른 리스트로 교체할 수 있습니다. 그러나 이는 일반적으로 필요하지 않습니다.

```csharp
void ReplaceList(ref List<string> myList)
{
    myList = new List<string> { "Replaced Item" };
}

void Start()
{
    List<string> myStrings = new List<string> { "Item 1", "Item 2" };
    ReplaceList(ref myStrings);
    // myStrings는 이제 "Replaced Item"을 포함합니다.
}
```



## 3. ref&#x20;
