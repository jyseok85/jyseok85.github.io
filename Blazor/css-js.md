# HTML 요소 찾기


### Sample
```
/* CSS */
.para{
  color : red;
}
/* HTML */
<input class="para" type="checkbox" name ="chk_interest" id="userid"/>
```

1. id로 찾기
```
let element = document.getElementByID("userid");
```

2. 태그명으로 찾기 (너무 많이 나와서 잘 안쓸듯)
```
let element = document.getElementByTagName("input");
```

3. 클래스명으로 찾기 
```
let element = document.getElementByClassName("para");
```

4. CSS 선택자를 이용해서 찾기
```
let element = document.querySelectorAll("para");
//복합 조합
let inputElement = document.querySelectorAll("input.para"); 
//체크가 선택된것만 찾기
let searched = document.querySelectorAll("[name=chk_interest]:checked");
```


# CSS
기본 기능 
```
tag-bg-red{
  color:red;
}
<tag-bg-red> test </tag-bg-red>

.class-bg-red{
  color:red;
}
<div class="class-bg-red"> text </div>

#id-bg-red{
  color:red
}
<div id="id-bg-red"> test </div>
```

# 복합 선택자
### Sample
```
<tag-bg-red>
  <ul class = "child0"/>
  <p class = "child1">
    <p class ="child1-child1/>
  </p>
  <ul class = "child2"/>
  <ul class = "child3"/>
</tag-bg-red>
```

1. 하위 모든 오브젝트
```
//tag-bg-red 태그아래의 p 태그에 컬러를 적용 (tag-bg-red 에는 적용하지 않음)
tag-bg-red p{
  color : white;
}

//class-bg-red 클래스 아래의 child-class 클래스에 컬러적용(자식만)
.class-bg-red .child-class{
  color : white
}
```

2.바로 아래 하나만 선택 => child1 선택됨
```
tag-bg-red>p{
  color : white;
}
```

3. 동일 레벨 인접 => child2 가 선택됨
```
p+ul{
  color : white;
}
```
4. 동일 레벨 모두 => child2 child3 모두 
```
p~ul{
  color : white;
}
```
# 속성 선택자
### Sample
```
<tag-bg-red>
  <ul class = "child0"/>
  <p class = "child1" name = "test">
    <p class ="child1-child1 name = "child-child"/>
  </p>
  <ul class = "child2"/>
  <ul class = "child3"/>
</tag-bg-red>
```


```
//p태그에 class 요소가 들어간것을 전부 찾기 
p[class]{
  color : red;
}
//p태그에 class 요소의 이름이 child0 인것을 찾기
p[class="child0"]{
  color : red;
}
//p태그에 name 요소의 이름이 child-child 인것을 찾기
p[name="child-child"]{
  color : red;
}
```

