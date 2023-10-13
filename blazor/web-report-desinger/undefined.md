# 눈금자

눈금자의 경우 당연히 Body 부분에 들어가게 됩니다.&#x20;

생각나는 대로 구현해도 상관없지만, 확대 축소가 들어갈 경우 로직이 복잡해집니다.

1. Body에 추가할  경우\
   \-> 용지가 확대 축소됨에 따라서 눈금자의 크기를 증가시키는 로직을 추가해야합니다\
   \-> 용지 위치(가운데 정렬) 에 따라서 눈금자의 기준 텍스트도 변경해줘야 합니다.&#x20;
2. 용지안에 눈금자를 포함시키는 경우\
   \-> 인쇄시 용지와 분리시키기 위해서 별도의 태그로 분리해야 합니다.   \
   \-> 확대함에 따라서 눈금자가 UI에서 안보일 수도 있습니다.&#x20;



MS Word 경우에는 1번 같은 방법으로 되어 있습니다.&#x20;

그러나 1번 케이스는 각 컨트롤의 연결이 복잡해져서 구현하기 어려운 단점이 있습니다.&#x20;

아무것도 없는 환경에서 구현한다면, 1번도 고려해 보겠지만, 브라우저의 기본 기능을 갖고 있는 환경에서는 (컨트롤 스크롤로 확대축소) 구현하기 쉬운 2번 방법을 사용하겠습니다.&#x20;



눈금자는 다음의 컨트롤을 사용했습니다.&#x20;

{% embed url="https://codepen.io/stoumann/pen/oNwQyxd" %}

기존 컨트롤은 배경에 삽입하기 때문에 하나의 페이지에 전부 있는데 그걸 상단, 좌측으로 분리 시키겠습니다.&#x20;

그리고 확대 축소를 위해서 상위에서 컨트롤 가능한 report-background 컨트롤을 추가하겠습니다.&#x20;

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>



<details>

<summary>Ruler Controls</summary>

{% code title="RulerX.razor" %}
```cshtml
<ul class="ruler-x" style=@("width:" + Size + "px;")>

    @for (int i = 0; i < Count; i++)
    {
        <li name = "@(i + 1)"></li>
    }

</ul>

<style>
    .ruler-x {
        color: var(--ruler-num-c);
        counter-reset: d -10;
        display: flex;
        font-size: var(--ruler-num-fz);
        line-height: 3;
        list-style: none;
        margin: 0;
        overflow: hidden;
        padding: 0;
        position: fixed;

        --ruler-num-c: #888;
        --ruler-num-fz: 10px;
        --ruler-num-pi: 0.75ch;
        --ruler-unit: 1mm;
        --ruler-x: 1;
        --ruler-y: 0;
        --ruler1-bdw: 1px;
        --ruler1-c: #BBB;
        --ruler1-h: 8px;
        --ruler1-space: 1;
        --ruler2-bdw: 1px;
        --ruler2-c: #BBB;
        --ruler2-h: 25px;
        --ruler2-space: 10;
        background-attachment: fixed;
        background-image: 
        linear-gradient(90deg, var(--ruler1-c) 0 var(--ruler1-bdw), transparent 0), 
        linear-gradient(90deg, var(--ruler2-c) 0 var(--ruler2-bdw), transparent 0);
        background-position: 0 0;
        background-repeat: repeat-x, repeat-x;
        background-size: 
        calc(var(--ruler-unit) * var(--ruler1-space) * var(--ruler-x)) var(--ruler1-h), 
        calc(var(--ruler-unit) * var(--ruler2-space) * var(--ruler-x)) var(--ruler2-h);
        background-position-y: bottom;
    }

    .ruler-x {
        height: var(--ruler2-h);
        inset-block-start: 0;
        opacity: var(--ruler-x);
    }

        .ruler-x li {
            align-self: flex-end;
        }

        .ruler-x li {
            counter-increment: d var(--ruler2-space);
            flex: 0 0 calc(var(--ruler-unit) * var(--ruler2-space));
        }

            .ruler-x li::after {
                content: counter(d)"";
                line-height: 1;
                padding-inline-start: var(--ruler-num-pi);
            }

</style>

@code{
    [Parameter]
    public int Count { get; set; } = 21;

    [Parameter]
    public double Size { get; set; } = 793.7;
}
```
{% endcode %}

{% code title="RulerY.razor" %}
```cshtml
<ul class="ruler-y" style=@("height:" + Size + "px;")>
    @for (int i = 0; i < Count; i++)
    {
        <li name="@(i + 1)"></li>
    }    
</ul>

<style>
    .ruler-y {
        color: var(--ruler-num-c);
        counter-reset:  d -10;
        display: flex;
        font-size: var(--ruler-num-fz);
        line-height: 0;
        list-style: none;
        margin: 0;
        overflow: hidden;
        padding: 1px;
        position: fixed;
        --ruler-num-c: #888;
        --ruler-num-fz: 10px;
        --ruler-num-pi: 0.5ch;
        --ruler-unit: 1mm;
        --ruler-x: 0; /* 표시여부 */
        --ruler-y: 1;
        --ruler1-bdw: 1px; /* 작은 눈금 두께 */
        --ruler1-c: #BBB;
        --ruler1-h: 8px;
        --ruler1-space: 1;
        --ruler2-bdw: 1px; /* 큰 눈금 두께 */
        --ruler2-c: #BBB;
        --ruler2-h: 25px;
        --ruler2-space: 10;
        background-attachment: fixed;
        background-image:  
        linear-gradient(0deg, var(--ruler1-c) 0 var(--ruler1-bdw), transparent 0), 
        linear-gradient(0deg, var(--ruler2-c) 0 var(--ruler2-bdw), transparent 0);
        background-position: 0 0;
        background-repeat:  repeat-y, repeat-y;
        background-size: 
        var(--ruler1-h) calc(var(--ruler-unit) * var(--ruler1-space) * var(--ruler-y)),
        var(--ruler2-h) calc(var(--ruler-unit) * var(--ruler2-space) * var(--ruler-y));
        background-position-x: right;
    }

    .ruler-y {
        width: var(--ruler2-h);
        inset-inline-start: 0;
        opacity: var(--ruler-y);
        flex-direction: column;
        border-top : solid 1px lightgray;
    }

        .ruler-y li {
            counter-increment: d var(--ruler2-space);
            flex: 0 0 calc(var(--ruler-unit) * var(--ruler2-space));
        }

            .ruler-y li::after {
                content: counter(d)"";
                display: block;
                padding-inline-end: var(--ruler-num-pi);
                transform: rotate(-90deg) translateY(-13px);
                transform-origin: 100% 0%;
                text-align: end;
                width: 100%;
            }
</style>
@code {
    [Parameter]
    public int Count { get; set; } = 31;

    [Parameter]
    public double Size { get; set; } = 1120;
}
```
{% endcode %}

</details>

