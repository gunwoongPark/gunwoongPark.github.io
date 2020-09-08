---
layout: single
title: "가운데 정렬을 하는 방법 (feat. display:flex)"
categories: 
   - CSS
author_profile: true
read_time: true
comments: true
---

CSS 에 있어서 가장 어렵다고 생각되는 것은 개인적으로 가운데에 맞춰서 정렬하는 것이라고 생각한다.

필자는 이를 flex 레이아웃을 통해 해결한다.

가장 간단한 예를 들겠다.

# 코드

## HTML

```html
<div class="parent">
    <div class="child"></div>
</div>
```

## CSS

```css
.parent {
    background-color: dodgerblue;
    width: 300px;
    height: 300px;
    display: flex;
    justify-content: center;
    align-items: center;
}

.child {
    background-color: tomato;
    width: 100px;
    height: 100px;
}
```

<br/>

# 결과

![CSS flex center](/../assets/img/CSS_flex_center.PNG)

간단하게 코드를 리뷰하자면 부모요소가 flex 레이아웃일 때, item 의 주 방향을 정하게 된다.

flex-direction 이라는 속성을 지정하지 않는다면 default 값으로 row 가 들어가게 된다.

또한 그 내부의 item 들은 두 개의 축을 가지게 된다.

하나는 주 축과 다른 하나는 교차 축으로 주 축은 주 방향과 같은 방향을 가지게 되고, 교차 축은 그 와 수직인 방향을 가지게 된다.

justify-content 는 주 축의 정렬 방법을 설정하는 속성으로 정렬방법으로 center 를 주어 수평으로 가운데 정렬을 한다.

align-items 는 교차 축의 정렬 방법을 설정하는 속성으로 정렬방법으로 center 를 주어 수직으로 가운데 정렬을 한다.

align-content 도 있는데 이는 item 들이 2줄 이상일 때 사용할 수 있다.