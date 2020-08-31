---
layout: single
title: "Vue.js computed 와 watch"
categories: 
   - Vue.js
author_profile: true
read_time: true
comments: true
---

Vue.js 를 공부하고 Vue.js 로 프로젝트를 진행하며 항상 궁금했던 것이 있다.

computed 와 watch 는 정확히 어떤 기능을 하고 어떤 차이가 있을까?

computed 와 watch 모두 반응형이라는 공통점이 있다.

<br/>

# Computed

computed 는 데이터가 **연산**이나 **변경**이 이루어지고 나서의 값을 반환하는 것이다.

방금과 같이 값을 반환하는 것을 반응형 getter 라고 하며 getter 처럼 반환된 값을 얻어오는 것과 달리 값을 세팅하는 setter 또한 같이 존재한다.

## 예시 코드

```javascript
reversedMsg: {
    get() {
        return this.msg.split('').reverse().join('')
    },
    set(value) {
        this.msg = value;
    }
}
```

위 코드는 msg 라는 string 을 담는 데이터를 역순으로 변화 시킬 때, 일어나는 연산을 코드로 작성한 것이다.

reversedMsg 의 값이 변경된다면 set 을 호출한 후 다시 get 을 호출하는 원리이다. 

<br/>

# Watch

watch 는 말 그대로 데이터의 변경을 감시한다는 점에서 computed 와 헷갈리는 부분이 있다.

하지만 사용법도 다르고 훨씬 간단하게 이해할 수 있다.

watch 는 Vue 인스턴스의 **특정 프로퍼티**가 **변경**될 때 지정한 콜백 함수가 실행되는 것이다.

## 예시 코드

```javascript
watch: {
    msg(newMsg) {
        console.log('New Msg: ' + newMsg)
    },

    reversedMsg(newMsg) {
        console.log('New reversedMsg' + newMsg);
    }
}
```

위 코드는 msg 라는 string 을 담는 데이터가 변화 될 때, 이를 감지하고 해당 콜백 함수를 실행하는 것을 보여준다.

추가적으로 computed 로 지정한 데이터(reversedMsg) 또한 변경될 때마다 호출 되는 것을 볼 수 있다.

<br/>

## 모든 코드

```vue
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
        <div>{{msg}}</div>
        <div>{{reversedMsg}}</div>
    </div>

    <script>
        const vm = new Vue({
            el: '#app',

            data: {
                msg: 'Hello Vue!'
            },

            computed: {
                reversedMsg: {
                    get() {
                        console.log('getter!');
                        return this.msg.split('').reverse().join('')
                    },
                    set(value) {
                        console.log('setter!');
                        this.msg = value;
                    }
                }
            },

            // data + computed 데이터도 감시
            watch: {
                msg(newMsg) {
                    console.log('New Msg: ' + newMsg)
                },

                reversedMsg(newMsg) {
                    console.log('New reversedMsg' + newMsg);
                }
            }
        })

    </script>
</body>

</html>
```