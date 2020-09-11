---
layout: single
title: "자바스크립트 비동기 처리"
categories: 
   - Javascript
author_profile: true
read_time: true
comments: true
---

자바스크립트의 가장 중요한 특징을 말해보라고 한다면 난 **비동기 처리** 라고 말한다.

그정도로 비동기 처리는 중요하고 중요한 만큼 자바스크립트에서는 비동기 처리를 유연하게 할 수 있는 방법이 많다.

> 비동기 처리란? 특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 먼저 실행하는 자바스크립트의 특성을 의미합니다.<br/>대표적인 반대되는 예로 C언어 는 절차지향 프로그래밍 언어로 현재 연산중인 코드가 연산을 끝마치지 못한다면 그 다음 코드로 진행되지 못한다.<br/>한마디로 위에서 아래로 순차적으로 코드의 연산이 진행된다고 보면 된다.

지금부터 하나씩 예를 살표보며 비동기의 이해부터 비동기 처리 방법까지 모두 알아보겠다.

<br/>

# 비동기 처리가 되지 않을 때

```javascript
function a() {
    setTimeout(function () {
        console.log('a')
    }, 1000)
}

function b() {
    console.log('b')
}

a();
b();
```

***

```
b
a
```

함수 a는 간단하게 1초 후 console창에 a를 출력하도록 코드를 짰다.

절차지향 언어의 경우에는 1초 후 a가 출력되고 나서 b가 출력되겠지만 자바스크립트는 비동기 프로그래밍 언어이기 때문에 a의 출력을 기다리는 동안 b를 먼저 출력하고 1초가 다 지난 후에야 a가 출력된다.

그렇다면 어떤 식으로 해결을 해야할까?

Callback 함수를 통해 해결하면 된다.

<br/>

# Callback 함수 사용

```javascript
function a(cb) {
    setTimeout(function () {
        console.log('a')
        cb()
    }, 1000)
}

function b() {
    console.log('b')
}

a(function () {
    b();
});
```

***

```
a
b
```

a가 출력되기도 전에 b가 출력된다면 강제적으로 함수를 인자로 넘겨 a가 출력된 후에 함수를 호출할 수 있도록 비교적 간단한 방법으로 해결했다.

함수 호출부에서도 a의 인자로 함수 b를 넘겨준 것을 볼 수 있다.

이를 'Callback 함수'를 사용하여 해결했다고 볼 수 있다.

하지만 이러한 방법에는 큰 문제점이 있는데 코드가 복잡해지면 함수 호출 부분이 상당히 복잡해진다.

이를 'Callback 지옥'이라고 표현하는데 지금부터 살펴보겠다.

<br/>

# Callback 지옥

```javascript
function a(cb) {
    setTimeout(function () {
        console.log('a')
        cb()
    }, 1000)
}

function b(cb) {
    setTimeout(function () {
        console.log('b')
        cb()
    }, 1000)
}

function c(cb) {
    setTimeout(function () {
        console.log('c')
        cb()
    }, 1000)
}

function d(cb) {
    setTimeout(function () {
        console.log('d')
        cb()
    }, 1000)
}

a(function () {
    b(function () {
        c(function () {
            d(function () {
                console.log('callback end!');
            });
        })
    })
})
```

***

```
a
b
c
d
callback end!
```

코드가 몇줄 길어지지도 않았는데 함수 호출 부분이 상당히 복잡해졌다.

이런식이면 코드의 가독성도 떨어지고 나중에 갈수록 헷갈리기 마련이다.

그래서 나온 방법이 'Promise 객체'를 활용하는 것이다.

<br/>

# Promise Object

```javascript
function a() {
    return new Promise(resolve => {
        setTimeout(function () {
            console.log('a')
            resolve();  
        }, 1000)
    })
}

function b() {
    return new Promise(resolve => { 
        setTimeout(function () {
            console.log('b')
            resolve();  
        }, 1000)
    })
}

function c() {
    return new Promise(resolve => { 
        setTimeout(function () {
            console.log('c')
            resolve(); 
        }, 1000)
    })
}

function d() {
    return new Promise(resolve => { 
        setTimeout(function () {
            console.log('d')
            resolve();  
        }, 1000)
    })
}

a()
    .then(() => b())
    .then(() => c())
    .then(() => d())
```

***

```
a
b
c
d
```

Promise 객체를 사용하는 방법은 어렵지 않다.

비동기 처리를 해야하는 함수라면 Promise 객체를 정해진 형식대로 반환하면 된다.

위의 'resolve' 는 비동기 처리가 진행되는 콜백 함수로 보면된다.

'then' 은 함수가 에러없이 정상적으로 실행될 때 호출된다.

확실히 함수호출부가 깔끔해진 것을 볼 수 있다.

또한 함수 호출부를 저렇게 작성하는 것 외에도 최근에 새롭게 도입된 'async' 와 'await' 키워드를 활용할 수도있다.

```javascript
async function asyncFunc() {
    await a()
    await b()
    await c()
    d()
}

asyncFunc();
```

***

```
a
b
c
d
```

await 는 호출하는 함수 앞에 붙이면 되는데 저렇게 붙인 함수들을 async 라는 키워드가 붙은 함수 내부에서 차례로 선언하면 된다.

추가적으로 함수를 실행하다 보면 예상치못한 에러가 발생하곤 한다.

이를 편하게 예외처리 해줄 수 있는 방법도 존재한다.

<br/>

# 예외처리

```javascript
let isError = false
function a() {
    return new Promise((resolve, reject) => { 
        if (isError) {
            reject('Error message!')
        }
        setTimeout(() => {
            console.log('a')
            resolve('done!')
        }, 1000)
    })
}

a().then((res) => {
    console.log(res)
}).catch((error) => {
    console.log(error)
}).finally(() => {
    console.log('무조건 실행')
})
```

***

```
a
done!
무조건 실행
```

Promise 객체를 선언할 때 추가적인 Callback 함수의 인자로 'reject' 라는 것이 보인다.

결론부터 말하자면 resolve 와 reject 는 둘 중 하나만 실행된다.

reject 는 에러가 발생하여 예외처리를 해야할 때 호출되고, resolve 는 정상적으로 진행될 때 호출된다.

함수 호출부분에 then 이외에도 새롭게 생긴 'catch' 나 'finally' 라는 것이 존재한다.

then 은 정상적인 실행

catch 는 에러 발생 시

finally 는 그 이후 무조건 실행이다.

이를 잘 활용하면 예외처리를 더 유연하게 할 수 있다.

추가적으로 async 와 await 사용 시 예외처리도 보겠다.

```javascript
async function asyncFunction() {
    try {
        const res = await a()
        console.log(res)
    } catch (error) {
        console.log(error)
    } finally {
        console.log('무조건 실행')
    }
}
asyncFunction()
```

***

```
a
done!
무조건 실행
```

위의 then 이 'try' 로 바뀐것 이외에는 별 다를게 없다.