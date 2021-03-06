---
layout: single
title: "Vue.js v-if VS v-show"
categories: 
   - Vue.js
author_profile: true
read_time: true
comments: true
---

Vue.js 에는 요소를 보여주는 대표적인 디렉티브로 두 가지가 존재한다.

하나는 v-if 로 기존에 아는 프로그래밍 언어의 조건문을 생각하면 간단하다.

값이 참이면 해당 요소를 보이고 값이 거짓이면 해당 요소를 보이지 않는다.

조건문과 같이 v-if 외에도 v-else-if 나 v-else 라는 디렉티브 또한 존재하고 동작원리도 기존 조건문과 같다.

다음은 v-show 로 마찬가지로 값이 참이면 해당 요소를 보이고 값이 거짓이면 해당 요소를 보이지 않는다.

그렇다면 같은 동작을 하는 디렉티브인데 왜 두 개나 존재할까?

답은 비용에 있다.

**v-if** 디렉티브의 경우 값이 거짓이면 해당 요소를 코드상에서 아예 지워버린다.

**v-show** 디렉티브의 경우 값이 거짓이면 해당 요소에 Inline CSS 방식으로 style="display:none;" 이 추가된다.

즉 코드상에서 지우는 행위는 당연히 Inline CSS 방식으로 삽입되는 것 보다 비용이 더 많이 든다.

따라서 사용자에 의해 자주 바뀌는 요소의 경우 v-if 디렉티브 보다는 v-show 디렉티브가 더 효율적이다.

하지만 v-show 디렉티브 또한 단점이 존재하는데 이는 바로 초기 렌더링 비용이다.

초기 렌더링 비용이란 만약 처음부터 값이 거짓인 요소의 경우 v-if 디렉티브는 아예 코드상에 존재하지 않지만 v-show 디렉티브는 코드상에 존재하지만 보이지 않도록 CSS 가 삽입된 상태이기 때문에 초기 렌더링 비용적 측면에서 v-show 디렉티브가 비효율적이다.

# 결론

일반적으로는 v-if 디렉티브를 사용하지만 너무 자주 바뀌는 요소의 경우 v-show 디렉티브를 사용하면 된다.