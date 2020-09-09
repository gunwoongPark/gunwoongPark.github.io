---
layout: single
title: "Vue.js Life Cycle"
categories: 
   - Vue.js
author_profile: true
read_time: true
comments: true
---

자바스크립트의 인기 프레임 워크인 Vue.js 의 라이프 사이클에 대하여 알아보겠다.

![Vue.js Life Cycle](/../assets/img/lifecycle.png)

라이프사이클은 크게 8단계로 나뉘고 이를 모두 메소드처럼 활용이 가능한데 이것을 '라이프 사이클 훅' 이라고 표현한다.

<br/>

## beforeCreate

Vue 인스턴스(객체)가 생성되고 나서 가장 처음으로 실행되는 라이프 사이클이다.

아직 인스턴스의 data 와 methods 속성이 정의되지 않는 단계이다.

## created

data 와 methods 속성이 정의되었기 때문에 두 속성에 접근이 가능하다.

하지만 아직 Vue 인스턴스가 화면에 부착되기 전, 즉 화면이 렌더링되기 전이기 때문에 template 속성에 정의된 DOM 요소에 접근할 수 없다.

**가장 많이 사용하는 라이프 사이클 훅 중 하나이다.**

## beforeMount

화면에 Vue 인스턴스가 부착되기 직전에 실행되는 라이프 사이클이다.

아직 template 속성에 정의된 DOM 요소에 접근할 수 없다.

## mounted

화면에 Vue 인스턴스가 부착되어 렌더링 된 시기에 실행되는 라이프 사이클이다.

template 속성에 정의한 화면 요소에 접근가능하기 때문에 DOM 으로 인한 접근이 가능하여, 화면 요소를 제어할 수 있는 로직을 구성할 수 있다.


**가장 많이 사용하는 라이프 사이클 훅 중 하나이다.**

## beforeUpdate

Vue 인스턴스 내부의 데이터들의 값이 변경된다면 변경되기 직전에 실행되는 라이프 사이클이다.

## updated

인스턴스 내부의 데이터들의 값이 변경되면 실행되는 라이프 사이클이다.

이렇게 데이터 값이 변경되는 것을 관찰하는 것을 데이터 관찰이라고 하고 이러한 값을 $watch 속성으로 계속해서 인스턴스 내부에서 감시한다.

## beforeDestroy

Vue 인스턴스가 소멸되기 직전에 실행되는 라이프 사이클이다.

## destroyed

Vue 인스턴스가 소멸된 후 실행되는 라이프 사이클이다.

만약 더 이상 사용하지 않는 인스턴스라면 괜히 메모리를 낭비 할 수 있기 때문에 이런식으로 해제시켜주는 것이 좋다.

<br/>

# 예제 코드

```html
<!DOCTYPE html>
<html lang="ko">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Vue test</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>

<body>
    <div id="app">
        <div ref="msg">{% raw %}{{msg}}{% endraw %}</div>
        <div ref="div"></div>
    </div>

    <script>
        const vm = new Vue({
            el: '#app',
            data: {
                msg: 'Hello Vue!'
            },
            beforeCreate() {
                console.log('beforeCreate!', this.msg);
            },
            created() {
                console.log('created!', this.msg);
            },
            beforeMount() {
                console.log('beforeMount!', this.$refs.div);
            },
            mounted() {
                console.log('mounted!', this.$refs.div);
            },
            beforeUpdate() {
                console.log('beforeUpdate!', this.$refs.msg.innerText);
            },
            updated() {
                console.log('updated!', this.$refs.msg.innerText);
            },
            beforeDestroy() {
                console.log('beforeDestroy!')
            },
            destroyed() {
                console.log('destroyed!')
            },
        })

    </script>
</body>

</html>

```