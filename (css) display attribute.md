230829 09:52~

display : 요소를 화면에 어떤식으로 보여줄 지 지정함

|값|뜻|
|--|--|
|none|속성을 화면에 보여주지않으며 위치도 할당하지 않음|
|block|속성을 block(한 줄을 모두 차지(<-보통)하며 "width", "height" 속성을 지정 가능함)형식으로 표현함|
|inline|속성을 inline(요소의 값 만큼만 화면의 위치 차지, "width", "height" 속성 사용 불가)형식으로 표현함|
|inline-block|inline 요소 처럼 화면에서 값 만큼의 크기를 점유하지만 "width", "height" 속성을 사용할 수 있음|
|flex|속성을 "main axis" 방향으로 나열함("main axis"는 "flex-direction" 속성으로 지정 가능함), "flex-container"로 지정된 부모 요소의 자식 요소("flex-item")의 배치를 설정함|
|inline-flex|"flex-container"의 크기가 내포하는 요소들의 값에 대해 container의 길이가 결정된다.(layout보다 주변의 요소와 어우러지기 위한 목적으로 사용됨)|

__flex, inline-flex image__
![](flex_inline-flex.jpeg)
<details>
<summary>ref</summary>
<div>
<a href="https://studiomeal.com/archives/197">https://studiomeal.com/archives/197</a>
</div>
</details>

### display: flex에 대한 부가 속성
|_속성_|_값_|_뜻_|
|---------|----|-|
|flex-direction||"main axis"를 지정함|
||row(기본 값)|요소("flex-item")를 가로, 좌에서 우방향으로한다.|
||row-reverse|요소("flex-item")를 가로, 우에서 좌방향으로한다.|
||column|요소("flex-item")를 세로, 위에서 아래방향으로한다.|
||column-reverse|요소("flex-item")를 세로, 아래에서 위방향으로한다.|
|flex-wrap||요소가 "flex-container"의 지정된 길이를 초과했을 경우 처리방법을 지정함|
||nowrap(기본 값)|아무 처리 하지 않는다.|
||wrap|초과된 요소를 새 행(기존 행의 아래쪽 위치)의 첫 요소로 지정합니다.|
||wrap-reverse|초과된 요소를 새 행(기존 행의 위쪽 위치)의 첫 요소로 지정합니다.|
|flex-flow||"flex-direction" + "flex-wrap"|
|justify-content||"flex-item"의 "main axis" 정렬|
||flex-start(기본 값)|첫 번째 요소를 시작점으로 정렬|
||flex-end|마지막 요소를 끝점으로 정렬|
||center|가운데 정렬|
||space-between|요소 사이 균일한 간격으로 정렬|
||space-around|각 요소에 동일한 간격을 주고 시작과 끝 지점에는 그 간격의 반절 만큼의 간격으로 정렬|
||space-evenly|각 요소와 시작 & 끝 지점에 모두 같은 간격으로 정렬|
|align-items||"flex-item"의 "cross axis" 정렬|
||stretch(기본 값)|"flex-container"의 높이 만큼 요소가 점유함|
||flex-start|요소를 시작 지점으로 정렬|
||flex-end|아이템을 끝 지점으로 정렬|
||center|가운데 정렬|
||baseline|flex-start에서 요소 내의 값을 기준으로 정렬함(가장 큰 문자를 기준으로 그 문자의 최하단을 기준으로 "main axis"와 수평한 baseline을 기준으로 함)|
|aligh-content||"flex-wrap"이 지정된 상태에 각 행을 하나의 요소로 보는 정렬|
||stretch|align-items.stretch 참조|
||flex-start|justify-content.flex-start, align-items.flex-start 참조|
||flex-end|justify-content.flex-end, align-items.flex-end 참조|
||center|justify-content.center, align-items.center 참조|
||space-between|justify-content.space-between 참조|
||space-around|justify-content.space-around 참조|
||space-evenly|justify-content.space-evenly 참조|
|flex-basis|width, height에 사용되는 단위 사용|요소의 최소 크기를 결정함(row\*\*=width, column\*\*=height)|
|flex-grow|0, (양의 유리수)|flex-basis로 지정된 여백을 제외한 나머지 여백의 비율|
|flex-shrink||값이 0이상일 경우 "flex-basis"로 지정한 값보다 작아질 수 있음(0이면 어떤 상황에서도 지정된 값 이하로 작아질 일은 없음)
|flex||"flex-grow" + "flex-shrink" + "flex-basis"|
|align-self|stretch, flex-start, flex-end, center, baseline|요소별로 직접 선택하는 "aligh-itmes"|
|order|(정수)|요소의 배치 순서 변경(적은 수 부터 오름차순으로 표시됨)|
|z-index|(정수)|숫자가 클수록 다른 아이템보다 위로 올라옴|
